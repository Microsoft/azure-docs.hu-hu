---
title: Az Azure Monitor üzembe helyezése
description: A Azure Monitor teljes megvalósításához szükséges különböző lépéseket ismerteti az Azure-előfizetés összes erőforrásának figyeléséhez.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: f5855d7ab1f7ba8e11334f1373fb10166f47003a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708254"
---
# <a name="deploy-azure-monitor"></a>Az Azure Monitor üzembe helyezése
Az Azure-erőforrások figyelésének engedélyezése Azure Monitor az Azure Monitor-összetevők konfigurálását és az Azure-erőforrások konfigurálását is lehetővé teszi, hogy a gyűjtéshez Azure Monitor figyelési adatokat készítsenek. Ez a cikk a Azure Monitor teljes megvalósításához szükséges különböző lépéseket ismerteti az Azure-előfizetésében lévő összes erőforrás figyelésére szolgáló közös konfiguráció használatával. Az egyes lépésekhez tartozó alapszintű leírások a részletes konfigurációs követelményekre mutató hivatkozásokkal is rendelkeznek.

> [!IMPORTANT]
> Az Azure Monitor és azok konfigurációjának funkciói az üzleti igényektől függően az engedélyezett funkciók díjszabása szerint változnak. Az alábbi lépések alapján megállapítható, hogy van-e lehetséges költség, és a lépés végrehajtása előtt fel kell mérnie ezeket a költségeket. A díjszabással kapcsolatos részletekért tekintse meg [Azure monitor díjszabását](https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="configuration-goals"></a>Konfigurációs célok
A Azure Monitor teljes megvalósításának célja, hogy összegyűjtse az összes rendelkezésre álló adatot az összes felhőalapú erőforrásból és alkalmazásból, és lehetővé tegye, hogy az adatok alapján minél több funkciót lehessen Azure Monitor.

A Azure Monitor által gyűjtött adatokat [Azure monitor metrikák](essentials/data-platform-metrics.md) vagy [Azure monitor naplók](logs/data-platform-logs.md)kapják meg. Mindegyik különböző típusú adattípust tárol, és különböző típusú elemzéseket és riasztásokat tesz lehetővé. A különböző típusú riasztások leírását lásd: [Azure monitor mérőszámok és naplók összehasonlítása](data-platform.md) a [Microsoft Azure riasztások](alerts/alerts-overview.md) összevetésével és áttekintésével. 

Néhány adat elküldhető mind a metrikák, mind a naplók számára a különböző funkciókkal való kihasználása érdekében. Ezekben az esetekben előfordulhat, hogy mindegyiket külön kell konfigurálnia. A metrikus adatokat például automatikusan elküldi az Azure-erőforrások a metrikák számára, amely támogatja a metrikák és a metrikai riasztásokat. Minden erőforráshoz létre kell hoznia egy diagnosztikai beállítást, hogy ugyanazokat a metrikákat küldje el a naplókhoz, ami lehetővé teszi, hogy a Log Analytics használatával elemezze a teljesítménnyel kapcsolatos trendeket más naplózási adatokkal. Az alábbi szakaszokban megtalálhatja az adatküldés helyét, és tartalmazza az egyes lépéseket, amelyek szükségesek az összes lehetséges helyhez való adatküldéshez.

További követelmények is lehetnek, például az Azure-on kívüli erőforrások figyelése és a Azure Monitoron kívüli adatok küldése. A követelmények, például ezek a jelen cikkben ismertetett funkciók további konfigurációjával is elérhetők. További konfigurációs lehetőségekért kövesse az egyes lépésekben található dokumentációra mutató hivatkozásokat.

## <a name="collect-data-from-azure-resources"></a>Adatok gyűjtése az Azure-erőforrásokból

> [!NOTE]
> Lásd: az [Azure-erőforrások monitorozása a Azure monitor](essentials/monitor-azure-resource.md) a virtuális gépek Azure Monitorsal való figyelésének teljes útmutatóját.

Az Azure-erőforrások bizonyos figyelése automatikusan, konfiguráció nélkül érhető el, míg a további figyelési adatok gyűjtéséhez konfigurációs lépéseket kell végrehajtania. Az alábbi táblázat azokat a konfigurációs lépéseket mutatja be, amelyek az Azure-erőforrások összes elérhető adatának gyűjtéséhez szükségesek, beleértve az adatok Azure Monitor metrikák és Azure Monitor naplókhoz való továbbításának lépéseit. Az alábbi szakaszok részletesen ismertetik az egyes lépéseket.

[Az ![ Azure erőforrás-figyelés ](media/deploy/deploy-azure-resources.png) üzembe helyezése ](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>Nincs konfiguráció
Az Azure-előfizetések létrehozásakor a Azure Monitor következő funkciói engedélyezve vannak, és nincs szükség konfigurációra. Ehhez a figyeléshez nem tartozik díj.

[Azure Active Directory naplók](../active-directory/reports-monitoring/overview-reports.md) – bérlői szintű előzményeket biztosít a bejelentkezési tevékenységhez és a Azure Active Directory végrehajtott módosítások naplózási nyomvonalához. A Azure Active Directory naplók részleteiről és a Azure Portal megtekintéséről a [Azure Active Directory-portálon](../active-directory/reports-monitoring/concept-audit-logs.md) és a [bejelentkezési tevékenység jelentéseiben](../active-directory/reports-monitoring/concept-sign-ins.md) talál további információt a Azure Active Directory portálon.

[Műveletnapló](essentials/platform-logs-overview.md) – betekintést nyújt a felügyeleti csoportba és az előfizetési szintű eseményekre, amelyek az Azure-ban jelentkeztek. Az eseményeket a rendszer automatikusan a tevékenység naplóba írja, amikor új Azure-erőforrást hoz létre, módosít egy erőforrást, vagy jelentős tevékenységet végez. Az eseményeket megtekintheti a Azure Portalban, és a tevékenységek naplójának riasztásait is létrehozhatja az egyes események létrehozásakor. Tekintse meg a tevékenység naplójának részleteit, és azt, hogy [miként tekintheti](essentials/activity-log.md) meg a Azure Portalban.

[Platform metrikák](essentials/metrics-supported.md) – az Azure-szolgáltatásokból automatikusan gyűjtött [Azure monitor mérőszámokra](essentials/data-platform-metrics.md). Ezek az információk gyakran a Azure Portal **áttekintő** lapján jelennek meg a különböző szolgáltatások esetében. A Azure Portal platform metrikáinak elemzésével kapcsolatos részletekért lásd: az [Azure Metrikaböngésző első lépései](essentials/metrics-getting-started.md) . 


### <a name="create-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása
Szükség van legalább egy Log Analytics munkaterületre a [Azure monitor naplók](logs/data-platform-logs.md)engedélyezéséhez, amelyek szükségesek az adatok Azure-erőforrásokba való gyűjtéséhez, az Azure-beli virtuális gépek vendég operációs rendszeréről származó adatok gyűjtéséhez, valamint a legtöbb Azure monitor elemzéshez. Más szolgáltatások, például az Azure Sentinel és a Azure Security Center egy Log Analytics-munkaterületet is használhatnak, és megoszthatják ugyanazt, mint Azure Monitor. Egyetlen munkaterülettel is elindíthatja ezt a figyelést, de a több munkaterület használatára vonatkozó útmutatásért lásd:  [a Azure monitor naplók üzembe helyezésének megtervezése](logs/design-logs-deployment.md) .

Log Analytics munkaterület létrehozásához nem számítunk fel díjat, de lehetséges, hogy az adatok begyűjtésére való konfigurálását követően a szolgáltatás díjköteles. A részletekért lásd: [a használat és a költségek kezelése Azure monitor naplókkal](logs/manage-cost-storage.md) .  

A kezdeti Log Analytics munkaterület létrehozásához tekintse meg [log Analytics munkaterület létrehozása a Azure Portalben](logs/quick-create-workspace.md) című témakört. A hozzáférés konfigurálásához lásd: [hozzáférés kezelése a naplózási és munkaterületek Azure monitor](logs/manage-access.md) . 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>Diagnosztikai beállítás létrehozása a bérlői és az előfizetési naplók összegyűjtéséhez
A bérlőhöz tartozó [Azure Active Directory naplókat](../active-directory/reports-monitoring/overview-reports.md) és az előfizetéséhez tartozó [tevékenység naplóját](essentials/platform-logs-overview.md) automatikusan gyűjti a rendszer, és egy log Analytics munkaterületre küldi őket, így az eseményeket más naplózási adatokkal is elemezheti a log Analytics lekérdezések használatával. Ez lehetővé teszi a napló lekérdezési riasztások létrehozását is, amelyek az egyetlen módja a Azure Active Directory naplók riasztásának, és összetettebb logikát biztosítanak, mint a tevékenység naplójának riasztásai.

A műveletnapló nem küldhető munkaterületre, de az adatfeldolgozás és a megőrzési díj Azure Active Directory naplók esetében is fennáll. 

Lásd: az [Azure ad-naplók integrálása Azure monitor naplókkal](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) és [diagnosztikai beállítások létrehozása a platform-naplók és-metrikák különböző célhelyekre való küldéséhez](essentials/diagnostic-settings.md) , hogy diagnosztikai beállítást hozzon létre a bérlő és az előfizetés számára, hogy bejegyzéseket küldjön a log Analytics munkaterületre. 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>Diagnosztikai beállítás létrehozása az erőforrás-naplók és a platform-metrikák összegyűjtéséhez
Az Azure-beli erőforrások automatikusan állítanak elő [erőforrás-naplókat](essentials/platform-logs-overview.md) , amelyek az erőforráson belül végrehajtott műveletek részleteit tartalmazzák. A platform metrikákkal ellentétben azonban be kell állítania az összegyűjteni kívánt erőforrás-naplókat. Diagnosztikai beállítás létrehozásával elküldheti azokat egy Log Analytics munkaterületre, és összekapcsolhatja azokat a Azure Monitor-naplókhoz használt többi adattal. Ugyanezt a diagnosztikai beállítást is használhatja arra, hogy a legtöbb erőforráshoz tartozó platform-metrikákat ugyanarra a munkaterületre küldje el, amely lehetővé teszi a metrikus adatok elemzését más összegyűjtött adatokkal rendelkező naplók használatával.

Ennek a gyűjteménynek a díja ebben az esetben a [Azure monitor díjszabására](https://azure.microsoft.com/pricing/details/monitor/) vonatkozik, mielőtt jelentős számú erőforrást kellene megvalósítani. Tekintse meg a [használat és a költségek kezelése Azure monitor naplókkal](logs/manage-cost-storage.md) című témakört is, amely részletesen ismerteti a naplók költségeinek optimalizálását.

Az Azure-erőforrások diagnosztikai beállításának létrehozásához tekintse meg a [diagnosztikai beállítás létrehozása az erőforrás-naplók és-mérőszámok létrehozásához az Azure-ban](essentials/diagnostic-settings.md#create-in-azure-portal) című témakört. Mivel minden egyes Azure-erőforráshoz létre kell hozni egy diagnosztikai beállítást, tekintse meg az Azure-erőforrások létrehozásakor az [Azure-szabályzatok](../governance/policy/overview.md) automatikus létrehozásával kapcsolatos részleteket a [Azure monitor üzembe helyezése](deploy-scale.md) az Azure-szabályzat használatával című témakörben.

### <a name="enable-insights-and-solutions"></a>Az adatfelismerések és megoldások engedélyezése
Az adatvizsgálatok és megoldások speciális figyelést biztosítanak egy adott szolgáltatáshoz vagy megoldáshoz. Az elemzések a Azure Monitor, például a munkafüzetek újabb funkcióit használják, ezért érdemes betekintést alkalmazni, ha a szolgáltatás számára elérhető. Minden Azure-előfizetésben automatikusan elérhetők, de a teljes funkcionalitáshoz némi konfigurációra lehet szükség. Általában a korábban konfigurált platform-metrikákat és erőforrás-naplókat fogják használni, és további adatokat gyűjthetnek.

A megoldásokat minden előfizetéshez hozzá kell adni, és kizárólag Azure Monitor naplókból származó adatokkal kell dolgozni, és további naplózási adatokat is gyűjthetnek.

A megoldásokért és az elemzésért nem számítunk fel díjat, de az összegyűjtött adatokért fizetnie kell.

Nézze meg, [mi figyeli a Azure monitor?](monitor-reference.md) a Azure monitor rendelkezésre álló információk és megoldások listáját. Minden egyedi konfigurációs vagy díjszabási információért tekintse meg a dokumentációt. 

## <a name="collect-data-from-virtual-machines"></a>Adatok gyűjtése a virtuális gépekről

> [!NOTE]
> Lásd: Azure-beli [virtuális gépek figyelése a Azure Monitorval](vm/monitor-vm-azure.md) teljes körű útmutató a virtuális gépek Azure monitor-vel történő figyeléséhez. 

A virtuális gépek hasonló adatokat hoznak, mint más Azure-erőforrásokat, de szüksége van egy ügynökre, amely adatokat gyűjt a vendég operációs rendszerből. A Azure Monitor által használt ügynökök összehasonlításához tekintse meg [Azure monitor ügynökök áttekintése](agents/agents-overview.md) című témakört. 

A [VM](vm/vminsights-overview.md) -elemzések a log Analytics ügynök és a függőségi ügynök használatával gyűjtenek adatokat a virtuális gépek vendég operációs rendszeréről, így ezeket az információkat az elemzés megvalósításának részeként is üzembe helyezheti. Ez lehetővé teszi, hogy a Log Analytics ügynök más, például Azure Security Center használó szolgáltatásokhoz.


[![Azure-beli virtuális gép ](media/deploy/deploy-azure-vm.png) üzembe helyezése](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-vm-insights"></a>Munkaterület konfigurálása a virtuális gépekhez – áttekintés
A VM-elemzések Log Analytics munkaterületet igényelnek, amely általában ugyanaz, mint a más Azure-erőforrásokból származó adatok gyűjtéséhez létrehozott. A virtuális gépek engedélyezése előtt hozzá kell adnia a virtuálisgép-bepillantást igénylő megoldást a munkaterülethez.

Lásd: [log Analytics munkaterület konfigurálása a VM](vm/vminsights-configure-workspace.md) -információkhoz a log Analytics munkaterület virtuálisgép-információkhoz való konfigurálásának részleteiért.

### <a name="enable-vm-insights-on-each-virtual-machine"></a>Virtuálisgép-bepillantást nyerhet minden virtuális gépen
Miután konfigurálta a munkaterületet, engedélyezheti az egyes virtuális gépeket a Log Analytics ügynök és a függőségi ügynök telepítésével. Az ügynökök telepítésére több módszer is rendelkezésre áll, beleértve a Azure Policy, amelyek lehetővé teszik az egyes virtuális gépek automatikus konfigurálását a létrehozásuk során. A virtuálisgép-elemzések által összegyűjtött teljesítményadatokat és folyamat-adatokat Azure Monitor naplók tárolja.

A virtuális gépekhez tartozó ügynökök üzembe helyezéséhez és a figyeléshez való engedélyezéséhez tekintse meg a [VM-információk áttekintését ismertető](vm/vminsights-enable-overview.md) cikket.

### <a name="configure-workspace-to-collect-events"></a>Munkaterület konfigurálása események gyűjtéséhez
A VM-elemzések a Teljesítményadatok és a folyamatok részleteit és függőségeit gyűjtik össze az egyes virtuális gépek vendég operációs rendszeréről. A Log Analytics ügynök naplókat is gyűjthet a vendégtől, beleértve az eseménynaplót a Windowsból és a syslog-ből a Linuxból. Lekéri a naplók konfigurációját a Log Analytics munkaterületről, amelyhez csatlakozik. Csak egyszer kell konfigurálnia a munkaterületet, és minden alkalommal, amikor egy ügynök csatlakozik, letölti a konfigurációs módosításokat. 

Az ügynök virtuális gépei további adatainak gyűjtéséhez tekintse Log Analytics meg az [ügynök adatforrásait Azure monitorban](agents/agent-data-sources.md) .

> [!NOTE]
> A munkaterületet a teljesítményszámlálók összegyűjtésére is beállíthatja, de ez valószínűleg redundáns a VM-elemzések által összegyűjtött teljesítményadatokat is. A munkaterület által összegyűjtött teljesítményadatokat a rendszer a *perf* táblában tárolja, míg a VM-elemzések által gyűjtött teljesítményadatokat a *InsightsMetrics* tábla tárolja. Csak akkor konfigurálja a teljesítményadatokat a munkaterületen, ha olyan számlálókat követel meg, amelyeket a virtuális gépek által még nem gyűjtöttek be.

### <a name="diagnostic-extension-and-telegraf-agent"></a>Diagnosztikai bővítmény és a-Grafi ügynök
A VM-elemzések a Log Analytics ügynököt használják, amely teljesítményadatokat küld egy Log Analytics munkaterületre, de nem Azure Monitor metrikákat. Ezeknek az adatoknak a metrikák számára történő elküldése lehetővé teszi, hogy a Metrikaböngésző és metrikus riasztásokkal is használható legyen. Ehhez szükség van a diagnosztikai bővítményre a Windows rendszeren és a Linuxon futó, a (z) szolgáltatásban.

Az ügynökök telepítésével és konfigurálásával kapcsolatos részleteket lásd: a [Windows Azure Diagnostics bővítmény (wad) telepítése és konfigurálása](agents/diagnostics-extension-windows-install.md) , valamint [Egyéni metrikák gyűjtése Linux rendszerű virtuális gépekhez a InfluxData-Graf ügynökkel](essentials/collect-custom-metrics-linux-telegraf.md) .


## <a name="monitor-applications"></a>Alkalmazások figyelése
Azure Monitor a [Application Insights](app/app-insights-overview.md)használatával figyeli az egyéni alkalmazásokat, amelyeket minden egyes figyelni kívánt alkalmazáshoz konfigurálnia kell. A konfigurációs folyamat a figyelt alkalmazás típusától és a végrehajtani kívánt figyelési típustól függően eltérő lesz. A Application Insights által gyűjtött adatokat Azure Monitor metrikák, Azure Monitor naplók és az Azure Blob Storage tárolja a szolgáltatástól függően. A teljesítményadatok Azure Monitor Metrikákban és Azure Monitor naplókban is tárolódnak, és nincs szükség további konfigurálásra.

### <a name="create-an-application-resource"></a>Alkalmazás-erőforrás létrehozása
Minden figyelni kívánt alkalmazáshoz létre kell hoznia egy erőforrást Application Insightsban. A Application Insights által gyűjtött adatokat a rendszer a munkaterület-alapú alkalmazások Azure Monitor naplófájljaiban tárolja. A klasszikus alkalmazások naplózási adatait a rendszer külön tárolja a Log Analytics munkaterületről az [adatstruktúra](logs/data-platform-logs.md#data-structure)című cikkben leírtak szerint.

 Az alkalmazás létrehozásakor ki kell választania, hogy a klasszikus vagy a munkaterület-alapú használatát kívánja-e használni. Lásd: [Application Insights-erőforrás létrehozása](app/create-new-resource.md) klasszikus alkalmazás létrehozásához. Munkaterület-alapú alkalmazás létrehozásához tekintse meg a [munkaterület-alapú Application Insights-erőforrások (előzetes verzió)](app/create-workspace-resource.md) című témakört.

### <a name="configure-codeless-or-code-based-monitoring"></a>Kód-vagy programkód-alapú figyelés konfigurálása
Az alkalmazások figyelésének engedélyezéséhez el kell döntenie, hogy a kód nélküli vagy a kód alapú figyelést fogja-e használni. A konfigurációs folyamat a döntéstől és a figyelni kívánt alkalmazás típusától függően eltérő lesz.

A **kód-figyelés** a legegyszerűbben implementálható, és a kód fejlesztése után konfigurálható. Nem igényli a kód frissítését. Az alkalmazástól függően a figyelés engedélyezésével kapcsolatos további információkért tekintse meg a következő forrásokat.

- [Az Azure Web Apps üzemeltetett alkalmazások](app/azure-web-apps.md)
- [Java-alkalmazások](app/java-in-process-agent.md)
- [Az IIS-ben üzemeltetett ASP.NET-alkalmazások Azure-beli virtuális gépen vagy Azure-beli virtuálisgép-méretezési csoporton](app/azure-vm-vmss-apps.md)
- [Az IIS helyszíni virtuális gépen üzemeltetett ASP.NET-alkalmazások](app/monitor-performance-live-website-now.md)


A **kód alapú figyelés** sokkal testreszabható, és további telemetria gyűjt, azonban a Application Insights SDK NuGet csomagjaihoz való függőség hozzáadását igényli. Az alkalmazástól függően a figyelés engedélyezéséről az alábbi forrásokban talál további információt.

- [ASP.NET-alkalmazások](app/asp-net.md)
- [Alkalmazások ASP.NET Core](app/asp-net-core.md)
- [.NET-konzol alkalmazásai](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [Más platformok](app/platforms.md)

### <a name="configure-availability-testing"></a>Rendelkezésre állási teszt konfigurálása
A Application Insights rendelkezésre állási tesztek olyan ismétlődő tesztek, amelyek rendszeres időközönként figyelik az alkalmazás rendelkezésre állását és reagálását a világ különböző pontjain. Létrehozhat egy egyszerű pingelési tesztet ingyenesen, vagy létrehozhat egy webes kérések sorozatából álló sorozatot, amellyel szimulálhatja a hozzárendelt költségeket tartalmazó felhasználói tranzakciókat. 

Lásd: [bármely webhely rendelkezésre állásának figyelése](app/monitor-web-app-availability.md) a különböző típusú tesztek összefoglalásához, valamint a létrehozásuk részleteiről.

### <a name="configure-profiler"></a>A Profiler konfigurálása
A Application Insights Profiler a .NET-alkalmazások teljesítmény-nyomkövetési funkcióit biztosítja. Segít azonosítani a "forró" kód elérési útját, amely a leghosszabb időt veszi igénybe egy adott webes kérelem kezelése során. A Profiler konfigurálásának folyamata az alkalmazás típusától függően változhat. 

A Profiler konfigurálásával kapcsolatos részletekért tekintse meg a [profil éles alkalmazásait az Azure-ban Application Insights](app/profiler-overview.md) .

### <a name="configure-snapshot-debugger"></a>Snapshot Debugger konfigurálása
Snapshot Debugger a Application Insights figyeli a telemetria kivételt a .NET-alkalmazásból, és pillanatképeket gyűjt a legfelső szintű kivételekről, így az éles környezetben felmerülő problémák diagnosztizálásához szükséges információkkal rendelkezik. A Snapshot Debugger konfigurálásának folyamata az alkalmazás típusától függően változik. 

A Snapshot Debugger konfigurálásával kapcsolatos részletekért lásd: a [.NET-alkalmazások kivételeit tartalmazó hibakeresési Pillanatképek](app/snapshot-debugger.md) .


## <a name="visualize-data"></a>Adatok vizualizációja
Az elemzések és megoldások magukban foglalják a saját munkafüzeteit és nézeteiket az adatok elemzéséhez. Ezen kívül saját [vizualizációkat](visualizations.md) is létrehozhat, például a Azure monitor adatok és irányítópultok munkafüzeteit, hogy az Azure-ban más szolgáltatásokból származó adatokkal egyesítse Azure monitor adatait.


### <a name="create-workbooks"></a>Munkafüzetek létrehozása
A Azure Monitorban lévő [munkafüzetek](visualize/workbooks-overview.md) lehetővé teszik, hogy gazdag vizuális jelentéseket hozzon létre a Azure Portalban. A különböző adatkészleteket kombinálhatja Azure Monitor metrikákkal, és Azure Monitor naplókat az egyesített interaktív élmények létrehozásához. A munkafüzetek katalógusát a Azure Monitor menü **munkafüzetek** lapján érheti el. 

Az egyéni munkafüzetek létrehozásával kapcsolatos részletekért tekintse meg [Azure monitor munkafüzeteket](visualize/workbooks-overview.md) .

### <a name="create-dashboards"></a>Irányítópultok létrehozása
Az Azure [irányítópultok](../azure-portal/azure-portal-dashboards.md) az Azure elsődleges irányítópult-technológiája, amely lehetővé teszi, hogy az adatok összeAzure monitor vonásával más szolgáltatásokból származó adatokkal kombinálják az Azure-infrastruktúrát, így egyetlen panelt biztosítanak az Azure-infrastruktúra számára. A Azure Monitor naplókból származó adatokat tartalmazó irányítópult létrehozásával kapcsolatos részletekért tekintse meg a [log Analytics-adatok irányítópultjának létrehozása és megosztása](visualize/tutorial-logs-dashboards.md) című témakört. 

További információ: [Egyéni KPI-irányítópultok létrehozása az Azure Application Insights használatával](app/tutorial-app-dashboards.md) a Application Insights adatokat tartalmazó irányítópult létrehozásával kapcsolatos részletekért. 

## <a name="alerts"></a>Riasztások
A Azure Monitor riasztásai proaktívan értesítik Önt a megfigyelési adataiban azonosított fontos adatmennyiségekről vagy mintákról. Néhány bepillantást követően a rendszer konfiguráció nélkül hoz majd riasztásokat. Más forgatókönyvek esetén [riasztási szabályokat](alerts/alerts-overview.md) kell létrehoznia, amelyek tartalmazzák az elemezni kívánt adatok és a riasztások generálásának időpontját, valamint azokat a műveleti csoportokat, amelyek meghatározzák a riasztás létrehozásakor végrehajtandó műveletet. 


### <a name="create-action-groups"></a>Műveletcsoportok létrehozása
A [műveleti csoportok](alerts/action-groups.md) a riasztási szabályok által használt értesítési beállítások gyűjteményei, amelyek meghatározzák a riasztás indításakor végrehajtandó műveletet. Ilyenek például a levelek vagy szövegek küldése, a webhookok hívása vagy az adatok küldése egy ITSM eszközre. Minden riasztási szabályhoz legalább egy műveleti csoport szükséges, és egyetlen műveleti csoport is használható több riasztási szabály használatával.

A műveleti csoportok létrehozásának és kezelésének részletes ismertetését lásd: [műveleti csoport létrehozása és kezelése a Azure Portalban](alerts/action-groups.md) .


### <a name="create-alert-rules"></a>Riasztási szabályok létrehozása
Több típusú riasztási szabály van megadva az általuk használt adattípussal. Mindegyik különböző képességekkel rendelkezik, és más költségeket is tartalmaz. Az alapszintű stratégia, amelyet követnie kell, a riasztási szabály típusának a legalacsonyabb költségeket kell használnia, amely a szükséges logikát biztosítja.

- [Tevékenység-naplózási szabályok](alerts/activity-log-alerts.md). Riasztást hoz létre egy olyan új műveletnapló eseményre, amely megfelel a megadott feltételeknek. Ezeket a riasztásokat nem kell fizetnie, ezért az első választásnak kell megjelennie. A műveletnapló riasztások létrehozásával kapcsolatos részletekért tekintse meg a [műveletnapló riasztások létrehozása, megtekintése és kezelése Azure monitor segítségével](alerts/alerts-activity-log.md) című témakört.
- [Metrikus riasztási szabályok](alerts/alerts-metric-overview.md). Riasztást hoz létre a küszöbértéket meghaladó egy vagy több metrikai értékre válaszul. A metrikai riasztások állapottal bírnak, ami azt jelenti, hogy a riasztás automatikusan lezárul, ha az érték a küszöbérték alá csökken, és csak akkor küld értesítést, ha az állapot megváltozik. A metrikai riasztások díja, de ez jóval kisebb, mint a naplózási riasztások. A metrikus riasztások létrehozásával kapcsolatos részletekért tekintse meg a [metrikai riasztások létrehozása, megtekintése és kezelése a Azure monitor használatával](alerts/alerts-metric.md) című témakört.
- [Riasztási szabályok naplózása](alerts/alerts-unified-log.md). Riasztást hoz létre, ha egy ütemezett lekérdezés eredményei megfelelnek a megadott feltételeknek. A riasztási szabályok legdrágábbak, de a legbonyolultabb feltételeket teszik lehetővé. A log-lekérdezések riasztásának létrehozásával kapcsolatos részletekért tekintse meg a [naplók létrehozása, megtekintése és kezelése a Azure monitor használatával](alerts/alerts-log.md) című témakört.
- Az [alkalmazások riasztásai](app/monitor-web-app-availability.md) lehetővé teszik a webalkalmazások proaktív teljesítményének és rendelkezésre állásának tesztelését. Egy egyszerű pingelési teszt díjmentesen elvégezhető, de összetettebb tesztelésre is van lehetőség. A különböző tesztek leírását és a létrehozásuk részleteit lásd: [bármely webhely rendelkezésre állásának figyelése](app/monitor-web-app-availability.md) .


## <a name="next-steps"></a>Következő lépések

- Lásd: [Azure monitor üzembe helyezése méretezéssel Azure Policy használatával](deploy-scale.md).