---
title: Erőforrás-zárolás létrehozása egy Azure Cosmos DB Table API táblához
description: Erőforrás-zárolás létrehozása egy Azure Cosmos DB Table API táblához
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 04f8d541fc534a60550ba77d9775b340571a504f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788945"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Erőforrás-zárolás létrehozása egy Azure Cosmos DB Table API táblához az Azure CLI használatával
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.9.1-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

> [!IMPORTANT]
> Az erőforrás-zárolások nem működnek az Cosmos DB Table SDK-t, az Azure Storage Table SDK-t, a fiókkulcsokkal csatlakozó eszközöket vagy az Azure Portalt összekötő felhasználók által végrehajtott módosítások esetében, kivéve, ha az Cosmos DB-fiók először zárolva van, és a tulajdonság engedélyezve `disableKeyBasedMetadataWriteAccess` van. A tulajdonság engedélyezésével kapcsolatos további információkért lásd: [Az SDK-k](../../../role-based-access-control.md#prevent-sdk-changes)változásainak megakadályozása.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az lock create](/cli/azure/lock#az_lock_create) | Zárolást hoz létre. |
| [az lock list](/cli/azure/lock#az_lock_list) | Sorolja fel a zárolási adatokat. |
| [az lock show](/cli/azure/lock#az_lock_show) | Egy zárolás tulajdonságainak megjelenítése. |
| [az lock delete](/cli/azure/lock#az_lock_delete) | Töröl egy zárolást. |

## <a name="next-steps"></a>Következő lépések

- [Erőforrások zárolása a váratlan módosítások megelőzése érdekében](../../../../azure-resource-manager/management/lock-resources.md)

- [Azure Cosmos DB CLI dokumentációja.](/cli/azure/cosmosdb)

- [Azure Cosmos DB CLI GitHub-adattár .](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
