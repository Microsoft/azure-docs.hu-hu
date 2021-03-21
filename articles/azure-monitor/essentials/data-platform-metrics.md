---
title: Metrikák a Azure Monitorban | Microsoft Docs
description: Ismerteti a Azure Monitor metrikáit, amelyek a közel valós idejű forgatókönyvek támogatására alkalmas, könnyű figyelési adatok.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 8ecfd74a4d486a83add490501c2f7af4a4003b85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700974"
---
# <a name="azure-monitor-metrics-overview"></a>Azure Monitor mérőszámok áttekintése
Azure Monitor mérőszámok olyan Azure Monitor szolgáltatása, amely a [figyelt erőforrások](../monitor-reference.md) numerikus adatait egy idősorozat-adatbázisba gyűjti. A metrikák olyan numerikus értékek, amelyeket rendszeres időközönként gyűjtenek, és egy adott rendszer bizonyos aspektusait egy adott időpontban írják le. A Azure Monitor metrikái nagyon egyszerűek, és a közel valós idejű forgatókönyvek támogatása révén különösen hasznosak lehetnek a riasztások és a problémák gyors észlelése érdekében. A mérőszámok Explorerrel interaktív módon elemezheti őket, és proaktívan értesítheti a riasztást, ha egy érték átlép egy küszöbértéket, vagy egy munkafüzetben vagy irányítópulton jeleníti meg őket.


> [!NOTE]
> Azure Monitor mérőszámok az Azure Monitor támogató adatplatform fele. A másik [Azure monitor naplók](../logs/data-platform-logs.md) , amelyek a napló-és teljesítményadatokat gyűjtik és rendszerezik, és lehetővé teszik, hogy a rendszer részletes lekérdezési nyelvvel elemezze azokat. A metrikák sokkal könnyebbek, mint Azure Monitor naplókban tárolt adatok, és a közel valós idejű forgatókönyvek támogatása lehetővé teszi számukra, hogy különösen hasznosak legyenek a riasztások és a problémák gyors észlelése érdekében. A metrikák azonban csak egy adott struktúrában tárolhatják a numerikus adatokat, míg a naplók számos különböző adattípust tárolhatnak a saját struktúrájuk használatával. A naplók adatain összetett elemzéseket is végrehajthat, ha olyan naplózási lekérdezéseket használ, amelyek nem használhatók a metrikák adatok elemzéséhez.


## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Mit tehet a Azure Monitor metrikákkal?
A következő táblázat felsorolja a Azure Monitor metrikáinak különböző módszereit.

