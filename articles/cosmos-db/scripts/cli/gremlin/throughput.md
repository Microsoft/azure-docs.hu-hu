---
title: Azure CLI-parancsfájlok átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Gremlin API-erőforrásokhoz
description: Azure CLI-parancsfájlok átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Gremlin API-erőforrásokhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 0aa05d165f83eec4bacb588ce974a18034918028
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94565568"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Átviteli sebesség (RU/s) műveletek az Azure CLI-vel egy adatbázishoz vagy gráfhoz Azure Cosmos DB-Gremlin API-hoz
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.12.1 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Gremlin-adatbázist közös átviteli sebességgel, valamint egy dedikált átviteli sebességgel rendelkező Gremlin gráfot, majd frissíti az adatátvitelt az adatbázis és a gráf esetében is. A szkript ezután áttelepíti a standard-ról az autoskálázási átviteli sebességre, majd beolvassa az átviteli sebesség értékét az áttelepítés után.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Throughput operations for a Gremlin database and graph.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Létrehoz egy Azure Cosmos DB-fiókot. |
| [az cosmosdb Gremlin Database Create](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | Létrehoz egy Azure Cosmos Gremlin-adatbázist. |
| [az cosmosdb Gremlin Graph Create](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Létrehoz egy Azure Cosmos Gremlin gráfot. |
| [az cosmosdb Gremlin Database átviteli sebességének frissítése](/cli/azure/cosmosdb/gremlin/database/throughput#az-cosmosdb-gremlin-database-throughput-update) | Az RU/s frissítése egy Azure Cosmos Gremlin-adatbázishoz. |
| [az cosmosdb Gremlin Graph átviteli sebességének frissítése](/cli/azure/cosmosdb/gremlin/graph/throughput#az-cosmosdb-gremlin-graph-throughput-update) | Az RU/s frissítése egy Azure Cosmos Gremlin gráfhoz. |
| [az cosmosdb Gremlin Database átviteli sebesség migrálása](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_migrate) | Átviteli sebesség áttelepíteni egy Azure Cosmos Gremlin-adatbázisra. |
| [az cosmosdb Gremlin Graph átviteli sebesség migrálása](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_migrate) | Átviteli sebesség áttelepíteni egy Azure Cosmos Gremlin gráfhoz. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

A Azure Cosmos DB CLI-vel kapcsolatos további információkért lásd: [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.
