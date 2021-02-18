---
title: Metrikai riasztások létrehozása a naplókhoz Azure Monitor
description: Útmutató a közel valós idejű metrikai riasztások létrehozásához a népszerű log Analytics-adatokon.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 06/17/2020
ms.subservice: alerts
ms.openlocfilehash: 7c7bb9df281a37ded51e3a35b90064d875cfd729
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614698"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Metrikai riasztások létrehozása a naplókhoz Azure Monitor

## <a name="overview"></a>Áttekintés

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor támogatja a [metrikus riasztási típust](./alerts-metric-near-real-time.md) , amely a [klasszikus riasztások](./alerts-classic-portal.md)előnyeivel rendelkezik. A metrikák az [Azure-szolgáltatások nagyméretű listájához](../platform/metrics-supported.md)érhetők el. Ez a cikk az erőforráshoz tartozó részhalmazok (azaz) használatát ismerteti `Microsoft.OperationalInsights/workspaces` .

Metrikai riasztásokat használhat a mutatóként kinyert népszerű Log Analytics naplókban, beleértve az Azure-ban vagy a helyszínen található erőforrásokat is. A támogatott Log Analytics megoldások az alábbiak szerint vannak felsorolva:

- [Teljesítményszámlálók](./../agents/data-sources-performance-counters.md) Windows & Linux rendszerű gépekhez
- [Szívverési rekordok Agent Health](../insights/solution-agenthealth.md)
- [Felügyeleti rekordok frissítése](../../automation/update-management/overview.md)
- [Esemény](./../agents/data-sources-windows-events.md) -adatnaplók

Az Azure-ban a lekérdezésen alapuló [naplózási riasztásokra](./alerts-log.md) **vonatkozó mérőszám-riasztások** használatának számos előnye van. Némelyikük alább látható:

- A metrikai riasztások közel valós idejű figyelési képességet és metrikai riasztásokat biztosítanak a naplókból származó adatoknak a log forrásból való biztosításához.
- A metrikai riasztások csak akkor jelennek meg, ha riasztást küldenek, és ha a riasztás megoldódott, Ha a riasztási feltétel teljesül, a riasztásokat a rendszer az állapot nélküli naplóba helyezi, és minden intervallumban megőrzi a tüzelést.
- A naplók metrikai riasztásai több dimenziót biztosítanak, így például a számítógépek, az operációs rendszer típusa stb. egyszerűbb a szűrés. anélkül, hogy az elemzés során kellene megadnia a lekérdezéseket.

> [!NOTE]
> Az adott metrika és/vagy dimenzió csak akkor jelenik meg, ha az adatok a kiválasztott időszakban szerepelnek. Ezek a metrikák az Azure Log Analytics-munkaterületekkel rendelkező ügyfelek számára érhetők el.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Naplók által támogatott mérőszámok és méretek

 A metrikai riasztások támogatják a dimenziókat használó mérőszámok riasztásait. Méretek használatával szűrheti a metrikát a megfelelő szintre. A rendszer felsorolja a [log Analytics munkaterületekről](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) származó naplókhoz támogatott metrikák teljes listáját. a támogatott megoldások között.

> [!NOTE]
> Egy Log Analytics munkaterületről [Azure monitor-metrikák](../platform/metrics-charts.md)használatával kinyert támogatott metrika megtekintéséhez az adott metrikán létre kell hozni egy metrikai riasztást a naplóhoz. A naplók metrikai riasztásában kiválasztott dimenziók csak Azure Monitor-metrikák használatával jelennek meg a feltáráshoz.

## <a name="creating-metric-alert-for-log-analytics"></a>Metrikai riasztás létrehozása Log Analyticshoz

A népszerű naplókból származó metrikai adatok a Log Analyticsba való feldolgozásuk előtt Azure Monitor metrikák. Ez lehetővé teszi a felhasználók számára, hogy kihasználják a metrikus platform képességeit, valamint a metrikai riasztást – beleértve a riasztások gyakoriságát is, amely akár 1 percet is igénybe vehet.
Az alábbiakban felsoroljuk a naplókhoz tartozó metrikus riasztások készítésének módját.

