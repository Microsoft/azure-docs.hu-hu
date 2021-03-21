---
title: Resource Manager-sablon – minták metrikus riasztásokhoz
description: Ez a cikk a Azure Monitorban metrikai riasztások létrehozásához használt Resource Manager-sablonokat ismerteti.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: sample
ms.date: 05/18/2020
ms.openlocfilehash: f41c6c8ab4e5b00c7281a75eeccb09a6e894764e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039410"
---
# <a name="resource-manager-template-samples-for-metric-alert-rules-in-azure-monitor"></a>Resource Manager-sablonok – minták metrikus riasztási szabályokhoz Azure Monitor

Ez a cikk példákat tartalmaz [Azure Resource Manager sablonok](../../azure-resource-manager/templates/template-syntax.md) használatával a [metrikai riasztási szabályok](../alerts/alerts-metric-near-real-time.md) konfigurálásához a Azure monitorban. Mindegyik minta tartalmaz egy sablonfájlt és egy, a sablonhoz adni kívánt minta értékeket tartalmazó paramétereket tartalmazó fájlt.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

A metrikus riasztási szabályokkal használható erőforrások listáját a [Azure monitor metrikai riasztások támogatott erőforrásai](../alerts/alerts-metric-near-real-time.md) című részben tekintheti meg. A riasztási szabályok sémájának és tulajdonságainak magyarázata a [metrikus riasztások – létrehozás vagy frissítés](/rest/api/monitor/metricalerts/createorupdate)lehetőségnél érhető el.

> [!NOTE]
> Erőforrás-sablon metrikai riasztások létrehozásához az erőforrástípus esetében: az Azure Log Analytics munkaterület (azaz) `Microsoft.OperationalInsights/workspaces` további lépéseket igényel. Részletekért lásd: [metrikai riasztás a naplókhoz – erőforrás-sablon](../alerts/alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).



## <a name="template-references"></a>Sablon referenciái

- [Microsoft. metricAlerts](/azure/templates/microsoft.insights/2018-03-01/metricalerts)

## <a name="single-criteria-static-threshold"></a>Egyetlen feltétel, statikus küszöbérték
Az alábbi minta egy metrikai riasztási szabályt hoz létre egyetlen feltétel és egy statikus küszöbérték használatával.

### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "New Metric Alert"
        },
        "alertDescription": {
            "value": "New metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/Microsoft.Compute/virtualMachines/replace-with-resource-name"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "80"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


## <a name="single-criteria-dynamic-threshold"></a>Egyetlen feltétel, dinamikus küszöbérték
Az alábbi minta egy metrikai riasztási szabályt hoz létre egyetlen feltétel és egy dinamikus küszöbérték használatával.

### <a name="template-file"></a>Sablonfájl
Mentse az alábbi JSON-t simpledynamicmetricalert.jsként az útmutató céljára.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "ignoreDataBefore": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Use this option to set the date from which to start learning the metric historical data and calculate the dynamic thresholds (in ISO8601 format, e.g. '2019-12-31T22:00:00Z')."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                "ignoreDataBefore": "[parameters('ignoreDataBefore')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "New Metric Alert with Dynamic Thresholds"
        },
        "alertDescription": {
            "value": "New metric alert with Dynamic Thresholds created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/Microsoft.Compute/virtualMachines/replace-with-resource-name"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "ignoreDataBefore": {
            "value": ""
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```



## <a name="multiple-criteria-static-threshold"></a>Több feltétel, statikus küszöbérték
A metrikai riasztások támogatják a többdimenziós metrikák és a riasztási szabályok legfeljebb 5 feltételének riasztását. Az alábbi minta egy metrikus riasztási szabályt hoz létre a dimenziós mérőszámokra vonatkozóan, és több feltételt határoz meg.

A következő korlátozások érvényesek, ha több feltételt tartalmazó riasztási szabályban használ dimenziókat:
- Minden egyes feltételhez csak egy értéket lehet kijelölni.
- A "" nem használható \* dimenzió értékként.
- Ha a különböző feltételekben konfigurált mérőszámok ugyanazt a dimenziót támogatják, akkor egy konfigurált dimenzió értékét explicit módon kell beállítani a vonatkozó feltételekben szereplő összes metrika esetében.
    - Az alábbi példában, mivel mind a **tranzakciók** , mind a **SuccessE2ELatency** metrikája **ApiName** dimenzióval rendelkezik, és a *criterion1* meghatározza a **ApiName** dimenzió *"GetBlob"* értékét, akkor a *criterion2* **a GetBlob** dimenzió *"ApiName"* értékét is be kell állítania.

### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion1":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "criterion2": {
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criterion1": "[array(parameters('criterion1'))]",
        "criterion2": "[array(parameters('criterion2'))]",
        "criteria": "[concat(variables('criterion1'),variables('criterion2'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "New Multi-dimensional Metric Alert (Replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template (Replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion1": {
            "value": {
                    "name": "1st criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Include",
                            "values": ["Success"]
                        },
                        {
                            "name":"ApiName",
                            "operator": "Include",
                            "values": ["GetBlob"]
                        }
                    ],
                    "operator": "GreaterThan",
                    "threshold": "5",
                    "timeAggregation": "Total"
                }
        },
        "criterion2": {
            "value":{
                "name": "2nd criterion",
                "metricName": "SuccessE2ELatency",
                "dimensions": [
                    {
                        "name":"ApiName",
                        "operator": "Include",
                        "values": ["GetBlob"]
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "250",
                "timeAggregation": "Average"
            }
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }
}
```


## <a name="multiple-dimensions-static-threshold"></a>Több dimenzió, statikus küszöbérték
Egyetlen riasztási szabály egyszerre több metrikai idősorozat figyelésére is képes, így kevesebb riasztási szabályt kell kezelni. Az alábbi minta egy statikus metrikai riasztási szabályt hoz létre a dimenziós metrikák esetében.

Ebben a mintában a riasztási szabály a **ResponseType** és a **ApiName** dimenzió értékeit figyeli a **tranzakciós** metrika esetében:
1. **ResponsType** – a " \* " helyettesítő karakter azt jelenti, hogy a **ResponseType** dimenzió minden értékéhez, beleértve a jövőbeli értékeket is, külön idősorozatot kell figyelni.
2. **ApiName** – a rendszer csak a **GetBlob** és a **PutBlob** dimenzió értékeit figyeli.

Például a riasztási szabály által figyelt egyes lehetséges idősorozatok:
- Metrika = *tranzakciók*, ResponseType = *sikeres*, ApiName = *GetBlob*
- Metrika = *tranzakciók*, ResponseType = *sikeres*, ApiName = *PutBlob*
- Metrika = *tranzakciók*, ResponseType = *kiszolgáló időkorlátja*, ApiName = *GetBlob*
- Metrika = *tranzakciók*, ResponseType = *kiszolgáló időkorlátja*, ApiName = *PutBlob*

### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator. The alert rule fires when ALL criteria are met"
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criteria": "[array(parameters('criterion'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "New multi-dimensional metric alert rule (replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert rule created via template (replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion": {
            "value": {
                    "name": "Criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Include",
                            "values": ["*"]
                        },
                        {
                            "name":"ApiName",
                            "operator": "Include",
                            "values": ["GetBlob", "PutBlob"]    
                        }
                    ],
                    "operator": "GreaterThan",
                    "threshold": "5",
                    "timeAggregation": "Total"
                }
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }
}
```



## <a name="multiple-dimensions-dynamic-thresholds"></a>Több dimenzió, dinamikus küszöbértékek
Egyetlen dinamikus küszöbértékek riasztási szabálya képes egyéni küszöbértékeket létrehozni több száz metrikus idősorozathoz (akár különböző típusokhoz is), így kevesebb riasztási szabályt kell kezelni. Az alábbi minta egy dinamikus küszöbértékek mérőszámának riasztási szabályát hozza létre a dimenziós metrikák esetében.


Ebben a mintában a riasztási szabály a **ResponseType** és a **ApiName** dimenzió értékeit figyeli a **tranzakciós** metrika esetében:
1. **ResponsType** – a **ResponseType** dimenzió minden értékéhez, beleértve a jövőbeli értékeket, egy másik idősorozatot külön kell figyelni.
2. **ApiName** – a rendszer csak a **GetBlob** és a **PutBlob** dimenzió értékeit figyeli.

Például a riasztási szabály által figyelt egyes lehetséges idősorozatok:
- Metrika = *tranzakciók*, ResponseType = *sikeres*, ApiName = *GetBlob*
- Metrika = *tranzakciók*, ResponseType = *sikeres*, ApiName = *PutBlob*
- Metrika = *tranzakciók*, ResponseType = *kiszolgáló időkorlátja*, ApiName = *GetBlob*
- Metrika = *tranzakciók*, ResponseType = *kiszolgáló időkorlátja*, ApiName = *PutBlob*

>[!NOTE]
> A dinamikus küszöbértékeket használó metrikus riasztási szabályok esetében jelenleg nem támogatott több feltétel.


### <a name="template-file"></a>Sablonfájl


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "Resource ID of the resource emitting the metric that will be used for the comparison."
            }
        },
        "criterion":{
            "type": "object",
            "metadata": {
                "description": "Criterion includes metric name, dimension values, threshold and an operator."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": { 
        "criteria": "[array(parameters('criterion'))]"
     },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": "[variables('criteria')]"
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "New Multi-dimensional Metric Alert with Dynamic Thresholds (Replace with your alert name)"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert with Dynamic Thresholds created via template (Replace with your alert description)"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourcegroup-name/providers/Microsoft.Storage/storageAccounts/replace-with-storage-account"
        },
        "criterion": {
            "value": {
                    "criterionType": "DynamicThresholdCriterion",
                    "name": "1st criterion",
                    "metricName": "Transactions",
                    "dimensions": [
                        {
                            "name":"ResponseType",
                            "operator": "Include",
                            "values": ["*"]
                        },
                        {
                            "name":"ApiName",
                            "operator": "Include",
                            "values": ["GetBlob", "PutBlob"]
                        }
                    ],
                    "operator": "GreaterOrLessThan",
                    "alertSensitivity": "Medium",
                    "failingPeriods": {
                        "numberOfEvaluationPeriods": "4",
                        "minFailingPeriodsToAlert": "3"
                    },
                    "timeAggregation": "Total"
                }
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-actiongroup-name"
        }
    }
}
```



## <a name="custom-metric-static-threshold"></a>Egyéni metrika, statikus küszöbérték

Az alábbi sablonnal speciális statikus küszöbértéket tartalmazó metrikai szabályt hozhat létre egyéni metrika esetén.

Ha többet szeretne megtudni az Azure Monitor lévő egyéni metrikákkal kapcsolatban, tekintse meg [a Azure monitor egyéni metrikáit](../essentials/metrics-custom-overview.md).

Ha egyéni metrika esetén riasztási szabályt hoz létre, meg kell adnia a metrika nevét és a metrikai névteret is. Győződjön meg arról is, hogy az egyéni metrika már be van jelentkezve, mivel nem hozható létre riasztási szabály olyan egyéni metrika esetében, amely még nem létezik.

### <a name="template-file"></a>Sablonfájl

Mentse az alábbi JSON-t customstaticmetricalert.jsként az útmutató céljára.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "metricNamespace": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Namespace of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "How often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "metricNamespace": "[parameters('metricNamespace')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "New alert rule on a custom metric"
        },
        "alertDescription": {
            "value": "New alert rule on a custom metric created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/microsoft.insights/components/replace-with-application-insights-resource-name"
        },
        "metricName": {
            "value": "The custom metric name"
        },
        "metricNamespace": {
            "value": "Azure.ApplicationInsights"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "80"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group"
        }
    }
}
```


>[!NOTE]
>
> Egy adott egyéni metrika metrikai névterét megkeresheti az [Egyéni metrikák a Azure Portal használatával történő tallózásával](../essentials/metrics-custom-overview.md#browse-your-custom-metrics-via-the-azure-portal) .


## <a name="multiple-resources"></a>Több erőforrás

Azure Monitor támogatja ugyanazon típus több erőforrásának figyelését egyetlen metrikai riasztási szabállyal, az azonos Azure-régióban található erőforrások esetében. Ez a funkció jelenleg csak az Azure nyilvános felhőben támogatott, csak virtuális gépek, SQL Server-adatbázisok, SQL Server rugalmas készletek és Data Box Edge eszközök esetében. Ez a funkció csak a platform metrikái esetében érhető el, és az egyéni metrikák esetében nem támogatott.

A dinamikus küszöbértékek riasztási szabálya segítséget nyújt a testre szabott küszöbértékek létrehozásához egyszerre több száz metrikus adatsorozathoz (akár különböző típusokhoz is), így kevesebb riasztási szabályt kell kezelni.

Ebből a szakaszból megtudhatja, hogy három forgatókönyv esetén hogyan figyelheti meg a több erőforrás egyetlen szabállyal való figyelésére Azure Resource Manager sablonokat.

- Egy vagy több erőforráscsoport összes virtuális gép (egy Azure-régióban) figyelése.
- Egy előfizetésben lévő összes virtuális gép (egy Azure-régióban) figyelése.
- A virtuális gépek listájának figyelése (egy Azure-régióban) egy előfizetésben.

> [!NOTE]
>
> A több erőforrást figyelő metrikai riasztási szabályban csak egy feltétel engedélyezett.

### <a name="static-threshold-alert-on-all-virtual-machines-in-one-or-more-resource-groups"></a>Statikus küszöbérték riasztása egy vagy több erőforráscsoport összes virtuális gépen

Ez a sablon egy statikus küszöbérték-metrikai riasztási szabályt hoz létre, amely egy vagy több erőforráscsoport esetében az összes virtuális gép százalékos PROCESSZORát figyeli (egy Azure-régióban).

Mentse az alábbi JSON-t all-vms-in-resource-group-static.js-ként az útmutató céljára.

### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceGroup":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "Full path of the resource group(s) where target resources to be monitored are in. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceGroup')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "Multi-resource metric alert via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceGroup":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```



### <a name="dynamic-thresholds-alert-on-all-virtual-machines-in-one-or-more-resource-groups"></a>Dinamikus küszöbértékek riasztása egy vagy több erőforráscsoport összes virtuális gépen
Ez a minta egy dinamikus küszöbértékek metrikai riasztási szabályt hoz létre, amely egy vagy több erőforráscsoport egy Azure-régiójában lévő összes virtuális gép százalékos PROCESSZORát figyeli.

### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceGroup":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "Full path of the resource group(s) where target resources to be monitored are in. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceGroup')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "Multi-resource metric alert with Dynamic Thresholds via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert with Dynamic Thresholds created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceGroup":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```


### <a name="static-threshold-alert-on-all-virtual-machines-in-a-subscription"></a>Statikus küszöbérték riasztása egy előfizetésben lévő összes virtuális gépen
Ez a minta egy statikus küszöbérték-metrikai riasztási szabályt hoz létre, amely egy előfizetésben egy Azure-régióban lévő összes virtuális gép százalékos PROCESSZORát figyeli.

### <a name="template-file"></a>Sablonfájl

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetSubscription":{
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Azure Resource Manager path up to subscription ID. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('targetSubscription')]"],
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "Multi-resource sub level metric alert via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource sub level metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetSubscription":{
            "value": "/subscriptions/replace-with-subscription-id"
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },        
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```

### <a name="dynamic-thresholds-alert-on-all-virtual-machines-in-a-subscription"></a>Dinamikus küszöbértékek riasztása egy előfizetésben lévő összes virtuális gépen
Ez a minta egy dinamikus küszöbértékek metrikai riasztási szabályt hoz létre, amely figyeli az előfizetésben lévő összes virtuális gép százalékos PROCESSZORát (egy Azure-régióban).


```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetSubscription":{
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Azure Resource Manager path up to subscription ID. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('targetSubscription')]"],
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "Multi-resource sub level metric alert with Dynamic Thresholds via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource sub level metric alert with Dynamic Thresholds created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetSubscription":{
            "value": "/subscriptions/replace-with-subscription-id"
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```


### <a name="static-threshold-alert-on-a-list-of-virtual-machines"></a>Statikus küszöbérték riasztása a virtuális gépek listájáról
Ez a példa egy statikus küszöbérték-metrikai riasztási szabályt hoz létre, amely figyeli a százalékos CPU-t az előfizetés egyik Azure-régiójában található virtuális gépek listájához.

### <a name="parameter-file"></a>Paraméter fájlja
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceId":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "array of Azure resource Ids. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroup/resource-group-name/Microsoft.compute/virtualMachines/vm-name"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H",
                "PT6H",
                "PT12H",
                "PT24H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "allowedValues": [
                "PT1M",
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceId')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "Multi-resource metric alert by list via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert by list created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceId":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1/Microsoft.Compute/virtualMachines/replace-with-vm-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2/Microsoft.Compute/virtualMachines/replace-with-vm-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterThan"
        },
        "threshold": {
            "value": "0"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```


### <a name="dynamic-thresholds-alert-on-a-list-of-virtual-machines"></a>Dinamikus küszöbértékek riasztása a virtuális gépek listájáról
Ez a minta egy dinamikus küszöbértékek metrikai riasztási szabályt hoz létre, amely figyeli a százalékos CPU-t egy előfizetés egyik Azure-régiójában található virtuális gépek listájához.

### <a name="parameter-file"></a>Paraméter fájlja

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "targetResourceId":{
            "type": "array",
            "minLength": 1,
            "metadata": {
                "description": "array of Azure resource Ids. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroup/resource-group-name/Microsoft.compute/virtualMachines/vm-name"
            }
        },
        "targetResourceRegion":{
            "type": "string",
            "allowedValues": [
                "EastUS",
                "EastUS2",
                "CentralUS",
                "NorthCentralUS",
                "SouthCentralUS",
                "WestCentralUS",
                "WestUS",
                "WestUS2",
                "CanadaEast",
                "CanadaCentral",
                "BrazilSouth",
                "NorthEurope",
                "WestEurope",
                "FranceCentral",
                "FranceSouth",
                "UKWest",
                "UKSouth",
                "GermanyCentral",
                "GermanyNortheast",
                "GermanyNorth",
                "GermanyWestCentral",
                "SwitzerlandNorth",
                "SwitzerlandWest",
                "NorwayEast",
                "NorwayWest",
                "SoutheastAsia",
                "EastAsia",
                "AustraliaEast",
                "AustraliaSoutheast",
                "AustraliaCentral",
                "AustraliaCentral2",
                "ChinaEast",
                "ChinaNorth",
                "ChinaEast2",
                "ChinaNorth2",
                "CentralIndia",
                "WestIndia",
                "SouthIndia",
                "JapanEast",
                "JapanWest",
                "KoreaCentral",
                "KoreaSouth",
                "SouthAfricaWest",
                "SouthAfricaNorth",
                "UAECentral",
                "UAENorth"
            ],
            "metadata": {
                "description": "Azure region in which target resources to be monitored are in (without spaces). For example: EastUS"
            }
        },
        "targetResourceType": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Resource type of target resources to be monitored. Currently only supported resource type is Microsoft.Compute/virtualMachines"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total",
                "Count"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
             "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one hour. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT5M",
             "allowedValues": [
                "PT5M",
                "PT15M",
                "PT30M",
                "PT1H"
            ],
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {  },
    "resources": [
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": "[parameters('targetResourceId')]",
                "targetResourceType": "[parameters('targetResourceType')]",
                "targetResourceRegion": "[parameters('targetResourceRegion')]",
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
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
        "alertName": {
            "value": "Multi-resource metric alert with Dynamic Thresholds by list via Azure Resource Manager template"
        },
        "alertDescription": {
            "value": "New Multi-resource metric alert with Dynamic Thresholds by list created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "targetResourceId":{
            "value": [
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name1/Microsoft.Compute/virtualMachines/replace-with-vm-name1",
                "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name2/Microsoft.Compute/virtualMachines/replace-with-vm-name2"
            ]
        },
        "targetResourceRegion":{
            "value": "SouthCentralUS"
        },
        "targetResourceType":{
            "value": "Microsoft.Compute/virtualMachines"
        },
        "metricName": {
            "value": "Percentage CPU"
        },
        "operator": {
          "value": "GreaterOrLessThan"
        },
        "alertSensitivity": {
            "value": "Medium"
        },
        "numberOfEvaluationPeriods": {
            "value": "4"
        },
        "minFailingPeriodsToAlert": {
            "value": "3"
        },
        "timeAggregation": {
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resource-group-name/providers/Microsoft.Insights/actionGroups/replace-with-action-group-name"
        }
    }
}
```


## <a name="availability-test-with-metric-alert"></a>Rendelkezésre állási teszt metrikus riasztással
[Application Insights rendelkezésre állási tesztek](../app/monitor-web-app-availability.md) segítségével figyelheti a webhely vagy alkalmazás elérhetőségét a világ különböző helyeiről. A rendelkezésre állási teszt riasztásai értesítik, ha a rendelkezésre állási tesztek bizonyos számú helyről sikertelenek. A rendelkezésre állási tesztekkel kapcsolatos riasztások ugyanazzal az erőforrással, mint a metrikai riasztások (Microsoft. bepillantások/metricAlerts). Az alábbi minta egy egyszerű rendelkezésre állási tesztet és egy hozzá tartozó riasztást hoz létre.

> [!NOTE]
> `&amp`; a & HTML-entitásának hivatkozása. Az URL-paramétereket még mindig egyetlen & választja el, de ha HTML-ben említi az URL-címet, akkor kódolni kell. Tehát ha a pingURL paraméter értéke "&", el kell menekülnie a következővel: " `&amp` ;"

### <a name="template-file"></a>Sablonfájl

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appName": {
      "type": "string"
    },
    "pingURL": {
      "type": "string"
    },
    "pingText": {
      "type": "string",
      "defaultValue": ""
    },
    "actionGroupId": {
      "type": "string"
    },
    "location": {
      "type": "string"
    }
  },
  "variables": {
    "pingTestName": "[concat('PingTest-', toLower(parameters('appName')))]",
    "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
  },
  "resources": [
    {
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('location')]",
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 300,
        "Timeout": 120,
        "Kind": "ping",
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('pingURL'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },
    {
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/metricAlerts",
      "apiVersion": "2018-03-01",
      "location": "global",
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "description": "Alert for web test",
        "severity": 1,
        "enabled": true,
        "scopes": [
          "[resourceId('Microsoft.Insights/webtests',variables('pingTestName'))]",
          "[resourceId('Microsoft.Insights/components',parameters('appName'))]"
        ],
        "evaluationFrequency": "PT1M",
        "windowSize": "PT5M",
        "templateType": 0,
        "criteria": {
          "odata.type": "Microsoft.Azure.Monitor.WebtestLocationAvailabilityCriteria",
          "webTestId": "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]",
          "componentId": "[resourceId('Microsoft.Insights/components', parameters('appName'))]",
          "failedLocationCount": 2
        },
        "actions": [
          {
            "actionGroupId": "[parameters('actionGroupId')]"
          }
        ]
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
        "appName": {
            "value": "Replace with your Application Insights resource name"
        },
        "pingURL": {
            "value": "https://www.yoursite.com"
        },
        "actionGroupId": {
            "value": "/subscriptions/replace-with-subscription-id/resourceGroups/replace-with-resourceGroup-name/providers/microsoft.insights/actiongroups/replace-with-action-group-name"
        },
        "location": {
            "value": "Replace with the location of your Application Insights resource"
        },
        "pingText": {
            "defaultValue": "Optional parameter that allows you to perform a content-match for the presence of a specific string within the content returned from a pingURL response",
            "type": "String"
        },
    }
}
```

