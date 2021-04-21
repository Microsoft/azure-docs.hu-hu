---
title: Az Application Health bővítmény használata Azure-beli virtuálisgép-méretezési készletekkel
description: Ismerje meg, hogyan használhatja az Application Health bővítményt a virtuálisgép-méretezési készletekben üzembe helyezett alkalmazások állapotának figyelése érdekében.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 05/06/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 381573ae40f6c31a1c7dbf18bc60be5944fff39e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762892"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>Az Application Health bővítmény használata virtuálisgép-méretezési csoportokkal
Az alkalmazás állapotának figyelése fontos jelzés az üzemelő példány kezeléséhez és frissítéséhez. Az Azure-beli virtuálisgép-méretezési készletek támogatják a működés közbeni frissítéseket, beleértve az operációsrendszer-lemezképek automatikus frissítését is, amelyek az üzemelő példányok állapotfigyelésére támaszkodnak az üzemelő példány frissítéséhez. [](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) [](virtual-machine-scale-sets-automatic-upgrade.md) Az állapotbővítmény használatával a méretezési készletben lévő egyes példányok alkalmazásának állapotát is figyelheti, és példányjavításokat hajthat végre a példányok automatikus [javításával.](virtual-machine-scale-sets-automatic-instance-repairs.md)

Ez a cikk azt ismerteti, hogyan használhatja az Application Health bővítményt a virtuálisgép-méretezési készletekben üzembe helyezett alkalmazások állapotának figyelése érdekében.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy Ön ismeri a következőt:
-   Azure-beli [virtuálisgép-bővítmények](../virtual-machines/extensions/overview.md)
-   [Virtuálisgép-méretezési](virtual-machine-scale-sets-upgrade-scale-set.md) készletek módosítása

## <a name="when-to-use-the-application-health-extension"></a>Mikor kell használni az Application Health bővítményt?
Az Application Health bővítmény egy virtuálisgép-méretezésikészlet-példányon belül van üzembe állítva, és a méretezésikészlet-példányon belül jelenti a virtuális gépek állapotát. A bővítmény konfigurálható úgy, hogy mintavételt állítson be egy alkalmazásvégponton, és frissítse az alkalmazás állapotát a példányon. Az Azure ellenőrzi a példány állapotát annak megállapításához, hogy egy példány jogosult-e frissítési műveletekre.

[Mivel](../load-balancer/load-balancer-custom-probe-overview.md)a bővítmény egy virtuális gépen belülről jelenti az állapotadatokat, a bővítmény olyan helyzetekben használható, amikor nem használhatók külső mintavételek, például az Alkalmazás állapot-mintavételei (amelyek egyéni Azure Load Balancer mintavételeket használnak).

## <a name="extension-schema"></a>Bővítményséma

Az alábbi JSON az Application Health bővítmény sémáját mutatja be. A bővítményhez legalább egy "tcp", "http" vagy "https" kérésre van szükség egy társított porttal vagy kérés elérési úttal.

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>Tulajdonságértékek

| Name | Érték /Példa | Adattípus
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | dátum |
| közzétevő | `Microsoft.ManagedServices` | sztring |
| típus | `ApplicationHealthLinux` (Linux), `ApplicationHealthWindows` (Windows) | sztring |
| typeHandlerVersion | `1.0` | int |

### <a name="settings"></a>Beállítások

| Name | Érték / Példa | Adattípus
| ---- | ---- | ----
| Protokoll | `http`vagy `https``tcp` | sztring |
| port | Nem kötelező, ha a protokoll `http` vagy `https` , kötelező, ha a protokoll `tcp` | int |
| requestPath (kérésipath) | Kötelező, ha a protokoll `http` vagy `https` , nem engedélyezett, ha a protokoll `tcp` | sztring |

## <a name="deploy-the-application-health-extension"></a>Az Application Health bővítmény üzembe helyezése
Az Application Health bővítmény többféleképpen is üzembe helyezhető a méretezési készletekben az alábbi példákban részletezett módon.

### <a name="rest-api"></a>REST API

A következő példa hozzáadja az Application Health bővítményt (myHealthExtension névvel) egy Windows-alapú méretezési készlet méretezésikészlet-modelljében lévő extensionProfile bővítményhez.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
A `PATCH` használatával szerkesszen egy már üzembe helyezett bővítményt.

### <a name="azure-powershell"></a>Azure PowerShell

Az [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmag használatával adja hozzá az Application Health bővítményt a méretezésicsoport-modell definícióhoz.

Az alábbi példa hozzáadja az Application Health bővítményt egy Windows-alapú méretezési készlet `extensionProfile` méretezésikészlet-modelljéhez. A példa az új Az PowerShell-modult használja.

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

Az [az vmss extension set használatával](/cli/azure/vmss/extension#az_vmss_extension_set) adja hozzá az Application Health bővítményt a méretezésicsoport-modell definícióhoz.

Az alábbi példa hozzáadja az Application Health bővítményt egy Linux-alapú méretezési készlet méretezésihalmaz-modelljéhez.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
A extension.jsfájltartalomra vonatkozó tartalmú.

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>Hibaelhárítás
A bővítmény végrehajtási kimenetét a rendszer a következő könyvtárakban található fájlokba naplózza:

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/Microsoft.ManagedServices.ApplicationHealthLinux-<extension_version>/status
/var/log/azure/applicationhealth-extension
```

A naplók rendszeres időközönként rögzítik az alkalmazás állapotát is.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [helyezheti üzembe alkalmazását](virtual-machine-scale-sets-deploy-app.md) virtuálisgép-méretezési készleteken.
