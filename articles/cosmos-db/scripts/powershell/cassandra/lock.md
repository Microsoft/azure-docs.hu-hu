---
title: PowerShell-parancsfájl erőforrás-zárolás létrehozásához az Azure Cosmos Cassandra API a térköz és a tábla
description: Erőforrás-zárolás létrehozása az Azure Cosmos Cassandra API-tárhely és-tábla számára
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: f8e1e2172137c1c6a569fb169c59191ab11cedfe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684370"
---
# <a name="create-a-resource-lock-for-azure-cosmos-cassandra-api-keyspace-and-table-using-azure-powershell"></a>Erőforrás-zárolás létrehozása az Azure Cosmos Cassandra API a webtárhely és a tábla használatával Azure PowerShell
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Ehhez a mintához Azure PowerShell az 5.4.0 vagy újabb verzió szükséges. `Get-Module -ListAvailable Az`A Futtatás gombra kattintva megtekintheti, hogy mely verziók vannak telepítve.
Ha telepítenie kell a-t, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört.

Futtassa a [AzAccount-](/powershell/module/az.accounts/connect-azaccount) t az Azure-ba való bejelentkezéshez.

> [!IMPORTANT]
> Az erőforrás-zárolások nem működnek a Cassandra SDK, a CQL Shell vagy az Azure Portal használatával csatlakozó felhasználók által végzett változtatások esetén, kivéve, ha a Cosmos DB fiók először zárolva van az `disableKeyBasedMetadataWriteAccess` engedélyezett tulajdonsággal. Ha többet szeretne megtudni a tulajdonság engedélyezéséről, tekintse meg az SDK-k [módosításainak megelőzését](../../../role-based-access-control.md#prevent-sdk-changes)ismertető témakört.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
|**Azure-erőforrás**| |
| [Új – AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | Erőforrás-zárolást hoz létre. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | Lekérdezi egy erőforrás zárolását, vagy listázza az erőforrás-zárolásokat. |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | Eltávolít egy erőforrás-zárolást. |
|||

## <a name="next-steps"></a>Következő lépések

További információ a Azure PowerShellről: [Azure PowerShell dokumentáció](/powershell/).