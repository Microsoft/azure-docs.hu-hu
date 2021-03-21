---
title: Azure Monitor meglévő Operations Manager ügyfelek számára
description: Útmutató a Operations Manager meglévő felhasználói számára, hogy a felhőbe való áttérés részeként bizonyos számítási feladatok monitorozását Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: 6d92b7c2f01a7e9ef12bc2bb422cfb6ed0076f73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039376"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Azure Monitor meglévő Operations Manager ügyfelek számára
Ez a cikk útmutatást nyújt azokhoz az ügyfelekhez, akik jelenleg használják a [System Center Operations Managert](/system-center/scom/welcome) , és az üzleti alkalmazások és egyéb erőforrások az Azure-ba való áttelepítésének megtervezése [Azure Monitorre](overview.md) Azt feltételezi, hogy a végső cél a felhőbe való teljes átállás, amely a lehető legtöbb Operations Manager funkciót helyettesíti a Azure Monitor, az üzleti és informatikai működési követelmények veszélyeztetése nélkül. 

Az ebben a cikkben ismertetett javaslatok Azure Monitor és Operations Manager szolgáltatások hozzáadása lehetőséggel változnak. Az alapvető stratégia azonban továbbra is konzisztens marad.

> [!IMPORTANT]
> Az itt ismertetett Azure Monitor szolgáltatások megvalósítása többek között azért van, hogy a teljes környezetbe való üzembe helyezés előtt értékelje az értékét.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy már használja a [Operations Managert](/system-center/scom) , és legalább [Azure monitor](overview.md)alapvető ismeretekkel rendelkezik. A kettő közötti teljes összehasonlítást lásd: a [felhőalapú figyelési útmutató: figyelési platformok áttekintése](/azure/cloud-adoption-framework/manage/monitor/platform-overview). Ez a cikk az egyes szolgáltatásokra vonatkozó, a kettő közötti különbségeket ismerteti, amelyek segítenek az itt ismertetett javaslatok megismerésében. 


## <a name="general-strategy"></a>Általános stratégia
Nincs áttelepítési eszköz az eszközök Operations Managerról Azure Monitorra való átalakításához, mivel a platformok alapvetően eltérőek. A Migrálás Ehelyett [standard Azure monitor megvalósítást](deploy.md) eredményez, miközben továbbra is használja a Operations Manager. Ahogy testreszabja Azure Monitor, hogy megfeleljen a különböző alkalmazásokra és összetevőkre vonatkozó követelményeinek, és mivel további funkciókat is kínál, megkezdheti a különböző felügyeleti csomagok és ügynökök kivonását Operations Managerokban.

A cikkben javasolt általános stratégia ugyanaz, mint a [felhő-figyelési útmutatóban](/azure/cloud-adoption-framework/manage/monitor/), amely a [hibrid felhőalapú figyelési](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) stratégiát javasolja, amely lehetővé teszi a felhőbe való fokozatos áttérést. Bár egyes funkciók átfedésben lehetnek, ez a stratégia lehetővé teszi, hogy megőrizze meglévő üzleti folyamatait, ahogy az új platformmal jobban megismerik. Csak Operations Manager-funkciókból való elmozdulás, mert a Azure Monitorre lecserélheti. A több figyelési eszköz használata bonyolultságot jelent, de lehetővé teszi, hogy kihasználhassa a Azure Monitor a Felhőbeli számítási feladatok figyelésére való képességet, miközben megtartja a Operations Manager képességét a helyszíni vagy más felhőkben található kiszolgálói szoftverek és infrastruktúra-összetevők figyelésére. 


## <a name="components-to-monitor"></a>Figyelni kívánt összetevők
Segít kategorizálni azokat a különböző típusú munkaterheléseket, amelyeket figyelni kell ahhoz, hogy külön megfigyelési stratégiát lehessen meghatározni. [Felhő-figyelési útmutató: a figyelési stratégia kialakítása](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) részletesen lebontja a környezet különböző rétegeit, amelyeknek figyelésre van szükségük a régi vállalati alkalmazások és a Felhőbeli modern alkalmazások terén.

