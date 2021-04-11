---
title: Az Azure Service Fabric monitorozási és diagnosztikai áttekintése
description: Ismerje meg az Azure Service Fabric-fürtök,-alkalmazások és-szolgáltatások figyelését és diagnosztizálását.
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: 71ec86f26de1e94b4e17e0990d2eafd1fff954e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627744"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorozás és diagnosztika az Azure Service Fabric

Ez a cikk áttekintést nyújt az Azure Service Fabric monitorozásáról és diagnosztizálásáról. A monitorozás és a diagnosztika kritikus fontosságú a számítási feladatok bármilyen felhőalapú környezetben való fejlesztéséhez, teszteléséhez és üzembe helyezéséhez. Például nyomon követheti az alkalmazások felhasználásának módját, a Service Fabric platform által végrehajtott műveleteket, az erőforrások kihasználtságát a teljesítményszámlálók segítségével, valamint a fürt általános állapotát. Ezeket az információkat felhasználhatja a problémák diagnosztizálásához és kijavításához, és megakadályozhatja, hogy a későbbiekben is megtörténjenek. A következő néhány szakasz röviden ismerteti Service Fabric monitorozás minden területét, hogy figyelembe vegye az éles munkaterheléseket. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Alkalmazások monitorozása
Az alkalmazás figyelése nyomon követi, hogy az alkalmazás mely szolgáltatásait és összetevőit használja. Figyelnie kell az alkalmazásait, hogy a felhasználók által észlelt problémákkal kapcsolatban is legyenek. Az alkalmazások figyelésének feladata az alkalmazások fejlesztését és szolgáltatásait fejlesztő felhasználók számára, mivel ez egyedi az alkalmazás üzleti logikája számára. Az alkalmazások figyelése a következő helyzetekben lehet hasznos:
* Mennyi forgalmat tapasztalok az alkalmazásom? – A szolgáltatásait úgy kell méreteznie, hogy megfeleljenek a felhasználói igényeknek, vagy az alkalmazás potenciális szűk keresztmetszetét?
* A szolgáltatásom sikeres és nyomon követési hívásokat végez?
* Milyen műveleteket végeznek az alkalmazás felhasználói? – A telemetria begyűjtése a jövőbeli szolgáltatások fejlesztését és az alkalmazások hibáinak jobb diagnosztizálását is ismerteti
* Az alkalmazásom nem kezelt kivételeket dobott? 
* Mi történik a saját tárolón belül futó szolgáltatásokban?

Az alkalmazások figyelésének nagyszerű feladata, hogy a fejlesztők bármilyen eszközt és keretrendszert igénybe vehetnek, mivel az alkalmazás kontextusában él. Az alkalmazások figyelésére szolgáló Azure-megoldásról az Azure Monitor-Application Insights az [események elemzése az Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)használatával című témakörben olvashat bővebben.
Emellett a [.NET-alkalmazásokra vonatkozó](service-fabric-tutorial-monitoring-aspnet.md)oktatóanyagot is tartalmazjuk. Ebből az oktatóanyagból megtudhatja, hogyan telepítheti a megfelelő eszközöket, például egyéni telemetria írhat az alkalmazásban, és megtekintheti az alkalmazás diagnosztikai és telemetria a Azure Portal. 


## <a name="platform-cluster-monitoring"></a>Platform (fürt) figyelése
A felhasználók szabályozzák, hogy milyen telemetria származnak az alkalmazásból, mivel a felhasználó maga írja be a kódot, de mi a helyzet a Service Fabric platform diagnosztikát? A Service Fabric egyik célja, hogy a hardveres hibákhoz rugalmasan tartsa az alkalmazásokat. Ez a cél a platform rendszerszolgáltatásai által a fürt más csomópontjaira történő észlelési képességgel és a gyors feladatátvételsel kapcsolatos lehetőségekkel érhető el. De ebben a konkrét esetben mi a helyzet, ha a rendszerszolgáltatások problémái vannak? Vagy ha a számítási feladatok üzembe helyezésére vagy áthelyezésére tett kísérlet során megsértették a szolgáltatások elhelyezésére vonatkozó szabályokat? A Service Fabric diagnosztikai funkciókat biztosít, és így biztos lehet benne, hogy tájékoztat a fürtben zajló tevékenységekről. Néhány példa a fürt figyelésére:

