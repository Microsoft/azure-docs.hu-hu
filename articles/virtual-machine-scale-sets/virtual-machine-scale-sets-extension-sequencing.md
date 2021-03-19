---
title: Bővítmények sorrendbe állítása Azure-beli virtuálisgép-méretezési csoportokkal
description: Ismerje meg, hogyan hozhat létre több bővítményt a virtuálisgép-méretezési csoportokon a bővítmények kiépítése során.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 3271041b9f4db100cd05588129c7d714d4478f10
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83121031"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Szekvenciális bővítmény kiépítés a virtuálisgép-méretezési csoportokban
Az Azure-beli virtuálisgép-bővítmények olyan képességeket biztosítanak, mint az üzembe helyezés utáni konfiguráció és a felügyelet, a figyelés, a biztonság és egyebek. Az éles üzembe helyezések jellemzően a virtuálisgép-példányokhoz konfigurált több bővítmény kombinációját használják a kívánt eredmények eléréséhez.

Ha több bővítményt használ egy virtuális gépen, fontos annak biztosítása, hogy az azonos operációsrendszer-erőforrásokhoz szükséges bővítmények ne próbáljanak meg egyszerre megnyerni ezeket az erőforrásokat. Egyes bővítmények a szükséges konfigurációk, például a környezeti beállítások és a titkos kulcsok biztosításához más bővítmények is függenek. A megfelelő sorrend és előkészítés nélkül a függő bővítmények telepítései sikertelenek lehetnek.

Ez a cikk részletesen ismerteti, hogyan lehet beállítani a bővítményeket a virtuálisgép-méretezési csoportokban lévő virtuálisgép-példányokhoz.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már ismeri a következőket:
-   Azure-beli virtuálisgép- [bővítmények](../virtual-machines/extensions/overview.md)
-   Virtuálisgép-méretezési csoportok [módosítása](virtual-machine-scale-sets-upgrade-scale-set.md)

## <a name="when-to-use-extension-sequencing"></a>Mikor kell használni a bővítmények sorrendjét
A kiterjesztések sorrendbe állítása nem kötelező a méretezési csoportokhoz, és ha meg van adva, a bővítmények a méretezési csoport példányain bármilyen sorrendben kihelyezhetők.

Ha például a méretezési csoport modelljének két kiterjesztése van – kiterjesztés és ExtensionB – a modellben megadva, akkor a következő üzembe helyezési sorozatok bármelyike előfordulhat:
-   Bővítmény – > ExtensionB
-   ExtensionB – > bővítmény

Ha az alkalmazás az A bővítményt a B kiterjesztés előtt mindig kiépíti, akkor a bővítmények sorrendjét a jelen cikkben leírtak szerint kell használni. A bővítmények sorrendje mostantól csak egy sorozatot fog bekövetkezni:
-   Bővítmény – > ExtensionB

A megadott kiépítési sorozatban nem megadott kiterjesztések bármikor kihelyezhetők, beleértve a korábban, az után vagy egy megadott sorozatban. A bővítmények sorrendbe állítása csak azt adja meg, hogy egy adott bővítmény egy másik konkrét bővítmény után lesz kiépítve. Ez nem befolyásolja a modellben definiált bármely más bővítmény kiépítési felhelyezését.

Ha például a méretezési csoport modelljének három kiterjesztése van – az A bővítmény, a B bővítmény és a C kiterjesztés – a modellben van megadva, a C kiterjesztés pedig az A bővítmény után lesz kiépítve, akkor a következő kiépítési sorozatok egyike jelenhet meg:
-   Bővítmény – > ExtensionC – > ExtensionB
-   ExtensionB – > bővítmény – > ExtensionC
-   Bővítmény – > ExtensionB – > ExtensionC

Ha meg kell győződnie arról, hogy nincs más bővítmény kiépítve a definiált bővítmény-szekvencia végrehajtása során, javasoljuk, hogy az összes bővítményt a méretezési csoport modelljébe hozzon létre. A fenti példában a B bővítmény úgy állítható be, hogy a C kiterjesztés után is kiépíthető legyen, hogy csak egy sorozat lehessen:
-   Bővítmény – > ExtensionC – > ExtensionB


## <a name="how-to-use-extension-sequencing"></a>A bővítmény-előkészítés használata
A bővítmények kiosztásához frissítenie kell a kiterjesztés definícióját a méretezési csoport modelljében, hogy tartalmazza a "provisionAfterExtensions" tulajdonságot, amely a bővítmények neveinek tömbjét fogadja el. A tulajdonság tömb értékében megemlített kiterjesztéseket teljes mértékben meg kell határozni a méretezési csoport modelljében.

### <a name="template-deployment"></a>Sablon központi telepítése
Az alábbi példa egy olyan sablont definiál, amelyben a méretezési csoport három bővítményrel rendelkezik – bővítmény, ExtensionB és ExtensionC –, hogy a bővítmények a sorrendben legyenek kiépítve:
-   Bővítmény – > ExtensionB – > ExtensionC

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA"
          ],
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

