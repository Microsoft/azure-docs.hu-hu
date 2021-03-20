---
title: Fejlesztés – ajánlott eljárások
description: Javaslatok és ajánlott eljárások, amelyeket érdemes tudni a szinapszis SQL-készlethez kapcsolódó megoldások fejlesztésekor.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: bc101e6bb743b9290593937a4d1bac656430b438
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685310"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>Fejlesztési ajánlott eljárások a szinapszis SQL-készlethez

Ez a cikk útmutatást és ajánlott eljárásokat ismertet az SQL-készlet megoldásának fejlesztésekor.

## <a name="tune-query-performance-with-new-product-enhancements"></a>A lekérdezési teljesítmény finomhangolása új termékek fejlesztésével

- [Teljesítmény-finomhangolás tényleges táblán alapuló nézetekkel](performance-tuning-materialized-views.md)
- [Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával](performance-tuning-ordered-cci.md)
- [Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által

A költségek felfüggesztésével és skálázásával kapcsolatos további információkért lásd a [számítások kezelése](sql-data-warehouse-manage-compute-overview.md) című cikket.

## <a name="maintain-statistics"></a>Statisztikák karbantartása

Az SQL-készlet konfigurálható úgy, hogy automatikusan felderítse és létrehozza az oszlopok statisztikáit.  Az optimalizáló által létrehozott lekérdezési tervek csak a rendelkezésre álló statisztikáknak megfelelőek.  

Javasoljuk, hogy engedélyezze az adatbázisok AUTO_CREATE_STATISTICSét, és tartsa meg a statisztikákat naponta, vagy az egyes terhelések után, hogy a lekérdezésekben használt oszlopokra vonatkozó statisztikák mindig naprakészek legyenek.

Ha úgy találja, hogy túl sokáig tart az összes statisztika frissítése során, érdemes lehet több szelektíven kipróbálni, hogy mely oszlopokra van szükség a gyakori statisztikai frissítésekhez. Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek.

> [!TIP]
> A legtöbb előnyt úgy kapja meg, hogy frissített statisztikát készít az illesztések, a WHERE záradékban használt oszlopok és a GROUP BY által megtalált oszlopok esetében.

Lásd még: [táblák statisztikáinak kezelése](sql-data-warehouse-tables-statistics.md), [statisztikák létrehozása](sql-data-warehouse-tables-statistics.md)és [frissítési statisztika](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása

Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.  Ez a kialakítás megkönnyíti a felhasználók számára a táblák létrehozásának megkezdését anélkül, hogy el kellene dönteniük, hogyan kell terjeszteni a tábláikat.  

A ciklikus időszeletelésű táblák megfelelő megoldást jelenthetnek egye számítási feladatok esetén, a legtöbb esetben azonban egy elosztási oszlop kiválasztása megfelelőbb eredményre vezet.  A leggyakoribb példája annak, amikor egy oszloppal elosztott tábla sokkal jobb teljesítményt nyújt egy ciklikus időszeletelésűnél, amikor két nagy tábla egyesül.  

Ha például van egy rendelésazonosító alapján elosztott rendelési táblája és egy, szintén a rendelésazonosító alapján elosztott tranzakciótáblája, akkor ha a rendelési táblát a tranzakciótáblával egyesíti a rendelésazonosító oszlopnál, a lekérdezés továbbított lekérdezéssé válik, így kiküszöbölhetők az adatátviteli műveletek.  Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat.  A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.  

Elosztott tábla betöltésekor győződjön meg arról, hogy a bejövő adatok nem az elosztási kulcs alapján vannak rendezve, mivel ez lelassítja a betöltéseket.  A következő cikkek további részleteket adnak a teljesítmény javításához egy terjesztési oszlop kiválasztásával, valamint egy elosztott tábla definiálásával a CREATE TABLEs utasítás WITH záradékában.

Lásd még: [táblák áttekintése](sql-data-warehouse-tables-overview.md), [tábla eloszlása](sql-data-warehouse-tables-distribute.md), a [táblázat terjesztése](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [CREATE TABLE](sql-data-warehouse-tables-overview.md)és [CREATE TABLE](sql-data-warehouse-develop-ctas.md) kiválasztása

## <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése

Míg az adatparticionálás hatékonyan kezelheti az adatait a partíciók váltásával vagy a vizsgálatok optimalizálásával a partíciók eltávolításával, a túl sok partíció lelassíthatja a lekérdezéseket.  

Gyakran előfordul, hogy a nagy részletességű particionálási stratégia, amely jól működik, SQL Server előfordulhat, hogy nem működik megfelelően az SQL-készleten.  A túl sok partíció a fürtözött oszlopcentrikus indexek hatékonyságát is csökkentheti, ha az egyes partíciók kevesebb mint 1 millió sorral rendelkeznek.  

Ne feledje, hogy a színfalak mögött az SQL Pool a 60-adatbázisokba particionálja az adatait, így ha 100-partíciót tartalmazó táblát hoz létre, akkor ez a 6000-partíciók tényleges eredményét eredményezi.  Mindegyik számítási feladat különböző, így érdemes kísérletezni a particionálással, és kideríteni, hogy az adott számítási feladatnál melyik megoldás a célravezető.  

> [!TIP]
> Érdemes lehet alacsonyabb részletességet használni, mint amit a SQL Serverban dolgozott.  A napi partíciók helyett például érdemes lehet heti vagy havi partíciókat használni.

Lásd még: [tábla particionálás](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása

Az INSERT, UPDATE és DELETE utasítások tranzakcióban futnak, és amikor meghiúsulnak, vissza kell őket állítani.  A hosszú visszaállítások előfordulásának csökkentése érdekében mindig minimalizálja a tranzakciók méretét, ahol erre lehetősége van.  Ezt úgy teheti meg, hogy részekre osztja az INSERT, UPDATE és DELETE utasításokat.  

Ha például van egy INSERT (beszúrt), amelynek várhatóan 1 órát vesz igénybe, akkor a BESZÚRÁSt négy részre kell bontania, amely 15 percen belül fut.  A visszaállítási kockázat csökkentése érdekében speciális minimális naplózási eseteket, például a CTAS, a CSONKOLT, a DROP TABLE vagy az INSERT (üres) táblázatokat használja.  

A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Ahelyett például, hogy DELETE utasítással törölné egy tábla összes olyan sorát, ahol az order_date 2001. október, havonta particionálhatja az adatokat, majd az adatokkal rendelkező partíciót egy másik tábla üres partíciójára cserélheti (lásd az ALTER TABLE-re vonatkozó példákat).  

A nem particionált táblák esetében érdemes lehet egy CTAS használni a táblákat megőrizni kívánt, a TÖRLÉSt nem használó adatbevitelre.  Ha egy CTAS ugyanannyi időt vesz igénybe, az sokkal biztonságosabb művelet, mert minimális tranzakciós naplózással rendelkezik, és szükség esetén gyorsan megszakítható.

Lásd még: [tranzakciók megismerése](sql-data-warehouse-develop-transactions.md), [tranzakciók optimalizálása](sql-data-warehouse-develop-best-practices-transactions.md), [táblák particionálása](sql-data-warehouse-tables-partition.md), [truncate Table](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)és [CREATE TABLE as Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata

A DDL meghatározásakor az adatait támogató legkisebb adattípussal javíthatja a lekérdezési teljesítményt.  Ez a megközelítés különösen fontos a CHAR és a VARCHAR oszlopok esetében.  

Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  NVARCHAR helyett VARCHAR típusként határozza meg az oszlopokat, amikor ez is elegendő.

Lásd még: [táblák áttekintése](sql-data-warehouse-tables-overview.md), [tábla adattípusok](sql-data-warehouse-tables-data-types.md)és [CREATE TABLE](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása

A fürtözött oszlopcentrikus indexek az egyik leghatékonyabb módszer az SQL-készletben tárolt adattároláshoz.  Alapértelmezés szerint az SQL-készletben lévő táblák fürtözött Oszlopcentrikus jönnek létre.  

> [!NOTE]
> Ahhoz, hogy a oszlopcentrikus-táblák lekérdezései optimális teljesítményt érjenek el, fontos a megfelelő szegmensek minősége.  

Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  A szegmensminőség a tömörített sorcsoportokban található sorok száma alapján mérhető fel.  

A fürtözött oszlopcentrikus-táblák szegmensének minőségének észlelésével és javításával kapcsolatos részletes utasításokért tekintse meg a [gyenge oszlopcentrikus indexének okait](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) a [táblázat indexek](sql-data-warehouse-tables-index.md) című cikkben.  

Mivel a kiváló minőségű oszlopcentrikus szegmensek fontosak, hasznos lehet a közepes vagy nagy erőforrás-osztályba tartozó felhasználói azonosítók használata az betöltéshez. Az alacsonyabb [adattárház-egységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) használata azt jelenti, hogy nagyobb erőforrás-osztályt szeretne hozzárendelni a betöltési felhasználóhoz.

Mivel a oszlopcentrikus-táblák általában nem küldenek le az adatlemezeket egy tömörített oszlopcentrikus, amíg a táblázat több mint 1 000 000 sort tartalmaz, és minden SQL Pool-tábla 60-táblázatba van particionálva, általában a oszlopcentrikus-táblák nem kapnak lekérdezést, kivéve, ha a tábla több mint 60 000 000 sorral rendelkezik.  

Az 60 000 000-nál kevesebb sorral rendelkező táblák esetében előfordulhat, hogy a oszlopcentrikus index nem feltétlenül szükséges.  A használatuk azonban hátrányt sem jelent.  

Ha particionálja az adatait, akkor azt is érdemes figyelembe venni, hogy minden partíciónak 1 millió sorral kell rendelkeznie a fürtözött oszlopcentrikus indexek előnyeinek kihasználása érdekében.  Ha egy tábla 100 partíciót tartalmaz, akkor legalább 6 000 000 000 sort kell használnia a fürtözött oszlopok tárolójának kihasználása érdekében (60 distributers *100 partitions* 1 000 000 sor).  

Ha a tábla nem rendelkezik 6 milliárd sorral, akkor csökkentse a partíciók számát, vagy fontolja meg egy halomtábla használatát.  Azzal is érdemes lehet kísérletezni, hogy jobb teljesítmény érhető-e el egy másodlagos indexekkel rendelkező halomtáblával, mint egy oszlopcentrikussal.

> [!TIP]
> Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  

Lásd még: [Table indexek](sql-data-warehouse-tables-index.md), [oszlopcentrikus indexek útmutató](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)és [oszlopcentrikus indexek újjáépítése](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>Következő lépések

Ha nem találja, amit ebben a cikkben keres, próbálkozzon az oldal bal oldalán található "dokumentumok keresése" kifejezéssel az összes Azure szinapszis-dokumentum kereséséhez.  

A [Microsoft Q&az Azure szinapszis-hoz kapcsolódó kérdés oldalának](/answers/topics/azure-synapse-analytics.html) célja, hogy kérdéseket tegyenek fel más felhasználók és az Azure szinapszis-termékcsoportra.  Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  

Ha szeretne kérdéseket feltenni a Stack Overflowra, egy Azure-beli [szinapszis Analytics-stack overflow fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)is rendelkezésre áll.

A szolgáltatással kapcsolatos kérések létrehozásához használja az [Azure szinapszis visszajelzési](https://feedback.azure.com/forums/307516-sql-data-warehouse) oldalát.  Ha kéréseket tesz közzé vagy más kérésekre szavaz, sokat segíthet a szolgáltatások fontossági sorrendjének megállapításában.