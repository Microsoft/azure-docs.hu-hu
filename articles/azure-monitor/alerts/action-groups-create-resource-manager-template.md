---
title: Műveleti csoportok létrehozása Resource Manager-sablonokkal
description: Megtudhatja, hogyan hozhat létre műveleti csoportot Azure Resource Manager sablon használatával.
author: dkamstra
services: azure-monitor
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: dukek
ms.subservice: alerts
ms.openlocfilehash: 978372481513f3d68fdc587ccc1148976640bc80
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729130"
---
# <a name="create-an-action-group-with-a-resource-manager-template"></a>Műveleti csoport létrehozása Resource Manager-sablonnal
Ez a cikk bemutatja, hogyan konfigurálhat egy [Azure Resource Manager sablont](../../azure-resource-manager/templates/template-syntax.md) a műveleti csoportok konfigurálásához. A sablonok használatával automatikusan beállíthat olyan műveleti csoportokat, amelyek bizonyos típusú riasztásokban újra felhasználhatók. Ezek a műveleti csoportok biztosítják, hogy az összes megfelelő fél értesítést kapjon, ha riasztást vált ki.

Az alapszintű lépések a következők:

1. Hozzon létre egy sablont JSON-fájlként, amely leírja, hogyan kell létrehozni a műveleti csoportot.

2. Telepítse a sablont [bármely üzembe helyezési módszer](../../azure-resource-manager/templates/deploy-powershell.md)használatával.

## <a name="resource-manager-templates-for-an-action-group"></a>A műveleti csoport Resource Manager-sablonjai

Ha Resource Manager-sablonnal szeretne létrehozni egy műveleti csoportot, létre kell hoznia egy típusú erőforrást `Microsoft.Insights/actionGroups` . Ezután töltse ki az összes kapcsolódó tulajdonságot. A következő két minta sablon hozzon létre egy műveleti csoportot.

Az első sablon azt ismerteti, hogyan lehet Resource Manager-sablont létrehozni egy olyan műveleti csoporthoz, amelyben a művelet-definíciók a sablonban nehezen kódoltak. A második sablon azt ismerteti, hogyan hozható létre olyan sablon, amely a webhook konfigurációs adatait bemeneti paraméterekként veszi igénybe a sablon telepítésekor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2019-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
          {
            "name": "contosoSMS",
            "countryCode": "1",
            "phoneNumber": "5555551212"
          },
          {
            "name": "contosoSMS2",
            "countryCode": "1",
            "phoneNumber": "5555552121"
          }
        ],
        "emailReceivers": [
          {
            "name": "contosoEmail",
            "emailAddress": "devops@contoso.com",
            "useCommonAlertSchema": true

          },
          {
            "name": "contosoEmail2",
            "emailAddress": "devops2@contoso.com",
            "useCommonAlertSchema": true
          }
        ],
        "webhookReceivers": [
          {
            "name": "contosoHook",
            "serviceUri": "http://requestb.in/1bq62iu1",
            "useCommonAlertSchema": true
          },
          {
            "name": "contosoHook2",
            "serviceUri": "http://requestb.in/1bq62iu2",
            "useCommonAlertSchema": true
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name (maximum 12 characters) for the Action group."
      }
    },
    "webhookReceiverName": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service Name."
      }
    },    
    "webhookServiceUri": {
      "type": "string",
      "metadata": {
        "description": "Webhook receiver service URI."
      }
    }    
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2019-03-01",
      "name": "[parameters('actionGroupName')]",
      "location": "Global",
      "properties": {
        "groupShortName": "[parameters('actionGroupShortName')]",
        "enabled": true,
        "smsReceivers": [
        ],
        "emailReceivers": [
        ],
        "webhookReceivers": [
          {
            "name": "[parameters('webhookReceiverName')]",
            "serviceUri": "[parameters('webhookServiceUri')]",
            "useCommonAlertSchema": true
          }
        ]
      }
    }
  ],
  "outputs":{
      "actionGroupResourceId":{
          "type":"string",
          "value":"[resourceId('Microsoft.Insights/actionGroups',parameters('actionGroupName'))]"
      }
  }
}
```


## <a name="next-steps"></a>Következő lépések
* További információ a [műveleti csoportokról](./action-groups.md).
* További információ a [riasztásokról](./alerts-overview.md).
* Megtudhatja, hogyan adhat hozzá [riasztásokat Resource Manager-sablonok használatával](./alerts-activity-log.md).