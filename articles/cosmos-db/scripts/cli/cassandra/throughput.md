---
title: Azure CLI-szkriptek átviteli sebességi (RU/s) műveletekhez Azure Cosmos DB Cassandra API erőforrásokhoz
description: Azure CLI-szkriptek átviteli sebességi (RU/s) műveletekhez Azure Cosmos DB Cassandra API erőforrásokhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 765ad939c8de9fb1b8c6b9c41cb2f7f685657d54
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770974"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Átviteli sebesség (RU/s) műveletek az Azure CLI-hez egy kulcstér vagy tábla Azure Cosmos DB – Cassandra API
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.12.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Cassandra-kulcsteret megosztott átviteli sebességet és egy Cassandra-táblát dedikált átviteli sebességet, majd frissíti az átviteli sebességet a kulcstérhez és a táblához is. A szkript ezután migrál a standard értékről az automatikus skálázású átviteli sebességre, majd beolvassa az automatikus skálázás átviteli sebességének értékét a migrálását követően.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Throughput operations for Cassandra keyspace and table.")]

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
| [az cosmosdb cassandra keyspace create](/cli/azure/cosmosdb/cassandra/keyspace#az_cosmosdb_cassandra_keyspace_create) | Létrehoz egy Azure Cosmos Cassandra-kulcsteret. |
| [az cosmosdb cassandra table create](/cli/azure/cosmosdb/cassandra/table#az_cosmosdb_cassandra_table_create) | Létrehoz egy Azure Cosmos Cassandra-táblát. |
| [az cosmosdb cassandra keyspace throughput update](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az_cosmosdb_cassandra_keyspace_throughput_update) | Azure Cosmos Cassandra-kulcstér ru/s-ének frissítése. |
| [az cosmosdb cassandra table throughput update](/cli/azure/cosmosdb/cassandra/table/throughput#az_cosmosdb_cassandra_table_throughput_update) | Azure Cosmos Cassandra-tábla RU/s-ének frissítése. |
| [az cosmosdb cassandra keyspace throughput migrate](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az_cosmosdb_cassandra_keyspace_throughput_migrate) | Azure Cosmos Cassandra-kulcstér átviteli sebességének áttelepítése. |
| [az cosmosdb cassandra table throughput migrate](/cli/azure/cosmosdb/cassandra/table/throughput#az_cosmosdb_cassandra_table_throughput_migrate) | Azure Cosmos Cassandra-tábla átviteli sebességének áttelepítése. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

A cli-ről további Azure Cosmos DB a CLI [Azure Cosmos DB talál.](/cli/azure/cosmosdb)

Minden Azure Cosmos DB CLI-szkriptminta megtalálható a Azure Cosmos DB [CLI GitHub-adattárában.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
