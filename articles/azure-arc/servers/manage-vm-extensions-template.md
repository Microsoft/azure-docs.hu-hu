---
title: Virtuálisgép-bővítmény engedélyezése Azure Resource Manager sablon használatával
description: Ez a cikk bemutatja, hogyan telepíthet virtuálisgép-bővítményeket hibrid felhőalapú környezetekben futó Azure arc-kompatibilis kiszolgálókra Azure Resource Manager sablon használatával.
ms.date: 03/01/2021
ms.topic: conceptual
ms.openlocfilehash: 88296cd4f410defcaf7db15507ddac42e80cba2d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688263"
---
# <a name="enable-azure-vm-extensions-by-using-arm-template"></a>Azure virtuálisgép-bővítmények engedélyezése ARM-sablon használatával

Ez a cikk bemutatja, hogyan használható egy Azure Resource Manager sablon (ARM-sablon) az Azure-beli virtuálisgép-bővítmények üzembe helyezéséhez az Azure arc-kompatibilis kiszolgálók által támogatott módon.

A virtuálisgép-bővítmények hozzáadhatók egy Azure Resource Manager sablonhoz, és a sablon üzembe helyezésével hajthatók végre. Az arc-kompatibilis kiszolgálók által támogatott virtuálisgép-bővítményekkel a Azure PowerShell használatával telepítheti a támogatott virtuálisgép-bővítményt Linux vagy Windows rendszerű gépekre. Az alábbi példákban egy sablonfájl és egy, a sablonhoz adni kívánt minta értékekkel rendelkező Parameters fájl szerepel.

>[!NOTE]
>Habár több bővítmény kötegelt feldolgozását és feldolgozását is elvégezheti, a rendszer sorosan telepíti őket. Az első bővítmény telepítésének befejezése után a rendszer a következő bővítmény telepítését kísérli meg.

## <a name="deploy-the-log-analytics-vm-extension"></a>A Log Analytics virtuálisgép-bővítmény üzembe helyezése

A Log Analytics-ügynök egyszerű üzembe helyezéséhez a következő minta kerül az ügynök telepítésére Windows vagy Linux rendszeren.

### <a name="template-file-for-linux"></a>A Linux sablon fájlja

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/OMSAgentForLinux')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "OmsAgentForLinux",
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="template-file-for-windows"></a>Sablonfájl a Windowshoz

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "workspaceId": {
            "type": "string"
        },
        "workspaceKey": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/MicrosoftMonitoringAgent')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.EnterpriseCloud.Monitoring",
                "type": "MicrosoftMonitoringAgent",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "workspaceId": "[parameters('workspaceId')]"
                },
                "protectedSettings": {
                    "workspaceKey": "[parameters('workspaceKey')]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "value": "<vmName>"
        },
        "location": {
            "value": "<region>"
        },
        "workspaceId": {
            "value": "<MyWorkspaceID>"
        },
        "workspaceKey": {
            "value": "<MyWorkspaceKey>"
        }
    }
}
```

Mentse a sablont és a paraméter fájljait a lemezre, és szerkessze a paramétert a központi telepítés megfelelő értékeivel. Ezután telepítheti a bővítményt az erőforráscsoport összes csatlakoztatott számítógépén a következő paranccsal. A parancs a *TemplateFile* paramétert használja a sablon és a *TemplateParameterFile* paraméter megadásához, amely paraméterek és paraméterek értékét tartalmazó fájlt ad meg.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgent.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentParms.json"
```

## <a name="deploy-the-custom-script-extension"></a>Az egyéni szkriptek bővítményének üzembe helyezése

Az egyéni szkriptek bővítményének használatához a következő minta a Windows és Linux rendszeren való futtatásra szolgál. Ha nem ismeri az egyéni szkriptek bővítményét, tekintse meg a [Windowshoz készült egyéni szkriptek bővítményét](../../virtual-machines/extensions/custom-script-windows.md) vagy a [Linuxhoz készült egyéni szkriptek bővítményét](../../virtual-machines/extensions/custom-script-linux.md). A bővítmény hibrid gépekkel való használata során több különböző jellemzővel is tisztában kell lennie:

