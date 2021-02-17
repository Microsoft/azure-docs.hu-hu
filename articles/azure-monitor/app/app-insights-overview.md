---
title: Mi az Azure Application Insights? | Microsoft Docs
description: Alkalmazásteljesítmény-felügyelet és élő webalkalmazások használatának nyomon követése.  Észlelheti, osztályozhatja és diagnosztizálhatja a problémákat, valamint megismerheti, hogy a felhasználók miként használják alkalmazását.
ms.topic: overview
ms.date: 06/03/2019
ms.custom: mvc
ms.openlocfilehash: ac6b6f2b47df0bce9ae164c83df54adfdf00b184
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575711"
---
# <a name="what-is-application-insights"></a>Mi az Application Insights?
A Application Insights a [Azure monitor](../overview.md)egy funkciója, amely egy bővíthető Application Performance Management-(APM-) szolgáltatás fejlesztők és DevOps szakemberek számára. Használhatja az élő alkalmazások figyelésére. A szolgáltatás automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz, amelyek segítségével diagnosztizálhatja a problémákat, és megtudhatja, hogy a felhasználók mit tesznek ténylegesen az alkalmazással.  Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot. Számos platformon használható, többek között a .NET, a Node.js, a Java és a Python helyszíni, hibrid vagy bármely nyilvános felhőben üzemeltetett alkalmazásokhoz. Emellett a DevOps folyamattal is integrálható, és eszközök széles köréhez rendelkezik kapcsolódási ponttal. Képes monitorozni és elemezni a mobilalkalmazások telemetriáját a Visual Studio App Center integrálásával.

## <a name="how-does-application-insights-work"></a>Hogyan működik az Application Insights?
Telepít egy kisméretű Instrumentation-csomagot (SDK-t) az alkalmazásban, vagy engedélyezheti Application Insights [használatát a](./platforms.md)Application Insights ügynökkel. A Instrumentation figyeli az alkalmazást, és a telemetria adatait egy Azure Application Insights-erőforrásra irányítja egy egyedi GUID-azonosító használatával, amelyet rendszerállapot-kulcsként említünk.

Ráadásul nem csak webszolgáltatási alkalmazásokat vizsgálhat, hanem háttérösszetevőket és weboldalak JavaScript-kódjait is. Az alkalmazás és annak összetevői bárhol futhatnak – nem kell az Azure-ban üzemeltetni.

![Az alkalmazásban telepített Application Insights-csomag telemetriai adatokat küld az Application Insights-erőforrásnak.](./media/app-insights-overview/diagram.png)

Emellett telemetriai adatok olvashatók be a gazdakörnyezetekből, például teljesítményszámlálók, Azure-beli diagnosztikai adatok vagy Docker-naplók. Olyan webes teszteket is beállíthat, amelyek rendszeres időközönként szintetikus kéréseket küldenek a webszolgáltatásnak.

Ezek a telemetriai adatfolyamok mind bekerülnek az Azure Monitorba. Az Azure Portalon hatékony elemzési és keresési eszközöket alkalmazhat a nyers adatokra.

### <a name="whats-the-overhead"></a>Milyen mértékű a többletterhelés?
Az alkalmazás teljesítményére gyakorolt hatás kicsi. A nem blokkoló nyomkövetési hívásokat a rendszer kötegeli, és a küldés külön szálakon történik.

## <a name="what-does-application-insights-monitor"></a>Mit figyel az Application Insights?

A fejlesztőcsapatoknak készült Application Insights segít az adott alkalmazás teljesítményének megismerésében, valamint abban, hogy a felhasználók miként használják az alkalmazást. A szolgáltatás az alábbiakat figyeli:

* **Kérések sebessége, válaszidők és hibaarányok** – megtudhatja, hogy mely lapok, mely napszakokban a legnépszerűbbek, és hol találhatók a felhasználók. Megtekintheti, hogy mely lapok teljesítenek a legjobban. Ha több kérés esetén a válaszidők és a hibaarányok értéke megnő, valószínűleg erőforrás-gazdálkodási hibáról van szó. 
* **Függőségi értékek, válaszidők és hibaarányok** – megtudhatja, hogy mely külső szolgáltatások okoznak lassulást.
* **Kivételek** – elemezze az összesített statisztikát, vagy válasszon ki konkrét példányokat, és részletezse a verem nyomkövetését és a kapcsolódó kérelmeket. A kiszolgálói és a böngészői kivételekről egyaránt készül jelentés.
* **Lapmegtekintések és betöltési teljesítmény** – a felhasználói böngészők jelentése alapján készül.
* Weblapokról származó **AJAX-hívások** – értékek, válaszidők és hibaarányok.
* **Felhasználók és munkamenetek száma**.
* Windows vagy Linux rendszerű kiszolgálói gépekről származó **teljesítményszámlálók**, például processzor-, memória- és hálózathasználat. 
* Dockerből vagy Azure-ból származó **gazdadiagnosztika**. 
* Alkalmazásból származó **nyomkövetési naplók diagnosztikája** – megállapíthatja a nyomkövetési események és a kérések korrelációját.
* **Egyéni események és metrikák**, amelyeket saját kezűleg helyezhet el az ügyfél vagy a kiszolgáló kódjában, és ezáltal üzleti eseményeket követhet (például eladott tételek vagy megnyert játékok).