Service Fabric az események széles körét biztosítja a dobozból. Ezek a [Service Fabric események](service-fabric-diagnostics-events.md) a EventStore vagy az operatív csatornán keresztül érhetők el (a platform által elérhető Event Channel). 

* Service Fabric Event channels – a Windowsban Service Fabric az események egyetlen ETW-szolgáltatóból érhetők el, amelyek az `logLevelKeywordFilters` operatív és az adat& üzenetkezelési csatornák közötti választáshoz szükségesek, így a kimenő Service Fabric események igény szerint szűrhetők. Linux rendszeren a Service Fabric események a Lttng érhető el-on keresztül érkeznek, és egy Storage-táblába kerülnek, ahonnan szükség szerint szűrhetők. Ezek a csatornák olyan kurátori és strukturált eseményeket tartalmaznak, amelyek segítségével jobban megismerheti a fürt állapotát. A diagnosztika alapértelmezés szerint engedélyezve van a fürt létrehozási idején, amely létrehoz egy Azure Storage-táblázatot, ahol a csatornákon keresztül érkező eseményeket a rendszer a jövőben lekérdezi. 

* EventStore – a EventStore a platform által kínált olyan szolgáltatás, amely Service Fabric platformon elérhető eseményeket biztosít a Service Fabric Explorer és a REST APIon keresztül. Láthatja, hogy mi történik a fürtben az egyes entitások esetében, például a csomópont, a szolgáltatás, az alkalmazás és a lekérdezés az esemény időpontja alapján. További információt a EventStore a [EventStore áttekintése című](service-fabric-diagnostics-eventstore.md)témakörben talál.    

![Képernyőfelvétel: a csomópontok ablaktábla események lapja, amely több eseményt is magában foglal, például egy NodeDown eseményt.](media/service-fabric-diagnostics-overview/eventstore.png)

A megadott diagnosztika a mezőből álló átfogó események formájában érhető el. Ezek a [Service Fabric események](service-fabric-diagnostics-events.md) illusztrálják a platform különböző entitások, például csomópontok, alkalmazások, szolgáltatások, partíciók stb. által végzett műveleteit. A fenti legutóbbi forgatókönyvben, ha egy csomópontot le kellett lépni, a platform kibocsát egy `NodeDown` eseményt, és azonnal értesítést kaphat a figyelési eszköztől. Más gyakori példák `ApplicationUpgradeRollbackStarted` `PartitionReconfigured` a feladatátvételre vagy azokra. **Ugyanezek az események Windows-és Linux-fürtökön is elérhetők.**

Az eseményeket a Windows és a Linux standard csatornákon keresztül küldik el, és az ezeket támogató figyelési eszközök által is olvashatók. A Azure Monitor megoldás Azure Monitor naplók. Nyugodtan tájékozódhat [Azure monitor naplók integrálásáról](service-fabric-diagnostics-event-analysis-oms.md) , amely tartalmaz egy egyéni operatív irányítópultot a fürthöz, valamint néhány olyan mintát, amelyekről riasztásokat hozhat létre. Több fürt figyelési koncepciója a [platform szintű esemény és a napló létrehozásakor](service-fabric-diagnostics-event-generation-infra.md)érhető el.

