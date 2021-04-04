---
title: Az adatátviteli teljesítmény növelése a Azure SQL Database Azure Stream Analytics
description: Ismerkedjen meg az adatok SQL Azure Azure Stream Analytics és magasabb írási sebesség elérésével.
author: chetanmsft
ms.author: chetang
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 8baa33c8d9622ff76db04345f5c6c465f026e261
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020230"
---
# <a name="increase-throughput-performance-to-azure-sql-database-from-azure-stream-analytics"></a>Az adatátviteli teljesítmény növelése a Azure SQL Database Azure Stream Analytics

Ez a cikk azokat a tippeket ismerteti, amelyekkel jobb írási teljesítményt érhet el, ha az adatok betöltését Azure SQL Database Azure Stream Analytics használatával végzi.

A Azure Stream Analyticsban az SQL-kimenet támogatja az írást párhuzamosan, lehetőségként. Ez a beállítás lehetővé teszi a [teljes párhuzamos](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) feladatok topológiáit, amelyekben egyszerre több kimeneti partíció is írásra kerül a célhely táblába. A beállítás engedélyezése Azure Stream Analytics azonban nem lehet elegendő a nagyobb átviteli sebesség eléréséhez, mivel az adatbázis-konfiguráció és a tábla sémája jelentősen függ. Az indexek, a fürtözési kulcs, az indexek kitöltési tényezője és a tömörítés megválasztása hatással van a táblázatok betöltésének idejére. További információ arról, hogyan optimalizálhatja az adatbázisát a lekérdezés és a teljesítmény növeléséhez a belső referenciaértékek alapján: [SQL Database teljesítmény-útmutatást](../azure-sql/database/performance-guidance.md). Az írási sorrend nem garantált, ha párhuzamosan SQL Database.

