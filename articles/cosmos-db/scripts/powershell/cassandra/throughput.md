---
title: PowerShell-parancsfájlok átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Cassandra API erőforrásokhoz
description: PowerShell-parancsfájlok átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Cassandra API erőforrásokhoz
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 8bf49445990596471a6526274c2aa86843d4868a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488265"
---
# <a name="throughput-rus-operations-with-powershell-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Átviteli sebesség (RU/s) műveletek a PowerShell-lel a Azure Cosmos DB-Cassandra API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="get-throughput"></a>Átviteli sebesség lekérdezése

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-get.ps1 "Get throughput on a keyspace or table for Cassandra API")]

## <a name="update-throughput"></a>Frissítési sebesség

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-update.ps1 "Update throughput on a keyspace or table for Cassandra API")]

## <a name="migrate-throughput"></a>Átviteli sebesség áttelepíteni

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a keyspace or table for Cassandra API")]

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
| [Get-AzCosmosDBCassandraKeyspaceThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbcassandrakeyspacethroughput) | Beolvassa a Cassandra APIi terület átviteli sebességét. |
| [Get-AzCosmosDBCassandraTableThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbcassandratablethroughput) | Beolvassa a Cassandra API tábla átviteli értékét. |
| [Frissítés – AzCosmosDBCassandraKeyspaceThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbcassandrakeyspacethroughput) | Frissíti a Cassandra APIi lemezterület átviteli értékét. |
| [Frissítés – AzCosmosDBCassandraTableThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbcassandratablethroughput) | Frissíti a Cassandra API tábla átviteli értékét. |
| [Meghívás – AzCosmosDBCassandraKeyspaceThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbcassandrakeyspacethroughputmigration) | Átviteli sebesség átmigrálása egy Cassandra API-alapterületre. |
| [Meghívás – AzCosmosDBCassandraTableThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbcassandratablethroughputmigration) | Cassandra API-tábla átviteli sebességének átmigrálása. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).