### <a name="health-monitoring"></a>Állapotfigyelés
A Service Fabric platform tartalmaz egy Health modellt, amely bővíthető állapot-jelentéskészítést biztosít a fürtben lévő entitások állapotára vonatkozóan. Az egyes csomópontok, alkalmazások, szolgáltatások, partíciók, replikák vagy példányok folyamatosan frissíthető állapottal rendelkeznek. Az állapot állapota "OK", "figyelmeztetés" vagy "hiba" lehet. Gondoljon Service Fabric eseményekre, mint a fürt által a különböző entitásokra és az állapotra, valamint az egyes entitásokra vonatkozó melléknévre végzett műveletekre. Egy adott entitás állapotának minden alkalommal történő elküldésekor a rendszer egy eseményt is kibocsát. Így beállíthat lekérdezéseket és riasztásokat a figyelési eszközben, a többi eseményhez hasonlóan. 

Emellett azt is lehetővé tesszük, hogy a felhasználók felülbírálják az entitások állapotát. Ha az alkalmazás egy frissítésen keresztül zajlik, és az érvényesítési tesztek sikertelenek, a Health API használatával Service Fabric állapotba írhatja, hogy az alkalmazás már nem kifogástalan állapotú, és Service Fabric automatikusan visszaállítják a frissítést. További információ az állapotfigyelő modellről: a [Service Fabric Health monitoring bemutatása](service-fabric-health-introduction.md)

