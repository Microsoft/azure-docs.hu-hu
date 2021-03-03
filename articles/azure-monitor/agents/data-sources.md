---
title: Adatforrások a Azure Monitorban | Microsoft Docs
description: Ismerteti az Azure-erőforrások és a rajtuk futó alkalmazások állapotának és teljesítményének figyeléséhez rendelkezésre álló információkat.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/06/2020
ms.openlocfilehash: e03e31c342f90a0ea9f24e6be098a801410d4b72
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732088"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>A Azure Monitor figyelési adatforrásai
Azure Monitor egy [közös figyelési adatplatformon](../data-platform.md) alapul, amely [naplókat](../logs/data-platform-logs.md) és [mérőszámokat](../essentials/data-platform-metrics.md)tartalmaz. Az adatok ebbe a platformba való gyűjtése lehetővé teszi, hogy a több erőforrásból származó adatok együttesen legyenek elemezve az Azure Monitor-ban lévő eszközök közös eszközkészletének használatával. A figyelési adatokat más helyszínekre is elküldheti bizonyos forgatókönyvek támogatása céljából, és egyes erőforrások a naplókba vagy metrikába való összegyűjtésük előtt más helyszínekre is írhatnak.

Ez a cikk az Azure-erőforrások által létrehozott figyelési adatok mellett Azure Monitor által gyűjtött figyelési adatok különböző forrásait ismerteti. A hivatkozások részletes információkkal szolgálnak az adatok különböző helyekre való összegyűjtéséhez szükséges konfigurációról.

## <a name="application-tiers"></a>Alkalmazásrétegek

Az Azure-alkalmazásokból származó monitorozási adatok forrásai rétegekbe rendezhetők, a legmagasabb szinten pedig az alkalmazás, és az alacsonyabb szintek az Azure platform összetevői. Az egyes rétegek adatainak elérési módja változó. Az alkalmazási rétegeket az alábbi táblázat foglalja össze, és az egyes rétegekben található megfigyelési adatforrások az alábbi részekben láthatók. Az egyes adatterületek leírását és az azokhoz való hozzáférés módját lásd: az [Azure-beli adathelyek figyelése](../monitor-reference.md) .


![Megfigyelési szintek](../media/overview/overview.png)


### <a name="azure"></a>Azure
Az alábbi táblázat röviden leírja az Azure-ra jellemző alkalmazási szinteket. Az alábbi szakaszban található további részletekért lásd a hivatkozást.

