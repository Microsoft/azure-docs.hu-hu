---
title: Resource Manager-sablonok – minták a műveleti csoportokhoz
description: Példa Azure Resource Manager sablonokra Azure Monitor műveleti csoportok telepítéséhez.
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 12/03/2020
ms.openlocfilehash: 55b56d9eeb2c7b05af86c42d12e82aef6d806b0a
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600960"
---
# <a name="resource-manager-template-samples-for-action-groups-in-azure-monitor"></a>Resource Manager-sablonok – minták a műveleti csoportokhoz a Azure Monitor
Ez a cikk minta- [Azure Resource Manager sablonokat](../../azure-resource-manager/templates/template-syntax.md) tartalmaz a Azure monitorban lévő [műveleti csoportok](../platform/action-groups.md) létrehozásához. Mindegyik minta tartalmaz egy sablonfájlt és egy, a sablonhoz adni kívánt minta értékeket tartalmazó paramétereket tartalmazó fájlt.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="create-an-action-group"></a>Műveleti csoport létrehozása
A következő minta létrehoz egy műveleti csoportot.


### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "actionGroupName": {
      "type": "string",
      "metadata": {
        "description": "Unique name within the resource group for the Action group."
      }
    },
    "actionGroupShortName": {
      "type": "string",
      "metadata": {
        "description": "Short name up to 12 characters for the Action group."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Insights/actionGroups",
      "apiVersion": "2018-03-01",
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

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "actionGroupName": {
        "value": "My Action Group"
      },
      "actionGroupShortName": {
        "value": "mygroup"
      }
  }
}
```



## <a name="next-steps"></a>További lépések

* [További Azure monitor-sablonok beszerzése](resource-manager-samples.md).
* [További információ a műveleti csoportokról](../platform/action-groups.md).

