---
title: Lemezterület-kezelés Azure SQL Database
description: Ez a lap leírja, hogyan kezelheti a tárhelyet a Azure SQL Database önálló és készletezett adatbázisaival, és Hogyan határozható meg, hogy miként lehet egy vagy több készletezett adatbázist összezsugorodni, valamint hogyan kell egy adatbázis-zsugorodó műveletet végrehajtani.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: jrasnick, sstein
ms.date: 12/22/2020
ms.openlocfilehash: 7bb754b892715adffc6ead99f3d866f9f9d8af9b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99096491"
---
# <a name="manage-file-space-for-databases-in-azure-sql-database"></a>Azure SQL Database-adatbázisok tárterületének kezelése
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk a Azure SQL Database adatbázisainak különböző típusú tárolóhelyeit ismerteti, valamint azokat a lépéseket, amelyek akkor hajthatók végre, ha a lefoglalt lemezterületet explicit módon kell kezelni.

> [!NOTE]
> Ez a cikk nem vonatkozik az Azure SQL felügyelt példányaira.

## <a name="overview"></a>Áttekintés

A Azure SQL Database esetében olyan számítási feladatok vannak, amelyekben az adatbázis alapjául szolgáló adatfájlok kiosztása nagyobb lehet, mint a felhasznált adatlapok mennyisége. Ez akkor fordulhat elő, amikor növekszik a használt terület, majd később adatok törlődnek. Ennek az az oka, hogy a lefoglalt tárterületet a rendszer nem állítja automatikusan vissza az Adattörléskor.

A következő esetekben szükség lehet a fájlterület használatának monitorozására és az adatfájlok zsugorítására:

- Ha lehetővé szeretné tenni egy rugalmas készletben található adatmennyiség növelését, ha az adatbázisokhoz lefoglalt fájlterület eléri a készlet maximális méretét.
- Ha lehetővé szeretné tenni egy önálló adatbázis vagy rugalmas készlet maximális méretének csökkentését.
- Ha lehetővé szeretné tenni egy önálló adatbázis vagy rugalmas készlet módosítását egy másik, kisebb maximális méretű szolgáltatási vagy teljesítményszintre.

### <a name="monitoring-file-space-usage"></a>A tárterület használatának figyelése

A Azure Portalban megjelenő legtöbb tárolóhely-metrika és a következő API-k csak a felhasznált adatlapok méretét mérik:

- Azure Resource Manager alapú mérőszámok API-k, beleértve a PowerShell [Get-metrikákat](/powershell/module/az.monitor/get-azmetric)
- T-SQL: [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)

A következő API-k ugyanakkor az adatbázisokhoz és rugalmas készletekhez lefoglalt terület méretét is mérik:

- T-SQL:  [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)
- T-SQL: [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)

### <a name="shrinking-data-files"></a>Az adatfájlok zsugorítása

