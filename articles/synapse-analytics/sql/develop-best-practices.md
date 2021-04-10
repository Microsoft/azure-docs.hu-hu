---
title: Fejlesztési ajánlott eljárások a szinapszis SQL-hez
description: Javaslatok és ajánlott eljárások, amelyeket érdemes tudni a szinapszis SQL-hez való fejlesztéshez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 479b20bcb0803d5483d139939da627d53ceccb49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667613"
---
# <a name="development-best-practices-for-synapse-sql"></a>Fejlesztési ajánlott eljárások a szinapszis SQL-hez

Ez a cikk útmutatást és ajánlott eljárásokat ismertet az adattárház-megoldás fejlesztése során. 

## <a name="dedicated-sql-pool-development-best-practices"></a>Az SQL Pool dedikált fejlesztése – ajánlott eljárások

### <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által

A költségek felfüggesztéssel és skálázással való csökkentéséről további információért tekintse meg a [Számítások kezelése](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című szakaszt.

### <a name="maintain-statistics"></a>Statisztikák karbantartása

Győződjön meg róla, hogy naponta vagy minden egyes terhelés után frissíti a statisztikát.  Mindig érdemes figyelembe venni, hogyan viszonyul egymáshoz a teljesítmény és a statisztikák létrehozásának és frissítésének költségei. Ha úgy találja, hogy túl sokáig tart az összes statisztika fenntartása érdekében, érdemes lehet szelektívebb, hogy mely oszlopokban vannak statisztikái, vagy mely oszlopoknak gyakori frissítésre van szükségük.  

Előfordulhat például, hogy frissíteni szeretné a dátum oszlopokat, ahol új értékek is hozzáadhatók naponta. 

> [!NOTE]
> A legtöbb előnyt az összekapcsolások, a WHERE záradékban használt oszlopok, valamint a GROUP BY által megtalált oszlopok statisztikájának használatával szerezheti be.

Lásd még: [táblák statisztikáinak kezelése](develop-tables-statistics.md), [statisztikák létrehozása](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true), [statisztikák frissítése](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

### <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása

Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva. Ez a képesség megkönnyíti a felhasználók számára a táblázatok létrehozását anélkül, hogy el kellene dönteniük a táblák elosztásának módját.  A ciklikus időszeletelésű táblázatok bizonyos munkaterhelések esetén megfelelően elvégezhetnek. Azonban a legtöbb esetben a terjesztési oszlop kiválasztása sokkal jobb teljesítményt tesz elérhetővé.  

A leggyakoribb példája annak, amikor egy oszloppal elosztott tábla sokkal jobb teljesítményt nyújt egy ciklikus időszeletelésűnél, amikor két nagy tábla egyesül.  

Ha például egy order_id által terjesztett Orders táblával rendelkezik, és egy order_id által is terjesztett tranzakciós táblát, amely az Orders (megrendelések) táblát a order_id tranzakciós táblájához csatlakoztatja, a lekérdezés egy átmenő lekérdezés lesz. 

Ez azt jelenti, hogy megkerüljük az adatáthelyezési műveleteket.  Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat.  A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.

> [!TIP]
> Elosztott tábla betöltésekor győződjön meg arról, hogy a bejövő adatok nem az elosztási kulcs alapján vannak rendezve, mivel ez lelassítja a betöltéseket.  

A terjesztési oszlopok kiválasztásával kapcsolatos további részletekért tekintse meg a következő hivatkozásokat, valamint azt, hogyan határozhat meg egy elosztott táblát a CREATE TABLEs utasítás WITH záradékában.

Lásd még: [táblák áttekintése](develop-tables-overview.md), [táblázat terjesztése](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), a [táblázat terjesztése](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)és [CREATE TABLE kiválasztása](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

### <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése
Míg az adatparticionálás hatékonyan kezelheti az adatait a partíciók váltásával vagy a vizsgálatok optimalizálásával a partíciók eltávolításával, a túl sok partíció lelassíthatja a lekérdezéseket.  Gyakran olyan nagy részletességű particionálási stratégia, amely jól használható SQL Server előfordulhat, hogy nem működik megfelelően a dedikált SQL-készleten.  

> [!NOTE]
> Gyakran olyan nagy részletességű particionálási stratégia, amely jól használható SQL Server előfordulhat, hogy nem működik megfelelően a dedikált SQL-készleten.  

A túl sok partíció a fürtözött oszlopcentrikus indexek hatékonyságát is csökkentheti, ha az egyes partíciók kevesebb mint 1 millió sorral rendelkeznek. A dedikált SQL Pool a 60-adatbázisokba particionálja adatait. 

Tehát ha 100 partíciót tartalmazó táblát hoz létre, akkor az eredmény 6000 partíció lesz.  Mindegyik számítási feladat különböző, így érdemes kísérletezni a particionálással, és kideríteni, hogy az adott számítási feladatnál melyik megoldás a célravezető.  

Az egyik lehetőség az, hogy egy olyan részletességet használ, amely alacsonyabb, mint amit a SQL Serverban dolgozott.  A napi partíciók helyett például érdemes lehet heti vagy havi partíciókat használni.

Lásd még: [tábla particionálás](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása

Az INSERT, UPDATE és DELETE utasítások tranzakcióban futnak, és amikor meghiúsulnak, vissza kell őket állítani.  A hosszú visszaállítások előfordulásának csökkentése érdekében mindig minimalizálja a tranzakciók méretét, ahol erre lehetősége van.  Ezt úgy teheti meg, hogy részekre osztja az INSERT, UPDATE és DELETE utasításokat.  

Ha például egy olyan BETÉTtel rendelkezik, amely 1 órát vesz igénybe, akkor a BESZÚRÁSt négy részre bontja, így az egyes futtatások 15 percenként lerövidítve jelennek meg.

> [!TIP]
> Használja ki a minimális naplózást igénylő speciális eseteket, például a CTAS, TRUNCATE, DROP TABLE vagy INSERT utasításokat a táblák kiürítéséhez, hogy csökkentse a visszaállítással kapcsolatos kockázatokat.  

A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  

Ahelyett például, hogy DELETE utasítással törölné egy tábla összes olyan sorát, ahol az order_date 2001. október, havonta particionálhatja az adatokat, majd az adatokkal rendelkező partíciót egy másik tábla üres partíciójára cserélheti (lásd az ALTER TABLE-re vonatkozó példákat).  

A nem particionált táblák esetében érdemes lehet egy CTAS használni a táblákat megőrizni kívánt, a TÖRLÉSt nem használó adatbevitelre.  Ha egy CTAS ugyanannyi időt vesz igénybe, az sokkal biztonságosabb művelet, mert minimális tranzakciós naplózással rendelkezik, és szükség esetén gyorsan megszakítható.

Lásd még: [tranzakciók megismerése](develop-transactions.md), [tranzakciók optimalizálása](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [táblák particionálása](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [truncate Table](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true)és [CREATE TABLE as Select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata

A DDL meghatározásakor az adatait támogató legkisebb adattípussal javíthatja a lekérdezési teljesítményt. Ez a művelet különösen fontos a CHAR és a VARCHAR oszlopok esetében.  

Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  Azt is megadhatja, hogy az oszlopok a NVARCHAR használata helyett VARCHAR-ként legyenek megadva.

Lásd még: [táblák áttekintése](develop-tables-overview.md), [tábla adattípusok](develop-tables-data-types.md)és [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

### <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása

A fürtözött oszlopcentrikus indexek az egyik leghatékonyabb módszer, amellyel az adatai a dedikált SQL-készletben tárolhatók.  Alapértelmezés szerint a dedikált SQL-készletben lévő táblák fürtözött Oszlopcentrikus jönnek létre.  

Annak érdekében, hogy az oszlopcentrikus táblák a lehető legjobb teljesítménnyel fussanak, fontos a jó szegmensminőség.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  

A szegmensminőség a tömörített sorcsoportokban található sorok száma alapján mérhető fel.  A fürtözött oszlopcentrikus-táblák szegmensének minőségének észlelésével és javításával kapcsolatos részletes utasításokért tekintse meg a [gyenge oszlopcentrikus index minőségének okait](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) és a [táblázat indexeit](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ismertető cikket.  

Mivel a kiváló minőségű oszlopcentrikus szegmensek fontosak, hasznos lehet a közepes vagy nagy erőforrás-osztályba tartozó felhasználói azonosítók használata az betöltéshez. Az alacsonyabb [adattárház-egységek](resource-consumption-models.md) használata azt jelenti, hogy nagyobb erőforrás-osztályt szeretne hozzárendelni a betöltési felhasználóhoz.

Mivel a oszlopcentrikus-táblák általában nem küldenek le az adataikat egy tömörített oszlopcentrikus, amíg a táblázat több mint 1 000 000 sort tartalmaz, és minden dedikált SQL Pool-tábla 60-táblázatba van particionálva, a oszlopcentrikus-táblák nem kapnak lekérdezést, kivéve, ha a tábla több mint 60 000 000 sorral rendelkezik.  

> [!TIP]
> Az 60 000 000-nál kevesebb sorral rendelkező táblák esetében előfordulhat, hogy a oszlopcentrikus index nem az optimális megoldás.  

Továbbá, ha particionálja az adatait, érdemes figyelembe vennie, hogy az egyes partíciók esetében 1 000 000 sor szükséges a fürtözött oszlopcentrikus-index kihasználása érdekében.  Ha egy tábla 100 partíciót tartalmaz, akkor legalább 6 000 000 000 sort kell használnia a fürtözött oszlopok tárolójának kihasználása érdekében (60 distributers *100 partitions* 1 000 000 sor).  

Ha a táblához nem tartozik 6 000 000 000 sor, csökkentse a partíciók számát, vagy használjon egy halom táblát.  Azt is érdemes lehet kísérletezni, hogy a jobb teljesítmény megszerezhető-e egy oszlopcentrikus tábla helyett a másodlagos indexekkel rendelkező heap tábla használatával.

Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  

Lásd még: [Table indexek](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [oszlopcentrikus indexek útmutató](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true), [oszlopcentrikus indexek újjáépítése](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="serverless-sql-pool-development-best-practices"></a>A kiszolgáló nélküli SQL Pool fejlesztése – ajánlott eljárások

### <a name="general-considerations"></a>Általános megfontolások

A kiszolgáló nélküli SQL-készlet lehetővé teszi a fájlok lekérdezését az Azure Storage-fiókokban. Nem rendelkezik helyi tárterülettel vagy betöltési képességekkel, ami azt jelenti, hogy a lekérdezés által célként megadott összes fájl kívül van a kiszolgáló nélküli SQL-készleten. Így a fájlok tárterületről való olvasásával kapcsolatos minden művelet hatással lehet a lekérdezés teljesítményére.

### <a name="colocate-azure-storage-account-and-serverless-sql-pool"></a>Azure Storage-fiók és kiszolgáló nélküli SQL-készlet közös elhelyezése

A késés csökkentése érdekében helyezze el az Azure Storage-fiókot és a kiszolgáló nélküli SQL-készlet végpontját. A munkaterület létrehozása során kiépített Storage-fiókok és-végpontok ugyanabban a régióban találhatók.

Az optimális teljesítmény érdekében, ha a kiszolgáló nélküli SQL-készlettel rendelkező más Storage-fiókokhoz fér hozzá, ügyeljen arra, hogy ugyanabban a régióban legyenek. Ellenkező esetben a távoli régióról a végpont régiójára irányuló adatátvitel nagyobb késéssel jár.

### <a name="azure-storage-throttling"></a>Azure Storage-szabályozás

Több alkalmazás és szolgáltatás is hozzáférhet a Storage-fiókhoz. Ha az alkalmazások, a szolgáltatások és a kiszolgáló nélküli SQL-készlet számítási feladata által generált kombinált IOPS vagy átviteli sebesség meghaladja a Storage-fiók korlátait, a tárterület-szabályozás történik. Tárolási sávszélesség esetén jelentős negatív hatással van a lekérdezés teljesítményére.

A szabályozás észlelése után a kiszolgáló nélküli SQL-készlet beépített kezelést tartalmaz ebben a forgatókönyvben. A kiszolgáló nélküli SQL-készlet lassabb ütemben kéri a tárolást, amíg a szabályozás meg nem oldódik. 

Az optimális lekérdezés-végrehajtás érdekében azonban javasoljuk, hogy a lekérdezés végrehajtása során ne hangsúlyozza a Storage-fiókot más munkaterhelésekkel.

### <a name="prepare-files-for-querying"></a>Fájlok előkészítése lekérdezéshez

Ha lehetséges, készíthet fájlokat a jobb teljesítmény érdekében:

- CSV konvertálása a parkettára – a parketta oszlopos formátumú. Mivel tömörítve van, kisebb fájlméretet tartalmaz, mint a CSV-fájlok ugyanazzal az adattal, és a kiszolgáló nélküli SQL-készletnek kevesebb időt és tárolási kérést kell elolvasnia.
- Ha egy lekérdezés egyetlen nagyméretű fájlt céloz meg, akkor a több kisebb fájlra való felosztása is hasznos lesz.
- Próbálja megtartani a CSV-fájl méretét 10GB-ban.
- Azt javasolt, hogy egyenlő méretű fájlokat lehessen használni egyetlen OPENROWSET elérési úthoz vagy egy külső tábla HELYéhez.
- Particionálja az adatait úgy, hogy a partíciókat különböző mappákba vagy fájlnevekre tárolja, [majd a fájlnév és a filepath függvények használatával adja meg az adott partíciókat](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>A fileinfo és filepath függvények használata adott partíciók célzásához

Az adathalmazok gyakran partíciókban vannak rendszerezve. A kiszolgáló nélküli SQL-készletet utasíthatja arra, hogy adott mappákat és fájlokat Kérdezzen le. Ez csökkenti a fájlok számát és az adatmennyiséget, amelyet a lekérdezésnek el kell olvasnia és fel kell dolgoznia. 

Így jobb teljesítményt érhet el. További információért olvassa el a [filename](query-data-storage.md#filename-function) és a [filepath](query-data-storage.md#filepath-function) függvények és példák című témakört a [megadott fájlok lekérdezéséhez](query-specific-files.md).

Ha a tárolóban lévő adatok particionálása nem történik meg, érdemes particionálni, hogy ezeket a függvényeket a fájlokra irányuló lekérdezések optimalizálására is használhatja.

Ha az [Azure szinapszis külső tábláihoz particionált Apache Spark lekérdezést](develop-storage-files-spark-tables.md) végez kiszolgáló nélküli SQL-készletből, a lekérdezés automatikusan csak a szükséges fájlokat célozza meg.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>A CETAS használata a lekérdezések teljesítményének és illesztésének növeléséhez

A [CETAS](develop-tables-cetas.md) a kiszolgáló nélküli SQL-készlet legfontosabb funkcióinak egyike. A CETAS egy párhuzamos művelet, amely létrehozza a külső tábla metaadatait, és exportálja a SELECT lekérdezés eredményét a Storage-fiókban lévő fájlok egy halmazára.

A CETAS-t használhatja a lekérdezések gyakran használt részeinek tárolására, például az összekapcsolt hivatkozási táblákat egy új fájlra. Később is csatlakozhat ehhez az egyetlen külső táblához, és nem kell ismétlődő közös illesztéseket használnia több lekérdezésben. 

Ahogy a CETAS a parketta-fájlokat hozza létre, a statisztikák automatikusan létrejönnek, amikor az első lekérdezés ezt a külső táblát célozza meg, és jobb teljesítményt fog nyerni.

### <a name="next-steps"></a>Következő lépések

Ha a jelen cikkben nem szereplő információkra van szüksége, használja az oldal bal oldalán található **Keresés a doc** függvényt az SQL-készlet összes dokumentumának kereséséhez.  A [Microsoft Q&az Azure szinapszis Analytics-re vonatkozó kérdés oldalának](/answers/topics/azure-synapse-analytics.html) célja, hogy kérdéseket tegyen fel más felhasználók és az Azure szinapszis Analytics-termékcsoport számára. Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  

Ha szeretne kérdéseket feltenni a Stack Overflowra, egy Azure-beli [szinapszis Analytics-stack overflow fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)is rendelkezésre áll.
