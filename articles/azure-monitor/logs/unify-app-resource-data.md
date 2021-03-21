---
title: Több Azure Monitor Application Insights erőforrás egységesítése | Microsoft Docs
description: Ez a cikk részletesen ismerteti, hogyan használható függvények a Azure Monitor naplókban több Application Insights erőforrás lekérdezéséhez és az adatok megjelenítéséhez.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 7f49bd23347b53f9a5287973c6b2aba82da50ca7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100619458"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Több Azure Monitor Application Insights erőforrás egységesítése 
Ez a cikk azt ismerteti, hogyan lehet lekérdezni és megtekinteni az összes Application Insights naplózási adatait egy helyen, még akkor is, ha azok különböző Azure-előfizetésekben találhatók, a Application Insights Connector elavulttá tételének pótlására. Az egyetlen lekérdezésben felvehető Application Insights erőforrások száma a 100-ra korlátozódik.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Több Application Insights erőforrás lekérdezésének ajánlott módszere 
A lekérdezésekben több Application Insights-erőforrás felsorolása nehézkes és nehéz fenntartani. Ehelyett a függvényt kihasználva elkülönítheti a lekérdezési logikát az alkalmazások hatókörében.  

Ez a példa azt mutatja be, hogyan figyelhető meg több Application Insights erőforrás, és Hogyan jeleníthető meg a sikertelen kérelmek száma az alkalmazás neve alapján.

Hozzon létre egy függvényt a Union operátor használatával az alkalmazások listájával, majd mentse a lekérdezést a munkaterületen a *applicationsScoping* alias függvényként. 

A felsorolt alkalmazásokat bármikor módosíthatja a portálon, ha megnyitja a lekérdezési tallózót a munkaterületen, majd kiválasztja a függvényt a szerkesztéshez, majd a PowerShell-parancsmag használatával vagy a mentéshez `SavedSearch` . 

>[!NOTE]
>Ez a metódus nem használható a naplózási riasztásokkal, mert a riasztási szabály erőforrásainak, köztük a munkaterületeknek és az alkalmazásoknak a hozzáférés-ellenőrzését a riasztás létrehozási ideje végzi. Új erőforrások hozzáadása a függvényhez a riasztás létrehozása után nem támogatott. Ha az erőforrás-hatókör függvényét szeretné használni a naplózási riasztásokban, szerkesztenie kell a riasztási szabályt a portálon, vagy egy Resource Manager-sablonnal a hatókörrel rendelkező erőforrások frissítéséhez. Azt is megteheti, hogy felveszi az erőforrások listáját a napló riasztási lekérdezésében.

A `withsource= SourceApp` parancs hozzáadja a naplót küldő alkalmazást jelölő oszlopokat az eredményekhez. Ebben a példában az elemzési operátor nem kötelező, és az alkalmazás nevét kinyeri az SourceApp tulajdonságból. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Most már készen áll a applicationsScoping függvény használatára a több erőforrást használó lekérdezésben:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

A lekérdezés Application Insights sémát használ, bár a lekérdezés végrehajtása a munkaterületen történik, mivel a applicationsScoping függvény a Application Insights adatstruktúrát adja vissza. A függvény aliasa az összes definiált alkalmazástól érkező kérések unióját adja vissza. A lekérdezés ezután szűri a sikertelen kérelmeket, és alkalmazás szerint jeleníti meg a trendeket.

![Példa több lekérdezés eredményére](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>A naplózási riasztásokban az [erőforrások közötti lekérdezések](../logs/cross-workspace-query.md) csak a jelenlegi [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)-ban támogatottak. Ha az örökölt Log Analytics riasztások API-t használja, át kell [váltania az aktuális API](../alerts/alerts-log-api-switch.md)-ra. [Lásd: példák a sablonokra](../alerts/alerts-log-create-templates.md).

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights és Log Analytics munkaterület sémájának eltérései
Az alábbi táblázat a Log Analytics és Application Insights közötti sémák közötti különbségeket mutatja be.  

| Log Analytics munkaterület tulajdonságai| Erőforrás-tulajdonságok Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | időbélyeg |
| Böngésző | client_browser |
| City | client_city |
| Ügyfélip | client_IP |
| Computer | cloud_RoleInstance | 
| Ország | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | típus |
| OperationID | operation_id |
| OperationName | operation_Name | 
| Operációs rendszer | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | id | 
| RequestName | name | 
| RequestSuccess | sikeres | 
| ResponseCode | resultCode | 
| Szerepkör | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | típus |
| URL-cím | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Következő lépések

A Application Insights-alkalmazások részletes adatainak megtekintéséhez használja a [log Search kifejezést](../logs/log-query-overview.md) .