A Azure SQL Database nem csökkenti automatikusan az adatfájlokat, hogy visszaigényelje a fel nem használt területet, mert az adatbázis teljesítményének lehetséges következményei vannak.  Az ügyfelek azonban az önkiszolgáló szolgáltatással is csökkenthetik az adatfájlokat az adott időpontban, a [használaton kívüli lefoglalt terület visszaigénylése](#reclaim-unused-allocated-space)című cikkben ismertetett lépéseket követve.

> [!NOTE]
> Az adatfájlokkal ellentétben a Azure SQL Database automatikusan csökkenti a naplófájlokat, mivel az adott művelet nem befolyásolja az adatbázis teljesítményét.

## <a name="understanding-types-of-storage-space-for-a-database"></a>Az adatbázis tárterület-típusainak ismertetése

A következő tárolóhelyek mennyiségének megismerése fontos az adatbázisok fájlméretének kezeléséhez.

|Adatbázis mennyisége|Definíció|Megjegyzések|
|---|---|---|
|**Felhasznált adatterület**|Az adatbázis-adatmennyiségek 8 KB-os lapokon való tárolására szolgáló tárterület.|Általánosságban elmondható, hogy a felhasznált terület növekedése (csökkenés) a lapkáknál (deletes). Bizonyos esetekben a felhasznált terület nem változik a lapkákon és a törléseken a műveletben érintett adatmennyiségtől és a töredezettségtől függően. Ha például egy sort töröl minden adatoldalról, nem feltétlenül csökkenti a felhasznált területet.|
|**Lefoglalt adatterület**|Az adatbázis-adattárolás számára elérhetővé tett formázott fájl mérete.|A lefoglalt terület mennyisége automatikusan növekszik, de a törlés után soha nem csökken. Ez a viselkedés biztosítja, hogy a jövőbeli lapkák gyorsabbak legyenek, mivel a tárhelyet nem kell formázni.|
|**Lefoglalt, de fel nem használt adatterület**|A lefoglalt adatterületek és a felhasznált adatterületek mennyisége közötti különbség.|Ez a mennyiség az adatbázis-adatfájlok zsugorodásával visszaigényelhető szabad terület maximális mennyiségét jelöli.|
|**Maximális adatméret**|Az adatbázis-adatmennyiség tárolására használható maximális tárterület.|A lefoglalt adatterület mennyisége nem növekedhet az adatmennyiség maximális méretén túl.|

Az alábbi ábra az adatbázis különböző típusai közötti kapcsolatot szemlélteti.

![tárolóhelyek típusai és kapcsolatai](./media/file-space-manage/storage-types.png)

## <a name="query-a-single-database-for-storage-space-information"></a>Egyetlen adatbázis lekérdezése a tárolóhelyekre vonatkozó információkhoz

A következő lekérdezések használhatók a tárolóhelyek mennyiségének meghatározására egyetlen adatbázis esetében.  

### <a name="database-data-space-used"></a>Az adatbázis által használt adatterület

Módosítsa a következő lekérdezést a felhasznált adatbázis-adatterületek mennyiségének visszaküldéséhez.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to master
-- Database data space used in MB
SELECT TOP 1 storage_in_megabytes AS DatabaseDataSpaceUsedInMB
FROM sys.resource_stats
WHERE database_name = 'db1'
ORDER BY end_time DESC;
```

### <a name="database-data-space-allocated-and-unused-allocated-space"></a>Az adatbázis-adatterület lefoglalt és nem használt lefoglalt terület

A következő lekérdezéssel visszaállíthatja a lefoglalt adatbázis-adatterületek mennyiségét, valamint a lefoglalt fel nem használt terület mennyiségét.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to database
-- Database data space allocated in MB and database data space allocated unused in MB
SELECT SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB,
SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB
FROM sys.database_files
GROUP BY type_desc
HAVING type_desc = 'ROWS';
```

### <a name="database-data-max-size"></a>Adatbázis-adatmennyiség maximális mérete

Módosítsa a következő lekérdezést az adatbázis-adatmennyiség maximális méretének visszaküldéséhez.  A lekérdezés eredményének egységei bájtban vannak megadva.

```sql
-- Connect to database
-- Database data max size in bytes
SELECT DATABASEPROPERTYEX('db1', 'MaxSizeInBytes') AS DatabaseDataMaxSizeInBytes;
```

## <a name="understanding-types-of-storage-space-for-an-elastic-pool"></a>A rugalmas készlethez tartozó tárterület-típusok ismertetése

A következő tárolóhelyek mennyiségének megismerése fontos a rugalmas készlet tárterületének kezeléséhez.

|Rugalmas készlet mennyisége|Definíció|Megjegyzések|
|---|---|---|
|**Felhasznált adatterület**|A rugalmas készletben lévő összes adatbázis által használt adatterület összegzése.||
|**Lefoglalt adatterület**|A rugalmas készlet összes adatbázisa által lefoglalt adatterület összegzése.||
|**Lefoglalt, de fel nem használt adatterület**|A rugalmas készletben lévő összes adatbázis által lefoglalt adatterületek és adatterületek mennyisége közötti különbség.|Ez a mennyiség a rugalmas készlet számára lefoglalt maximális tárterületet jelöli, amelyet az adatbázis-adatfájlok zsugorodásával lehet visszaigényelni.|
|**Maximális adatméret**|A rugalmas készlet által az összes adatbázisához felhasználható adatterület maximális mennyisége.|A rugalmas készlethez lefoglalt terület mérete nem haladhatja meg a rugalmas készlet maximális méretét.  Ha ez az állapot előfordul, akkor a felhasználatlan helyet a rendszer az adatbázis-adatfájlok zsugorodása után visszaigényelheti.|

> [!NOTE]
> A "a rugalmas készlet elérte a tárolási korlátot" hibaüzenet azt jelzi, hogy az adatbázis-objektumok elég helyet foglalnak el a rugalmas készlet tárolási korlátjának kielégítéséhez, de az adatterület-lefoglalásban nem használható terület. Vegye fontolóra a rugalmas készlet tárolási korlátjának növelését, vagy rövid távú megoldásként szabadítson fel adatterületet az alábbi, nem [**használt lefoglalt terület visszaigénylése**](#reclaim-unused-allocated-space) szakasz használatával. Érdemes figyelembe vennie az adatbázisfájlok zsugorodásának lehetséges negatív teljesítményre gyakorolt hatását is, lásd alább az [**indexek újraépítése**](#rebuild-indexes) szakaszt.

## <a name="query-an-elastic-pool-for-storage-space-information"></a>Rugalmas készlet lekérdezése a tárolóhelyekkel kapcsolatos információkhoz

Az alábbi lekérdezések segítségével meghatározható a rugalmas készlethez kapcsolódó tárterület mennyisége.  

### <a name="elastic-pool-data-space-used"></a>Rugalmas készlethez felhasznált adatterület

Módosítsa a következő lekérdezést, hogy visszaállítsa a felhasznált rugalmas készlet adatterületének mennyiségét.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to master
-- Elastic pool data space used in MB  
SELECT TOP 1 avg_storage_percent / 100.0 * elastic_pool_storage_limit_mb AS ElasticPoolDataSpaceUsedInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

### <a name="elastic-pool-data-space-allocated-and-unused-allocated-space"></a>A rugalmas készlethez lefoglalt és fel nem használt lemezterület

Módosítsa az alábbi példákat egy olyan tábla visszaküldéséhez, amely a rugalmas készletben lévő minden adatbázishoz lefoglalt területet és fel nem használt területet tartalmaz. A táblázat ezeket az adatbázisokat a legkevesebb felhasználatlan lefoglalt terület közül a legnagyobb mennyiségű, nem felhasznált lefoglalt terület közül rendeli.  A lekérdezés eredményének egységei MB-ban vannak.  

A rendszer a készletben lévő egyes adatbázisok számára lefoglalt terület meghatározására szolgáló lekérdezés eredményeit hozzáadhatja a rugalmas készlethez lefoglalt teljes terület meghatározásához. A lefoglalt rugalmas készlet területe nem haladhatja meg a rugalmas készlet maximális méretét.  

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor. Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

A PowerShell-parancsfájlhoz SQL Server PowerShell-modulra van szükség – lásd: a telepítéshez szükséges [PowerShell-modul letöltése](/sql/powershell/download-sql-server-ps-module) .

```powershell
$resourceGroupName = "<resourceGroupName>"
$serverName = "<serverName>"
$poolName = "<poolName>"
$userName = "<userName>"
$password = "<password>"

# get list of databases in elastic pool
$databasesInPool = Get-AzSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName `
    -ServerName $serverName -ElasticPoolName $poolName
$databaseStorageMetrics = @()

# for each database in the elastic pool, get space allocated in MB and space allocated unused in MB
foreach ($database in $databasesInPool) {
    $sqlCommand = "SELECT DB_NAME() as DatabaseName, `
    SUM(size/128.0) AS DatabaseDataSpaceAllocatedInMB, `
    SUM(size/128.0 - CAST(FILEPROPERTY(name, 'SpaceUsed') AS int)/128.0) AS DatabaseDataSpaceAllocatedUnusedInMB `
    FROM sys.database_files `
    GROUP BY type_desc `
    HAVING type_desc = 'ROWS'"
    $serverFqdn = "tcp:" + $serverName + ".database.windows.net,1433"
    $databaseStorageMetrics = $databaseStorageMetrics + 
        (Invoke-Sqlcmd -ServerInstance $serverFqdn -Database $database.DatabaseName `
            -Username $userName -Password $password -Query $sqlCommand)
}

# display databases in descending order of space allocated unused
Write-Output "`n" "ElasticPoolName: $poolName"
Write-Output $databaseStorageMetrics | Sort -Property DatabaseDataSpaceAllocatedUnusedInMB -Descending | Format-Table
```

A következő képernyőkép a parancsfájl kimenetét szemlélteti:

![a rugalmas készlet lefoglalt területe és a fel nem használt szabad terület – példa](./media/file-space-manage/elastic-pool-allocated-unused.png)

### <a name="elastic-pool-data-max-size"></a>Rugalmas készletre vonatkozó adatmennyiség maximális mérete

Módosítsa az alábbi T-SQL-lekérdezést, hogy a legutóbb rögzített rugalmas készlet adatai maximális méretét adják vissza.  A lekérdezés eredményének egységei MB-ban vannak.

```sql
-- Connect to master
-- Elastic pools max size in MB
SELECT TOP 1 elastic_pool_storage_limit_mb AS ElasticPoolMaxSizeInMB
FROM sys.elastic_pool_resource_stats
WHERE elastic_pool_name = 'ep1'
ORDER BY end_time DESC;
```

## <a name="reclaim-unused-allocated-space"></a>Nem használt lefoglalt terület visszaigénylése

> [!NOTE]
> Ha csökkenti a parancsokat, a futás közben hatással van az adatbázis teljesítményére, és ha lehetséges, alacsony használati időszakok alatt kell futnia.

### <a name="dbcc-shrink"></a>DBCC zsugorodása

Miután azonosította az adatbázisokat a fel nem használt lefoglalt terület visszaigényléséhez, módosítsa az adatbázis nevét az alábbi parancsban az egyes adatbázisok adatfájljainak összezsugorodása érdekében.

```sql
-- Shrink database data space allocated.
DBCC SHRINKDATABASE (N'db1');
```

Ha csökkenti a parancsokat, a futás közben hatással van az adatbázis teljesítményére, és ha lehetséges, alacsony használati időszakok alatt kell futnia.  

Érdemes figyelembe vennie az adatbázisfájlok zsugorodásának lehetséges negatív teljesítményre gyakorolt hatását is, lásd alább az [**indexek újraépítése**](#rebuild-indexes) szakaszt.

További információ erről a parancsról: [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).

### <a name="auto-shrink"></a>Automatikus zsugorodás

Másik lehetőségként az automatikus zsugorodás is engedélyezhető az adatbázishoz.  Az automatikus zsugorodás csökkenti a fájlkezelési bonyolultságot, és kevésbé befolyásolja az adatbázis teljesítményét, mint a `SHRINKDATABASE` vagy a `SHRINKFILE` .  Az automatikus zsugorodás különösen hasznos lehet a sok adatbázissal rendelkező rugalmas készletek kezeléséhez.  Az automatikus zsugorodás azonban kevésbé hatékony lehet a fájlméret visszaigénylése során `SHRINKDATABASE` `SHRINKFILE` .
Alapértelmezés szerint az automatikus zsugorodás le van tiltva a legtöbb adatbázishoz ajánlott módon. További információ: [AUTO_SHRINK szempontjai](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink).

Az automatikus zsugorodás engedélyezéséhez módosítsa az adatbázis nevét a következő parancsban.

```sql
-- Enable auto-shrink for the database.
ALTER DATABASE [db1] SET AUTO_SHRINK ON;
```

További információ erről a parancsról: [adatbázis-beállítási](/sql/t-sql/statements/alter-database-transact-sql-set-options) beállítások.

### <a name="rebuild-indexes"></a>Indexek újraépítése

Az adatbázis-adatfájlok összezsugorodása után az indexek töredezettek lehetnek, és elveszítik a teljesítmény optimalizálásának hatékonyságát. Ha a teljesítmény romlása történik, érdemes megfontolnia az adatbázis-indexek újjáépítését. Az indexek töredezettségével és újraépítésével kapcsolatos további információkért lásd: az [indexek újrarendezése és újraépítése](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).

## <a name="next-steps"></a>Következő lépések

- Az adatbázis maximális méretével kapcsolatos információkért lásd:
  - [Azure SQL Database virtuális mag-alapú beszerzési modell korlátai egyetlen adatbázishoz](resource-limits-vcore-single-databases.md)
  - [Erőforráskorlátok önálló adatbázisokhoz a DTU-alapú vásárlási modellel](resource-limits-dtu-single-databases.md)
  - [Azure SQL Database virtuális mag-alapú beszerzési modell korlátai rugalmas készletekhez](resource-limits-vcore-elastic-pools.md)
  - [A rugalmas készletek erőforrásokra vonatkozó korlátai a DTU-alapú vásárlási modell használatával](resource-limits-dtu-elastic-pools.md)
- További információ a `SHRINKDATABASE` parancsról: [SHRINKDATABASE](/sql/t-sql/database-console-commands/dbcc-shrinkdatabase-transact-sql).
- Az indexek töredezettségével és újraépítésével kapcsolatos további információkért lásd: az [indexek újrarendezése és újraépítése](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes).