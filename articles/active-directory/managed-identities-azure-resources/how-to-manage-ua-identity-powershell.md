---
title: Felhasználó által hozzárendelt felügyelt identitás & és listába való törlése a Azure PowerShell – Azure AD
description: Részletes útmutató a felhasználó által hozzárendelt felügyelt identitások létrehozásához, listához és törléséhez a Azure PowerShell.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: af76affd9f4034401225e82de4e25e8b0a51125a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784838"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listába sorolás vagy törlése a Azure PowerShell

Az Azure-erőforrások felügyelt identitása felügyelt identitást biztosít az Azure-szolgáltatások számára a Azure Active Directory. Ezzel az identitással anélkül hitelesítheti az Azure AD-hitelesítést támogató szolgáltatásokat, hogy hitelesítő adatokat kellene megadnia a kódban. 

Ebből a cikkből megtudhatja, hogyan hozhat létre, listelhet és törölhet felhasználó által hozzárendelt felügyelt identitásokat a Azure PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, tekintse meg az [áttekintés szakaszt.](overview.md) Mindenképpen tekintse át a rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt **[identitások közötti különbséget.](overview.md#managed-identity-types)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A példaszk szkriptek futtatásához két lehetőség áll rendelkezésre:
    - Használja [a Azure Cloud Shell,](../../cloud-shell/overview.md)amelyet a kódblokkok jobb felső sarkában található **Try It (Próbálja** ki) gombbal nyithat meg.
    - Futtatassa helyileg a Azure PowerShell parancsprogramokat a következő szakaszban leírtak szerint.

### <a name="configure-azure-powershell-locally"></a>Helyi Azure PowerShell konfigurálása

Ha helyileg Azure PowerShell a cikkhez (a Cloud Shell használata helyett), kövesse az alábbi lépéseket:

1. Telepítse [a Azure PowerShell](/powershell/azure/install-az-ps) legújabb verzióját, ha még nem.

1. Jelentkezzen be az Azure-ba:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Telepítse a [PowerShellGet legújabb verzióját](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Előfordulhat, hogy ki kell lépnie az `Exit` aktuális PowerShell-munkamenetből, miután futtatta ezt a parancsot a következő lépéshez.

1. Telepítse a modul előzetes verzióját a cikkben a felhasználó által hozzárendelt felügyelt identitásokkal kapcsolatos `Az.ManagedServiceIdentity` műveletek végrehajtásához:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Felhasználó által hozzárendelt felügyelt identitás létrehozásához [](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) a fiókjának a Felügyelt identitás közreműködője szerepkör-hozzárendelésre van szüksége.

Felhasználó által hozzárendelt felügyelt identitás létrehozásához használja az `New-AzUserAssignedIdentity` parancsot. A `ResourceGroupName` paraméter megadja azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitást létre kell hozni, a paraméter pedig a nevét adja `-Name` meg. Cserélje le a `<RESOURCE GROUP>` és `<USER ASSIGNED IDENTITY NAME>` paraméterértékeket a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások felsorolása

Felhasználó által hozzárendelt felügyelt identitás listához/olvasásához a fiókjának a [Felügyelt](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitáskezelő vagy a Felügyelt identitás közreműködője szerepkör-hozzárendelésre [van](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szüksége.

A felhasználó által hozzárendelt felügyelt identitások listához használja a [Get-AzUserAssigned] parancsot.  A `-ResourceGroupName` paraméter határozza meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létre lett hozva. Cserélje le a `<RESOURCE GROUP>` értéket a saját értékére:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
A válaszban a felhasználó által hozzárendelt felügyelt identitások a kulcs értékét `"Microsoft.ManagedIdentity/userAssignedIdentities"` kapják `Type` vissza.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

Felhasználó által hozzárendelt felügyelt identitás törléséhez [](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) a fióknak a Felügyelt identitás közreműködője szerepkör-hozzárendelésre van szüksége.

Felhasználó által hozzárendelt felügyelt identitás törléséhez használja az `Remove-AzUserAssignedIdentity` parancsot.  A `-ResourceGroupName` paraméter megadja azt az erőforráscsoportot, ahol a felhasználó által hozzárendelt identitás létrejött, és a paraméter adja meg a `-Name` nevét. Cserélje le a `<RESOURCE GROUP>` és a paraméter értékét a saját `<USER ASSIGNED IDENTITY NAME>` értékeire:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> A felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a hivatkozást a hozzárendelt erőforrásokból. Az identitás-hozzárendeléseket külön kell eltávolítani.

## <a name="next-steps"></a>Következő lépések

Az Azure-erőforrások felügyelt identitásai Azure PowerShell részletes listájáért lásd: [Az.ManagedServiceIdentity.](/powershell/module/az.managedserviceidentity#managed_service_identity)
