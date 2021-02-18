---
title: Az Azure Application Insights használatának és költségeinek kezelése | Microsoft Docs
description: Telemetria-kötetek kezelése és a költségek figyelése Application Insightsban.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: DaleKoetke
ms.author: dalek
ms.date: 2/7/2021
ms.reviewer: mbullwin
ms.openlocfilehash: db5506f1f1fcabf3a922115c24aa64e35b888fbd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573754"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Használat és költségek kezelése az Application Insights szolgáltatásban

> [!NOTE]
> Ez a cikk a Application Insights költségeinek megismerését és szabályozását ismerteti.  A kapcsolódó cikkek, a [monitorozási használat és a becsült költségek](..//usage-estimated-costs.md) azt írják le, hogyan lehet megtekinteni a használati és becsült költségeket több Azure-figyelési funkció között különböző díjszabási modellekhez.

Application Insights úgy lett kialakítva, hogy a webalkalmazások rendelkezésre állásának, teljesítményének és használatának nyomon követéséhez szükséges mindent meg lehessen figyelni, függetlenül attól, hogy az Azure-ban vagy a helyszínen vannak tárolva. Application Insights támogatja a népszerű nyelveket és keretrendszerek, például a .NET, a Java és a Node.js használatát, és integrálható a DevOps-folyamatokkal és-eszközökkel, például az Azure DevOps, a JIRA és a PagerDuty. Fontos tisztában lenni azzal, hogy mi határozza meg az alkalmazások monitorozásának költségeit. Ebben a cikkben áttekintjük, hogy mi vezet az alkalmazás figyelési költségeihez, és hogy miként lehet proaktívan figyelni és felügyelni őket.

Ha kérdése van a Application Insights díjszabásával kapcsolatban, tegye fel kérdéseit a [Microsoft Q&egy kérdés oldalára](/answers/topics/azure-monitor.html).

## <a name="pricing-model"></a>Díjszabási modell

Az [Azure Application Insights][start] díjszabása egy **utólagos** elszámolású modell, amely a betöltött adatmennyiségen és opcionálisan a hosszú adatmegőrzésen alapul. Minden Application Insights erőforrás külön szolgáltatásként lesz felszámítva, és hozzájárul az Azure-előfizetéshez tartozó számlához. Az adatmennyiséget a rendszer az alkalmazásból Application Insights által fogadott, tömörítetlen JSON-adatcsomag méretének megfelelően méri. A [élő metrikastream](./live-stream.md)használatához nincs adatmennyiség.

A [többlépéses webes tesztek](./availability-multistep.md) felár ellenében merülhetnek fel. A többlépéses webes tesztek olyan webes tesztek, amelyek műveletek sorozatát hajtják végre. Egyetlen oldal *pingelési tesztei* esetében nincs külön díj. A ping tesztekből és a többlépéses tesztekből származó telemetria az alkalmazás más telemetria azonos módon kell fizetni.

Az [Egyéni metrikai dimenziókkal kapcsolatos riasztások engedélyezésének](./pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) Application Insights lehetősége további költségeket is eredményezhet, mivel ez további előzetes összesítési mérőszámok létrehozását eredményezheti. [További](./pre-aggregated-metrics-log-metrics.md) információ a Application Insights naplózási és előre összesített mérőszámáról, valamint a Azure monitor egyéni metrikák [díjszabásáról](https://azure.microsoft.com/pricing/details/monitor/) .

### <a name="workspace-based-application-insights"></a>Munkaterület-alapú Application Insights

Olyan Application Insights-erőforrások esetében, amelyek az adatoknak a [munkaterület-alapú Application Insights erőforrások](create-workspace-resource.md)nevű log Analytics munkaterületre küldenek, az adatok betöltésének és megőrzésének számlázását azon munkaterület végzi el, ahol a Application Insights adatok találhatók. Ez lehetővé teszi az ügyfelek számára a Log Analytics [díjszabási modell](../logs/manage-cost-storage.md#pricing-model) összes beállítását, amely az utólagos elszámolású kapacitás fenntartását is magában foglalja. Log Analytics emellett több lehetőség is rendelkezésre áll az adatmegőrzésre, beleértve az [adattípusok megőrzését](../logs/manage-cost-storage.md#retention-by-data-type)is. A munkaterületen Application Insights adattípusok esetén a rendszer 90 napos megőrzési időt kap, díjak nélkül. A webes tesztek használata és a riasztások egyéni metrikai dimenziókban való engedélyezése továbbra is Application Insights keresztül történik. Megtudhatja, hogyan követheti nyomon az adatfeldolgozási és-megőrzési költségeket Log Analytics a [használati és becsült költségek](../logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs), [Azure Cost Management + számlázási](../logs/manage-cost-storage.md#viewing-log-analytics-usage-on-your-azure-bill) és [log Analytics lekérdezések](#data-volume-for-workspace-based-application-insights-resources)használatával. 

## <a name="estimating-the-costs-to-manage-your-application"></a>Az alkalmazás kezelésével kapcsolatos költségek becslése

Ha még nem használja az Application Insights-t, a [Azure monitor árképzési számológép](https://azure.microsoft.com/pricing/calculator/?service=monitor) használatával megbecsülheti a Application Insights használatának költségeit. Először írja be a "Azure Monitor" kifejezést a keresőmezőbe, és kattintson az eredményül kapott Azure Monitor csempére. Görgessen le az oldalról Azure Monitorre, majd válassza a legördülő menüből a Application Insights lehetőséget.  Itt adhatja meg, hogy hány GB-nyi adatot szeretne gyűjteni havonta, így a kérdés az, hogy mennyi adat Application Insights gyűjti az alkalmazás figyelését.

Ennek a megoldásnak két megközelítése van: az alapértelmezett monitorozás és az adaptív mintavételezés használata, amely a ASP.NET SDK-ban érhető el, vagy a várható adatfeldolgozást a hasonló ügyfelektől függően megbecsülheti.

### <a name="data-collection-when-using-sampling"></a>Adatgyűjtés mintavételezéskor

A ASP.NET SDK [adaptív mintavételezésével](sampling.md#adaptive-sampling)az adatmennyiség automatikusan módosul, hogy az alapértelmezett Application Insights figyeléshez megadott maximális adatforgalomon belül maradjon. Ha az alkalmazás alacsony telemetria (például hibakeresés vagy alacsony kihasználtság miatt) hoz létre, akkor a mintavételi processzor nem távolítja el az elemeket, feltéve, hogy a kötet nem éri el a beállított események másodpercenkénti szintjét. A nagy mennyiségű alkalmazás esetében az 5 esemény alapértelmezett küszöbértéke másodpercenként az adaptív mintavételezés a napi események számát 432 000-re korlátozza. Az 1 KB-os átlagos esemény-méretet használva ez az alkalmazást üzemeltető csomópontok 31 napos telemetria 13,4 GB-os, a mintavételezést pedig az egyes csomópontok esetében a helyi gépen végezheti el. 

Olyan SDK-k esetében, amelyek nem támogatják az adaptív mintavételezést, betöltési [mintavételezést](./sampling.md#ingestion-sampling)is alkalmazhat, amely az adatoknak a megőrzött adatok százalékos arányán alapuló Application Insights, illetve a [ASP.NET, a ASP.net Core és a Java-webhelyeken](sampling.md#fixed-rate-sampling) a webkiszolgálótól és a webböngészőktől elküldött forgalom csökkentése érdekében mintavételt végez.

### <a name="learn-from-what-similar-customers-collect"></a>Ismerje meg, milyen hasonló ügyfelek gyűjtenek

Ha a Application Insights Azure monitoring díjszabási számológépében engedélyezi az "adatmennyiség becslése az alkalmazási tevékenység alapján" funkciót, akkor az alkalmazással kapcsolatos adatokat (havi kérések havonta és a lapok megtekintését) is megadhatja, ha az ügyféloldali telemetria is begyűjti, majd a Számológép a hasonló alkalmazások által összegyűjtött adatmennyiség középértékét és 90%-os százalékos értékét fogja megállapítani. Ezek az alkalmazások a Application Insights konfiguráció tartományára terjednek ki (például néhány alapértelmezett [mintavételezéssel](./sampling.md), némelyikük nem tartalmaz mintavételezést stb.), így továbbra is szabályozhatja, hogy az Ön által használt adatmennyiség csökkenthető legyen a medián szint alatt, a mintavételezés használatával. Ez azonban egy kiindulási pont, amelyből megismerheti, hogy milyen más, hasonló ügyfelek látják.

## <a name="understand-your-usage-and-estimate-costs"></a>A használati és becsült költségek megismerése

A Application Insights segítségével könnyen megismerheti, hogy milyen költségek várhatók a legutóbbi használati szokások alapján. Első lépésként a Azure Portal Application Insights erőforráshoz lépjen a **használati és becsült költségek** lapra:

![Díjszabás kiválasztása](./media/pricing/pricing-001.png)

A. Tekintse át az adatmennyiséget a hónapban. Ebbe beletartozik az összes kapott és megőrzött adat (a [mintavételezés](./sampling.md)után) a kiszolgálóról és az ügyfélalkalmazások és a rendelkezésre állási tesztek közül.  
B. A [többlépéses webes tesztekért](./availability-multistep.md)külön díjat kell fizetni. (Ez nem tartalmazza az egyszerű rendelkezésre állási teszteket, amelyek az adatmennyiség-feltöltés részét képezik.)  
C. Az elmúlt hónap adatmennyiség-trendjeinek megtekintése.  
D. Adatfeldolgozási [mintavételezés](./sampling.md)engedélyezése.
E. Állítsa be a napi adatmennyiség korlátját.  

(Vegye figyelembe, hogy az ebben a cikkben szereplő képernyőképeken megjelenő összes ár csak példaként szolgál. A pénznem és a régió aktuális áraival kapcsolatban lásd: [Application Insights díjszabása][pricing].)

A Application Insights használatának mélyebb vizsgálatához nyissa meg a **metrikák** lapot, adja hozzá az "adatpont kötete" nevű metrikát, majd válassza a *felosztás alkalmazása* lehetőséget az adatok "telemetria-elem típusa" szerinti felosztásához.

Application Insights díjak hozzáadódnak az Azure-számlához. Az Azure-számlázás részleteit a Azure Portal **Cost Management + számlázási** szakaszában vagy az [Azure számlázási portálon](https://account.windowsazure.com/Subscriptions)tekintheti meg.  A Application Insights használatáról az [alábbi témakörben talál](#viewing-application-insights-usage-on-your-azure-bill) további információt. 

![A bal oldali menüben válassza a számlázás lehetőséget.](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Adatmennyiség metrikáinak használata
<a id="understanding-ingested-data-volume"></a>

Ha többet szeretne megtudni az adatkötetekről, válassza ki a Application Insights erőforrás **metrikáit** , és adjon hozzá egy új diagramot. A diagram metrikájának **log-alapú metrikák** területén válassza az **adatpont kötet** lehetőséget. Kattintson a **felosztás alkalmazása** elemre, és válassza a csoportosítás **`Telemetryitem` típus** szerint lehetőséget.

![Mérőszámok használata az adatmennyiség megkereséséhez](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Az adatmennyiség részleteit értelmező lekérdezések

A Application Insights adatköteteinek vizsgálatára két módszer áll rendelkezésre. Az első összesített információt használ a `systemEvents` táblában, a második pedig a `_BilledSize` tulajdonságot használja, amely minden betöltött eseménynél elérhető. `systemEvents` a nem rendelkezik adatméreti információkkal a [munkaterület-alapú alkalmazások](#data-volume-for-workspace-based-application-insights-resources)és az elemzések számára.

#### <a name="using-aggregated-data-volume-information"></a>Összesített adatmennyiség-információ használata

A táblázat segítségével például `systemEvents` megtekintheti az elmúlt 24 órában betöltött adatmennyiséget a lekérdezéssel:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Vagy az utolsó 30 nap adattípusa szerinti adatmennyiség (bájtban) diagram megjelenítéséhez használhatja a következőt:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Vegye figyelembe, hogy ez a lekérdezés egy Azure-beli [naplóbeli riasztásban](../alerts/alerts-unified-log.md) is használható az adatkötetek riasztásának beállításához.  

Ha többet szeretne megtudni a telemetria-adatváltozásokról, a lekérdezés használatával lekérheti az események számát típus szerint:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Adatok méretének használata az események adatai alapján

Az adatkötetek forrásával kapcsolatos további információkért használja az egyes betöltött `_BilledSize` eseményeken megtalálható tulajdonságot.

Ha például azt szeretné megvizsgálni, hogy mely műveletek eredményezik a legtöbb adatmennyiséget az elmúlt 30 napban, akkor az `_BilledSize` összes függőségi esemény összegét összesítheti:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>Adatmennyiség munkaterület-alapú Application Insights erőforrásokhoz

Ha szeretné megtekinteni a munkaterület [-alapú Application Insights összes erőforrásának](create-workspace-resource.md) adatmennyiségét az elmúlt hét munkaterületen, lépjen a log Analytics munkaterületre, és futtassa a lekérdezést:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

Ha egy adott munkaterület-alapú Application Insights erőforráshoz tartozó adatmennyiség-trendeket szeretne lekérdezni, akkor a Log Analytics munkaterületen a következőt használja:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Application Insights használatának megtekintése az Azure-számlán

Az Azure nagyszerű hasznos funkciókat biztosít a [Azure Cost Management + számlázási](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) központban. A "Cost Analysis" funkció például lehetővé teszi az Azure-erőforrások költségeinek megtekintését. Ha erőforrás-típus alapján (a Microsoft. bepillantások/összetevők Application Insights) egy szűrőt ad hozzá, lehetővé teszi a kiadások nyomon követését. Ezután a "csoportosítás" lehetőségnél válassza a "mérési kategória" vagy a "mérőszám" lehetőséget.  A jelenlegi árképzési csomagok Application Insights erőforrásai esetében a legtöbb felhasználási lehetőség Log Analyticsként jelenik meg a mérőszám kategóriájában, mivel az összes Azure Monitor összetevőhöz egyetlen naplós háttér tartozik. 

Használati adatait még részletesebben megismerheti, ha [letölti a használati adatokat az Azure Portalról](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
A letöltött számolótáblában napi Azure-erőforrás-használatot láthat. Ebben az Excel-táblázatban a Application Insights-erőforrásokkal való használat első szűréssel megtalálhatók a "Application Insights" és a "Log Analytics" megjelenítéséhez, majd hozzá kell adni egy szűrőt a "példány azonosítója" oszlophoz, amely "a Microsoft. bepillantást vagy összetevőket tartalmazza".  A legtöbb Application Insights-használat a Log Analytics mérőszám-kategóriával rendelkező fogyasztásmérőn szerepel, mivel az összes Azure Monitor összetevőhöz egyetlen naplós háttér tartozik.  A rendszer csak a régi árképzési szinteken és a többlépéses webes tesztek Application Insights erőforrásait jeleníti meg Application Insights fogyasztásmérő-kategóriája alapján.  A használat megjelenik a "felhasznált mennyiség" oszlopban, és az egyes bejegyzések egysége a "mértékegység" oszlopban látható.  További részletek is elérhetők, ha [meg szeretné ismerni Microsoft Azure-számláját](../../cost-management-billing/understand/review-individual-bill.md).

## <a name="managing-your-data-volume"></a>Adatmennyiség kezelése

A küldött adatmennyiség a következő módszerekkel kezelhető:

* **Mintavételezés**: a mintavétel segítségével csökkentheti a kiszolgálóról és az ügyfélalkalmazások által eljuttatott telemetria mennyiségét, a metrikák minimális torzításával. A mintavétel az elsődleges eszköz, amellyel beállíthatja az elküldött adatmennyiséget. További információ a [mintavételezési funkciókról](./sampling.md).

* **Ajax-hívások korlátozása**: [korlátozhatja az összes oldal nézetben jelentett Ajax-hívások számát](./javascript.md#configuration) , vagy kikapcsolhatja az Ajax-jelentéskészítést. Vegye figyelembe, hogy az Ajax-hívások letiltása letiltja a [JavaScript korrelációját](./javascript.md#enable-correlation).

* **Szükségtelen modulok letiltása**: [ApplicationInsights.configszerkesztésével](./configuration-with-applicationinsights-config.md) kikapcsolhatja a nem szükséges gyűjteményi modulokat. Dönthet például úgy, hogy a teljesítményszámlálók vagy a függőségi adat nem elengedhetetlen.

* **Előre összevont mérőszámok**: Ha a TrackMetric-hívásokat az alkalmazásban helyezi el, csökkentheti a forgalmat a mérések egy kötegének átlagos és szórásos kiszámítását elfogadó túlterhelés használatával. Vagy használhatja az [Összesítés előtti csomagot](https://www.myget.org/gallery/applicationinsights-sdk-labs)is.
 
* **Napi korlát**: Ha Application Insights erőforrást hoz létre a Azure Portal, a napi korlát 100 GB/nap lesz. Ha Application Insights-erőforrást hoz létre a Visual Studióban, az alapértelmezett érték kicsi (csak 32,3 MB/nap). A napi korlát alapértelmezett értéke a tesztelés megkönnyítésére szolgál. A rendszer azt a célt szolgálja, hogy a felhasználó a napi korlátot az alkalmazás éles környezetben való üzembe helyezése előtt emelje fel. 

    A maximális korlát 1 000 GB/nap, ha nagy forgalmú alkalmazás esetén magasabb maximális értéket kér.
    
    A napi korláttal kapcsolatos figyelmeztetési e-maileket a rendszer a következő szerepkörökhöz tartozó fiókra küldi el a Application Insights erőforráshoz: "ServiceAdmin", "AccountAdmin", "társtulajdonosa", "Owner".

    A napi korlát beállításakor körültekintően járjon el. Az Ön szándéka, hogy *Soha ne nyomja meg a napi korlátot*. Ha eléri a napi korlátot, a nap hátralevő részében elveszíti az adatait, és nem tudja monitorozni az alkalmazást. A napi korlát módosításához használja a **napi mennyiségi korlátot** . Ezt a beállítást a **használati és becsült költségek** ablaktáblán érheti el (a cikk későbbi részében részletesebben ismertetjük).
    
    Eltávolítjuk a korlátozást olyan előfizetési típusoknál, amelyek olyan Kredittel rendelkeznek, amely nem használható Application Insightshoz. Korábban, ha az előfizetés költségkerettel rendelkezik, a napi korlátot tartalmazó párbeszédablak útmutatást tartalmaz a költségkeret eltávolításához, és lehetővé teszi, hogy a napi korlát 32,3 MB/nap-nál nagyobb legyen.
    
* **Szabályozás**: a szabályozás korlátozza az adatátviteli sebességet másodpercenként 32 000 eseményre, átlagosan 1 percnél nagyobb kialakítási kulcsra. Az alkalmazás által küldött adatmennyiség percenként kerül értékelésre. Ha az érték meghaladja a percben mért másodpercenkénti arányt, a kiszolgáló elutasítja a kérelmeket. Az SDK pufferbe írja az adathalmazt, majd megpróbálja újból elküldeni. Több percet is igénybe vehet. Ha az alkalmazás a sávszélesség-szabályozásnál nagyobb mértékben küld adatokat, a rendszer bizonyos adatokat eldob. (A ASP.NET, a Java és a JavaScript SDK-k megpróbálnak ily módon újraküldeni az adatküldést. más SDK-k egyszerűen elhúzhatja a szabályozott adatátvitelt.) Ha a szabályozás bekövetkezik, a rendszer figyelmeztetést küld arról, hogy ez bekövetkezett.

## <a name="manage-your-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

A napi mennyiségi korlátot használhatja a gyűjtött adatok korlátozására. Ha azonban a korlát teljesül, az alkalmazásból a nap hátralevő részében elérkező összes telemetria elvesztése történik. *Nem tanácsos* az alkalmazásnak a napi korlátot megtalálnia. Az alkalmazás állapota és teljesítménye nem követhető nyomon, miután elérte a napi korlátot.

A napi mennyiségi korlát használata helyett [mintavételezéssel](./sampling.md) hangolja be az adatmennyiséget a kívánt szintre. Ezt követően a napi korlátot csak akkor használja, ha az alkalmazás váratlanul megkezdi a sokkal nagyobb mennyiségű telemetria küldését.

### <a name="identify-what-daily-data-limit-to-define"></a>A definiálni kívánt napi adatkorlát meghatározása

Tekintse át Application Insights használati és becsült költségét, és Ismerje meg az adatfeldolgozási trendet, valamint azt, hogy mi a napi mennyiségi korlát. Körültekintően kell fontolóra venni, mert a korlát elérésekor nem fogja tudni figyelni az erőforrásokat.

### <a name="set-the-daily-cap"></a>A napi korlát beállítása

A napi korlát módosításához a Application Insights erőforrás **Konfigurálás** szakaszában a **használat és a becsült költségek** lapon válassza a  **napi korlát** lehetőséget.

![A napi telemetria mennyiségi korlátjának módosítása](./media/pricing/pricing-003.png)

A [napi korlát Azure Resource Manageron keresztüli módosításához](./powershell.md)a módosítandó tulajdonság a következő: `dailyQuota` .  Azure Resource Manager a `dailyQuotaResetTime` és a napi korlátot is beállíthatja `warningThreshold` .

### <a name="create-alerts-for-the-daily-cap"></a>Riasztások létrehozása a napi korláthoz

A Application Insights napi korlát egy eseményt hoz létre az Azure-beli tevékenység naplójában, amikor a betöltött adatmennyiség eléri a figyelmeztetési szintet vagy a napi korlátot.  [Ezek a műveletnapló-események alapján létrehozhatók riasztások](../alerts/alerts-activity-log.md#create-with-the-azure-portal). A következő eseményekhez tartozó jelek nevei:

* Application Insights összetevő napi korlátjának figyelmeztetési küszöbértéke elérte

* Application Insights összetevő napi korlátja elérve

## <a name="sampling"></a>Mintavételezés
a [mintavétel](./sampling.md) olyan módszer, amely csökkenti a telemetria az alkalmazásba való küldésének mértékét, miközben megőrzi a kapcsolódó események keresésének lehetőségét a diagnosztikai keresések során. Megőrzi a helyes események számát is.

A mintavétel hatékony módszert jelent a költségek csökkentéséhez és a havi kvótán belüli tartózkodáshoz. A mintavételi algoritmus megőrzi a kapcsolódó telemetria, így például a keresés használatakor megkeresheti az adott kivételhez kapcsolódó kérelmet. Az algoritmus emellett megőrzi a helyes számadatokat, így a megfelelő értékeket látja a mérőszám-kezelőben a kérelmek díjszabása, a kivételek és az egyéb darabszámok tekintetében.

A mintavételnek többféle formája van.

* Az [adaptív mintavételezés](./sampling.md) a ASP.net SDK alapértelmezett értéke. Az adaptív mintavételezés automatikusan igazodik az alkalmazás által küldött telemetria. Automatikusan működik az SDK-ban a webalkalmazásban, így csökken a telemetria-forgalom a hálózaton. 
* A betöltési *mintavételezés* olyan alternatíva, amely arra a pontra működik, ahol az alkalmazás telemetria belép a Application Insights szolgáltatásba. A betöltési mintavételezés nem befolyásolja az alkalmazásból eljuttatott telemetria mennyiségét, de csökkenti a szolgáltatás által megőrzött kötetet. A betöltési mintavételezés használatával csökkentheti a telemetria által a böngészőkből és más SDK-k által használt kvótát.

A betöltési mintavételezés beállításához lépjen a  **díjszabás** panelre:

![A kvóta és díjszabás ablaktáblán válassza ki a minták csempét, majd válasszon ki egy mintavételi frakciót](./media/pricing/pricing-004.png)

> [!WARNING]
> Az **adatmintavételezési** panel csak a betöltési mintavételezés értékét vezérli. Nem tükrözi az alkalmazásban az Application Insights SDK által alkalmazott mintavételi sebességet. Ha a bejövő telemetria már meg van határozva az SDK-ban, a rendszer nem alkalmazza a betöltési mintavételezést.
>

Ha a tényleges mintavételezési sebességet szeretné felderíteni, függetlenül attól, hogy hol lett alkalmazva, használjon egy [elemzési lekérdezést](../logs/log-query-overview.md). A lekérdezés így néz ki:

```kusto
requests | where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h)
| render areachart
```

Az egyes megőrzött rekordokban `itemCount` az eredeti rekordok számát jelöli. A korábbi elvetett rekordok száma 1.

## <a name="change-the-data-retention-period"></a>Az adatmegőrzési időtartam módosítása

Application Insights erőforrások alapértelmezett megőrzése 90 nap. Minden Application Insights erőforráshoz különböző megőrzési időszakok választhatók ki. A rendelkezésre álló adatmegőrzési időszakok teljes készlete 30, 60, 90, 120, 180, 270, 365, 550 vagy 730 nap. [További](https://azure.microsoft.com/pricing/details/monitor/) információ a hosszabb adatmegőrzés díjszabásáról. 

Az adatmegőrzés módosításához a Application Insights erőforrásból lépjen a **használati és becsült költségek** lapra, és válassza ki az **adatmegőrzési** beállítást:

![Képernyőfelvétel: az adatmegőrzési időszak módosításának helye.](./media/pricing/pricing-005.png)

A megőrzési idő csökkentése után a legrégebbi adatok eltávolítása előtt több napos türelmi időszakot is megtarthat.

A megőrzés a programozott módon is [beállítható a PowerShell](powershell.md#set-the-data-retention) használatával a `retentionInDays` paraméter használatával. Ha az adatmegőrzést 30 napra állítja be, a paraméter használatával azonnal törölheti a régebbi adatok törlését `immediatePurgeDataOn30Days` , ami a megfelelőséggel kapcsolatos forgatókönyvek esetében hasznos lehet. Ez a kiürítési funkció csak Azure Resource Manageron keresztül érhető el, és rendkívül körültekintően használható. Az adatmennyiség-korlát napi visszaállítási ideje konfigurálható Azure Resource Manager használatával a paraméter beállításához `dailyQuotaResetTime` .

## <a name="data-transfer-charges-using-application-insights"></a>Adatátviteli díjak az Application Insights használatával

Az adatok Application Insights való küldése adatsávszélességi díjat eredményezhet. Az [Azure sávszélesség-díjszabási oldalán](https://azure.microsoft.com/pricing/details/bandwidth/)leírtak szerint a két régióban található Azure-szolgáltatások közötti adatforgalom a normál díjszabás szerint kifelé irányuló kimenő adatforgalom. A bejövő adatforgalom ingyenes. Ez a díj azonban nagyon kicsi (néhány%) a Application Insights naplózási adatfeldolgozás költségeihez képest. Ennek következtében a Log Analytics költségeinek szabályozása a betöltött adatmennyiségre összpontosíthat, és útmutatást nyújtunk ennek [megértéséhez.](#managing-your-data-volume)

## <a name="limits-summary"></a>Korlátok összegzése

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Napi Cap-beli e-mailek letiltása

A napi mennyiségi korláttal rendelkező e-mailek letiltásához a Application Insights erőforrásának **Konfigurálás** szakaszában, a **használat és a becsült költségek** panelen válassza a  **napi korlát** lehetőséget. A rendszer elküldi az e-mailek küldését, amikor eléri a korlátot, valamint ha elérte az állítható figyelmeztetési szintet. Ha le szeretné tiltani az összes napi korlátot a kötethez kapcsolódó e-maileket, törölje mindkét négyzet jelölését.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Örökölt nagyvállalati (node) árképzési csomag

Az Azure Application Insights korai alkalmazói számára még két lehetséges árképzési szint létezik: alapszintű és vállalati. Az alapszintű díjszabás megegyezik a fentiekben leírtak szerint, és az alapértelmezett szint. Minden nagyvállalati szintű szolgáltatást magában foglal, díjmentesen. Az alapszintű csomag elsősorban a betöltött adatok mennyiségét számlázza.

Ezek az örökölt árképzési szintek át lettek nevezve. A nagyvállalati díjszabási szint mostantól **csomópontként** van meghívva, és az alapszintű díjszabás mostantól **GB-ra** van meghívva. A rendszer ezeket az új neveket használja a Azure Portal.  

A per node (korábban nagyvállalati) szinten egy csomópontos díjat számítunk fel, és minden egyes csomópont napi adatmennyiséget kap. A per Node díjszabási szinten a befoglalt mennyiség fölött betöltött adatért kell fizetnie. Ha az Operations Management Suite-t használja, válassza ki a csomópontok közötti szintet. A 2018-es verzióban egy új díjszabási modellt [vezettünk be](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) az Azure monitoring szolgáltatáshoz. Ez a modell egy egyszerű "utólagos elszámolású" modellt alkalmaz a figyelési szolgáltatások teljes portfóliójában. További információ az [új díjszabási modellről](..//usage-estimated-costs.md).

A pénznem és a régió aktuális áraiért lásd: [Application Insights díjszabása](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="understanding-billed-usage-on-the-legacy-enterprise-per-node-tier"></a>Az örökölt vállalati (csomóponton belüli) díj számlázásának ismertetése 

Az alább leírtak szerint az örökölt nagyvállalati (csomóponton belüli) szint az előfizetésben lévő összes Application Insights-erőforrás használatát ötvözi a csomópontok számának és az adatok túlterhelésének kiszámításához. A kombinált folyamat miatt a rendszer az **előfizetésben lévő összes Application Insights erőforrás használatát csak az egyik erőforrásra vonatkozóan** jelenti.  Így összehangolhatja a [számlázott használatot](https://docs.microsoft.com/azure/azure-monitor/app/pricing#viewing-application-insights-usage-on-your-azure-bill) az egyes Application Insights-erőforrásoknál megfigyelt használattal. 

> [!WARNING]
> Az örökölt nagyvállalati (csomópont-) szinten Application Insights erőforrások használatának és megismerésének összetettsége miatt erősen ajánlott az aktuális utólagos elszámolású díjszabás használata. 

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>/Csomópontok és az Operations Management Suite előfizetési jogosultságai

Azok az ügyfelek, akik az Operations Management Suite E1-es és E2-es vásárlási csomaggal rendelkeznek, a [korábban bejelentettnél](/archive/blogs/msoms/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription)további díj nélkül kaphatnak Application Insightst további szolgáltatásként. Pontosabban, az Operations Management Suite E1 és E2 minden egysége jogosultságot tartalmaz a Application Insights egy csomópontjára. Az egyes Application Insights csomópontok naponta akár 200 MB-nyi adatot foglalnak magukban (a Log Analytics adatfeldolgozástól elkülönítve), a 90 napos adatmegőrzéssel együtt, többletköltség nélkül. A szintet a cikk későbbi részében részletesebben ismertetjük.

Mivel ez a csomag csak az Operations Management Suite-előfizetéssel rendelkező ügyfelekre alkalmazható, az Operations Management Suite-előfizetéssel nem rendelkező ügyfeleknek nem jelennek meg a csomag kiválasztására szolgáló lehetőség.

> [!NOTE]
> A jogosultság beszerzése érdekében a Application Insights-erőforrásoknak a Node díjszabási szinten kell lenniük. Ez a jogosultság csak csomópontok esetén érvényes. A GB-onként Application Insights erőforrások nem vesznek igénybe semmilyen előnyt. Ez a jogosultság nem látható a **használati és becsült költség** ablaktáblán látható becsült költségek között. Emellett, ha az előfizetést az új Azure monitoring díjszabási modellre helyezi át a 2018 áprilisában, a GB-os szintet az egyetlen rendelkezésre álló csomag. Az előfizetés az új Azure monitoring díjszabási modellre való áthelyezése nem ajánlott, ha Operations Management Suite-előfizetéssel rendelkezik.

### <a name="how-the-per-node-tier-works"></a>A/csomópontok szintjeinek működése

* Minden olyan csomópontért fizetnie kell, amely telemetria küld a Node szinten lévő összes alkalmazás számára.
  * A *csomópont* egy fizikai vagy virtuális kiszolgáló, illetve az alkalmazást üzemeltető platform-szolgáltatás szerepkör-példány.
  * A fejlesztői gépek, az ügyféloldali böngészők és a mobileszközök nem számítanak csomópontnak.
  * Ha az alkalmazás több olyan összetevővel is rendelkezik, amelyek telemetria küldenek, például egy webszolgáltatást és egy háttérbeli munkavégzőt, akkor az összetevőket külön kell megszámolni.
  * A [élő metrikastream](./live-stream.md) -adatértékek nem számítanak fel díjszabási célokra. Egy előfizetésben a díjak felhasználónként, nem pedig alkalmazásként jelennek meg. Ha öt olyan csomópontja van, amely 12 alkalmazás telemetria küld, a díj öt csomópontra van felszámítva.
* Bár a díjak havonta vannak feltüntetve, csak olyan órára kell fizetnie, amikor egy csomópont telemetria küld egy alkalmazásból. Az óradíj a 744-as számú (az órák száma 31 napos hónapban).
* A rendszer naponta 200 MB adatmennyiség-lefoglalást kap minden észlelt csomóponthoz (óránkénti részletességgel). A fel nem használt adatfoglalás nem egy napról a másikra történik.
  * Ha a felhasználónkénti díjszabási szintet választja, az egyes előfizetések napi adatmennyiséget szereznek az adott előfizetés Application Insights erőforrásaira telemetria küldő csomópontok száma alapján. Tehát ha öt olyan csomópontja van, amely egész nap küldi az adatküldést, akkor az adott előfizetéshez tartozó összes Application Insights esetében 1 GB-os készletezett támogatással fog rendelkezni. Nem számít, hogy egyes csomópontok több, mint más csomópontot küldenek, mert az összes csomóponton meg van osztva a tartalmazott információ. Ha egy adott napon a Application Insights-erőforrások több, mint az előfizetéshez tartozó napi adatfoglalásban foglalt adatmennyiséget kapnak, akkor a GB-nál nagyobb adatforgalmi díjak érvényesek. 
  * A napi adatmennyiség kiszámítása a nap folyamán (UTC szerint), az egyes csomópontok által küldött telemetria pedig 24 – 200 MB-kal elosztva történik. Tehát ha négy olyan csomópontja van, amely telemetria küld a nap 24 órájában, akkor az adott napra vonatkozó tartalmazott adat (4 &#215; 15)/24) &#215; 200 MB = 500 MB. A (z) 2,30 USD/GB értékű adatmennyiség esetén a díj 1,15 USD, ha a csomópontok naponta 1 GB adat küldését küldik.
  * A csomópontok közötti napi támogatás nincs megosztva olyan alkalmazásokkal, amelyekhez GB-nyi szintet választott. A fel nem használt pótlékot nem a napi naptól számítva.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Példák a különböző csomópontok számának meghatározására

| Eset                               | Csomópontok napi száma összesen |
|:---------------------------------------|:----------------:|
| 1 alkalmazás 3 Azure App Service példány és 1 virtuális kiszolgáló használatával | 4 |
| 3 alkalmazás 2 virtuális gépen; az alkalmazások Application Insights erőforrásai ugyanahhoz az előfizetéshez tartoznak, és a csomóponti szinten | 2 | 
| 4 olyan alkalmazás, amelynek alkalmazásaiban az alkalmazások erőforrásai ugyanabban az előfizetésben találhatók; minden, 2 példányt futtató alkalmazás 16 óra alatt, illetve 4 példányban 8 csúcsidőben | 13,33 |
| A Cloud Services 1 feldolgozói szerepkörrel és 1 webes szerepkörrel rendelkezik, amelyek mindegyike 2 példányt futtat | 4 | 
| Egy 5 csomópontos Azure Service Fabric-fürt, amely 50-es szolgáltatást futtat; minden 3 példányt futtató szolgáltatás | 5|

* A csomópontok pontos számlálása attól függ, hogy az alkalmazás melyik Application Insights SDK-t használja. 
  * Az SDK 2,2-es és újabb verzióiban mind a Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) -t, mind a [web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) -t minden egyes alkalmazás-gazdagép csomópontként jelenti. Ilyenek például a fizikai kiszolgáló és a virtuálisgép-gazdagépek számítógépneve, vagy a Cloud Services-példány neve.  Az egyetlen kivétel egy olyan alkalmazás, amely csak a [.net Core](https://dotnet.github.io/) -t és a Application INSIGHTS Core SDK-t használja. Ebben az esetben csak egy csomópontot kell jelenteni az összes gazdagépről, mert az állomásnév nem érhető el.
  * Az SDK korábbi verzióihoz a [web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) az újabb SDK-verzióhoz hasonlóan viselkedik, de az [alapszintű SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) csak egy csomópontot jelent, az alkalmazás gazdagépek számától függetlenül.
  * Ha az alkalmazás az SDK-val állítja be a **roleInstance** egyéni értékre, alapértelmezés szerint ugyanazt az értéket használja a csomópontok számának meghatározásához.
  * Ha egy új SDK-verziót használ az ügyfélgépekről vagy mobileszközökön futó alkalmazással, a csomópontok száma nagy (az ügyfélszámítógépek és a mobileszközök nagy száma miatt).

## <a name="automation"></a>Automation

Írhat egy parancsfájlt az árképzési csomag beállításához az Azure Erőforrás-kezelés használatával. [Ismerje meg, hogyan](powershell.md#price).

## <a name="next-steps"></a>Következő lépések

* [mintavételi](./sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ./app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
