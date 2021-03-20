---
title: PowerShell-parancsfájlok az átviteli sebesség (RU/s) műveleteihez Azure Cosmos DB SQL API-adatbázishoz vagy-tárolóhoz
description: PowerShell-parancsfájlok az átviteli sebesség (RU/s) műveleteihez Azure Cosmos DB SQL API-adatbázishoz vagy-tárolóhoz
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 73fdfa06fab42a5687e3aa354e3ba92937b92605
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684292"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Átviteli sebesség (RU/s) műveletek a PowerShell-lel a Azure Cosmos DB Core (SQL) API-hoz tartozó adatbázishoz vagy tárolóhoz
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Ehhez a mintához Azure PowerShell az 5.4.0 vagy újabb verzió szükséges. `Get-Module -ListAvailable Az`A Futtatás gombra kattintva megtekintheti, hogy mely verziók vannak telepítve.
Ha telepítenie kell a-t, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört.

Futtassa a [AzAccount-](/powershell/module/az.accounts/connect-azaccount) t az Azure-ba való bejelentkezéshez.

## <a name="get-throughput"></a>Átviteli sebesség lekérdezése

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-get.ps1 "Get throughput (RU/s) for Azure Cosmos DB Core (SQL) API database or container")]

## <a name="update-throughput"></a>Frissítési sebesség

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-update.ps1 "Update throughput (RU/s) for an Azure Cosmos DB Core (SQL) API database or container")]

## <a name="migrate-throughput"></a>Átviteli sebesség áttelepíteni

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-ru-migrate.ps1 "Migrate between standard and autoscale throughput on an Azure Cosmos DB Core (SQL) API database or container")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBSqlDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabasethroughput) | Az Azure Cosmos DB Core (SQL) API-adatbázison kiépített átviteli sebesség beolvasása. |
| [Get-AzCosmosDBSqlContainerThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainerthroughput) | Az Azure Cosmos DB Core (SQL) API-tárolón kiépített átviteli sebesség beolvasása. |
| [Frissítés – AzCosmosDBSqlDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Frissíti egy Azure Cosmos DB Core (SQL) API-adatbázis átviteli értékét. |
| [Frissítés – AzCosmosDBSqlContainerThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Egy Azure Cosmos DB Core (SQL) API-tároló átviteli értékét frissíti. |
| [Meghívás – AzCosmosDBSqlDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqldatabasethroughputmigration) | Egy Azure Cosmos DB Core (SQL) API-adatbázis átviteli sebességének áttelepítését. |
| [Meghívás – AzCosmosDBSqlContainerThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbsqlcontainerthroughputmigration) | Egy Azure Cosmos DB Core (SQL) API-tároló átviteli sebességének áttelepítését. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).