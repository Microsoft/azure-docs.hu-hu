---
title: Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel indítása Azure Cosmos-fiókhoz
description: Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel indítása Azure Cosmos-fiókhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 0ae724f9900eea713af7d295eba26bf03cdf471a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94563239"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel indítása Azure Cosmos-fiókhoz az Azure CLI használatával
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.9.1 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="sample-script"></a>Példaszkript

Ez a szkript három műveletet mutat be.

- Régió hozzáadása egy meglévő Azure Cosmos-fiókhoz.
- Regionális feladatátvételi prioritás módosítása (az automatikus feladatátvételt használó fiókokra vonatkozik)
- Manuális feladatátvétel elindítása az elsődlegesről a másodlagos régiókba (a manuális feladatátvételsel rendelkező fiókokra vonatkozik)

> [!NOTE]
> Területi műveletek hozzáadása és eltávolítása Cosmos-fiókban nem végezhető el más tulajdonságok módosításakor.

> [!NOTE]
> Ez a példa egy SQL-(Core-) API-fiók használatát mutatja be, de ezek a műveletek megegyeznek az összes Cosmos DB adatbázis-API-ban.

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
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Létrehoz egy Azure Cosmos DB-fiókot. |
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Egy Azure Cosmos DB fiók frissítése (régió hozzáadása vagy eltávolítása). |
| [az cosmosdb feladatátvétel – prioritás – módosítás](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Frissítse a feladatátvételi prioritást, vagy indítsa el a feladatátvételt egy Azure Cosmos DB fiókon. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

A Azure Cosmos DB CLI-vel kapcsolatos további információkért lásd: [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.
