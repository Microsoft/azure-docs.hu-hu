---
title: A Azure Disk Encryption és az Azure virtuálisgép-méretezési csoportok bővítmény-sorrendje
description: Ebből a cikkből megtudhatja, hogyan engedélyezheti Microsoft Azure lemezes titkosítást linuxos IaaS virtuális gépekhez.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 1aff05e51bcbc99f33325efb905ade819ae22e02
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90988032"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>A Azure Disk Encryption használata a virtuálisgép-méretezési csoport bővítményének előkészítésével

A bővítmények, például az Azure Disk Encryption hozzáadhatók egy adott sorrendben elérhető Azure-beli virtuálisgép-méretezési csoportokhoz. Ehhez használja a [bővítmények sorrendjét](virtual-machine-scale-sets-extension-sequencing.md). 

Általánosságban a titkosítást egy lemezre kell alkalmazni:

- A lemezeket vagy köteteket előkészítő bővítmények vagy egyéni parancsfájlok után.
- Olyan bővítmények vagy egyéni parancsfájlok előtt, amelyek hozzáférnek vagy használják a titkosított lemezeken vagy köteteken tárolt adatmennyiséget.

Mindkét esetben a tulajdonság azt jelzi, hogy `provisionAfterExtensions` melyik bővítményt kell hozzáadni később a sorozatban.

## <a name="sample-azure-templates"></a>Példa Azure-sablonokra

Ha azt szeretné, hogy a Azure Disk Encryption egy másik bővítmény után is alkalmazza, helyezze a `provisionAfterExtensions` tulajdonságot a AzureDiskEncryption-bővítmény blokkba. 

Az alábbi példa egy olyan PowerShell-parancsfájlt használ, amely inicializálja és formázza a Windows lemezét, majd ezt követi a "AzureDiskEncryption":

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Ha azt szeretné, hogy a Azure Disk Encryption egy másik bővítmény előtt kelljen alkalmazni, helyezze a `provisionAfterExtensions` tulajdonságot a bővítmény blokkjában a következőre:.

Íme egy példa a "AzureDiskEncryption" kifejezéssel, amelyet a "VMDiagnosticsSettings" követ, amely egy Windows-alapú Azure virtuális gépen a figyelési és diagnosztikai képességeket nyújtja:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Részletesebb sablon a következő témakörben található:
* A Azure Disk Encryption bővítmény alkalmazása a lemezt formázó egyéni rendszerhéj-parancsfájl után (Linux): [deploy-extseq-linux-ADE-after-customscript.jsbe](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>Következő lépések
- További információ a bővítmények sorrendbe [állításáról: a bővítmények kiosztása a virtuálisgép-méretezési csoportokban](virtual-machine-scale-sets-extension-sequencing.md).
- További információ a `provisionAfterExtensions` tulajdonságról: [Microsoft. számítási virtualMachineScaleSets/bővítmények sablonjának referenciája](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Azure Disk Encryption a virtuálisgép-méretezési csoportokhoz](disk-encryption-overview.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](disk-encryption-cli.md)
- [Virtuálisgép-méretezési csoportok titkosítása a Azure PowerShell használatával](disk-encryption-powershell.md)
- [Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md)
