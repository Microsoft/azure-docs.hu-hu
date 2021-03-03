---
title: A virtuális gépek és a méretezési csoportok automatikus bővítményének frissítése az Azure-ban
description: Megtudhatja, hogyan engedélyezheti az automatikus bővítmény frissítését a virtuális gépek és a virtuálisgép-méretezési csoportok számára az Azure-ban.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-extension-upgrades
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: 104eada6dc342c21b8da2f409756e9f34c103936
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668333"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>Előzetes verzió: az Azure-beli virtuális gépek és méretezési csoportok automatikus bővítményének frissítése

Az automatikus bővítmény frissítése az Azure-beli virtuális gépek és az Azure Virtual Machine Scale Sets előzetes verziójában érhető el. Ha engedélyezve van az automatikus bővítmény frissítése a virtuális gépen vagy a méretezési csoporton, a bővítmény automatikusan frissül, amikor a bővítmény közzétevője új verziót szabadít fel az adott bővítmény számára.

 Az automatikus bővítmény frissítése a következő funkciókkal rendelkezik:
- Azure-beli virtuális gépek és Azure-Virtual Machine Scale Sets esetén támogatott. A Service Fabric Virtual Machine Scale Sets jelenleg nem támogatottak.
- A frissítések a rendelkezésre állási első üzembe helyezési modellben lesznek alkalmazva (lásd alább).
- Virtuálisgép-méretezési csoport esetén a méretezési csoport virtuális gépei nem több mint 20%-a lesz frissítve egyetlen kötegben. A köteg minimális mérete egy virtuális gép.
- Minden virtuálisgép-mérettel működik, valamint Windows-és Linux-bővítmények esetén is.
- Bármikor letilthatja az automatikus frissítést.
- Az automatikus bővítmények frissítése bármilyen méretű Virtual Machine Scale Sets engedélyezhető.
- Minden támogatott bővítmény regisztrálása külön történik, és kiválaszthatja, hogy mely bővítmények legyenek automatikusan frissítve.
- Minden nyilvános felhőalapú régióban támogatott.


> [!IMPORTANT]
> Az automatikus bővítmény frissítése jelenleg nyilvános előzetes verzióban érhető el. Az alábbiakban ismertetett nyilvános előzetes funkciók használatához egy opt-in eljárás szükséges.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="how-does-automatic-extension-upgrade-work"></a>Hogyan működik az automatikus bővítmény frissítése?
A bővítmény frissítési folyamata lecseréli a meglévő bővítmény verzióját egy virtuális gépre, amely a bővítmény közzétevője által közzétett, azonos kiterjesztésű új verzióval rendelkezik. A virtuális gép állapotát az új bővítmény telepítése után figyeli a rendszer. Ha a virtuális gép nem kifogástalan állapotban van a frissítés befejezését követően 5 percen belül, a bővítmény verziója visszaáll az előző verzióra.

A rendszer automatikusan újrapróbálkozik egy sikertelen bővítmény frissítésével. Az újrapróbálkozást a rendszer minden egyes nap automatikusan megkísérli a felhasználói beavatkozás nélkül.

### <a name="availability-first-updates"></a>Rendelkezésre állás – első frissítések
A platformmal összehangolt frissítések rendelkezésre állása – első modellje biztosítja, hogy az Azure-beli rendelkezésre állási konfigurációk több rendelkezésre állási szinten legyenek figyelembe véve.

A frissítés alatt álló virtuális gépek egy csoportja esetében az Azure platform a frissítéseket fogja összehangolni:

**Régiók között:**
- Egy frissítés globálisan zajlik az Azure-ban az Azure-alapú üzembe helyezési hibák megelőzése érdekében.
- A "Phase" egy vagy több régióval rendelkezhet, és a frissítés csak akkor halad át a fázisokban, ha a jogosult virtuális gépek az előző fázisban sikeresen frissültek.
- A Geo-párosítású régiók nem frissülnek egyidejűleg, és nem lehetnek ugyanabban a regionális fázisban.
- A frissítés sikerességét a virtuális gépek frissítés utáni állapotának nyomon követésével mérjük. A virtuális gép állapotának nyomon követése a virtuális gép platformjának állapotára vonatkozó mutatókon keresztül történik. Virtual Machine Scale Sets esetében a virtuálisgép-állapot nyomon követése az Application Health-vizsgálatok vagy az alkalmazás-állapot bővítmény használatával történik, ha a méretezési csoportra alkalmazva van.

