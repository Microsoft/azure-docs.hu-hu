---
title: Azure CLI-szkriptek átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Core (SQL) API-erőforrásokhoz
description: Azure CLI-szkriptek átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Core (SQL) API-erőforrásokhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 2df31e5903785b6e25ea79a107a53084849c66fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789086"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Átviteli sebességet (RU/s) használó műveletek az Azure CLI-hez egy adatbázishoz vagy tárolóhoz a Azure Cosmos DB Core (SQL) API-hoz
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.12.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Core (SQL) API-adatbázist megosztott átviteli sebességet és egy Core (SQL) API-tárolót dedikált átviteli sebességet, majd frissíti az adatbázis és a tároló átviteli sebességét. A szkript ezután migrál a standard értékről az automatikus skálázású átviteli sebességre, majd beolvassa az automatikus skálázás átviteli sebességének értékét a migrálását követően.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

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
| [az cosmosdb sql database create](/cli/azure/cosmosdb/sql/database#az_cosmosdb_sql_database_create) | Létrehoz egy Azure Cosmos Core- (SQL-) adatbázist. |
| [az cosmosdb sql container create](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create) | Létrehoz egy Azure Cosmos Core- (SQL-) tárolót. |
| [az cosmosdb sql database átviteli sebességének frissítése](/cli/azure/cosmosdb/sql/database/throughput#az_cosmosdb_sql_database_throughput_update) | Azure Cosmos Core- (SQL-) adatbázis átviteli sebességének frissítése. |
| [az cosmosdb sql container throughput update](/cli/azure/cosmosdb/sql/container/throughput#az_cosmosdb_sql_container_throughput_update) | Azure Cosmos Core- (SQL-) tároló átviteli sebességének frissítése. |
| [az cosmosdb sql database throughput migrate](/cli/azure/cosmosdb/sql/database/throughput#az_cosmosdb_sql_database_throughput_migrate) | Azure Cosmos Core- (SQL-) adatbázis átviteli sebességének áttelepítése. |
| [az cosmosdb sql container throughput migrate](/cli/azure/cosmosdb/sql/container/throughput#az_cosmosdb_sql_container_throughput_migrate) | Azure Cosmos Core- (SQL-) tároló átviteli sebességének áttelepítése. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

A cli-ről további Azure Cosmos DB a CLI [Azure Cosmos DB talál.](/cli/azure/cosmosdb)

Minden Azure Cosmos DB CLI-szkriptminta megtalálható a Azure Cosmos DB [CLI GitHub-adattárában.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
