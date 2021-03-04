---
title: Erőforrások lekérdezése Azure Monitorsal | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet lekérdezéseket lekérdezni több munkaterületről és az App betekintő alkalmazásból származó erőforrásokról az előfizetésben.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/22/2020
ms.openlocfilehash: 57ed43b25c9031138a91f0870d316e1ae7a07a5b
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030967"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Napló lekérdezés végrehajtása a munkaterületeken és alkalmazásokon átnyúló Azure Monitorekben

Azure Monitor naplók támogatják a lekérdezéseket több Log Analytics munkaterületen, és Application Insights alkalmazás ugyanabban az erőforráscsoport, egy másik erőforráscsoport vagy egy másik előfizetés között. Ez teljes körű áttekintést nyújt az adatairól.

A több munkaterületen és alkalmazásokban tárolt adatlekérdezés két módszerből áll:
1. Explicit módon a munkaterület és az alkalmazás részleteinek megadásával. Ez a technika részletesen szerepel ebben a cikkben.
2. Implicit módon használja az [erőforrás-környezet lekérdezéseit](./design-logs-deployment.md#access-mode). Ha egy adott erőforrás, erőforráscsoport vagy előfizetés kontextusában kérdez le adatokat, a rendszer beolvassa a kapcsolódó adatokat az összes olyan munkaterületről, amely ezen erőforrások adatait tartalmazza. Az alkalmazásokban tárolt adatApplication Insights nem lesznek beolvasva.

> [!IMPORTANT]
> Ha [munkaterületen alapuló Application Insights erőforrás-](../app/create-workspace-resource.md) telemetria használ, a rendszer egy log Analytics munkaterületen tárolja az összes többi naplózási adattal. A munkaterület () kifejezés használatával olyan lekérdezést írhat, amely több munkaterületen is tartalmaz alkalmazást. Ugyanazon a munkaterületen több alkalmazás esetében nincs szükség több munkaterület-lekérdezésre.


## <a name="cross-resource-query-limits"></a>Erőforrások közötti lekérdezési korlátok 

* Az egyetlen lekérdezésben felvehető Application Insights-erőforrások és Log Analytics-munkaterületek száma legfeljebb 100.
* Az erőforrások közötti lekérdezés nem támogatott a Tervező nézetében. Létrehozhat egy lekérdezést a Log Analyticsban, és rögzítheti az Azure-irányítópulton [egy napló lekérdezésének megjelenítéséhez](../visualize/tutorial-logs-dashboards.md). 
* A naplózási riasztásokban az erőforrások közötti lekérdezések csak a jelenlegi [SCHEDULEDQUERYRULES API](/rest/api/monitor/scheduledqueryrules)-ban támogatottak. Ha az örökölt Log Analytics riasztások API-t használja, át kell [váltania az aktuális API](../alerts/alerts-log-api-switch.md)-ra.


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Lekérdezés Log Analytics munkaterületeken és a Application Insights
Ha a lekérdezésben egy másik munkaterületre szeretne hivatkozni, használja a [*munkaterület*](../logs/workspace-expression.md) -azonosítót, és Application Insights alkalmazásból származó alkalmazáshoz használja az [*alkalmazás*](./app-expression.md) azonosítóját.  

### <a name="identifying-workspace-resources"></a>Munkaterület-erőforrások azonosítása
Az alábbi példák bemutatják Log Analytics munkaterületek lekérdezéseit, hogy a *contosoretail* nevű munkaterület frissítési táblájában lévő naplók összesített számát adja vissza. 

A munkaterület azonosításához többféleképpen is elvégezhető:

* Erőforrás neve – a munkaterület egy ember által olvasható neve, más néven az *összetevő neve*. 

    `workspace("contosoretail-it").Update | count`

* Minősített név – a munkaterület "teljes neve", amely az előfizetés nevét, az erőforráscsoportot és az összetevő nevét alkotja ebben a formátumban: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Mivel az Azure-előfizetések nevei nem egyediek, lehet, hogy ez az azonosító nem egyértelmű. 
    >

* Munkaterület-azonosító – a munkaterület-azonosító a globálisan egyedi azonosítóként (GUID) jelölt minden munkaterülethez rendelt egyedi, nem módosítható azonosító.

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure Resource ID – a munkaterület Azure által meghatározott egyedi identitása. Az erőforrás-azonosítót akkor használja, ha az erőforrás neve nem egyértelmű.  A munkaterületek formátuma a következő: */Subscriptions/subscriptionId/resourcegroups/resourceGroup/Providers/Microsoft. OperationalInsights/munkaterületek/componentName*.  

    Például:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Alkalmazás azonosítása
Az alábbi példák egy *fabrikamapp* nevű alkalmazásra vonatkozó kérelmek összesített számát adják vissza Application Insights. 

Application Insights alkalmazásának azonosítására az alkalmazás *(azonosító)* kifejezés használható.  Az *azonosító* argumentum a következők egyikét használja az alkalmazáshoz:

* Erőforrás neve – az alkalmazás egy ember által olvasható neve, más néven az *összetevő neve*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Az alkalmazások név szerinti azonosítása feltételezi az összes elérhető előfizetés egyediségét. Ha több alkalmazás is van a megadott névvel, a lekérdezés a kétértelműség miatt meghiúsul. Ebben az esetben a többi azonosító egyikét kell használnia.

* Minősített név – az alkalmazás "teljes neve", amely az előfizetés neve, az erőforráscsoport és az összetevő neve, ebben a formátumban: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Mivel az Azure-előfizetések nevei nem egyediek, lehet, hogy ez az azonosító nem egyértelmű. 
    >

* AZONOSÍTÓ – az alkalmazás alkalmazás-GUID azonosítója.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure Resource ID – az alkalmazás Azure-ban meghatározott egyedi identitása. Az erőforrás-azonosítót akkor használja, ha az erőforrás neve nem egyértelmű. A formátum: */Subscriptions/subscriptionId/resourcegroups/resourceGroup/Providers/Microsoft. OperationalInsights/Components/componentName*.  

    Például:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Lekérdezés végrehajtása több erőforrás között
Bármelyik erőforrás-példányból több erőforrást is lekérdezheti, ezek lehetnek munkaterületek és alkalmazások együttes használata.
    
Példa két munkaterületre vonatkozó lekérdezésre:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Több erőforrás közötti lekérdezés használata több erőforráshoz
Ha több Log Analytics munkaterületről származó adatok összekapcsolására és Application Insights erőforrásokra vonatkozó több erőforrás-lekérdezést használ, a lekérdezés bonyolult és nehézkesen kezelhető lesz. A függvények kihasználása [Azure monitor napló lekérdezésekben](./functions.md) a lekérdezési logika elkülönítése a lekérdezési erőforrások hatóköréről, ami leegyszerűsíti a lekérdezési struktúrát. Az alábbi példa bemutatja, hogyan figyelhető meg több Application Insights erőforrás, és Hogyan jeleníthető meg a sikertelen kérelmek száma az alkalmazás neve alapján. 

Hozzon létre egy, az alábbihoz hasonló lekérdezést, amely a Application Insights erőforrások hatókörére hivatkozik. A `withsource= SourceApp` parancs egy oszlopot hoz létre, amely kijelöli a naplót küldő alkalmazás nevét. [Mentse a lekérdezést függvényként](./functions.md#create-a-function) az alias _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



[Ezt a függvényt](./functions.md#use-a-function) mostantól egy több erőforrást tartalmazó lekérdezésben is használhatja, például a következőhöz. A _applicationsScoping_ függvény alias a kérelmek tábla unióját adja vissza az összes megadott alkalmazásból. A lekérdezés ezután szűri a sikertelen kérelmeket, és alkalmazás szerint jeleníti meg a trendeket. Ebben a példában az _elemzési_ operátor nem kötelező. Kibontja az alkalmazás nevét a _SourceApp_ tulajdonságból.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Ez a metódus nem használható a naplózási riasztásokkal, mert a riasztási szabály erőforrásainak, köztük a munkaterületeknek és az alkalmazásoknak a hozzáférés-ellenőrzését a riasztás létrehozási ideje végzi. Új erőforrások hozzáadása a függvényhez a riasztás létrehozása után nem támogatott. Ha az erőforrás-hatókör függvényét szeretné használni a naplózási riasztásokban, szerkesztenie kell a riasztási szabályt a portálon, vagy egy Resource Manager-sablonnal a hatókörrel rendelkező erőforrások frissítéséhez. Azt is megteheti, hogy felveszi az erőforrások listáját a napló riasztási lekérdezésében.


![Idődiagramját](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Következő lépések

- Tekintse át a naplófájlok [elemzését Azure monitor](./log-query-overview.md) a naplók áttekintését, valamint a Azure monitor naplózási adatai strukturált módját.