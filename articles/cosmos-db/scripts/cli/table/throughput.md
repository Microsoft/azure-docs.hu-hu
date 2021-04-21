---
title: Azure CLI-szkriptek átviteli sebességi (RU/s) műveletekhez Azure Cosmos DB Table API erőforrásokhoz
description: Azure CLI-szkriptek átviteli sebességi (RU/s) műveletekhez Azure Cosmos DB Table API erőforrásokhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 12ee46316a3eadceedf6f1772ae41938d1cc903e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761922"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-table-for-azure-cosmos-db-table-api"></a>Átviteli sebesség (RU/s) műveletek az Azure CLI-hez egy táblához a Azure Cosmos DB Table API
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.12.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Table API, majd frissíti a tábla átviteli sebességét. A szkript ezután standardról automatikus skálázású átviteli sebességre migrál, majd beolvassa az automatikus skálázás átviteli sebességének értékét az áttelepítés után.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/throughput.sh "Throughput operations for Table API.")]

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
| [az cosmosdb table create](/cli/azure/cosmosdb/table#az_cosmosdb_table_create) | Létrehoz egy Azure Cosmos Table API táblát. |
| [az cosmosdb table throughput update](/cli/azure/cosmosdb/table/throughput#az_cosmosdb_table_throughput_update) | Azure Cosmos-tábla átviteli sebességének Table API frissítése. |
| [az cosmosdb table throughput migrate](/cli/azure/cosmosdb/table/throughput#az_cosmosdb_table_throughput_migrate) | Azure Cosmos-tábla átviteli sebességének Table API át. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure Cosmos DB CLI-ről további Azure Cosmos DB [CLI dokumentációjában talál.](/cli/azure/cosmosdb)

Minden Azure Cosmos DB CLI-szkriptminta megtalálható a Azure Cosmos DB [CLI GitHub-adattárában.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
