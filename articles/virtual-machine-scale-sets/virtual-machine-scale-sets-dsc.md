---
title: A kívánt állapot konfigurációjának használata Virtual Machine Scale Sets
description: A virtuális gépek konfigurálásához használja a Virtual Machine Scale Setst az Azure desired State Configuration bővítménnyel.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 6/25/2020
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 20e5bff87d5cd0d6e0a35a558462bb5598bfe3f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87080488"
---
# <a name="using-virtual-machine-scale-sets-with-the-azure-dsc-extension"></a>Virtual Machine Scale Sets használata az Azure DSC bővítménnyel
[Virtual Machine Scale sets](./overview.md) használható az [Azure desired State Configuration (DSC)](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) bővítmény kezelőjével. A virtuálisgép-méretezési csoportok nagy számú virtuális gép üzembe helyezését és felügyeletét teszik lehetővé, és rugalmasan méretezhetik be és ki a terhelésre válaszul. A DSC használatával a virtuális gépek úgy konfigurálhatók, ahogy online állapotba kerülnek, így az éles szoftvert futtatják.

## <a name="differences-between-deploying-to-virtual-machines-and-virtual-machine-scale-sets"></a>A Virtual Machines és Virtual Machine Scale Sets üzembe helyezése közötti különbségek
A virtuálisgép-méretezési csoport mögöttes sablon szerkezete némileg eltér egyetlen virtuális gépről. Pontosabban, egyetlen virtuális gép telepíti a bővítményeket a "virtualMachines" csomópont alatt. Létezik egy "Extensions" típusú bejegyzés, amelyben a DSC hozzá van adva a sablonhoz

```
"resources": [
          {
              "name": "Microsoft.Powershell.DSC",
              "type": "extensions",
              "location": "[resourceGroup().location]",
              "apiVersion": "2015-06-15",
              "dependsOn": [
                  "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
              ],
              "tags": {
                  "displayName": "dscExtension"
              },
              "properties": {
                  "publisher": "Microsoft.Powershell",
                  "type": "DSC",
                  "typeHandlerVersion": "2.20",
                  "autoUpgradeMinorVersion": false,
                  "forceUpdateTag": "[parameters('dscExtensionUpdateTagVersion')]",
                  "settings": {
                      "configuration": {
                          "url": "[concat(parameters('_artifactsLocation'), '/', variables('dscExtensionArchiveFolder'), '/', variables('dscExtensionArchiveFileName'))]",
                          "script": "DscExtension.ps1",
                          "function": "Main"
                      },
                      "configurationArguments": {
                          "nodeName": "[variables('vmName')]"
                      }
                  },
                  "protectedSettings": {
                      "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                  }
              }
          }
      ]
```

A virtuálisgép-méretezési csoport csomópontjának "Properties" szakasza "VirtualMachineProfile", "extensionProfile" attribútummal rendelkezik. A DSC a "Extensions" alatt lett hozzáadva

```
"extensionProfile": {
            "extensions": [
                {
                    "name": "Microsoft.Powershell.DSC",
                    "properties": {
                        "publisher": "Microsoft.Powershell",
                        "type": "DSC",
                        "typeHandlerVersion": "2.20",
                        "autoUpgradeMinorVersion": false,
                        "forceUpdateTag": "[parameters('DscExtensionUpdateTagVersion')]",
                        "settings": {
                            "configuration": {
                                "url": "[concat(parameters('_artifactsLocation'), '/', variables('DscExtensionArchiveFolder'), '/', variables('DscExtensionArchiveFileName'))]",
                                "script": "DscExtension.ps1",
                                "function": "Main"
                            },
                            "configurationArguments": {
                                "nodeName": "localhost"
                            }
                        },
                        "protectedSettings": {
                            "configurationUrlSasToken": "[parameters('_artifactsLocationSasToken')]"
                        }
                    }
                }
            ]
```

## <a name="behavior-for-a-virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport viselkedése
A virtuálisgép-méretezési csoport viselkedése azonos egyetlen virtuális gép viselkedésével. Új virtuális gép létrehozásakor a rendszer automatikusan kiépíti a DSC-bővítményt. Ha a bővítmény a WMF újabb verzióját igényli, a virtuális gép újraindul, mielőtt online állapotba kerül. Ha online állapotban van, letölti a DSC Configuration. zip fájlt, és kiépíti a virtuális gépre. További részleteket [Az Azure DSC bővítmény áttekintésében](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json)találhat.

## <a name="next-steps"></a>Következő lépések
Vizsgálja [meg a DSC-bővítmény Azure Resource Manager sablonját](../virtual-machines/extensions/dsc-template.md?toc=/azure/virtual-machines/windows/toc.json).

Ismerje meg, hogyan [kezeli a DSC-bővítmény a hitelesítő adatok biztonságos kezelését](../virtual-machines/extensions/dsc-credentials.md?toc=/azure/virtual-machines/windows/toc.json). 

Az Azure DSC bővítmény kezelőjével kapcsolatos további információkért lásd: [Bevezetés az Azure kívánt állapotának konfigurációs bővítmény-kezelőjébe](../virtual-machines/extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json). 

A PowerShell DSC-vel kapcsolatos további információkért [látogasson el a PowerShell dokumentációs központba](/powershell/scripting/dsc/overview/overview). 
