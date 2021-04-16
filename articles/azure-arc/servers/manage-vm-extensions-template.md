---
title: Virtuálisgép-bővítmény engedélyezése Azure Resource Manager használatával
description: Ez a cikk bemutatja, hogyan helyezhet üzembe virtuálisgép-bővítményeket Azure Arc hibrid felhőkörnyezetekben futó, engedélyezett kiszolgálókon egy Azure Resource Manager sablon használatával.
ms.date: 04/13/2021
ms.topic: conceptual
ms.openlocfilehash: 095f95192a2054d34e438d8683ac9c2e20a824f1
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389638"
---
# <a name="enable-azure-vm-extensions-by-using-arm-template"></a>Azure-beli virtuálisgép-bővítmények engedélyezése ARM-sablonnal

Ez a cikk bemutatja, hogyan használhat Azure Resource Manager sablont (ARM-sablont) azure-beli virtuálisgép Azure Arc bővítmények üzembe helyezéséhez.

A virtuálisgép-bővítmények hozzáadhatóak a Azure Resource Manager sablonhoz, és a sablon üzembe helyezéssel hajthatóak végre. Az Arc-kompatibilis kiszolgálók által támogatott virtuálisgép-bővítményekkel linuxos vagy Windows rendszerű gépeken is üzembe helyezheti a támogatott virtuálisgép-Azure PowerShell. Az alábbi minták tartalmaznak egy sablonfájlt és egy paraméterfájlt, amelyek mintaértékeket tartalmaznak a sablonhoz.

>[!NOTE]
>Bár több bővítmény kötegbe is köteg telepíthető és feldolgozható, a rendszer sorosan telepíti őket. Az első bővítmény telepítésének befejezése után a rendszer megkísérli a következő bővítmény telepítését.

> [!NOTE]
> Azure Arc kompatibilis kiszolgálók nem támogatják a virtuálisgép-bővítmények Azure-beli virtuális gépeken való üzembe helyezését és felügyeletét. Azure-beli virtuális gépekhez tekintse meg a virtuálisgép-bővítmények [áttekintését ismertető cikket.](../../virtual-machines/extensions/overview.md)

## <a name="deploy-the-log-analytics-vm-extension"></a>A Log Analytics virtuálisgép-bővítmény üzembe helyezése

A Log Analytics-ügynök egyszerű üzembe helyezéséhez a következő minta segítségével telepítheti az ügynököt Windows vagy Linux rendszeren.

### <a name="template-file-for-linux"></a>Sablonfájl Linuxhoz

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

### <a name="template-file-for-windows"></a>Sablonfájl Windowshoz

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

### <a name="parameter-file"></a>Paraméterfájl

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

Mentse a sablont és a paraméterfájlokat a lemezre, és szerkessze a paraméterfájlt az üzemelő példány megfelelő értékeivel. Ezután az alábbi paranccsal telepítheti a bővítményt az erőforráscsoportban található összes csatlakoztatott gépre. A parancs a *TemplateFile* paraméterrel adja meg a sablont és a *TemplateParameterFile* paramétert egy paraméter- és paraméterértékeket tartalmazó fájl megadásához.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\LogAnalyticsAgent.json" -TemplateParameterFile "D:\Azure\Templates\LogAnalyticsAgentParms.json"
```

## <a name="deploy-the-custom-script-extension"></a>Az egyéni szkriptbővítmény üzembe helyezése

Az egyéni szkriptbővítmény használata esetén a következő minta futtatását biztosítjuk Windows és Linux rendszeren. Ha nem ismeri az egyéni szkriptbővítményt, lásd: Egyéni szkriptbővítmény [Windows](../../virtual-machines/extensions/custom-script-windows.md) rendszeren vagy Egyéni [szkriptbővítmény Linux rendszeren.](../../virtual-machines/extensions/custom-script-linux.md) A bővítmény hibrid gépekkel való használata esetén érdemes tisztában lennie néhány különböző tulajdonsággal:

* Az Azure-beli virtuális gépek egyéni szkriptbővítménye által támogatott operációs rendszerek listája nem alkalmazható Azure Arc kiszolgálókra. Az Arc-kompatibilis kiszolgálók támogatott operációs rendszereit itt [talál.](agent-overview.md#supported-operating-systems)

* Az Azure-beli Virtual Machine Scale Sets virtuális gépekkel kapcsolatos konfigurációs adatok nem alkalmazhatók.

* Ha a gépeknek kívülről kell letöltenie egy szkriptet, és [](manage-agent.md#update-or-remove-proxy-settings) csak proxykiszolgálón keresztül tudnak kommunikálni, a Csatlakoztatott gép ügynököt úgy kell konfigurálnia, hogy beállítsa a proxykiszolgáló környezeti változóját.

Az egyéni szkriptbővítmény konfigurációja olyan adatokat ad meg, mint a szkript helye és a futtatni szükséges parancs. Ezt a konfigurációt egy Azure Resource Manager adja meg, amely linuxos és windowsos hibrid gépekhez is elérhető alább.

### <a name="template-file-for-linux"></a>Sablonfájl Linuxhoz

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

### <a name="template-file-for-windows"></a>Sablonfájl Windowshoz

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

### <a name="parameter-file"></a>Paraméterfájl

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

## <a name="deploy-the-dependency-agent-extension"></a>A függőségi ügynök bővítmény üzembe helyezése

A függőségi Azure Monitor bővítményének használatával a következő minta futtatható Windows és Linux rendszeren. Ha nem ismeri a függőségi ügynököt, tekintse meg a következőt: Overview of Azure Monitor agents (A függőségi [ügynökök áttekintése).](../../azure-monitor/agents/agents-overview.md#dependency-agent)

### <a name="template-file-for-linux"></a>Sablonfájl Linuxhoz

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

### <a name="template-file-for-windows"></a>Sablonfájl Windowshoz

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

Mentse a sablonfájlt lemezre. Ezután a következő paranccsal telepítheti a bővítményt a csatlakoztatott gépen.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\DependencyAgent.json"
```

