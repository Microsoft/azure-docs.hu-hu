---
title: Az Azure Alerts szolgáltatásban naplóriasztásokra vonatkozó Webhook-műveletek
description: Ez a cikk bemutatja, hogyan egy riasztási szabály a log analytics munkaterület és application insights segítségével adatokat küld le HTTP-webhook és a részletek a különböző testreszabási lehetséges.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 2307fa985c88608d80400c8951c47b9f20caa1dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515521"
---
# <a name="webhook-actions-for-log-alert-rules"></a>Webhook-műveletek a riasztási szabály
Ha egy [riasztás jön létre az Azure-ban](alerts-log.md), lehetősége van a [konfigurálása a Műveletcsoportok használatával](action-groups.md) egy vagy több művelet végrehajtásához.  Ez a cikk ismerteti a rendelkezésre álló különböző webhook-műveletek és a részletek az egyéni JSON-alapú webhook konfigurálásáról.


## <a name="webhook-actions"></a>Webhook-műveletek

Webhook-műveletek lehetővé teszik a külső folyamatok egyetlen HTTP POST-kérés.  A hívott szolgáltatást kell webhookok támogatása, és határozza meg, hogyan használja a bármely hasznos kap.    

Webhook-műveletek az alábbi táblázatban szereplő tulajdonságok szükségesek:

| Tulajdonság | Leírás |
|:--- |:--- |
| Webhook URL-címe |A webhook URL-címe |
| Egyéni JSON-adattartalmat |Egyéni tartalom használatával küldhet a webhookot, ha ezt a lehetőséget választja a riasztás létrehozásakor. A részletek [riasztások kezelése](alerts-log.md) |

> [!NOTE]
> Nézet Webhook gomb problémamentesen *Belefoglalás egyéni JSON-adattartalom-webhook* beállításhoz tartozó értesítési napló, megjeleníti a megadott testreszabása a webhook hasznosadat-minta. Tényleges adatok és riasztások használt JSON-sémájában képviselője nem tartalmaz. 

Webhookok tartalmaznak egy URL-CÍMÉT és a egy hasznos, amely a külső szolgáltatásnak küldött adatok JSON-formátumú.  Alapértelmezés szerint a hasznos adatokat az alábbi táblázatban az értékeket tartalmazza:  Ha szeretné, cserélje le a saját egyéni egy hasznos adatot.  Ebben az esetben használhatja a változók a táblázatban az egyes paraméterek értékük szerepeljenek az egyéni adattartalom.


| Paraméter | Változó | Leírás |
|:--- |:--- |:--- |
| AlertRuleName |#alertrulename |A riasztási szabály neve. |
| Severity |#severity |Állítsa be az aktivált riasztás súlyossága. |
| AlertThresholdOperator |#thresholdoperator |A riasztási szabály küszöbértékét operátort.  *Nagyobb, mint* vagy *kevesebb mint*. |
| AlertThresholdValue |#thresholdvalue |A riasztási szabályhoz tartozó küszöbérték. |
| LinkToSearchResults |#linktosearchresults |Analitikai portál, amely a rekordokat adja vissza a lekérdezést, amely létrehozta a riasztást mutató hivatkozás. |
| ResultCount |#searchresultcount |A keresési eredmények rekordok száma. |
| Keresési intervallum befejezési időpontja |#searchintervalendtimeutc |Befejezési idő (UTC), a lekérdezés format - hh/nn/éééé óó: pp: ss de. / du. |
| Keresési intervallum |#searchinterval |Ez alatt az időszak a riasztáshoz tartozó szabályt,-formátum – ÓÓ. |
| Keresési intervallum kezdő időpont |#searchintervalstarttimeutc |(UTC), a lekérdezés kezdete format - hh/nn/éééé óó: pp: ss de. / du.... 
| SearchQuery |#searchquery |Naplóbeli keresési lekérdezés a riasztási szabály által használt. |
| SearchResults |"IncludeSearchResults": true|A lekérdezés által visszaadott JSON táblázat, csak az első 1000 rekord; rekordok Ha a "IncludeSearchResults": true adnak hozzá a egyéni JSON webhook definíciója adható meg a legfelső szintű tulajdonsággal. |
| WorkspaceID |#workspaceid |A Log Analytics-munkaterület azonosítója. |
| Alkalmazásazonosító |#applicationid |Az Application Insights azonosítója alkalmazást. |
| Előfizetés azonosítója |#subscriptionid |Az Application insights szolgáltatással használt Azure-előfizetésében azonosítója. 

