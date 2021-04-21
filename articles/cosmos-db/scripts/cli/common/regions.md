---
title: Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel aktiválása Azure Cosmos-fiókhoz
description: Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel aktiválása Azure Cosmos-fiókhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 4a1a061945fe1c6c6a95eb62d286d40a158281ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770769"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel aktiválása Azure Cosmos-fiókhoz az Azure CLI használatával
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.9.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript három műveletet mutat be.

- Adjon hozzá egy régiót egy meglévő Azure Cosmos-fiókhoz.
- Regionális feladatátvételi prioritás módosítása (az automatikus feladatátvételt használó fiókokra vonatkozik)
- Manuális feladatátvétel aktiválása az elsődleges régiókból a másodlagos régiókba (a manuális feladatátvételsel rendelkező fiókokra vonatkozik)

> [!NOTE]
> A Cosmos-fiókon nem lehet régióműveleteket hozzáadni és eltávolítani más tulajdonságok módosítása közben.

> [!NOTE]
> Ez a példa egy SQL (Core) API-fiók használatát mutatja be, de ezek a műveletek megegyeznek a virtuális Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az_cosmosdb_update) | Frissíti a Azure Cosmos DB fiókot (régió hozzáadása vagy eltávolítása). |
| [az cosmosdb failover-priority-change](/cli/azure/cosmosdb#az_cosmosdb_failover_priority_change) | Frissítse a feladatátvételi prioritást, vagy aktiválja a feladatátvételt egy Azure Cosmos DB fiókon. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

Az Azure Cosmos DB CLI-ről további Azure Cosmos DB [CLI dokumentációjában talál.](/cli/azure/cosmosdb)

Minden Azure Cosmos DB CLI-szkriptminta megtalálható a Azure Cosmos DB [CLI GitHub-adattárában.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
