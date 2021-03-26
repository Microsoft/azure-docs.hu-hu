---
title: 'REST API: szinkronizálás több adatbázis között'
description: Használjon REST API példa parancsfájlt több adatbázis közötti szinkronizáláshoz.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: d3ff8114c11b224a0bdbb0bd2d0e5686a7e57b55
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565905"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>Több adatbázis közötti adatszinkronizálás REST API használata 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez a REST API például úgy konfigurálja a SQL-adatszinkronizálás, hogy több adatbázis között szinkronizálja az adatszinkronizálást.

A SQL-adatszinkronizálás áttekintését lásd: az [adatszinkronizálás több Felhőbeli és helyszíni adatbázis között az Azure-ban SQL-adatszinkronizálás](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> A SQL-adatszinkronizálás jelenleg nem támogatja az Azure SQL felügyelt példányát.

## <a name="create-sync-group"></a>Szinkronizálási csoport létrehozása

Szinkronizálási csoport létrehozásához használja a [create vagy a Update](/rest/api/sql/syncgroups/createorupdate) sablont.
 
A szinkronizálási csoport létrehozásakor ne adja át a szinkronizálási sémát (table\column), és ne adja át a masterSyncMemberName, mert ebben az időben a szinkronizálási csoportban még nincs table\column információ.

A szinkronizálási csoport létrehozásához szükséges mintavételi kérelem: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Példa a szinkronizálási csoport létrehozására:

Állapotkód: 200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

Állapotkód: 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>Szinkronizálási tag létrehozása

Szinkronizált tag létrehozásához használja a [create vagy a Update](/rest/api/sql/syncmembers/createorupdate) sablont.

A szinkronizálási tag létrehozásához szükséges mintavételi kérelem:

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
Példa a szinkronizálási tag létrehozására:

Állapotkód: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Állapotkód: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>Séma frissítése

A szinkronizálási csoport sikeres létrehozása után frissítse a sémát a következő sablonok használatával.

A hub-adatbázis sémájának frissítéséhez használja a [központi séma frissítése](/rest/api/sql/syncgroups/refreshhubschema)  sablont. 

Példa központi adatbázis-séma frissítésére: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

Példa a hub adatbázis-sémájának frissítésére: 

Állapotkód: 200

Állapotkód: 202

A hub-adatbázis sémájának listázásához használja a [List hub-sémák](/rest/api/sql/syncgroups/listhubschemas) sablont. 

A tag adatbázis-sémájának frissítéséhez használja a [tag frissítése](/rest/api/sql/syncmembers/refreshmemberschema) sablont. 

A tagsági [séma](/rest/api/sql/syncmembers/listmemberschemas) sablonjának használata a tag adatbázis-sémájának listázásához. 

A séma sikeres frissítése után csak folytassa a következő lépéssel. 

## <a name="update-sync-group"></a>Szinkronizálási csoport frissítése 

A [create vagy a Update](/rest/api/sql/syncgroups/createorupdate) sablon használatával frissítse a szinkronizálási csoportot.

Szinkronizálási csoport frissítése a szinkronizálási séma megadásával. Adja meg a séma-és masterSyncMemberName, amely a használni kívánt sémát tartalmazó név. 

Példa a szinkronizálási csoport frissítésére: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Válasz a szinkronizálási csoport frissítésére: 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>Szinkronizálási tag frissítése

A szinkronizálási tag frissítéséhez használja a [create vagy a Update](/rest/api/sql/syncmembers/createorupdate) sablont.

Példa szinkronizálási tag frissítésére: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

Válasz a szinkronizálási tag frissítésére: 

Állapotkód: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Állapotkód: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>Trigger szinkronizálása

Szinkronizálási művelet elindításához használja a [trigger Sync](/rest/api/sql/syncgroups/triggersync) sablont.

A szinkronizálási művelet elindítására szolgáló mintavételi kérelem: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

Példa a szinkronizálási művelet aktiválására: 

Állapotkód: 200

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről [az Azure PowerShell dokumentációjában](/powershell/azure/) talál további információt.

Az [Azure SQL Database PowerShell-szkriptekben](../powershell-script-content-guide.md) további SQL Database PowerShell-szkriptminták találhatók.

További információ a SQL-adatszinkronizálásról:

- Áttekintés – az [adatszinkronizálás több Felhőbeli és helyszíni adatbázison SQL-adatszinkronizálás az Azure-ban](../sql-data-sync-data-sql-server-sql-database.md)
- Adatszinkronizálás beállítása
    - Használja a Azure Portal- [oktatóanyag: SQL-adatszinkronizálás beállítása az adatAzure SQL Database és a SQL Server közötti szinkronizáláshoz](../sql-data-sync-sql-server-configure.md)
    - A PowerShell használata a [PowerShell használatával Azure SQL Database-és SQL Server-adatbázis közötti adatszinkronizáláshoz](sql-data-sync-sync-data-between-azure-onprem.md)
- Adatszinkronizálási ügynök – [adatszinkronizálási ügynök SQL-adatszinkronizálás az Azure-ban](../sql-data-sync-agent-overview.md)
- Ajánlott eljárások – [ajánlott eljárások az Azure](../sql-data-sync-best-practices.md) -beli SQL-adatszinkronizálás
- Figyelő – [SQL-adatszinkronizálás figyelése Azure monitor naplókkal](../monitor-tune-overview.md)
- Hibaelhárítás – [Az Azure-SQL-adatszinkronizálás kapcsolatos problémák elhárítása](../sql-data-sync-troubleshoot.md)
- A szinkronizálási séma frissítése
    - Transact-SQL – [a séma változásainak automatizálása a SQL-adatszinkronizálás az Azure-ban](../sql-data-sync-update-sync-schema.md)
    - A PowerShell használata [a szinkronizálási séma egy meglévő szinkronizálási csoportban való frissítéséhez a PowerShell használatával](update-sync-schema-in-sync-group.md)

További információ a SQL Databaseról:

- [SQL Database áttekintése](../sql-database-paas-overview.md)
- [Az adatbázis életciklusának felügyelete](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))