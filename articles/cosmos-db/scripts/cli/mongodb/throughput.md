---
title: Azure CLI-szkriptek a MongoDB-erőforrásokhoz Azure Cosmos DB API átviteli sebességi (RU/s) műveleteihez
description: Azure CLI-szkriptek a MongoDB-erőforrásokhoz Azure Cosmos DB API átviteli sebességi (RU/s) műveleteihez
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: fc477fdd842025722a2bcfcd0172ab9d57fb0d4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790710"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db-api-for-mongodb"></a>Átviteli sebesség (RU/s) műveletek az Azure CLI-hez a MongoDB-hez Azure Cosmos DB API-hoz
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.12.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy MongoDB-adatbázist megosztott átviteli sebességet és gyűjteményt dedikált átviteli sebességen, majd mindkét esetben frissíti az átviteli sebességet. A szkript ezután migrál a standard értékről az automatikus skálázású átviteli sebességre, majd beolvassa az automatikus skálázás átviteli sebességének értékét a migrálását követően.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Throughput operations for Azure Cosmos DB API for MongoDB.")]

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
| [az cosmosdb mongodb database create](/cli/azure/cosmosdb/mongodb/database#az_cosmosdb_mongodb_database_create) | Létrehoz egy Azure Cosmos MongoDB API-adatbázist. |
| [az cosmosdb mongodb collection create](/cli/azure/cosmosdb/mongodb/collection#az_cosmosdb_mongodb_collection_create) | Létrehoz egy Azure Cosmos MongoDB API-gyűjteményt. |
| [az cosmosdb mongodb adatbázis átviteli sebességének frissítése](/cli/azure/cosmosdb/mongodb/database/throughput#az_cosmosdb_mongodb_database_throughput_update) | Az Azure Cosmos MongoDB API-adatbázis ru ru-inak frissítése. |
| [az cosmosdb mongodb collection throughput update](/cli/azure/cosmosdb/mongodb/collection/throughput#az_cosmosdb_mongodb_collection_throughput_update) | Az Azure Cosmos MongoDB API-gyűjtemény ru ru-inak frissítése. |
| [az cosmosdb mongodb adatbázis átviteli sebességének áttelepítése](/cli/azure/cosmosdb/mongodb/database/throughput#az_cosmosdb_mongodb_database_throughput_migrate) | Adatbázis átviteli sebességének áttelepítése. |
| [az cosmosdb mongodb collection throughput migrate](/cli/azure/cosmosdb/mongodb/collection/throughput#az_cosmosdb_mongodb_collection_throughput-migrate) | Egy gyűjtemény átviteli sebességének áttelepítése. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

A cli-ről további Azure Cosmos DB a CLI [Azure Cosmos DB talál.](/cli/azure/cosmosdb)

Minden Azure Cosmos DB CLI-szkriptminta megtalálható a Azure Cosmos DB [CLI GitHub-adattárában.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
