---
title: Azure CLI-szkriptek a Gremlin API-erőforrások átviteli sebességi (RU/s) Azure Cosmos DB műveletekhez
description: Azure CLI-szkriptek a Gremlin API-erőforrások átviteli sebességi (RU/s) Azure Cosmos DB műveletekhez
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: ac8f915a1e9cc73c076879ea8e5ee487c3fe0c3f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770632"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Átviteli sebességet (RU/s) használó műveletek az Azure CLI-hez egy adatbázishoz vagy Azure Cosmos DB – Gremlin API
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.12.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Gremlin-adatbázist megosztott átviteli sebességet és egy Gremlin-gráfot dedikált átviteli sebességet, majd frissíti az adatbázis és a gráf átviteli sebességét. A szkript ezután migrál a standard értékről az automatikus skálázású átviteli sebességre, majd beolvassa az automatikus skálázás átviteli sebességének értékét a migrálását követően.

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
| [az group create](/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Létrehoz egy Azure Cosmos DB-fiókot. |
| [az cosmosdb gremlin database create](/cli/azure/cosmosdb/gremlin/database#az_cosmosdb_gremlin_database_create) | Létrehoz egy Azure Cosmos Gremlin-adatbázist. |
| [az cosmosdb gremlin graph create](/cli/azure/cosmosdb/gremlin/graph#az_cosmosdb_gremlin_graph_create) | Létrehoz egy Azure Cosmos Gremlin-gráfot. |
| [az cosmosdb gremlin adatbázis átviteli sebességének frissítése](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_update) | Azure Cosmos Gremlin-adatbázis RU/s-ének frissítése. |
| [az cosmosdb gremlin graph throughput update](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_update) | Azure Cosmos Gremlin-gráf ru/s értékének frissítése. |
| [az cosmosdb gremlin database throughput migrate](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_migrate) | Azure Cosmos Gremlin-adatbázis átviteli sebességének áttelepítése. |
| [az cosmosdb gremlin graph throughput migrate](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_migrate) | Azure Cosmos Gremlin-gráf átviteli sebességének áttelepítése. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

A cli-ről további Azure Cosmos DB a CLI [Azure Cosmos DB talál.](/cli/azure/cosmosdb)

Minden Azure Cosmos DB CLI-szkriptminta megtalálható a Azure Cosmos DB [CLI GitHub-adattárában.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
