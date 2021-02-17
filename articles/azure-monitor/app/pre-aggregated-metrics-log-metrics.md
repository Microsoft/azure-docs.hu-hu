---
title: Naplózási és előre aggregált mérőszámok az Azure Application Insightsban | Microsoft Docs
description: Miért érdemes a log-alapú és az előre összevont mérőszámokat használni az Azure-ban Application Insights
ms.topic: conceptual
author: vgorbenko
ms.author: vitalyg
ms.date: 09/18/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c419411b0956cdc42055f0e97a47fc8e4ddb38c9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589736"
---
# <a name="log-based-and-pre-aggregated-metrics-in-application-insights"></a>Naplóalapú és előre összesített metrikák az Application Insightsban

Ez a cikk a "hagyományos" Application Insights a naplókon alapuló mérőszámok és az előre aggregált mérőszámok közötti különbséget ismerteti. Mindkét típusú metrika elérhető a Application Insights felhasználók számára, és mindegyik egyedi értéket biztosít az alkalmazás állapotának, diagnosztizálásának és elemzésének figyeléséhez. Az alkalmazásokat kiépítő fejlesztők eldönthetik, hogy az alkalmazás méretétől, a várt telemetria és a metrikák pontosságára és riasztására vonatkozó üzleti követelményektől függően milyen típusú metrika felel meg legjobban az adott forgatókönyvnek.

## <a name="log-based-metrics"></a>Log-alapú metrikák

A múltban a Application Insights alkalmazás-figyelési telemetria adatmodelljét kizárólag kis számú előre definiált esemény, például kérések, kivételek, függőségi hívások, oldalmegtekintések stb. alapján használták. A fejlesztők az SDK-t használhatják az események manuális kiküldésére (kód írásához, amely explicit módon meghívja az SDK-t), vagy az automatikus rendszerállapotból származó események automatikus gyűjtésére számíthatnak. Mindkét esetben a Application Insights-háttér az összes összegyűjtött eseményt naplófájlként tárolja, és a Azure Portal Application Insights pengéi analitikai és diagnosztikai eszközként szolgálnak az eseményvezérelt adatoknak a naplókból való megjelenítéséhez.

Az események teljes készletének megtartására szolgáló naplók használatával nagyszerű analitikai és diagnosztikai érték adható meg. Megadhatja például, hogy az adott URL-címre érkező kérések pontos száma pontosan hány különböző felhasználó számára készült. Vagy részletes diagnosztikai nyomkövetést is beolvashat, beleértve a kivételeket és a függőségi hívásokat a felhasználói munkamenetek esetében. Az ilyen típusú információk jelentős mértékben javíthatják az alkalmazás állapotát és használatát, így az alkalmazással kapcsolatos problémák diagnosztizálásához szükséges időt is csökkentheti.

Ugyanakkor előfordulhat, hogy az események teljes készletének begyűjtése nem praktikus (vagy akár lehetetlen) olyan alkalmazások esetében, amelyek nagy mennyiségű telemetria hoznak létre. Olyan helyzetekben, amikor az események mennyisége túl magas, Application Insights több telemetria-csökkentési módszert valósít meg, például a [mintavételt](./sampling.md) és a [szűrést](./api-filtering-sampling.md) , amely csökkenti az összegyűjtött és tárolt események számát. Sajnos a tárolt események számának csökkentése csökkenti a mutatók pontosságát is, amely a jelenetek mögött a naplókban tárolt események lekérdezési idejű összesítéseit is el kell végeznie.

> [!NOTE]
> Application Insights a naplókon tárolt események és mérések lekérdezési idejű összesítésén alapuló mérőszámokat log-alapú mérőszámoknak nevezzük. Ezek a metrikák jellemzően számos dimenzióval rendelkeznek az esemény tulajdonságaiból, így azok az elemzésekhez is magasabbak, de a mérőszámok pontossága negatívan befolyásolja a mintavételezést és a szűrést.

## <a name="pre-aggregated-metrics"></a>Előre összevont mérőszámok

A log-alapú 2018 mérőszámokon kívül a Application Insights csapat az idősorozatra optimalizált speciális adattárban tárolt mérőszámok nyilvános előzetes verzióját is elküldte. Az új metrikák már nem tekinthetők meg olyan egyedi eseményekként, amelyeknek sok tulajdonsága van. Ehelyett a rendszer előre összesített idősorozatként tárolja őket, és csak a legfontosabb dimenziókkal. Így az új metrikák a lekérdezési időpontnál jobbak: az adatok beolvasása sokkal gyorsabban történik, és kevesebb számítási teljesítményt igényel. Ez Következésképpen olyan új forgatókönyveket tesz lehetővé, mint a [közel valós idejű riasztás a mérőszámok méreteivel](../alerts/alerts-metric-near-real-time.md), rugalmasabb [irányítópultokkal](./overview-dashboard.md)és egyebekkel.

