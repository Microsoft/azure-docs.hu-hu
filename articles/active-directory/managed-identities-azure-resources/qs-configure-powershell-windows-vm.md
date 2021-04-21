---
title: Felügyelt identitások konfigurálása Azure-beli virtuális gépen a PowerShell használatával – Azure AD
description: Részletes útmutató azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának konfigurálásához a PowerShell használatával.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/19/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1eca17106067d964ba2f280d358b2973b41459a6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783268"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának konfigurálása a PowerShell használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitása automatikusan felügyelt identitást biztosít az Azure-szolgáltatásoknak a Azure Active Directory. Ezzel az identitással bármely Azure AD-hitelesítést támogató szolgáltatásban hitelesíthet anélkül, hogy hitelesítő adatokat ad meg a kódban.

Ebből a cikkből megtudhatja, hogyan végezheti el a következő felügyelt identitásokkal kapcsolatos műveleteket az Azure-beli virtuális gépeken a PowerShell használatával.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, tekintse meg az [áttekintési szakaszt.](overview.md) Mindenképpen tekintse át a rendszer által hozzárendelt és a felhasználó által **[hozzárendelt felügyelt identitások közötti különbséget.](overview.md#managed-identity-types)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A példaszk szkriptek futtatásához két lehetőség áll rendelkezésre:
    - Használja [a Azure Cloud Shell,](../../cloud-shell/overview.md)amelyet a kódblokkok jobb felső sarkában található **Try It (Próbálja** ki) gombbal nyithat meg.
    - Futtatassa helyileg a szkripteket az Azure PowerShell legújabb verziójának [telepítésével,](/powershell/azure/install-az-ps)majd jelentkezzen be az Azure-ba a `Connect-AzAccount` használatával. 

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitást a Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése Azure-beli virtuális gép létrehozása során

Ahhoz, hogy olyan Azure-beli virtuális gépet hozzon létre, amely számára engedélyezve van a rendszer által hozzárendelt felügyelt identitás, a fiókjának a Virtuális gépek közreműködője [szerepkör-hozzárendelésre](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) van szüksége.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Tekintse meg az azure-beli virtuális gépek alábbi rövid útmutatóit, és csak a szükséges szakaszokat (Bejelentkezés az Azure-ba, "Erőforráscsoport létrehozása", "Hálózatcsoport létrehozása", "Virtuális gép létrehozása").

    Amikor a "Virtuális gép létrehozása" szakaszra jut, kis mértékben módosítsa a [New-AzVMConfig](/powershell/module/az.compute/new-azvm) parancsmag szintaxisát. Mindenképpen adjon hozzá egy paramétert, amely a rendszer által hozzárendelt identitással rendelkező virtuális gépet `-IdentityType SystemAssigned` létesíti, például:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName myVM -IdentityType SystemAssigned ...
    ```

   - [Windows rendszerű virtuális gépek létrehozása a PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md)
   - [Linux rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md)

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése meglévő Azure-beli virtuális gépen

Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitást olyan virtuális gépen engedélyezze, amely eredetileg nem lett kiépítve, a fiókjának rendelkeznie kell a [Virtuális](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) gépek közreműködője szerepkör-hozzárendeléssel.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. A virtuális gép tulajdonságainak lekérése a `Get-AzVM` parancsmaggal. Ezután a rendszer által hozzárendelt felügyelt identitás engedélyezéséhez használja a kapcsolót `-IdentityType` az [Update-AzVM](/powershell/module/az.compute/update-azvm) parancsmagon:

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType SystemAssigned
   ```

### <a name="add-vm-system-assigned-identity-to-a-group"></a>VM-rendszer által hozzárendelt identitás hozzáadása csoporthoz

Miután engedélyezte a rendszer által hozzárendelt identitást egy virtuális gépen, hozzáadhatja azt egy csoporthoz.  Az alábbi eljárás egy VM rendszer által hozzárendelt identitását hozzáadja egy csoporthoz.

1. Olvassa be és jegyezze fel a virtuális gép szolgáltatásnévének értékét (a visszaadott értékek `ObjectID` `Id` mezőjében megadottak szerint):

   ```azurepowershell-interactive
   Get-AzADServicePrincipal -displayname "myVM"
   ```

1. Olvassa be és jegyezze fel a csoport értékét (a visszaadott értékek `ObjectID` `Id` mezőjében megadottak szerint):

   ```azurepowershell-interactive
   Get-AzADGroup -searchstring "myGroup"
   ```

1. Adja hozzá a virtuális gép szolgáltatását a csoporthoz:

   ```azurepowershell-interactive
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Rendszer által hozzárendelt felügyelt identitás letiltása Azure-beli virtuális gépről

Ha le szeretné tiltani a rendszer által hozzárendelt felügyelt identitást egy virtuális gépen, a fiókjának szüksége van a Virtuális gépek közreműködője [szerepkör-hozzárendelésre.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

Ha olyan virtuális géppel van, amely már nem szükséges a rendszer által hozzárendelt felügyelt identitáshoz, de továbbra is felhasználó által hozzárendelt felügyelt identitásra van szüksége, használja a következő parancsmagot:

1. A parancsmaggal olvassa be a virtuális gép tulajdonságait, és `Get-AzVM` állítsa a `-IdentityType` paramétert a `UserAssigned` következőre:

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Ha olyan virtuális géppel rendelkezik, amely már nem rendelkezik rendszer által hozzárendelt felügyelt identitással, és nem rendelkezik felhasználó által hozzárendelt felügyelt identitásokkal, használja a következő parancsokat:

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználó által hozzárendelt felügyelt identitást egy virtuális gépről a Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy virtuális géphez a létrehozás során

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fiókjának a Virtuális gépek közreműködője és a [Felügyelt](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitáskezelő szerepkör-hozzárendelésre van szüksége. [](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Tekintse meg az azure-beli virtuális gépek alábbi rövid útmutatóit, és csak a szükséges szakaszokat (Bejelentkezés az Azure-ba, "Erőforráscsoport létrehozása", "Hálózatcsoport létrehozása", "Virtuális gép létrehozása").

    Amikor a "Virtuális gép létrehozása" szakaszhoz jut, kis mértékben módosítsa a [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) parancsmag szintaxisát. Adja hozzá a és paramétereket a virtuális gép felhasználó által `-IdentityType UserAssigned` `-IdentityID` hozzárendelt identitással való üzembe hozása érdekében.  Cserélje `<VM NAME>` le a , , és `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` értékeket a saját `<USER ASSIGNED IDENTITY NAME>` értékeire.  Például:

    ```azurepowershell-interactive
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```

    - [Windows rendszerű virtuális gépek létrehozása a PowerShell-lel](../../virtual-machines/windows/quick-create-powershell.md)
    - [Linux rendszerű virtuális gép létrehozása a PowerShell használatával](../../virtual-machines/linux/quick-create-powershell.md)


### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuális géphez

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fiókjának a Virtuális gépek közreműködője és a [Felügyelt](../../role-based-access-control/built-in-roles.md#managed-identity-operator) identitáskezelő szerepkör-hozzárendelésre van szüksége. [](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást a [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) parancsmaggal.  Jegyezze `Id` fel a kimenetben a et, mert a következő lépésben szüksége lesz rá.

   > [!IMPORTANT]
   > A felhasználó által hozzárendelt felügyelt identitások létrehozása csak alfanumerikus, aláhúzásjeles és kötőjeles (0–9, a-z, A-Z vagy \_ -) karaktereket támogat. Emellett a névnek 3–128 karakter hosszúságúnak kell lennie ahhoz, hogy a vm/VMSS-hez való hozzárendelés megfelelően működjön. További információ: Gyakori kérdések [és ismert problémák](known-issues.md)

   ```azurepowershell-interactive
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
1. A virtuális gép tulajdonságainak lekérése a `Get-AzVM` parancsmaggal. Ha ezután felhasználó által hozzárendelt felügyelt identitást szeretne hozzárendelni az Azure-beli virtuális géphez, használja az `-IdentityType` `-IdentityID` [Update-AzVM](/powershell/module/az.compute/update-azvm) parancsmagot, és kapcsolja be a kapcsolót.  A paraméter értéke `-IdentityId` az előző `Id` lépésben feljegyzett érték.  Cserélje `<VM NAME>` le a , , és `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` értékeket a saját `<USER ASSIGNED IDENTITY NAME>` értékeire.

   > [!WARNING]
   > A korábban a virtuális géphez hozzárendelt felhasználó által hozzárendelt felügyelt identitások megőrzéséhez le kellkérdezni a virtuálisgép-objektum `Identity` tulajdonságát `$vm.Identity` (például: ).  Ha a rendszer felhasználóhoz rendelt felügyelt identitásokat ad vissza, foglalja bele őket a következő parancsba a virtuális géphez hozzárendelni szeretne új felhasználó által hozzárendelt felügyelt identitással együtt.

   ```azurepowershell-interactive
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy Azure-beli virtuális gépről

Ha el szeretne távolítani egy felhasználó által hozzárendelt identitást egy virtuális gépről, a fiókjának a Virtuális gépek közreműködője [szerepkör-hozzárendelésre van](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szüksége.

Ha a virtuális gép több felhasználó által hozzárendelt felügyelt identitást is használ, az alábbi parancsokkal az utolsón kívül az összeset eltávolíthatja. Ne felejtse el a `<RESOURCE GROUP>` és `<VM NAME>` paraméterek értékeit a saját értékeire cserélni. A a felhasználó által hozzárendelt felügyelt identitás name (név) tulajdonsága, amelynek a virtuális gépen `<USER ASSIGNED IDENTITY NAME>` kell maradnia. Ez az információ a virtuálisgép-objektum `Identity` tulajdonságának lekérdezése alapján található meg.  Például: `$vm.Identity`

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Ha a virtuális gép nem rendelkezik rendszer által hozzárendelt felügyelt identitással, és el szeretné távolítani róla az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Ha a virtuális gép rendszer által hozzárendelt és felhasználó által hozzárendelt felügyelt identitásokkal is rendelkezik, az összes felhasználó által hozzárendelt felügyelt identitást eltávolíthatja úgy, hogy a rendszer által hozzárendelt felügyelt identitások használatára vált.

```azurepowershell-interactive
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure-erőforrások felügyelt identitásának áttekintése](overview.md)
- Az Azure-beli virtuális gépek létrehozásának teljes rövid útmutatóiért lásd:

  - [Windows rendszerű virtuális gép létrehozása PowerShell használatával](../../virtual-machines/windows/quick-create-powershell.md)
  - [Linux rendszerű virtuális gép létrehozása PowerShell segítségével](../../virtual-machines/linux/quick-create-powershell.md)
