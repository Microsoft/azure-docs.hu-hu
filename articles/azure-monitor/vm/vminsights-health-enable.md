---
title: A virtuális gépek bepillantást tevő vendég állapotának engedélyezése (előzetes verzió)
description: Ismerteti, hogyan lehet engedélyezni a virtuális gépek beszerzését az előfizetésben, és hogyan kell a virtuális gépeket bevezetni.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/16/2020
ms.custom: references_regions
ms.openlocfilehash: 540f0725427ace4aec76de373556cd6699a1b229
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732754"
---
# <a name="enable-vm-insights-guest-health-preview"></a>A virtuális gépek bepillantást tevő vendég állapotának engedélyezése (előzetes verzió)
A virtuális gép elemzése lehetővé teszi a virtuális gépek állapotának megtekintését olyan teljesítmény-mérési készlet alapján, amely rendszeres időközönként mintavételt végez. Ez a cikk bemutatja, hogyan engedélyezheti ezt a funkciót az előfizetésében, és hogyan engedélyezheti a vendég figyelését az egyes virtuális gépeken.

## <a name="current-limitations"></a>Aktuális korlátozások
A virtuálisgép-megállapítások vendég állapota a nyilvános előzetes verzióban a következő korlátozásokkal rendelkezik:

- Jelenleg csak az Azure-beli virtuális gépek támogatottak. A kiszolgálói Azure Arc jelenleg nem támogatott.
- A hálózati proxyk jelenleg nem támogatottak.


## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
A virtuális gépnek az alábbi operációs rendszerek egyikét kell futtatnia: 

  - Ubuntu 16,04 LTS, Ubuntu 18,04 LTS
  - Windows Server 2012 vagy újabb

## <a name="supported-regions"></a>Támogatott régiók

A virtuális gépnek az alábbi régiók egyikében kell elhelyezkednie:

- Ausztrália középső régiója
- Kelet-Ausztrália
- Délkelet-Ausztrália
- Közép-Kanada
- Közép-India
- Az USA középső régiója
- Kelet-Ázsia
- USA keleti régiója
- USA 2. keleti régiója
- USA 2. keleti – EUAP
- Közép-Franciaország
- Középnyugat-Németország
- Kelet-Japán
- Dél-Korea középső régiója
- USA északi középső régiója
- Észak-Európa
- USA déli középső régiója
- Dél-Afrika északi régiója
- Délkelet-Ázsia
- Észak-Svájc
- Az Egyesült Királyság déli régiója
- Az Egyesült Királyság nyugati régiója
- USA nyugati középső régiója
- Nyugat-Európa
- USA nyugati régiója
- USA 2. nyugati régiója


Log Analytics munkaterület a következő régiók egyikében kell, hogy legyen:

- Ausztrália középső régiója
- Kelet-Ausztrália
- Délkelet-Ausztrália
- Közép-Kanada
- Kanada, India
- Az USA középső régiója
- Kelet-Ázsia
- USA keleti régiója
- USA 2. keleti régiója
- USA 2. keleti – EUAP
- Közép-Franciaország
- Kelet-Japán
- USA északi középső régiója
- Észak-Európa
- USA déli középső régiója
- Délkelet-Ázsia
- Észak-Svájc
- Az Egyesült Királyság déli régiója
- Nyugat-európai régió
- USA nyugati régiója
- USA 2. nyugati régiója

## <a name="prerequisites"></a>Előfeltételek

- A virtuális gépet a VM-eredményekbe kell bevezetni.
- A bevezetési lépéseket végrehajtó felhasználónak legalább közreműködői szintű hozzáféréssel kell rendelkeznie ahhoz az előfizetéshez, amelyben a virtuális gép és az adatgyűjtési szabály található.
- A szükséges Azure-erőforrás-szolgáltatókat a következő szakaszban leírtak szerint kell regisztrálni.

