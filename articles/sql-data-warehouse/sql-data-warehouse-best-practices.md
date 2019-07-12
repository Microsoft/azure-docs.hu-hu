---
title: Ajánlott eljárások az Azure SQL Data Warehouse-hoz | Microsoft Docs
description: Javaslatok és ajánlott eljárások, amelyeket érdemes tudni az Azure SQL Data Warehouse-megoldások fejlesztésekor.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/26/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9c9e293a6e9c8126f2b82f68d591aee56ec32aec
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672277"
---
# <a name="best-practices-for-azure-sql-data-warehouse"></a>Ajánlott eljárások az Azure SQL Data Warehouse-hoz
Ez a cikk gyakorlati tanácsok segítséget nyújtanak az optimális teljesítmény érdekében az Azure SQL Data Warehouse-ból gyűjteménye.  A cikkben található fogalmak némelyike könnyen elmagyarázható, míg mások összetettebb fogalmak, amelyeket csak nagy vonalakban mutatunk be.  A cikk célja, hogy némi alapszintű útmutatással és a legfontosabb dolgok hangsúlyozásával segítse Önt az adattárház létrehozásában.  Minden szakasz röviden bemutat egy fogalmat, majd részletesebb cikkekhez irányít át, amelyek a fogalmat alaposabban is körüljárják.

Ha csak most kezdi el használni az Azure SQL Data Warehouse-t, ne hagyja, hogy ez a cikk megijessze.  A témakörök nagyjából fontossági sorrendben követik egymást.  Ha kezdetnek csak az első néhány fogalommal foglalkozik, az tökéletesen elegendő.  Növekedésével további számára ismerősek és kényelmesek, és az SQL Data Warehouse, térjen vissza, és tekintse meg néhány újabb fogalommal.  Nem fog sokáig tartani, amíg a dolgok világosan összeállnak.