**Régión belül:**
- A különböző Availability Zonesban lévő virtuális gépek egyidejű frissítése nem történik meg.
- A rendelkezésre állási csoportba nem tartozó egyetlen virtuális gép a legjobb erőfeszítést eredményez, hogy elkerülje az előfizetésben lévő összes virtuális gép egyidejű frissítését.  

**A "Set" értéken belül:**
- A Common rendelkezésre állási csoportba vagy méretezési csoportba tartozó összes virtuális gép egyidejű frissítése nem történik meg.  
- A Common rendelkezésre állási csoportba tartozó virtuális gépek a frissítési tartomány határain belül frissülnek, a több frissítési tartományba tartozó virtuális gépek pedig nem frissülnek egyszerre.  
- A közös virtuálisgép-méretezési csoportba tartozó virtuális gépek kötegekben vannak csoportosítva, és frissülnek a frissítési tartomány határain belül.

### <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets frissítési folyamata
1. A frissítési folyamat megkezdése előtt a Orchestrator biztosítja, hogy a teljes méretezési csoporton belül a virtuális gépek legfeljebb 20%-a sérült (bármilyen okból).

2. A frissítési Orchestrator azonosítja a frissítendő virtuálisgép-példányok kötegét. Egy frissítési köteg legfeljebb 20%-kal rendelkezhet a virtuális gépek teljes számától, és egy virtuális gép minimális batch-mérete is megadható.

3. A konfigurált alkalmazás-állapottal vagy alkalmazás-állapottal rendelkező méretezési csoportok esetében a frissítés a következő köteg frissítése előtt legfeljebb 5 percet vár (vagy a definiált állapot mintavételi konfigurációja), hogy a virtuális gép Kifogástalan állapotba kerüljön. Ha egy virtuális gép a frissítés után nem állítja helyre az állapotát, akkor a rendszer alapértelmezés szerint újratelepíti az előző bővítmény verzióját a virtuális gépen.

4. A frissítési Orchestrator azt a virtuális gépek százalékos arányát is nyomon követi, amelyek a frissítés után nem Kifogástalan állapotba válnak. A frissítés leáll, ha a frissített példányok több mint 20%-a nem Kifogástalan állapotba kerül a frissítési folyamat során.

A fenti folyamat addig folytatódik, amíg a méretezési csoport összes példánya frissítve nem lett.

A méretezési csoport frissítési Orchestrator az összes köteg frissítése előtt ellenőrzi a teljes méretezési csoport állapotát. A kötegek frissítésekor előfordulhat, hogy a méretezési csoport virtuális gépei állapotát befolyásoló egyidejű tervezett vagy nem tervezett karbantartási tevékenységek lehetnek. Ilyen esetekben, ha a méretezési csoport példányainak több mint 20%-a nem megfelelő állapotba kerül, a méretezési csoport frissítése az aktuális köteg végén leáll.

