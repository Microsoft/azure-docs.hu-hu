---
title: Erőforrás-zárolás létrehozása adatbázishoz és gyűjteményhez a MongoDB API-hoz Azure Cosmos DB
description: Erőforrás-zárolás létrehozása adatbázishoz és gyűjteményhez a MongoDB API-hoz Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 2ea833b72b9522cdfca836a7b13f7b411402103e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94562695"
---
# <a name="create-a-resource-lock-for-azure-cosmos-dbs-api-for-mongodb-using-azure-cli"></a>Erőforrás-zárolás létrehozása Azure Cosmos DB API-MongoDB az Azure CLI használatával
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.9.1 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

> [!IMPORTANT]
> Az erőforrás-zárolások nem működnek a felhasználók által a MongoDB SDK, a Mongoshell, a bármely eszköz vagy az Azure Portal használatával összekapcsolt módosítások esetében, kivéve, ha a Cosmos DB fiókot az `disableKeyBasedMetadataWriteAccess` engedélyezett tulajdonsággal először zárolták. Ha többet szeretne megtudni a tulajdonság engedélyezéséről, tekintse meg az SDK-k [módosításainak megelőzését](../../../role-based-access-control.md#prevent-sdk-changes)ismertető témakört.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/lock.sh "Create a resource lock for an Azure Cosmos DB MongoDB API database and collection.")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az Lock Create](/cli/azure/lock#az-lock-create) | Zárolást hoz létre. |
| [az Lock List](/cli/azure/lock#az-lock-list) | Zárolási információk listázása. |
| [az Lock show](/cli/azure/lock#az-lock-show) | Zárolás tulajdonságainak megjelenítése. |
| [az Lock delete](/cli/azure/lock#az-lock-delete) | Törli a zárolást. |

## <a name="next-steps"></a>Következő lépések

- [Erőforrások zárolása a váratlan módosítások megelőzése érdekében](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

- [Azure Cosmos db CLI GitHub-tárház](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
