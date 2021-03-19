---
title: Azure webalkalmazási tűzfal figyelése és naplózása
description: A webalkalmazási tűzfal (WAF) megismerése FrontDoor-figyeléssel és-naplózással
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 596374d4f3f188e08a10bd25b36b178cc79a6e57
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84808952"
---
# <a name="azure-web-application-firewall-monitoring-and-logging"></a>Azure webalkalmazási tűzfal figyelése és naplózása

Az Azure webalkalmazási tűzfal (WAF) figyelése és naplózása a Azure Monitor-és Azure Monitor-naplókkal való naplózással és integrációval történik.

## <a name="azure-monitor"></a>Azure Monitor

A FrontDoor naplóval rendelkező WAF integrálva van a [Azure monitor](../../azure-monitor/overview.md). Azure Monitor lehetővé teszi a diagnosztikai információk nyomon követését, beleértve a WAF-riasztásokat és-naplókat. A WAF-figyelést a portálon belül, a **diagnosztika** lapon vagy közvetlenül a Azure monitor szolgáltatáson keresztül konfigurálhatja.

Azure Portal válassza a bejárati erőforrás típusa lehetőséget. A bal oldali **figyelési** / **metrikák** lapról hozzáadhat **WebApplicationFirewallRequestCount** a WAF-szabályoknak megfelelő kérelmek számának nyomon követéséhez. Az egyéni szűrők a műveleti típusok és a szabályok nevei alapján hozhatók létre.

:::image type="content" source="../media/waf-frontdoor-monitor/waf-frontdoor-metrics.png" alt-text="WAFMetrics ":::

## <a name="logs-and-diagnostics"></a>Naplók és diagnosztika

A WAF az észlelt fenyegetésekkel kapcsolatos részletes jelentéseket biztosít. A naplózás integrálva van az Azure Diagnostics naplóival, a riasztások pedig JSON formátumban vannak rögzítve. Ezek a naplók [Azure monitor naplókkal](../../azure-monitor/insights/azure-networking-analytics.md)integrálhatók.

![WAFDiag](../media/waf-frontdoor-monitor/waf-frontdoor-diagnostics.png)

A [FrontdoorAccessLog](../../frontdoor/front-door-diagnostics.md) az összes kérelmet naplózza. A FrontdoorWebApplicationFirewallLog minden olyan kérést naplóz, amely megfelel egy, az alábbi sémával rendelkező WAF-szabálynak:

| Tulajdonság  | Leírás |
| ------------- | ------------- |
|Művelet|A kérelemben végrehajtott művelet|
| ClientIp (Ügyfél IP-címe) | Annak az ügyfélnek az IP-címe, amely a kérelmet elvégezte. Ha a kérelemben a fejléchez X-továbbítás történt, akkor az ügyfél IP-címe a fejléc mezőből lesz kiválasztva. |
| ClientPort | Az ügyfél IP-portja, amely a kérést elvégezte. |
| Részletek|További részletek a megfeleltetett kérelemről |
|| matchVariableName: a kérelemben szereplő http-paraméter neve egyezik, például a fejléc neve|
|| matchVariableValue: a megfeleltetést kiváltó értékek|
| Gazdagép | Az egyeztetett kérelem állomásneve |
| Szabályzat | Annak a WAF-házirendnek a neve, amelyhez a kérelem illeszkedik. |
| PolicyMode | A WAF házirend működési módja. A lehetséges értékek a következők: "megelőzés" és "észlelés" |
| RequestUri | Az egyeztetett kérelem teljes URI-ja. |
| RuleName | Annak a WAF-szabálynak a neve, amelyhez a kérelem illeszkedik. |
| SocketIp | A WAF által látott forrás IP-cím. Ez az IP-cím a kérések fejlécének független TCP-munkameneten alapul.|
| TrackingReference | A bejárati ajtó által kiszolgált kérést azonosító egyedi hivatkozási sztring, amely az ügyfélnek X-Azure-ref fejlécként is elküldve. Egy adott kérelem hozzáférési naplóiban található adatok kereséséhez szükséges. |

A következő lekérdezési példa visszaadja a WAF-naplókat a blokkolt kérelmeknél:

``` WAFlogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorWebApplicationFirewallLog"
| where action_s == "Block"

```

Íme egy példa egy naplózott kérelemre a WAF-naplóban:

``` WAFlogQuerySample
{
    "time":  "2020-06-09T22:32:17.8376810Z",
    "category": "FrontdoorWebApplicationFirewallLog",
    "operationName": "Microsoft.Network/FrontDoorWebApplicationFirewallLog/Write",
    "properties":
    {
        "clientIP":"xxx.xxx.xxx.xxx",
        "clientPort":"52097",
        "socketIP":"xxx.xxx.xxx.xxx",
        "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
        "ruleName":"Microsoft_DefaultRuleSet-1.1-SQLI-942100",
        "policy":"WafDemoCustomPolicy",
        "action":"Block",
        "host":"wafdemofrontdoorwebapp.azurefd.net",
        "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
        "policyMode":"prevention",
        "details":
            {
            "matches":
                [{
                "matchVariableName":"QueryParamValue:q",
                "matchVariableValue":"' or 1=1"
                }]
            }
     }
}
```

A következő példában szereplő lekérdezés a AccessLogs-bejegyzéseket adja vissza:

``` AccessLogQuery
AzureDiagnostics
| where ResourceType == "FRONTDOORS" and Category == "FrontdoorAccessLog"

```

Íme egy példa egy naplózott kérelemre a hozzáférési naplóban:

``` AccessLogSample
{
"time": "2020-06-09T22:32:17.8383427Z",
"category": "FrontdoorAccessLog",
"operationName": "Microsoft.Network/FrontDoor/AccessLog/Write",
 "properties":
    {
    "trackingReference":"08Q3gXgAAAAAe0s71BET/QYwmqtpHO7uAU0pDRURHRTA1MDgANjMxNTAwZDAtOTRiNS00YzIwLTljY2YtNjFhNzMyOWQyYTgy",
    "httpMethod":"GET",
    "httpVersion":"2.0",
    "requestUri":"https://wafdemofrontdoorwebapp.azurefd.net:443/?q=%27%20or%201=1",
    "requestBytes":"715",
    "responseBytes":"380",
    "userAgent":"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/85.0.4157.0 Safari/537.36 Edg/85.0.531.1",
    "clientIp":"xxx.xxx.xxx.xxx",
    "socketIp":"xxx.xxx.xxx.xxx",
    "clientPort":"52097",
    "timeTaken":"0.003",
    "securityProtocol":"TLS 1.2",
    "routingRuleName":"WAFdemoWebAppRouting",
    "rulesEngineMatchNames":[],
    "backendHostname":"wafdemowebappuscentral.azurewebsites.net:443",
    "sentToOriginShield":false,
    "httpStatusCode":"403",
    "httpStatusDetails":"403",
    "pop":"SJC",
    "cacheStatus":"CONFIG_NOCACHE"
    }
}

```

## <a name="next-steps"></a>Következő lépések

- További információ a [bejárati ajtóról](../../frontdoor/front-door-overview.md).