## <a name="supported-extensions"></a>Támogatott bővítmények
Az automatikus bővítmény frissítésének előnézete a következő bővítményeket támogatja (és rendszeresen bővülnek):
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) és [Linux](./extensions/agent-dependency-linux.md)
- [Application Health bővítmény](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows és Linux


## <a name="enabling-preview-access"></a>Az előnézeti hozzáférés engedélyezése
Az előzetes verzió működésének engedélyezéséhez egyszeri bejelentkezésre van szükség az **AutomaticExtensionUpgradePreview** szolgáltatáshoz, az alábbiakban részletezett módon.

### <a name="rest-api"></a>REST API
Az alábbi példa azt ismerteti, hogyan engedélyezhető az előfizetés előnézete:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

A szolgáltatás regisztrálása akár 15 percet is igénybe vehet. A regisztráció állapotának ellenõrzése:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

Miután regisztrálta a szolgáltatást az előfizetéséhez, fejezze be a beléptetési folyamatot a számítási erőforrás-szolgáltatóra történő váltás propagálásával.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Az előfizetés előnézetének engedélyezéséhez használja a [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancsmagot.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

A szolgáltatás regisztrálása akár 15 percet is igénybe vehet. A regisztráció állapotának ellenõrzése:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

Miután regisztrálta a szolgáltatást az előfizetéséhez, fejezze be a beléptetési folyamatot a számítási erőforrás-szolgáltatóra történő váltás propagálásával.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
Az az [Feature Register](/cli/azure/feature#az-feature-register) paranccsal engedélyezheti az előfizetésének előnézetét.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

A szolgáltatás regisztrálása akár 15 percet is igénybe vehet. A regisztráció állapotának ellenõrzése:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

Miután regisztrálta a szolgáltatást az előfizetéséhez, fejezze be a beléptetési folyamatot a számítási erőforrás-szolgáltatóra történő váltás propagálásával.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>Az automatikus bővítmény frissítésének engedélyezése
Ha engedélyezni szeretné az automatikus bővítmény frissítését egy bővítmény számára, győződjön meg arról, hogy a *enableAutomaticUpgrade* tulajdonság értéke *true (igaz* ), és minden egyes bővítmény-definícióhoz hozzá van adva.


### <a name="rest-api-for-virtual-machines"></a>Virtual Machines REST API
Ha engedélyezni szeretné az automatikus bővítmény frissítését egy bővítményhez (ebben a példában a Dependency Agent bővítményt) egy Azure-beli virtuális gépen, használja a következőt:

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

### <a name="rest-api-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets REST API
A következő paranccsal adhatja hozzá a bővítményt a méretezési csoport modelljéhez:

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

### <a name="azure-powershell-for-virtual-machines"></a>Virtual Machines Azure PowerShell
Használja a [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension) parancsmagot:

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


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets Azure PowerShell
A bővítmény a méretezési csoport modelljéhez való hozzáadásához használja az [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmagot:

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

A bővítmény hozzáadása után frissítse a méretezési csoport [Update-AzVmss](/powershell/module/az.compute/update-azvmss) használatával.


### <a name="azure-cli-for-virtual-machines"></a>Azure CLI a virtuális gépekhez
Használja az az [VM Extension set](/cli/azure/vm/extension#az_vm_extension_set) parancsmagot:

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Azure CLI a Virtual Machine Scale Setshoz
A bővítmény a méretezési csoport modelljéhez való hozzáadásához használja az az [vmss Extension](/cli/azure/vmss/extension#az_vmss_extension_set) parancsmagot:

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>Bővítmények frissítése több bővítménnyel

Egy virtuális gép vagy virtuálisgép-méretezési csoport több bővítménnyel is rendelkezhet, amelyeken engedélyezve van az automatikus bővítmény frissítése. Ugyanez a virtuális gép vagy méretezési csoport más bővítményekkel is rendelkezhet, ha engedélyezve van az automatikus bővítmény frissítése.  

Ha egy virtuális gép több bővítményt is frissít, akkor előfordulhat, hogy a frissítések kötegbe vannak csoportosítva, de az egyes bővítmények frissítése külön történik a virtuális gépen. Az egyik bővítmény hibája nem befolyásolja az esetlegesen frissíthető többi bővítményt (ka) t. Ha például két bővítmény van ütemezve egy frissítésre, és az első bővítmény frissítése meghiúsul, a második Kiterjesztés továbbra is frissül.

Az automatikus bővítmények frissítése akkor is alkalmazható, ha egy virtuális gép vagy virtuálisgép-méretezési csoport több bővítményt is konfigurál a [bővítmény-előkészítéssel](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). A bővítmények sorrendbe állítása a virtuális gép első üzembe helyezése esetén érvényes, és a bővítmények jövőbeli frissítései egymástól függetlenül lesznek alkalmazva.


## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Tudnivalók az alkalmazás állapotának kiterjesztéséről](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md)
