---
title: Fiókkulcsok és kapcsolati sztringek használata Azure Cosmos-fiókhoz
description: Fiókkulcsok és kapcsolati sztringek használata Azure Cosmos-fiókhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: bef0501704a3c9c400d998b6e84cf1cabb839dd1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770866"
---
# <a name="work-with-account-keys-and-connection-strings-for-an-azure-cosmos-account-using-azure-cli"></a>Fiókkulcsok és kapcsolati sztringek használata Azure Cosmos-fiókhoz az Azure CLI használatával
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.9.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript négy műveletet mutat be.

- Az összes fiókkulcs listába sorolva
- Csak olvasható fiókkulcsok listája
- Kapcsolati sztringek felsorolása
- Fiókkulcsok ismételt létrehozása

> [!NOTE]
> Ez a példa egy SQL (Core) API-fiók használatát mutatja be, de a fiókkulcs- és a kapcsolati sztringműveletek azonosak a szolgáltatás összes adatbázis-API-jában Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/keys.sh "Keys and connection string operations for Cosmos DB.")]

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
| [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) | List the keys for an Azure Cosmos DB account( List the keys for an Azure Cosmos DB account. |
| [az cosmosdb list-read-only-keys](/cli/azure/cosmosdb#az_cosmosdb_list_read_only_keys) | List the read only keys for an Azure Cosmos DB account .list the read only keys for an Azure Cosmos DB account. |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb#az_cosmosdb_list_connection_strings) | Listába sorolja fel egy Azure Cosmos DB kapcsolati sztringeket. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az_cosmosdb_regenerate-key) | Kulcsok újragenerálása egy Azure Cosmos DB fiókhoz. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure Cosmos DB CLI-ről további Azure Cosmos DB [CLI dokumentációjában talál.](/cli/azure/cosmosdb)

Minden Azure Cosmos DB CLI-szkriptminta megtalálható a Azure Cosmos DB [CLI GitHub-adattárában.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
