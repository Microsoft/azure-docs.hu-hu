---
title: PowerShell-szkript az Azure Cosmos SQL API-adatbázis és-tároló erőforrás-zárolásának létrehozásához
description: Erőforrás-zárolás létrehozása az Azure Cosmos SQL API-adatbázis és-tároló számára
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: d893abc2abacda45dabcdd8529a2ace7c5056399
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674844"
---
# <a name="create-a-resource-lock-for-azure-cosmos-sql-api-database-and-container-using-azure-powershell"></a>Erőforrás-zárolás létrehozása az Azure Cosmos SQL API-adatbázishoz és-tárolóhoz Azure PowerShell használatával
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Ehhez a mintához Azure PowerShell az 5.4.0 vagy újabb verzió szükséges. `Get-Module -ListAvailable Az`A Futtatás gombra kattintva megtekintheti, hogy mely verziók vannak telepítve.
Ha telepítenie kell a-t, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört.

Futtassa a [AzAccount-](/powershell/module/az.accounts/connect-azaccount) t az Azure-ba való bejelentkezéshez.

> [!IMPORTANT]
> Az erőforrás-zárolások nem működnek a felhasználók által a Cosmos DB SDK-val összekapcsolt módosítások, a fiókok kulcsain keresztül csatlakozó eszközök vagy az Azure Portal használatával, kivéve, ha a Cosmos DB fiókot a rendszer először zárolja az `disableKeyBasedMetadataWriteAccess` engedélyezett tulajdonsággal. Ha többet szeretne megtudni a tulajdonság engedélyezéséről, tekintse meg az SDK-k [módosításainak megelőzését](../../../role-based-access-control.md#prevent-sdk-changes)ismertető témakört.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-lock.ps1 "Create, list, and remove resource locks")]

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