## <a name="prerequisites-for-metric-alert-for-logs"></a>A naplók metrika-riasztásának előfeltételei

A Log Analytics-adatokon összegyűjtött naplók metrikája előtt be kell állítani és elérhetővé kell tennie a következőket:

1. **Aktív log Analytics munkaterület**: egy érvényes és aktív log Analytics munkaterület jelen kell lennie. További információ: [log Analytics munkaterület létrehozása a Azure Portalban](../learn/quick-create-workspace.md).
2. Az **ügynök konfigurálva van log Analytics munkaterületre**: az ügynököt az Azure-beli virtuális gépekhez (és/vagy) a helyszíni virtuális gépekhez kell konfigurálni, hogy az adatküldés a korábbi lépésben használt log Analytics munkaterületre történjen. További információ: [log Analytics – ügynök áttekintése](./../agents/agents-overview.md).
3. A **támogatott log Analytics megoldások telepítve** vannak: a log Analytics megoldást konfigurálni kell, és adatokat kell elküldeni log Analytics munkaterület által támogatott megoldásokba a [teljesítményszámlálók a Windows & Linux](./../agents/data-sources-performance-counters.md), [szívverési rekordok a Agent Health, az](../insights/solution-agenthealth.md) [Update Management](../../automation/update-management/overview.md)és az [Event adatok](./../agents/data-sources-windows-events.md)számára.
4. **Naplók küldésére konfigurált log Analytics megoldások**: a log Analytics megoldásnak rendelkeznie kell az [log Analytics munkaterületek által támogatott mérőszámokhoz](../platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) tartozó szükséges naplókkal/adatokkal. A *(z)% rendelkezésre álló memória* számlálójának esetében például először a [teljesítményszámlálók](./../agents/data-sources-performance-counters.md) megoldásban kell konfigurálni.

## <a name="configuring-metric-alert-for-logs"></a>Metrikai riasztás konfigurálása a naplókhoz

 A metrikai riasztások a Azure Portal, a Resource Manager-sablonok, a REST API, a PowerShell és az Azure CLI használatával hozhatók létre és kezelhetők. Mivel a naplók metrikai riasztásai, a metrikus riasztások egy változata – amint az előfeltételek teljesülnek, a naplók metrikai riasztása a megadott Log Analytics munkaterülethez hozható létre. A [metrikai riasztások](./alerts-metric-near-real-time.md) minden jellemzője és funkciója a naplók metrikájának riasztására is vonatkozik. beleértve a hasznos adatok sémáját, a vonatkozó kvóta korlátait és a számlázás díját.

Részletes részletekért és példákért tekintse meg a [metrikai riasztások létrehozásával és kezelésével](./alerts-metric.md)foglalkozó témakört. A naplók metrikai riasztásai esetében kövesse a metrikai riasztások kezelésével kapcsolatos útmutatást, és győződjön meg a következőkről:

- A metrikai riasztás célja érvényes *log Analytics munkaterület*
- A kiválasztott *log Analytics-munkaterület* metrikai riasztására kiválasztott jel a következő típusú: **metrika**
- Adott feltételek vagy erőforrások szűrése dimenzió szűrők használatával; a naplók metrikái többdimenziós
- A *jel logikájának* konfigurálásakor egyetlen riasztás hozható létre a dimenzió több értékének (például a számítógép) átméretezéséhez.
- Ha **nem** használja a Azure Portal a metrikai riasztások létrehozásához a kiválasztott *log Analytics-munkaterülethez*; Ezután a felhasználónak manuálisan kell létrehoznia egy explicit szabályt, amellyel a rendszer [Azure monitor ütemezett lekérdezési szabályok](/rest/api/monitor/scheduledqueryrules)segítségével konvertálja a napló adatait metrikai adatokra.

