---
title: Automatikus bővítményfrissítés virtuális gépekhez és méretezési készletekhez az Azure-ban
description: Megtudhatja, hogyan engedélyezheti az automatikus bővítményfrissítést a virtuális gépek és virtuálisgép-méretezési készletek számára az Azure-ban.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrade
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: bf9e802e2485e84211044ce650c7748e789e752e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762604"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Előzetes verzió: Automatikus bővítményfrissítés virtuális gépekhez és méretezési készletekhez az Azure-ban

Az automatikus bővítményfrissítés előzetes verzióban érhető el az Azure-beli virtuális gépekhez és az Azure Virtual Machine Scale Sets. Ha egy virtuális gépen vagy méretezési csoporton engedélyezve van az automatikus bővítményfrissítés, a bővítmény automatikusan frissül, amikor a bővítmény közzétevője új verziót ad ki a bővítményhez.

 Az automatikus bővítményfrissítés a következő funkciókat tartalmazza:
- Az Azure-beli virtuális gépek és az Azure Virtual Machine Scale Sets. Service Fabric Virtual Machine Scale Sets jelenleg nem támogatottak.
- A frissítéseket a rendszer egy rendelkezésre állási szintű üzembe helyezési modellben alkalmazza (az alábbiakban részletesen).
- Virtuálisgép-méretezési készlet esetén a méretezési készlet virtuális gépének nem több mint 20%-a lesz frissítve egyetlen kötegben. A köteg minimális mérete egy virtuális gép.
- Minden virtuálisgép-mérethez, valamint Windows- és Linux-bővítményekhez is használható.
- Az automatikus frissítéseket bármikor kikapcsolhatja.
- Az automatikus bővítményfrissítés bármilyen méretű Virtual Machine Scale Sets engedélyezhető.
- Az egyes támogatott bővítmények egyenként vannak regisztrálva, és kiválaszthatja, hogy mely bővítményeket frissítse automatikusan.
- Az összes nyilvános felhőrégióban támogatott.


> [!IMPORTANT]
> Az automatikus bővítményfrissítés jelenleg nyilvános előzetes verzióban érhető el. Az alább ismertetett nyilvános előzetes verziójú funkciók használatához egy lemondási eljárásra van szükség.
> Ez az előzetes verzió szolgáltatói szerződés nélkül érhető el, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Hogyan működik az automatikus bővítményfrissítés?
A bővítményfrissítési folyamat lecseréli a virtuális gép meglévő bővítményverzióját ugyanannak a bővítménynek egy új verziójára, amikor a bővítmény közzétevője közzéteszi. A virtuális gép állapota az új bővítmény telepítése után monitorozásra kerül. Ha a virtuális gép a frissítés befejezését követő 5 percen belül nem kifogástalan állapotban van, a bővítmény verziója vissza lesz gördülve az előző verzióra.

A rendszer automatikusan újraküldi a sikertelen bővítményfrissítéseket. A rendszer néhány naponta automatikusan, felhasználói beavatkozás nélkül kísérel meg újrapróbálkozásokat.

### <a name="availability-first-updates"></a>Rendelkezésre állásra vonatkozó első frissítések
A platform által vezényelt frissítések rendelkezésre állásra vonatkozó első modellje biztosítja, hogy az Azure rendelkezésre állási konfigurációi több rendelkezésre állási szinten is teljesüljenek.

A frissítés alatt lévő virtuális gépek egy csoportja számára az Azure platform fogja összehangolni a frissítéseket:

**Régiók között:**
- A frissítések globálisan, szakaszos módon haladnak át az Azure-ban, hogy megakadályozzák az Azure-beli üzembe helyezési hibákat.
- Egy "fázis" egy vagy több régióval is lehet, és a frissítés csak akkor lép át a fázisok között, ha az előző fázisban lévő jogosult virtuális gépek sikeresen frissülnek.
- A földrajzilag párosított régiók nem frissülnek egyidejűleg, és nem lehet ugyanabban a regionális fázisban.
- A frissítés sikerességének méréséhez nyomon kell követni a virtuális gép állapotát a frissítés után. A virtuális gép állapotát a virtuális gép platform-állapotjelzői követik nyomon. A Virtual Machine Scale Sets a virtuális gép állapotát az alkalmazás állapot-mintavételei vagy az Application Health bővítmény követi nyomon, ha a méretezési készletre van alkalmazva.

**Egy régión belül:**
- A különböző Availability Zones virtuális gépek nem frissülnek egyidejűleg.
- A rendelkezésre állási készlet részét nem képezi egyetlen virtuális gép kötegbe van állítva, hogy elkerülje az előfizetésben futó összes virtuális gép egyidejű frissítését.  

**Egy "készleten" belül:**
- Egy közös rendelkezésre állási vagy méretezési készletben nem minden virtuális gép frissül egyidejűleg.  
- Az egy közös rendelkezésre állási csoportban lévő virtuális gépek a frissítési tartományhatáron belül frissülnek, a több frissítési tartományban lévő virtuális gépek pedig nem frissülnek egyidejűleg.  
- Az egy közös virtuálisgép-méretezési csoportban lévő virtuális gépek kötegekbe vannak csoportosítva, és a frissítési tartományhatáron belül frissülnek.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Frissítési folyamat a Virtual Machine Scale Sets
1. A frissítési folyamat megkezdése előtt a vezénylési rendszer biztosítja, hogy a teljes méretezési készletben a virtuális gépeknek csak 20%-a legyen nem megfelelő (bármilyen okból).

2. A frissítési vezénylés azonosítja a frissíthető virtuálisgép-példányok kötegét. A frissítési kötegek a teljes virtuálisgép-szám legfeljebb 20%-át kaphatják meg, egy virtuális gép minimális kötegméretének megfelelően.