* Az Azure VM Custom script bővítménnyel rendelkező támogatott operációs rendszerek listája nem alkalmazható az Azure arc-kompatibilis kiszolgálókra. Az arc-kompatibilis kiszolgálókhoz támogatott OSs listája [itt](agent-overview.md#supported-operating-systems)található.

* Az Azure Virtual Machine Scale Sets vagy klasszikus virtuális gépekre vonatkozó konfigurációs adatok nem alkalmazhatók.

* Ha a gépeken kívülről kell letöltenie egy parancsfájlt, és csak proxykiszolgálón keresztül tud kommunikálni, akkor [konfigurálnia kell a csatlakoztatott számítógép ügynököt](manage-agent.md#update-or-remove-proxy-settings) a proxykiszolgáló környezeti változójának beállításához.

Az egyéni szkriptek bővítményének konfigurációja meghatározza a parancsfájl helyét és a futtatandó parancsot. Ez a konfiguráció egy Azure Resource Manager sablonban van megadva, amely a Linux és a Windows hibrid gépek esetében is elérhető.

### <a name="template-file-for-linux"></a>A Linux sablon fájlja

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "fileUris": {
      "type": "array"
    },
    "commandToExecute": {
      "type": "securestring"
    }
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/CustomScript')]",
      "type": "Microsoft.HybridCompute/machines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2019-08-02-preview",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "autoUpgradeMinorVersion": true,
        "settings": {},
        "protectedSettings": {
          "commandToExecute": "[parameters('commandToExecute')]",
          "fileUris": "[parameters('fileUris')]"
        }
      }
    }
  ]
}
```

### <a name="template-file-for-windows"></a>Sablonfájl a Windowshoz

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "fileUris": {
            "type": "string"
        },
        "arguments": {
            "type": "securestring",
            "defaultValue": " "
        }
    },
    "variables": {
        "UriFileNamePieces": "[split(parameters('fileUris'), '/')]",
        "firstFileNameString": "[variables('UriFileNamePieces')[sub(length(variables('UriFileNamePieces')), 1)]]",
        "firstFileNameBreakString": "[split(variables('firstFileNameString'), '?')]",
        "firstFileName": "[variables('firstFileNameBreakString')[0]]"
    },
    "resources": [
        {
            "name": "[concat(parameters('vmName'),'/CustomScriptExtension')]",
            "type": "Microsoft.HybridCompute/machines/extensions",
            "location": "[parameters('location')]",
            "apiVersion": "2019-08-02-preview",
            "properties": {
                "publisher": "Microsoft.Compute",
                "type": "CustomScriptExtension",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "fileUris": "[split(parameters('fileUris'), ' ')]"
                },
                "protectedSettings": {
                    "commandToExecute": "[concat ('powershell -ExecutionPolicy Unrestricted -File ', variables('firstFileName'), ' ', parameters('arguments'))]"
                }
            }
        }
    ]
}
```

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "customScriptExt",
        "label": "Add Custom Script Extension",
        "elements": [
          {
            "name": "fileUris",
            "type": "Microsoft.Common.FileUpload",
            "label": "Script files",
            "toolTip": "The script files that will be downloaded to the virtual machine.",
            "constraints": {
              "required": false
            },
            "options": {
              "multiple": true,
              "uploadMode": "url"
            },
            "visible": true
          },
          {
            "name": "commandToExecute",
            "type": "Microsoft.Common.TextBox",
            "label": "Command",
            "defaultValue": "sh script.sh",
            "toolTip": "The command to execute, for example: sh script.sh",
            "constraints": {
              "required": true
            },
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "vmName": "[vmName()]",
      "location": "[location()]",
      "fileUris": "[steps('customScriptExt').fileUris]",
      "commandToExecute": "[steps('customScriptExt').commandToExecute]"
    }
  }
}
```

## <a name="deploy-the-dependency-agent-extension"></a>A függőségi ügynök bővítményének üzembe helyezése

A Azure Monitor függőségi ügynök bővítményének használatához a következő minta a Windows és Linux rendszeren való futtatásra szolgál. Ha nem ismeri a függőségi ügynököt, tekintse meg [a Azure monitor ügynökök áttekintése](../../azure-monitor/agents/agents-overview.md#dependency-agent)című témakört.

### <a name="template-file-for-linux"></a>A Linux sablon fájlja

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Linux machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentLinux",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-file-for-windows"></a>Sablonfájl a Windowshoz

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "metadata": {
        "description": "The name of existing Windows machine."
      }
    }
  },
  "variables": {
    "vmExtensionsApiVersion": "2017-03-30"
  },
  "resources": [
    {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/DAExtension')]",
      "apiVersion": "[variables('vmExtensionsApiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5",
        "autoUpgradeMinorVersion": true
      }
    }
  ],
    "outputs": {
    }
}
```

### <a name="template-deployment"></a>Sablonalapú telepítés

Mentse a sablonfájlt a lemezre. Ezután központilag telepítheti a bővítményt a csatlakoztatott gépre a következő parancs használatával.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\DependencyAgent.json"
```

## <a name="deploy-azure-key-vault-vm-extension-preview"></a>Azure Key Vault VM-bővítmény üzembe helyezése (előzetes verzió)

A következő JSON a Key Vault virtuálisgép-bővítmény (előzetes verzió) sémáját mutatja be. A bővítmény nem igényel védett beállításokat – az összes beállítás nyilvános információnak minősül. A bővítményhez meg kell adni a figyelt tanúsítványok listáját, a lekérdezés gyakoriságát és a célhely tanúsítványtárolóját. Ezek konkrétan a következők:

### <a name="template-file-for-linux"></a>A Linux sablon fájlja

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "autoUpgradeMinorVersion":{
            "type": "bool"
        },
        "pollingIntervalInS":{
          "type": "int"
        },
        "certificateStoreName":{
          "type": "string"
        },
        "certificateStoreLocation":{
          "type": "string"
        },
        "observedCertificates":{
          "type": "string"
        },
        "msiEndpoint":{
          "type": "string"
        },
        "msiClientId":{
          "type": "string"
        }
},
"resources": [
   {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/KVVMExtensionForLinux')]",
      "apiVersion": "2019-12-12",
      "location": "[parameters('location')]",
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <ignored on linux>,
          "certificateStoreLocation": <disk path where certificate is stored, default: "/var/lib/waagent/Microsoft.Azure.KeyVault">,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
          },
          "authenticationSettings": {
                "msiEndpoint":  <MSI endpoint e.g.: "http://localhost:40342/metadata/identity">,
                "msiClientId":  <MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
    }
  }
 ]
}
```