## <a name="register-required-azure-resource-providers"></a>A szükséges Azure-erőforrás-szolgáltatók regisztrálása
A következő Azure-erőforrás-szolgáltatók regisztrálva lesznek az előfizetéséhez, hogy lehetővé tegyék a virtuális gépek bepillantást a vendég állapotára. 

- Microsoft. WorkloadMonitor
- Microsoft. bepillantások

Az erőforrás-szolgáltatót az [Azure Resource Providers és types](../../azure-resource-manager/management/resource-providers-and-types.md)című témakörben leírtak szerint bármelyik elérhető módszer használatával regisztrálhatja. A következő minta paranccsal is használhatja a armclient, a Poster vagy egy másik módszert, amellyel a Azure Resource Manager hitelesített hívást hajthat végre:

```
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.WorkloadMonitor/register?api-version=2019-10-01
POST https://management.azure.com/subscriptions/[subscriptionId]/providers/Microsoft.Insights/register?api-version=2019-10-01
```


## <a name="enable-a-virtual-machine-using-the-azure-portal"></a>Virtuális gép engedélyezése az Azure Portalon
Amikor engedélyezi egy virtuális gép vendégállapotát az Azure Portalon, a rendszer elvégzi Ön helyett a szükséges konfigurálásokat. Ide tartozik a szükséges adatgyűjtési szabály létrehozása, a vendég állapot-bővítmény telepítése a virtuális gépen, valamint az adatgyűjtési szabállyal való társítás létrehozása.

A VM-adatok első **lépések** nézetében kattintson a virtuális gép frissítési üzenete melletti hivatkozásra, majd kattintson a **frissítés** gombra. Egyszerre több virtuális gépet is kiválaszthat, és egyszerre frissítheti őket.

![Az állapotfigyelő funkció engedélyezése a virtuális gépen](media/vminsights-health-enable/enable-agent.png)


## <a name="enable-a-virtual-machine-using-resource-manager-template"></a>Virtuális gép engedélyezése Resource Manager-sablonnal
A virtuális gépek Azure Resource Manager használatával történő engedélyezéséhez három lépés szükséges.

- Adatgyűjtési szabály létrehozása.
- A vendég állapot-bővítmény telepítése minden virtuális gépen
- Társítás létrehozása a virtuális gép és az adatgyűjtési szabály között.

### <a name="create-data-collection-rule-dcr"></a>Adatgyűjtési szabály létrehozása (DCR)

> [!NOTE]
> Ha a Azure Portal segítségével engedélyez egy virtuális gépet, az itt ismertetett adatgyűjtési szabályt hozza létre a rendszer. Ebben az esetben nem kell elvégeznie ezt a lépést.

A virtuálisgép-elemzések vendég állapotában lévő figyelők konfigurációját az [adatgyűjtési szabályok (DCR)](../agents/data-collection-rule-overview.md)tárolja. A vendég állapot-kiterjesztésű virtuális gépeknek ehhez a szabályhoz kell társítaniuk.

> [!NOTE]
> További adatgyűjtési szabályokat is létrehozhat a figyelők alapértelmezett konfigurációjának módosításához a következő témakörben ismertetett módon: a [figyelés konfigurálása a VM-elemzések vendég állapota (előzetes verzió)](vminsights-health-configure.md).

A sablonhoz a következő paraméterek értékei szükségesek:

- **defaultHealthDataCollectionRuleName**: megtartja a sablonban definiált alapértelmezett nevet.
- **destinationWorkspaceResourceId**: a virtuális gépek adatgyűjtéséhez használt log Analytics munkaterület erőforrás-azonosítója.
- **dataCollectionRuleLocation**: az adatgyűjtés szabályának régiója. Ennek meg kell egyeznie Log Analytics munkaterület régiójával.


