---
title: Használja őket egységes előtérrendszerként több Azure Monitor az Application Insights-erőforrást |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Monitor naplóira függvény használatával több Application Insights-erőforrások lekérdezése és megjelenítése az adatokat.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: magoedte
ms.openlocfilehash: 3f3de81197b05d4f025a3fd8638cffe4b07cecad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61424643"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Használja őket egységes előtérrendszerként több Azure Monitor az Application Insights-erőforrást 
Ez a cikk bemutatja, hogyan lekérdezéséhez és az összes Application Insights alkalmazás log adatok megtekintése az egyik helyen, akkor is, ha azokat az Azure-előfizetések, az Application Insights-összekötő elavulása helyettesítője. Az Application Insights-erőforrások, amelyeket megadhat egyetlen lekérdezést erőforrások számát 100-ra korlátozódik.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Ajánlott megközelítést több Application Insights-erőforrások lekérdezése 
A lekérdezés több Application Insights-erőforrások listázása lehet nehézkes és nehezen fenntartható. Ehelyett használhatja a lekérdezés logikai elkülönítése az alkalmazások hatókörének függvény.  

Ez a példa bemutatja, hogyan figyelheti több Application Insights-erőforrást, és jelenítheti meg a sikertelen kérelmek száma alkalmazásnév szerint. Mielőtt elkezdené, futtassa ezt a lekérdezést a munkaterületet, amely csatlakozik egymáshoz kapcsolódó alkalmazások listájának az Application Insights-erőforrások: 

```
ApplicationInsights
| summarize by ApplicationName
```

Hozzon létre union operátor használata az elérhető alkalmazások listája egy függvényt, majd mentse a lekérdezés a munkaterületén az aliasszal függvény *applicationsScoping*.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>Módosíthatja a listán szereplő alkalmazások a portálon bármikor ellenőrizheti, hogy a munkaterület lekérdezéskezelő, majd válassza a Szerkesztés és majd mentése, vagy használja a függvény a `SavedSearch` PowerShell-parancsmagot. A `withsource= SourceApp` parancs hozzáadja az eredményeket egy oszlopot, amely az alkalmazás azt jelzi a napló küldött. 
>
>A lekérdezés az Application Insights-sémát, használja, bár a lekérdezés végrehajtása a munkaterületen, mivel a applicationsScoping függvényt ad vissza az Application Insights-adatok struktúrája. 
>
>Az elemzési operátor használata nem kötelező ebben a példában, SourceApp tulajdonságból bontja ki az alkalmazás nevét. 

Most már készen áll az erőforrások közötti lekérdezési applicationsScoping függvénnyel:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

A függvény aliasa, a kérelmek unióját adja vissza a meghatározott alkalmazások. A lekérdezés és a sikertelen kérelmek szűrése elérhetővé, és a trendek alkalmazás.

![Példa a kereszt-lekérdezés eredményei](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Application Insights-erőforrások és a munkaterület adatainak lekérdezése 
Amikor leállítja az összekötő, és meg kell keresztül, amely által az Application Insights-adatok megőrzése (90 nap) volt vágott időtartomány-lekérdezések végrehajtásához, kell elvégeznie [erőforrások közötti lekérdezések](../../azure-monitor/log-query/cross-workspace-query.md) a munkaterület és Application Insights erőforrások egy köztes időszakban. Ez nem lesz, amíg a fent említett új Application Insights-adatmegőrzés / gyűlnek az alkalmazások adatokat. A lekérdezés néhány feldolgozás van szükség, mivel a sémák az Application Insights és a munkaterület különböző. Lásd a jelen szakasz későbbi kiemelése a séma különbségek a táblát. 

>[!NOTE]
>[Erőforrások közötti lekérdezési](../log-query/cross-workspace-query.md) naplóban riasztások az új támogatott [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Alapértelmezés szerint az Azure Monitor használja a [örökölt Log Analytics-riasztás API](../platform/api-alerts.md) új naplófájl riasztási szabályok létrehozását az Azure Portalról, kivéve, ha átvált a [örökölt Log riasztások API](../platform/alerts-log-api-switch.md#process-of-switching-from-legacy-log-alerts-api). A váltás után az új API lesz az alapértelmezett új riasztási szabályok az Azure Portalon, és lehetővé teszi, hogy az erőforrások közötti lekérdezési napló riasztások, szabályok létrehozása. Létrehozhat [erőforrások közötti lekérdezési](../log-query/cross-workspace-query.md) riasztási szabályok jelentkezzen anélkül, hogy a kapcsoló használatával a [scheduledQueryRules API az ARM-sablon](../platform/alerts-log.md#log-alert-with-cross-resource-query-using-azure-resource-template) –, de ez a riasztási szabály kezelhető azonban [ scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) és nem az Azure Portalról.

Például ha az összekötő leállt a 2018-11-01, amikor, naplók az Application Insights-erőforrások és alkalmazások adatok lekérdezése a munkaterületen, a lekérdezés lenne kell kialakítani, az alábbi példához hasonlóan:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Az Application Insights és a Log Analytics munkaterület között
Az alábbi táblázat a Log Analytics és az Application Insights séma különbségeit.  

| Log Analytics-munkaterület tulajdonságai| Application Insights erőforrás-tulajdonságok|
|------------|------------| 
| AnonUserId | user_id|
| Alkalmazásazonosító | appId|
| Alkalmazásnév | Alkalmazásnév|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | az elemek száma |
| AvailabilityDuration | Időtartam |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | id |
| AvailabilityTestName | name |
| AvailabilityTimestamp | timestamp |
| Böngésző | client_browser |
| Város | client_city |
| ClientIP | client_IP |
| Computer | cloud_RoleInstance | 
| Ország | client_CountryOrRegion | 
| CustomEventCount | az elemek száma | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | az elemek száma | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| ExceptionType | type |
| Műveletazonosító: | operation_id |
| OperationName | operation_Name | 
| Operációs rendszer | client_OS | 
| PageViewCount | az elemek száma |
| PageViewDuration | Időtartam | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | az elemek száma | 
| RequestDuration | Időtartam | 
| Kérelemazonosító: | id | 
| RequestName | name | 
| RequestSuccess | sikeres | 
| ResponseCode | Eredménykód | 
| Szerepkör | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| munkamenet-azonosító | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | type |
| URL-cím | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>További lépések

Használat [naplóbeli keresés](../../azure-monitor/log-query/log-query-overview.md) részletes információk az Application Insights-alkalmazások megtekintéséhez.