### <a name="template-file-for-windows"></a>Sablonfájl a Windowshoz

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "vmName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "autoUpgradeMinorVersion":{
            "type": "bool"
        },
        "pollingIntervalInS":{
          "type": "int"
        },
        "certificateStoreName":{
          "type": "string"
        },
        "linkOnRenewal":{
          "type": "bool"
        },
        "certificateStoreLocation":{
          "type": "string"
        },
        "requireInitialSync":{
          "type": "bool"
        },
        "observedCertificates":{
          "type": "string"
        },
        "msiEndpoint":{
          "type": "string"
        },
        "msiClientId":{
          "type": "string"
        }
},
"resources": [
   {
      "type": "Microsoft.HybridCompute/machines/extensions",
      "name": "[concat(parameters('vmName'),'/KVVMExtensionForWindows')]",
      "apiVersion": "2019-12-12",
      "location": "[parameters('location')]",
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForWindows",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": "3600",
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Only Windows. This feature ensures s-channel binding when certificate renews, without necessitating a re-deployment.  e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net"
        },
        "authenticationSettings": {
                "msiEndpoint": <MSI endpoint e.g.: "http://localhost:40342/metadata/identity">,
                "msiClientId": <MSI identity e.g.: "c7373ae5-91c2-4165-8ab6-7381d6e75619">
        }
      }
    }
  }
 ]
}
```

> [!NOTE]
> A megfigyelt tanúsítványok URL-címeinek űrlapnak kell lenniük `https://myVaultName.vault.azure.net/secrets/myCertName` .
>
> Ennek az az oka, hogy az `/secrets` elérési út a teljes tanúsítványt adja vissza, beleértve a titkos kulcsot is, míg az `/certificates` elérési út nem. A tanúsítványokkal kapcsolatos további információkért tekintse meg a következőt: [Key Vault tanúsítványok](../../key-vault/general/about-keys-secrets-certificates.md)

### <a name="template-deployment"></a>Sablonalapú telepítés

Mentse a sablonfájlt a lemezre. Ezután központilag telepítheti a bővítményt a csatlakoztatott gépre a következő parancs használatával.

> [!NOTE]
> A virtuálisgép-bővítményhez szükség van egy rendszerhez rendelt identitás hozzárendelésére a Key vaulthoz való hitelesítéshez. Tekintse meg, [Hogyan lehet hitelesíteni a Key Vault felügyelt identitás használatával](managed-identity-authentication.md) Windows-és Linux-alapú arc-kiszolgálókon.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\KeyVaultExtension.json"
```

## <a name="deploy-the-azure-defender-integrated-scanner"></a>Az Azure Defender integrált képolvasó üzembe helyezése

Az Azure Defender integrált képolvasó bővítményének használatához a következő minta a Windows és Linux rendszeren való futtatásra szolgál. Ha nem ismeri az integrált képolvasót, tekintse meg a hibrid gépekhez [készült Azure Defender sebezhetőség-felmérési megoldásának áttekintését](../../security-center/deploy-vulnerability-assessment-vm.md) .

### <a name="template-file-for-windows"></a>Sablonfájl a Windowshoz

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/WindowsAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-file-for-linux"></a>A Linux sablon fájlja

```json
{
  "properties": {
    "mode": "Incremental",
    "template": {
      "contentVersion": "1.0.0.0",
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "parameters": {
        "vmName": {
          "type": "string"
        },
        "apiVersionByEnv": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "resourceType/providers/LinuxAgent.AzureSecurityCenter",
          "name": "[concat(parameters('vmName'), '/Microsoft.Security/default')]",
          "apiVersion": "[parameters('apiVersionByEnv')]"
        }
      ]
    },
    "parameters": {
      "vmName": {
        "value": "resourceName"
      },
      "apiVersionByEnv": {
        "value": "2015-06-01-preview"
      }
    }
  }
}
```

### <a name="template-deployment"></a>Sablonalapú telepítés

Mentse a sablonfájlt a lemezre. Ezután központilag telepítheti a bővítményt a csatlakoztatott gépre a következő parancs használatával.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\AzureDefenderScanner.json"
```

## <a name="next-steps"></a>Következő lépések

* A virtuálisgép-bővítmények üzembe helyezése, kezelése és eltávolítása a [Azure PowerShell](manage-vm-extensions-powershell.md), a [Azure Portal](manage-vm-extensions-portal.md)vagy az [Azure CLI](manage-vm-extensions-cli.md)használatával végezhető el.

* A hibaelhárítási információk a virtuálisgép- [bővítmények hibaelhárítási útmutatójában](troubleshoot-vm-extensions.md)találhatók.