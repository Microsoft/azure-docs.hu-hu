---
title: Hogyan... Az Azure Application Insightsban | Microsoft Docs
description: Gyakori kérdések a Application Insights.
ms.topic: conceptual
ms.date: 04/04/2017
ms.openlocfilehash: 74a4d7ee65dccead132cfcebd9bf8c0de9b761a5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584158"
---
# <a name="how-do-i--in-application-insights"></a>Hogyan tegyem... az Application Insights szolgáltatásban?
## <a name="get-an-email-when-"></a>E-mail küldése, ha...
### <a name="email-if-my-site-goes-down"></a>E-mail, ha a webhely leáll
Adja meg a [rendelkezésre állási webes tesztet](./monitor-web-app-availability.md).

### <a name="email-if-my-site-is-overloaded"></a>E-mail-cím, ha a webhely túl van terhelve
[Riasztás](../alerts/alerts-log.md) beállítása a **kiszolgáló válaszideje**. Egy 1 és 2 másodperc közötti küszöbértéknek kell működnie.

![Képernyőkép, amely bemutatja, hogyan állítható be a riasztás a kiszolgáló válaszideje alapján.](./media/how-do-i/030-server.png)

Az alkalmazás a meghibásodási kódok visszaadásával is megjelenítheti a törzs jeleit. Riasztás beállítása a **sikertelen kérelmeknél**.

Ha riasztást szeretne beállítani a **kiszolgálók kivételei** között, akkor előfordulhat, hogy [további beállításokat](./asp-net-exceptions.md) kell megtennie az adatmegjelenítéshez.

### <a name="email-on-exceptions"></a>Kivételek e-mail-címe
1. [Kivételek figyelésének beállítása](./asp-net-exceptions.md)
2. [Riasztás beállítása](../alerts/alerts-log.md) a kivételek számának metrikája

### <a name="email-on-an-event-in-my-app"></a>E-mail küldése az alkalmazáson belüli eseményről
Tegyük fel, hogy egy adott esemény bekövetkezésekor szeretne e-mailt kapni. Application Insights nem biztosítja ezt a létesítményt közvetlenül, de [riasztást küldhet, ha egy metrika átlép egy küszöbértéket](../alerts/alerts-log.md).

