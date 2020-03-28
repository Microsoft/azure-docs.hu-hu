---
title: Oktatóanyag – Virtuális gépek kezelése a PowerShell segítségével
description: Ez az oktatóanyag bemutatja, hogyan kezelheti Azure-beli virtuális gépeit az RBAC, szabályzatok, zárolások és címkék alkalmazásával az Azure PowerShell-lel
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.topic: tutorial
ms.date: 12/05/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: fd7e7f14d076a6a9652e902c4dc0ec41665735ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75981758"
---
# <a name="tutorial-learn-about-windows-virtual-machine-management-with-azure-powershell"></a>Oktatóanyag: Ismerje meg a Windows virtuálisgépek kezelését az Azure PowerShell segítségével

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell indítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shellt egy külön böngészőlapon [https://shell.azure.com/powershell](https://shell.azure.com/powershell)is elindíthatja a segítségével. A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="understand-scope"></a>A hatókör bemutatása

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Ebben az oktatóanyagban minden kezelési beállítást egy erőforráscsoportra alkalmaz, így egyszerűen eltávolíthatja a beállításokat, ha elkészült.

Hozzuk létre az erőforráscsoportot.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Az erőforráscsoport jelenleg üres.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Győződjön meg arról, hogy az intézmény felhasználói megfelelő hozzáférési szinttel rendelkeznek ezekhez az erőforrásokhoz. Nem ajánlott korlátlan hozzáférést nyújtania a felhasználóknak, de fontos biztosítania, hogy el tudják végezni a munkájukat. A [Szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md) kezelheti, hogy az egyes hatókörökben mely felhasználók hajthatnak végre adott műveleteket.

A szerepkör-hozzárendelések létrehozásához és eltávolításához a felhasználóknak `Microsoft.Authorization/roleAssignments/*` hozzáféréssel kell rendelkezniük. Ez a hozzáférés a tulajdonosi vagy a felhasználói hozzáférés rendszergazdájának szerepkörével adható meg.

A virtuálisgép-megoldások kezeléséhez három erőforrás-specifikus szerepkör létezik, amelyek biztosítják a leggyakrabban szükséges hozzáféréseket:

* [Virtuális gépek közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Hálózati közreműködő](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Tárfiók-közreműködő](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Ahelyett, hogy szerepköröket rendelne az egyéni felhasználókhoz, gyakran célszerűbb egy azokat a felhasználókat tartalmazó Azure Active Directory-csoportot használni, akiknek hasonló műveleteket kell elvégezniük. Ezután rendelje hozzá a csoportot a megfelelő szerepkörhöz. Ehhez a cikkhez használhat egy meglévő csoportot a virtuális gép kezeléséhez, vagy a portálon [létrehozhat egy Azure Active Directory-csoportot](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Új csoport létrehozása vagy egy meglévő keresése után a [New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment) paranccsal rendelje hozzá az Azure Active Directory-csoportot az erőforráscsoport virtuálisgép-közreműködői szerepköréhez.  

```azurepowershell-interactive
$adgroup = Get-AzADGroup -DisplayName <your-group-name>

New-AzRoleAssignment -ObjectId $adgroup.id `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Ha olyan hibaüzenetet kap, amely szerint **a> \<nem létezik a címtárban,** az új csoport nem propagált az Azure Active Directoryban. Próbálja meg ismét futtatni a parancsot.

A folyamatot általában a *Hálózati közreműködő* és a *Tárfiók-közreműködő* szerepkörön is végre kell hajtani, hogy a felhasználók megkapják az üzembe helyezett erőforrások kezeléséhez szükséges jogosultságokat. Ebben a cikkben kihagyhatja ezeket a lépéseket.

## <a name="azure-policy"></a>Azure szabályzat

Az [Azure Policy](../../governance/policy/overview.md) segítségével ellenőrizheti, hogy az előfizetés összes erőforrása megfelel-e a vállalati szabványoknak. Az előfizetése már számos szabályzatdefinícióval rendelkezik. A rendelkezésre álló házirend-definíciók megtekintéséhez használja a [Get-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/Get-AzPolicyDefinition) parancsot:

```azurepowershell-interactive
(Get-AzPolicyDefinition).Properties | Format-Table displayName, policyType
```

Itt láthatja a meglévő szabályzatdefiníciókat. A szabályzat típusa lehet **Beépített** vagy **Egyéni**. Keresse meg azokat a definíciókat, amelyek az Ön által hozzárendelni kívánt feltételt írják le. Ebben a cikkben olyan szabályzatokat rendel hozzá, amelyek:

* Korlátozzák az összes erőforrás helyét.
* Korlátozzák a virtuális gépek termékváltozatait.
* Naplózzák a felügyelt lemezeket nem használó virtuális gépeket.

A következő példában három olyan szabályzatdefiníciót fog lekérni, amely a megjelenítendő néven alapul. A [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment) paranccsal ezeket a definíciókat rendelheti az erőforráscsoporthoz. Egyes szabályzatoknál paraméterértékekkel határozhatja meg az engedélyezett értékeket.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>A virtuális gép üzembe helyezése

A szerepkörök és szabályzatok hozzárendelése megtörtént, így megkezdheti a megoldás üzembe helyezését. Az alapértelmezett méret a Standard_DS1_v2, amely az egyik engedélyezett termékváltozat. Ennek a lépésnek a futtatásakor a rendszer a hitelesítő adatok megadását kéri. Az itt megadott értékek határozzák meg a virtuális géphez tartozó felhasználónevet és jelszavát.

```azurepowershell-interactive
New-AzVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Az üzembe helyezés befejezése után további kezelési beállításokat alkalmazhat a megoldáson.

## <a name="lock-resources"></a>Erőforrások zárolása

Az [Erőforrás-zárolások](../../azure-resource-manager/management/lock-resources.md) megakadályozzák, hogy a cég vagy intézmény felhasználói véletlenül töröljék vagy módosítsák a kritikus erőforrásokat. Az erőforrás-zárolások a szerepköralapú hozzáférés-vezérléssel szemben minden felhasználóra és szerepkörre érvényes korlátozásokat alkalmaznak. A zárolási szintet *CanNotDelete* (nem törölhető) vagy *ReadOnly* (csak olvasható) értékre állíthatja be.

A virtuális gép és a hálózati biztonsági csoport zárolásához használja a [New-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcelock) parancsot:

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

A zárolások teszteléséhez futtassa az alábbi parancsot:

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroup
```

Megjelenik egy hibaüzenet, amely szerint a törlési művelet nem hajtható végre egy zárolás miatt. Az erőforráscsoport csak akkor törölhető, ha külön eltávolítja a zárolásokat. Ezt a lépést [Az erőforrások eltávolítása](#clean-up-resources) című szakasz ismerteti.

## <a name="tag-resources"></a>Erőforrások címkézése

[Címkéket](../../azure-resource-manager/management/tag-resources.md) alkalmazhat az Azure-erőforrásoklogikusan rendszerezheti őket kategóriák szerint. Minden címke egy névből és egy értékből áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Ha címkéket szeretne alkalmazni egy virtuális gépre, használja a [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) parancsot:

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Erőforrások keresése címke szerint

A címkenévvel és értékkel rendelkező erőforrások megkereséséhez használja a [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) parancsot:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Environment="Test"}).Name
```

A visszaadott értékeket kezelési feladatokhoz, például az adott címkeértékkel rendelkező összes virtuális gép leállításához használhatja.

```azurepowershell-interactive
Get-AzResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzVM
```

### <a name="view-costs-by-tag-values"></a>Költségek megtekintése címkeértékek szerint

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A zárolt hálózati biztonsági csoport nem törölhető a zárolás eltávolításáig. A zárolás eltávolításához használja az [Remove-AzResourceLock](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcelock) parancsot:

```azurepowershell-interactive
Remove-AzResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Ha már nincs szükség rá, az [Eltávolítás-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) paranccsal eltávolíthatja az erőforráscsoportot, a virtuális gépés az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy egyéni virtuálisgép-rendszerképet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Felhasználók hozzárendelése szerepkörhöz
> * Szabványokat kényszerítő szabályzatok alkalmazása
> * Kritikus erőforrások védelme zárolásokkal
> * Erőforrások címkézése számlázáshoz és felügyelethez

A következő oktatóanyagra lépve megismerkedhet a módosításokkal és a csomagfrissítések linuxos virtuális gépen történő kezelésével kapcsolatos tudnivalókat.

> [!div class="nextstepaction"]
> [Virtuális gépek kezelése](tutorial-config-management.md)

