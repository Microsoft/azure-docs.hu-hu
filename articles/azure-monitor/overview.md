---
title: Azure Monitor áttekintése | Microsoft Docs
description: Az Azure-szolgáltatások és -alkalmazások teljes monitorozási stratégiájának részét képező Microsoft-szolgáltatások és funkciók áttekintése.
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 11/17/2019
ms.openlocfilehash: 544d6937e412e3e1cfc2cf4e520c02f3f804fc8c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047162"
---
# <a name="azure-monitor-overview"></a>Azure Monitor – áttekintés

Azure Monitor segítségével maximalizálhatja alkalmazásai és szolgáltatásai rendelkezésre állását és teljesítményét. Átfogó megoldást kínál a Felhőbeli és a helyszíni környezetek telemetria gyűjtésére, elemzésére és működésének elvégzésére. Ez az információ segít megismerni az alkalmazások teljesítményét, és proaktív módon azonosítja azokat a problémákat, amelyek hatással vannak a rájuk és az azoktól függő erőforrásokra.

Csak néhány példa arra, hogy mit tehet a Azure Monitor a következők:

- Az alkalmazásokkal és függőségekkel kapcsolatos problémák észlelése és diagnosztizálása [Application Insightsokkal](app/app-insights-overview.md).
- A [VM](vm/vminsights-overview.md) -megállapításokkal és a [tároló](containers/container-insights-overview.md)-megállapításokkal kapcsolatos infrastrukturális problémákat.
- A monitorozási adatait [log Analytics](logs/log-query-overview.md) a hibaelhárítás és a mélyreható diagnosztika érdekében végezheti el.
- Az [intelligens riasztásokkal](alerts/alerts-smartgroups-overview.md) és az [automatizált műveletekkel](alerts/alerts-action-rules.md)nagy léptékű műveletek használhatók.
- Vizualizációkat hozhat létre az Azure- [irányítópultok](visualize/tutorial-logs-dashboards.md) és- [munkafüzetek](visualize/workbooks-overview.md)létrehozásával.
- Adatok gyűjtése a [figyelt erőforrásokból](./monitor-reference.md) [Azure monitor metrikák](./essentials/data-platform-metrics.md)használatával.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Áttekintés

Az alábbi diagramon az Azure Monitor magas szintű áttekintése látható. A diagram középpontjában a metrikák és naplók adattárai találhatók, amelyek az Azure Monitor által használt két alapvető típusú adat. A bal oldalon az ezeket az [adattárakat](data-platform.md)feltöltő [megfigyelési adatok forrásai](agents/data-sources.md) . A jobb oldalon azokat a különböző függvényeket hajtja végre, amelyeket az összegyűjtött adatokkal Azure Monitor. Ezek közé tartoznak az elemzés, a riasztások és a külső rendszerekre való folyamatos átvitel.