Mivel a "provisionAfterExtensions" tulajdonság a bővítmények neveinek tömbjét fogadja el, a fenti példa módosítható úgy, hogy a ExtensionC a bővítmény és a ExtensionB után legyen kiépítve, de a bővítmény és a ExtensionB között nem szükséges rendezés. Ezt a forgatókönyvet a következő sablon használatával érheti el:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "ExtensionA",
        "properties": {
          "publisher": "ExtensionA.Publisher",
          "settings": {},
          "typeHandlerVersion": "1.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionA"
        }
      },
      {
        "name": "ExtensionB",
        "properties": {
          "publisher": "ExtensionB.Publisher",
          "settings": {},
          "typeHandlerVersion": "2.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionB"
        }
      }, 
      {
        "name": "ExtensionC",
        "properties": {
          "provisionAfterExtensions": [
            "ExtensionA","ExtensionB"
          ],
          "publisher": "ExtensionC.Publisher",
          "settings": {},
          "typeHandlerVersion": "3.0",
          "autoUpgradeMinorVersion": true,
          "type": "ExtensionC"                   
        }
      }
    ]
  }
}
```

### <a name="rest-api"></a>REST API
Az alábbi példa egy ExtensionC nevű új bővítményt helyez egy méretezési csoport modelljébe. A ExtensionC a bővítmény és a ExtensionB függőségeivel rendelkezik, amelyek már definiálva vannak a méretezési csoport modelljében.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "name": "ExtensionC",
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ],
    "publisher": "ExtensionC.Publisher",
    "settings": {},
    "typeHandlerVersion": "3.0",
    "autoUpgradeMinorVersion": true,
    "type": "ExtensionC" 
  }                  
}
```

Ha a ExtensionC korábban lett definiálva a méretezési csoport modelljében, és most hozzá kívánja adni a függőségeit, végrehajthatja a `PATCH` parancsot a már telepített bővítmény tulajdonságainak szerkesztéséhez.

```
PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/ExtensionC?api-version=2018-10-01`
```

```json
{ 
  "properties": {
    "provisionAfterExtensions": [
      "ExtensionA","ExtensionB"
    ]
  }                  
}
```
A meglévő méretezési csoport példányainak módosításait a rendszer a következő [frissítéskor](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)alkalmazza.

### <a name="azure-powershell"></a>Azure PowerShell
Az [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmaggal adja hozzá az alkalmazás-állapot bővítményt a méretezési csoport modellje definícióhoz. A bővítmény-szekvencia használatához az az PowerShell 1.2.0 vagy újabb verzió szükséges.

A következő példa hozzáadja az [alkalmazás állapotának kiterjesztését](virtual-machine-scale-sets-health-extension.md) egy `extensionProfile` Windows-alapú méretezési csoport méretezési csoport modelljéhez. Az alkalmazás állapota a méretezési csoporton már definiált [egyéni parancsfájl-kiterjesztés](../virtual-machines/extensions/custom-script-windows.md)kiosztása után lesz kiépítve.

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
  -ProvisionAfterExtension "CustomScriptExtension" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Az az [vmss Extension set](/cli/azure/vmss/extension#az-vmss-extension-set) paranccsal adja hozzá az alkalmazás állapota bővítményt a méretezési csoport modellje definícióhoz. A bővítmények sorrendje az Azure CLI-2.0.55 vagy újabb verzió használatát igényli.

Az alábbi példa hozzáadja az [alkalmazás-állapot bővítményt](virtual-machine-scale-sets-health-extension.md) egy Windows-alapú méretezési csoport méretezési csoport modelljéhez. Az alkalmazás állapota a méretezési csoporton már definiált [egyéni parancsfájl-kiterjesztés](../virtual-machines/extensions/custom-script-windows.md)kiosztása után lesz kiépítve.

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthWindows \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --provision-after-extensions CustomScriptExtension \
  --settings ./extension.json
```


## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="not-able-to-add-extension-with-dependencies"></a>Nem lehet függőségekkel bővíteni a bővítményt?
1. Győződjön meg arról, hogy a provisionAfterExtensions megadott bővítmények definiálva vannak a méretezési csoport modelljében.
2. Győződjön meg arról, hogy nincsenek bevezetve körkörös függőségek. A következő sorozat például nem engedélyezett: ExtensionA-> ExtensionB-> ExtensionC-> ExtensionA
3. Győződjön meg arról, hogy a függőben lévő összes bővítmény "Settings" tulajdonsággal rendelkezik a "Properties" kiterjesztésnél. Ha például a ExtentionB-t a bővítmény után kell kiépíteni, akkor a bővítménynek a "Properties" elemnél szerepelnie kell a "Settings" (beállítások) mezőnek. Megadhat egy üres "beállítások" tulajdonságot, ha a bővítmény nem kötelezi a szükséges beállításokat.

### <a name="not-able-to-remove-extensions"></a>Nem sikerült eltávolítani a bővítményeket?
Győződjön meg arról, hogy az eltávolítandó bővítmények nem szerepelnek a provisionAfterExtensions területen a többi bővítménynél.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [helyezheti üzembe az alkalmazást](virtual-machine-scale-sets-deploy-app.md) a virtuálisgép-méretezési csoportokban.