## <a name="deploy-azure-key-vault-vm-extension-preview"></a>Virtuálisgép Azure Key Vault bővítmény üzembe helyezése (előzetes verzió)

Az alábbi JSON a virtuálisgép-bővítmény (előzetes verzió Key Vault sémáját mutatja be. A bővítmény nem igényel védett beállításokat – minden beállítása nyilvános információnak minősül. A bővítményhez szükség van a figyelt tanúsítványok listájára, a lekérdezés gyakoriságára és a céltanúsítvány-tárolóra. Ezek konkrétan a következők:

### <a name="template-file-for-linux"></a>Sablonfájl Linuxhoz

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

### <a name="template-file-for-windows"></a>Sablonfájl Windowshoz

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
> A megfigyelt tanúsítványok URL-címének a következő formában kell lennie: `https://myVaultName.vault.azure.net/secrets/myCertName` .
>
> Ennek az az oka, hogy az elérési út visszaadja a teljes tanúsítványt, beleértve a titkos kulcsot is, az `/secrets` elérési út azonban `/certificates` nem. További információt a tanúsítványokról itt talál: Key Vault [tanúsítványok](../../key-vault/general/about-keys-secrets-certificates.md)

### <a name="template-deployment"></a>Sablonalapú telepítés

Mentse a sablonfájlt lemezre. Ezután a következő paranccsal telepítheti a bővítményt a csatlakoztatott gépen.

> [!NOTE]
> A virtuálisgép-bővítményhez rendszer által hozzárendelt identitást kellene hozzárendelni a Key Vaultban való hitelesítéshez. Lásd: How to authenticate to Key Vault using managed identity for Windows and Linux Arc enabled servers (Hitelesítés a felügyelt [identitással](managed-identity-authentication.md) Windows- és Linux Arc-kompatibilis kiszolgálókon).

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\KeyVaultExtension.json"
```

## <a name="deploy-the-azure-defender-integrated-scanner"></a>Az integrált Azure Defender üzembe helyezése

Az integrált Azure Defender bővítményének használatával a következő minta futtatható Windows és Linux rendszeren. Ha nem ismeri az integrált olvasót, [](../../security-center/deploy-vulnerability-assessment-vm.md) tekintse meg a hibrid gépek Azure Defender biztonságirés-felmérési megoldását ismertető témakört.

### <a name="template-file-for-windows"></a>Sablonfájl Windowshoz

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

### <a name="template-file-for-linux"></a>Sablonfájl Linuxhoz

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

Mentse a sablonfájlt lemezre. Ezután a következő paranccsal telepítheti a bővítményt a csatlakoztatott gépen.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "ContosoEngineering" -TemplateFile "D:\Azure\Templates\AzureDefenderScanner.json"
```

## <a name="next-steps"></a>Következő lépések

* A virtuálisgép-bővítményeket a Azure PowerShell [használatával](manage-vm-extensions-powershell.md)helyezheti üzembe, kezelheti és távolíthatja el a [Azure Portal](manage-vm-extensions-portal.md)vagy az [Azure CLI-ről.](manage-vm-extensions-cli.md)

* A hibaelhárítási információkat a Virtuálisgép-bővítmények [hibaelhárítása útmutatóban talál.](troubleshoot-vm-extensions.md)