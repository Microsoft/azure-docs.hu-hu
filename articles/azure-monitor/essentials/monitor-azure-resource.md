---
title: Azure-erőforrások figyelése Azure Monitor | Microsoft Docs
description: Leírja, hogyan gyűjtheti és elemezheti az Azure-beli erőforrásokból származó monitorozási adatokat a Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: cb778d826ef094d71fd27f3c10bc1f2c292baa47
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862398"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Azure-erőforrások monitorozása az Azure Monitor segítségével
Ha kritikus fontosságú alkalmazásokkal és üzleti folyamatokkal rendelkezik, amelyek Azure-erőforrásokra támaszkodnak, monitorozással szeretné figyelni az erőforrások rendelkezésre állását, teljesítményét és működését. Ez a cikk bemutatja az Azure-erőforrások által létrehozott monitorozási adatokat, és hogy hogyan használhatja a Azure Monitor funkcióit az adatok elemzéséhez és riasztáshoz.

> [!IMPORTANT]
> Ez a cikk az Azure összes olyan szolgáltatására vonatkozik, amely Azure Monitor. A számítási erőforrások, beleértve a virtuális gépeket és App Service is, az itt ismertetett monitorozási adatokat generálják, de egy vendég operációs rendszerrel is rendelkezik, amely naplókat és metrikákat is generálhat. Az adatok gyűjtésével és elemzésével kapcsolatos részletekért tekintse meg ezeknek a szolgáltatásoknak a monitorozási dokumentációját.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
Azure Monitor egy teljes körű monitorozási szolgáltatás az Azure-ban, amely a más felhőkben és a helyszínen található erőforrások mellett az Azure-erőforrások monitorozására is teljes körű funkciókat biztosít. A [Azure Monitor adatplatform](../data-platform.md) naplókba [](../essentials/data-platform-metrics.md) és metrikákba gyűjti az adatokat, ahol együtt elemezhetők a monitorozási eszközök teljes készletével. [](../logs/data-platform-logs.md) A miben figyelt alkalmazások és szolgáltatások teljes listáját a Azure Monitor által figyelt alkalmazások és [szolgáltatások? Azure Monitor.](../monitor-reference.md)