![Azure Monitor – áttekintés](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Az adatplatform figyelése

Az Azure Monitor által összegyűjtött összes adat a két alapvető típus, a [metrikák és a naplók](data-platform.md)egyikére illeszkedik. A [metrikák](essentials/data-platform-metrics.md) olyan numerikus értékek, amelyek egy adott rendszer bizonyos aspektusait írják le egy adott időpontban. Ezek egyszerűek és a közel valós idejű forgatókönyvek támogatására alkalmasak. A [naplók](logs/data-platform-logs.md) különböző típusú adatokat tárolnak a rekordokban, amelyek mindegyike különböző tulajdonságokkal rendelkezik. A telemetria, például az eseményeket és a nyomkövetési naplókat a teljesítményadatok mellett naplófájlként tárolja a rendszer, így az elemzéshez is egyesíthető.

Számos Azure-erőforrás esetében Azure Monitor közvetlenül gyűjtött adatokat a Azure Portal áttekintő lapján láthatók. Tekintse meg például a virtuális gépeket, és láthatja, hogy a teljesítmény-mérőszámokat több diagram jeleníti meg. Kattintson bármelyik gráfra, hogy megnyissa az adatokat a Azure Portal [mérőszámok Explorerben](essentials/metrics-charts.md) , amely lehetővé teszi, hogy az idő múlásával több mérőszám értékét is ábrázolja.  Megtekintheti a diagramokat interaktív módon, vagy rögzítheti azokat egy irányítópulton, és megtekintheti őket más vizualizációkkal.

![A diagram megjeleníti a Metrikaböngészőbe áramló mérőszámokat a vizualizációkban való használatra.](media/overview/metrics.png)

A Azure Monitor által gyűjtött naplózási adatokat elemezheti a [lekérdezésekkel](logs/log-query-overview.md) , így gyorsan lekérheti, összevonhatja és elemezheti az összegyűjtött adatokat.  A Azure Portal [log Analytics](./logs/log-query-overview.md) használatával létrehozhat és tesztelheti a lekérdezéseket. Ezután közvetlenül elemezheti az adatelemzést különböző eszközök használatával, vagy mentheti a lekérdezéseket [vizualizációkkal](visualizations.md) vagy [riasztási szabályokkal](alerts/alerts-overview.md)való használatra.

Azure Monitor a [Kusto lekérdezési nyelv](/azure/kusto/query/) egy olyan verzióját használja, amely egyszerű naplózási lekérdezésekhez alkalmas, de speciális funkciókat is tartalmaz, például összesítéseket, illesztéseket és intelligens elemzéseket. A lekérdezés nyelvét [több leckével](logs/get-started-queries.md)is gyorsan megismerheti.  Azon felhasználók számára, akik már ismerik az [SQL](/azure/data-explorer/kusto/query/sqlcheatsheet) és [Splunk](/azure/data-explorer/kusto/query/splunk-cheat-sheet) használatát, külön útmutató áll rendelkezésére.

![A diagram az elemzéshez Log Analyticsba áramló naplókat jeleníti meg.](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Milyen adatokat gyűjt az Azure Monitor?

A Azure Monitor [különböző forrásokból](monitor-reference.md)származó adatokat gyűjthet. Ez a tartomány az alkalmazástól, az összes olyan operációs rendszertől és szolgáltatástól függ, amelyik támaszkodik a platformra. Az Azure Monitor az alábbi szintek mindegyikéről gyűjt adatokat:

- **Alkalmazás-figyelési adat**: az Ön által írt kód teljesítményével és működésével kapcsolatos adat, függetlenül annak platformján.
- **Vendég operációs rendszer figyelési adatai**: azon operációs rendszer adatai, amelyeken az alkalmazás fut. A futtatás történhet az Azure-ban, egy másik felhőben vagy a helyszínen is. 
- **Azure-erőforrások monitorozási adatai**: Az egyes Azure-erőforrások működésével kapcsolatos adatok.
- **Azure-előfizetések figyelésére vonatkozó** információk: az Azure-előfizetés működésével és kezelésével, valamint az Azure állapotával és működésével kapcsolatos adatmennyiség. 
- Az **Azure-bérlő figyelési adatait**: a bérlői szintű Azure-szolgáltatások működésével, például Azure Active Directoryával kapcsolatos információk.

Amint létrehoz egy Azure-előfizetést és elkezdi hozzáadni az erőforrásokat (például virtuális gépeket vagy webalkalmazásokat), az Azure Monitor elkezdi az adatokat gyűjteni.  A [Tevékenységnaplók](essentials/platform-logs-overview.md) rögzítik az erőforrások létrehozását vagy módosítását. A [metrikák](data-platform.md) megmutatják, hogyan végezheti el az erőforrást, és milyen erőforrásokat fogyaszt. 

A [diagnosztika engedélyezésével](essentials/platform-logs-overview.md) kiterjesztheti a gyűjtött adatok mennyiségét az erőforrások belső műveleteibe.  [Vegyen fel egy ügynököt](agents/agents-overview.md) az erőforrások kiszámításához a telemetria a vendég operációs rendszerből való összegyűjtéséhez. 

Az alkalmazás figyelésének engedélyezése a [Application Insights](app/app-insights-overview.md) segítségével részletes információkat gyűjthet, beleértve az oldalletöltések, az alkalmazásra vonatkozó kérelmeket és a kivételeket. Ellenőrizze az alkalmazás rendelkezésre állását a [rendelkezésre állási teszt](app/monitor-web-app-availability.md) konfigurálásával a felhasználói forgalom szimulálása érdekében.

### <a name="custom-sources"></a>Egyéni források

A Azure Monitor [Adatgyűjtői API](logs/data-collector-api.md)-val bármely Rest-ügyfélről gyűjthet naplózott adatokat. Ez lehetővé teszi egyéni figyelési forgatókönyvek létrehozását és a figyelés kiterjesztését olyan erőforrásokra, amelyek nem teszik elérhetővé a telemetria más forrásokon keresztül.

## <a name="insights"></a>Insights
A monitorozási adatai csak akkor hasznosak, ha növeli a számítástechnikai környezet működésének átláthatóságát. Az adatok testreszabott figyelési [élményt nyújtanak az](monitor-reference.md#insights-and-core-solutions) adott Azure-szolgáltatásokhoz. Minimális konfigurációt igényelnek, és növelik a kritikus erőforrások működésének átláthatóságát.

### <a name="application-insights"></a>Application Insights
[Application Insights](app/app-insights-overview.md) figyeli a webalkalmazások rendelkezésre állását, teljesítményét és használatát, függetlenül attól, hogy azok a felhőben vagy a helyszínen vannak tárolva. Kihasználja a hatékony adatelemzési platformot a Azure Monitor-ben, hogy mélyreható betekintést nyújtson az alkalmazás műveleteibe. Lehetővé teszi a hibák diagnosztizálását anélkül, hogy a felhasználónak meg kellene várnia a jelentést. Application Insights a különböző fejlesztői eszközökhöz való kapcsolódási pontokat tartalmaz, és a Visual Studióval integrálva támogatja a DevOps folyamatokat.

![Alkalmazásokkal kapcsolatos megállapítások](media/overview/app-insights.png)

### <a name="container-insights"></a>Tároló-felismerések
A [tárolók](containers/container-insights-overview.md) elemzése az Azure Kubernetes szolgáltatásban (ak) üzemeltetett felügyelt Kubernetes-fürtökön üzembe helyezett tároló-munkaterhelések teljesítményét figyeli. Teljesítménybeli láthatóságot biztosít a Kubernetes a mérőszámok API-n keresztül elérhető vezérlők, csomópontok és tárolók metrikáinak összegyűjtésével. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a Kubernetes-fürtök figyelését, ezeket a metrikákat és naplókat a rendszer automatikusan összegyűjti a Linux rendszerhez készült Log Analytics-ügynök egy tárolós verziójával.

![Tároló állapota](media/overview/container-insights.png)

### <a name="vm-insights"></a>VM-ismeretek
A [VM-ismeretek](vm/vminsights-overview.md) skálán figyelik az Azure-beli virtuális gépeket (VM). Elemzi a Windows-és Linux-alapú virtuális gépek teljesítményét és állapotát, és azonosítja a különböző folyamatokat és egymással összekapcsolt függőségeket a külső folyamatokon. A megoldás támogatja a helyszíni vagy más felhőalapú szolgáltató által üzemeltetett virtuális gépek teljesítményének és alkalmazási függőségeinek figyelését.  


![VM-ismeretek](media/overview/vm-insights.png)


## <a name="responding-to-critical-situations"></a>Válaszadás a kritikus helyzetekre
Amellett, hogy lehetővé teszi a figyelési adatok interaktív elemzését, a hatékony figyelési megoldásnak képesnek kell lennie proaktív módon válaszolni a gyűjtött adatokban azonosított kritikus feltételekre. Ez egy szöveges vagy e-mail-címre küldheti a probléma kivizsgálásához felelős rendszergazdának. Vagy elindíthat egy olyan automatizált folyamatot, amely megkísérli kijavítani a hiba feltételeit.


### <a name="alerts"></a>Riasztások
[A Azure monitor riasztásai](alerts/alerts-overview.md) proaktívan értesítik Önt a kritikus feltételekről, és potenciálisan megkísérlik a javítási műveleteket. A metrikák alapján a riasztási szabályok közel valós idejű riasztásokat biztosítanak a numerikus értékek alapján. A naplókon alapuló szabályok lehetővé teszik a különböző forrásokból származó adatokon keresztüli összetett logikát.

A Azure Monitor riasztási szabályai a különböző szabályokban megosztható címzettek és műveletek egyedi készleteit tartalmazó [műveleti csoportok](alerts/action-groups.md)használata. A követelmények alapján a műveleti csoportok olyan műveleteket hajthatnak végre, mint például a webhookok, hogy a riasztások elindítják a külső műveleteket, vagy integrálni tudják a ITSM-eszközökkel.

![A képernyőképen a Azure Monitor súlyossággal, a teljes riasztásokkal és egyéb információkkal kapcsolatos riasztások láthatók.](media/overview/alerts.png)

### <a name="autoscale"></a>Automatikus méretezés
Az automatikus méretezés lehetővé teszi, hogy az alkalmazás terhelésének kezeléséhez megfelelő mennyiségű erőforrást futtasson. Olyan szabályokat hozhat létre, amelyek a Azure Monitor által összegyűjtött mérőszámokat használják annak meghatározására, hogy mikor történjen az erőforrások automatikus hozzáadása a terhelés növekedésekor. Pénzt takaríthat meg a tétlenül ülő erőforrások eltávolításával. Megadhatja a példányok minimális és maximális számát, valamint a logikát az erőforrások növelése vagy csökkentése érdekében.

![A diagram az autoskálázást jeleníti meg, több kiszolgálóval a > 80%-os számú és két kiszolgálóval, amelyek a jelenlegi kapacitásként vannak megjelölve, és öt maximális értékkel rendelkeznek.](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Figyelési adatgyűjtés megjelenítése
Az olyan [vizualizációk](visualizations.md) , mint a diagramok és a táblázatok, hatékony eszközök a monitorozási és a különböző célközönségeknek való megjelenítéshez. A Azure Monitor saját funkciókat biztosít a figyelési adatmegjelenítéshez és más Azure-szolgáltatások kihasználása érdekében a különböző célközönségeknek való közzétételhez.

### <a name="dashboards"></a>Irányítópultok
Az [Azure-irányítópultok](../azure-portal/azure-portal-dashboards.md) lehetővé teszik, hogy különböző típusú adattípusokat egyesítse egyetlen ablaktáblán a [Azure Portalban](https://portal.azure.com). Igény szerint megoszthatja az irányítópultot más Azure-felhasználókkal. Bármely naplózási lekérdezés vagy metrika diagram kimenetének hozzáadása egy Azure-irányítópulthoz. Létrehozhat például egy irányítópultot, amely a metrikák gráfját, a tevékenységek naplóit, a Application Insights használati diagramját, valamint egy napló lekérdezés kimenetét jeleníti meg.

![A képernyőképen egy Azure-irányítópult látható, amely az alkalmazás-és biztonsági csempéket, valamint más testreszabható adatokat tartalmaz.](media/overview/dashboard.png)

### <a name="workbooks"></a>Munkafüzetek
A [munkafüzetek](visualize/workbooks-overview.md) rugalmas vásznat biztosítanak az adatelemzéshez és a Azure Portal gazdag vizuális jelentéseinek létrehozásához. Lehetővé teszik, hogy több adatforrásra koppintson az Azure-ból, és egyesítse azokat egységes interaktív környezetekben. Az információk alapján létrehozott munkafüzetek és az előre definiált sablonok használatával is létrehozhatók.


![Munkafüzetek – példa](media/overview/workbooks.png)

### <a name="power-bi"></a>Power BI
A [Power bi](https://powerbi.microsoft.com) egy üzleti elemzési szolgáltatás, amely interaktív vizualizációkat biztosít különböző adatforrások között. Ez hatékony eszköz a szervezeten belüli és kívüli mások számára elérhetővé tételéhez. A Power BI beállítható úgy, hogy a [rendszer automatikusan importálja a naplófájlokat a Azure monitorból](./visualize/powerbi.md) , hogy kihasználhassa ezeket a további vizualizációkat.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Az Adatintegráció és-exportálás
Gyakran a Azure Monitor más rendszerekkel való integrálására, valamint a megfigyelési adatait használó egyéni megoldások létrehozására van szükség. Más Azure-szolgáltatások együttműködik a Azure Monitor az integráció biztosításához.

### <a name="event-hub"></a>Eseményközpont
Az [Azure Event Hubs](../event-hubs/index.yml) egy folyamatos átviteli platform és egy esemény-betöltési szolgáltatás. Bármilyen valós idejű elemzési szolgáltató vagy batch-vagy Storage-adapter használatával képes átalakítani és tárolni az adattárolást. A Event Hubs használatával [továbbíthatja Azure monitor-adatforrásait](essentials/stream-monitoring-data-event-hubs.md) a partner Siem-hez és a figyelési eszközökhöz.


### <a name="logic-apps"></a>Logic Apps
A [Logic apps](https://azure.microsoft.com/services/logic-apps) egy olyan szolgáltatás, amely lehetővé teszi a feladatok és az üzleti folyamatok automatizálását a különböző rendszerekkel és szolgáltatásokkal integrált munkafolyamatok használatával. Olyan tevékenységek érhetők el, amelyek a metrikák és a naplók olvasását és írását Azure Monitor. Ez lehetővé teszi, hogy különböző rendszerekkel integrációs munkafolyamatokat építsen ki.


### <a name="api"></a>API
Több API áll rendelkezésre a metrikák és a naplók olvasására és írására Azure Monitor a generált riasztások elérése mellett. Lehetőség van a riasztások konfigurálására és lekérésére is. Ez lényegében korlátlan lehetőségeket biztosít a Azure Monitor-nal integrált egyéni megoldások létrehozásához.

## <a name="next-steps"></a>Következő lépések
További információk:

* A Azure Monitor által gyűjtött adatok [metrikái és naplói](https://docs.microsoft.com/azure/azure-monitor/data-platform#metrics) .
* [Adatforrások](agents/data-sources.md) az alkalmazás különböző összetevőinek telemetria küldéséhez.
* [Naplók lekérdezése](logs/log-query-overview.md) az összegyűjtött adatok elemzéséhez.
* [Ajánlott eljárások](/azure/architecture/best-practices/monitoring) a felhőalapú alkalmazások és szolgáltatások monitorozásához.
