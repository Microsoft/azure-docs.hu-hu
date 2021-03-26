---
title: Az Azure-beli logikai kiszolgálók erőforrás-korlátai
description: Ez a cikk áttekintést nyújt az Azure-beli logikai kiszolgáló Azure SQL Database és az Azure szinapszis Analytics által használt erőforrás-korlátairól. Emellett tájékoztatást nyújt arról is, hogy mi történik, ha az erőforrás korlátai elérik vagy túllépik azokat.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 03/25/2021
ms.openlocfilehash: 5e95bc50a74413389bd2583beb90128b3fd0810a
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543516"
---
# <a name="resource-limits-for-azure-sql-database-and-azure-synapse-analytics-servers"></a>A Azure SQL Database és az Azure szinapszis Analytics-kiszolgálók erőforrás-korlátai
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Ez a cikk áttekintést nyújt a Azure SQL Database és az Azure szinapszis Analytics által használt [logikai kiszolgáló](logical-servers.md) erőforrás-korlátairól. Információkkal szolgál arról, hogy mi történik, ha az adott erőforrás korlátai elérik vagy túllépik a korlátot, és ismerteti a korlátok kikényszeríthető erőforrás-irányítási mechanizmusait.

> [!NOTE]
> Az Azure SQL felügyelt példányokra vonatkozó korlátaival kapcsolatban lásd: [a felügyelt példányok erőforrás-korlátai](../managed-instance/resource-limits.md).

## <a name="maximum-resource-limits"></a>Erőforrás-korlátok maximális száma

| Erőforrás | Korlát |
| :--- | :--- |
| Adatbázisok/logikai kiszolgálók | 5000 |
| Logikai kiszolgálók alapértelmezett száma előfizetésben egy régióban | 20 |
| Logikai kiszolgálók maximális száma előfizetésben egy régióban | 200 |  
| DTU/eDTU kvóta/logikai kiszolgáló | 54 000 |  
| Virtuális mag kvóta/logikai kiszolgáló | 540 |
| Készletek maximális száma logikai kiszolgálónként | Korlátozott számú DTU vagy virtuális mag. Ha például az egyes készletek 1000 DTU, akkor a kiszolgáló támogatja az 54-es készleteket.|
|||

> [!IMPORTANT]
> Mivel az adatbázisok száma a logikai kiszolgálókon beállított korlátot közelíti meg, a következő műveletek végezhetők el:
>
> - Növekvő késés a főadatbázison futó lekérdezések futtatásakor.  Ebbe beletartozik az erőforrás-kihasználtsági statisztikák (például: `sys.resource_stats` ) nézetei.
> - Növekvő késés a felügyeleti műveletekben és a portálon olyan nézőpontok, amelyek a kiszolgáló adatbázisainak számbavételét foglalják magukban.

> [!NOTE]
> Ha további DTU/eDTU kvótát, virtuális mag kvótát vagy több logikai kiszolgálót szeretne beszerezni az alapértelmezett értéknél, küldjön új támogatási kérelmet a Azure Portal. További információ: [a kérelmek kvótájának növekedése Azure SQL Database](quota-increase-request.md).

### <a name="storage-size"></a>Tárterület mérete

Az önálló adatbázisok erőforrás-tárolási méreteit a [DTU-alapú erőforrás-korlátok](resource-limits-dtu-single-databases.md) vagy a [virtuális mag-alapú erőforrás-](resource-limits-vcore-single-databases.md) korlátok alapján, a tárterületre vonatkozó korlátok alapján, díjszabási szinten tekintheti meg.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Mi történik az adatbázis-erőforrások korlátainak elérésekor

### <a name="compute-cpu"></a>Számítási CPU