3. A konfigurált alkalmazás-állapot-mintavételekkel vagy Application Health bővítővel konfigurált méretezési készletek esetén a frissítés legfeljebb 5 percet (vagy a megadott állapot-mintavételi konfigurációt) vár a virtuális gép kifogástalan állapotára a következő köteg frissítése előtt. Ha egy virtuális gép állapota a frissítés után nem áll helyre, akkor a rendszer alapértelmezés szerint újratelepíti a virtuális gép előző bővítményverzióját.

4. A frissítési vezénylés a frissítés után nem megfelelővé vált virtuális gépek százalékos arányát is nyomon követi. A frissítés leáll, ha a frissített példányok több mint 20%-a nem megfelelő a frissítési folyamat során.

A fenti folyamat addig folytatódik, amíg a méretezési készletben az összes példány frissítése meg nem történt.

A méretezési készlet frissítési vezénylője minden köteg frissítése előtt ellenőrzi a méretezési készlet általános állapotát. A kötegek frissítése közben más tervezett vagy nem tervezett karbantartási tevékenységek is lehetnek, amelyek hatással lehetnek a méretezési készlet virtuális gépei állapotára. Ilyen esetekben, ha a méretezési készlet példányainak több mint 20%-a nem megfelelő, akkor a méretezési készlet frissítése az aktuális köteg végén leáll.

## <a name="supported-extensions"></a>Támogatott bővítmények
Az automatikus bővítményfrissítés előzetes verziója a következő bővítményeket támogatja (és rendszeresen bővül újabbak is):
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) és [Linux](./extensions/agent-dependency-linux.md)
- [Alkalmazás állapotának bővítménye](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows és Linux


## <a name="enabling-preview-access"></a>Előzetes verziójú hozzáférés engedélyezése
Az előzetes verziójú funkció engedélyezéséhez előfizetésenként egyszer kell engedélyezni az **AutomaticExtensionUpgradePreview** funkciót az alább részletezett módon.

### <a name="rest-api"></a>REST API
Az alábbi példa bemutatja, hogyan engedélyezheti az előzetes verziójú előfizetését:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Miután regisztrálta a funkciót az előfizetésében, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással kell befejeznie a feliratkozási folyamatot.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
A [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsmag használatával engedélyezheti az előzetes verzió használatát az előfizetéséhez.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Miután regisztrálta a funkciót az előfizetésében, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással kell befejeznie a feliratkozási folyamatot.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
Az [az feature register használatával](/cli/azure/feature#az_feature_register) engedélyezheti az előzetes verzió használatát az előfizetéséhez.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

A szolgáltatásregisztráció akár 15 percet is igénybe vehet. A regisztráció állapotának ellenőrzése:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Miután regisztrálta a funkciót az előfizetésében, a módosításnak a Compute erőforrás-szolgáltatóba való propagálással kell befejeznie a feliratkozási folyamatot.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Az automatikus bővítményfrissítés engedélyezése
Ha engedélyezni szeretné egy bővítmény automatikus bővítményfrissítését, győződjön meg arról, hogy az *enableAutomaticUpgrade* tulajdonság *true* (igaz) értékre van állítva, és minden bővítménydefinícióhoz külön-külön hozzá kell adni.


### <a name="rest-api-for-virtual-machines"></a>REST API a Virtual Machines
Ha engedélyezni szeretné egy bővítmény (ebben a példában az Dependency Agent bővítmény) automatikus frissítését egy Azure-beli virtuális gépen, használja a következőt:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>REST API a Virtual Machine Scale Sets
A bővítményt a következőkkel használhatja a méretezésihalmaz-modellhez:

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Azure PowerShell a Virtual Machines
Használja a [Set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) parancsmagot:

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Azure PowerShell a Virtual Machine Scale Sets
Az [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmag használatával adja hozzá a bővítményt a méretezésihalmaz-modellhez:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

A bővítmény hozzáadása után frissítse a méretezési készletet az [Update-AzVmss](/powershell/module/az.compute/update-azvmss) használatával.


### <a name="azure-cli-for-virtual-machines"></a>Azure CLI a virtuális gépekhez
Használja az [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) parancsmagot:

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Azure CLI a Virtual Machine Scale Sets
Az [az vmss extension set parancsmag](/cli/azure/vmss/extension#az_vmss_extension_set) használatával adja hozzá a bővítményt a méretezésihalmaz-modellhez:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Bővítményfrissítések több bővítvekkel

Egy virtuális géphez vagy virtuálisgép-méretezési csoporthoz több bővítmény is lehet, amelyeken engedélyezve van az automatikus bővítményfrissítés. Ugyanannak a virtuális gépnek vagy méretezési csoportnak más bővítményei is lehetek anélkül, hogy engedélyezve lenne az automatikus bővítményfrissítés.  

Ha egy virtuális géphez több bővítményfrissítés is elérhető, a frissítések kötegbe tehetőek, de minden bővítményfrissítés külön-külön lesz alkalmazva egy virtuális gépen. Az egyik bővítmény hibája nem befolyásolja az esetleg frissítő többi bővítményt. Ha például két bővítmény frissítésre van ütemezve, és az első bővítmény frissítése sikertelen, a második bővítmény továbbra is frissítve lesz.

Az automatikus bővítményfrissítések akkor is alkalmazhatók, ha egy virtuális gép vagy virtuálisgép-méretezési készlet több bővítményt konfigurált [bővítmény-alkalmazással.](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md) A bővítmény-szekvenálás a virtuális gép első üzembe helyezésére vonatkozik, és a bővítmények jövőbeli frissítései egymástól függetlenül vannak alkalmazva.


## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Tudnivalók az Application Health bővítményről](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
