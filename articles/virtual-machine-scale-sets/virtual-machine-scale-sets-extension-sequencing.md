---
title: Bővítmény-szekvenálás használata Azure-beli virtuálisgép-méretezési készletekkel
description: Megtudhatja, hogyan kell sorrendbe helyezni a bővítmények üzembe helyezését több bővítmény virtuálisgép-méretezési készletekben való üzembe helyezésekor.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 01/30/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1b5aea1f0f0101231408dc9ad7b57a30f2c86256
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788156"
---
# <a name="sequence-extension-provisioning-in-virtual-machine-scale-sets"></a>Sorozatbővítmény-kiépítés virtuálisgép-méretezési készletekben
Az Azure-beli virtuálisgép-bővítmények olyan képességeket biztosítanak, mint például az üzembe helyezés utáni konfigurálás és felügyelet, a monitorozás, a biztonság stb. Az éles környezetek általában több, a virtuálisgép-példányokhoz konfigurált bővítmény kombinációját használják a kívánt eredmények eléréséhez.

Ha több bővítményt használ egy virtuális gépen, fontos biztosítani, hogy az azonos operációsrendszer-erőforrásokat igénylő bővítmények ne próbálják meg egyszerre szerezni ezeket az erőforrásokat. Egyes bővítmények más bővítményektől is függenek a szükséges konfigurációk, például a környezeti beállítások és a titkos kulcsok megadása érdekében. A megfelelő sorrend és sorrendbe állítás nélkül a függő bővítmények üzembe helyezése meghiúsulhat.

Ez a cikk részletesen bemutatja, hogyan lehet a virtuálisgép-méretezési készletekben lévő virtuálisgép-példányokhoz konfigurálni a bővítményeket.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy ön ismeri a következőt:
-   Azure-beli [virtuálisgép-bővítmények](../virtual-machines/extensions/overview.md)
-   [Virtuálisgép-méretezési](virtual-machine-scale-sets-upgrade-scale-set.md) készletek módosítása

## <a name="when-to-use-extension-sequencing"></a>Mikor kell a bővítmény-alkalmazás-alkalmazásokat használni?
A alkalmazás-sorrendbe való alkalmazásának bővítményei nem kötelezőek a méretezési készletekhez, és ha nincs megadva, a bővítmények bármilyen sorrendben kiépítendők a méretezésikészlet-példányon.

