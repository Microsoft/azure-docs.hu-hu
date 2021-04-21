---
title: Erőforrás-zárolás létrehozása egy Cassandra-kulcstérhez és -táblához a Azure Cosmos DB
description: Erőforrás-zárolás létrehozása egy Cassandra-kulcstérhez és -táblához a Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: b2c1e663c659935e7e820f30a281099058a4cf34
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771010"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-cli"></a>Erőforrás-zárolás létrehozása az Azure Cosmos Cassandra API-kulcstérhez és -táblához az Azure CLI használatával
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.9.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

> [!IMPORTANT]
> Az erőforrás-zárolások nem működnek a Cassandra SDK-val, CQL-rendszerhéjjal vagy az Azure Portallal csatlakozó felhasználók által végrehajtott módosítások esetén, kivéve, ha az Cosmos DB-fiók először zárolva van, és a tulajdonság engedélyezve `disableKeyBasedMetadataWriteAccess` van. A tulajdonság engedélyezésével kapcsolatos további információkért lásd: [Az SDK-k változásainak megakadályozása.](../../../role-based-access-control.md#prevent-sdk-changes)

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/lock.sh "Create a resource lock for an Azure Cosmos DB Cassandra API keyspace, and table.")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az lock create](/cli/azure/lock#az_lock_create) | Zárolást hoz létre. |
| [az lock list](/cli/azure/lock#az_lock_list) | List lock information. |
| [az lock show](/cli/azure/lock#az_lock_show) | Egy zárolás tulajdonságainak megjelenítése. |
| [az lock delete](/cli/azure/lock#az_lock_delete) | Töröl egy zárolást. |

## <a name="next-steps"></a>Következő lépések

- [Erőforrások zárolása a váratlan módosítások megelőzése érdekében](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB CLI dokumentációja.](/cli/azure/cosmosdb)

- [Azure Cosmos DB CLI GitHub-adattárban.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