> [!NOTE]
> LinkToSearchResults átadja SearchQuery, keresési intervallum StartTime & keresési intervallum befejezési idő például paramétereket az URL-címben Analytics szakaszban megtekintése az Azure Portalon. Az Azure portal mérete megközelítőleg 2000 karakterből áll, és automatikusan URI tartozik *nem* riasztásokat, ha paraméterek értékét az említett korlátját meghaladja a megadott hivatkozás megnyitása. Felhasználók manuálisan bevinni részleteit az eredmények megtekintése az Analytics-portálon, vagy használja a [Application Insights Analytics REST API](https://dev.applicationinsights.io/documentation/Using-the-API) vagy [Log Analytics REST API](/rest/api/loganalytics/) eredmények programozott módon lekéréséhez 

Például megadhatja a következő nevű egyetlen paramétert tartalmazó egyéni adattartalom *szöveg*.  A szolgáltatás, amely meghívja ezt a webhookot szeretné ezt a paramétert várt lehet.

```json

    {
        "text":"#alertrulename fired with #searchresultcount over threshold of #thresholdvalue."
    }
```
Ebben a példában hasznos, ha a webhook küld az alábbihoz hasonlóra szeretné feloldani.

```json
    {
        "text":"My Alert Rule fired with 18 records over threshold of 10 ."
    }
```
Mivel minden változóját egy egyéni webhook kell például a "#searchinterval" JSON ház belül megadott, a létrejövő webhook is megkapják ház mint belül változó adatok "00: 05:00".

Keresési eredmények belefoglalása az egyéni adattartalom, ellenőrizze, hogy **IncludeSearchResults** a json-adattartalom legfelső szintű tulajdonság be van állítva. 

## <a name="sample-payloads"></a>Minta is észleltünk adattartalmakat.
Ez a szakasz bemutatja a webhook hasznosadat-minta a riasztások, beleértve a szabványos terhelés esetén, és ha az egyéni.

### <a name="standard-webhook-for-log-alerts"></a>Standard szintű, Naplóriasztásokra vonatkozó Webhook 
A helyőrző adattartalom csak két oszlop, és két sor mindkét példa jeleztük.

#### <a name="log-alert-for-azure-log-analytics"></a>Az Azure Log-Analytics riasztás
Az alábbiakban a egy hasznosadat-minta egy szabványos webhook művelet *egyéni Json-beállítás nélkül* a log analytics-alapú riasztásokat használja.

```json
{
    "WorkspaceId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
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
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://workspaceID.portal.mms.microsoft.com/#Workspace/search/index?_timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "Warning"
 }
 ```

> [!NOTE]
> Súlyosság mező értéke változhatnak, ha rendelkezik [váltott, az API szabályozó](alerts-log-api-switch.md) riasztások a Log Analytics számára.


#### <a name="log-alert-for-azure-application-insights"></a>Az Azure Application Insights riasztás
Az alábbiakban a egy hasznosadat-minta egy szabványos webhook *egyéni Json-beállítás nélkül* az application insights-alapú-riasztások használatakor.
    
```json
{
    "schemaId":"Microsoft.Insights/LogAlert","data":
    { 
    "SubscriptionId":"12345a-1234b-123c-123d-12345678e",
    "AlertRuleName":"AcmeRule","SearchQuery":"search *",
    "SearchResult":
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
        },
    "SearchIntervalStartTimeUtc": "2018-03-26T08:10:40Z",
    "SearchIntervalEndtimeUtc": "2018-03-26T09:10:40Z",
    "AlertThresholdOperator": "Greater Than",
    "AlertThresholdValue": 0,
    "ResultCount": 2,
    "SearchIntervalInSeconds": 3600,
    "LinkToSearchResults": "https://analytics.applicationinsights.io/subscriptions/12345a-1234b-123c-123d-12345678e/?query=search+*+&timeInterval.intervalEnd=2018-03-26T09%3a10%3a40.0000000Z&_timeInterval.intervalDuration=3600&q=Usage",
    "Description": null,
    "Severity": "3",
    "ApplicationId": "123123f0-01d3-12ab-123f-abc1ab01c0a1"
    }
}
```

#### <a name="log-alert-with-custom-json-payload"></a>Egyéni JSON-adattartalom-riasztás
Ha például szeretne létrehozni egy egyéni hasznos adat, amely tartalmazza a riasztás neve és a keresési eredmények között, használhatja a következőket: 

```json
    {
       "alertname":"#alertrulename",
       "IncludeSearchResults":true
    }
```

Következő egy hasznosadat-minta bármely riasztás egyéni webhook művelet.
    
```json
    {
    "alertname":"AcmeRule","IncludeSearchResults":true,
    "SearchResult":
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


## <a name="next-steps"></a>További lépések
- Ismerje meg [Naplóriasztások az Azure-riasztások](alerts-unified-log.md)
- Megismerheti [kezelése naplóriasztások az Azure-ban](alerts-log.md)
- Létrehozásához és kezeléséhez [Műveletcsoportok az Azure-ban](action-groups.md)
- Tudjon meg többet [Application Insights](../../azure-monitor/app/analytics.md)
- Tudjon meg többet [lekérdezések naplózását](../log-query/log-query-overview.md). 

