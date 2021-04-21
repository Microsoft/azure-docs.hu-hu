---
title: Felügyelt identitások konfigurálása virtuálisgép-méretezési készleteken a PowerShell használatával – Azure AD
description: Részletes útmutató a rendszer és a felhasználó által hozzárendelt felügyelt identitások konfigurálásához egy virtuálisgép-méretezési csoportban a PowerShell használatával.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eb651df2fe3b9771154f9e188d41c660f7ffd028
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774138"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Azure-erőforrások felügyelt identitásának konfigurálása virtuálisgép-méretezési készleteken a PowerShell használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitása automatikusan felügyelt identitást biztosít az Azure-szolgáltatásoknak a Azure Active Directory. Ezzel az identitással bármely Azure AD-hitelesítést támogató szolgáltatásban hitelesíthet anélkül, hogy hitelesítő adatokat ad meg a kódban. 

Ebben a cikkben a PowerShell használatával megtudhatja, hogyan hajtható végre az Azure-erőforrások felügyelt identitásai műveletek egy virtuálisgép-méretezési készleten:

- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása virtuálisgép-méretezési csoporton
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása virtuálisgép-méretezési csoporton

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, tekintse meg az [áttekintési szakaszt.](overview.md) Mindenképpen tekintse át a rendszer által hozzárendelt és a felhasználó által felügyelt **[hozzárendelt identitások közötti különbséget.](overview.md#managed-identity-types)**

- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

- A cikkben említett felügyeleti műveletek végrehajtásához a fiókjának a következő Azure-beli szerepköralapú hozzáférés-vezérlési hozzárendelésre van szüksége:

    > [!NOTE]
    > Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

    - [Virtuális gépek közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) egy virtuálisgép-méretezési csoport létrehozásához, valamint a rendszer által hozzárendelt felügyelt és/vagy felhasználó által hozzárendelt felügyelt identitás engedélyezéséhez és eltávolításához egy virtuálisgép-méretezési csoportból.
    - [Felügyelt identitás közreműködője](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör felhasználó által hozzárendelt felügyelt identitás létrehozásához.
    - [Felügyelt identitáskezelői](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör a felhasználó által hozzárendelt felügyelt identitások virtuálisgép-méretezési csoportokhoz való hozzárendeléshez és eltávolításához.

- A példaszk szkriptek futtatásához két lehetőség áll rendelkezésre:
    - Használja [a Azure Cloud Shell,](../../cloud-shell/overview.md)amelyet a kódblokkok jobb felső sarkában található **Try It (Próbálja** ki) gombbal nyithat meg.
    - Futtatassa helyileg a szkripteket a Azure PowerShell [legújabb](/powershell/azure/install-az-ps)verziójának telepítésével, majd jelentkezzen be az Azure-ba a `Connect-AzAccount` használatával. 

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan engedélyezheti és távolíthatja el a rendszer által hozzárendelt felügyelt identitásokat a Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése Azure-beli virtuálisgép-méretezési csoport létrehozása során

Virtuálisgép-méretezési csoport létrehozása engedélyezett rendszer által hozzárendelt felügyelt identitással:

1. A Virtuálisgép-méretezési csoport rendszer által hozzárendelt felügyelt identitással való létrehozásához tekintse meg a [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig) parancsmagok referenciacikkének *1.* példáját.  Adja hozzá a `-IdentityType SystemAssigned` paramétert a `New-AzVmssConfig` parancsmaghoz:

    ```azurepowershell-interactive
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése meglévő Azure-beli virtuálisgép-méretezési csoporton

Ha engedélyeznie kell egy rendszer által hozzárendelt felügyelt identitást egy meglévő Azure-beli virtuálisgép-méretezési csoporton:

1. Győződjön meg arról, hogy a használt Azure-fiók olyan szerepkörhöz tartozik, amely írási engedélyeket biztosít a virtuálisgép-méretezési csoporton, például "Virtuális gépek közreműködője".
   
1. A virtuálisgép-méretezési készlet tulajdonságainak lekérése a [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) parancsmaggal. Ezután a rendszer által hozzárendelt felügyelt identitás engedélyezéséhez használja az `-IdentityType` [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag kapcsolót:

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>A rendszer által hozzárendelt felügyelt identitás letiltása azure-beli virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési csoportja van, amely már nem szükséges a rendszer által hozzárendelt felügyelt identitáshoz, de továbbra is felhasználó által hozzárendelt felügyelt identitásra van szüksége, használja a következő parancsmagot:

1. Győződjön meg arról, hogy a fiókja olyan szerepkörhöz tartozik, amely írási engedélyeket biztosít a virtuálisgép-méretezési csoporton, például "Virtuális gépek közreműködője".

1. Futtassa a következő parancsmagot:

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

1. Ha olyan virtuálisgép-méretezési csoportokkal rendelkezik, amelyekhez már nincs szükség rendszer által hozzárendelt felügyelt identitásra, és nem rendelkezik felhasználó által hozzárendelt felügyelt identitásokkal, használja a következő parancsot:

    ```azurepowershell-interactive
    Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
    ```
    
## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban megtudhatja, hogyan adhat hozzá és távolíthat el felhasználó által hozzárendelt felügyelt identitást egy virtuálisgép-méretezési csoportból a Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése Azure-beli virtuálisgép-méretezési csoport létrehozása során

Az új virtuálisgép-méretezési csoport felhasználó által hozzárendelt felügyelt identitással való létrehozása jelenleg nem támogatott a PowerShell használatával. Tekintse meg a felhasználó által hozzárendelt felügyelt identitás meglévő virtuálisgép-méretezési csoporthoz való hozzáadásának következő szakaszát. Térjen vissza frissítésekért.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése meglévő Azure-beli virtuálisgép-méretezési csoporthoz

Felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy meglévő Azure-beli virtuálisgép-méretezési csoporthoz:

1. Győződjön meg arról, hogy a fiókja olyan szerepkörhöz tartozik, amely írási engedélyeket biztosít a virtuálisgép-méretezési csoporton, például "Virtuális gépek közreműködője".

1. A virtuálisgép-méretezési készlet tulajdonságainak lekérése a `Get-AzVM` parancsmaggal. Ezután ha felhasználó által hozzárendelt felügyelt identitást szeretne hozzárendelni a virtuálisgép-méretezési csoporthoz, használja az `-IdentityType` `-IdentityID` [Update-AzVmss](/powershell/module/az.compute/update-azvmss) parancsmag és kapcsolóját. Cserélje `<VM NAME>` le a , , , és `<SUBSCRIPTION ID>` `<RESROURCE GROUP>` `<USER ASSIGNED ID1>` `USER ASSIGNED ID2` értékeket a saját értékeire.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```azurepowershell-interactive
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy Azure-beli virtuálisgép-méretezési csoportból

Ha a virtuálisgép-méretezési csoport több felhasználó által hozzárendelt felügyelt identitást is használ, az alábbi parancsokkal az utolsón csak az összeset távolíthatja el. Ne felejtse el a `<RESOURCE GROUP>` és `<VIRTUAL MACHINE SCALE SET NAME>` paraméterek értékeit a saját értékeire cserélni. A a felhasználó által hozzárendelt felügyelt identitás name (név) tulajdonsága, amelynek a virtuálisgép-méretezési csoportban `<USER ASSIGNED IDENTITY NAME>` kell maradnia. Ezek az információk a virtuálisgép-méretezési csoport identitás szakaszában találhatók a `az vmss show` használatával:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Ha a virtuálisgép-méretezési csoport nem rendelkezik rendszer által hozzárendelt felügyelt identitással, és el szeretné távolítani róla az összes felhasználó által hozzárendelt felügyelt identitást, használja a következő parancsot:

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Ha a virtuálisgép-méretezési csoport rendszer által hozzárendelt és felhasználó által hozzárendelt felügyelt identitásokkal is rendelkezik, az összes felhasználó által hozzárendelt felügyelt identitást eltávolíthatja, ha a kizárólag rendszer által hozzárendelt felügyelt identitás használatára vált.

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure-erőforrások felügyelt identitásának áttekintése](overview.md)
- Az Azure-beli virtuális gépek létrehozásának teljes rövid útmutatóiért lásd:
  
  - [Windows rendszerű virtuális gép létrehozása PowerShell használatával](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Linux rendszerű virtuális gép létrehozása PowerShell segítségével](../../virtual-machines/linux/quick-create-powershell.md)
