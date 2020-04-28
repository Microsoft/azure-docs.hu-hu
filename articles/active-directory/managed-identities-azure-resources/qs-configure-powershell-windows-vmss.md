---
title: Felügyelt identitások konfigurálása virtuálisgép-méretezési csoportokon a PowerShell használatával – Azure AD
description: Részletes útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálásához egy virtuálisgép-méretezési csoporton a PowerShell használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 755aee312fd0492fd57a82cb7a437b04ebf72987
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74547269"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Felügyelt identitások konfigurálása az Azure-erőforrásokhoz a virtuálisgép-méretezési csoportokban a PowerShell használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai az Azure-szolgáltatásokat a Azure Active Directory automatikusan felügyelt identitással biztosítják. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban. 

Ebben a cikkben a PowerShell használatával megtudhatja, hogyan hajthatja végre a felügyelt identitásokat az Azure-erőforrások műveleteihez egy virtuálisgép-méretezési csoporton:
- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása egy virtuálisgép-méretezési csoporton
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása virtuálisgép-méretezési csoportokban

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által felügyelt hozzárendelt identitás közötti különbséget](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A cikkben szereplő felügyeleti műveletek végrehajtásához a fióknak a következő Azure-beli szerepköralapú hozzáférés-vezérlési hozzárendelésekre van szüksége:

    > [!NOTE]
    > Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

    - Virtuálisgép-méretezési csoport létrehozásához és a rendszer által hozzárendelt felügyelt és/vagy felhasználó által hozzárendelt felügyelt identitás engedélyezéséhez és eltávolításához egy [virtuálisgép-](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) méretezési csoportból.
    - [Felügyelt identitás közreműködői](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör felhasználó által hozzárendelt felügyelt identitás létrehozásához.
    - [Felügyelt identitás-kezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör a felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez és eltávolításához egy virtuálisgép-méretezési csoportba.
- Ha még nem tette meg [, telepítse a Azure PowerShell legújabb verzióját](/powershell/azure/install-az-ps) . 

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan engedélyezheti és távolíthatja el a rendszerhez rendelt felügyelt identitásokat Azure PowerShell használatával.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>A rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure virtuálisgép-méretezési csoport létrehozása során

Virtuálisgép-méretezési csoport létrehozása a rendszerhez rendelt felügyelt identitás engedélyezésével:

1. A rendszer által hozzárendelt felügyelt identitással rendelkező virtuálisgép-méretezési csoport létrehozásához tekintse meg az *1. példát* a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) parancsmag-dokumentációban.  Adja hozzá a `-IdentityType SystemAssigned` paramétert `New-AzVmssConfig` a parancsmaghoz:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```



## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Rendszerhez rendelt felügyelt identitás engedélyezése meglévő Azure virtuálisgép-méretezési csoportokban

Ha egy meglévő Azure virtuálisgép-méretezési csoporton kell engedélyeznie a rendszer által hozzárendelt felügyelt identitást:

1. Jelentkezzen be az Azure `Connect-AzAccount`-ba a használatával. Olyan fiókot használjon, amely a virtuálisgép-méretezési csoportját tartalmazó Azure-előfizetéshez van társítva. Győződjön meg arról is, hogy a fiókja olyan szerepkörhöz tartozik, amely írási engedélyeket ad a virtuálisgép-méretezési csoportnak, például a "virtuális gép közreműködői":

   ```powershell
   Connect-AzAccount
   ```

2. Először kérje le a virtuálisgép-méretezési csoport tulajdonságait [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) a parancsmag használatával. Ezután a rendszerhez rendelt felügyelt identitás engedélyezéséhez használja az `-IdentityType` [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag kapcsolóját:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```



### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>A rendszer által hozzárendelt felügyelt identitás letiltása egy Azure virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési csoporttal rendelkezik, amelynek már nincs szüksége a rendszer által hozzárendelt felügyelt identitásra, de továbbra is a felhasználó által hozzárendelt felügyelt identitásokra van szüksége, használja a következő parancsmagot:

1. Jelentkezzen be az Azure `Connect-AzAccount`-ba a használatával. Használjon olyan fiókot, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva. Győződjön meg arról is, hogy a fiókja olyan szerepkörhöz tartozik, amely írási engedélyeket ad a virtuálisgép-méretezési csoportnak, például a "virtuális gép közreműködői":

2. Futtassa a következő parancsmagot:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Ha olyan virtuálisgép-méretezési csoporttal rendelkezik, amelynek már nincs szüksége a rendszerhez rendelt felügyelt identitásra, és nincs felhasználó által hozzárendelt felügyelt identitása, használja a következő parancsokat:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebből a szakaszból megtudhatja, hogyan adhat hozzá és távolíthat el egy felhasználó által hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoportból Azure PowerShell használatával.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy Azure virtuálisgép-méretezési csoport létrehozása során

A felhasználó által hozzárendelt felügyelt identitással rendelkező új virtuálisgép-méretezési csoport létrehozása jelenleg nem támogatott a PowerShellen keresztül. Tekintse meg a következő szakaszt, amely bemutatja, hogyan adhat hozzá egy felhasználóhoz rendelt felügyelt identitást egy meglévő virtuálisgép-méretezési csoporthoz. Térjen vissza frissítésekért.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése meglévő Azure virtuálisgép-méretezési csoporthoz

Felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő Azure virtuálisgép-méretezési csoporthoz:

1. Jelentkezzen be az Azure `Connect-AzAccount`-ba a használatával. Olyan fiókot használjon, amely a virtuálisgép-méretezési csoportját tartalmazó Azure-előfizetéshez van társítva. Győződjön meg arról is, hogy a fiókja olyan szerepkörhöz tartozik, amely írási engedélyeket ad a virtuálisgép-méretezési csoportnak, például a "virtuális gép közreműködői":

   ```powershell
   Connect-AzAccount
   ```

2. Először kérje le a virtuálisgép-méretezési csoport tulajdonságait `Get-AzVM` a parancsmag használatával. Ezután rendeljen hozzá egy felhasználóhoz rendelt felügyelt identitást a virtuálisgép-méretezési csoporthoz, használja az `-IdentityType` and `-IdentityID` kapcsolót az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmaggal. `<SUBSCRIPTION ID>`Cserélje `<VM NAME>`le a `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>`, `USER ASSIGNED ID2` ,, értéket a saját értékeire.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy Azure virtuálisgép-méretezési csoportból

Ha a virtuálisgép-méretezési csoport több felhasználó által hozzárendelt felügyelt identitással rendelkezik, a következő parancsokkal távolíthatja el az összeset, de az utolsót is. Ne felejtse el a `<RESOURCE GROUP>` és `<VIRTUAL MACHINE SCALE SET NAME>` paraméterek értékeit a saját értékeire cserélni. A `<USER ASSIGNED IDENTITY NAME>` a felhasználó által hozzárendelt felügyelt identitás neve tulajdonság, amely a virtuálisgép-méretezési csoporton marad. Ezek az információk a virtuálisgép-méretezési csoport identitás szakaszában találhatók a következő használatával `az vmss show`:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Ha a virtuálisgép-méretezési csoport nem rendelkezik rendszerhez rendelt felügyelt identitással, és el szeretné távolítani az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Ha a virtuálisgép-méretezési csoporthoz a rendszerhez hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás is tartozik, a felhasználó által hozzárendelt összes felügyelt identitást eltávolíthatja úgy, hogy csak a rendszer által hozzárendelt felügyelt identitás használatára váltson.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások az Azure-erőforrásokhoz – áttekintés](overview.md)
- A teljes körű Azure-beli virtuális gépek létrehozásához a következő témakörben talál további információt:
  
  - [Windows rendszerű virtuális gép létrehozása PowerShell használatával](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux rendszerű virtuális gép létrehozása PowerShell segítségével](../../virtual-machines/linux/quick-create-powershell.md) 

















