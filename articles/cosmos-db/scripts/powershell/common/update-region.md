---
title: PowerShell-parancsfájl egy Azure Cosmos-fiók régiói frissítéséhez
description: Azure PowerShell parancsfájl-minta – Azure Cosmos-fiók régiói frissítése
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: febe03b7f2f34302ed57ceffe96191f3541abc73
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684892"
---
# <a name="update-an-azure-cosmos-accounts-regions-using-powershell"></a>Azure Cosmos-fiók régiói frissítése a PowerShell használatával
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Ehhez a mintához Azure PowerShell az 5.4.0 vagy újabb verzió szükséges. `Get-Module -ListAvailable Az`A Futtatás gombra kattintva megtekintheti, hogy mely verziók vannak telepítve.
Ha telepítenie kell a-t, olvassa el a [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps)című témakört.

Futtassa a [AzAccount-](/powershell/module/az.accounts/connect-azaccount) t az Azure-ba való bejelentkezéshez.

## <a name="sample-script"></a>Példaszkript

> [!NOTE]
> A régiók nem módosíthatók, és más Cosmos-fiókok tulajdonságai nem módosíthatók ugyanabban a műveletben. Ezeket két külön műveletnek kell elvégeznie.
> [!NOTE]
> Ez a példa egy SQL-(Core-) API-fiók használatát mutatja be. Ha más API-khoz szeretné használni ezt a mintát, másolja a kapcsolódó tulajdonságokat, és alkalmazza az API-specifikus parancsfájlra.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update-region.ps1 "Update Azure Cosmos account regions")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Felsorolja Cosmos DB fiókokat, vagy lekéri a megadott Cosmos DB fiókot. |
| [Új – AzCosmosDBLocationObject](/powershell/module/az.cosmosdb/new-azcosmosdblocationobject) | Egy PSLocation típusú objektumot hoz létre, amely paraméterként használható az Update-AzCosmosDBAccountRegion. |
| [Frissítés – AzCosmosDBAccountRegion](/powershell/module/az.cosmosdb/update-azcosmosdbaccountregion) | Cosmos DB fiók régióinak frissítése. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).