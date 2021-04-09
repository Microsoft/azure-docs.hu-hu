---
title: Resource Manager-sablonok – minták az Azure függvényalkalmazás + Application Insights erőforrásaihoz
description: Példa Azure Resource Manager sablonokra az Azure-függvényalkalmazás Application Insights-erőforrással való üzembe helyezéséhez.
ms.subservice: application-insights
ms.topic: sample
author: lgayhardt
ms.author: lagayhar
ms.date: 08/06/2020
ms.openlocfilehash: 85829052f2e9179b0a721efce720763f82d6402c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100627853"
---
# <a name="resource-manager-template-sample-for-creating-azure-function-apps-with-application-insights-monitoring"></a>Resource Manager-sablon minta Azure Function-alkalmazások létrehozásához Application Insights figyeléssel

Ez a cikk minta [Azure Resource Manager sablonokat](../../azure-resource-manager/templates/template-syntax.md) tartalmaz a [klasszikus Application Insights erőforrások](../app/create-new-resource.md) üzembe helyezéséhez és konfigurálásához az Azure Function alkalmazással együtt. A minta tartalmaz egy sablonfájl és egy, a sablon számára elérhető minta értékeket tartalmazó paramétereket tartalmazó fájlt.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="azure-function-app"></a>Azure-függvényalkalmazás

Az alábbi minta egy .NET Core 3,1 Azure Function-alkalmazást hoz létre, amely egy Windows App Service-csomagban fut, és egy [klasszikus Application Insights erőforrás](../app/create-new-resource.md) , amelyen engedélyezve van a figyelés. 

### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "kind": "functionapp",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/function-app-01",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "FUNCTIONS_EXTENSION_VERSION",
                            "value": "~3"
                        },
                        {
                            "name": "FUNCTIONS_WORKER_RUNTIME",
                            "value": "dotnet"
                        },
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/function-app-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/function-app-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "AzureWebJobsStorage",
                            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
                        }
                    ],
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": true
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "function-app-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": {},
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        },
        {
            "apiVersion": "2019-06-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "tags": {},
            "sku": {
                "name": "Standard_LRS"
            },
            "properties": {
                "supportsHttpsTrafficOnly": true
            }
        }
    ]
}
```

### <a name="parameters-file"></a>Parameters fájl

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "function-app-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "WebApplication2720191003010920Plan"
    },
    "serverFarmResourceGroup": {
      "value": "Testwebapp01"
    },
    "alwaysOn": {
      "value": true
    },
    "storageAccountName": {
      "value": "storageaccountest93"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések

* [További Azure monitor-sablonok beszerzése](../resource-manager-samples.md).
* [További információ a klasszikus Application Insights erőforrásokról](../app/create-new-resource.md).
