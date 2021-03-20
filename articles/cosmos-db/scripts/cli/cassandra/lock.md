---
title: Erőforrás-zárolás létrehozása Cassandra-térközhez és a Azure Cosmos DB táblához
description: Erőforrás-zárolás létrehozása Cassandra-térközhez és a Azure Cosmos DB táblához
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 94ff29d2c3272b61ff09fa0e564fcf2fcd281130
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566044"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-cli"></a>Erőforrás-zárolás létrehozása az Azure Cosmos Cassandra API és a tábla Azure CLI-vel való használatával
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.9.1 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

> [!IMPORTANT]
> Az erőforrás-zárolások nem működnek a Cassandra SDK, a CQL Shell vagy az Azure Portal használatával csatlakozó felhasználók által végzett változtatások esetén, kivéve, ha a Cosmos DB fiók először zárolva van az `disableKeyBasedMetadataWriteAccess` engedélyezett tulajdonsággal. Ha többet szeretne megtudni a tulajdonság engedélyezéséről, tekintse meg az SDK-k [módosításainak megelőzését](../../../role-based-access-control.md#prevent-sdk-changes)ismertető témakört.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/lock.sh "Create a resource lock for an Azure Cosmos DB Cassandra API keyspace, and table.")]

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
