---
title: Lekérdezés erőforrások között Azure Monitor | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet több munkaterületről és az előfizetésében lévő App Insights-alkalmazásból származó erőforrásokat lekérdezni.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/11/2021
ms.openlocfilehash: 19cc85751fc5e4a165b646ac89d9d6b6e90c4408
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379553"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Naplólekérdezés végrehajtása Azure Monitor több munkaterületre és alkalmazásra is

Azure Monitor naplók támogatják a több Log Analytics-munkaterületre és Application Insights egy adott erőforráscsoportban, másik erőforráscsoportban vagy egy másik előfizetésben található alkalmazás lekérdezését. Így rendszer szintű áttekintést nyújt az adatokról.

A több munkaterületen és alkalmazásban tárolt adatok lekérdezésének két módja van:
1. Explicit módon, a munkaterület és az alkalmazás részleteinek megadásával. Ezt a technikát ez a cikk részletezi.
2. Implicit módon használja az [erőforrás-környezet lekérdezéseit.](./design-logs-deployment.md#access-mode) Ha egy adott erőforrás, erőforráscsoport vagy előfizetés kontextusában lekérdezést ad meg, a releváns adatokat a rendszer az erőforrásokhoz kapcsolódó adatokat tartalmazó összes munkaterületről lekéri. Application Insights alkalmazásokban tárolt adatok nem lesznek lekérve.

> [!IMPORTANT]
> Ha munkaterület-alapú [erőforrás-Application Insights a](../app/create-workspace-resource.md) rendszer az összes többi naplóadatot tartalmazó Log Analytics-munkaterületen tárolja. A workspace() kifejezéssel olyan lekérdezést írhat, amely több munkaterületen lévő alkalmazást is tartalmaz. Az ugyanazon a munkaterületen lévő több alkalmazáshoz nincs szükség több munkaterületre vonatkozó lekérdezésre.


## <a name="cross-resource-query-limits"></a>Több erőforrásra vonatkozó lekérdezési korlátok 

* Az Application Insights és Log Analytics-munkaterületek száma, amelyek egy lekérdezésben foglalhatóak bele, legfeljebb 100 lehet.
* A több erőforrásra vonatkozó lekérdezés nem támogatott a Nézettervező. A Log Analyticsben lekérdezést hozhat létre, és rögzítheti azt az Azure-irányítópulton [egy](../visualize/tutorial-logs-dashboards.md) naplólekérdezés megjelenítéséhez, vagy a [Munkafüzetekbe való beágyazása céljából.](../visualize/workbooks-overview.md)
* A naplóriasztás erőforrásközi lekérdezései csak az aktuális [ütemezettQueryRules API-ban támogatottak.](/rest/api/monitor/scheduledqueryrules) Ha az örökölt Log Analytics Alerts API-t használja, át kell váltania az [aktuális API-re.](../alerts/alerts-log-api-switch.md)


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Lekérdezés a Log Analytics-munkaterületeken és a Application Insights
A lekérdezés egy másik munkaterületére [](../logs/workspace-expression.md) való hivatkozáshoz használja a munkaterület azonosítóját, a Application Insights [*alkalmazásazonosítót*](./app-expression.md) használja.  

### <a name="identifying-workspace-resources"></a>Munkaterület-erőforrások azonosítása
Az alábbi példák a Log Analytics-munkaterületek lekérdezéseit mutatják be a naplók összesített darabszámának visszaadására a *contosoretail-it* nevű munkaterület Update táblájában. 

A munkaterületek azonosítása többféleképpen is lehetséges:

* Erőforrás neve – a munkaterület ember által olvasható neve, más néven *összetevőnév.* 

    >[!IMPORTANT]
    >Mivel az alkalmazás- és munkaterületnevek nem egyediek, ez az azonosító nem egyértelmű lehet. Ajánlott minősített név, munkaterület-azonosító vagy Azure-erőforrásazonosító alapján hivatkozni.

    `workspace("contosoretail-it").Update | count`

* Minősített név – a munkaterület "teljes neve", amely az előfizetés nevéből, az erőforráscsoportból és az összetevő nevéből áll a következő formátumban: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Mivel az Azure-előfizetések nevei nem egyediek, ez az azonosító nem egyértelmű lehet.

* Munkaterület azonosítója – A munkaterület-azonosító a globálisan egyedi azonosítóként (GUID) ábrázolt munkaterületek egyedi, nem módosítható azonosítója.

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Azure-erőforrásazonosító – a munkaterület Azure-ban meghatározott egyedi identitása. Akkor használja az erőforrás-azonosítót, ha az erőforrás neve nem egyértelmű.  Munkaterületek esetén a formátum a következő: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/workspaces/componentName*.  

    Például:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Alkalmazás azonosítása
Az alábbi példák egy *fabrikamapp* nevű alkalmazásra vonatkozó kérelmek összesített számát adja vissza a Application Insights. 

Egy alkalmazás azonosítása a Application Insights az *app(Identifier)* kifejezéssel valósítható meg.  Az *Identifier* argumentum az alábbi lehetőségek egyikével határozza meg az alkalmazást:

* Erőforrás neve – az alkalmazás emberi olvasásra használható neve, más néven *az összetevő neve.*  

    `app("fabrikamapp")`

    >[!NOTE]
    >Az alkalmazások név alapján való azonosítása az összes elérhető előfizetés egyediségét feltételezi. Ha több alkalmazása van a megadott névvel, a lekérdezés a kétértelműség miatt meghiúsul. Ebben az esetben a többi azonosítót kell használnia.

* Minősített név – az alkalmazás "teljes neve", amely az előfizetés nevéből, az erőforráscsoportból és az összetevő nevéből áll a következő formátumban: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Mivel az Azure-előfizetések nevei nem egyediek, ez az azonosító nem egyértelmű lehet. 
    >

* Azonosító – az alkalmazás GUID azonosítója.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Azure-erőforrásazonosító – az alkalmazás Azure által meghatározott egyedi identitása. Akkor használja az erőforrás-azonosítót, ha az erőforrás neve nem egyértelmű. A formátum a következő: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/components/componentName*.  

    Például:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Lekérdezés végrehajtása több erőforráson
Több erőforrást is lekérdezhet bármelyik erőforráspéldányból. Ezek munkaterületek és alkalmazások kombinálhatók.
    
Példa két munkaterületen való lekérdezésre:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Erőforrásközi lekérdezés használata több erőforráshoz
Ha több Log Analytics-munkaterületről származó adatokat összevető, több erőforrást Application Insights lekérdezéseket használ, a lekérdezés bonyolultsá és nehezen karbantarthatóvá válhat. A naplólekérdezésekben Azure Monitor [függvényeket kell](./functions.md) használnia a lekérdezési logika és a lekérdezési erőforrások hatókörének elválasztása érdekében, ami leegyszerűsíti a lekérdezési struktúrát. Az alábbi példa bemutatja, hogyan figyelhet több Application Insights erőforrást, és hogyan vizualizálhatja a sikertelen kérelmek számát alkalmazásnév szerint. 

Hozzon létre egy, az alábbihoz hasonló lekérdezést, amely a Application Insights hatókörére hivatkozik. A parancs hozzáad egy oszlopot, amely a naplót `withsource= SourceApp` küldött alkalmazásnevet jelöli. [Mentse a lekérdezést függvényként az](./functions.md#create-a-function) _applications aliassal Hatókörkezelés._

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Most már [használhatja ezt](./functions.md#use-a-function) a függvényt az alábbihoz hasonló, több erőforrásra vonatkozó lekérdezésben. Az _applicationsScoping_ függvény alias visszaadja a requests tábla unióját az összes meghatározott alkalmazásból. A lekérdezés ezután szűri a sikertelen kérelmeket, és alkalmazás szerint vizualizálja a trendeket. Ebben _a példában_ az parse operátor nem kötelező. Kinyeri az alkalmazás nevét a _SourceApp tulajdonságból._

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Ez a módszer nem használható naplóriasztásokkal, mert a riasztási szabály erőforrásainak hozzáférés-ellenőrzése, beleértve a munkaterületeket és az alkalmazásokat is, a riasztás létrehozásakor történik. A riasztás létrehozása után nem lehet új erőforrásokat hozzáadni a függvényhez. Ha inkább a függvényt szeretné használni az erőforrás-hatókörkezeléshez a naplóriasztásban, a hatókörön belül az erőforrások frissítéséhez szerkesztenie kell a riasztási szabályt a portálon vagy egy Resource Manager-sablonnal. Másik lehetőségként felveheti az erőforrások listáját a naplóriasztásos lekérdezésbe.


![Idődiagram](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Következő lépések

- A [naplólekérdezések áttekintését és](./log-query-overview.md) a naplóadatok Azure Monitor áttekintését Azure Monitor naplóadatok elemzését ismertető témakörben olvashat.