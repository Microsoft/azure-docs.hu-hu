---
title: Azure Cosmos-fiók létrehozása virtuális hálózati szolgáltatásvégpontokkal
description: Azure Cosmos-fiók létrehozása virtuális hálózati szolgáltatásvégpontokkal
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 6aa8da221818f807c29310f0b124b58ae70b853e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770727"
---
# <a name="create-an-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Azure Cosmos-fiók létrehozása virtuális hálózati szolgáltatásvégpontokkal az Azure CLI használatával
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.9.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a minta létrehoz egy új virtuális hálózatot egy előtér- és háttér-alhálózattal, és engedélyezi a szolgáltatásvégpontokat a `Microsoft.AzureCosmosDB` számára. Ezután lekéri az alhálózat erőforrás-azonosítóját, és alkalmazza az Azure Cosmos-fiókra, és engedélyezi a szolgáltatásvégpontokat a fiók számára.

> [!NOTE]
> Ez a példa egy Core (SQL) API-fiók használatát mutatja be. Ha ezt a mintát más API-khoz is használnia kell, alkalmazza az alábbi szkriptben található és paramétereket `enable-virtual-network` `virtual-network-rules` az API-specifikus szkriptre.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints.sh "Create an Azure Cosmos account with service endpoints.")]

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
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Létrehoz egy Azure-beli virtuális hálózatot. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) | Létrehoz egy alhálózatot egy Azure-beli virtuális hálózathoz. |
| [az network vnet subnet show](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_show) | Egy Azure-beli virtuális hálózat alhálózatát adja vissza. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Létrehoz egy Azure Cosmos DB-fiókot. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure Cosmos DB CLI-ről további Azure Cosmos DB [CLI dokumentációjában talál.](/cli/azure/cosmosdb)

Minden Azure Cosmos DB CLI-szkriptminta megtalálható a Azure Cosmos DB [CLI GitHub-adattárában.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