A Content-Match paraméter további konfigurációja a `pingText` sablonfájl részét képezi `Configuration/Webtest` . Konkrétan az alábbi szakaszt:

```xml
<RuleParameter Name=\"FindText\" Value=\"',parameters('pingText'), '\" />
<RuleParameter Name=\"IgnoreCase\" Value=\"False\" />
<RuleParameter Name=\"UseRegularExpression\" Value=\"False\" /> 
<RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />
```
### <a name="test-locations"></a>Tesztelési helyszínek

|Id                  | Region           |
|:-------------------|:-----------------|
| `emea-nl-ams-azr`  | Nyugat-Európa      |
| `us-ca-sjc-azr`    | USA nyugati régiója          |
| `emea-ru-msa-edge` | Az Egyesült Királyság déli régiója         |
| `emea-se-sto-edge` | Az Egyesült Királyság nyugati régiója          |
| `apac-sg-sin-azr`  | Délkelet-Ázsia   |
| `us-tx-sn1-azr`    | USA déli középső régiója |
| `us-il-ch1-azr`    | USA északi középső régiója |
| `emea-gb-db3-azr`  | Észak-Európa     |
| `apac-jp-kaw-edge` | Kelet-Japán       |
| `emea-fr-pra-edge` | Közép-Franciaország   |
| `emea-ch-zrh-edge` | Dél-Franciaország     |
| `us-va-ash-azr`    | USA keleti régiója          |
| `apac-hk-hkn-azr`  | Kelet-Ázsia        |
| `us-fl-mia-edge`   | Az USA középső régiója       |
| `latam-br-gru-edge`| Dél-Brazília      |
| `emea-au-syd-edge` | Kelet-Ausztrália   |

### <a name="us-government-test-locations"></a>Egyesült államokbeli kormányzati tesztelési helyszínek

|Id                    | Region           |
|----------------------|------------------|
| `usgov-va-azr`       | `USGov Virginia` |
| `usgov-phx-azr`      | `USGov Arizona`  |
| `usgov-tx-azr`       | `USGov Texas`    |
| `usgov-ddeast-azr`   | `USDoD East`     |
| `usgov-ddcentral-azr`| `USDoD Central`  |

## <a name="next-steps"></a>Következő lépések

- [További Azure monitor-sablonok beszerzése](../resource-manager-samples.md).
- [További információ a riasztásokról](./alerts-overview.md).
- [Minta beszerzése egy műveleti csoport létrehozásához Resource Manager-sablonnal](resource-manager-action-groups.md)