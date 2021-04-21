---
title: Erőforrás-zárolás létrehozása adatbázishoz és gyűjteményhez a MongoDB API-hoz a Azure Cosmos DB
description: Erőforrás-zárolás létrehozása adatbázishoz és gyűjteményhez a MongoDB API-hoz a Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: ee298b33736ae25b365cc54ee1bb5ec9f38bfb62
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763603"
---
# <a name="create-a-resource-lock-for-azure-cosmos-dbs-api-for-mongodb-using-azure-cli"></a>Erőforrás-zárolás létrehozása Azure Cosmos DB MongoDB API-hoz az Azure CLI használatával
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.9.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

> [!IMPORTANT]
> Az erőforrás-zárolások nem működnek a MongoDB SDK-val, MongoShell-lel, eszközökkel vagy az Azure Portallal csatlakozó felhasználók által végrehajtott módosítások esetén, kivéve, ha az Cosmos DB-fiók először zárolva van, és a tulajdonság engedélyezve `disableKeyBasedMetadataWriteAccess` van. A tulajdonság engedélyezésével kapcsolatos további információkért lásd: [Az SDK-k változásainak megakadályozása.](../../../role-based-access-control.md#prevent-sdk-changes)

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/lock.sh "Create a resource lock for an Azure Cosmos DB MongoDB API database and collection.")]

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
