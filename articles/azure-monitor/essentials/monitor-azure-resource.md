---
title: Azure-erőforrások figyelése Azure Monitorokkal | Microsoft Docs
description: Ismerteti, hogyan gyűjthet és elemezheti a monitorozási adatokat az Azure-beli erőforrásokból Azure Monitor használatával.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: af2803a945821a23ff59f196967d792a6593009f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033384"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Azure-erőforrások monitorozása az Azure Monitor segítségével
Ha kritikus fontosságú alkalmazásokat és üzleti folyamatokat kíván használni az Azure-erőforrásokon, figyelnie kell ezeket az erőforrásokat a rendelkezésre állással, a teljesítménnyel és a művelettel kapcsolatban. Ez a cikk ismerteti az Azure-erőforrások által létrehozott figyelési információkat, valamint azt, hogy miként használhatók a Azure Monitor funkciói az adatelemzéshez és a riasztáshoz.

> [!IMPORTANT]
> Ez a cikk az Azure-ban Azure Monitort használó összes szolgáltatásra vonatkozik. A számítási erőforrások, beleértve a virtuális gépeket és a App Service is, az itt ismertetett figyelési adatokat, de a vendég operációs rendszert is létrehozhatja, amely naplók és metrikák létrehozását is eredményezheti. Az adatok gyűjtésével és elemzésével kapcsolatos további információkért tekintse meg a szolgáltatások figyelési dokumentációját.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
A Azure Monitor az Azure teljes körű figyelési szolgáltatása, amely teljes körű funkciókat biztosít az Azure-erőforrások figyeléséhez más Felhőbeli és helyszíni erőforrásokon kívül. A [Azure monitor adatplatform](../data-platform.md) a [naplókba](../logs/data-platform-logs.md) és a [metrikába](../essentials/data-platform-metrics.md) gyűjti az adatokat, ahol a monitorozási eszközök teljes készletével együtt is elemezhetők. Tekintse meg a [Azure monitor által figyelt](../monitor-reference.md)alkalmazások és szolgáltatások teljes listáját Azure monitor alapján.