## <a name="where-do-i-see-my-telemetry"></a>Hol láthatók a telemetriai adatok?

Az adatokat többféle módon lehet böngészni. Olvassa el az alábbi cikkeket:

|  |  |
| --- | --- |
| [**Intelligens észlelés és manuális riasztások**](./proactive-diagnostics.md)<br/>Állítson be automatikus riasztásokat, amelyek alkalmazkodnak az alkalmazás normál telemetria, és akkor aktiválódik, ha a szokásos mintázaton kívül van valami. [Riasztásokat is beállíthat](../alerts/alerts-log.md) egyéni vagy standard mérőszámok bizonyos szintjeire. |![Példa a riasztásokra](./media/app-insights-overview/alerts-tn.png) |
| [**Alkalmazás-hozzárendelés**](./app-map.md)<br/>Ismerkedjen meg az alkalmazás összetevőivel, és tekintse meg a legfontosabb mérőszámokat és riasztásokat. |![Alkalmazástérkép](./media/app-insights-overview/appmap-tn.png)  |
| [**Profilkészítő**](./profiler.md)<br/>A mintavételezett kérelmek végrehajtási profiljainak vizsgálata. |![A képernyőfelvételen a mintavételi kérelmek végrehajtási profiljai láthatók.](./media/app-insights-overview/profiler.png) |
| [**Használat elemzése**](./usage-overview.md)<br/>Felhasználószegmentálás és -megtartás elemzése.|![Megtartási eszköz](./media/app-insights-overview/retention.png) |
| [**Példányadatok diagnosztikai keresése**](./diagnostic-search.md)<br/>Események keresése és szűrése, például kérések, kivételek, függőségi hívások, naplókivonatok és lapmegtekintések.  |![Telemetriai adatok keresése](./media/app-insights-overview/search-tn.png) |
| [**Összesített adatok metrikaböngészője**](../essentials/metrics-charts.md)<br/>Összesített adatok – például kérés- és hibaarányok, valamint kivételek, válaszidők és lapbetöltési idők – böngészése, szűrése és szegmentálása. |![Mérőszámok](./media/app-insights-overview/metrics-tn.png) |
| [**Irányítópultok**](./overview-dashboard.md)<br/>Különböző erőforrásokból származó adatokat fűzhet össze és oszthat meg másokkal. Kiváló megoldás több összetevőből álló alkalmazások, valamint csoportszobában való folyamatos megjelenítés esetén. |![Példa az irányítópultokra](./media/app-insights-overview/dashboard-tn.png) |
| [**Élő metrikastream**](./live-stream.md)<br/>Új buildverzió telepítésekor közel valós idejű teljesítménymutatókat figyelhet, és meggyőződhet arról, hogy minden megfelelő módon működik-e. |![Példa a valós idejű metrikákra](./media/app-insights-overview/live-metrics-tn.png) |
| [**Elemzés**](../logs/log-query-overview.md)<br/>A hatékony lekérdezési nyelvnek köszönhetően válaszokat kaphat az alkalmazás teljesítményére és használatára vonatkozó legégetőbb kérdésekre. |![Példa az elemzésre](./media/app-insights-overview/analytics-tn.png) |
| [**Visual Studio**](./visual-studio.md)<br/>A kódban tekintheti meg a teljesítményadatokat. A hívásláncokból a kódba ugorhat.|![A képernyőfelvételen a Visual Studióban a kivétel részletei láthatók.](./media/app-insights-overview/visual-studio-tn.png) |
| [**Pillanatkép-hibakereső**](./snapshot-debugger.md)<br/>A működés közbeni műveletekről készült pillanatképek hibakeresése paraméterértékekkel.|![Visual Studio](./media/app-insights-overview/snapshot.png) |
| [**Power BI**](./export-power-bi.md)<br/>Integrálhatja a használati metrikákat más üzleti intelligenciával.| ![Power BI](./media/app-insights-overview/power-bi.png)|
| [**REST API**](https://dev.applicationinsights.io/)<br/>Kód írásával lekérdezéseket futtathat a metrikákhoz és a nyers adatokhoz kapcsolódóan.| ![REST API](./media/app-insights-overview/rest-tn.png) |
| [**Folyamatos exportálás**](./export-telemetry.md)<br/>Az adatok beérkezésével párhuzamosan végezheti a nyers adatok tömeges exportálását a tárba. |![Exportálás](./media/app-insights-overview/export-tn.png) |

## <a name="how-do-i-use-application-insights"></a>Hogyan használható az Application Insights?

### <a name="monitor"></a>Monitor
Telepítse az Application Insightsot az alkalmazásba, állítsa be a [rendelkezésre állási webes teszteket](./monitor-web-app-availability.md), és az alábbiakra nyílik lehetőség:

* Tekintse meg az alapértelmezett [alkalmazás-irányítópultot](./overview-dashboard.md) a munkahelye számára, hogy figyelje a terhelést, a válaszadást és a függőségek, az oldalak terhelését és az Ajax-hívásokat.
* Megismerheti a leglassabb és a legsikertelenebb kéréseket.
* Figyelheti az [élő streamet](./live-stream.md) új kiadások telepítésekor, és azonnal értesülhet bármilyen teljesítménycsökkenésről.

### <a name="detect-diagnose"></a>Észlelés, diagnosztizálás
Riasztások fogadásakor vagy problémák észlelésekor:

* Felmérheti, hogy hány felhasználó érintett.
* Kivételek, függőségi hívások és nyomkövetési hibák korrelációja.
* A profilkészítő, a pillanatképek, a veremkiíratások és a nyomkövetési naplók vizsgálata.

### <a name="build-measure-learn"></a>Fejlesztés, mérés, tapasztalatszerzés
Az újonnan üzembe helyezett szolgáltatások [hatékonyságának mérése](./usage-overview.md).

* Megtervezheti az új felhasználói felület vagy üzleti szolgáltatások ügyfelek általi használatának mérését.
* Egyéni telemetriai adatokat vehet fel a kódba.
* A következési fejlesztési ciklust a telemetriai adatokból származó konkrét tényekre alapozhatja.

## <a name="get-started"></a>Bevezetés
Az Application Insights a Microsoft Azure-ban üzemeltetett számos szolgáltatás egyike, és a telemetriai adatok ide érkeznek be elemzésre és megjelenítésre. A használat megkezdéséhez egy [Microsoft Azure-előfizetésre](https://azure.com) van szükség. A regisztráció ingyenes, és ha az Application Insights alapszintű [díjcsomagját](https://azure.microsoft.com/pricing/details/application-insights/) választja, a használat mindaddig díjmentes, amíg az alkalmazás el nem ér egy jelentős használati értéket. Ha a munkahelye már rendelkezik előfizetéssel, lehetőség van felvenni az Ön Microsoft-fiókját az adott előfizetésbe.

Az első lépések megtételének számos módja van. Kezdje azzal, amelyik Önnek a legmegfelelőbb. További szolgáltatásokat később is felvehet.

* **Futtatáskor: webalkalmazás vizsgálata a kiszolgálón.** Ideális a már üzembe helyezett alkalmazásokhoz. Elkerülheti a kódfrissítéseket.
  * [**Az Web Apps Azure-ban üzemeltetett ASP.NET-vagy ASP.NET Core-alkalmazások**](./azure-web-apps.md)
  * [**Az IIS-ben üzemeltetett ASP.NET-alkalmazások Azure-beli virtuális gépen vagy Azure-beli virtuálisgép-méretezési csoporton**](./azure-vm-vmss-apps.md)
  * [**Az IIS helyszíni virtuális gépen üzemeltetett ASP.NET-alkalmazások**](./monitor-performance-live-website-now.md)
* **Fejlesztéskor: az Application Insights hozzáadása a kódhoz.** Lehetővé teszi a telemetria-gyűjtemény testreszabását és további telemetria küldését.
  * [ASP.NET-alkalmazások](./asp-net.md)
  * [Alkalmazások ASP.NET Core](./asp-net-core.md)
  * [.NET-konzol alkalmazásai](./console.md)
  * [Java](./java-get-started.md)
  * [Node.js](./nodejs.md)
  * [Python](./opencensus-python.md)
  * [Más platformok](./platforms.md)
* Megtekintheti **[a weblapok](./javascript.md)** megtekintését, Ajax-t és más ügyféloldali telemetria.
* **[Elemezheti a mobilalkalmazások használatát](../app/mobile-center-quickstart.md)** a Visual Studio App Center integrálásával.
* **[Rendelkezésre állási tesztek](./monitor-web-app-availability.md)** – rendszeresen pingelheti webhelyét kiszolgálóinkról.

## <a name="next-steps"></a>Következő lépések
Első lépések futtatáskor:

* [Azure-beli virtuális gépek és Azure-beli virtuálisgép-méretezési csoport – IIS által üzemeltetett alkalmazások](./azure-vm-vmss-apps.md)
* [IIS-kiszolgáló](./monitor-performance-live-website-now.md)
* [Azure-webalkalmazások](./azure-web-apps.md)

Első lépések fejlesztéskor:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-get-started.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript](./javascript.md)


## <a name="support-and-feedback"></a>Támogatás és visszajelzés
* Kérdések és problémák:
  * [Hibaelhárítás][qna]
  * [Microsoft Q&egy kérdés oldal](/answers/topics/azure-monitor.html)
  * [StackOverflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* Javaslatok:
  * [UserVoice](https://feedback.azure.com/forums/357324-application-insights/filters/top)
* Blog:
  * [Application Insights blog](https://azure.microsoft.com/blog/tag/application-insights)

<!--Link references-->

[android]: ../app/mobile-center-quickstart.md
[azure]: ../../insights-perf-analytics.md
[client]: ./javascript.md
[desktop]: ./windows-desktop.md
[greenbrown]: ./asp-net.md
[ios]: ../app/mobile-center-quickstart.md
[java]: ./java-get-started.md
[knowUsers]: app-insights-web-track-usage.md
[platforms]: ./platforms.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md