A riasztások [Egyéni metrikák](./api-custom-events-metrics.md#trackmetric)esetén is beállíthatók, de nem egyéni események. Írjon be egy kódot a metrika növeléséhez az esemény bekövetkezésekor:

```csharp
telemetry.TrackMetric("Alarm", 10);
```

vagy:

```csharp
var measurements = new Dictionary<string,double>();
measurements ["Alarm"] = 10;
telemetry.TrackEvent("status", null, measurements);
```

Mivel a riasztások két állapottal rendelkeznek, alacsony értéket kell küldenie, amikor a riasztást véget ér:

```csharp
telemetry.TrackMetric("Alarm", 0.5);
```

Diagram létrehozása a [metrika Explorerben](../essentials/metrics-charts.md) a riasztás megtekintéséhez:

![Képernyőfelvétel: diagram létrehozása a metrika Explorerben a riasztás megtekintéséhez.](./media/how-do-i/010-alarm.png)

Most állítsa be a riasztást, ha a metrika rövid ideig meghaladja a középső értéket:

![Képernyőkép, amely bemutatja, hogyan állítható be a riasztás, ha a metrika rövid ideig a középső érték fölé kerül.](./media/how-do-i/020-threshold.png)

Állítsa az átlagos időszakot a minimum értékre.

Az e-maileket akkor is megkapja, ha a metrika a küszöbérték fölött és alatt marad.

Néhány megfontolandó szempont:

* A riasztások két állapottal rendelkeznek ("riasztás" és "kifogástalan"). Az állapot csak akkor lesz kiértékelve, ha metrika érkezik.
* A rendszer csak akkor küld e-mailt, ha az állapot megváltozik. Ezért a magas és az alacsony értékű mérőszámokat is el kell küldenie.
* A riasztás kiértékeléséhez az átlagot a rendszer az előző időszakban kapott értékek alapján veszi figyelembe. Ez minden alkalommal megtörténik, amikor egy metrika érkezik, így a megadott időszaknál gyakrabban lehet elküldeni az e-maileket.
* Mivel az e-maileket a "riasztás" és az "kifogástalan" is elküldi, érdemes lehet átgondolni az egyeseményes eseményt egy kétállapotú feltételnek. Például a "feladatok befejezve" esemény helyett a "feladatok folyamatban" állapotot kell megadni, amely a feladatok elején és végén kap e-maileket.

### <a name="set-up-alerts-automatically"></a>Riasztások automatikus beállítása
[Új riasztások létrehozása a PowerShell használatával](../alerts/alerts-log.md)

## <a name="use-powershell-to-manage-application-insights"></a>Application Insights kezelése a PowerShell használatával
* [Új erőforrások létrehozása](./create-new-resource.md#creating-a-resource-automatically)
* [Új riasztások létrehozása](../alerts/alerts-log.md)

## <a name="separate-telemetry-from-different-versions"></a>Különálló telemetria különböző verziókból

* Több szerepkör egy alkalmazásban: használjon egyetlen Application Insights erőforrást, és szűrje a [cloud_Rolename](./app-map.md).
* A fejlesztési, tesztelési és kiadási verziók elkülönítése: használjon különböző Application Insights erőforrásokat. Vegye fel a rendszerállapot-kulcsokat a web.configból. [További információ](./separate-resources.md)
* Jelentéskészítési Build verziók: adjon hozzá egy tulajdonságot egy telemetria inicializáló használatával. [További információ](./separate-resources.md)

## <a name="monitor-backend-servers-and-desktop-apps"></a>Háttérbeli kiszolgálók és asztali alkalmazások figyelése
[Használja a Windows Server SDK-modult](./windows-desktop.md).

## <a name="visualize-data"></a>Adatok vizualizációja
#### <a name="dashboard-with-metrics-from-multiple-apps"></a>Irányítópult több alkalmazás metrikákkal
* A [metrika Explorerben](../essentials/metrics-charts.md)szabja testre a diagramot, és mentse kedvencként. Rögzítse az Azure-irányítópulton.

#### <a name="dashboard-with-data-from-other-sources-and-application-insights"></a>Irányítópult más forrásokból származó adatokkal és Application Insights
* [Telemetria exportálása a Power BIba](./export-power-bi.md).

Vagy

* A SharePoint használata irányítópultként, a SharePoint-kijelzők adatait jeleníti meg. A [folyamatos exportálás és a stream Analytics használatával exportálhat SQL-](./code-sample-export-sql-stream-analytics.md)re.  A PowerView használatával vizsgálja meg az adatbázist, és hozzon létre egy SharePoint-kijelzőt a PowerView számára.

<a name="search-specific-users"></a>

### <a name="filter-out-anonymous-or-authenticated-users"></a>Névtelen vagy hitelesített felhasználók kiszűrése
Ha a felhasználók bejelentkeznek, beállíthatja a [hitelesített felhasználói azonosítót](./api-custom-events-metrics.md#authenticated-users). (Ez nem automatikusan történik.)

Ezután:

* Keresés adott felhasználói azonosítókban

![Képernyőkép, amely az adott felhasználói azonosítók keresve beállításait mutatja.](./media/how-do-i/110-search.png)

* Mérőszámok szűrése névtelen vagy hitelesített felhasználók számára

![A névtelen vagy hitelesített felhasználók szűrési metrixs megjelenítő képernyőkép.](./media/how-do-i/115-metrics.png)

## <a name="modify-property-names-or-values"></a>Tulajdonságok nevének vagy értékének módosítása
Hozzon létre egy [szűrőt](./api-filtering-sampling.md#filtering). Ez lehetővé teszi a telemetria módosítását vagy szűrését az alkalmazásból a Application Insightsba való elküldése előtt.

## <a name="list-specific-users-and-their-usage"></a>Adott felhasználók és azok használatának listázása
Ha csak [bizonyos felhasználókat szeretne keresni](#search-specific-users), beállíthatja a [hitelesített felhasználói azonosítót](./api-custom-events-metrics.md#authenticated-users).

Ha olyan felhasználók listáját szeretné megtekinteni, amelyeknek az információi, például hogy milyen lapokat látnak vagy milyen gyakran jelentkeznek be, két lehetőség közül választhat:

* [Állítsa be a hitelesített felhasználói azonosítót](./api-custom-events-metrics.md#authenticated-users), [exportáljon egy adatbázisba](./code-sample-export-sql-stream-analytics.md) , és használjon megfelelő eszközöket a felhasználói adatai elemzéséhez.
* Ha csak kis számú felhasználóval rendelkezik, küldjön egyéni eseményeket vagy metrikákat, a kamatot a metrika értékeként vagy az esemény neveként használja, és állítsa be a felhasználói azonosítót tulajdonságként. Az oldalletöltések elemzéséhez cserélje le a szokásos JavaScript trackPageView hívást. A kiszolgálóoldali telemetria elemzéséhez egy telemetria inicializáló használatával adja hozzá a felhasználói azonosítót az összes kiszolgáló telemetria. Ezután szűrheti és szegmentálhatja a mérőszámokat, és megkeresheti a felhasználói azonosítót.

## <a name="reduce-traffic-from-my-app-to-application-insights"></a>Az alkalmazásból a Application Insightsre irányuló forgalom csökkentése
* A [ApplicationInsights.configban ](./configuration-with-applicationinsights-config.md)tiltsa le a nem szükséges modulokat, például a teljesítményszámláló gyűjtőjét.
* [Mintavétel és szűrés](./api-filtering-sampling.md) használata az SDK-ban.
* A weblapokon korlátozza az összes oldal nézethez jelentett Ajax-hívások számát. A parancsfájl-kódrészletbe `instrumentationKey:...` szúrja be a `,maxAjaxCallsPerView:3` következőt: (vagy egy megfelelő szám).
* Ha a [TrackMetric](./api-custom-events-metrics.md#trackmetric)-t használja, akkor az eredmény elküldése előtt számítsa ki a metrikai értékek kötegének összesítését. A TrackMetric () túlterhelést biztosít.

További információ a [díjszabásról és a kvótáról](./pricing.md).

## <a name="disable-telemetry"></a>Telemetria letiltása
A telemetria a kiszolgálóról történő gyűjtésének és továbbításának **dinamikus leállítása és elindítása** :

### <a name="aspnet-classic-applications"></a>Klasszikus alkalmazások ASP.NET

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

### <a name="other-applications"></a>Egyéb alkalmazások
Az Egypéldányos `TelemetryConfiguration.Active` konzolos vagy ASP.net Core alkalmazások használata nem ajánlott.
Ha `TelemetryConfiguration` saját maga hozta létre a példányt – állítsa a következőre: `DisableTelemetry` `true` .

ASP.NET Core alkalmazásokhoz `TelemetryConfiguration` [ASP.net Core függőségi befecskendezés](/aspnet/core/fundamentals/dependency-injection/)használatával férhet hozzá a példányhoz. A [ASP.net Core-alkalmazások ApplicationInsights kapcsolatos](./asp-net-core.md) további részletekért tekintse meg a következő cikket:.

## <a name="disable-selected-standard-collectors"></a>Kiválasztott standard gyűjtők letiltása
Letilthatja a standard gyűjtőket (például teljesítményszámlálók, HTTP-kérelmek vagy függőségek).

* **ASP.NET-alkalmazások** – az [ApplicationInsights.config](./configuration-with-applicationinsights-config.md) megfelelő sorainak törlése vagy megjegyzése
* **ASP.net Core alkalmazások** – kövesse a telemetria-modulok konfigurációs beállításait a [ApplicationInsights ASP.net Core](./asp-net-core.md#configuring-or-removing-default-telemetrymodules)

## <a name="view-system-performance-counters"></a>Rendszerteljesítmény-számlálók megtekintése
A mérőszámokban megjeleníthető mérőszámok közé tartoznak a rendszerteljesítményszámlálók. Van egy előre definiált panel, amely többek között megjeleníti a **kiszolgálókat** .

![Nyissa meg Application Insights erőforrását, és kattintson a kiszolgálók elemre](./media/how-do-i/121-servers.png)

### <a name="if-you-see-no-performance-counter-data"></a>Ha nem jelenik meg a teljesítményszámláló adatai
* **IIS-kiszolgáló** a saját számítógépén vagy egy virtuális gépen. [Telepítse a Állapotmonitor](./monitor-performance-live-website-now.md).
* **Azure-webhely** – a teljesítményszámlálók még nem támogatottak. Az Azure-webhely Vezérlőpultjának standard részeként több mérőszám is elérhető.
* **UNIX-kiszolgáló**  -  A [telepítés begyűjtve](./java-collectd.md)

### <a name="to-display-more-performance-counters"></a>További teljesítményszámlálók megjelenítése
* Először [vegyen fel egy új diagramot](../essentials/metrics-charts.md) , és ellenőrizze, hogy a számláló az általunk kínált alapszintű készletben van-e.
* Ha nem, [adja hozzá a számlálót a teljesítményszámláló modul által összegyűjtött készlethez](./performance-counters.md).