Amint létrehoz egy Azure-erőforrást, Azure Monitor engedélyezve van, és megkezdi a metrikák és a tevékenységek naplóinak gyűjtését, amelyeket [a Azure Portal tekinthet meg és elemez](#monitoring-in-the-azure-portal). Néhány konfigurációval további figyelési adatokat gyűjthet, és további funkciókat is engedélyezhet. A konfigurációs követelmények részleteiért lásd az alábbi [figyelési adatokat](#monitoring-data) .


## <a name="costs-associated-with-monitoring"></a>A figyeléshez kapcsolódó költségek
Az alapértelmezés szerint összegyűjtött figyelési adatok elemzéséhez nincs díj. Ezek a következők lehetnek:

- A platform metrikáinak összegyűjtése és a metrikák Explorerrel való elemzése.
- A tevékenység naplójának összegyűjtése és elemzése a Azure Portalban.
- Műveletnapló riasztási szabályának létrehozása.

A naplók és a metrikák összegyűjtésére és exportálására nincs Azure Monitor költség, de a célhoz kapcsolódó költségek is előfordulhatnak:

- Az adatfeldolgozással és-megőrzéssel kapcsolatos költségek Log Analytics munkaterületen a naplók és a metrikák összegyűjtésekor. Tekintse [meg a Log Analytics Azure monitor díjszabását](https://azure.microsoft.com/pricing/details/monitor/).
- Az adattárolással kapcsolatos költségek naplók és mérőszámok Azure Storage-fiókba való összegyűjtéséhez. Lásd: [Az Azure Storage díjszabása a blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/)-hoz.
- A naplók és a metrikák Azure-Event Hubsra való továbbításakor az Event hub streaming költségeihez kapcsolódó költségek. Lásd: az [Azure Event Hubs díjszabása](https://azure.microsoft.com/pricing/details/event-hubs/).

A következőhöz kapcsolódóan Azure Monitor költségek is előfordulhatnak. Lásd a [Azure monitor díjszabását](https://azure.microsoft.com/pricing/details/monitor/):

- Egy napló lekérdezésének futtatása.
- Metrikai vagy napló lekérdezési riasztási szabály létrehozása.
- Értesítés küldése bármely riasztási szabályból.
- Metrikák elérése API-n keresztül.

## <a name="monitoring-data"></a>Adatok monitorozása
Az Azure-beli erőforrások a következő ábrán látható [naplókat](../logs/data-platform-logs.md) és [mérőszámokat](../essentials/data-platform-metrics.md) állítanak elő. Tekintse át az egyes Azure-szolgáltatások dokumentációját az általuk létrehozott konkrét információkra, valamint az általuk biztosított további megoldásokra vagy elemzésekre vonatkozóan.

![Áttekintés](media/monitor-azure-resource/logs-metrics.png)



- [Platform metrikái](../essentials/data-platform-metrics.md) – a rendszeres időközönként automatikusan összegyűjtött numerikus értékek, amelyek egy adott időpontban egy erőforrás bizonyos aspektusait írják le. 
- [Erőforrás-naplók](./platform-logs-overview.md) – betekintést nyújt az Azure-erőforrásokon (az adatsíkon) végrehajtott műveletekre, például a titkos kód beszerzése egy Key Vault vagy egy adatbázisra irányuló kérés. Az erőforrás-naplók tartalma és szerkezete az Azure szolgáltatás és az erőforrás típusa szerint változik.
- [Műveletnapló](./platform-logs-overview.md) – betekintést nyújt a műveletekre az előfizetésben lévő összes Azure-erőforráson kívülről (a felügyeleti síkon), például egy új erőforrás létrehozásához vagy egy virtuális gép elindításához. Ez az előfizetésben található erőforrásokon végrehajtott írási műveletek (PUT, POST, DELETE) adatait és az azokra vonatkozó információkat ismerteti.


## <a name="configuration-requirements"></a>Konfigurációs követelmények

### <a name="configure-monitoring"></a>Figyelés konfigurálása
Egyes figyelési adatokat a rendszer automatikusan gyűjt, de előfordulhat, hogy a követelményektől függően valamilyen konfigurációt kell végrehajtania. Az alábbi információk az egyes megfigyelési adatokra vonatkozó információkra vonatkoznak.

- [Platform metrikái](../essentials/data-platform-metrics.md) – a platform metrikái automatikusan bekerülnek [Azure monitor-metrikába](../essentials/data-platform-metrics.md) , és nincs szükség konfigurációra. Diagnosztikai beállítás létrehozásával bejegyzéseket küldhet Azure Monitor naplókba, vagy továbbíthatja őket az Azure-on kívül.
- [Erőforrás-naplók](./platform-logs-overview.md) – az erőforrás-naplókat az Azure-erőforrások automatikusan generálják, de diagnosztikai beállítások nélkül nem gyűjtik őket.  Diagnosztikai beállítás létrehozásával bejegyzéseket küldhet Azure Monitor naplókba, vagy továbbíthatja őket az Azure-on kívül.
- [Műveletnapló](./platform-logs-overview.md) – a tevékenység naplóját a rendszer automatikusan gyűjti, és a konfiguráció nem szükséges, és a Azure Portalban is megtekinthető. Hozzon létre egy diagnosztikai beállítást Azure Monitor naplókba való másoláshoz vagy az Azure-on kívüli továbbításhoz.

### <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
Az adatok Azure Monitor naplókba való gyűjtéséhez egy Log Analytics munkaterület szükséges. Az új munkaterület létrehozásával gyorsan megkezdheti a szolgáltatás figyelését, de előfordulhat, hogy olyan munkaterületet használ, amely más szolgáltatásokból származó adatok gyűjtésére szolgál. A munkaterületek létrehozásával és a [Azure monitor naplók központi telepítésének megtervezésével](../logs/design-logs-deployment.md) kapcsolatos részletekért lásd: [log Analytics munkaterület létrehozása a Azure Portalban](../logs/quick-create-workspace.md) . Ha a szervezet egy meglévő munkaterületét használja, akkor a megfelelő engedélyeket kell megkövetelni a következő témakörben ismertetett módon: a [hozzáférés kezelése a naplózási adataihoz és munkaterületekhez Azure monitor](../logs/manage-access.md). 





## <a name="diagnostic-settings"></a>Diagnosztikai beállítások
A diagnosztikai beállítások határozzák meg, hogy az adott erőforráshoz milyen erőforrás-naplókat és mérőszámokat kell elküldeni. A lehetséges célpontok:

- [Log Analytics munkaterület](./resource-logs.md#send-to-log-analytics-workspace) , amely lehetővé teszi az adatok elemzését a Azure monitor által gyűjtött más megfigyelési adatokkal hatékony naplók használatával, valamint más Azure monitor funkciók, például a naplózási riasztások és a vizualizációk kihasználása. 
- Az [Event hubok](./resource-logs.md#send-to-azure-event-hubs) külső rendszerekre, például harmadik féltől származó Siem-re és más log Analytics-megoldásokra továbbítják az adatátvitelt. 
- [Azure Storage-fiók](./resource-logs.md#send-to-azure-storage) , amely naplózásra, statikus elemzésre vagy biztonsági mentésre használható.

Kövesse a diagnosztikai beállítások [létrehozása a platform-naplók és-metrikák összegyűjtéséhez az Azure-ban](../essentials/diagnostic-settings.md) című eljárást a Azure Portalon keresztüli diagnosztikai beállítások létrehozásához és kezeléséhez. Lásd: [diagnosztikai beállítás létrehozása az Azure-ban Resource Manager-sablonnal egy](./resource-manager-diagnostic-settings.md) sablonban való definiáláshoz, valamint az erőforrások teljes figyelésének engedélyezése a létrehozáskor.


## <a name="monitoring-in-the-azure-portal"></a>Figyelés a Azure Portal
 A legtöbb Azure-erőforrás figyelési adatait a Azure Portal erőforrás menüjéből érheti el. Így egyetlen erőforrás adataihoz férhet hozzá a standard Azure Monitor eszközök használatával. Bizonyos Azure-szolgáltatások különböző lehetőségeket biztosítanak, ezért további információkat a szolgáltatás dokumentációjában talál. Az összes figyelt erőforrásból származó adatok elemzéséhez használja a **Azure monitor** menüt. 

### <a name="overview"></a>Áttekintés
Számos szolgáltatás a működésük gyors áttekintése érdekében az **áttekintő** oldalon található megfigyelési információkat is tartalmazza. Ez általában a Azure Monitor Mérőszámokban tárolt platform-mérőszámok egy részhalmazán alapul. Az egyéb megfigyelési lehetőségek általában a szolgáltatás menüjének **figyelés** szakaszában lesznek elérhetők.

![Áttekintő oldal](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat 
Egyes szolgáltatások a Azure Monitor normál szolgáltatásain kívül is biztosítanak eszközöket. Az elemzések a Azure Monitor adatplatformra és a standard funkciókra épülő, testreszabott figyelési [felületet biztosítanak](../monitor-reference.md) . A [megoldások](../insights/solutions.md) Azure monitor naplókra épülő, előre meghatározott figyelési logikát biztosítanak. 

Ha egy szolgáltatás Azure Monitor betekintéssel rendelkezik, akkor az egyes erőforrások menüjében elérheti azt a **figyelés** lehetőséggel. A **Azure monitor** menüből elérheti az összes bepillantást és megoldást.

![Betekintés a Azure Portal](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Mérőszámok
A metrikák elemzése a Azure Portal a metrikák [Explorer](./metrics-getting-started.md) használatával, amely a legtöbb szolgáltatás **mérőszámok** menüjéből érhető el. Ez az eszköz lehetővé teszi, hogy egyéni metrikákkal működjön együtt, és kombinálja a többit a korrelációk és trendek azonosítására. 

- Tekintse meg az [Azure Metrikaböngésző használatának első lépései](./metrics-getting-started.md) a metrikák Explorerrel.
- Tekintse meg az [Azure Metrikaböngésző speciális funkcióit](../essentials/metrics-charts.md) a metrikák Explorer speciális szolgáltatásaihoz, például több metrika használatával, valamint szűrők és felosztás alkalmazásával.

![Metrikák Intéző a Azure Portal](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Tevékenységnapló 
Megtekintheti a tevékenység naplójában lévő bejegyzéseket a Azure Portal az aktuális erőforráshoz beállított kezdeti szűrővel. Másolja a tevékenység naplóját egy Log Analytics munkaterületre, hogy hozzáférhessen a napló lekérdezésekben és munkafüzetekben való használathoz. 

- A tevékenységek naplójának megtekintésével és a bejegyzések beolvasásával kapcsolatos részletekért tekintse meg az [Azure-Tevékenységnaplók eseményeinek megtekintése és beolvasása](../essentials/activity-log.md#view-the-activity-log) című témakört.
- Az Azure-szolgáltatás dokumentációjában találhatja meg a naplózott eseményeket.

![Tevékenységnapló](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók
Azure Monitor a naplók több szolgáltatásból és más adatforrásokból származó naplókat és mérőszámokat egyesítenek egy hatékony lekérdezési eszközzel történő elemzéshez. A fentiekben leírtak szerint hozzon létre egy diagnosztikai beállítást a platform metrikáinak, a tevékenységek naplójának és az erőforrás-naplóknak a Azure Monitor Log Analytics munkaterületre való gyűjtéséhez.

[Log Analytics](../logs/log-analytics-tutorial.md) lehetővé teszi a [naplózási lekérdezések](../logs/log-query-overview.md)használatát, amely a Azure monitor hatékony funkciója, amely lehetővé teszi a naplózási adatok speciális elemzését teljes körűen Kiemelt lekérdezési nyelv használatával. Nyissa meg Log Analytics az Azure-erőforrások **figyelés** menüjében lévő **naplókból** , hogy a [lekérdezési hatókörként](../logs/scope.md#query-scope)használja az erőforrást használó napló lekérdezéseit. Ez lehetővé teszi, hogy csak az adott erőforráshoz több táblán elemezze az adatelemzést. A Azure Monitor menüben található **naplók** használatával férhet hozzá az összes erőforráshoz tartozó naplókhoz. 

- A naplók írásához használt lekérdezési nyelv használatával kapcsolatos oktatóanyagért lásd: a [Azure monitor naplózási lekérdezésének első lépései](../logs/get-started-queries.md) .
- Tekintse meg az [Azure-erőforrás-naplók gyűjtése a log Analytics munkaterületen a Azure monitorban](./resource-logs.md#send-to-log-analytics-workspace) című témakört, amely arról nyújt tájékoztatást, hogyan történik az erőforrás-naplók gyűjtése a Azure monitor-naplókban, és hogy miként lehet őket
- Tekintse meg az [adatgyűjtési módot](./resource-logs.md#send-to-log-analytics-workspace) , amely azt ismerteti, hogyan épülnek fel az erőforrás-naplózási információk Azure monitor naplókba.
- Tekintse meg az egyes Azure-szolgáltatások dokumentációját Azure Monitor naplókban található táblázat részleteivel.

![Log Analytics a Azure Portal](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Figyelés parancssorból
Az erőforrásból gyűjtött figyelési adatokat parancssorból vagy [Azure PowerShell](/powershell/azure/) vagy az [Azure parancssori felületét](/cli/azure/)használó parancsfájlba is elérheti. 

- Lásd: [CLI-metrika referenciája](/cli/azure/monitor/metrics) metrikai adatok eléréséhez a parancssori felületről.
- Lásd: [CLI-log Analytics referenciája](/cli/azure/ext/log-analytics/monitor/log-analytics) Azure monitor naplók adatainak eléréséhez a CLI-ből származó log lekérdezés használatával.
- Lásd: [Azure PowerShell metrikák referenciája](/powershell/module/azurerm.insights/get-azurermmetric) a metrikai adatok Azure PowerShellból való eléréséhez.
- A következő témakörben talál további információt: [Azure PowerShell naplózási lekérdezési referenciája](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) Azure monitor naplók adatainak eléréséhez Azure PowerShellből származó log lekérdezés használatával.

## <a name="monitoring-from-rest-api"></a>Figyelés REST APIról
Az erőforrásból gyűjtött figyelési adatok belefoglalása egy egyéni alkalmazásba egy REST API használatával.

- Az Azure Monitor REST API metrikáinak elérésével kapcsolatos részletekért tekintse meg az [Azure Monitoring REST API útmutatóját](./rest-api-walkthrough.md) .
- Az [Azure Log Analytics REST API](https://dev.loganalytics.io/) további információ a Azure monitor naplók adatainak a Azure PowerShellból történő naplózási lekérdezéssel való eléréséről.

## <a name="alerts"></a>Riasztások
A [riasztások](../alerts/alerts-overview.md) proaktívan értesítik Önt, és lehetséges, hogy a figyelési adataiban fontos feltételek találhatók. Létre kell hoznia egy riasztási szabályt, amely meghatározza a riasztás célját, a riasztások létrehozásának feltételeit, valamint azokat a műveleteket, amelyeket válaszként kell végrehajtani.

Különböző típusú figyelési adattípusokat használ a riasztási szabályok különböző típusaihoz.

- [Műveletnapló riasztása](../alerts/alerts-activity-log.md) – riasztás létrehozása, ha egy bejegyzés jön létre a tevékenység naplójában, amely megfelel a megadott feltételeknek. Ez lehetővé teszi, hogy értesítést kapjon például egy adott típusú erőforrás létrehozásakor, vagy ha egy konfigurációs módosítás meghiúsul.
- [Metrika riasztása](../alerts/alerts-metric.md) – riasztás létrehozása, ha egy metrika értéke meghaladja az adott küszöbértéket. A metrikai riasztások jobban reagálnak, mint a többi riasztás, és a probléma javítása után automatikusan feloldhatók.
- [Naplózási lekérdezés riasztása](../alerts/alerts-log.md) – rendszeres időközönként futtasson egy napló lekérdezést, és hozzon létre egy riasztást, ha talál egy adott feltételt. Ez lehetővé teszi, hogy összetett elemzéseket végezzen több adatkészletben és.

**Riasztások használata egy** erőforrás menüjében a riasztások megtekintéséhez és az adott erőforrás riasztási szabályainak kezeléséhez. Csak a műveletnapló riasztásai és a metrikus riasztások használják az egyes Azure-erőforrásokat célként. A naplózási lekérdezési riasztások a Log Analytics munkaterületet célként használják, és egy olyan lekérdezésen alapulnak, amely hozzáfér az adott munkaterületen tárolt naplókhoz. A Azure Monitor menüben megtekintheti és kezelheti az összes erőforrásra vonatkozó riasztásokat, valamint a napló lekérdezési riasztási szabályait.

- A riasztási szabályok létrehozásával kapcsolatos részletekért tekintse meg a fenti különböző típusú riasztások cikkeit.
- A műveleti csoportok létrehozásáról [és kezeléséről a Azure Portalban](../alerts/action-groups.md) talál további információt a riasztásokra adott válaszok kezeléséhez.



## <a name="next-steps"></a>Következő lépések

* A különböző Azure-szolgáltatásokhoz tartozó erőforrás-naplók részleteiért lásd: [támogatott szolgáltatások, sémák és kategóriák az Azure-erőforrás-naplókhoz](./resource-logs-schema.md) .