> [!NOTE]
> Amikor metrikai riasztást hoz létre Log Analytics munkaterülethez Azure Portal – a naplózási adatok metrikaba való átalakítására vonatkozó szabály a [Azure monitor ütemezett lekérdezési szabályok](/rest/api/monitor/scheduledqueryrules) használatával automatikusan jön létre, és nincs *szükség felhasználói beavatkozásra vagy műveletre*. Ahhoz, hogy a naplók a Azure Portalon kívüli módon történő létrehozásához metrikai riasztást kapjanak, tekintse meg a metrikus riasztások [a naplókhoz](#resource-template-for-metric-alerts-for-logs) című szakaszát, amely a ScheduledQueryRule-alapú naplózás metrikus átalakítási szabályhoz való létrehozását ismerteti a metrikus riasztások létrehozása előtt

## <a name="resource-template-for-metric-alerts-for-logs"></a>Erőforrás-sablon metrikai riasztásokhoz a naplókhoz

Ahogy korábban már említettük, a naplókból származó metrikus riasztások létrehozásának folyamata két részből áll:

1. Szabály létrehozása a mérőszámok kinyeréséhez a támogatott naplókból a scheduledQueryRule API használatával
2. Metrikai riasztás létrehozása a naplóból kinyert metrika (1. lépés) és Log Analytics munkaterület célként megadott erőforrásként

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Metrikus riasztások statikus küszöbértékű naplók esetén

Ha ugyanezt szeretné elérni, használhatja az alábbi minta Azure Resource Manager sablont – ahol a statikus küszöbértékek metrikai riasztásának létrehozása a scheduledQueryRule-on keresztüli naplókból kinyerhető szabály sikeres létrehozásával függ.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
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
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
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
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
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
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
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

Tegyük fel, hogy a fenti JSON-t metricfromLogsAlertStatic.jsként menti a rendszer, így az erőforrás-sablon alapú létrehozáshoz tartozó JSON-fájllal is párosítható. A következő lista egy példaként szolgáló JSON-fájlt mutat be:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Feltételezve, hogy a fenti paraméter fájlja metricfromLogsAlertStatic.parameters.jsként van mentve; Ezután létrehozhat metrikai riasztást a naplókhoz [erőforrás-sablon használatával a Azure Portal létrehozásához](../../azure-resource-manager/templates/deploy-portal.md).

Azt is megteheti, hogy az alábbi Azure PowerShell-parancsot is használhatja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Vagy használja az Azure CLI-vel történő üzembe helyezési sablont:

```azurecli
az deployment group create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Metrikus riasztások a dinamikus küszöbértékekkel rendelkező naplókhoz

Ha ugyanezt szeretné elérni, használhatja az alábbi minta Azure Resource Manager sablont – ahol a dinamikus küszöbértékek metrikai riasztásának létrehozása a scheduledQueryRule-on keresztüli adatnaplókból kinyert szabály sikeres létrehozásával függ.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
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
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example: /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
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
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
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
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
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

Tegyük fel, hogy a fenti JSON-t metricfromLogsAlertDynamic.jsként menti a rendszer, így az erőforrás-sablon alapú létrehozáshoz tartozó JSON-fájllal is párosítható. A következő lista egy példaként szolgáló JSON-fájlt mutat be:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
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
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Feltételezve, hogy a fenti paraméter fájlja metricfromLogsAlertDynamic.parameters.jsként van mentve; Ezután létrehozhat metrikai riasztást a naplókhoz [erőforrás-sablon használatával a Azure Portal létrehozásához](../../azure-resource-manager/templates/deploy-portal.md).

Azt is megteheti, hogy az alábbi Azure PowerShell-parancsot is használhatja:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Vagy használja az Azure CLI-vel történő üzembe helyezési sablont:

```azurecli
az deployment group create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Következő lépések

- További információ a [metrikai riasztásokról](../alerts/alerts-metric.md).
- További információ a [log-riasztásokról az Azure-ban](./alerts-unified-log.md).
- További információ [Az Azure-beli riasztásokról](../platform/alerts-overview.md).