> [!IMPORTANT]
> Mind a naplózási, mind az előre aggregált mérőszámok párhuzamosan léteznek a Application Insightsban. A kettő megkülönböztetéséhez az Application Insights UX-ben az előre összevont metrikák mostantól "standard metrikák (előzetes verzió)" néven jelennek meg, míg az események hagyományos mérőszámait átnevezték "log-alapú metrikák" névre.

Az újabb SDK-k ([Application Insights 2,7](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.7.2) SDK vagy újabb verziók a .net-hez) a gyűjteményben előre összevont metrikák. Ez az  [alapértelmezett, alapértelmezés szerint eljuttatott mérőszámokra](../essentials/metrics-supported.md#microsoftinsightscomponents) vonatkozik, így a pontosság nem befolyásolja a mintavételezést vagy szűrést. A [GetMetric](./api-custom-events-metrics.md#getmetric) használatával elküldett egyéni mérőszámokra is vonatkozik, ami kevesebb adatfeldolgozást és alacsonyabb költségeket eredményez.

Az olyan SDK-k esetében, amelyek nem implementálják az előzetes összesítést (azaz Application Insights SDK-k régebbi verziói vagy a böngésző-rendszerállapotok), az Application Insights háttérrendszer továbbra is feltölti az új metrikákat az Application Insights esemény-gyűjtési végpont által fogadott események összesítésével. Ez azt jelenti, hogy ha nem használja ki a hálózaton keresztül továbbított adatmennyiséget, továbbra is használhatja az előre összevont mérőszámokat, és a közel valós idejű dimenziós riasztások jobb teljesítményét és támogatását is kihasználhatja az olyan SDK-k esetében, amelyek nem összesítik az előre összevont mérőszámokat a gyűjtemény során.

Érdemes megemlíteni, hogy a gyűjtemény végpontja előre összesíti az eseményeket a betöltési mintavételezés előtt, ami azt jelenti, hogy a betöltési [mintavételezés](./sampling.md) soha nem befolyásolja az előre aggregált mérőszámok pontosságát, függetlenül az alkalmazáshoz használt SDK-verziótól.  

### <a name="sdk-supported-pre-aggregated-metrics-table"></a>Az SDK által támogatott előre összesített mérőszámok táblázata

| Aktuális üzemi SDK-k | Standard mérőszámok (SDK előzetes összesítés) | Egyéni metrikák (az SDK előzetes összesítése nélkül) | Egyéni metrikák (az SDK előzetes összesítésével)|
|------------------------------|-----------------------------------|----------------------------------------------|---------------------------------------|
| .NET Core-és .NET-keretrendszer | Támogatott (V 2.13.1 +)| [TrackMetric](api-custom-events-metrics.md#trackmetric) -n keresztül támogatott| Támogatott (V 2.7.2 +) [GetMetric](get-metric.md) keresztül |
| Java                         | Nem támogatott       | [TrackMetric](api-custom-events-metrics.md#trackmetric) -n keresztül támogatott| Nem támogatott                           |
| Node.js                      | Nem támogatott       | [TrackMetric](api-custom-events-metrics.md#trackmetric) -n keresztül támogatott| Nem támogatott                           |
| Python                       | Nem támogatott       | Támogatott                                 | [OpenCensus. stats](opencensus-python.md#metrics) használatával támogatott |  


### <a name="codeless-supported-pre-aggregated-metrics-table"></a>Kód nem támogatott előre összesített mérőszámok táblázata

| Aktuális üzemi SDK-k | Standard mérőszámok (SDK előzetes összesítés) | Egyéni metrikák (az SDK előzetes összesítése nélkül) | Egyéni metrikák (az SDK előzetes összesítésével)|
|-------------------------|--------------------------|-------------------------------------------|-----------------------------------------|
| ASP.NET                 | Támogatott <sup> 1<sup>    | Nem támogatott                             | Nem támogatott                           |
| ASP.NET Core            | Támogatott <sup> 2<sup>    | Nem támogatott                             | Nem támogatott                           |
| Java                    | Nem támogatott            | Nem támogatott                             | [Támogatott](java-in-process-agent.md#metrics) |
| Node.js                 | Nem támogatott            | Nem támogatott                             | Nem támogatott                           |

1. A ASP.NET-kód App Service csak a teljes figyelési módban lévő mérőszámokat bocsátja ki. A ASP.NET-kód nélküli csatolás App Service, VM/VMSS, a helyszíni standard mérőszámok pedig méretek nélkül. Az SDK minden dimenzióhoz szükséges.
2. ASP.NET Core a kód nélküli csatolás a App Service standard mérőszámokat bocsát ki dimenziók nélkül. Az SDK minden dimenzióhoz szükséges.

## <a name="using-pre-aggregation-with-application-insights-custom-metrics"></a>Előzetes összesítés használata Application Insights egyéni metrikákkal

Az összesítést egyéni metrikákkal is használhatja. A két fő előnye az egyéni metrika dimenziójának konfigurálása és riasztása, valamint az SDK-ból a Application Insights-gyűjtemény végpontja számára továbbított adatok mennyiségének csökkentése.

[Az Application INSIGHTS SDK-ból többféleképpen küldhet egyéni metrikákat](./api-custom-events-metrics.md). Ha az SDK verziója a [GetMetric és a TrackValue](./api-custom-events-metrics.md#getmetric) metódust is tartalmazza, akkor ez az egyéni metrikák küldésének előnyben részesített módja, mivel ebben az esetben az előzetes ÖSSZESÍTÉS az SDK-ban történik, nem csak az Azure-ban tárolt adatok mennyiségének csökkentése, hanem az SDK-ból a Application Insightsra továbbított adatok mennyisége is. Ellenkező esetben használja a [trackMetric](./api-custom-events-metrics.md#trackmetric)  metódust, amely előre összesíti a metrikai eseményeket az adatok betöltése során.

## <a name="custom-metrics-dimensions-and-pre-aggregation"></a>Egyéni metrikák méretei és előzetes összesítés

A [trackMetric](./api-custom-events-metrics.md#trackmetric) vagy a [GetMetric és a TrackValue](./api-custom-events-metrics.md#getmetric) API-hívások használatával küldött összes mérőszámot a rendszer automatikusan tárolja a naplók és a metrikák tárolókban. Ugyanakkor bár az egyéni metrika naplózási alapú verziója mindig megőrzi az összes dimenziót, a metrika előre összevont verziója alapértelmezés szerint nincs dimenzió nélkül tárolva. Az egyéni metrikák dimenzióinak gyűjteményét bekapcsolhatja a [használati és becsült költségek](./pricing.md) lapon a "riasztás engedélyezése az egyéni metrika-dimenziókban" lehetőségre kattintva: 

![Használat és becsült költségek](./media/pre-aggregated-metrics-log-metrics/001-cost.png)

## <a name="why-is-collection-of-custom-metrics-dimensions-turned-off-by-default"></a>Miért van alapértelmezés szerint kikapcsolva az egyéni metrikák méreteinek gyűjteménye?

Az egyéni metrikák dimenzióinak gyűjteménye alapértelmezés szerint ki van kapcsolva, mert az egyéni metrikák a dimenziókkal való tárolásának jövőbeli elszámolása külön történik a Application Insightstól, miközben a nem dimenziós egyéni metrikák tárolása ingyenes marad (egy kvótáig). A hivatalos [díjszabási oldalon](https://azure.microsoft.com/pricing/details/monitor/)megismerheti a közelgő díjszabási modell változásait.

## <a name="creating-charts-and-exploring-log-based-and-standard-pre-aggregated-metrics"></a>Diagramok létrehozása és a naplózási és standard szintű előre összesített mérőszámok feltárása

[Azure Monitor Metrikaböngésző](../essentials/metrics-getting-started.md) használatával diagramokat rajzolhat előre összesített és naplózott metrikák alapján, valamint irányítópultokat hozhat létre a diagramokkal. A kívánt Application Insights erőforrás kiválasztása után a névtér-választóval válthat a standard (előzetes) és a log-alapú metrikák között, vagy kijelölhet egy egyéni metrikai névteret:

![Metrikai névtér](./media/pre-aggregated-metrics-log-metrics/002-metric-namespace.png)

## <a name="pricing-models-for-application-insights-metrics"></a>Application Insights mérőszámok díjszabási modelljei

A metrikák a Application Insightsba való betöltése, függetlenül attól, hogy a napló-vagy az előre összesítve, a betöltött adatok méretétől függően költségeket fog-e előkészíteni, az [itt](./pricing.md#pricing-model)leírtak szerint. Az egyéni metrikák, beleértve az összes dimenziót, mindig a Application Insights log-Store-ban tárolódnak; Emellett az egyéni metrikák (dimenziókkal nem rendelkező) előre összevont verziója a metrikák tárolóba kerül, alapértelmezés szerint továbbítva.

Ha az [Egyéni metrikai dimenziók engedélyezése](#custom-metrics-dimensions-and-pre-aggregation) lehetőségre kattint, a metrika-tárolóban lévő előre aggregált mérőszámok összes dimenziójának tárolásához **további** költségeket is létrehozhat az [Egyéni metrikák díjszabása](https://azure.microsoft.com/pricing/details/monitor/)alapján.

## <a name="next-steps"></a>Következő lépések

* [Közel valós idejű riasztás](../alerts/alerts-metric-near-real-time.md)
* [GetMetric és TrackValue](./api-custom-events-metrics.md#getmetric)