A sablon üzembe helyezése a [Resource Manager-sablonok bármely üzembe helyezési módszerének](../../azure-resource-manager/templates/deploy-powershell.md)használatával. A következő parancsokkal telepítheti a sablont és a paramétereket tartalmazó fájlt a PowerShell vagy az Azure CLI használatával.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDataCollectionRule -ResourceGroupName my-resource-group -TemplateFile Health.DataCollectionRule.template.json -TemplateParameterFile Health.DataCollectionRule.template.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDataCollectionRule --resource-group my-resource-group --template-file Health.DataCollectionRule.template.json --parameters Health.DataCollectionRule.template.parameters.json
```

---

Az alábbi Resource Manager-sablonban meghatározott adatgyűjtési szabály lehetővé teszi a virtuális gépekhez tartozó összes figyelő használatát a vendég Health bővítménnyel. Tartalmaznia kell a figyelők által használt egyes teljesítményszámlálók adatforrásait.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "defaultHealthDataCollectionRuleName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule to create."
      },
      "defaultValue": "Microsoft-VMInsights-Health"
    },
    "destinationWorkspaceResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the Azure resource ID of the Log Analytics workspace to use to store virtual machine health data."
      }
    },
    "dataCollectionRuleLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code in which the data collection rule should be deployed. Examples: eastus, westeurope, etc"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/dataCollectionRules",
      "name": "[parameters('defaultHealthDataCollectionRuleName')]",
      "location": "[parameters('dataCollectionRuleLocation')]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Data collection rule for VM Insights health.",
        "dataSources": {
          "performanceCounters": [
              {
                  "name": "VMHealthPerfCounters",
                  "streams": [ "Microsoft-Perf" ],
                  "scheduledTransferPeriod": "PT1M",
                  "samplingFrequencyInSeconds": 60,
                  "counterSpecifiers": [
                      "\\LogicalDisk(*)\\% Free Space",
                      "\\Memory\\Available Bytes",
                      "\\Processor(_Total)\\% Processor Time"
                  ]
              }
          ],
          "extensions": [
            {
              "name": "Microsoft-VMInsights-Health",
              "streams": [
                "Microsoft-HealthStateChange"
              ],
              "extensionName": "HealthExtension",
              "extensionSettings": {
                "schemaVersion": "1.0",
                "contentVersion": "",
                "healthRuleOverrides": [
                  {
                    "scopes": [ "*" ],
                    "monitors": ["root"],
                    "alertConfiguration": {
                      "isEnabled": true
                    }
                  }
                ]
              },
              "inputDataSources": [
                  "VMHealthPerfCounters"
              ]

            }
          ]
        },
        "destinations": {
          "logAnalytics": [
            {
              "workspaceResourceId": "[parameters('destinationWorkspaceResourceId')]",
              "name": "Microsoft-HealthStateChange-Dest"
            }
          ]
        },                  
        "dataFlows": [
          {
            "streams": [
              "Microsoft-HealthStateChange"
            ],
            "destinations": [
              "Microsoft-HealthStateChange-Dest"
            ]
          }
        ]
      }
    }
  ]
}
```

### <a name="sample-parameter-file"></a>Minta paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "destinationWorkspaceResourceId": {
        "value": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
      },
      "dataCollectionRuleLocation": {
        "value": "eastus"
      }
  }
}
```



### <a name="install-guest-health-extension-and-associate-with-data-collection-rule"></a>A vendég állapot-kiterjesztés telepítése és az adatgyűjtési szabályhoz való hozzárendelés
A következő Resource Manager-sablon használatával engedélyezheti a virtuális gépeket a vendég állapotához. Ezzel telepíti a vendég Health bővítményt, és létrehozza a társítást az adatgyűjtési szabállyal. Ezt a sablont a [Resource Manager-sablonok bármely üzembe helyezési módszerével](../../azure-resource-manager/templates/deploy-powershell.md)üzembe helyezheti.


Például az alábbi parancsokkal telepítheti a sablont és a paramétereket tartalmazó fájlt egy erőforráscsoporthoz a PowerShell vagy az Azure CLI használatával.


# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
New-AzResourceGroupDeployment -Name GuestHealthDeployment -ResourceGroupName my-resource-group -TemplateFile azure-monitor-deploy.json -TemplateParameterFile azure-monitor-deploy.parameters.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create --name GuestHealthDeployment --resource-group my-resource-group --template-file Health.VirtualMachine.template.json --parameters Health.VirtualMachine.template.parameters.json
```