Íme néhány konfiguráció az egyes szolgáltatásokon belül, amelyek segíthetnek a megoldás általános teljesítményének javításában.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Particionálás öröklése** – ez az SQL kimeneti konfigurációs beállítás lehetővé teszi az előző lekérdezési lépés vagy bemenet particionálási sémájának öröklését. Ezzel a beállítással egy lemezes táblára írhat, és [teljes mértékben párhuzamos](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topológiával rendelkezik a feladatokhoz, ezért várhatóan jobb teljesítmény érhető el. Ez a particionálás már automatikusan megtörténik számos más [kimenetnél](stream-analytics-parallelization.md#partitions-in-inputs-and-outputs). A tábla zárolása (TABLOCK opció) le van tiltva az ezzel a kapcsolóval készített tömeges beszúrások esetében is.

> [!NOTE] 
> Ha több mint 8 bemeneti partíció van, akkor előfordulhat, hogy a bemeneti particionálási séma öröklése nem megfelelő választás. Ez a felső korlát egyetlen azonosító oszlopot és egy fürtözött indexet tartalmazó táblában volt megfigyelve. Ebben az esetben [érdemes a lekérdezésben 8-](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) at használni, hogy explicit módon meg lehessen adni a kimeneti írók számát. A séma és az indexek kiválasztása alapján a megfigyelések eltérőek lehetnek.

- **Batch-méret** – az SQL kimeneti konfiguráció lehetővé teszi, hogy egy Azure stream Analytics SQL-kimenetben megadja a Batch maximális méretét a céltábla/munkaterhelések természetétől függően. A köteg mérete az összes tömegesen beszúrt tranzakcióval ellátott rekordok maximális száma. A fürtözött oszlopcentrikus indexekben a 100 000-es köteg mérete nagyobb párhuzamos, minimális naplózást és zárolási optimalizálást [tesz lehetővé.](/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) A lemezes táblákban a 10K (alapértelmezett) vagy az alacsonyabb érték lehet optimális a megoldáshoz, mivel a nagyobb méretű kötegek a tömeges beszúrások során is kiválthatják a zárolási eszkalációt.

- **Bemeneti üzenet finombeállítása** – ha optimalizálta a particionálás és a köteg méretének öröklését, az üzenetekben lévő bemeneti események számának növelésével az írási sebesség tovább fog megjelenni. A bemeneti üzenet finomhangolása lehetővé teszi, hogy a Azure Stream Analytics belül a Batch-méretek a megadott batch-méretig legyenek felhasználva, ami javítja a teljesítményt. Ez [tömörítéssel](stream-analytics-define-inputs.md) vagy a bemeneti üzenetek méretének növelésével érhető el a EventHub vagy a blobban.

## <a name="sql-azure"></a>SQL Azure

- **Particionált tábla és indexek** [– particionált SQL-](/sql/relational-databases/partitions/partitioned-tables-and-indexes) táblázat és particionált indexek használatával a táblán ugyanazzal az oszloppal, mint a partíciós kulccsal (például PartitionID) az írás során jelentősen csökkenthetik a partíciók közötti adattartalmakat. Particionált tábla esetén létre kell hoznia egy [Partition függvényt](/sql/t-sql/statements/create-partition-function-transact-sql) és egy [partíciós sémát](/sql/t-sql/statements/create-partition-scheme-transact-sql) az elsődleges fájlcsoportja. Ez a meglévő adatmennyiségek rendelkezésre állását is növeli az új adatbetöltések során. A log IO-korlát lehet a partíciók száma alapján, ami növelhető az SKU frissítésével.

- **Kerülje az egyedi kulcs megsértését** – ha [több kulcs megsértését jelző figyelmeztető üzenetet](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) kap a Azure stream Analyticsi tevékenység naplójában, ügyeljen arra, hogy a feladatot ne befolyásolja az egyedi korlátozás megsértése, amely valószínűleg a helyreállítási esetekben fog történni. Ennek elkerüléséhez állítsa be a [dup- \_ \_ kulcs mellőzése](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) beállítást az indexeken.

## <a name="azure-data-factory-and-in-memory-tables"></a>Táblázatok Azure Data Factory és In-Memory

- Memóriában tárolt **tábla** – a memóriában lévő [táblák](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) , amelyek nagy sebességű adatterheléseket tesznek lehetővé, de az adatmennyiségnek a memóriában kell lennie. A Teljesítménytesztek egy memóriában tárolt táblázatból származó tömeges betöltést jelenítenek meg a lemezes táblákhoz képest, mint a közvetlen tömeges beszúrások egy azonosító oszlop és egy fürtözött index használatával történő, a lemezes táblába való közvetlen kihelyezése során. A tömeges beszúrási teljesítmény kihasználása érdekében állítson be egy [másolási feladatot olyan Azure Data Factory használatával](../data-factory/connector-azure-sql-database.md) , amely az adatok memóriából való másolása a lemez alapú táblába.

## <a name="avoiding-performance-pitfalls"></a>A teljesítménybeli buktatók elkerülése
Az adatok tömeges beszúrása sokkal gyorsabb, mint az adatok betöltése egyetlen beszúrással, mert az adatok átvitelének ismétlődő terhelése, az INSERT utasítás elemzése, az utasítás futtatása és a tranzakciós rekord kiállítása elkerülhető. Ehelyett a tárolási motor hatékonyabb elérési utat használ az adatstreamek továbbításához. Az elérési út telepítési díja azonban sokkal nagyobb, mint egy lemezes tábla egyetlen INSERT utasítása. A break-even pont általában körülbelül 100 sor, amelyeken a tömeges betöltés szinte mindig hatékonyabb. 

Ha a bejövő események aránya alacsony, könnyedén létrehozhat 100-nál kisebb méretű kötegeket, így a tömeges Beszúrás nem hatékony, és túl sok lemezterületet használ. A korlátozás megkerüléséhez a következő műveletek közül választhat:
* Az [trigger](/sql/t-sql/statements/create-trigger-transact-sql) helyett hozzon létre egyszerű beszúrást az összes sorhoz.
* Használjon In-Memory Temp táblát az előző szakaszban leírtak szerint.

Egy másik ilyen forgatókönyv akkor fordul elő, ha nem fürtözött oszlopcentrikus indexbe (NCCI) ír, ahol a kisebb tömeges beszúrások túl sok szegmenst hozhatnak létre, ami összeomlhat az indexben. Ebben az esetben az ajánlott, hogy fürtözött Oszlopcentrikus indexet használjon.

## <a name="summary"></a>Összefoglalás

Összefoglalva, az SQL-kimenethez Azure Stream Analytics particionált kimeneti funkciójával, a feladatoknak a SQL Azure particionált táblájával való igazításával jelentős teljesítménybeli párhuzamos kell biztosítania. A In-Memory táblából lemezes táblákba való adatáthelyezési Azure Data Factoryának kihasználása lehetővé teheti, hogy az adatátviteli teljesítmény megtörténjen. Ha lehetséges, az üzenetek sűrűségének javítása is jelentős tényező lehet a teljes átviteli sebesség javításában.
