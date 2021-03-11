---
title: Virtuálisgép-bővítmények telepítésének korlátozása Azure Policy használatával (Windows)
description: A bővítmények üzembe helyezésének korlátozásához használja a Azure Policy.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/23/2018
ms.openlocfilehash: 0587c2af8a90ce362fa6243e9da8a05734f0d8ec
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559749"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>A bővítmények Windows rendszerű virtuális gépeken való telepítésének korlátozása Azure Policy használatával

Ha meg szeretné akadályozni bizonyos bővítmények használatát vagy telepítését a Windows rendszerű virtuális gépeken, létrehozhat egy Azure Policy definíciót a PowerShell használatával, amellyel korlátozhatja a virtuális gépek bővítményeit az adott erőforráscsoporthoz. 

Ez az oktatóanyag Azure PowerShellt használ a Cloud Shellon belül, amely folyamatosan frissül a legújabb verzióra. 

 

## <a name="create-a-rules-file"></a>Szabály létrehozása fájl

A bővítmények telepítésének korlátozásához rendelkeznie kell egy olyan [szabállyal](../../governance/policy/concepts/definition-structure.md#policy-rule) , amely megadja a logikát a bővítmény azonosításához.

Ebből a példából megtudhatja, hogyan tagadhatja meg a "Microsoft. számítás" által közzétett bővítményeket a szabályok létrehozásával Azure Cloud Shellban, de ha helyileg dolgozik a PowerShellben, létrehozhat egy helyi fájlt is, és lecserélheti az elérési utat ($home/clouddrive) a helyi fájl elérési útjára a gépen.

A [Cloud Shell](https://shell.azure.com/powershell)írja be a következőt:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Másolja és illessze be a következő. JSON fájlt a fájlba.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Ha elkészült, nyomja le a **CTRL + O billentyűkombinációt** , majd a fájl mentéséhez **írja be** a következőt:. Nyomja le a **CTRL + X** billentyűkombinációt a fájl bezárásához és a kilépéshez.

## <a name="create-a-parameters-file"></a>Parameters-fájl létrehozása

Szükség van egy [paraméter](../../governance/policy/concepts/definition-structure.md#parameters) -fájlra is, amely létrehoz egy struktúrát, amellyel elvégezhető a letiltani kívánt bővítmények listájának átadása. 

Ebből a példából megtudhatja, hogyan hozhat létre paramétereket a virtuális gépekhez Cloud Shellban, de ha helyileg dolgozik a PowerShellben, létrehozhat egy helyi fájlt is, és lecserélheti az elérési utat ($home/clouddrive) a helyi fájl elérési útjára a gépen.

A [Cloud Shell](https://shell.azure.com/powershell)írja be a következőt:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Másolja és illessze be a következő. JSON fájlt a fájlba.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "displayName": "Denied extension"
        }
    }
}
```

Ha elkészült, nyomja le a **CTRL + O billentyűkombinációt** , majd a fájl mentéséhez **írja be** a következőt:. Nyomja le a **CTRL + X** billentyűkombinációt a fájl bezárásához és a kilépéshez.

## <a name="create-the-policy"></a>A szabályzat létrehozása

A házirend-definíció a használni kívánt konfiguráció tárolására szolgáló objektum. A házirend-definíció a szabályok és paraméterek fájlok használatával határozza meg a szabályzatot. Hozzon létre egy szabályzat-definíciót a [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition) parancsmag használatával.

 A házirend-szabályok és paraméterek a létrehozott és. JSON-fájlként tárolt fájlok a Cloud shellben.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>A szabályzat hozzárendelése

Ez a példa a szabályzatot egy erőforráscsoporthoz rendeli hozzá a [New-AzPolicyAssignment](/powershell/module/az.resources/new-azpolicyassignment)használatával. A **myResourceGroup** erőforráscsoporthoz létrehozott összes virtuális gép nem tudja telepíteni a virtuálisgép-hozzáférési ügynököt vagy az egyéni parancsfájl-bővítményeket. 

A [Get-AzSubscription használata | Formázza a Table](/powershell/module/az.accounts/get-azsubscription) parancsmagot, hogy az előfizetés-azonosítót a példában szereplő egyik helyett használja.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>A szabályzat tesztelése

A szabályzat teszteléséhez próbálja meg használni a virtuális gép elérési bővítményét. A következőnek sikertelennek kell lennie, ha a házirend nem engedélyezte a "set-AzVMAccessExtension: Resource" myVMAccess "üzenetet."

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

A portálon a jelszó módosításának sikertelennek kell lennie a "a sablon központi telepítése sikertelen volt a szabályzat megsértése miatt." üzenetet küldi vissza.

## <a name="remove-the-assignment"></a>A hozzárendelés eltávolítása

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>A házirend eltávolítása

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Következő lépések
További információ: [Azure Policy](../../governance/policy/overview.md).