Amint létrehoz egy Azure-erőforrást, a Azure Monitor engedélyezve lesz, és megkezdi a metrikák és tevékenységnaplók gyűjtését, amelyeket megtekinthet és elemezhet a [Azure Portal.](#monitoring-in-the-azure-portal) Bizonyos konfigurációk esetén további monitorozási adatokat gyűjthet, és további funkciókat engedélyezhet. A [konfigurációs követelményekkel kapcsolatos](#monitoring-data) részletekért tekintse meg az alábbi Monitorozási adatok adatokat.


## <a name="costs-associated-with-monitoring"></a>A monitorozással kapcsolatos költségek
Az alapértelmezés szerint gyűjtött monitorozási adatok elemzése nem jár költséggel. Ezek a következők lehetnek:

- Platformmetrikák gyűjtése és elemzése a Metrikák explorerrel.
- Tevékenységnapló gyűjtése és elemzése a Azure Portal.
- Tevékenységnapló-riasztási szabály létrehozása.

A naplók Azure Monitor és exportálása nem jár további költségekkel, de a célhelyhez kapcsolódó költségek is előfordulhatnak:

- Az adatbegyűjtéssel és -megőrzéssel kapcsolatos költségek a naplók és metrikák Log Analytics-munkaterületen való gyűjtésekor. Lásd [Azure Monitor Log Analytics díjszabását.](https://azure.microsoft.com/pricing/details/monitor/)
- Az adattárolással kapcsolatos költségek, amikor naplókat és metrikákat gyűjt egy Azure-tárfiókba. Lásd: [Az Azure Storage díjszabása a Blob Storage-hoz.](https://azure.microsoft.com/pricing/details/storage/blobs/)
- Az eseményközpont streamelésekor, amikor naplókat és metrikákat továbbít a Azure Event Hubs. Lásd [Azure Event Hubs díjszabást.](https://azure.microsoft.com/pricing/details/event-hubs/)

Előfordulhat, Azure Monitor költségek a következőkhöz kapcsolódnak. Lásd [Azure Monitor díjszabást:](https://azure.microsoft.com/pricing/details/monitor/)

- Naplólekérdezés futtatása.
- Metrika- vagy naplólekérdezés-riasztási szabály létrehozása.
- Értesítés küldése bármely riasztási szabályból.
- Metrikák elérése API-n keresztül.

## <a name="monitoring-data"></a>Adatok monitorozása
Az Azure-erőforrások [](../essentials/data-platform-metrics.md) az [alábbi](../logs/data-platform-logs.md) ábrán látható naplókat és metrikákat hoznak létre. Az egyes Azure-szolgáltatások dokumentációjában találhatja meg a generált adatokat, valamint az ezek által nyújtott további megoldásokat és elemzéseket.

![Áttekintés](media/monitor-azure-resource/logs-metrics.png)



- [Platformmetrikák](../essentials/data-platform-metrics.md) – Rendszeres időközönként automatikusan gyűjtött numerikus értékek, amelyek egy adott időpontban egy erőforrás bizonyos aspektusát írják le. 
- [Erőforrásnaplók](./platform-logs-overview.md) – Betekintést nyerhet az Azure-erőforráson (az adatsíkon) végrehajtott műveletekbe, például egy titkos adatokat kérhet le egy Key Vault vagy kérést kérhet le egy adatbázishoz. Az erőforrásnaplók tartalma és szerkezete az Azure-szolgáltatástól és az erőforrástípustól függ.
- [Tevékenységnapló](./platform-logs-overview.md) – Betekintést nyújt az előfizetésben lévő egyes Azure-erőforrások műveleteibe kívülről (a felügyeleti síkon), például új erőforrás létrehozása vagy virtuális gép indítása. Ez az előfizetésben található erőforrásokra vonatkozó írási műveletekre (PUT, POST, DELETE) vonatkozó információk.


## <a name="configuration-requirements"></a>Konfigurációs követelmények

### <a name="configure-monitoring"></a>Figyelés konfigurálása
Bizonyos monitorozási adatok gyűjtése automatikusan történik, de előfordulhat, hogy a követelményektől függően el kell végeznie néhány konfigurálást. Az egyes monitorozási adatokról az alábbi információkban olvashat részletesen.

- [Platformmetrikák](../essentials/data-platform-metrics.md) – A platformmetrikákat a rendszer automatikusan gyűjti Azure Monitor [metrikákba](../essentials/data-platform-metrics.md) konfiguráció nélkül. Hozzon létre egy diagnosztikai beállítást, amely bejegyzéseket küld Azure Monitor naplókba, vagy továbbítja azokat az Azure-ban kívülre.
- [Erőforrásnaplók](./platform-logs-overview.md) – Az erőforrásnaplókat az Azure-erőforrások automatikusan generálják, de diagnosztikai beállítás nélkül nem gyűjtik.  Hozzon létre egy diagnosztikai beállítást, amely bejegyzéseket küld Azure Monitor naplókba, vagy továbbítja azokat az Azure-ban kívülre.
- [Tevékenységnapló](./platform-logs-overview.md) – A rendszer automatikusan gyűjti a tevékenységnaplót, és nem igényel konfigurációt, és megtekinthető a Azure Portal. Hozzon létre egy diagnosztikai beállítást, amely átmásolja Azure Monitor naplókba, vagy továbbítja őket az Azure-ban kívülre.

### <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
Az adatok naplókba Azure Monitor Log Analytics-munkaterületre van szükség. Egy új munkaterület létrehozásával gyorsan elkezdheti a szolgáltatás monitorozását, de a más szolgáltatásokból adatokat gyűjtő munkaterület használata értékes lehet. A [munkaterületek létrehozásával](../logs/quick-create-workspace.md) kapcsolatos részletekért lásd a Azure Portal Log Analytics-munkaterület létrehozása és a [Azure Monitor-naplók](../logs/design-logs-deployment.md) üzembe helyezésének megtervezése a munkaterület igényeinek leginkább megfelelő kialakításának meghatározásához. Ha egy meglévő munkaterületet használ a szervezetben, akkor a naplóadatokhoz és munkaterülethez való hozzáférés kezelése a következőben ismertetett megfelelő engedélyekre lesz szüksége a [Azure Monitor.](../logs/manage-access.md) 





## <a name="diagnostic-settings"></a>Diagnosztikai beállítások
A diagnosztikai beállítások határozzák meg, hogy egy adott erőforráshoz hová kell küldeni az erőforrásnaplókat és -metrikákat. A lehetséges célhely a következő:

- [Log](./resource-logs.md#send-to-log-analytics-workspace) Analytics-munkaterület, amely lehetővé teszi az adatok elemzését a Azure Monitor által gyűjtött egyéb monitorozási adatokkal hatékony naplólekérdezésekkel, valamint más funkciók, például naplóriasztás és vizualizációk Azure Monitor kihasználása érdekében. 
- [Eseményközpontok az](./resource-logs.md#send-to-azure-event-hubs) adatok külső rendszerekbe, például külső SIEM-rendszerekbe vagy más naplóelemzési megoldásokba való streamelése céljából. 
- [Azure Storage-fiók,](./resource-logs.md#send-to-azure-storage) amely naplózáshoz, statikus elemzéshez vagy biztonsági mentéshez lehet hasznos.

Kövesse a Diagnosztikai beállítás létrehozása szakasz eljárását a [platformnaplók](../essentials/diagnostic-settings.md) és -metrikák gyűjtéséhez az Azure-ban a diagnosztikai beállítások létrehozásához és kezeléséhez a Azure Portal. Tekintse [meg a Diagnosztikai beállítás létrehozása](./resource-manager-diagnostic-settings.md) az Azure-ban Resource Manager sablon használatával szakaszt, amely meghatározza őket egy sablonban, és engedélyezi az erőforrások teljes monitorozását annak létrehozásakor.


## <a name="monitoring-in-the-azure-portal"></a>Monitorozás a Azure Portal
 A legtöbb Azure-erőforrás monitorozási adatait az erőforrás menüjében, a Azure Portal. Ez hozzáférést biztosít egy adott erőforrás adataihoz standard Azure Monitor eszközök használatával. Egyes Azure-szolgáltatások különböző lehetőségeket biztosítanak, ezért további információkért érdemes az erre a szolgáltatásra vonatkozó dokumentációra hivatkozni. A **Azure Monitor** menüben elemezheti az összes figyelt erőforrás adatait. 

### <a name="overview"></a>Áttekintés
Sok szolgáltatás az Áttekintés  oldalon is tartalmazza a monitorozási adatokat, amelyek gyors áttekintést nyújtanak a működésükről. Ez általában a metrikákban tárolt platformmetrikák egy Azure Monitor alapul. Más figyelési lehetőségek általában **a** szolgáltatás menüjének Figyelés szakaszában érhetők el.

![Áttekintő oldal](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Elemzések és megoldások 
Egyes szolgáltatások a szolgáltatás szabványos funkcióin túl is Azure Monitor. [Az](../monitor-reference.md) elemzések az adatplatformra és a standard funkciókra Azure Monitor egyéni monitorozási élményt biztosítanak. [A megoldások](../insights/solutions.md) előre meghatározott, a naplókra Azure Monitor logikát biztosítanak. 

Ha egy szolgáltatáshoz Azure Monitor, akkor az egyes  erőforrások menüjének Figyelés menüjében használhatja. Az összes elemzés és megoldás elérhető a **Azure Monitor** menüből.

![A Azure Portal](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Mérőszámok
Elemezze a metrikákat a [](./metrics-getting-started.md) Azure Portal metrikák explorerrel, amely a legtöbb szolgáltatásHoz elérhető a **Metrikák** menüpontban. Ez az eszköz lehetővé teszi, hogy egyéni metrikákat dolgozzon, vagy több egyesítésével azonosítsa a korrelációkat és trendeket. 

- A [Metrikák explorer](./metrics-getting-started.md) használatának alapjait az Azure Metrikaböngésző első lépések között tekintse meg.
- Az [Azure Metrikaböngésző](../essentials/metrics-charts.md) metrikák explorerének speciális funkcióit, például több metrika használatával, szűrők és felosztás alkalmazásával kapcsolatos cikkeket.

![Metrikák explorer a Azure Portal](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Tevékenységnapló 
A tevékenységnapló bejegyzéseinek megtekintése a Azure Portal az aktuális erőforrásra beállított kezdeti szűrővel. Másolja a tevékenységnaplót egy Log Analytics-munkaterületre, hogy hozzáférjen a naplólekérdezésekben és munkafüzetekben való használathoz. 

- A [Tevékenységnapló megtekintésével](../essentials/activity-log.md#view-the-activity-log) és a bejegyzések különböző módszerekkel való lekérésével kapcsolatos részletekért lásd: Az Azure-tevékenységnapló eseményeinek megtekintése és lekérése.
- A naplózott eseményekért tekintse meg az Azure-szolgáltatás dokumentációját.

![Tevékenységnapló](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók
Azure Monitor Naplók egy hatékony lekérdezési eszközzel egyesíti a naplókat és metrikákat több szolgáltatásból és más adatforrásból elemzés céljából. A fentiekben leírtak szerint hozzon létre egy diagnosztikai beállítást a platformmetrikák, a tevékenységnaplók és az erőforrásnaplók egy Log Analytics-munkaterületre való gyűjtéséhez a Azure Monitor.

[A Log Analytics](../logs/log-analytics-tutorial.md) lehetővé [](../logs/log-query-overview.md)teszi, hogy naplólekérdezésekkel dolgozzon, amely a Azure Monitor egy hatékony funkciója, amely lehetővé teszi a naplóadatok fejlett elemzését egy teljes körű lekérdezési nyelv használatával. Nyissa meg a Log  **Analyticset** egy Azure-erőforrás Monitorozás menüjének Naplók menüjéből, hogy a lekérdezési hatókörként az erőforrást használó naplólekérdezésekkel [dolgozzon.](../logs/scope.md#query-scope) Ez lehetővé teszi, hogy több tábla adatait elemezze csak az adott erőforráshoz. A **Naplók** menüben Azure Monitor az összes erőforrás naplóihoz való hozzáféréshez. 

- A [naplólekérdezések írásának](../logs/get-started-queries.md) nyelvvel Azure Monitor a naplólekérdezések használatának első Azure Monitor oktatóanyagot.
- Az Erőforrásnaplók gyűjtése a Log Analytics-munkaterületen a [Azure Monitor-ban](./resource-logs.md#send-to-log-analytics-workspace) dokumentumban további információt talál az erőforrásnaplók Azure Monitor-naplókban való gyűjtéséről, valamint a lekérdezésben való hozzáférésük részleteiről.
- Az [erőforrásnapló](./resource-logs.md#send-to-log-analytics-workspace) adatainak a Naplókban való strukturálása a Gyűjtemény módban Azure Monitor meg.
- Az egyes Azure-szolgáltatások dokumentációjában további részleteket talál a naplók Azure Monitor táblázatában.

![Log Analytics a Azure Portal](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Figyelés parancssorból
Az erőforrásból gyűjtött monitorozási adatokat elérheti egy parancssorból, vagy egy szkriptbe foglalhatja az Azure PowerShell [azure parancssori felület használatával.](/cli/azure/) [](/powershell/azure/) 

- A [metrikaadatok CLI-ről](/cli/azure/monitor/metrics) való eléréséhez tekintse meg a CLI-metrikák referenciáját.
- A [naplóadatokat a CLI-Azure Monitor](/cli/azure/monitor/log-analytics) naplólekérdezés használatával való eléréséhez lásd: CLI Log Analytics-referencia.
- A [Azure PowerShell metrikák](/powershell/module/azurerm.insights/get-azurermmetric) referencia-útmutatója a metrikák adatainak eléréséhez Azure PowerShell.
- A [Azure PowerShell naplólekérdezés](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) referenciája a naplóadatok Azure Monitor naplólekérdezés használatával való eléréséhez Azure PowerShell.

## <a name="monitoring-from-rest-api"></a>Monitorozás REST API
Foglalja bele az erőforrásból gyűjtött monitorozási adatokat egy egyéni alkalmazásba egy REST API.

- A [metrikák](./rest-api-walkthrough.md) REST API az Azure Monitoring Azure Monitor REST API.
- Az [Azure Log Analytics REST API](https://dev.loganalytics.io/) naplóadatok naplólekérdezés használatával való eléréséhez Azure Monitor Azure Log Analytics Azure PowerShell.

## <a name="alerts"></a>Riasztások
[A riasztások](../alerts/alerts-overview.md) proaktívan értesítik Önt, és ha fontos feltételeket talál a monitorozási adatokban, esetleg lépéseket is kell találnia. Létrehoz egy riasztási szabályt, amely meghatározza a riasztás célját, a riasztás létrehozására vonatkozó feltételeket, valamint a válaszként szükséges műveleteket.

A különböző riasztási szabályokhoz különböző típusú figyelési adatok használhatók.

- [Tevékenységnapló-riasztás](../alerts/alerts-activity-log.md) – Riasztást hozhat létre, ha egy adott feltételeknek megfelelő bejegyzés jön létre a tevékenységnaplóban. Ez lehetővé teszi, hogy értesítést kap, például egy adott típusú erőforrás létrehozásakor, vagy ha egy konfiguráció módosítása meghiúsul.
- [Metrikák riasztása](../alerts/alerts-metric.md) – Riasztást hozhat létre, ha egy metrikaérték meghalad egy adott küszöbértéket. A metrikák riasztásai rugalmasabbak, mint a többi riasztás, és a probléma kijavítása után automatikusan megoldhatók.
- [Naplólekérdezés-riasztás](../alerts/alerts-log.md) – Rendszeres időközönként naplólekérdezéseket futtathat, és riasztást hozhat létre egy adott feltétel esetén. Ez lehetővé teszi összetett elemzések elvégzését több adatkészleten és a adatkészleten.

Használja **az** erőforrás menüjének Riasztások menüpontját a riasztások megtekintéséhez és az erőforrás riasztási szabályainak kezeléséhez. Célként csak a Tevékenységnapló-riasztások és a Metrikákra vonatkozó riasztások használják az egyes Azure-erőforrásokat. A naplólekérdezés-riasztások a Log Analytics-munkaterületet használják célként, és olyan lekérdezésen alapulnak, amely hozzáfér az adott munkaterületen tárolt naplókhoz. A Azure Monitor menüben megtekintheti és kezelheti az összes erőforrásra vonatkozó riasztásokat, valamint a naplólekérdezés riasztási szabályait.

- A riasztási szabályok létrehozásával kapcsolatos részletekért tekintse meg a fenti riasztások különböző fajtáit tartalmazó cikkeket.
- A [riasztásra adott](../alerts/action-groups.md) válaszok kezelését lehetővé tő műveletcsoportok létrehozásával és kezeléséhez Azure Portal műveletcsoportok létrehozásával kapcsolatos részletekért lásd: Create and manage action groups in the Azure Portal( Műveletcsoportok létrehozása és kezelése).



## <a name="next-steps"></a>Következő lépések

* A különböző Azure-szolgáltatások erőforrásnaplóiról a Supported [services, schemas, and categories for Azure Resource Logs (Az](./resource-logs-schema.md) Azure-erőforrásnaplók támogatott szolgáltatásai, sémái és kategóriái) talál további információt.
