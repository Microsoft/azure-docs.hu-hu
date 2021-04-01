---
title: 'Oktatóanyag: egyéni Azure-szerepkör létrehozása Azure PowerShell – Azure RBAC'
description: Első lépésként hozzon létre egy Azure-beli egyéni szerepkört a Azure PowerShell és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával ebben az oktatóanyagban.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: 8b1815e7598410cd709572d93082d5dee5e0b0fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369242"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-powershell"></a>Oktatóanyag: egyéni Azure-szerepkör létrehozása Azure PowerShell használatával

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját egyéni szerepköröket is. Ebben az oktatóanyagban egy Reader Support Tickets (Olvasó – Támogatási jegyek) nevű egyéni szerepkört fog létrehozni az Azure PowerShell-lel. Az egyéni szerepkör lehetővé teszi a felhasználó számára, hogy megtekintse az előfizetés felügyeleti síkja összes adatát, valamint a támogatási jegyek megnyitását is.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Egyéni szerepkör létrehozása
> * Egyéni szerepkörök listázása
> * Egyéni szerepkörök frissítése
> * Egyéni szerepkörök törlése

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre van szükség:

- Egyéni szerepkörök létrehozására vonatkozó engedélyre, amely lehet például [Tulajdonos](built-in-roles.md#owner) vagy [Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>Bejelentkezés az Azure PowerShellbe

Jelentkezzen be [Azure PowerShellba](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása

Legegyszerűbben úgy hozhat létre egyéni szerepkört, ha egy beépített szerepkört szerkeszt át.

1. A PowerShellben használja a [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) parancsot a Microsoft. support erőforrás-szolgáltató műveleteinek listájának lekéréséhez. Érdemes megismerkedni az engedélyek létrehozására szolgáló műveletekkel. Az [Azure erőforrás-szolgáltató műveleteivel](resource-provider-operations.md#microsoftsupport)kapcsolatos összes művelet listáját is megtekintheti.

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot az [olvasói](built-in-roles.md#reader) szerepkör JSON formátumban való exportálásához.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Nyissa meg a **ReaderSupportRole.json** fájlt egy tetszőleges szövegszerkesztőben.

    Az alábbiakban a JSON kimenete látható. További információ a különböző tulajdonságokkal kapcsolatban: [Egyéni Azure-szerepkörök](custom-roles.md).

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. A JSON-fájl szerkesztésével adja hozzá a `"Microsoft.Support/*"` műveletet az `Actions` tulajdonsághoz. Ügyeljen arra, hogy az olvasási művelet után mindenképpen használjon vesszőt. Ez a művelet lehetővé teszi a felhasználónak a támogatási jegyek létrehozását.

1. Szerezze be az előfizetés AZONOSÍTÓját a [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) parancs használatával.

    ```azurepowershell
    Get-AzSubscription
    ```

1. Az `AssignableScopes` elemnél adja meg az előfizetés azonosítóját a következő formátumban: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    Explicit előfizetési azonosítókat kell megadnia, különben nem importálhatja a szerepkört az előfizetésébe.

1. Törölje az `Id` tulajdonságsort, és módosítsa az `IsCustom` tulajdonságot `true` értékre.

1. Módosítsa a `Name` és a `Description` tulajdonságot „Reader Support Tickets” és „View everything in the subscription and also open support tickets” (Mindent megtekinthet az előfizetésben, valamint támogatási jegyeket nyithat) értékre.

    A JSON-fájlnak a következőhöz kell hasonlítania:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Az új egyéni szerepkör létrehozásához használja a [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) parancsot, és határozza meg a JSON-szerepkör definícióját tartalmazó fájlt.

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Az új egyéni szerepkör mostantól elérhető az Azure Portalon, és a beépített szerepkörökhöz hasonlóan hozzárendelhető felhasználókhoz, csoportokhoz vagy szolgáltatásnevekhez.

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

- Az összes egyéni szerepkör listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot.

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    Az egyéni szerepköröket az Azure Portalon is megtekintheti.

    ![Képernyőkép az importált egyéni szerepkörről az Azure Portalon](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

Egyéni szerepkör frissítéséhez módosítsa a JSON-fájlt, vagy használja a `PSRoleDefinition` objektumot.

1. A JSON-fájl frissítéséhez használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot az egyéni szerepkör JSON formátumban való exportálásához.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Nyissa meg a fájlt egy tetszőleges szövegszerkesztőben.

1. Az `Actions` elemnél adja hozzá a műveletet az erőforráscsoportok `"Microsoft.Resources/deployments/*"` üzemelő példányainak létrehozásához és kezeléséhez.

    A frissített JSON-fájlnak a következőhöz kell hasonlítania:

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Az egyéni szerepkör frissítéséhez használja a [set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) parancsot, és adja meg a frissített JSON-fájlt.

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Ha az `PSRoleDefintion` objektum használatával szeretné frissíteni az egyéni szerepkört, először használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot a szerepkör beszerzéséhez.

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. Hívja meg az `Add` metódust, hogy hozzáadja a műveletet a diagnosztikai beállítások olvasásához.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. A szerepkör frissítéséhez használja a [set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) .

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Egyéni szerepkörök törlése

1. Használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot az egyéni szerepkör azonosítójának lekéréséhez.

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. Használja a [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) parancsot, és határozza meg a SZEREPKÖR-azonosítót az egyéni szerepkör törléséhez.

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Ha rendszer megerősítést kér, írja be a következőt: **Y**.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni Azure-szerepkörök létrehozása vagy frissítése Azure PowerShell használatával](custom-roles-powershell.md)
