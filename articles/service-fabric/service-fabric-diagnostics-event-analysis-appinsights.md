---
title: Azure Service Fabric Event Analysis Application Insights
description: Ismerje meg, hogyan jelenítheti meg és elemezheti az eseményeket Application Insights használatával az Azure Service Fabric-fürtök monitorozásához és diagnosztizálásához.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: f4d77cc3b2f2182b02d47f047070b819daa5f3c1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589021"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Események elemzése és vizualizáció Application Insights

Azure Monitor részeként Application Insights egy bővíthető platform az alkalmazások monitorozásához és diagnosztizálásához. Hatékony elemzési és lekérdezési eszközt, testreszabható irányítópultot és vizualizációkat tartalmaz, valamint további lehetőségeket, például automatizált riasztásokat. A Application Insights integrációja Service Fabric magában foglalja a Visual Studio és a Azure Portal, valamint Service Fabric konkrét mérőszámok bevezetését, amely átfogó, beépített naplózási felületet biztosít. Bár a rendszer sok naplót hoz létre és gyűjt a Application Insightsával, javasoljuk, hogy további egyéni naplózást adjon hozzá az alkalmazásaihoz, hogy gazdagabb diagnosztikai élményt hozzon létre.

Ez a cikk a következő gyakori kérdésekre nyújt segítséget:

* Hogyan tudni, hogy mi történik az alkalmazáson és a Szolgáltatásokon belül, és gyűjtsön telemetria?
* Hogyan az alkalmazással kapcsolatos hibakeresést, különösen az egymással kommunikáló szolgáltatásokat?
* Hogyan mérőszámok beszerzése a szolgáltatások teljesítményéről, például az oldal betöltési ideje, a HTTP-kérések?