A felhő előtt a Operations Manager az összes réteg figyelésére használta. Az infrastruktúra-szolgáltatásként (IaaS) való áttérés megkezdése után továbbra is Operations Managert használ a virtuális gépekhez, de elkezdi használni Azure Monitor a Felhőbeli erőforrásaihoz. Ahogy a szolgáltatásként nyújtott platform ("Pásti") használatával továbbra is átvált a modern alkalmazásokra, jobban összpontosíthat Azure Monitorra, és megkezdheti Operations Manager funkcióinak kivonását.


![Felhőbeli modellek](/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

Ezek a rétegek a következő kategóriákba foglalhatók, amelyeket a cikk további részében ismertetünk. Habár előfordulhat, hogy a környezet minden figyelési munkaterhelése nem fér bele tökéletesen az egyik kategóriába, ezért mindegyiknek elég egy adott kategóriához tartoznia az általános javaslatok alkalmazásához.

**Üzleti alkalmazások.** Alkalmazások, amelyek a vállalatra jellemző funkciókat biztosítanak. Lehet, hogy belső vagy külső, és az egyéni kóddal általában belsőleg vannak kifejlesztve. Az örökölt alkalmazásai általában Windows vagy Linux rendszerű virtuális vagy fizikai gépeken futnak, míg az újabb alkalmazások az Azure-ban, például az Azure Web Apps és a Azure Functions alkalmazás-szolgáltatásokon alapulnak.

**Azure-szolgáltatások.** Az Azure-ban olyan erőforrások, amelyek támogatják a felhőbe áttelepített üzleti alkalmazásokat. Ez olyan szolgáltatásokat is magában foglal, mint például az Azure Storage, az Azure SQL és az Azure IoT. Ez magában foglalja az Azure-beli virtuális gépeket is, mivel azok a többi Azure-szolgáltatáshoz hasonlóan vannak figyelve, de a virtuális gépek vendég operációs rendszerén futó alkalmazásoknak és szoftvereknek több figyelésre van szükségük a gazdagépen kívül.

**Kiszolgálói szoftver.** Virtuális és fizikai gépeken futó szoftver, amely támogatja az üzleti alkalmazásokat, vagy olyan csomagolt alkalmazásokat, amelyek általános funkcionalitást biztosítanak a vállalat számára. Ilyenek például az Internet Information Server (IIS), a SQL Server, az Exchange és a SharePoint. Ez magában foglalja a Windows vagy Linux operációs rendszert is a virtuális és fizikai gépeken.

**Helyi infrastruktúra.** A helyszíni környezethez kapcsolódó, figyelést igénylő összetevők. Ide tartoznak a fizikai kiszolgálók, a tárolók és a hálózati összetevők. Ezek azok az összetevők, amelyek virtualizálva vannak a felhőre való áttéréskor.

## <a name="sample-walkthrough"></a>Mintaútmutató
Az alábbiakban a Operations Managerról Azure Monitorre történő áttelepítésre vonatkozó feltételezett útmutató. Ez nem felel meg a tényleges áttelepítés teljes összetettségének, de legalább az alapszintű lépéseket és sorozatot biztosítja. Az alábbi szakaszok részletesebben ismertetik ezeket a lépéseket.

A környezet az Azure-ba való áthelyezést megelőzően a helyszínen vagy egy felügyelt szolgáltatón belüli virtuális és fizikai gépeken alapul. A Operations Manager az üzleti alkalmazások, a kiszolgálói szoftverek és a környezet más infrastrukturális összetevőinek, például a fizikai kiszolgálók és a hálózatok figyelésére támaszkodik. Szabványos felügyeleti csomagokat használ a kiszolgálói szoftverekhez, például az IIS-hez, SQL Serverhoz és különböző gyártói szoftverekhez, és ezeket a felügyeleti csomagokat az adott követelményekhez hangolhatja. Egyéni felügyeleti csomagokat hoz létre az üzleti alkalmazásaihoz és más összetevőkhöz, amelyek nem figyelhetők meg a meglévő felügyeleti csomagokkal, és nem konfigurálható Operations Manager az üzleti folyamatok támogatásához.

Az Azure-ba való Migrálás a IaaS-val kezdődik, és az üzleti alkalmazásokat támogató virtuális gépeket az Azure-ba helyezi. Ezeknek az alkalmazásoknak és a kiszolgálói szoftvereknek a figyelésére vonatkozó követelmények nem változnak, és továbbra is a meglévő felügyeleti csomagokkal folytathatja a Operations Manager használatát ezeken a kiszolgálókon. 

Az Azure-előfizetések létrehozásakor a Azure Monitor az Azure-szolgáltatásokhoz is engedélyezve van. A szolgáltatás automatikusan gyűjti a platform metrikáit és a tevékenység naplóját, és az erőforrás-naplókat úgy konfigurálja, hogy a rendszer az összes rendelkezésre álló telemetria interaktív módon elemezheti a naplók lekérdezésével. A virtuális gépeken a virtuálisgép-elemzések segítségével elemezheti a teljes környezet figyelési adatait, és felderítheti a gépek és a folyamatok közötti kapcsolatokat. Az Azure arc-kompatibilis kiszolgálók engedélyezésével kiterjesztheti Azure Monitor használatát a helyszíni fizikai és virtuális gépekre. 

Minden üzleti alkalmazás esetében engedélyezheti a Application Insights. Azonosítja az egyes alkalmazások különböző összetevőit, megkezdi a használati és teljesítményadatok gyűjtését és a kódban előforduló hibák azonosítását. Rendelkezésre állási teszteket hozhat létre a külső alkalmazások proaktív teszteléséhez, és riasztást küld a teljesítményre és a rendelkezésre állásra vonatkozó problémákra. Bár a Application Insights olyan hatékony szolgáltatásokat nyújt, amelyek nem szerepelnek a Operations Managerban, továbbra is az üzleti alkalmazásaihoz fejlesztett egyéni felügyeleti csomagokat használja, mivel azok olyan figyelési forgatókönyveket tartalmaznak, amelyeket a Azure Monitor még nem szabályoz. 

A Azure Monitor megismeréséhez olyan riasztási szabályokat hozhat létre, amelyek képesek a felügyeleti csomagok bizonyos funkcióinak cseréjére, és megkezdik az üzleti folyamatok fejlesztését az új figyelési platform használatára. Ez lehetővé teszi, hogy elindítsa a gépek és a felügyeleti csomagok eltávolítását a Operations Manager felügyeleti csoportból. Továbbra is a felügyeleti csomagokat használja a kritikus kiszolgálói szoftverekhez és a helyszíni infrastruktúrához, de továbbra is figyelje a Azure Monitor új funkcióit, amelyek lehetővé teszik további funkciók kivonását.

## <a name="monitor-azure-services"></a>Azure-szolgáltatások figyelése
Az Azure-szolgáltatásokhoz ténylegesen Azure Monitor szükséges a telemetria gyűjtéséhez, és lehetővé teszi az Azure-előfizetések létrehozásának pillanatát. A rendszer automatikusan összegyűjti a [tevékenység naplóját](essentials/activity-log.md) az előfizetéshez, és a rendszer automatikusan összegyűjti a [platform metrikáit](essentials/data-platform-metrics.md) a létrehozott Azure-erőforrásoktól. Azonnal megkezdheti a [metrikák Explorer](essentials/metrics-getting-started.md)használatát, amely hasonló az operatív konzol teljesítmény nézeteihez, de interaktív elemzést és [speciális adatösszesítéseket](essentials/metrics-charts.md) is biztosít. [Hozzon létre egy metrikai riasztást](alerts/alerts-metric.md) , amely értesítést kap, ha egy érték túllép egy küszöbértéket, vagy [hozzáadhat egy diagramot egy Azure-irányítópulthoz](essentials/metrics-charts.md#pinning-to-dashboards) a láthatóság érdekében.

[![Metrikaböngésző](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

[Hozzon létre egy diagnosztikai beállítást](essentials/diagnostic-settings.md) minden egyes Azure-erőforráshoz mérőszámok és [erőforrás-naplók](essentials/resource-logs.md)küldéséhez, amelyek részletesen ismertetik az egyes erőforrások belső műveleteit log Analytics munkaterületre. Ez biztosítja az összes rendelkezésre álló telemetria az erőforrásokhoz, és lehetővé teszi, hogy a [log Analytics](logs/log-analytics-overview.md) segítségével interaktívan elemezze a napló-és teljesítményadatokat olyan speciális lekérdezési nyelv használatával, amely nem egyenértékű a Operations Manager. Létrehozhat [napló-lekérdezési riasztásokat](alerts/alerts-log-query.md)is, amelyek összetett logikát használhatnak a riasztási feltételek meghatározásához és az adatkezeléshez több erőforrás között.

[![Naplók Analytics](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

Az Azure Monitor a felügyeleti csomagokhoz hasonló, az adott Azure-szolgáltatás egyedi figyelését biztosító [betekintést](monitor-reference.md) biztosít. Az adatellenőrzések jelenleg több szolgáltatáshoz, például hálózatkezeléshez, tároláshoz és tárolóhoz érhetők el, mások pedig folyamatosan bővülnek.

[![Példa a betekintésre](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


Az adatok a Azure Monitorban lévő [munkafüzeteken](visualize/workbooks-overview.md) alapulnak, amelyek a mérőszámokat és a naplóbeli lekérdezéseket gazdag interaktív jelentésekkel ötvözik. Hozzon létre saját munkafüzeteket több szolgáltatásból származó adatok összevonásához, hasonlóan ahhoz, hogyan hozhat létre egyéni nézeteket és jelentéseket az operatív konzolon.

### <a name="azure-management-pack"></a>Azure felügyeleti csomag
Az [Azure felügyeleti csomagja](https://www.microsoft.com/download/details.aspx?id=50013) lehetővé teszi Operations Manager számára az Azure-erőforrások felderítését és az állapotuk figyelését egy adott megfigyelési forgatókönyv alapján. Ehhez a felügyeleti csomaghoz további konfigurációt kell végrehajtani az Azure-ban, de hasznos lehet az Azure-erőforrások láthatóságának biztosítása az operatív konzolon, amíg nem fejleszti az üzleti folyamatokat a Azure Monitorra való összpontosítás érdekében.

[![Azure felügyeleti csomag](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 Dönthet úgy is, hogy az Azure felügyeleti csomagot használja, ha az operatív konzolon bizonyos Azure-erőforrásokkal szeretné megtekinteni a láthatóságot, valamint a meglévő folyamatokkal kapcsolatos alapvető riasztások integrálását. Valójában a Azure Monitor által összegyűjtött adatokat használja. Az Azure-erőforrások hosszú távú teljes monitorozása érdekében Azure Monitort kell megkeresnie. 


## <a name="monitor-server-software-and-local-infrastructure"></a>A kiszolgálói szoftver és a helyi infrastruktúra figyelése
Ha a gépeket a felhőbe helyezi át, a szoftver figyelési követelményei nem változnak. A továbbiakban nem kell figyelnie a fizikai összetevőket, mivel azok virtualizáltak, a vendég operációs rendszernek és a számítási feladatainak azonban a környezettől függetlenül ugyanazok a követelmények.

A virtuális gépek és a vendég operációs rendszerek és a munkaterhelések figyelése Azure Monitor elsődleges funkciója a virtuálisgép- [bepillantást](vm/vminsights-overview.md) . A Operations Managerhoz hasonlóan a VM-elemzések ügynök használatával gyűjtenek adatokat a virtuális gépek vendég operációs rendszeréről. Ez ugyanaz a teljesítmény-és esemény-adat, amelyet általában a felügyeleti csomagok használnak az elemzéshez és a riasztáshoz. Nem léteznek még meglévő szabályok az adott gépeken futó üzleti alkalmazások és kiszolgálói szoftverek problémáinak azonosítására és riasztására. Létre kell hoznia saját riasztási szabályait, hogy proaktívan értesüljön az észlelt problémákról.

[![VIRTUÁLIS gépekkel kapcsolatos eredmények](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor emellett nem méri a virtuális gépen futó különböző alkalmazások és szolgáltatások állapotát. A metrikai riasztások automatikusan feloldhatók, ha egy érték egy küszöbérték alá csökken, de Azure Monitor jelenleg nem tudja meghatározni a gépen futó alkalmazások és szolgáltatások állapotának feltételeit, és nem biztosítja az állapot összesítését a kapcsolódó összetevők állapotának csoportosításához.

> [!NOTE]
> Mostantól nyilvános előzetes verzióban érhető el a virtuális gépek új [vendég állapotának szolgáltatása](vm/vminsights-health-overview.md) , amely a teljesítmény-mérőszámok egy készletének állapota alapján riasztást küld. Ez kezdetben a vendég operációs rendszerhez kapcsolódó teljesítményszámlálók adott készletére korlátozódik, a virtuális gépen futó alkalmazások és egyéb számítási feladatok azonban nem.
> 
> [![A virtuális gép bepillantást nyújt a vendég állapotára](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

A gépek hibrid környezetben történő figyelése jellemzően a virtuálisgép-bepillantást és a Operations Manager kombinációját használja, az egyes gépek követelményeitől és a lejárat a Azure Monitor-on belüli működési folyamatainak kiépítésével függően. Mindkét platform használja a Microsoft Management agentet (amelyet a Azure Monitor Log Analytics ügynökének nevezünk), így egyszerre egyetlen gépet is megfigyelheti.

> [!NOTE]
> A jövőben a virtuálisgép-bepillantást a [Azure monitor ügynökre](agents/azure-monitor-agent-overview.md)vált, amely jelenleg nyilvános előzetes verzióban érhető el. Kompatibilis lesz a Microsoft monitoring Agent szolgáltatással, így ugyanaz a virtuális gép továbbra is képes lesz figyelni mindkét platformon.

Továbbra is használhatja a Operations Manager olyan funkciókhoz, amelyeket a Azure Monitor még nem biztosít. Ide tartoznak a kritikus kiszolgálói szoftverek, például az IIS, az SQL Server vagy az Exchange felügyeleti csomagjai. Emellett olyan egyéni felügyeleti csomagokat is használhat, amelyek olyan helyszíni infrastruktúrához lettek kifejlesztve, amely nem érhető el Azure Monitor. Továbbra is használhatja a Operations Manager, ha szorosan integrálva van az operatív folyamataiba, amíg át nem kerül a szolgáltatási műveletek korszerűsítésére, ha Azure Monitor és más Azure-szolgáltatások bővíteni vagy helyettesíteni lehet. 

Azure Monitor fo virtuális gépek használatával növelheti az aktuális monitorozást, még akkor is, ha az nem azonnal helyettesíti Operations Manager. A Azure Monitor egyedi funkciói például a következők:

- A virtuális gépek és a külső függőségek közötti kapcsolatok felderítése és figyelése.
- Az összesített teljesítményadatokat több virtuális gép között tekintheti meg interaktív diagramokban és munkafüzetekben.
- A [naplók](logs/log-query-overview.md) használatával interaktív módon elemezheti a telemetria a virtuális gépekről a többi Azure-erőforrásból származó adatokkal.
- A [naplózási riasztási szabályokat](alerts/alerts-log-query.md) több virtuális gép összetett logikája alapján hozhatja létre.

[![VM-alapú bepillantást Térkép](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

Az Azure Virtual Machines szolgáltatáson kívül a VM-információk az [Azure arc-kompatibilis kiszolgálók](../azure-arc/servers/overview.md)használatával is megfigyelhetők a helyszíni és más Felhőbeli gépeken. Az arc-kompatibilis kiszolgálók lehetővé teszik az Azure-on kívül üzemeltetett Windows-és Linux-gépek, a vállalati hálózat vagy más felhőalapú szolgáltatók felügyeletét a natív Azure-beli virtuális gépek kezelésével összhangban.



## <a name="monitor-business-applications"></a>Üzleti alkalmazások figyelése
Általában egyéni felügyeleti csomagokra van szükség az üzleti alkalmazások figyeléséhez Operations Manager, az egyes virtuális gépeken telepített ügynökökkel. A Azure Monitor Application Insights figyeli a webalapú alkalmazásokat, függetlenül attól, hogy az Azure-ban, más felhőkben vagy a helyszínen vannak, így az összes alkalmazásához használható, függetlenül attól, hogy az Azure-ba lettek-e telepítve.

Ha az üzleti alkalmazások figyelése a Operations Manager [.NET-alkalmazás teljesítmény-sablonja]() által biztosított funkciókra korlátozódik, akkor a legvalószínűbb, hogy a funkció elvesztése nélkül Application Insights. Valójában a Application Insights jelentős számú további funkciót tartalmaz, többek között az alábbiakat:

- Alkalmazás-összetevők automatikus felderítése és figyelése.
- Részletes alkalmazás-használati és teljesítményadatokat, például a válaszidő, a meghibásodási arány és a kérelmek díjszabását gyűjti.
- Összegyűjtheti a böngésző adatait, például a lapok nézeteit és a terhelési teljesítményt.
- Kivételek észlelése és részletezése verem-nyomkövetési és kapcsolódó kérelmek esetén.
- Speciális elemzések elvégzése olyan funkciók használatával, mint az [elosztott nyomkövetés](app/distributed-tracing.md) és az [intelligens észlelés](app/proactive-diagnostics.md).
- A [metrika-kezelő](essentials/metrics-getting-started.md) használatával interaktívan elemezheti a teljesítményadatokat.
- A [naplók](logs/log-query-overview.md) segítségével interaktív módon elemezheti az összegyűjtött telemetria, valamint az Azure-szolgáltatásokhoz és a virtuális gépekhez gyűjtött adatokhoz kapcsolódó adatokat.

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

Bizonyos esetekben előfordulhat, hogy a Application Insights mellett továbbra is szükség van Operations Manager használatára, amíg el nem éri a szükséges funkciókat. Például a következőkre lehet szükség a Operations Manager folytatásához:

-  A [rendelkezésre állási tesztek](app/monitor-web-app-availability.md)lehetővé teszik az alkalmazások rendelkezésre állásának és reagálásának figyelését és riasztását, ha a webteszt-ügynökök IP-címeiről érkező kéréseket igényelnek. Ha a házirend nem engedélyezi az ilyen hozzáférést, akkor előfordulhat, hogy továbbra is a [webalkalmazás rendelkezésre állási figyelőit](/system-center/scom/web-application-availability-monitoring-template) kell használnia a Operations Manager.
- Operations Manager megadhatja a rendelkezésre állási tesztek lekérdezési időközét, és sok ügyfél ellenőrzi a 60-120 másodpercenkénti számát. Application Insights legalább 5 perces lekérdezési időközt tartalmaz, amely egyes ügyfelek esetében túl hosszú lehet.
- Az alkalmazások által generált események összegyűjtésével és a helyi ügynökön futó parancsfájlok futtatásával jelentős mennyiségű figyelési Operations Manager történik. Ezek nem a szokásos lehetőségek a Application Insightsban, így az üzleti igények kielégítése érdekében egyéni munkát igényelhet. Ez tartalmazhat egyéni riasztási szabályokat a Log Analytics munkaterületen tárolt eseményazonosító és a [hibrid runbook-feldolgozót](../automation/automation-hybrid-runbook-worker.md)használó virtuális gépek vendégei által indított parancsfájlok használatával.
- Attól függően, hogy az alkalmazás milyen nyelven íródott, korlátozhatja a [Application Insights használatával használható rendszerállapotot](app/platforms.md).

Az útmutató további részeiben az alapszintű stratégiát követve továbbra is használhatja a Operations Manager üzleti alkalmazásaihoz, de kihasználhatja a Application Insights által nyújtott további szolgáltatásokat. Mivel a kritikus funkciókat a Azure Monitor segítségével helyettesíthetik, megkezdheti az egyéni felügyeleti csomagok kivonását.


## <a name="next-steps"></a>Következő lépések

- A hibrid figyelési környezet tervezésével és megvalósításával kapcsolatos további részletekért tekintse meg a [felhőalapú figyelési útmutatót](/azure/cloud-adoption-framework/manage/monitor/) a Azure Monitor és System Center Operations Manager részletes összehasonlításához.
- További információ a [Azure monitor Azure-erőforrások monitorozásáról](essentials/monitor-azure-resource.md).
- További információ a [Azure monitor Azure-beli virtuális gépek monitorozásáról](vm/monitor-vm-azure.md).
- További információ a [virtuális](vm/vminsights-overview.md)gépekről.
- További információ a [Application Insightsról](app/app-insights-overview.md).