Ha az adatbázis számítási CPU-kihasználtsága magas lesz, a lekérdezés késése megnő, és a lekérdezések is időtúllépést okozhatnak. Ilyen körülmények között előfordulhat, hogy a szolgáltatás várólistára helyezi a lekérdezéseket, és erőforrásokat biztosít a végrehajtáshoz, mivel az erőforrások ingyenesek lesznek.
Ha magas számítási kihasználtságot tapasztal, a kockázatcsökkentő lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet számítási méretének növelése az adatbázis további számítási erőforrásokkal való biztosításához. Lásd: [önálló adatbázis-erőforrások méretezése](single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](elastic-pool-scale.md).
- Lekérdezések optimalizálása az egyes lekérdezések CPU-erőforrásai kihasználtságának csökkentése érdekében. További információkért lásd: [Lekérdezések finomhangolása/Javaslatok](performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Tárolás

Ha az adatbázis-terület eléri a maximális méretkorlátot, az adatbázis-beszúrások és az adatméretet növelő frissítések sikertelenek lesznek, és az ügyfelek [hibaüzenetet](troubleshoot-common-errors-issues.md)kapnak. A SELECT és DELETE utasítások továbbra is sikeresek lesznek.

A magas lemezterület-használat során a megoldás a következőkre terjed ki:

- Az adatbázis vagy a rugalmas készlet maximális méretének növelése, vagy további tárhely hozzáadása. Lásd: [önálló adatbázis-erőforrások méretezése](single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](elastic-pool-scale.md).
- Ha az adatbázis egy rugalmas készletben található, akkor azt is megteheti, hogy az adatbázist a készleten kívülre helyezi, hogy a tárolóhelye ne legyen megosztva más adatbázisokkal.
- Adatbázis zsugorítása a nem használt terület felszabadításához. További információ: [a tárterület kezelése Azure SQL Databaseban](file-space-manage.md).
- Ellenőrizze, hogy a magas lemezterület-kihasználtságot az állandó verziójú tároló (PVS) méretében lévő tüske okozza-e. A PVS az egyes adatbázisok részét képezi, és a  [gyorsított adatbázis-helyreállítás](../accelerated-database-recovery.md)megvalósítására szolgál. Az aktuális PVS-méret megállapításához tekintse meg a [PVS hibaelhárítását](/sql/relational-databases/accelerated-database-recovery-management#troubleshooting)ismertető témakört. A nagyméretű PVS méretének gyakori oka egy hosszú ideig (órákban) nyitott tranzakció, amely megakadályozza a régebbi verziók törlését a PVS-ben.

### <a name="sessions-and-workers-requests"></a>Munkamenetek és feldolgozók (kérelmek)

A munkamenetek és a feldolgozók maximális számát a szolgáltatási szintek és a számítási méret (DTU/Edtu vagy virtuális mag) határozzák meg. A rendszer elutasítja az új kérelmeket, ha a munkamenet-vagy feldolgozói korlátok elérésekor az ügyfelek hibaüzenetet kapnak. Míg az elérhető kapcsolatok száma az alkalmazás által szabályozható, az egyidejű feldolgozók száma gyakran nehezebb a becsléshez és a szabályozáshoz. Ez különösen igaz a maximális betöltési időszakok során, amikor az adatbázis-erőforrások korlátai elérik, a feldolgozók pedig hosszabb ideig futó lekérdezések, nagyméretű blokkoló láncok vagy túlzott lekérdezés-párhuzamosság miatt halmozódnak fel.

A magas munkamenet vagy munkavégző kihasználtsága esetén a kockázatcsökkentő lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet szolgáltatási szintjeinek vagy számítási méretének növelése. Lásd: [önálló adatbázis-erőforrások méretezése](single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](elastic-pool-scale.md).
- A lekérdezések optimalizálása az egyes lekérdezések erőforrás-kihasználtságának csökkentése érdekében, ha a munkavégzők nagyobb kihasználtságának oka a számítási erőforrások miatti kihasználása. További információkért lásd: [Lekérdezések finomhangolása/Javaslatok](performance-guidance.md#query-tuning-and-hinting).
- Csökkentse a [MAXDOP](/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maximális párhuzamossági fok) beállítást.
- A lekérdezési munkaterhelés optimalizálása az előfordulások számának csökkentése és a lekérdezés letiltásának időtartama alapján. További információ: az [Azure SQL-blokkoló problémáinak megismerése és megoldása](understand-resolve-blocking.md).

### <a name="memory"></a>Memória

Más erőforrásokkal (CPU, munkavégzők, tárterület) ellentétben a memória korlátja nem befolyásolja negatívan a lekérdezési teljesítményt, és nem okoz hibákat és hibákat. A [memória-kezelési architektúra útmutatójában](/sql/relational-databases/memory-management-architecture-guide)leírtak szerint a SQL Server adatbázismotor gyakran használja az összes rendelkezésre álló memóriát a tervezés szerint. A memóriát elsősorban az adatgyorsítótárazáshoz használják, hogy elkerülje a drágább tárterület-hozzáférést. Így a magasabb memóriahasználat általában a memóriából való gyorsabb olvasások miatt javítja a lekérdezési teljesítményt, nem pedig a tárterület lassabb olvasását.

Az adatbázismotor elindítása után, ahogy a munkaterhelés elkezdi beolvasni az adatok tárolásból való beolvasását, az adatbázismotor agresszív módon gyorsítótárazza a memóriában tárolt adatok mennyiségét. A kezdeti felfutási időszak után gyakori és várható, hogy a `avg_memory_usage_percent` sys.dm_db_resource_stats és az `avg_instance_memory_percent` oszlopok az 100 [](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) %-ban vagy azzal egyenlőek legyenek, különösen olyan adatbázisok esetén, amelyek nem tétlenek, és nem teljes mértékben illenek a memóriába.

Az adatgyorsítótáron kívül a rendszer a memóriát használja az adatbázismotor más összetevőiben. Ha igény van a memóriára, és az összes rendelkezésre álló memóriát felhasználta az adatgyorsítótár, az adatbázismotor dinamikusan csökkenti az adatgyorsítótár méretét, hogy a memóriát elérhetővé tegye más összetevők számára, és dinamikusan növelje az adatgyorsítótárat, amikor más összetevők kibocsátják a memóriát.

Ritka esetekben a megfelelően megterhelt számítási feladatok elégtelen memória-feltételt okozhatnak, ami a memórián kívüli hibákhoz vezethet. Ez a memóriahasználat bármely szintjén történhet 0% és 100% között. Ez nagyobb valószínűséggel fordul elő kisebb számítási méretekben, amelyeknek arányosan kisebb a memóriája, és/vagy a munkaterhelések több memóriát használnak a lekérdezések feldolgozásához, például [sűrű rugalmas készletekben](elastic-pool-resource-management.md).

A memórián kívüli hibák észlelésekor a megoldás a következőkre terjed ki:
- Az adatbázis vagy a rugalmas készlet szolgáltatási szintjeinek vagy számítási méretének növelése. Lásd: [önálló adatbázis-erőforrások méretezése](single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](elastic-pool-scale.md).
- A lekérdezések és a konfiguráció optimalizálása a memória kihasználtságának csökkentése érdekében. Az alábbi táblázat ismerteti az általános megoldásokat.

|Megoldás|Description|
| :----- | :----- |
|A memóriabeli támogatások méretének csökkentése|A memória-támogatással kapcsolatos további információkért tekintse meg a [SQL Server memória-engedélyezés](https://techcommunity.microsoft.com/t5/sql-server/understanding-sql-server-memory-grant/ba-p/383595) blogbejegyzésének ismertetése című témakört. A túlzottan nagy memória-támogatás elkerülésére szolgáló közös megoldás a [statisztikák](/sql/relational-databases/statistics/statistics) naprakészen tartása. Ez a lekérdezési motor által a memória-használat pontosabb becslését eredményezi, így elkerülhető a szükségtelenül nagy memória-támogatás.</br></br>Az adatbázis-kezelő a 140-es és újabb kompatibilitási szintet használó adatbázisokban automatikusan megváltoztathatja a memória-engedélyezési méretet a [Batch Mode memória-engedélyezési visszajelzések](/sql/relational-databases/performance/intelligent-query-processing#batch-mode-memory-grant-feedback)használatával. Az adatbázis-kezelő a 150-es és újabb kompatibilitási szintet használó adatbázisokban hasonlóan a [sor módú memória-engedélyezési visszajelzéseket](/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)is használja a leggyakoribb soros üzemmódú lekérdezéseknél. Ez a beépített funkció segít elkerülni a memórián belüli hibákat a szükségtelenül nagy memória-támogatás miatt.|
|A lekérdezési terv gyorsítótára méretének csökkentése|Az adatbázismotor gyorsítótárazza a memóriában a lekérdezési terveket, így elkerülhető a lekérdezés-végrehajtás minden lekérdezési tervének fordítása. Ha el szeretné kerülni, hogy a lekérdezési terv gyorsítótára a csak egyszer használt gyorsítótárazási csomagokat okozza, engedélyezze a OPTIMIZE_FOR_AD_HOC_WORKLOADS [adatbázis-hatókörű konfigurációt](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).|
|A zárolási memória méretének csökkentése|Az adatbázismotor memóriát használ a [zárolásokhoz](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#Lock_Engine). Ha lehetséges, kerülje a nagyméretű tranzakciókat, amelyek nagy számú zárolást igényelhetnek, és nagy mennyiségű zárolási memóriát okozhatnak.|


## <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Erőforrás-felhasználás felhasználói munkaterhelések és belső folyamatok alapján

Az egyes adatbázisokban a processzor és a memória kihasználtsága a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) és a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) nézetekben, a-ben `avg_cpu_percent` és az `avg_memory_usage_percent` oszlopokban történik. Rugalmas készletek esetén a rendszer a készlet szintű erőforrás-felhasználást a [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetben jeleníti meg. A felhasználói munkaterhelés CPU-felhasználását a Azure Monitor metrikán keresztül is jelenteni kell a `cpu_percent` készlet szintjén található [önálló adatbázisok](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) és [rugalmas készletek](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) esetében.

Azure SQL Database számítási erőforrásokat igényel az olyan alapvető szolgáltatási funkciók megvalósításához, mint a magas rendelkezésre állás és a vész-helyreállítás, az adatbázis biztonsági mentése és visszaállítása, figyelés, lekérdezési tároló, automatikus hangolás stb. A rendszer az [erőforrás-irányítási](#resource-governance) mechanizmusok használatával helyezi el az ezen belső folyamatok általános erőforrásainak egy bizonyos korlátozott részét, így a fennmaradó erőforrásokat elérhetővé teszi a felhasználói munkaterhelések számára. Amikor a belső folyamatok nem használnak számítási erőforrásokat, a rendszer elérhetővé teszi azokat a felhasználói munkaterhelések számára.

A felhasználói munkaterhelések és a belső folyamatok teljes CPU-és memóriahasználat-kihasználtsága a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) és a [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) nézetekben, `avg_instance_cpu_percent` valamint az `avg_instance_memory_percent` oszlopokban. Ezeket az adatokat a `sqlserver_process_core_percent` és a `sqlserver_process_memory_percent` Azure monitor metrikái is jelentik, [önálló adatbázisok](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserversdatabases) és [rugalmas készletek](../../azure-monitor/essentials/metrics-supported.md#microsoftsqlserverselasticpools) esetén a készlet szintjén.

A felhasználók munkaterhelései és belső folyamatai részletesebben részletezik a legutóbbi erőforrás-felhasználást a [sys.dm_resource_governor_resource_pools_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) és a [sys.dm_resource_governor_workload_groups_history_ex](/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) nézetben. Az ezekben a nézetekben hivatkozott erőforrás-készletekkel és munkaterhelési csoportokkal kapcsolatos részletekért lásd: [erőforrás-szabályozás](#resource-governance). Ezek a nézetek a felhasználói munkaterhelések és a kapcsolódó erőforráskészlet-és munkaterhelés-csoportok adott belső folyamatai alapján jelentést készítenek az erőforrás-használatról.

A Teljesítményfigyelés és a hibaelhárítás kontextusában fontos figyelembe venni a felhasználói **CPU-fogyasztást** ( `avg_cpu_percent` , `cpu_percent` ) és a felhasználói munkaterhelések és a belső folyamatok (,) **teljes CPU-felhasználását** is `avg_instance_cpu_percent` `sqlserver_process_core_percent` .

A **felhasználói CPU-felhasználás** kiszámítása a felhasználói munkaterhelés korlátainak százalékában történik az egyes szolgáltatási célkitűzésekben. Az 100%-os **felhasználói CPU-kihasználtság** azt jelzi, hogy a felhasználói munkaterhelés elérte a szolgáltatási cél korlátját. Ha azonban a **teljes CPU-felhasználás** eléri a 70-100%-os tartományt, megtekintheti a felhasználói munkaterhelés átviteli sebességét, és a lekérdezés késése növekszik, még akkor is, ha a jelentett **felhasználói CPU-felhasználás** jelentős mértékben a 100% alá esik. Ez nagyobb valószínűséggel fordul elő, ha kisebb szolgáltatási célkitűzéseket használ a számítási erőforrások mérsékelt kiosztásával, de viszonylag intenzívebb felhasználói munkaterhelésekkel, például [sűrű rugalmas készletekben](elastic-pool-resource-management.md). Ez kisebb szolgáltatási célkitűzések esetén is előfordulhatnak, ha a belső folyamatok átmenetileg további erőforrásokat igényelnek, például amikor új replikát hoz létre az adatbázisból.

Ha a **teljes CPU** -használat magas, a kockázatcsökkentő lehetőségek ugyanazok, mint a korábban feljegyzett, és tartalmazzák a szolgáltatási cél növelését és/vagy a felhasználói munkaterhelés optimalizálását.

## <a name="resource-governance"></a>Erőforrások szabályozása

Az erőforrás-korlátok kikényszeríthető, Azure SQL Database a SQL Server [Resource Governoron](/sql/relational-databases/resource-governor/resource-governor)alapuló erőforrás-irányítási implementációt használ, amely a Azure SQL Database futtatására lett módosítva és kiterjesztve. SQL Database, több [erőforráskészlet](/sql/relational-databases/resource-governor/resource-governor-resource-pool) és munkaterhelés- [csoport](/sql/relational-databases/resource-governor/resource-governor-workload-group), valamint a készlet és a csoportszint szintjén beállított erőforrás-korlátok biztosítják a [kiegyensúlyozott adatbázis-szolgáltatást](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). A felhasználói munkaterhelés és a belső munkaterhelések külön erőforrás-készletbe és munkaterhelési csoportba vannak besorolva. Az elsődleges és olvasható másodlagos replikák (beleértve a földrajzi replikákat is) felhasználói munkaterhelése az `SloSharedPool1` erőforráskészlet és a `UserPrimaryGroup.DBId[N]` munkaterhelés csoportba van besorolva, ahol `N` az adatbázis-azonosító érték áll rendelkezésre. Emellett több erőforráskészlet és munkaterhelés-csoport is található a különböző belső munkaterhelésekhez.

Amellett, hogy a Resource Governor használatával szabályozza az erőforrásokat az SQL-folyamaton belül, Azure SQL Database a Windows- [feladatok objektumait](/windows/win32/procthread/job-objects) is használja a feldolgozási szintű erőforrás-szabályozáshoz, valamint a Windows [Fájlkiszolgálói erőforrás-kezelőt (FSRM)](/windows-server/storage/fsrm/fsrm-overview) a tárolási kvóta kezeléséhez.

A Azure SQL Database erőforrás-szabályozás hierarchikus jellegű. Fentről lefelé a korlátokat az operációs rendszer erőforrás-irányítási mechanizmusai és Resource Governor, az erőforrás-készlet szintjén, a Resource Governor, majd a munkaterhelés csoport szintjén, a Resource Governor használatával kényszeríti ki. Az aktuális adatbázisra vagy rugalmas készletre vonatkozó erőforrás-irányítási korlátok a [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) nézetben vannak felszínben.

### <a name="data-io-governance"></a>Adatio-irányítás

Az adatio-szabályozás egy olyan folyamat, Azure SQL Database az olvasási és írási fizikai IO-t az adatbázisok adatfájljaira korlátozza. Az egyes IOPS korlátokat úgy állítjuk be, hogy a lehető legkisebbre csökkentsék a "zajos szomszéd" hatást, hogy az erőforrás-elosztás méltányos legyen a több-bérlős szolgáltatásban, és a mögöttes hardver és tárterület képességein belül maradjon.

Az önálló adatbázisok esetében a munkaterhelési csoportra vonatkozó korlátokat a rendszer az adatbázisra vonatkozó összes összes tárterületre alkalmazza, az erőforrás-készlet korlátozásai pedig az azonos dedikált SQL-készlet összes adatbázisára érvényesek, az `tempdb` adatbázissal együtt. A rugalmas készletek esetében a munkaterhelési csoport korlátai a készlet minden adatbázisára érvényesek, míg az erőforráskészlet-korlát a teljes rugalmas készletre vonatkozik, beleértve az adatbázist is, `tempdb` amely a készletben lévő összes adatbázis között meg van osztva. Az erőforrás-készlet korlátai általában nem valósíthatók meg egy adatbázison (akár egyetlen, akár készletezett) keresztül, mert a munkaterhelés-csoport korlátai alacsonyabbak az erőforráskészlet korlátainál, és hamarabb korlátozzák a IOPS/átviteli sebességet. A készletre vonatkozó korlátokat azonban a kombinált munkaterhelés is elérheti ugyanazon a készleten lévő több adatbázisra vonatkozóan.

Ha például egy lekérdezés az i/o-erőforrás szabályozása nélkül 1000 IOPS hoz létre, de a munkaterhelési csoport maximális IOPS értéke 900 IOPS, a lekérdezés nem fog tudni több mint 900 IOPS-t létrehozni. Ha azonban az erőforráskészlet maximális IOPS-korlátja 1500 IOPS, és az erőforráskészlet összes munkaterhelési csoportjának teljes IO-értéke meghaladja az 1500-as IOPS, akkor az azonos lekérdezés IO-értéke a 900 IOPS munkacsoport-korlátja alatt csökkenhet.

A [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) nézet által visszaadott minimális/maximális értékek IOPS és átviteli sebessége korlátoknak/sapkának, nem pedig garanciának. Az erőforrás-szabályozás továbbá nem garantálja az adott tárolási késést. Egy adott felhasználói munkaterhelések esetében az elérhető legjobb késés, IOPS és teljesítmény nem csupán az IO-erőforrásokra vonatkozó irányítási korlátokra, hanem a felhasznált IO-méretek és a mögöttes tároló képességeire is érvényes. A SQL Database a 512 KB és 4 MB közötti méretekben eltérő IOs-t használ. A IOPS-korlátok betartatása érdekében minden i/o-érték a mérettől függetlenül fiókba kerül, az Azure Storage-ban tárolt adatfájlokkal rendelkező adatbázisok kivételével. Ebben az esetben a 256 KB-nál nagyobb IOs-nél több 256-KB-os IOs-ként van ellátva, hogy illeszkedjen az Azure Storage IO-nyilvántartásához.

Az Azure Storage-ban adatfájlokat használó alapszintű, standard és általános célú adatbázisok esetében az `primary_group_max_io` érték nem érhető el, ha egy adatbázis nem rendelkezik elegendő adatfájllal, hogy összesítse a IOPS, vagy ha az adatok nem egyenletesen vannak elosztva a fájlok között, vagy ha az alapul szolgáló Blobok teljesítményi szintje korlátozza az erőforrás-irányítási korlátok alatti IOPS/átviteli sebességet. Hasonlóképpen, a gyakori tranzakciók által generált kis log IOs esetében az `primary_max_log_rate` érték nem valósítható meg az alapul szolgáló Azure Storage-blob IOPS korlátja miatt. Az Azure Premium Storaget használó adatbázisok esetén a Azure SQL Database megfelelően nagy tárolási blobokat használ a szükséges IOPS/átviteli sebesség eléréséhez, az adatbázis méretétől függetlenül. Nagyobb adatbázisok esetén a rendszer több adatfájlt hoz létre a teljes IOPS/átviteli kapacitás növelése érdekében.

A (z) és a (z) `avg_data_io_percent` `avg_log_write_percent` [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database),  [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)és [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetekben jelentett erőforrás-felhasználási értékeket a rendszer a maximális erőforrás-irányítási korlátok százalékában számítja ki. Ezért ha az erőforrás-szabályozástól eltérő tényezők IOPS/átviteli sebességre vannak korlátozva, megtekintheti a IOPS/átviteli sebesség simítását és a késések növelését a munkaterhelés növekedésével, bár a jelentett erőforrás-kihasználtság a 100% alatti marad.

Ha szeretné megtekinteni az IOPS, az átviteli sebesség és a késés olvasási és írási időpontját, használja a [sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) függvényt. Ez a függvény felfedi az összes i/o-adatbázist az adatbázison, beleértve a háttér i/o-t, amely nem része a felé `avg_data_io_percent` , de az alapul szolgáló TÁRTERÜLET IOPS és átviteli sebességét használja, és hatással lehet a megfigyel A függvény felfedi a további késéseket, amelyeket az i/o-erőforrások irányításával lehet bevezetni az olvasási és írási műveletekhez a `io_stall_queued_read_ms` és az `io_stall_queued_write_ms` oszlopokban.

### <a name="transaction-log-rate-governance"></a>Tranzakciós naplók arányának szabályozása

A tranzakciós napló arányának szabályozása Azure SQL Database folyamat, amellyel korlátozható a nagy mennyiségű betöltési arány a számítási feladatok, például a tömeges Beszúrás, a kiválasztás és az index-buildek esetében. Ezeket a korlátokat nyomon követik és érvényesítik a második szinten a naplózási rekordok generálásának arányában, az átviteli sebesség korlátozásával függetlenül attól, hogy hány IOs-t lehet kiadni az adatfájlokban.  A tranzakciónapló-generálási díjak jelenleg lineárisan méretezhetők egy hardvertől függő pontra, és a maximálisan megengedett naplózási sebesség 96 MB/s a virtuális mag beszerzési modellel.

> [!NOTE]
> A tényleges fizikai IOs – tranzakciós naplófájlok nem szabályozottak vagy korlátozottak.

A naplózási díjszabás úgy van beállítva, hogy különböző forgatókönyvekben legyenek elérhetők és fenntarthatók, míg a teljes rendszer a felhasználói terhelésnek való lehető legkisebbre csökkentheti a funkcionalitását. A naplózási sebesség szabályozása biztosítja, hogy a tranzakciónapló biztonsági mentései a közzétett helyreállító SLA-ban maradjanak.  Ez a szabályozás a másodlagos replikák túlzott lemaradását is megakadályozza.

A naplóbejegyzések létrehozásakor a rendszer minden egyes műveletet kiértékel és értékel ki, hogy késleltetve legyen-e a maximálisan szükséges naplózási sebesség (MB/s/másodperc) fenntartása érdekében. A rendszer nem adja hozzá a késéseket a naplófájlok tárolóba való kiürítéséhez, hanem a naplózási ráta irányítását a naplózási arány létrehozásakor.

A tényleges log-generálási sebességet a futtatási időszakban is befolyásolhatja a visszajelzési mechanizmusok, ami átmenetileg csökkenti az engedélyezett naplók sebességét, így a rendszer képes stabilizálni. A naplófájlok kezelése, amelyekkel elkerülhető, hogy a naplózási terület feltételeit és a rendelkezésre állási csoport replikálási mechanizmusa átmenetileg csökkentse a teljes rendszerkorlátot.

A naplózási arány kormányzója Traffic Shaping a következő várakozási típusoknál (amelyek a [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) és [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) nézetekben vannak kitéve):

| Várakozás típusa | Jegyzetek |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Adatbázis korlátozása |
| POOL_LOG_RATE_GOVERNOR | Készlet korlátozása |
| INSTANCE_LOG_RATE_GOVERNOR | Példány szintjének korlátozása |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Visszajelzés-vezérlés, rendelkezésre állási csoport fizikai replikálása prémium/üzletileg kritikus nem kell tartani |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Visszajelzés-vezérlés, a díjszabás korlátozása, hogy elkerülje a naplón kívüli hely feltételeit |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Geo-replikálási visszajelzések szabályozása, a naplózási sebesség korlátozása, hogy elkerülje a nagy adatkésést és a Geo-formátumú másodlagos zónák nem rendelkezésre állását|
|||

Ha egy, a kívánt skálázhatóságot akadályozó naplózási sebességre vonatkozó korlátot tapasztal, vegye figyelembe a következő lehetőségeket:

- Akár magasabb szolgáltatási szintet is igénybe veheti, hogy megszerezze a maximális 96 MB/s naplózási sebességet, vagy váltson át egy másik szolgáltatási szintre. A [nagy kapacitású](service-tier-hyperscale.md) szolgáltatási szint 100 MB/s naplózási sebességet biztosít a választott szolgáltatási szinttől függetlenül.
- Ha a betöltés alatt álló adatmennyiség átmeneti, például egy ETL-folyamatban lévő átmeneti adatmennyiség, akkor a tempdb-be (amely minimálisan naplózva van).
- Elemzési forgatókönyvek esetén helyezzen betöltést egy fürtözött oszlopcentrikus-táblázatba. Ez csökkenti a szükséges naplózási sebességet a tömörítés miatt. Ezzel a technikával növelheti a CPU-kihasználtságot, és csak olyan adatkészletekre alkalmazható, amelyek kihasználják a fürtözött oszlopcentrikus indexeket.

### <a name="storage-space-governance"></a>Tárolóhelyek szabályozása

A prémium és üzletileg kritikus szolgáltatási szinteken a rendszer az adatés tranzakciós naplófájlokat az adatbázist vagy rugalmas készletet üzemeltető gép helyi SSD-kötetén tárolja. Ez magas IOPS és átviteli sebességet, valamint alacsony IO-késést biztosít. A helyi kötet mérete a hardver képességeitől függ, és véges. Egy adott gépen a helyi kötetet az ügyfél-adatbázisok használják, például `tempdb` az operációs rendszer, a felügyeleti szoftver, a figyelési adat, a naplók stb. Az adatbázisok létrehozása, törlése, valamint a lemezterület-használat növelése/csökkentése, a gép helyi fogyasztása idővel ingadozik. 

Ha a rendszer észleli, hogy a gépen rendelkezésre álló szabad terület alacsony, és egy adatbázis vagy rugalmas készlet kifogyott a területtől, akkor az adatbázist vagy a rugalmas készletet egy másik, elegendő szabad hellyel rendelkező gépre helyezi át, amely lehetővé teszi, hogy a beállított szolgáltatási cél maximális méretére korlátozza a növekedést. Ez az áthelyezés online módon történik, hasonlóan egy adatbázis-skálázási művelethez is, és hasonló [hatással](single-database-scale.md#impact)van a művelet végén található rövid (másodperces) feladatátvételre. Ez a feladatátvétel megszakítja a nyitott kapcsolatokat, és Visszagörgeti a tranzakciókat, ami potenciálisan hatással lehet az alkalmazásokra az adatbázis használatával.

Mivel az adatmennyiséget fizikailag egy másik gépre másolják, a nagyobb adatbázisok áthelyezése jelentős időt igényelhet. Ebben az időszakban, ha egy nagyméretű felhasználói adatbázis vagy rugalmas készlet, vagy az `tempdb` adatbázis nagy mértékben növekszik, akkor a lemezterület kifogyása növekszik. A rendszer kiegyensúlyozott módon kezdeményezi az adatbázis-áthelyezést, hogy megakadályozza a felesleges hibákat, és elkerülje a szükségtelen feladatátvételeket.

## <a name="next-steps"></a>Következő lépések

- Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- További információ a DTU és a Edtu: [DTU és edtu](purchasing-models.md#dtu-based-purchasing-model).
- További információ a tempdb méretéről: [tempdb Azure SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).