![SFX állapot irányítópult](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Házőrző
A watchdog általában egy különálló szolgáltatás, amely az állapotot és a terhelést a szolgáltatásokban, a ping végpontokat és a jelentések állapotát a fürtben található összes adatnál megtekintheti. Ez segít megakadályozni azokat a hibákat, amelyek nem észlelhetők egyetlen szolgáltatás nézete alapján. A watchdogok olyan kód futtatására is alkalmasak, amely felhasználói beavatkozás nélkül végez javító műveleteket (például a naplófájlok bizonyos időközönkénti tisztítása). [Itt](https://github.com/Azure-Samples/service-fabric-watchdog-service)talál egy példa watchdog Service-implementációt.

## <a name="infrastructure-performance-monitoring"></a>Infrastruktúra (teljesítmény) figyelése
Most, hogy a diagnosztika az alkalmazásban és a platformon is szerepel, honnan tudható, hogy a hardver a várt módon működik? A mögöttes infrastruktúra monitorozása kulcsfontosságú része a fürt állapotának és az erőforrás-felhasználásnak. A rendszer teljesítményének mérése számos olyan tényezőtől függ, amelyek a munkaterheléstől függően szubjektívek lehetnek. Ezeket a tényezőket általában a teljesítményszámlálók mérik. Ezek a teljesítményszámlálók különböző forrásokból származhatnak, beleértve az operációs rendszert, a .NET-keretrendszert vagy a Service Fabric platformot is. Egyes esetekben hasznosak lehetnek

* Hatékonyan használom a hardvert? A hardvert a 90%-os CPU vagy a 10%-os CPU használatával kívánja használni. Ez a fürt skálázásakor, illetve az alkalmazás folyamatainak optimalizálásakor hasznos.
* Proaktív módon tudom előre megjósolni az infrastrukturális problémákat? – számos problémát megelőz a teljesítmény hirtelen változása (Drops), így a teljesítményszámlálók, például a hálózati I/O és a CPU-kihasználtság használatával előre megjósolhatja és diagnosztizálhatja a problémákat.

Az infrastruktúra szintjén összegyűjthető teljesítményszámlálók listáját a [teljesítmény mérőszámok](service-fabric-diagnostics-event-generation-perf.md)között találja. 

A Service Fabric a Reliable Services és a Actors programozási modellekhez is biztosít teljesítményszámlálókat. Ha ezeket a modelleket használja, ezek a teljesítményszámlálók olyan információkkal szolgálnak, amelyek biztosítják, hogy a szereplők megfelelően fel-és lelassulnak, vagy hogy a megbízható szolgáltatási kérelmek kezelése elég gyors legyen. További információ: a [megbízható szolgáltatás távelérésének figyelése](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) és [a Reliable Actors teljesítményének figyelése](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

A Azure Monitor-megoldás, amely összegyűjti ezeket, ugyanúgy Azure Monitor naplók, mint a platform szintű monitorozás. A megfelelő teljesítményszámlálók összegyűjtéséhez használja a [log Analytics ügynököt](service-fabric-diagnostics-oms-agent.md) , és tekintse meg őket a Azure monitor-naplókban.

## <a name="recommended-setup"></a>Ajánlott beállítás
Most, hogy elvégezte a monitorozást és a példákat az egyes területeken, itt látható az Azure monitoring-eszközök összefoglalása, és az összes fenti terület figyeléséhez szükséges beállítás. 

* Alkalmazások figyelése [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Fürt figyelése [diagnosztikai ügynökkel](service-fabric-diagnostics-event-aggregation-wad.md) és [Azure monitor naplók](service-fabric-diagnostics-oms-setup.md)
* Infrastruktúra-figyelés [Azure monitor naplókkal](service-fabric-diagnostics-oms-agent.md)

Az [itt](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) található minta ARM-sablont is használhatja és módosíthatja az összes szükséges erőforrás és ügynök üzembe helyezésének automatizálásához. 

## <a name="other-logging-solutions"></a>Egyéb naplózási megoldások

Bár a javasolt két megoldás, [Azure monitor a naplók](service-fabric-diagnostics-event-analysis-oms.md) és a [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) a Service Fabric-nal való integrációra épülnek, számos eseményt ETW-szolgáltatókon keresztül kell kiírni, és más naplózási megoldásokkal bővíthetők. Érdemes áttekinteni a [rugalmas veremet](https://www.elastic.co/products) is (különösen akkor, ha egy fürt offline környezetben való futtatását tervezi), [Dynatrace](https://www.dynatrace.com/)vagy bármely más platformot. Az itt elérhető integrált partnereink listáját [itt](service-fabric-diagnostics-partners.md)találja.

Az Ön által választott platform főbb pontjainak tartalmaznia kell a felhasználói felület, a lekérdezési képességek, a rendelkezésre álló egyéni vizualizációk és irányítópultok, valamint az általuk biztosított további eszközök használatát a figyelési élmény fokozása érdekében. 

## <a name="next-steps"></a>Következő lépések

* Az alkalmazások kialakításának első lépéseiért tekintse meg az [alkalmazás szintű esemény és a napló létrehozása](service-fabric-diagnostics-event-generation-app.md)című témakört.
* Hajtsa végre az alkalmazáshoz való Application Insights beállításának lépéseit, [és figyelje meg, hogyan diagnosztizálhatja a ASP.net Core alkalmazást a Service Fabricon](service-fabric-tutorial-monitoring-aspnet.md).
* További információ a platform figyeléséről és az események Service Fabric biztosít a [platform szintű esemény és a napló létrehozásához](service-fabric-diagnostics-event-generation-infra.md).
* A Azure Monitor naplók integrációjának konfigurálása a Service Fabrichoz a [fürt Azure monitor naplófájljainak beállításakor](service-fabric-diagnostics-oms-setup.md)
* Ismerje meg, hogyan állíthat be Azure Monitor naplókat a tárolók figyeléséhez – [figyelés és diagnosztika Windows-tárolók számára az Azure Service Fabricban](service-fabric-tutorial-monitoring-wincontainers.md).
* A [gyakori forgatókönyvek diagnosztizálásával](service-fabric-diagnostics-common-scenarios.md) kapcsolatban lásd: diagnosztikai problémák és megoldások Service Fabric
* Tekintse meg a [Service Fabric diagnosztikai partnerei](service-fabric-diagnostics-partners.md) Service Fabrickal integrált diagnosztikai termékeket
* Ismerje meg az Azure-erőforrások általános figyelési javaslatait – [ajánlott eljárások – monitorozás és diagnosztika](/azure/architecture/best-practices/monitoring). 
