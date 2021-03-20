---
title: Azure CLI-parancsfájlok átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Table API erőforrásokhoz
description: Azure CLI-parancsfájlok átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Table API erőforrásokhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 6e76da71cb14122817090e64354babf5a618db8b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94562661"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-table-for-azure-cosmos-db-table-api"></a>Átviteli sebesség (RU/s) műveletek az Azure CLI-vel Azure Cosmos DB Table API
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.12.1 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Table API táblát, majd frissíti a tábla átviteli sebességét. A szkript ezután áttelepíti a standard-ról az autoskálázási átviteli sebességre, majd beolvassa az átviteli sebesség értékét az áttelepítés után.

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
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Létrehoz egy Azure Cosmos DB-fiókot. |
| [az cosmosdb Table Create](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | Létrehoz egy Azure Cosmos Table API táblát. |
| [az cosmosdb Table átviteli sebességének frissítése](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-update) | Azure Cosmos Table API-tábla frissítési sebességének frissítése. |
| [az cosmosdb Table átviteli sebesség migrálása](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-migrate) | Áttelepítheti az átviteli sebességet egy Azure Cosmos Table API-táblához. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

A Azure Cosmos DB CLI-vel kapcsolatos további információkért lásd: [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.