Ha például a méretezési készlet modelljének két bővítménye van ( ExtensionA és ExtensionB – a modellben megadva, akkor a következő kiépítési sorozatok bármelyike történhet:
-   ExtensionA -> B bővítmény
-   ExtensionB -> ExtensionA

Ha az alkalmazáshoz az szükséges, hogy az A bővítmény mindig a B bővítmény előtt legyen kiépítve, akkor a jelen cikkben ismertetett bővítmény-alkalmazás-alkalmazásokat kell használnia. A bővítmények sorrendbe valórendezése mostantól csak egy sorozatot fog bekövetkezni:
-   ExtensionA – > B bővítmény

A megadott kiépítési sorrendben meg nem adott bővítmények bármikor kiépítve lesznek, beleértve a megadott sorrend előtt, után vagy közben is. A bővítmény-szekvenálás csak azt határozza meg, hogy egy adott bővítmény egy másik bővítmény után legyen kiépítve. Ez nem befolyásolja a modellben meghatározott egyéb bővítmények üzembeését.

Ha például a méretezési csoport modelljének három bővítménye van – az A bővítmény, a B bővítmény és a C bővítmény – a modellben megadva, és a C bővítmény úgy van beállítva, hogy az A bővítmény után legyen kiépítve, akkor a következő kiépítési sorozatok bármelyike történhet:
-   ExtensionA -> ExtensionC -> ExtensionB
-   ExtensionB -> ExtensionA -> ExtensionC
-   ExtensionA -> ExtensionB -> ExtensionC

Ha biztosítania kell, hogy a megadott bővítménysorozat végrehajtása közben ne legyen más bővítmény kiépítve, javasoljuk, hogy a méretezésikészlet-modellben minden bővítményt sorrendbe állítson. A fenti példában a B bővítmény beállítható úgy, hogy a C bővítmény után legyen kiépítve úgy, hogy csak egy sorozat következhet be:
-   ExtensionA -> ExtensionC -> ExtensionB


## <a name="how-to-use-extension-sequencing"></a>A bővítmények alkalmazás-alkalmazás-alkalmazásának használata
A bővítménykiépítés sorrendjének meghatározásához frissítenie kell a bővítménydefiníciót a méretezésicsoport-modellben, hogy tartalmazza a "provisionAfterExtensions" tulajdonságot, amely a bővítménynevek tömbjeként működik. A tulajdonságtömb értékében említett bővítményeket teljes mértékben definiálni kell a méretezésihalmaz-modellben.

### <a name="template-deployment"></a>Sablon üzembe helyezése
Az alábbi példa egy olyan sablont határoz meg, amelyben a méretezési készlet három bővítménysel rendelkezik (ExtensionA, ExtensionB és ExtensionC) úgy, hogy a bővítmények a sorrendben vannak kiépítve:
-   ExtensionA -> ExtensionB -> ExtensionC

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

Mivel a "provisionAfterExtensions" tulajdonság elfogadja a bővítménynevek tömböt, a fenti példa módosítható úgy, hogy az ExtensionC az ExtensionA és az ExtensionB után legyen kiépítve, de az ExtensionA és az ExtensionB között nincs szükség sorrendre. A forgatókönyv eléréséhez a következő sablon használható:

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
Az alábbi példa egy ExtensionC nevű új bővítményt ad hozzá egy méretezésikészlet-modellhez. Az ExtensionC az A és az ExtensionB bővítménytől függ, amelyek már meg vannak határozva a méretezésikészlet-modellben.

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

Ha az ExtensionC korábban lett definiálva a méretezésihalmaz-modellben, és most hozzá szeretné adni a függőségeit, végrehajthat egy parancsot a már üzembe helyezett bővítmény `PATCH` tulajdonságainak szerkesztéséhez.

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
A meglévő méretezésikészlet-példányok módosításait a rendszer a következő [frissítéskor alkalmazza.](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)

### <a name="azure-powershell"></a>Azure PowerShell
Az [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) parancsmag használatával adja hozzá az Application Health bővítményt a méretezésicsoport-modell definícióhoz. A bővítmény-szekvenáláshoz az Az PowerShell 1.2.0-s vagy azt feletti használata szükséges.

Az alábbi példa hozzáadja az [Application Health bővítményt](virtual-machine-scale-sets-health-extension.md) a alkalmazáshoz egy Windows-alapú méretezési készlet `extensionProfile` méretezésihalmaz-modelljében. Az Application Health bővítmény a méretezési [](../virtual-machines/extensions/custom-script-windows.md)csoportban már definiált egyéni szkriptbővítmény kiépítése után lesz kiépítve.

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
Az [az vmss extension set használatával](/cli/azure/vmss/extension#az_vmss_extension_set) adja hozzá az Application Health bővítményt a méretezésicsoport-modell definícióhoz. A bővítmény-szekvenáláshoz az Azure CLI 2.0.55-ös vagy azt feletti használata szükséges.

Az alábbi példa hozzáadja az [Application Health bővítményt](virtual-machine-scale-sets-health-extension.md) egy Windows-alapú méretezési készlet méretezésihalmaz-modelljéhez. Az Application Health bővítmény a méretezési [](../virtual-machines/extensions/custom-script-windows.md)csoportban már definiált egyéni szkriptbővítmény kiépítése után lesz kiépítve.

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

### <a name="not-able-to-add-extension-with-dependencies"></a>Nem tud függőségekkel bővítő bővítményt hozzáadni?
1. Győződjön meg arról, hogy a provisionAfterExtensions (kiépítési bővítmények) beállításban megadott bővítmények meg vannak határozva a méretezésikészlet-modellben.
2. Győződjön meg arról, hogy nem vezetnek be körkörös függőségeket. A következő sorozat például nem engedélyezett: ExtensionA -> ExtensionB -> ExtensionC -> ExtensionA
3. Győződjön meg arról, hogy minden bővítmény, amelytől függőséget hoz, rendelkezik egy "settings" (beállítások) tulajdonsággal a "properties" kiterjesztés alatt. Ha például az ExtentionB-t az ExtensionA után kell kiépítenünk, akkor az A bővítménynek rendelkeznie kell a "settings" (beállítások) mezővel az ExtensionA "properties" (Tulajdonságok) területen. Megadhat egy üres "beállítások" tulajdonságot, ha a bővítmény nem írja elő a szükséges beállításokat.

### <a name="not-able-to-remove-extensions"></a>Nem tudja eltávolítani a bővítményeket?
Győződjön meg arról, hogy az eltávolított bővítmények nem szerepelnek a provisionAfterExtensions listában a többi bővítménynél.

## <a name="next-steps"></a>Következő lépések
Megtudhatja, [hogyan helyezheti üzembe alkalmazását](virtual-machine-scale-sets-deploy-app.md) virtuálisgép-méretezési készleteken.
