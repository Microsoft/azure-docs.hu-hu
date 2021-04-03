---
title: 'PowerShell: SQL-adatszinkronizálás szinkronizálási séma frissítése'
description: Azure PowerShell-példaszkript az SQL Data Sync szinkronizálási sémájának frissítéséhez
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7d346d1ff30c138667749822b258bab4c6a621f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792718"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>PowerShell használata meglévő szinkronizálási csoport szinkronizálási sémájának frissítéséhez
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Ez a Azure PowerShell például egy meglévő SQL-adatszinkronizálás szinkronizálási csoportban frissíti a szinkronizálási sémát. Ha több táblát szinkronizál, ez a szkript segít Önnek a szinkronizálási séma hatékony frissítésében. Ez a példa a bemutatja az **UpdateSyncSchema** szkript használatát, amely [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1) néven érhető el a GitHubon.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, az oktatóanyaghoz az az PowerShell 1.4.0 vagy újabb verzió szükséges. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

Az SQL Data Sync áttekintéséhez tekintse meg a [több felhőalapú és helyszíni adatbázis közötti, az Azure SQL Data Sync segítségével végzett adatszinkronizálást](../sql-data-sync-data-sql-server-sql-database.md) ismertető cikket.

> [!IMPORTANT]
> A SQL-adatszinkronizálás jelenleg nem támogatja az Azure SQL felügyelt példányát.

## <a name="examples"></a>Példák

### <a name="add-all-tables-to-the-sync-schema"></a>Az összes tábla hozzáadása a szinkronizálási sémához

Az alábbi példa frissíti az adatbázissémát, és a központi adatbázis összes érvényes tábláját hozzáadja a szinkronizálási sémához.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Táblák és oszlopok hozzáadása és eltávolítása

Az alábbi példa hozzáadja a szinkronizálási sémához a `[dbo].[Table1]` és a `[dbo].[Table2].[Column1]` elemet, valamint eltávolítja a `[dbo].[Table3]` elemet.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Szkriptparaméterek

Az **UpdateSyncSchema** szkript a következő paramétereket tartalmazza:

| Paraméter | Jegyzetek |
|---|---|
| $subscriptionId | Az előfizetés, amelyben a szinkronizálási csoport létrejön. |
| $resourceGroupName | Az erőforráscsoport, amelyben a szinkronizálási csoport létrejön.|
| $serverName | A központi adatbázis kiszolgálóneve.|
| $databaseName | A központi adatbázis neve. |
| $syncGroupName | A szinkronizálási csoport neve. |
| $memberName | Akkor adja meg a tag nevét, ha a központi adatbázis helyett a szinkronizálási tagból kívánja betölteni az adatbázissémát. Ha a központi adatbázisból kívánja betölteni az adatbázissémát, akkor hagyja üresen ezt a paramétert. |
| $timeoutInSeconds | Az időtúllépési érték, amelyet elérve a szkript frissíti az adatbázissémát. Az alapértelmezett érték 900 másodperc. |
| $refreshDatabaseSchema | Meghatározza, hogy a szkriptnek frissítenie kell-e az adatbázissémát. Ha az adatbázis sémája módosult az előző konfigurációtól (például ha új táblát vagy újbóli oszlopot adott hozzá), akkor az újrakonfigurálás előtt frissítenie kell a sémát. Az alapértelmezett érték a false (hamis). |
| $addAllTables | Ha az érték true (igaz), akkor az összes érvényes tábla és oszlop hozzáadódik a szinkronizálási sémához. A $TablesAndColumnsToAdd és a $TablesAndColumnsToRemove értékeit a rendszer figyelmen kívül hagyja. |
| $tablesAndColumnsToAdd | Meghatározza a szinkronizálási sémához hozzáadandó táblákat vagy oszlopokat. Minden tábla- vagy oszlopnevet tagolni kell a séma nevével. Például: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Több tábla-vagy oszlopnév is megadható, és vesszővel (,) elválasztva. |
| $tablesAndColumnsToRemove | Meghatározza a szinkronizálási sémából eltávolítandó táblákat vagy oszlopokat. Minden tábla- vagy oszlopnevet tagolni kell a séma nevével. Például: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Több tábla-vagy oszlopnév is megadható, és vesszővel (,) elválasztva. |

## <a name="script-explanation"></a>Szkript ismertetése

Az **UpdateSyncSchema** szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzSqlSyncGroup](/powershell/module/az.sql/get-azsqlsyncgroup) | Egy szinkronizálási csoport adatait adja vissza. |
| [Frissítés – AzSqlSyncGroup](/powershell/module/az.sql/update-azsqlsyncgroup) | Frissít egy szinkronizálási csoportot. |
| [Get-AzSqlSyncMember](/powershell/module/az.sql/get-azsqlsyncmember) | Egy szinkronizálási taggal kapcsolatos adatokat ad vissza. |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/get-azsqlsyncschema) | Egy szinkronizálási sémával kapcsolatos információkat ad vissza. |
| [Frissítés – AzSqlSyncSchema](/powershell/module/az.sql/update-azsqlsyncschema) | Frissít egy szinkronizálási sémát. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről [az Azure PowerShell dokumentációjában](/powershell/azure/) talál további információt.

Az [Azure SQL Database PowerShell-szkriptekben](../powershell-script-content-guide.md) további SQL Database PowerShell-szkriptminták találhatók.

További információ a SQL-adatszinkronizálásról:

- Áttekintés – az [Azure SQL Database és a SQL Server közötti adatszinkronizálás az Azure-ban SQL-adatszinkronizálás](../sql-data-sync-data-sql-server-sql-database.md)
- Adatszinkronizálás beállítása
    - Használja a Azure Portal- [oktatóanyag: SQL-adatszinkronizálás beállítása az adatAzure SQL Database és a SQL Server közötti szinkronizáláshoz](../sql-data-sync-sql-server-configure.md)
    - A PowerShell használata
        -  [A PowerShell használatával szinkronizálhatja az adatAzure SQL Database több adatbázisa között](sql-data-sync-sync-data-between-sql-databases.md)
        -  [Az Azure SQL Database és SQL Server közötti adatszinkronizálás a PowerShell használatával](sql-data-sync-sync-data-between-azure-onprem.md)
- Adatszinkronizálási ügynök – [adatszinkronizálási ügynök SQL-adatszinkronizálás az Azure-ban](../sql-data-sync-agent-overview.md)
- Ajánlott eljárások – [ajánlott eljárások az Azure](../sql-data-sync-best-practices.md) -beli SQL-adatszinkronizálás
- Figyelő – [SQL-adatszinkronizálás figyelése Azure monitor naplókkal](../monitor-tune-overview.md)
- Hibaelhárítás – [Az Azure-SQL-adatszinkronizálás kapcsolatos problémák elhárítása](../sql-data-sync-troubleshoot.md)
- A szinkronizálási séma frissítése
    - Transact-SQL – [a séma változásainak automatizálása a SQL-adatszinkronizálás az Azure-ban](../sql-data-sync-update-sync-schema.md)

További információ a SQL Databaseról:

- [SQL Database áttekintése](../sql-database-paas-overview.md)
- [Az adatbázis életciklusának felügyelete](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))