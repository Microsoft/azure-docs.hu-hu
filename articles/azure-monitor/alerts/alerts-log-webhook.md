---
title: A log-riasztások webhook-műveletei az Azure-riasztásokban
description: Ismerteti, hogyan lehet naplózni a riasztásokat a webhook művelettel és a rendelkezésre álló testreszabásokkal
author: yanivlavi
ms.author: yalavi
services: monitoring
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 228193066c45421c4dddee1802aba1feed59e9c8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042674"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-műveletek naplóriasztási szabályokhoz

A [log riasztás](alerts-log.md) támogatja a [webhook műveleti csoportok konfigurálását](./action-groups.md#webhook). Ebben a cikkben leírjuk, hogy milyen tulajdonságok érhetők el, és hogyan konfigurálhatók az egyéni JSON-webhookok.

> [!NOTE]
> Az egyéni JSON-alapú webhook jelenleg nem támogatott az API-verzióban `2020-05-01-preview`

> [!NOTE]
> Javasoljuk, hogy az [általános riasztási sémát](../alerts/alerts-common-schema.md) használja a webhook-integrációhoz. Az általános riasztási séma előnye, hogy egyetlen bővíthető és egységesített riasztási adattartalommal rendelkezik a Azure Monitor összes riasztási szolgáltatásában. Az egyéni JSON-adattartalommal rendelkező naplózási riasztási szabályok esetében a közös séma az [itt](../alerts/alerts-common-schema-definitions.md#log-alerts)ismertetett módon visszaállíthatja a hasznos adatokat tartalmazó sémát. A közös sémával rendelkező riasztások legfeljebb 256 KB-os felső korláttal rendelkeznek, és a nagyobb riasztások nem tartalmazzák a keresési eredményeket. Ha a keresési eredmények nem szerepelnek, a vagy a használatával érheti el a `LinkToFilteredSearchResultsAPI` `LinkToSearchResultsAPI` lekérdezési eredményeket a log Analytics API-n keresztül.

## <a name="webhook-payload-properties"></a>Webhook hasznos adatainak tulajdonságai

A webhook-műveletek lehetővé teszik egyetlen HTTP POST-kérelem meghívását. A hívott szolgáltatásnak támogatnia kell a webhookokat, és tudnia kell, hogyan használja a kapott adattartalmat.

Az alapértelmezett webhook művelet tulajdonságai és az egyéni JSON-paraméterek nevei:

| Paraméter | Változó | Leírás |
|:--- |:--- |:--- |
| *AlertRuleName* |#alertrulename |A riasztási szabály neve. |
| *Súlyosság* |#severity |A kilőtt napló riasztásának súlyossági értéke. |
| *AlertThresholdOperator* |#thresholdoperator |A riasztási szabály küszöbérték-kezelője. |
| *AlertThresholdValue* |#thresholdvalue |A riasztási szabály küszöbértéke. |
| *LinkToSearchResults* |#linktosearchresults |Az Analytics-portálra mutató hivatkozás, amely a riasztást létrehozó lekérdezés rekordjait adja vissza. |
| *LinkToSearchResultsAPI* |#linktosearchresultsapi |Az Analytics API-ra mutató hivatkozás, amely a riasztást létrehozó lekérdezés rekordjait adja vissza. |
| *LinkToFilteredSearchResultsUI* |#linktofilteredsearchresultsui |Az elemzési portálra mutató hivatkozás, amely a riasztást létrehozó dimenzió érték kombinációk alapján szűrt rekordokat adja vissza. |
| *LinkToFilteredSearchResultsAPI* |#linktofilteredsearchresultsapi |Az Analytics API-ra mutató hivatkozás, amely a riasztást létrehozó dimenzió érték kombinációk alapján szűrt rekordokat adja vissza. |
| *ResultCount* |#searchresultcount |A keresési eredményekben szereplő rekordok száma. |
| *Keresési időköz befejezési időpontja* |#searchintervalendtimeutc |A lekérdezés befejezési időpontja UTC szerint, hh/nn/éééé óó: PP: SS AM/du formátumban. |
| *Keresési időköz* |#searchinterval |A riasztási szabály időablaka, a HH: PP: mm formátumban. |
| *Keresési időköz kezdő időpont* |#searchintervalstarttimeutc |A lekérdezés kezdési időpontja UTC formátumban, hh/nn/éééé óó: PP: SS AM/du formátumban. 
| *SearchQuery* |#searchquery |A riasztási szabály által használt napló keresési lekérdezése. |
| *SearchResults* |"IncludeSearchResults": igaz|A lekérdezés által visszaadott rekordok JSON-táblázatként, az első 1 000-rekordokra korlátozódik. "IncludeSearchResults": az igaz érték egy egyéni JSON webhook-definícióban van hozzáadva legfelső szintű tulajdonságként. |
| *Méretek* |"IncludeDimensions": igaz|Dimenzió érték kombinációk, amelyek a riasztást a JSON-szakaszként váltották ki. "IncludeDimensions": az igaz érték egy egyéni JSON webhook-definícióban van hozzáadva legfelső szintű tulajdonságként. |
| *Riasztás típusa*| #alerttype | A [metrika mértékének vagy az eredmények számának](./alerts-unified-log.md#measure)megfelelően konfigurált log riasztási szabály típusa.|
| *Munkaterület azonosítója* |#workspaceid |A Log Analytics munkaterület azonosítója. |
| *Alkalmazásazonosító* |#applicationid |A Application Insights alkalmazás azonosítója. |
| *Előfizetés azonosítója* |#subscriptionid |A használt Azure-előfizetés azonosítója. |

## <a name="custom-webhook-payload-definition"></a>Egyéni webhook-adattartalom definíciója

Az egyéni JSON- **adattartalom belefoglalása a webhookhoz** lehetőséggel egyéni JSON-adattartalomot szerezhet be a fenti paraméterek használatával. További tulajdonságokat is létrehozhat.
Például megadhatja a következő egyéni adattartalmat, amely egyetlen, *szöveg* nevű paramétert tartalmaz. Az a szolgáltatás, amelyhez ez a webhook-hívás a következő paramétert várja:

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Ez a példa a következőhöz hasonló adatokat oldja fel a webhooknak való elküldésekor:

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Egy egyéni webhook változóit JSON-házban kell megadni. Ha például a fenti webhook-példában a "#searchresultcount" hivatkozik, a kimenet a riasztás eredményei alapján jelenik meg.

A keresési eredmények belefoglalásához adja hozzá a **IncludeSearchResults** -t az egyéni JSON legfelső szintű tulajdonságához. A keresési eredmények JSON-struktúraként szerepelnek, ezért az eredmények nem szerepelhetnek egyéni definiált mezőkben. 

> [!NOTE]
> A **webhookhoz** **tartozó egyéni JSON-adattartalom belefoglalása** gombra kattintva megjelenik a megadottak előzetes verziója. Nem tartalmaz tényleges adatokat, de a rendszer a használni kívánt JSON-sémát reprezentálja. 

## <a name="sample-payloads"></a>Minta hasznos adatok
Ez a szakasz a naplókhoz tartozó webhookok mintavételi hasznos adatait mutatja be. A minta hasznos adatokat tartalmaz példákat, ha a hasznos adatok standard és egyéni.

### <a name="log-alert-for-log-analytics"></a>Log Analytics naplózási riasztása
A következő minta adattartalom a riasztások Log Analyticson alapuló szabványos webhook-műveletre szolgál:

> [!NOTE]
> A "súlyosság" mező értéke megváltozik, ha az [örökölt log Analytics riasztási API](./api-alerts.md)-ból [átváltotta az aktuális scheduledQueryRules API](../alerts/alerts-log-api-switch.md) -t.

```json
{
    "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
    "AlertRuleName": "AcmeRule",
    "SearchQuery": "Perf | where ObjectName == \"Processor\" and CounterName == \"% Processor Time\" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 5m), Computer",
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToFilteredSearchResultsUI": "https://portal.azure.com/#Analyticsblade/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "LinkToSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "LinkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/workspaces/workspaceID/query?query=Heartbeat&timespan=2020-05-07T18%3a11%3a51.0000000Z%2f2020-05-07T18%3a16%3a51.0000000Z",
    "Description": "log alert rule",
    "Severity": "Warning",
    "AffectedConfigurationItems": [
        "INC-Gen2Alert"
    ],
    "Dimensions": [
        {
            "name": "Computer",
            "value": "INC-Gen2Alert"
        }
    ],
    "SearchResult": {
        "tables": [
            {
                "name": "PrimaryResult",
                "columns": [
                    {
                        "name": "$table",
                        "type": "string"
                    },
                    {
                        "name": "Computer",
                        "type": "string"
                    },
                    {
                        "name": "TimeGenerated",
                        "type": "datetime"
                    }
                ],
                "rows": [
                    [
                        "Fabrikam",
                        "33446677a",
                        "2018-02-02T15:03:12.18Z"
                    ],
                    [
                        "Contoso",
                        "33445566b",
                        "2018-02-02T15:16:53.932Z"
                    ]
                ]
            }
        ]
    },
    "WorkspaceId": "12345a-1234b-123c-123d-12345678e",
    "AlertType": "Metric measurement"
}
```

### <a name="log-alert-for-application-insights"></a>Application Insights naplózási riasztása
A következő minta adattartalom a szabványos webhookok esetében használható, ha Application Insights erőforrásokon alapuló naplózási riasztásokat használ:
    
```json
{
    "schemaId": "Microsoft.Insights/LogAlert",
    "data": {
        "SubscriptionId": "12345a-1234b-123c-123d-12345678e",
        "AlertRuleName": "AcmeRule",
        "SearchQuery": "requests | where resultCode == \"500\" | summarize AggregatedValue = Count by bin(Timestamp, 5m), IP",
        "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
        "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
        "AlertThresholdOperator": "Greater Than",
        "AlertThresholdValue": 0,
        "ResultCount": 2,
        "SearchIntervalInSeconds": 3600,
        "LinkToSearchResults": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToFilteredSearchResultsUI": "https://portal.azure.com/AnalyticsBlade/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
        "LinkToSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "LinkToFilteredSearchResultsAPI": "https://api.applicationinsights.io/v1/apps/0MyAppId0/metrics/requests/count",
        "Description": null,
        "Severity": "3",
        "Dimensions": [
            {
                "name": "IP",
                "value": "1.1.1.1"
            }
        ],
        "SearchResult": {
            "tables": [
                {
                    "name": "PrimaryResult",
                    "columns": [
                        {
                            "name": "$table",
                            "type": "string"
                        },
                        {
                            "name": "Id",
                            "type": "string"
                        },
                        {
                            "name": "Timestamp",
                            "type": "datetime"
                        }
                    ],
                    "rows": [
                        [
                            "Fabrikam",
                            "33446677a",
                            "2018-02-02T15:03:12.18Z"
                        ],
                        [
                            "Contoso",
                            "33445566b",
                            "2018-02-02T15:16:53.932Z"
                        ]
                    ]
                }
            ]
        },
        "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1",
        "AlertType": "Metric measurement"
    }
}
```

### <a name="log-alert-for-other-resources-logs-from-api-version-2020-05-01-preview"></a>Napló riasztása más erőforrás-naplókhoz (API-verzióból `2020-05-01-preview` )

> [!NOTE]
> Az API `2020-05-01-preview` -verzió és az erőforrás-központú naplózási riasztások esetében jelenleg nem számítunk fel további díjakat.  Az előzetes verzióban elérhető szolgáltatások díjszabása a jövőben lesz bejelentve, és a számlázás megkezdése előtt értesítést küld. Ha továbbra is az új API-verziót és az erőforrás-központú napló riasztásait szeretné használni a hirdetmény lejárta után, akkor a megfelelő díjszabás alapján számítjuk fel a díjat.

A következő minta adattartalom a szabványos webhookok esetében használható, ha más erőforrás-naplókon alapuló naplózási riasztásokat használ (kivéve a munkaterületeket és a Application Insights):

```json
{
    "schemaId": "azureMonitorCommonAlertSchema",
    "data": {
        "essentials": {
            "alertId": "/subscriptions/12345a-1234b-123c-123d-12345678e/providers/Microsoft.AlertsManagement/alerts/12345a-1234b-123c-123d-12345678e",
            "alertRule": "AcmeRule",
            "severity": "Sev4",
            "signalType": "Log",
            "monitorCondition": "Fired",
            "monitoringService": "Log Alerts V2",
            "alertTargetIDs": [
                "/subscriptions/12345a-1234b-123c-123d-12345678e/resourcegroups/ai-engineering/providers/microsoft.compute/virtualmachines/testvm"
            ],
            "originAlertId": "123c123d-1a23-1bf3-ba1d-dd1234ff5a67",
            "firedDateTime": "2020-07-09T14:04:49.99645Z",
            "description": "log alert rule V2",
            "essentialsVersion": "1.0",
            "alertContextVersion": "1.0"
        },
        "alertContext": {
            "properties": null,
            "conditionType": "LogQueryCriteria",
            "condition": {
                "windowSize": "PT10M",
                "allOf": [
                    {
                        "searchQuery": "Heartbeat",
                        "metricMeasure": null,
                        "targetResourceTypes": "['Microsoft.Compute/virtualMachines']",
                        "operator": "LowerThan",
                        "threshold": "1",
                        "timeAggregation": "Count",
                        "dimensions": [
                            {
                                "name": "ResourceId",
                                "value": "/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm"
                            }
                        ],
                        "metricValue": 0.0,
                        "failingPeriods": {
                            "numberOfEvaluationPeriods": 1,
                            "minFailingPeriodsToAlert": 1
                        },
                        "linkToSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsUI": "https://portal.azure.com#@12f345bf-12f3-12af-12ab-1d2cd345db67/blade/Microsoft_Azure_Monitoring_Logs/LogsBlade/source/Alerts.EmailLinks/scope/%7B%22resources%22%3A%5B%7B%22resourceId%22%3A%22%2Fsubscriptions%2F12345a-1234b-123c-123d-12345678e%2FresourceGroups%2FTEST%2Fproviders%2FMicrosoft.Compute%2FvirtualMachines%2Ftestvm%22%7D%5D%7D/q/eJzzSE0sKklKTSypUSjPSC1KVQjJzE11T81LLUosSU1RSEotKU9NzdNIAfJKgDIaRgZGBroG5roGliGGxlYmJlbGJnoGEKCpp4dDmSmKMk0A/prettify/1/timespan/2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z",
                        "linkToFilteredSearchResultsAPI": "https://api.loganalytics.io/v1/subscriptions/12345a-1234b-123c-123d-12345678e/resourceGroups/TEST/providers/Microsoft.Compute/virtualMachines/testvm/query?query=Heartbeat%7C%20where%20TimeGenerated%20between%28datetime%282020-07-09T13%3A44%3A34.0000000%29..datetime%282020-07-09T13%3A54%3A34.0000000%29%29&timespan=2020-07-07T13%3a54%3a34.0000000Z%2f2020-07-09T13%3a54%3a34.0000000Z"
                    }
                ],
                "windowStartTime": "2020-07-07T13:54:34Z",
                "windowEndTime": "2020-07-09T13:54:34Z"
            }
        }
    }
}
```

### <a name="log-alert-with-a-custom-json-payload"></a>Riasztás egyéni JSON-adattartalommal
Ha például olyan egyéni adattartalmat szeretne létrehozni, amely csak a riasztás nevét és a keresési eredményeket tartalmazza, használja ezt a konfigurációt: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

A következő minta hasznos adatokat tartalmaz egy egyéni webhook-művelethez a naplózási riasztásokhoz:
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResults":
        {
        "tables":[
                    {"name":"PrimaryResult","columns":
                        [
                        {"name":"$table","type":"string"},
                        {"name":"Id","type":"string"},
                        {"name":"TimeGenerated","type":"datetime"}
                        ],
                    "rows":
                        [
                            ["Fabrikam","33446677a","2018-02-02T15:03:12.18Z"],
                            ["Contoso","33445566b","2018-02-02T15:16:53.932Z"]
                        ]
                    }
                ]
        }
    }
```

## <a name="next-steps"></a>Következő lépések
- Tudnivalók a [riasztásokról az Azure-riasztásokban](./alerts-unified-log.md).
- Ismerje meg, hogyan [kezelheti a naplózási riasztásokat az Azure-ban](alerts-log.md).
- Műveleti csoportok létrehozása és kezelése [Az Azure-ban](./action-groups.md).
- További információ a [Application Insightsról](../logs/log-query-overview.md).
- További információ a [naplók lekérdezéséről](../logs/log-query-overview.md).