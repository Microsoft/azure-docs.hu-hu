---
title: Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása & Azure PowerShell-Azure AD használatával
description: Részletes útmutató a felhasználó által hozzárendelt felügyelt identitás Azure PowerShell használatával történő létrehozásához, listázásához és törléséhez.
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
ms.openlocfilehash: 636f40f0c425c25dfe7f41f1f404afc90ed5ba56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548222"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása vagy törlése Azure PowerShell használatával

Az Azure-erőforrások felügyelt identitásai biztosítják az Azure-szolgáltatásokat Azure Active Directory felügyelt identitással. Ezt az identitást hitelesítheti az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy hitelesítő adatokat kellene használnia a kódban. 

Ebből a cikkből megtudhatja, hogyan hozhat létre, listázhat és törölhet egy felhasználó által hozzárendelt felügyelt identitást Azure PowerShell használatával.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#managed-identity-types)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A példaként szolgáló szkriptek futtatásához két lehetőség közül választhat:
    - Használja a [Azure Cloud shellt](../../cloud-shell/overview.md), amelyet a kódrészletek jobb felső sarkában található **kipróbálás** gomb használatával nyithat meg.
    - A következő szakaszban leírtak szerint futtassa helyileg a parancsfájlokat Azure PowerShellokkal.

### <a name="configure-azure-powershell-locally"></a>Azure PowerShell helyi konfigurálása

Ha a cikkben Azure PowerShell helyileg szeretné használni (Cloud Shell helyett), hajtsa végre a következő lépéseket:

1. Ha még nem tette meg [, telepítse a Azure PowerShell legújabb verzióját](/powershell/azure/install-az-ps) .

1. Jelentkezzen be az Azure-ba:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Telepítse a [PowerShellGet legújabb verzióját](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Előfordulhat, hogy a `Exit` következő lépéshez a parancs futtatása után ki kell lépnie az aktuális PowerShell-munkamenetből.

1. Telepítse a modul előzetes verzióját a `Az.ManagedServiceIdentity` felhasználó által hozzárendelt felügyelt identitási műveletek elvégzéséhez ebben a cikkben:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör-hozzárendelésével.

Felhasználó által hozzárendelt felügyelt identitás létrehozásához használja az `New-AzUserAssignedIdentity` parancsot. A `ResourceGroupName` paraméter határozza meg azt az erőforráscsoportot, amelyben létre kell hozni a felhasználó által hozzárendelt felügyelt identitást, és a `-Name` paraméter megadja a nevét. Cserélje le a `<RESOURCE GROUP>` és a `<USER ASSIGNED IDENTITY NAME>` paraméter értékét a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitások listázása

Felhasználó által hozzárendelt felügyelt identitás listázásához/olvasásához a fióknak a [felügyelt identitás-kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) vagy a [felügyelt identitás közreműködői](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör-hozzárendelésre van szüksége.

A felhasználó által hozzárendelt felügyelt identitások listázásához használja a [Get-AzUserAssigned] parancsot.  A `-ResourceGroupName` paraméter határozza meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt felügyelt identitás létrejött. Cserélje le a `<RESOURCE GROUP>` értéket a saját értékére:

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
A válaszban a felhasználó által hozzárendelt felügyelt identitások `"Microsoft.ManagedIdentity/userAssignedIdentities"` értéke a kulcshoz lett visszaadva `Type` .

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás törlése

Felhasználó által hozzárendelt felügyelt identitás törléséhez a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör-hozzárendelésével.

Felhasználó által hozzárendelt felügyelt identitás törléséhez használja az `Remove-AzUserAssignedIdentity` parancsot.  A `-ResourceGroupName` paraméter határozza meg azt az erőforráscsoportot, amelyben a felhasználó által hozzárendelt identitás létrejött, és a `-Name` paraméter a nevét adja meg. Cserélje le a `<RESOURCE GROUP>` és a `<USER ASSIGNED IDENTITY NAME>` paramétereket értékeket a saját értékeire:

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> A felhasználó által hozzárendelt felügyelt identitás törlése nem távolítja el a hivatkozást, a hozzá rendelt összes erőforrásból. Az identitás-hozzárendeléseket külön kell eltávolítani.

## <a name="next-steps"></a>Következő lépések

A teljes listát és az Azure-erőforrások parancsainak Azure PowerShell felügyelt identitások részletes ismertetését az [az. ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)című részben tekintheti meg.
