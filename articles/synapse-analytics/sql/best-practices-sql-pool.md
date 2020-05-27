---
title: Ajánlott eljárások az SQL-készletekhez
description: Javaslatok és ajánlott eljárások az SQL-készletek használata esetén.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 345794eead05a4ff5e5d1396df78878d2693f405
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834985"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Ajánlott eljárások az SQL-készletekhez az Azure szinapszis Analyticsben

Ez a cikk az Azure szinapszis Analytics szolgáltatásban az SQL-készletek optimális teljesítményének elérését segítő ajánlott eljárásokat tartalmazza. Az alábbiakban alapszintű útmutatást és fontos területeket talál a megoldás létrehozásakor. Az egyes szakaszok egy koncepciót vezetnek be, majd a részletesebb cikkek részletes cikkeire mutatnak.

## <a name="sql-pools-loading"></a>SQL-készletek betöltése

Az SQL-készletek útmutatójának betöltéséhez lásd: [útmutató az betöltéshez](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által

A költségek felfüggesztésével és skálázásával kapcsolatos további információkért lásd: a [számítások kezelése](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Statisztikák karbantartása

Míg SQL Server automatikusan észleli és létrehozza vagy frissíti az oszlopok statisztikáit, az SQL-készletek a statisztikák manuális karbantartását igénylik. Meg szeretné őrizni a statisztikáit, hogy az SQL-készlet csomagjai optimalizálva legyenek.  Az optimalizáló által létrehozott csomagok csak annyira jók, amennyire az elérhető statisztikák azok.

> [!TIP]
> A statisztikák megismerésének egyik legjobb módja a mintastatisztikák létrehozása minden oszlopról.  

Ugyanilyen fontos a statisztikák frissítése is, mivel az adatokban jelentős változások történhetnek.  Ennek hagyományos módszere, ha naponta vagy minden betöltés után frissíti a statisztikákat.  Mindig érdemes figyelembe venni, hogyan viszonyul egymáshoz a teljesítmény és a statisztikák létrehozásának és frissítésének költségei.

A statisztikai karbantartási idő lerövidítéséhez szelektíven kell lennie, hogy mely oszlopok rendelkeznek statisztikával, vagy a leggyakoribb frissítésre van szükség. Előfordulhat például, hogy frissíteni szeretné a dátum oszlopokat, amelyekben naponta új értékek adhatók hozzá. Az illesztések, a WHERE záradékban használt oszlopok, valamint a GROUP BY által megtalált oszlopok statisztikájának kiépítésére koncentrálhat.

A statisztikával kapcsolatos további információk a [tábla statisztikáinak kezelése](develop-tables-statistics.md), [statisztikák létrehozása](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és [statisztika frissítése](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) cikkekben találhatók.

## <a name="group-insert-statements-into-batches"></a>INSERT utasítások csoportosítása kötegekbe

Egyszeri betöltés egy kisméretű táblába egy INSERT utasítással, például `INSERT INTO MyLookup VALUES (1, 'Type 1')` az igényeitől függően a legjobb megközelítés lehet. Ha azonban több ezer vagy több millió sort kell betölteni a nap folyamán, valószínű, hogy az egyszeres lapkák nem optimálisak.

A probléma megoldásának egyik módja egy olyan folyamat fejlesztése, amely egy fájlba ír, majd egy másik folyamat a fájl rendszeres betöltéséhez. További információért tekintse meg a [beszúrási](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) cikket.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Az adatok gyors betöltése és exportálása a PolyBase segítségével

Az SQL-készlet támogatja az adatok betöltését és exportálását számos eszközön, többek között a Azure Data Factory, a Base és a BCP használatával.  Kis mennyiségű adat kezelése esetén, ahol a teljesítmény nem kulcsfontosságú tényező, bármelyik eszköz megfelelhet az igényeinek.  

> [!NOTE]
> Nagy mennyiségű adat betöltésére vagy exportálására, vagy gyorsabb teljesítményre van szükség.

A PolyBase-betöltések a CTAS vagy az INSERT INTO paranccsal futtathatók. A CTAS a tranzakció-naplózást, és az adatai betöltésének leggyorsabb módját fogja csökkenteni. A Azure Data Factory támogatja a alapszintű terheléseket is, és a CTAS hasonló teljesítményt érhet el. A Base többféle fájlformátumot támogat, többek között a gzip-fájlokat.

A gzip szövegfájlok használatakor az átviteli sebesség maximalizálása érdekében bontsa a fájlokat 60 vagy több fájlba, hogy maximalizálja a terhelés párhuzamosságát. A gyorsabb teljes átviteli teljesítmény érdekében érdemes lehet egy időben betölteni az adatokat. Az ehhez a szakaszhoz kapcsolódó témakörökkel kapcsolatos további információkat a következő cikkek tartalmaznak:

- [Adatok betöltése](data-loading-overview.md)
- [Útmutató a PolyBase használatához](data-loading-best-practices.md)
- [Az Azure SQL Pool betöltési minták és stratégiák](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Az adatterhelés Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Adatok betöltése az Azure Data Factoryvel](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Betöltés, majd külső táblák lekérdezése

A Kiindulás nem optimális a lekérdezéseknél. Az SQL-készletek alapszintű táblái jelenleg csak az Azure Blob-fájlokat és Azure Data Lake tárolót támogatják. Ezek a fájlok nem rendelkeznek biztonsági mentéssel. Ennek eredményeképpen az SQL-készletek nem tudják kiszervezni ezt a munkát, és a teljes fájlt be kell olvasni a tempdb-be való betöltésével, hogy el tudja olvasni az adatforrást.

Ha több lekérdezés is van az ilyen adatlekérdezéshez, akkor érdemes egyszer betölteni ezeket az adatfájlokat, és a lekérdezésekkel a helyi tábla is használható. A cikk további, alapszintű útmutatást tartalmaz a [alapszintű cikkek használatához](data-loading-best-practices.md) .

## <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása

Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.   Ez az alapértelmezett beállítás azt teszi lehetővé, hogy a felhasználók elkezdődjön a táblák létrehozásának eldöntése anélkül, hogy el kellene dönteniük a táblák elosztásának módját. A ciklikus időszeletelésű táblázatok bizonyos munkaterhelések esetén megfelelően elvégezhetnek. A legtöbb esetben azonban a terjesztési oszlop jobb teljesítményt nyújt.  

A ciklikus időszeletelési táblázatot végrehajtó oszlopok leggyakoribb példája, ha két nagyméretű tábla van csatlakoztatva.  

Ha például rendelkezik egy order_id által terjesztett Orders táblával, és egy order_id által terjesztett tranzakciós táblát is, amikor az Orders (megrendelések) táblát a order_id tranzakciós táblájához csatlakoztatja, a lekérdezés egy átmenő lekérdezés lesz. Ezt követően az adatáthelyezési műveletek el lesznek távolítva. Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat. A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.

> [!NOTE]
> Elosztott tábla betöltésekor a bejövő adatai nem rendezhetők a terjesztési kulcs alapján. Ez lelassítja a terhelést.

Az alábbi hivatkozásokra kattintva további részleteket tudhat meg a teljesítmény növeléséről a terjesztési oszlop kiválasztásával. Azt is megtudhatja, hogyan határozhat meg egy elosztott táblát a CREATE TABLE utasítás WITH záradékában:

- [Táblázat áttekintése](develop-tables-overview.md)
- [a táblaelosztással](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [a táblaelosztás kiválasztásával](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése

Míg az adatparticionálás hatékonyan kezelheti az adatait a partíciók váltásával vagy a vizsgálatok optimalizálásával a partíciók eltávolításával, a túl sok partíció lelassíthatja a lekérdezéseket.  Gyakran olyan nagy részletességű particionálási stratégia, amely jól működik SQL Server előfordulhat, hogy nem működik megfelelően az SQL-készleten.  

A túl sok partíció csökkentheti a fürtözött oszlopcentrikus indexek hatékonyságát, ha az egyes partíciók 1 000 000-nál kevesebb sorral rendelkeznek. Az SQL-készletek automatikusan 60 adatbázisba particionálják az adataikat. Tehát ha 100 partíciót tartalmazó táblát hoz létre, akkor az eredmény 6000 partíció lesz. Az egyes számítási feladatok különbözőek, ezért a legjobb Tanács, hogy kísérletezzen a particionálással, hogy lássa, mi a legmegfelelőbb a számítási feladatokhoz.  

Az egyik lehetőség az, hogy egy olyan részletességet használ, amely alacsonyabb, mint amit a SQL Server használatával implementált. Például érdemes lehet hetente vagy havonta partíciót használni a napi partíciók helyett.

A particionálásról a [táblázat particionálásáról](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) szóló cikkben talál további információt.

## <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása

Az INSERT, UPDATE és DELETE utasítások egy tranzakcióban futnak. Ha nem sikerül, vissza kell őket állítani. Ha csökkenteni szeretné a hosszú visszaállítás lehetőségét, minimalizálja a tranzakciók méretét, amikor csak lehetséges.  A tranzakciók méretének minimalizálása a BESZÚRÁSi, frissítési és TÖRLÉSi utasítások részekre osztásával végezhető el. Ha például egy olyan BESZÚRÁSt szeretne, amely 1 órát vesz igénybe, akkor a BESZÚRÁSt négy részre bontja. Az egyes futtatások lerövidítve 15 percet vesznek igénybe.  

> [!TIP]
> Kihasználhatja a speciális minimális naplózási eseteket, például a CTAS, a CSONKÍTás, a DROP TABLE vagy az INSERT utasítást üres táblázatokra a visszaállítási kockázat csökkentése érdekében.  

A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Például ahelyett, hogy a DELETE utasítás végrehajtása után törli egy olyan tábla összes sorát, amelyben a order_date a 2001 októberében volt, az adatokat havonta particionálhatja. Ezután kikapcsolhatja a partíciót egy üres partícióra vonatkozó adatokkal egy másik táblából (lásd: ALTER TABLE examples).  

A nem particionált táblák esetében érdemes lehet egy CTAS használni a táblákat megőrizni kívánt, a TÖRLÉSt nem használó adatbevitelre.  Ha egy CTAS ugyanannyi időt vesz igénybe, sokkal biztonságosabb futni, mivel minimális tranzakciós naplózással rendelkezik, és szükség esetén gyorsan megszakítható.

Az ehhez a szakaszhoz kapcsolódó tartalommal kapcsolatos további információk az alábbi cikkekben találhatók:

- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Tranzakciók megismerése](develop-transactions.md)
- [Tranzakciók optimalizálása](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [tábla particionálásával](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [MÓDOSÍTÁSI TÁBLÁZAT](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>Lekérdezési eredmények méretének csökkentése

A lekérdezési eredmények méretének csökkentése segít elkerülni a nagyméretű lekérdezési eredmények által okozott ügyféloldali problémákat.  A lekérdezés szerkesztésével csökkentheti a visszaadott sorok számát. Egyes lekérdezés-létrehozási eszközök lehetővé teszik a "legfontosabb N" szintaxis hozzáadását az egyes lekérdezésekhez.  A lekérdezés eredményét egy ideiglenes táblára is CETAS, majd a viszonyítási alap exportálást használhatja az alacsonyabb szintű feldolgozáshoz.

## <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata

A DDL meghatározásakor használja azt a legkisebb adattípust, amely támogatja az adatait, így javul a lekérdezési teljesítmény.  Ez a javaslat különösen fontos a CHAR és a VARCHAR oszlopokhoz.  Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  Emellett a NVARCHAR használata helyett VARCHAR-ként definiálja az oszlopokat.

A fenti információkkal kapcsolatos alapvető fogalmak részletesebb áttekintéséhez tekintse meg a [táblázat áttekintését](develop-tables-overview.md), a [táblázat adattípusait](develop-tables-data-types.md)és a [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) cikkeket.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Ideiglenes halomtáblák használata átmeneti adatokhoz

Ha az SQL-készletekben ideiglenesen történik az adattárolás, a halom táblái általában gyorsabban elvégzik a teljes folyamatot.  Ha csak a további átalakítások futtatása előtt készíti el az adatgyűjtést, a tábla betöltése egy halom táblába gyorsabb lesz, mint a fürtözött oszlopcentrikus-táblába való betöltés.  

Az adattöltés egy ideiglenes táblába is sokkal gyorsabb lesz, mint a tábla állandó tárterületre való betöltése.  Az ideiglenes táblák "#" értékkel kezdődnek, és csak az azt létrehozó munkamenet számára érhetők el. Ennek következtében előfordulhat, hogy csak korlátozott helyzetekben működnek. A halomtáblákat a CREATE TABLE utasítás WITH záradékával lehet meghatározni.  Ha ideiglenes táblát használ, ne felejtsen el rajta is statisztikákat létrehozni.

További útmutatásért tekintse meg az [ideiglenes táblákat](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), a [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és a [create TABLEt](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) .

## <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása

A fürtözött oszlopcentrikus indexek az egyik leghatékonyabb módszer az SQL-készletben tárolt adattároláshoz.  Alapértelmezés szerint az SQL-készletben lévő táblák fürtözött Oszlopcentrikus jönnek létre.  Annak érdekében, hogy az oszlopcentrikus táblák a lehető legjobb teljesítménnyel fussanak, fontos a jó szegmensminőség.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  

A szegmens minőségét a tömörített sorcsoport sorainak száma alapján lehet mérni. A fürtözött oszlopcentrikus-táblák szegmensének minőségének észlelésével és javításával kapcsolatos részletes utasításokért tekintse meg a [gyenge oszlopcentrikus indexének okait](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) a [táblázat indexek](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című cikkben.  

Mivel a kiváló minőségű oszlopcentrikus szegmensek fontosak, hasznos lehet a közepes vagy nagy erőforrás-osztályba tartozó felhasználói azonosítók használata az betöltéshez. Az alacsonyabb [adattárház-egységek](resource-consumption-models.md) használata azt jelenti, hogy nagyobb erőforrás-osztályt szeretne hozzárendelni a betöltési felhasználóhoz.

A oszlopcentrikus-táblák általában nem küldenek le az adatmennyiséget egy tömörített oszlopcentrikus, amíg a tábla 1 000 000-nál több sora van. Minden SQL Pool-tábla 60 táblázatba van particionálva. Ennek megfelelően a oszlopcentrikus-táblák nem részesülnek lekérdezésben, kivéve, ha a tábla 60 000 000-nál több sorral rendelkezik.  

> [!TIP]
> Az 60 000 000-nál kevesebb sorral rendelkező táblák esetében előfordulhat, hogy a oszlopcentrikus index nem az optimális megoldás.  

Ha particionálja az adatait, az egyes partíciók esetében 1 000 000 sort kell kihasználnia egy fürtözött oszlopcentrikus-indexből.  Az 100-partíciót tartalmazó táblák esetében legalább 6 000 000 000 sort kell kihasználnia a fürtözött oszlopok tárolójából (60-eloszlások *100-partíciók* 1 000 000-sorok).  

Ha a táblához nem tartozik 6 000 000 000 sor, két fő lehetőség közül választhat. Csökkentse a partíciók számát, vagy használjon egy halom táblát.  Azt is érdemes lehet kísérletezni, hogy a jobb teljesítmény megszerezhető-e egy oszlopcentrikus tábla helyett a másodlagos indexekkel rendelkező heap tábla használatával.

Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  További információ a tábla-és oszlopcentrikus indexekről, valamint a [Table indexek](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), a [oszlopcentrikus indexek](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és a [oszlopcentrikus indexek újraépítése](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality) című cikkekben található.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Nagyobb erőforrásosztály használata a lekérdezés teljesítményének javítása érdekében

Az SQL-készletek erőforráscsoportok használatával foglalnak le memóriát a lekérdezésekhez. Kezdetben az összes felhasználó hozzá van rendelve a kisméretű erőforrás osztályhoz, amely elosztás esetén 100 MB memóriát biztosít.  Mindig 60-disztribúció van. Az egyes eloszlások legalább 100 MB-ot kapnak. A teljes rendszerszintű memória kiosztása 6 000 MB, vagy csak 6 GB alatt.  

Bizonyos lekérdezéseknél, például a nagyobb egyesítéseknél vagy a fürtözött oszlopcentrikus táblákba végzett betöltésnél előnyt jelent a nagyobb memórialefoglalások használata.  Egyes lekérdezések, például a tiszta vizsgálatok, nem kapnak előnyt. A nagyobb erőforrás-osztályok kihasználása a párhuzamosságot befolyásolja. Ezért érdemes szem előtt tartani ezeket a tényeket, mielőtt az összes felhasználót egy nagy erőforrás-osztályba helyezné.

Az erőforrás-osztályokkal kapcsolatos további információkért tekintse meg az erőforrás-osztályok a számítási [feladatok kezelésével](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) foglalkozó cikket.

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Kisebb erőforrás-osztály használata a párhuzamosság növeléséhez

Ha hosszú késleltetést tapasztal a felhasználói lekérdezésekben, előfordulhat, hogy a felhasználók nagyobb erőforrás-osztályokban futnak. Ez a forgatókönyv az egyidejűségi tárolóhelyek felhasználását segíti elő, ami más lekérdezések várólistára helyezését is okozhatja.  Annak megállapításához, hogy a felhasználók lekérdezései várólistára kerültek-e, futtassa a parancsot, `SELECT * FROM sys.dm_pdw_waits` hogy megjelenjenek-e a sorok.

A számítási [feladatok kezeléséhez](../sql-data-warehouse/resource-classes-for-workload-management.md) és a [sys. dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) cikkekhez tartozó erőforrás-osztályok további információkat biztosítanak.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>A lekérdezések megfigyelése és optimalizálása DMV-kkel

Az SQL-készletek több DMV is használhatók a lekérdezések végrehajtásának figyelésére.  Az alábbi figyelési cikk lépésről lépésre bemutatja, hogyan tekintheti meg a végrehajtó lekérdezés részleteit.  Ha gyorsan szeretne lekérdezéseket kikeresni a DMV-kben, segíthet, ha a lekérdezéseknél használja a LABEL beállítást. További részletekért tekintse meg az alábbi listában szereplő cikkeket:

- [Monitor your workload using DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [CÍMKE](develop-label.md)
- [BEÁLLÍTÁS](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>További lépések

Tekintse meg a gyakori problémákkal és megoldásokkal kapcsolatos [hibaelhárítási](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) cikket is.

Ha a jelen cikkben nem szereplő információkra van szüksége, keresse fel a [Microsoft Q&az Azure szinapszis kérdéseit tartalmazó oldalt](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) , hogy kérdéseket tegyen fel más felhasználók és az SQL Pool termékcsoport számára.  

Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  Ha szeretne kérdéseket feltenni a Stack Overflowra, egy [Azure SQL-készlettel](https://stackoverflow.com/questions/tagged/azure-sqldw)is rendelkezünk stack overflow fórumban.

A szolgáltatásra vonatkozó kérelmek esetében használja az [Azure SQL Pool visszajelzési](https://feedback.azure.com/forums/307516-sql-data-warehouse) oldalát.  A kérések hozzáadása vagy más kérések szavazása segít nekünk, hogy a leginkább igény szerinti funkciókra összpontosítsanak.