| Szint | Leírás | Gyűjtési módszer |
|:---|:---|:---|
| [Azure-bérlő](#azure-tenant) | Bérlői szintű Azure-szolgáltatások, például az Azure Active Directory működésével kapcsolatos adatok. | Megtekintheti a HRE a portálon, vagy megadhatja a gyűjteményt Azure Monitor a bérlői diagnosztikai beállítások használatával. |
| [Azure-előfizetés](#azure-subscription) | Az Azure-előfizetésben, például a Resource Managerben és a Service Healthban található erőforrások közötti szolgáltatások állapotával és kezelésével kapcsolatos adat. | Tekintse meg a portálon, vagy konfigurálja a gyűjteményt Azure Monitor egy log-profillal. |
| [Azure-erőforrások](#azure-resources) |  Az egyes Azure-erőforrások működésével és teljesítményével kapcsolatos információk. | Automatikusan összegyűjtött metrikák, megtekintés Metrikaböngészőban.<br>A diagnosztikai beállítások konfigurálása a naplók Azure Monitor való összegyűjtéséhez.<br>Az egyes erőforrástípusok részletesebb figyeléséhez rendelkezésre álló figyelési megoldások és információk. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, más felhő vagy helyszíni 
Az alábbi táblázat röviden ismerteti az Azure-ban, egy másik felhőben vagy a helyszínen található alkalmazások rétegeit. Az alábbi szakaszban található további részletekért lásd a hivatkozást.

| Szint | Leírás | Gyűjtési módszer |
|:---|:---|:---|
| [Operációs rendszer (vendég)](#operating-system-guest) | A számítási erőforrások operációs rendszerére vonatkozó adatok. | Telepítse Log Analytics-ügynököt az ügyfél-adatforrások Azure Monitor és függőségi ügynökbe való gyűjtéséhez a virtuális gépek elemzését támogató függőségek összegyűjtéséhez.<br>Az Azure Virtual Machines esetében telepítse az Azure diagnosztikai bővítményt a naplók és a metrikák Azure Monitorba való gyűjtéséhez. |
| [Alkalmazás kódja](#application-code) | A tényleges alkalmazás és kód teljesítményével és működésével kapcsolatos adatok, beleértve a teljesítmény-nyomkövetést, az alkalmazás naplóit és a felhasználói telemetria. | A kód kiszámításával adatokat gyűjthet a Application Insightsba. |
| [Egyéni források](#custom-sources) | Külső szolgáltatásokból vagy más összetevőkből vagy eszközökről származó adatok. | Napló-vagy metrikák adatainak összegyűjtése bármely REST-ügyfélről Azure Monitorba. |

## <a name="azure-tenant"></a>Azure-bérlő
Az Azure-bérlőhöz kapcsolódó telemetria a bérlői szintű szolgáltatásokból, például a Azure Active Directoryból gyűjtik össze.

![Azure-bérlők gyűjteménye](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Naplók Azure Active Directory
[Azure Active Directory jelentéskészítés](../../active-directory/reports-monitoring/overview-reports.md) a bejelentkezési tevékenység előzményeit és az adott bérlőn belül végrehajtott módosítások naplózási nyomvonalát tartalmazza. 

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | Konfigurálja az Azure AD-naplókat a Azure Monitorba való gyűjtéshez, hogy elemezze azokat más figyelési adatokkal. | [Azure AD-naplók integrálása Azure Monitor naplókkal (előzetes verzió)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Azure AD-naplók exportálása az Azure Storage-ba archiválás céljából. | [Oktatóanyag: Azure AD-naplók archiválása egy Azure-tárfiókba (előzetes verzió)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Eseményközpont | Azure AD-naplók továbbítása más helyszínekre Event Hubs használatával. | [Oktatóanyag: Stream Azure Active Directory naplók egy Azure Event hub-ba (előzetes verzió)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Azure-előfizetés
Telemetria az Azure-előfizetés állapotával és működésével kapcsolatos.

![Azure-előfizetés](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Azure-tevékenységnapló 
Az [Azure-tevékenység naplója](../essentials/platform-logs-overview.md) tartalmazza a szolgáltatás állapotával kapcsolatos rekordokat, valamint az Azure-előfizetésében található erőforrásokon végrehajtott konfigurációs módosításokat tartalmazó rekordokat. A tevékenység naplója az összes Azure-erőforrás számára elérhető, és a _külső_ nézetet jelöli.

| Cél | Leírás | Referencia |
|:---|:---|
| Tevékenységnapló | A rendszer begyűjti a tevékenység naplóját a saját adattárolójába, amelyet a Azure Monitor menüből tekinthet meg, vagy a használatával tevékenységi naplók létrehozására vonatkozó riasztásokat hozhat létre. | [A tevékenység naplójának lekérdezése a Azure Portal](../essentials/activity-log.md#view-the-activity-log) |
| Azure Monitor-naplók | Konfigurálja Azure Monitor naplókat a tevékenység naplójának összegyűjtéséhez, hogy elemezze más megfigyelési adatokkal. | [Azure-beli tevékenység-naplók gyűjtése és elemzése Log Analytics munkaterületen Azure Monitor](../essentials/activity-log.md) |
| Azure Storage | Exportálja a tevékenység naplóját az Azure Storage-ba archiválás céljából. | [Archiválási tevékenység naplója](../essentials/resource-logs.md#send-to-azure-storage)  |
| Event Hubs | A tevékenység naplójának továbbítása más helyszínekre Event Hubs használatával | [Adatfolyam-tevékenység naplója az Event hub-](../essentials/resource-logs.md#send-to-azure-event-hubs)ba. |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) információt nyújt az előfizetésében lévő Azure-szolgáltatások állapotáról, amelyet az alkalmazás és az erőforrások támaszkodnak.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Tevékenységnapló<br>Azure Monitor-naplók | Service Health rekordokat az Azure-tevékenység naplójában tárolja a rendszer, így megtekintheti őket a Azure Portalban, vagy elvégezheti a tevékenység naplójában végrehajtható egyéb tevékenységeket. | [Szolgáltatás állapotára vonatkozó értesítések megtekintése az Azure Portalon](../../service-health/service-notifications.md) |


## <a name="azure-resources"></a>Azure-erőforrások
A mérőszámok és az erőforrás-naplók információt nyújtanak az Azure-erőforrások _belső_ műveleteiről. Ezek a legtöbb Azure-szolgáltatáshoz elérhetők, és a figyelési megoldások és elemzések további adatokat gyűjtenek az egyes szolgáltatásokhoz.

![Azure-erőforrások gyűjteménye](media/data-sources/data-source-azure-resources.svg)


### <a name="platform-metrics"></a>Platformmetrikák 
A legtöbb Azure-szolgáltatás olyan [platform-metrikákat](../essentials/data-platform-metrics.md) küld, amelyek a teljesítményt és a műveletet közvetlenül a metrikák adatbázisára mutatnak. Az [egyes típusú erőforrások esetében az adott mérőszámok eltérőek lesznek](../essentials/metrics-supported.md). 

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-metrikák | A platform metrikái a konfiguráció nélkül fognak írni a Azure Monitor metrikai adatbázisba. A platform metrikáinak elérése Metrikaböngészőból.  | [Bevezetés az Azure Metrikaböngésző használatába](../essentials/metrics-getting-started.md)<br>[Támogatott metrikák Azure Monitor](../essentials/metrics-supported.md) |
| Azure Monitor-naplók | A platform metrikáinak másolása a trendek és egyéb elemzések naplókba Log Analytics használatával. | [Az Azure Diagnostics közvetlen Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Stream metrikák más helyszínekre Event Hubs használatával. |[Az Azure monitoring-adattovábbítása egy Event hub-ba külső eszköz általi felhasználás céljából](../essentials/stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Erőforrásnaplók
Az [erőforrás-naplók](../essentials/platform-logs-overview.md) betekintést nyújtanak az Azure-erőforrások _belső_ műveleteibe.  Az erőforrás-naplók automatikusan létrejönnek, de létre kell hoznia egy diagnosztikai beállítást, amely meghatározza, hogy az egyes erőforrásokhoz hogyan kell gyűjteni a célhelyet.

A konfigurációs követelmények és az erőforrás-naplók tartalma az erőforrástípus szerint változik, és nem minden szolgáltatás hozza létre őket. Az egyes szolgáltatásokról és a részletes konfigurációs eljárásokra mutató hivatkozásokról a [támogatott szolgáltatások, sémák és kategóriák találhatók az Azure-erőforrás naplóiban](../essentials/resource-logs-schema.md) . Ha a szolgáltatás nem szerepel ebben a cikkben, akkor a szolgáltatás jelenleg nem hoz létre erőforrás-naplókat.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | Az erőforrás-naplók elküldése Azure Monitor naplókba más összegyűjtött naplózási adatok elemzéséhez. | [Azure-beli erőforrás-naplók gyűjtése Log Analytics munkaterületen Azure Monitor](../essentials/resource-logs.md#send-to-azure-storage) |
| Tárolás | Erőforrás-naplók küldése az Azure Storage-ba archiválás céljából. | [Azure-beli erőforrás-naplók archiválása](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Event Hubs | Az erőforrás-naplók továbbítása más helyszínekre Event Hubs használatával. |[Azure-beli erőforrás-naplók továbbítása egy Event hubhoz](../essentials/resource-logs.md#send-to-azure-event-hubs) |

## <a name="operating-system-guest"></a>Operációs rendszer (vendég)
A számítási erőforrások az Azure-ban, más felhőkben, a helyszínen pedig vendég operációs rendszert kell figyelni. Egy vagy több ügynök telepítésével telemetria gyűjthet a vendégtől a Azure Monitorba, hogy elemezze azokat ugyanazzal a figyelési eszközökkel, mint maguk az Azure-szolgáltatások.

![Azure számítási erőforrások gyűjtése](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Azure diagnosztikai bővítmény
Az Azure Diagnostics bővítmény Azure-beli virtuális gépekhez való engedélyezése lehetővé teszi naplók és metrikák gyűjtését az Azure számítási erőforrások vendég operációs rendszeréről, beleértve az Azure Cloud Service (klasszikus) webes és feldolgozói szerepköröket, a Virtual Machines, a virtuálisgép-méretezési csoportokat és a Service Fabric.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Tárolás | Az Azure Diagnostics bővítmény mindig egy Azure Storage-fiókba ír. | [A Windows Azure Diagnostics bővítmény (WAD) telepítése és konfigurálása](./diagnostics-extension-windows-install.md)<br>[Metrikák és naplók figyelése a Linux diagnosztikai bővítmény használatával](../../virtual-machines/extensions/diagnostics-linux.md) |
| Azure Monitor-metrikák | Ha a diagnosztikai bővítményt a teljesítményszámlálók gyűjtésére konfigurálja, a rendszer a Azure Monitor metrikák adatbázisába írja. | [Vendég operációs rendszer metrikáinak küldése a Azure Monitor metrika-tárolóba egy Resource Manager-sablon használatával a Windows rendszerű virtuális gépekhez](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) |
| Event Hubs | Konfigurálja a diagnosztikai bővítményt úgy, hogy az Event Hubs használatával továbbítsa az adatait más helyszínekre.  | [Adatfolyam-Azure Diagnosticsi adattovábbítás Event Hubs használatával](./diagnostics-extension-stream-event-hubs.md)<br>[Metrikák és naplók figyelése a Linux diagnosztikai bővítmény használatával](../../virtual-machines/extensions/diagnostics-linux.md) |
| Naplók Application Insights | Naplók és teljesítményszámlálók gyűjtése a számítási erőforrásból, amely támogatja az alkalmazás más alkalmazásadatok alapján történő elemzését. | [A felhőalapú szolgáltatás, a virtuális gép vagy a Service Fabric diagnosztikai adatbázis küldése Application Insights](./diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Log Analytics-ügynök 
Telepítse a Log Analytics-ügynököt a Windows-vagy Linux-alapú virtuális gépek átfogó monitorozásához és felügyeletéhez. A virtuális gép az Azure-ban, egy másik felhőben vagy a helyszínen is futtatható.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | A Log Analytics ügynök közvetlenül vagy System Center Operations Manageron keresztül csatlakozik Azure Monitorhoz, és lehetővé teszi, hogy adatokat gyűjtsön olyan adatforrásokból, amelyeket Ön konfigurál vagy olyan figyelési megoldásokkal, amelyek további elemzéseket nyújtanak a virtuális gépen futó alkalmazásokról. | [Ügynök-adatforrások a Azure Monitorban](../agents/agent-data-sources.md)<br>[Operations Manager összekötése a Azure Monitor](./om-agents.md) |
| VM-tároló | A VM-információk a Log Analytics ügynök használatával tárolják az egészségügyi állapottal kapcsolatos információkat egy egyéni helyen. További információt a következő szakaszban talál.  |


### <a name="vm-insights"></a>VM-ismeretek 
A [VM](../vm/vminsights-overview.md) -alapú adatvizsgálatok testre szabott figyelési élményt biztosítanak a virtuális gépek számára, amelyek a Core Azure monitor funkcionalitásán felül szolgáltatásokat biztosítanak. A Windows-és Linux-alapú virtuális gépeken olyan Dependency Agent igényel, amely integrálható a Log Analytics-ügynökkel a virtuális gépen és a külső folyamat függőségein futó folyamatok felderített adatainak összegyűjtéséhez.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | Az ügynök folyamataira és függőségeire vonatkozó információkat tárolja. | [A VM-ismeretek (előzetes verzió) leképezése az alkalmazás-összetevők értelmezéséhez](../vm/vminsights-maps.md) |



## <a name="application-code"></a>Alkalmazás kódja
A Azure Monitor részletes alkalmazás-figyelése [Application Insights](/azure/application-insights/) , amely különböző platformokon futó alkalmazások adatait gyűjti. Az alkalmazás az Azure-ban, egy másik felhőben vagy a helyszínen is futtatható.

![Alkalmazásadatok gyűjtése](media/data-sources/applications.png)


### <a name="application-data"></a>Alkalmazásadatok
Ha a rendszerállapot-csomagok telepítésével engedélyezi a Application Insights alkalmazást, az az alkalmazás teljesítményével és működésével kapcsolatos metrikákat és naplókat gyűjt. Application Insights az általa gyűjtött adatokat a más adatforrások által használt Azure Monitor adatplatformon tárolja. Kiterjedt eszközöket tartalmaz az adatok elemzéséhez, de más forrásokból származó adatokkal is elemezheti azokat olyan eszközökkel, mint például a Metrikaböngésző és a Log Analytics.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | Az alkalmazás üzemeltetési adatai, beleértve az oldalletöltések, az alkalmazásra vonatkozó kérelmek, a kivételek és a Nyomkövetések. | [Naplófájlok elemzése Azure Monitor](../logs/log-query-overview.md) |
|                    | Függőségi információk az alkalmazás-összetevők között az alkalmazások leképezésének és a telemetria korrelációjának támogatásához. | [Telemetria korreláció a Application Insightsban](../app/correlation.md) <br> [Alkalmazástérkép](../app/app-map.md) |
|            | A rendelkezésre állási tesztek eredményei, amelyek tesztelik az alkalmazás rendelkezésre állását és reagálását a nyilvános interneten lévő különböző helyekről. | [Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése](../app/monitor-web-app-availability.md) |
| Azure Monitor-metrikák | A Application Insights az alkalmazás teljesítményének és működésének leírását, valamint az alkalmazásban az Azure Monitor metrikai adatbázisba definiált egyéni metrikákat is összegyűjti. | [Naplóalapú és előre összesített metrikák az Application Insightsban](../app/pre-aggregated-metrics-log-metrics.md)<br>[Application Insights API egyéni eseményekhez és metrikákhoz](../app/api-custom-events-metrics.md) |
| Azure Storage | Alkalmazásadatok küldése az Azure Storage-ba archiválás céljából. | [Telemetria exportálása az Application Insightsból](../app/export-telemetry.md) |
|            | A rendelkezésre állási tesztek részleteit az Azure Storage tárolja. A helyi elemzéshez a Azure Portal Application Insights használatával töltheti le. A rendelkezésre állási tesztek eredményei Azure Monitor naplókban tárolódnak. | [Webhelyek rendelkezésre állásának és válaszkészségének megfigyelése](../app/monitor-web-app-availability.md) |
|            | A Profiler nyomkövetési adatait az Azure Storage tárolja. A helyi elemzéshez a Azure Portal Application Insights használatával töltheti le.  | [Profil éles alkalmazások az Azure-ban Application Insights](../app/profiler-overview.md) 
|            | A kivételek részhalmaza számára rögzített pillanatkép-adatok hibakeresése az Azure Storage-ban történik. A helyi elemzéshez a Azure Portal Application Insights használatával töltheti le.  | [A pillanatképek működése](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>A megoldások és az adatvizsgálatok figyelése
A [monitorozási megoldások](../insights/solutions.md) és elemzések [adatokat gyűjtenek, hogy](../monitor-reference.md) további betekintést nyújtsanak egy adott szolgáltatás vagy alkalmazás működéséhez. A különböző alkalmazási rétegekben és akár több rétegben is megoszthatják az erőforrásokat.

### <a name="monitoring-solutions"></a>Monitorozási megoldások

| Cél | Leírás | Referencia
|:---|:---|:---|
| Azure Monitor-naplók | A figyelési megoldások adatokat gyűjtenek Azure Monitor naplókba, ahol elemezni lehet a megoldásban általában szereplő lekérdezési nyelv vagy [nézetek](../visualize/view-designer.md) használatával. | [Az Azure-beli monitorozási megoldások adatgyűjtési adatai](../monitor-reference.md) |


### <a name="container-insights"></a>Tároló-felismerések
A [Container-bepillantást](../containers/container-insights-overview.md) az [Azure KUBERNETES Service (ak)](../../aks/index.yml)testreszabott figyelési felülete biztosítja. A következő táblázatban ismertetett erőforrásokra vonatkozó további adatokat gyűjt.

| Cél | Leírás | Referencia |
|:---|:---|:---|
| Azure Monitor-naplók | Tárolja a figyelési adattáblákat, beleértve a leltárt, a naplókat és az eseményeket. A metrikák adatait a rendszer a naplókban is tárolja, hogy az elemzési funkcióit kihasználja a portálon. | [Az AK-fürt teljesítményének megértése a tárolók bepillantást nyerhet](../containers/container-insights-analyze.md) |
| Azure Monitor-metrikák | A metrikai adatok a metrikus adatbázisban tárolódnak a vizualizációk és a riasztások megjelenítéséhez. | [Tároló metrikáinak megtekintése a metrikák Explorerben](../containers/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Közvetlen hozzáférést biztosít az Azure Kubernetes szolgáltatás (ak) tároló naplóihoz (StdOut/stderror), az eseményekhez és a pod metrikához a portálon. | [A Kubernetes-naplók,-események és a pod-metrikák valós idejű megtekintése ](../containers/container-insights-livedata-overview.md) |

### <a name="vm-insights"></a>VM-ismeretek
A [VM](../vm/vminsights-overview.md) -alapú adatvizsgálatok testreszabott felhasználói élményt biztosítanak a virtuális gépek figyelésére. A VM-elemzések által gyűjtött adatok leírását a fenti [operációs rendszer (vendég)](#operating-system-guest) szakasza tartalmazza.

## <a name="custom-sources"></a>Egyéni források
Az alkalmazások standard szintjein kívül előfordulhat, hogy figyelnie kell más erőforrásokat is, amelyek nem gyűjthetők össze más adatforrásokkal a telemetria. Ezekhez az erőforrásokhoz a Azure Monitor API használatával írhatja be ezeket az adatokat mérőszámok vagy naplók számára.

![Egyéni gyűjtemény](media/data-sources/custom.png)

| Cél | Metódus | Leírás | Referencia |
|:---|:---|:---|:---|
| Azure Monitor-naplók | Data Collector API | Gyűjtsön adatokat a REST-ügyfélről, és tárolja Log Analytics munkaterületen. | [Naplóbejegyzések küldése a Azure Monitornak a HTTP-adatgyűjtő API-val (nyilvános előzetes verzió)](../logs/data-collector-api.md) |
| Azure Monitor-metrikák | Egyéni metrikák API | Metrikus adatokat gyűjthet a REST-ügyfélről, és Azure Monitor metrikák adatbázisában tárolja őket. | [Egyéni metrikák küldése az Azure-erőforrásokhoz az Azure Monitor metrikai tárolóba egy REST API használatával](../essentials/metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Egyéb szolgáltatások
Az Azure egyéb szolgáltatásai a Azure Monitor adatplatformba írnak be adatfájlokat. Ez lehetővé teszi a szolgáltatások által összegyűjtött adatok elemzését Azure Monitor által összegyűjtött adatokkal, és ugyanazokat az elemzési és vizualizációs eszközöket használják.

| Szolgáltatás | Cél | Leírás | Referencia |
|:---|:---|:---|:---|
| [Azure Security Center](../../security-center/index.yml) | Azure Monitor-naplók | Azure Security Center tárolja az általa gyűjtött biztonsági adatokat egy Log Analytics munkaterületen, amely lehetővé teszi, hogy az a Azure Monitor által gyűjtött egyéb naplózási adatokkal elemezze.  | [Adatgyűjtés az Azure Security Centerben](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../../sentinel/index.yml) | Azure Monitor-naplók | Az Azure Sentinel a különböző adatforrásokból gyűjtött adatokat egy Log Analytics munkaterületen tárolja, amely lehetővé teszi, hogy a rendszer a Azure Monitor által gyűjtött egyéb naplózási adatokkal elemezze.  | [Adatforrások csatlakoztatása](../../sentinel/quickstart-onboard.md) |


## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor által gyűjtött megfigyelési adatok típusairól](../data-platform.md) , valamint az adatok megtekintésének és elemzésének módjáról.
- Sorolja fel azokat a [különböző helyeket, amelyekben az Azure-erőforrások tárolják az adattárolási](../monitor-reference.md) és elérési módját.