Útmutatás a betöltéshez: [Útmutató az adatok betöltéséhez](guidance-for-loading-data.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által
A költségek felfüggesztéssel és skálázással való csökkentéséről további információért tekintse meg a [Számítások kezelése](sql-data-warehouse-manage-compute-overview.md) című szakaszt. 


## <a name="maintain-statistics"></a>Statisztikák karbantartása
Az SQL Serverrel ellentétben az SQL Data Warehouse nem észleli, hozza létre vagy frissíti az oszlopok statisztikáit, hanem ezek manuális karbantartása szükséges.  Noha mindezen a jövőben változtatni tervezünk, egyelőre az SQL Data Warehouse-csomagok optimalizálása érdekében saját kezűleg kell karbantartania a statisztikákat.  Az optimalizáló által létrehozott csomagok csak annyira jók, amennyire az elérhető statisztikák azok.  **A statisztikák megismerésének egyik legjobb módja a mintastatisztikák létrehozása minden oszlopról.**  Ugyanilyen fontos a statisztikák frissítése is, mivel az adatokban jelentős változások történhetnek.  Ennek hagyományos módszere, ha naponta vagy minden betöltés után frissíti a statisztikákat.  Mindig érdemes figyelembe venni, hogyan viszonyul egymáshoz a teljesítmény és a statisztikák létrehozásának és frissítésének költségei. Ha úgy gondolja, hogy túl sokáig tart az összes statisztika karbantartása, lehet, hogy körültekintőbben kell kiválasztania, mely oszlopok rendelkezzenek statisztikákkal vagy melyek igényelnek gyakori frissítést.  Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek. **A legnagyobb előnnyel az jár, ha az összekapcsolások részét képező, a WHERE záradékban használt és a GROUP BY elemben megtalálható oszlopok statisztikáit kéri le.**

Lásd még: [táblastatisztikák][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][statisztika frissítése]

## <a name="group-insert-statements-into-batches"></a>INSERT utasítások csoportosítása kötegekbe
A kis táblák INSERT utasítással végzett egyszeri feltöltése vagy akár egy keresés rendszeres újratöltése is kielégítheti az igényeit, ha egy, a következőhöz hasonló utasítást használ: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Ha azonban egy nap során több ezer vagy több millió sort kell betöltenie, észreveheti, hogy az egyszeres INSERT utasítások nem feltétlenül bizonyulnak elegendőnek.  Ehelyett érdemes olyan folyamatokat kifejleszteni, amelyek egy fájlba írnak, amelyet egy időről időre aktiválódó másik folyamat rendszeresen betölt.

Lásd még: [INSERT][INSERT].

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Az adatok gyors betöltése és exportálása a PolyBase segítségével
Az SQL Data Warehouse az adatok több eszközzel (például az Azure Data Factoryval, a PolyBase-el és a BCP-vel) végzett betöltését és exportálását is támogatja.  Kis mennyiségű adat kezelése esetén, ahol a teljesítmény nem kulcsfontosságú tényező, bármelyik eszköz megfelelhet az igényeinek.  Amikor nagy mennyiségű adatot tölt be vagy exportál, vagy gyors teljesítményre van szükség, a PolyBase a legjobb választás.  A PolyBase úgy van kialakítva, hogy kihasználja az SQL Data Warehouse MPP (Massively Parallel Processing, nagymértékben párhuzamos feldolgozási) architektúráját, így bármely más eszköznél sokkal gyorsabban tölti be és exportálja az adatokat.  A PolyBase-betöltések a CTAS vagy az INSERT INTO paranccsal futtathatók.  **A CTAS használata minimalizálja a tranzakciók naplózásának mértékét, és ez az adatok betöltésének leggyorsabb módja.**  Az Azure Data Factory is támogatja a PolyBase-betöltések és a CTAS t hasonló teljesítmény érhető el.  A PolyBase többféle fájlformátumot támogat, beleértve a Gzip-fájlokat is.  **A gzip szöveges fájlok használatakor az átviteli teljesítmény maximalizálása érdekében tördelje a fájlokat 60 vagy több fájllá a betöltés párhuzamosságának növelése érdekében.**  A gyorsabb teljes átviteli teljesítmény érdekében érdemes lehet egy időben betölteni az adatokat.

Lásd még: [adatok betöltése][Load data], [Guide for using PolyBase][Guide for using PolyBase], [Azure SQL Data Warehouse betöltési minták és stratégiák][Azure SQL Data Warehouse betöltési minták és stratégiák], [ Adatok betöltése az Azure Data Factory][Load Data with Azure Data Factory], [Move data with Azure Data Factory][Move data with Azure Data Factory], [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT], [tábla létrehozása, mert a select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Betöltés, majd külső táblák lekérdezése
Míg a PolyBase, más néven a külső táblák használata lehet az adatok betöltésének leggyorsabb módja, lekérdezésekhez nem ez a legjobb megoldás. Az SQL Data Warehouse PolyBase-táblák egyelőre még csak az Azure-blobfájlokat és az Azure Data Lake-tárolót támogatják. Ezekhez a fájlokhoz nem tartoznak külön számítási erőforrások.  Ennek eredményeképpen az SQL Data Warehouse nem tudja kiszervezni a munkát, és be kell olvasnia a teljes fájlt úgy, hogy betölti a tempdb adatbázisba.  Ezért ha több olyan lekérdezése is van, amelyek ennek a fájlnak az adatait kérik le, érdemesebb egyszer betölteni az adatokat, és úgy beállítani a lekérdezéseket, hogy a helyi táblát használják.

Lásd még a [Útmutató a PolyBase használatához][Guide for using PolyBase].

## <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása
Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.  Ez megkönnyíti, hogy a felhasználók anélkül kezdhessenek neki a táblák létrehozásának, hogy dönteniük kéne az elosztás módjáról.  A ciklikus időszeletelésű táblák megfelelő megoldást jelenthetnek egye számítási feladatok esetén, a legtöbb esetben azonban egy elosztási oszlop kiválasztása megfelelőbb eredményre vezet.  A leggyakoribb példája annak, amikor egy oszloppal elosztott tábla sokkal jobb teljesítményt nyújt egy ciklikus időszeletelésűnél, amikor két nagy tábla egyesül.  Ha például van egy rendelésazonosító alapján elosztott rendelési táblája és egy, szintén a rendelésazonosító alapján elosztott tranzakciótáblája, akkor ha a rendelési táblát a tranzakciótáblával egyesíti a rendelésazonosító oszlopnál, a lekérdezés továbbított lekérdezéssé válik, így kiküszöbölhetők az adatátviteli műveletek.  Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat.  A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.  Ez a magyarázat csak témakörre vonatkozó tudnivalókba. Elosztott tábla betöltésekor győződjön meg arról, hogy a bejövő adatok nem az elosztási kulcs alapján vannak rendezve, mivel ez lelassítja a betöltéseket.  Tekintse meg az alábbi mutató hivatkozások nagy hogyan elosztási oszlop kiválasztása javíthatja a teljesítményt és határozhat meg elosztott táblát a CREATE TABLE utasítás WITH záradékának használatával további részleteiért.

Lásd még: [táblák áttekintésével][Table overview], [Table distribution][Table distribution], [Táblaelosztás][Táblaelosztás], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése
Míg az adatok particionálása nagyon hatásos eszköz lehet az adatok partícióváltáson keresztüli megőrzéséhez vagy a vizsgálatok partíciókizárással végzett optimalizálásához, a túl sok partíció lelassíthatja a lekérdezéseket.  Az SQL Serveren jól működő részletesebb particionálási stratégia nem feltétlenül működik hasonló hatékonysággal az SQL Data Warehouse-ban.  A túl sok partíció a fürtözött oszlopcentrikus indexek hatékonyságát is csökkentheti, ha az egyes partíciók kevesebb mint 1 millió sorral rendelkeznek.  Ne feledje, hogy a háttérben az SQL Data Warehouse 60 adatbázisba particionálja az adatait, így ha 100 partícióval rendelkező táblát hoz létre, ez 6000 partíciót eredményez.  Mindegyik számítási feladat különböző, így érdemes kísérletezni a particionálással, és kideríteni, hogy az adott számítási feladatnál melyik megoldás a célravezető.  Érdemes kisebb szintű részletességet használni, mint az SQL Serveren.  A napi partíciók helyett például érdemes lehet heti vagy havi partíciókat használni.

Lásd még a [tábla particionálásával][Table partitioning] foglalkozó témakört.

## <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása
Az INSERT, UPDATE és DELETE utasítások tranzakcióban futnak, és amikor meghiúsulnak, vissza kell őket állítani.  A hosszú visszaállítások előfordulásának csökkentése érdekében mindig minimalizálja a tranzakciók méretét, ahol erre lehetősége van.  Ezt úgy teheti meg, hogy részekre osztja az INSERT, UPDATE és DELETE utasításokat.  Ha például olyan INSERT utasítása van, amelynek a futtatása várhatóan 1 órát vesz igénybe, ha megoldható, ossza 4, egyenként 15 percig futó részre az INSERT utasítást.  Használja ki a minimális naplózást igénylő speciális eseteket, például a CTAS, TRUNCATE, DROP TABLE vagy INSERT utasításokat a táblák kiürítéséhez, hogy csökkentse a visszaállítással kapcsolatos kockázatokat.  A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Ahelyett például, hogy DELETE utasítással törölné egy tábla összes olyan sorát, ahol az order_date 2001. október, havonta particionálhatja az adatokat, majd az adatokkal rendelkező partíciót egy másik tábla üres partíciójára cserélheti (lásd az ALTER TABLE-re vonatkozó példákat).  A nem particionált táblák esetén érdemes lehet CTAS utasítással kiírni a táblában megőrizni kívánt adatokat a DELETE használata helyett.  Noha egy CTAS utasítás ugyanannyi időt vesz igénybe, sokkal biztonságosabban futtatható művelet, mivel minimális tranzakciónaplózást igényel, és szükség esetén gyorsan megszakítható.

Lásd még: [tranzakciók megismerése][Understanding transactions], [Optimizing transactions][Optimizing transactions], [tábla particionálásával][tábla particionálásával], [TRUNCATE TABLE][TRUNCATE TABLE] , [ALTER TABLE][ALTER TABLE], [Tábla létrehozása, mert a select (CTAS)][tábla létrehozása, mert a select (CTAS)]

## <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata
Ha a DDL meghatározásakor az adatokat támogató legkisebb adattípust használja, nagyban javíthatja a lekérdezés teljesítményét.  Ez különösen fontos a CHAR és VARCHAR oszlopok esetén.  Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  NVARCHAR helyett VARCHAR típusként határozza meg az oszlopokat, amikor ez is elegendő.

Lásd még: [táblák áttekintésével][Table overview], [Table data types][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Ideiglenes halomtáblák használata átmeneti adatokhoz
Amikor ideiglenesen tárol adatokat az SQL Data Warehouse-ban, a halomtábla használata a teljes folyamatot felgyorsíthatja.  Ha csak azért tölt be adatokat, hogy további átalakítások futtatása előtt előkészítse őket, sokkal gyorsabban töltheti a táblát egy halomtáblába, mint egy fürtözött oszlopcentrikus táblába.  Azzal is tovább gyorsíthatja a folyamatot, ha az adatokat állandó tároló helyett ideiglenes táblába tölti.  Az ideiglenes táblák „#” karakterrel kezdődnek, és csak az őket létrehozó munkamenet által érhetők el, így csak korlátozott esetekben használhatók.   A halomtáblákat a CREATE TABLE utasítás WITH záradékával lehet meghatározni.  Ha ideiglenes táblát használ, ne felejtsen el rajta is statisztikákat létrehozni.

Lásd még: [ideiglenes táblák][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása
A fürtözött oszlopcentrikus indexek az adatok tárolásának leghatékonyabb módjai közé tartoznak az SQL Data Warehouse-ban.  Alapértelmezés szerint a táblák az SQL Data Warehouse-ban fürtözött oszlopcentrikusként jönnek létre.  Annak érdekében, hogy az oszlopcentrikus táblák a lehető legjobb teljesítménnyel fussanak, fontos a jó szegmensminőség.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  A szegmensminőség a tömörített sorcsoportokban található sorok száma alapján mérhető fel.  Tekintse meg a [Táblaindexek indexek minőségének][Causes of poor columnstore index quality] in the [Table indexes][Table indexes] lépésenkénti utasításokat észlelésével és javításával foglalkoznak a fürtözött oszlopcentrikus táblák szegmensminőségének ismertető cikket.  Kiváló minőségű oszlopcentrikus szegmensek fontosak, mert célszerű használni a felhasználók azonosítók, amelyek a közepes vagy nagy erőforrásosztály az adatok betöltéséhez. Lower használatával [adattárházegységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) esetén érdemes nagyobb erőforrásosztályt hozzárendelni a betöltést végző felhasználóhoz.

Mivel az oszlopcentrikus táblák általában nem küldenek adatokat tömörített oszlopcentrikus szegmensbe addig, amíg nincs több mint 1 millió sor táblánként és az SQL Data Warehouse-táblák nincsenek 60 táblává particionálva, az oszlopcentrikus táblák használata nem célravezető a lekérdezéseknél, ha a tábla kevesebb, mint 60 millió sorral rendelkezik.  Előfordulhat, hogy a 60 milliónál kevesebb sorral rendelkező táblák esetén egyáltalán nem érdemes oszlopcentrikus indexet használni.  A használatuk azonban hátrányt sem jelent.  Ha particionálja az adatait, akkor azt is érdemes figyelembe venni, hogy minden partíciónak 1 millió sorral kell rendelkeznie a fürtözött oszlopcentrikus indexek előnyeinek kihasználása érdekében.  Ha egy tábla 100 partícióval rendelkezik, akkor legalább 6 milliárd sorral kell rendelkeznie a fürtözött oszloptárolók előnyeinek kihasználása érdekében (60 elosztás × 100 partíció × 1 millió sor).  Ha a tábla nem rendelkezik 6 milliárd sorral, akkor csökkentse a partíciók számát, vagy fontolja meg egy halomtábla használatát.  Azzal is érdemes lehet kísérletezni, hogy jobb teljesítmény érhető-e el egy másodlagos indexekkel rendelkező halomtáblával, mint egy oszlopcentrikussal.

Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  

Lásd még: [Oszlopcentrikus indexek][Table indexes], [Columnstore indexes guide][Columnstore indexes guide], [oszlopcentrikus indexek Újjáépítésével][oszlopcentrikus indexek Újjáépítésével]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Nagyobb erőforrásosztály használata a lekérdezés teljesítményének javítása érdekében
Az SQL Data Warehouse erőforráscsoportokat használ arra, hogy memóriát foglaljon le a lekérdezésekhez.  Először minden felhasználó kis erőforrásosztályhoz van rendelve, amely elosztásonként 100 MB memóriát biztosít.  Mivel mindig 60 elosztás van és mindegyik elosztás minimum 100 MB-ot kap, a rendszeren belül összesen 6000 MB, vagyis majdnem 6 GB memória van lefoglalva.  Bizonyos lekérdezéseknél, például a nagyobb egyesítéseknél vagy a fürtözött oszlopcentrikus táblákba végzett betöltésnél előnyt jelent a nagyobb memórialefoglalások használata.  Néhány lekérdezésnél, például amelyek csak vizsgálati műveleteket végeznek, ezeknek a használata nem jár további előnnyel.  A nagyobb erőforrásosztályok használata hatással van az egyidejűség mértékére. Ezt érdemes figyelembe venni, mielőtt az összes felhasználót nagy erőforrásosztályba helyezné.

Lásd még: [Erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Kisebb erőforrásosztály használata az egyidejűség mértékének növelése érdekében
Ha azt látja, hogy megnőtt a felhasználói lekérdezések késleltetése, előfordulhat, hogy a felhasználók nagyobb erőforrásosztályban futnak és sok egyidejű helyet foglalnak le, így más lekérdezések sorban állásra kényszerülnek.  Ha a `SELECT * FROM sys.dm_pdw_waits` parancs futtatásakor a rendszer sorokat ad vissza, láthatja, hogy a felhasználók lekérdezései sorban állnak.

Lásd még: [erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md), [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>A lekérdezések megfigyelése és optimalizálása DMV-kkel
Az SQL Data Warehouse több DMV-vel is rendelkezik, amelyekkel a lekérdezések futtatása figyelhető meg.  A megfigyeléssel foglalkozó alábbi témakör részletes útmutatót szolgáltat ahhoz, hogyan nézheti meg egy futtatott lekérdezés részleteit.  Ha gyorsan szeretne lekérdezéseket kikeresni a DMV-kben, segíthet, ha a lekérdezéseknél használja a LABEL beállítást.

Lásd még: [a számítási feladat monitorozása DMV][Monitor your workload using DMVs], [LABEL][LABEL], [beállítás][beállítás], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Egyéb erőforrások
Lásd még az általános problémákat és megoldásokat tartalmazó, [hibaelhárítással][Troubleshooting] foglalkozó témakört.

Ha nem találta meg a cikkben, amit keresett, próbálkozzon az oldal bal oldalán található „Dokumentumok keresése” mezővel. Az itt megadott kifejezésre a rendszer az összes Azure SQL Data Warehouse-dokumentumban rákeres.  A [az Azure SQL Data Warehouse-fórum][Azure SQL Data Warehouse MSDN Forum] is a place for you to ask questions to other users and to the SQL Data Warehouse Product Group.  We actively monitor this forum to ensure that your questions are answered either by another user or one of us.  If you prefer to ask your questions on Stack Overflow, we also have an [Azure SQL Data Warehouse Stack Overflow Forum][Azure SQL Data Warehouse Stack Overflow Forum].

A szolgáltatással kapcsolatos kéréseit az [Azure SQL Data Warehouse visszajelzési][Azure SQL Data Warehouse Feedback] oldalán küldheti be.  Ha kéréseket tesz közzé vagy más kérésekre szavaz, sokat segíthet a szolgáltatások fontossági sorrendjének megállapításában.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
