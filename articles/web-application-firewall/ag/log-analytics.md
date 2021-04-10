---
title: Az Azure Log Analytics használata Application Gateway webalkalmazási tűzfal naplófájljainak vizsgálatához
description: Ez a cikk bemutatja, hogyan használható az Azure Log Analytics a webalkalmazási tűzfal naplófájljainak Application Gateway vizsgálatához
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/06/2019
ms.author: victorh
ms.openlocfilehash: c928d4234e82e7c43c2365e20d57d0b97d4dbc1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588998"
---
# <a name="use-log-analytics-to-examine-application-gateway-web-application-firewall-waf-logs"></a>Log Analytics használata Application Gateway webalkalmazási tűzfal (WAF) naplófájljainak vizsgálatához

Ha a Application Gateway WAF működőképes, engedélyezheti a naplók számára, hogy megvizsgálják, mi történik az egyes kérelmekkel. A tűzfal naplói betekintést nyújtanak a WAF értékelésére, egyeztetésére és blokkolására. A Azure Monitor Log Analytics segítségével megvizsgálhatja a tűzfalakon belüli adatnaplókat, hogy még több elemzést nyújtson. Log Analytics munkaterület létrehozásával kapcsolatos további információkért lásd: [log Analytics munkaterület létrehozása a Azure Portal](../../azure-monitor/logs/quick-create-workspace.md). További információ a naplózási lekérdezésekről: [a Azure monitorban található lekérdezések áttekintése](../../azure-monitor/logs/log-query-overview.md).

## <a name="import-waf-logs"></a>WAF-naplók importálása

A tűzfal naplóinak Log Analyticsba való importálásához tekintse meg [a háttér állapota, az erőforrás-naplók és a metrikák Application Gateway](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging). Ha a tűzfal naplókat tartalmaz a Log Analytics munkaterületen, megtekintheti az adatait, lekérdezéseket írhat, vizualizációkat hozhat létre, és hozzáadhatja őket a portál irányítópultján.

## <a name="explore-data-with-examples"></a>Az adatelemzés példákkal

A következő lekérdezés futtatásával tekintheti meg a tűzfal naplójában lévő nyers adatfájlokat:

```
AzureDiagnostics 
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
```

Ez a következő lekérdezéshez hasonlóan fog kinézni:

![Log Analytics lekérdezés](../media/log-analytics/log-query.png)

Megtekintheti az adatok részletezését, és diagramokat rajzolhat, vagy létrehozhat vizualizációkat. Kiindulási pontként tekintse meg a következő lekérdezéseket:

### <a name="matchedblocked-requests-by-ip"></a>Egyeztetett/letiltott kérések IP alapján

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by clientIp_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="matchedblocked-requests-by-uri"></a>Egyeztetett/letiltott kérések URI szerint

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by requestUri_s, bin(TimeGenerated, 1m)
| render timechart
```

### <a name="top-matched-rules"></a>Leggyakoribb egyezési szabályok

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize count() by ruleId_s, bin(TimeGenerated, 1m)
| where count_ > 10
| render timechart
```

### <a name="top-five-matched-rule-groups"></a>Az első öt megfeleltetett szabály csoportja

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.NETWORK" and Category == "ApplicationGatewayFirewallLog"
| summarize Count=count() by details_file_s, action_s
| top 5 by Count desc
| render piechart
```

## <a name="add-to-your-dashboard"></a>Hozzáadás az irányítópulthoz

A lekérdezés létrehozása után felveheti azt az irányítópultra.  A log Analytics-munkaterület jobb felső sarkában válassza a **rögzítés az irányítópulton** lehetőséget. Egy példa irányítópultra rögzített előző négy lekérdezéssel az alábbi információk láthatók:

![A képernyőképen egy Azure-irányítópult látható, ahol felveheti a lekérdezést.](../media/log-analytics/dashboard.png)

## <a name="next-steps"></a>Következő lépések

[Háttérbeli állapot, erőforrás-naplók és metrikák a Application Gateway](../../application-gateway/application-gateway-diagnostics.md)