---

### <a name="template-file"></a>Sablonfájl

``` json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "virtualMachineName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the virtual machine."
      }
    },
    "virtualMachineLocation": {
      "type": "string",
      "metadata": {
        "description": "The location code of the virtual machine region (location). Examples: eastus, westeurope, etc"
      }
    },
    "virtualMachineOsType": {
      "type": "string",
      "metadata": {
        "description": "Specifies operating system type of the target virtual machine."
      },
      "allowedValues": ["windows", "linux"]
    },
    "dataCollectionRuleAssociationName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the data collection rule association to create."
      },
      "defaultValue": "VM-Health-Dcr-Association"
    },
    "healthDataCollectionRuleResourceId": {
      "type": "string",
      "metadata": {
        "description": "Specifies resource id of Azure Monitor Data Collection Rule for virtual machine health data."
      }
    }
  },
  "variables": {
    "healthExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthWindowsAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor.VirtualMachines.GuestHealth",
        "type": "GuestHealthLinuxAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true
      }
    },
    "azureMonitorAgentExtensionProperties": {
      "windows": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorWindowsAgent", 
        "typeHandlerVersion": "1.0", 
        "autoUpgradeMinorVersion": false 
      },
      "linux": {
        "publisher": "Microsoft.Azure.Monitor", 
        "type": "AzureMonitorLinuxAgent", 
        "typeHandlerVersion": "1.5", 
        "autoUpgradeMinorVersion": false 
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('virtualMachineName')]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2017-03-30",
      "identity": {
        "type": "SystemAssigned"
      }
    },
    {
      "type": "Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations",
      "name": "[concat(parameters('virtualMachineName'), '/Microsoft.Insights/', parameters('dataCollectionRuleAssociationName'))]",
      "apiVersion": "2019-11-01-preview",
      "properties": {
        "description": "Association of data collection rule for VM Insights Health.",
        "dataCollectionRuleId": "[parameters('healthDataCollectionRuleResourceId')]"
      }
    },
    { 
      "type": "Microsoft.Compute/virtualMachines/extensions", 
      "apiVersion": "2019-12-01", 
      "name": "[concat(parameters('virtualMachineName'), '/', variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ], 
      "properties": "[variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')]]"
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'), '/', variables('healthExtensionProperties')[parameters('virtualMachineOsType')].type)]",
      "location": "[parameters('virtualMachineLocation')]",
      "apiVersion": "2018-06-01",
      "dependsOn": [ 
        "[resourceId('Microsoft.Compute/virtualMachines/extensions', parameters('virtualMachineName'), variables('azureMonitorAgentExtensionProperties')[parameters('virtualMachineOsType')].type)]",
        "[resourceId('Microsoft.Compute/virtualMachines/providers/dataCollectionRuleAssociations', parameters('virtualMachineName'), 'Microsoft.Insights', parameters('dataCollectionRuleAssociationName'))]"
      ],    
      "properties": "[variables('healthExtensionProperties')[parameters('virtualMachineOsType')]]"
    }               
  ]
}
```

### <a name="sample-parameter-file"></a>Minta paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualMachineName": {
        "value": "myvm"
      },
      "virtualMachineLocation": {
        "value": "eastus"
      },
      "virtualMachineOsType": {
        "value": "linux"
      },
      "healthDataCollectionRuleResourceId": {
        "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/my-resource-group/providers/Microsoft.Insights/dataCollectionRules/Microsoft-VMInsights-Health"
      }
  }
}
```

## <a name="next-steps"></a>Következő lépések

- [A VM-alapú adatfelismerések által engedélyezett figyelők testreszabása](vminsights-health-configure.md)