|  |  |
|:---|:---|
| **Elemzés** | A [metrikák](metrics-charts.md) segítségével elemezheti a diagramon összegyűjtött mérőszámokat, és összehasonlíthatja a különböző erőforrások metrikáit. |
| **Riasztás** | Olyan [metrikai riasztási szabályt](../alerts/alerts-metric.md) konfigurálhat, amely értesítést küld, vagy [automatizált műveletet hajt végre](../alerts/action-groups.md) , ha a metrika értéke átlépi a küszöbértéket. |
| **Vizualizáció** | Diagram rögzítése a metrikák Intézőből egy Azure- [irányítópultra](../app/tutorial-app-dashboards.md).<br>Hozzon létre egy [munkafüzetet](../visualize/workbooks-overview.md) , amely kombinálható több adatkészlettel egy interaktív jelentésben. Egy lekérdezés eredményeinek exportálása a [Grafana](../visualize/grafana-plugin.md) , hogy kihasználja az irányítópultot, és kombinálja más adatforrásokkal. |
| **Automatizálás** |  Az [autoscale](../autoscale/autoscale-overview.md) használatával növelheti vagy csökkentheti az erőforrásokat egy küszöbértéket áthaladó metrika értéke alapján. |
| **Beolvasni** | Metrikai értékek elérése parancssorból PowerShell- [parancsmagok](/powershell/module/az.applicationinsights) használatával<br>Az egyéni alkalmazás metrikai értékeinek elérése [REST API](./rest-api-walkthrough.md)használatával.<br>Metrikai értékek elérése parancssorból a  [CLI](/cli/azure/monitor/metrics)használatával. |
| **Exportálás** | [Átirányítja a metrikákat a naplókba](./resource-logs.md#send-to-azure-storage) , hogy Azure monitor mérőszámokban lévő adatokat elemezze Azure monitor-naplókba, és hogy a metrikus értékeket 93 napnál hosszabb ideig tárolja.<br>Stream-metrikák egy [Event hub](./stream-monitoring-data-event-hubs.md) -hoz, hogy átirányítsa őket a külső rendszerekre. |
| **Archívum** | Az erőforrás teljesítmény-vagy állapotadatok [archiválása](./platform-logs-overview.md) megfelelőségi, naplózási vagy offline jelentéskészítési célokra. |

![A metrikák áttekintése](media/data-platform-metrics/metrics-overview.png)


## <a name="data-collection"></a>Adatgyűjtés
Az Azure Monitor által összegyűjtött mérőszámok három alapvető forrása. A metrikák a Azure Monitor metrikus adatbázisban való gyűjtése után a forrástól függetlenül is kiértékelhető.

**Azure-erőforrások**. A platform metrikáit az Azure-erőforrások hozzák létre, és megtekinthetik az állapotukat és a teljesítményt. Minden típusú erőforrás [külön mérőszámok készletét hozza létre, és](./metrics-supported.md) nincs szükség konfigurációra. A platform metrikáit egyperces gyakorisággal gyűjtjük az Azure-erőforrásokból, kivéve, ha a mérőszám definíciójában másképp van megadva. 

**Alkalmazások**. A metrikákat Application Insights hozza létre a figyelt alkalmazásokhoz, és segít a teljesítménnyel kapcsolatos problémák észlelésében és a trendek nyomon követésében az alkalmazás használatának módjában. Ez olyan értékeket foglal magában, mint a _kiszolgáló válaszideje_ és a _böngésző kivételei_.

**Virtuálisgép-ügynökök**. A metrikák egy virtuális gép vendég operációs rendszeréről lesznek gyűjtve. Engedélyezze a vendég operációs rendszer metrikáit a Windows-alapú virtuális gépekhez [Windows diagnosztikai bővítménnyel (wad)](../agents/diagnostics-extension-overview.md) és a [InfluxData](https://www.influxdata.com/time-series-platform/telegraf/)-val rendelkező Linux rendszerű virtuális gépekhez.

**Egyéni metrikák**. A metrikákat az automatikusan elérhető szabványos metrikák mellett is meghatározhatja. Egyéni metrikákat adhat meg az [alkalmazásban](../app/api-custom-events-metrics.md) , amelyet Application Insights figyel, vagy egyéni metrikákat hozhat létre egy Azure-szolgáltatáshoz az [Egyéni metrikák API](./metrics-store-custom-rest-api.md)használatával.

- Tekintse meg a [Azure monitor által figyelt adatokat?](../monitor-reference.md) az adatforrások teljes listáját, amelyek adatokat küldhetnek Azure monitor metrikáknak.

## <a name="metrics-explorer"></a>Metrikaböngésző
A [Metrikaböngésző](metrics-charts.md) használatával interaktív módon elemezheti a metrikus adatbázisban lévő adatokat, és az idő múlásával több mérőszám értékét ábrázolhatja. A diagramokat rögzítheti egy irányítópulton, és megtekintheti azokat más vizualizációkkal. A metrikákat az [Azure monitoring REST API](./rest-api-walkthrough.md)használatával is lekérheti.

![Metrikaböngésző](media/data-platform-metrics/metrics-explorer.png)

- A metrikák Explorer használatának megkezdéséhez tekintse meg [a Azure monitor mérőszámok Explorer](./metrics-getting-started.md) használatába című témakört.

## <a name="data-structure"></a>Adatszerkezet
Azure Monitor metrikák által gyűjtött adatokat egy idősorozat-adatbázis tárolja, amely az időbélyegzővel ellátott adatok elemzésére van optimalizálva. A metrikai értékek minden készlete egy idősorozat a következő tulajdonságokkal:

* Az érték gyűjtésének időpontja
* Az az erőforrás, amelyhez az érték társítva van
* Egy olyan névtér, amely a metrika kategóriához hasonlóan működik
* Metrika neve
* Maga az érték
* Egyes mérőszámok több dimenzióval rendelkezhetnek, a [többdimenziós metrikák](#multi-dimensional-metrics)részben leírtak szerint. Az egyéni metrikák legfeljebb 10 dimenzióval rendelkezhetnek.

## <a name="multi-dimensional-metrics"></a>Többdimenziós metrikák
A metrikus adatok egyik kihívása, hogy gyakran korlátozott információkkal szolgálnak az összegyűjtött értékek kontextusának biztosításához. Azure Monitor ez a kihívás többdimenziós metrikákkal foglalkozik. A mérőszámok méretei olyan név-érték párok, amelyek további adatokat hordoznak a metrika értékének leírásához. Egy metrikus _rendelkezésre álló_ lemezterület például rendelkezhet a " _C:_, _D:_" értékkel rendelkező _meghajtóval_ , amely lehetővé tenné a rendelkezésre álló lemezterület megtekintését az összes meghajtón vagy az egyes meghajtókon.

Az alábbi példa egy _hálózati átviteli sebességű_ feltételezett metrikai adatkészleteket mutat be. Az első adatkészlet nem rendelkezik dimenziókkal. A második adatkészlet a két dimenziót, az _IP-címet_ és az _irányt_ tartalmazó értékeket jeleníti meg:

### <a name="network-throughput"></a>Hálózati átviteli sebesség

| Időbélyeg     | Metrika értéke |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1 331,8 kbps |
| 8/9/2017 8:15 | 1 141,4 kbps |
| 8/9/2017 8:16 | 1 110,2 kbps |

Ez a nem dimenziós metrika csak olyan alapvető kérdésekre tud válaszolni, mint például a "mi volt a hálózati átviteli sebesség egy adott időpontban?"

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>Hálózati átviteli sebesség + két dimenzió ("IP" és "irány")

| Időbélyeg     | "IP" dimenzió   | Dimenzió "iránya" | Metrika értéke|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direction = "Küldés"    | 646,5 kbps |
| 8/9/2017 8:14 | IP = "192.168.5.2" | Direction = "fogadás" | 420,1 kbps |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direction = "Küldés"    | 150,0 kbps |
| 8/9/2017 8:14 | IP = "10.24.2.15"  | Direction = "fogadás" | 115,2 kbps |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direction = "Küldés"    | 515,2 kbps |
| 8/9/2017 8:15 | IP = "192.168.5.2" | Direction = "fogadás" | 371,1 kbps |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direction = "Küldés"    | 155,0 kbps |
| 8/9/2017 8:15 | IP = "10.24.2.15"  | Direction = "fogadás" | 100,1 kbps |

Ez a mérőszám a "mi volt az egyes IP-címek hálózati átviteli sebessége?" és a "mennyi adat lett elküldve, és nem kapott adatokat A többdimenziós mérőszámok további analitikai és diagnosztikai értékeket is magukban foglalhatnak a nem dimenziós mérőszámokhoz képest.

## <a name="retention-of-metrics"></a>Mérőszámok megőrzése
Az Azure-ban a legtöbb erőforrás esetében a metrikák 93 napig tárolódnak. Bizonyos kivételek:

**Vendég operációs rendszer metrikái**
-   **Klasszikus vendég operációs rendszer metrikái**. Ezek a [Windows diagnosztikai bővítmény (wad)](../agents/diagnostics-extension-overview.md) vagy a [Linux diagnosztikai bővítmény (Lad)](../../virtual-machines/extensions/diagnostics-linux.md) által gyűjtött teljesítményszámlálók, és egy Azure Storage-fiókhoz irányíthatók. A metrikák megőrzése 14 nap.
-   **Azure monitor metrikáknak eljuttatott vendég operációs rendszer metrikái**. Ezek a [Windows diagnosztikai bővítmény (wad)](../agents/diagnostics-extension-overview.md) által gyűjtött teljesítményszámlálók, amelyeket a rendszer elküld a Azure monitor adatfogadónak, vagy a [InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) - [alapú](../agents/diagnostics-extension-overview.md#data-destinations), a Linux rendszerű gépeken található. A metrikák megőrzése 93 nap.
-   **Log Analytics ügynök által gyűjtött vendég operációsrendszer-metrikák**. Ezek a Log Analytics ügynök által gyűjtött és Log Analytics munkaterületre továbbított teljesítményszámlálók. A metrikák megőrzése 31 nap, és legfeljebb 2 évig bővíthető.

**Application Insights log-alapú metrikák**. 
- A [log-alapú metrikák](../app/pre-aggregated-metrics-log-metrics.md) a jelenet mögött vannak lefordítva. Ezek megőrzése megegyezik a mögöttes naplókban lévő események megőrzésével. Application Insights erőforrások esetében a rendszer a naplókat 90 napig tárolja.


> [!NOTE]
> A hosszú távú trendek [érdekében platform-metrikákat küldhet Azure monitor erőforrásoknak log Analytics munkaterületre](./resource-logs.md#send-to-azure-storage) .





## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor adatplatformról](../data-platform.md).
- Tudnivalók a [Azure monitor naplózási adatainak](../logs/data-platform-logs.md)használatáról.
- Ismerje meg az Azure különböző erőforrásaihoz [elérhető figyelési információkat](../agents/data-sources.md) .