Ennek a cikknek a célja, hogy bemutassa, hogyan szerezhet be betekintést és hibakeresést Application Insightson belülről. Ha meg szeretné tudni, hogyan állíthatja be és konfigurálja a Application Insightst a Service Fabric, tekintse meg ezt az [oktatóanyagot](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Figyelés Application Insights

A Service Fabric használatakor a Application Insights gazdag a box-ban. Az Áttekintés oldalon Application Insights a szolgáltatással kapcsolatos legfontosabb információkat biztosít, például a válaszidő és a feldolgozott kérelmek száma. A felső részen található Keresés gombra kattintva megtekintheti az alkalmazásban elérhető legutóbbi kérelmek listáját. Emellett a sikertelen kérelmeket itt is megtekintheti, és diagnosztizálhatja, hogy milyen hibák fordultak elő.

![Application Insights áttekintése](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

A fenti képen a jobb oldali panelen két fő típusú bejegyzés szerepel a listán: kérelmek és események. A kérések az alkalmazás API-n keresztül, ebben az esetben HTTP-kérelmeken keresztül kezdeményezett hívások, az események pedig egyéni események, amelyek telemetria a kódban bárhol felvehetők. Az alkalmazások Application Insights API-ban való kialakításának megismerése az [Egyéni események és mérőszámok esetében](../azure-monitor/app/api-custom-events-metrics.md)továbbra is megtekinthető. A kérelemre kattintva további részleteket jeleníthet meg az alábbi ábrán látható módon, beleértve a Service Fabricra vonatkozó adatokat is, amelyeket a Application Insights Service Fabric NuGet csomagban gyűjtöttek be. Ez az információ hasznos lehet a hibaelhárításhoz és az alkalmazás állapotának megismeréséhez, és az összes információ kereshető a Application Insightson belül

![Képernyőkép, amely további részleteket jelenít meg, beleértve a Service Fabricra vonatkozó adatokat, amelyeket a rendszer a Application Insights Service Fabric NuGet csomagban gyűjt.](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

Application Insights rendelkezik egy kijelölt nézettel, amely a-ban található összes adattal lekérdezhető. Az Áttekintés lap tetején válassza a "Metrikaböngésző" lehetőséget, hogy az Application Insights portálra navigáljon. Itt a Kusto lekérdezési nyelv használatával futtathat lekérdezéseket a korábban említett egyéni események, kérések, kivételek, teljesítményszámlálók és egyéb mérőszámok alapján. Az alábbi példa az összes kérelmet az elmúlt 1 órában mutatja.

![Application Insights kérelem részletei](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

A Application Insights-portál képességeinek megismeréséhez lépjen a [Application Insights portál dokumentációjában](../azure-monitor/app/overview-dashboard.md).

### <a name="configuring-application-insights-with-eventflow"></a>Application Insights konfigurálása a EventFlow segítségével

Ha EventFlow segítségével-t használ az események összesítéséhez, akkor ügyeljen arra, hogy importálja a `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` NuGet-csomagot. A következő kódnak kell megadnia a *eventFlowConfig.js* *kimenet* szakaszában:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Győződjön meg arról, hogy a szűrők szükséges módosításai, valamint a többi bemenet (a megfelelő NuGet-csomagokkal együtt) is elérhetők.

## <a name="application-insights-sdk"></a>Application Insights SDK

Azt javasoljuk, hogy a EventFlow segítségével és a WAD-et aggregációs megoldásként használja, mivel ezek lehetővé teszik a diagnosztika és a monitorozás moduláris megközelítését, vagyis ha módosítani kívánja a EventFlow segítségével származó kimeneteket, akkor nem szükséges módosítania a tényleges rendszerállapotot, csak egy egyszerű módosítást a konfigurációs fájlhoz. Ha úgy dönt, hogy Application Insightst használ, és nem valószínű, hogy másik platformra vált át, érdemes megtekinteni a Application Insights "új SDK használata az események összesítéséhez és a Application Insights elküldéséhez. Ez azt jelenti, hogy többé nem kell konfigurálnia az EventFlow segítségével, hogy az adatait Application Insightsba küldje el, hanem telepíti a ApplicationInsight Service Fabric NuGet-csomagot. A csomag részletei [itt](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)találhatók.

A Application Insights-szolgáltatások [és-tárolók támogatása](https://azure.microsoft.com/blog/app-insights-microservices/) megmutatja, hogy a jelenleg használt új funkciók közül néhányat (még a bétaverzióban is) használ, amely lehetővé teszi, hogy a Application Insightsokkal gazdagabb, beépített figyelési lehetőségeket biztosítson. Ezek közé tartozik a függőségek nyomon követése (amely a fürt összes szolgáltatásának és alkalmazásának Alkalmazástérkép, valamint a közöttük zajló kommunikációt foglalja magában), valamint a szolgáltatásokból érkező Nyomkövetések jobb korrelációját (az alkalmazások vagy szolgáltatások munkafolyamataiban felmerülő problémák jobb megjelölését segíti).

Ha a .NET-ben fejleszti a fejlesztést, és valószínűleg a Service Fabric programozási modelljét fogja használni, és készen áll a Application Insights használatára az események és a naplózási adatok megjelenítéséhez és elemzéséhez, akkor javasoljuk, hogy a figyelési és diagnosztikai munkafolyamaton keresztül lépjen az Application Insights SDK-útvonalra. Olvassa el a [Application Insights dokumentációs](../azure-monitor/azure-monitor-app-hub.yml) és [nyomkövetési naplók dokumentációját](../azure-monitor/app/asp-net-trace-logs.md) , amelyből megtudhatja, hogyan gyűjthet és jeleníthet meg naplókat a Application Insights használatával.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navigálás a Application Insights erőforrásban Azure Portal

Miután konfigurálta Application Insights az események és naplók kimenete, néhány percen belül meg kell jelennie a Application Insights erőforrásban. Navigáljon a Application Insights erőforráshoz, amely végigvezeti a Application Insights erőforrás-irányítópulton. A Application Insights tálcán válassza a **Keresés** lehetőséget, hogy megtekintse a kapott legújabb nyomokat, és hogy képes legyen szűrni rajtuk.

*Metrikaböngésző* egy hasznos eszköz az egyéni irányítópultok létrehozásához az alkalmazások, szolgáltatások és a fürt által jelentett mérőszámok alapján. A begyűjtött adatok alapján több diagramot is beállíthat a [Application Insights a metrikák feltárásával](../azure-monitor/essentials/metrics-charts.md) kapcsolatban.

Az **elemzés** gombra kattintva megtekintheti a Application Insights Analytics-portált, ahol az eseményeket és a nyomkövetéseket nagyobb hatókörrel és választható módon kérdezheti le. További információ erről a [Application Insights Analytics szolgáltatásában](../azure-monitor/logs/log-query-overview.md)olvasható.

## <a name="next-steps"></a>Következő lépések

* [Riasztások beállítása az AI-ben](../azure-monitor/alerts/alerts-log.md) a teljesítmény vagy a használat változásairól való értesítéshez
* Az [intelligens észlelés a Application Insights](../azure-monitor/app/proactive-diagnostics.md) a Application Insights küldött telemetria proaktív elemzését hajtja végre, hogy figyelmeztesse Önt a lehetséges teljesítménnyel kapcsolatos problémákról
