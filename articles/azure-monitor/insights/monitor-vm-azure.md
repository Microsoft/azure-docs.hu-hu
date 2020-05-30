---
title: Azure-beli virtuális gépek figyelése Azure Monitor
description: Ismerteti, hogyan gyűjthet és elemezheti az Azure-beli virtuális gépek monitorozási adatait Azure Monitor használatával.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: ebb25d49250b71ab8d948833ac982ef244225539
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2020
ms.locfileid: "84216448"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Azure-beli virtuális gépek figyelése Azure Monitor
Ez a cikk azt ismerteti, hogyan használható a Azure Monitor az Azure-beli virtuális gépek monitorozási adatainak gyűjtésére és elemzésére az állapotuk fenntartása érdekében. A virtuális gépeket a rendelkezésre állás és a teljesítmény figyelésére használhatja Azure Monitor mint bármely [más Azure-erőforrást](monitor-azure-resource.md), de ezek más erőforrásokkal is egyediek, mivel a vendég operációs és a rendszer, valamint a rajta futó munkaterhelések figyelésére is szükség van. 

> [!NOTE]
> Ez a cikk átfogó áttekintést nyújt a virtuális gépek Azure Monitor-ban való figyelésével kapcsolatos fogalmakról és lehetőségekről. A virtuális gépek gyors monitorozása anélkül, hogy az alapul szolgáló fogalmakra koncentráljon, tekintse meg a rövid útmutató [: Azure-beli virtuális gép figyelése Azure monitor](../learn/quick-monitor-azure-vm.md)használatával című témakört.


## <a name="differences-from-other-azure-resources"></a>Más Azure-erőforrásoktól származó különbségek
Az [Azure-erőforrások Azure monitor való figyelése](monitor-azure-resource.md) az Azure-erőforrások által generált figyelési információkat ismerteti, valamint azt, hogy miként használhatók a Azure monitor funkciói az adatelemzéshez és a riasztáshoz. Az Azure-beli virtuális gépekről az alábbi eltérésekkel gyűjthet és helyezhet el adatokat:

-  A [platform metrikáit](../platform/data-platform-metrics.md) a rendszer automatikusan gyűjti a virtuális gépekhez, de csak a [virtuális gép gazdagépe](#monitoring-data)számára. Ahhoz, hogy a vendég operációs rendszer teljesítményadatokat gyűjtsön, egy ügynökre van szüksége. 
- A virtuális gépek nem hoznak olyan [erőforrás-naplókat](../platform/platform-logs-overview.md) , amelyek betekintést nyújtanak az Azure-erőforráson belül végrehajtott műveletekre. Ügynök használatával gyűjti be a naplózási adatokat a vendég operációs rendszerből.
- Létrehozhat [diagnosztikai beállításokat](../platform/diagnostic-settings.md) a virtuális gépek számára a platform metrikáinak más célhelyekre, például a Storage és az Event hubokba való küldéséhez, de ezek a diagnosztikai beállítások nem konfigurálhatók a Azure Portal. 

## <a name="monitoring-data"></a>Adatok monitorozása
Az Azure-beli virtuális gépek [naplókat](../platform/data-platform-logs.md) és [mérőszámokat](../platform/data-platform-metrics.md) hoznak elő a következő ábrán látható módon.

![Áttekintés](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Virtuális gép gazdagépe
Az Azure-beli virtuális gépek a következő, az [Adatfigyelési](monitor-azure-resource.md#monitoring-data)szolgáltatásokban leírt Azure-erőforrásokhoz hasonló, a virtuális gép gazdagépén tárolt adatforrásokat eredményezik.

- [Platform metrikái](../platform/data-platform-metrics.md) – a rendszeres időközönként automatikusan összegyűjtött numerikus értékek, amelyek egy adott időpontban egy erőforrás bizonyos aspektusait írják le. A rendszer a virtuális gép gazdagépe számára gyűjti a platform metrikáit, de a diagnosztika bővítmény szükséges a vendég operációs rendszer metrikáinak összegyűjtéséhez.
- [Műveletnapló](../platform/platform-logs-overview.md) – betekintést nyújt a műveletekre az előfizetésben lévő összes Azure-erőforráson kívülről (a felügyeleti síkon). Virtuális gépek esetén ez a következő információkat tartalmazza: az elindításuk időpontja és a konfigurációs változások.


### <a name="guest-operating-system"></a>Vendég operációs rendszer
Ha egy virtuális gép vendég operációs rendszeréről szeretne adatokat gyűjteni, szüksége van egy ügynökre, amely helyileg fut az egyes virtuális gépeken, és adatokat küld Azure Monitor. Több ügynök is rendelkezésre áll a Azure Monitorhoz, amelyekkel különböző adatok gyűjthetők össze, és az adatok különböző helyekre kerülnek. Tekintse át a különböző ügynökök részletes összehasonlítását a [Azure monitor ügynökök áttekintésével](../platform/agents-overview.md). 

- [Log Analytics ügynök](../platform/agents-overview.md#log-analytics-agent) – elérhető az Azure-beli virtuális gépek, más felhőalapú környezetek és a helyszíni eszközök számára. Adatokat gyűjt Azure Monitor naplókhoz. Támogatja a Azure Monitor for VMs és a monitorozási megoldásokat. Ez ugyanaz az ügynök, amelyet a System Center Operations Manager használ.
- [Függőségi ügynök](../platform/agents-overview.md#dependency-agent) – adatokat gyűjt a virtuális gépen futó folyamatokról és azok függőségeiről. A Log Analytics ügynökre támaszkodik, hogy adatokat továbbítson az Azure-ba, és támogatja Azure Monitor for VMs, Service Map és Wire Data 2.0 megoldásokat.
- [Azure diagnosztikai bővítmény](../platform/agents-overview.md#azure-diagnostics-extension) – csak Azure monitor virtuális gépekhez érhető el. Több helyről is gyűjthet adatokat, de elsődlegesen a vendég teljesítményadatokat a Windows rendszerű virtuális gépek Azure Monitor metrikáinak összegyűjtésére használják.
- A [Grafi ügynök](../platform/collect-custom-metrics-linux-telegraf.md) – teljesítményadatokat gyűjt a linuxos virtuális gépekről Azure monitor mérőszámokra.


## <a name="configuration-requirements"></a>Konfigurációs követelmények
Ha a Azure Monitor összes funkcióját engedélyezni szeretné egy virtuális gép figyeléséhez, a virtuális gép gazdagépéről és a vendég operációs rendszerről is össze kell gyűjtenie a megfigyelési adatokat a [Azure monitor metrikák](../platform/data-platform-logs.md) és [Azure monitor naplók](../platform/data-platform-logs.md)számára. A következő táblázat felsorolja a gyűjtemény engedélyezéséhez elvégzendő konfigurációt. Dönthet úgy, hogy nem hajtja végre ezeket a lépéseket az adott követelményektől függően.

| Konfigurációs lépés | Befejezett műveletek | Engedélyezett funkciók |
|:---|:---|:---|
| Nincs konfiguráció | -A metrikák számára gyűjtött platform-mérőszámok.<br>– A tevékenység naplója begyűjtve. | -Metrikai tallózó a gazdagéphez.<br>-Metrikákkal kapcsolatos riasztások a gazdagéphez.<br>-Műveletnapló riasztásai. |
| [Azure Monitor for VMs engedélyezése](#enable-azure-monitor-for-vms) | -Log Analytics ügynök telepítve.<br>-A függőségi ügynök telepítve van.<br>– Vendég teljesítményadatokat gyűjtött a naplókba.<br>– A naplókba gyűjtött folyamat-és függőségi részletek. | – Teljesítmény-diagramok és a vendég teljesítményadatokat tartalmazó munkafüzetek.<br>– A vendég teljesítményadatokat érintő lekérdezések naplózása.<br>– A vendég teljesítményadatokat naplózó riasztások.<br>– Függőségi Térkép. |
| [A diagnosztikai bővítmény és a-Graf ügynök telepítése](#enable-diagnostics-extension-and-telegraf-agent) | – A vendég teljesítményadatokat gyűjt a metrikák között. | -Metrikai tallózó vendégként.<br>-Metrikák riasztások vendég számára.  |
| [Log Analytics munkaterület konfigurálása](#configure-log-analytics-workspace) | – Vendégtől gyűjtött események. | – A vendég eseményeihez tartozó lekérdezések naplózása.<br>– Riasztások naplózása a vendég eseményeihez. |
| [Diagnosztikai beállítás létrehozása a virtuális géphez](#collect-platform-metrics-and-activity-log) | – A naplókba gyűjtött platform-metrikák.<br>– A naplókba gyűjtött tevékenység naplója. | -LOQ lekérdezések a gazdagép metrikái számára.<br>– Riasztások naplózása a gazdagép metrikái esetében.<br>– A tevékenység naplójának lekérdezése.

Ezeket a konfigurációs lépéseket az alábbi szakaszokban ismertetjük.

### <a name="enable-azure-monitor-for-vms"></a>Azure Monitor for VMs engedélyezése
[Azure monitor for VMS](vminsights-overview.md) [Az Azure monitor](insights-overview.md) , amely a virtuális gépek Azure monitorban való figyelésének elsődleges eszköze. A következő további értéket nyújt a standard Azure Monitor szolgáltatásaihoz képest.

- A Log Analytics ügynök és a függőségi ügynök egyszerűsített bevezetésével lehetővé teheti a virtuális gépek vendég operációs rendszerének és munkaterhelésének figyelését. 
- Előre definiált teljesítmény-diagramok és munkafüzetek, amelyek segítségével elemezheti a virtuális gép vendég operációs rendszerének alapvető teljesítmény-mérőszámait.
- Függőségi Térkép, amely megjeleníti az egyes virtuális gépeken futó folyamatokat, valamint az összekapcsolt összetevőket más gépekkel és külső forrásokkal.

![Azure Monitor virtuális gépekhez](media/monitor-vm-azure/vminsights-01.png)

![Azure Monitor virtuális gépekhez](media/monitor-vm-azure/vminsights-02.png)


A Azure Portal virtuális gép menüjében engedélyezze a Azure Monitor for VMst az **adatáttekintések** lehetőségnél. További részletekért és egyéb konfigurációs módszerekhez tekintse meg a [Azure monitor for VMS áttekintésének engedélyezése](vminsights-enable-overview.md) című témakört.

![Azure Monitor for VMs engedélyezése](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>Log Analytics munkaterület konfigurálása
A Azure Monitor for VMs által használt Log Analytics ügynök adatokat küld egy [log Analytics munkaterületre](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured). A Log Analytics munkaterület konfigurálásával engedélyezheti a további teljesítményadatok, események és egyéb megfigyelési adatok gyűjtését az ügynöktől. Csak egyszer kell konfigurálni, mivel a munkaterülethez csatlakozó összes ügynök automatikusan letölti a konfigurációt, és azonnal elkezdi a definiált adatok gyűjtését. 

A munkaterülethez tartozó konfigurációt közvetlenül a Azure Monitor for VMsból érheti el, ha kiválasztja a **munkaterület-konfiguráció** elemet az első **lépések**közül. Kattintson a munkaterület nevére a menü megnyitásához.

![Munkaterület konfigurációja](media/monitor-vm-azure/workspace-configuration.png)

Válassza a **Speciális beállítások** elemet a munkaterület menüben, majd az **adatok** lehetőséget az adatforrások konfigurálásához. Windows-ügynökök esetében válassza a **Windows-eseménynaplók** lehetőséget, és vegyen fel gyakori eseménynaplókat, például a *rendszert* és az *alkalmazást*. Linux-ügynökök esetében válassza a **syslog** lehetőséget, és adjon hozzá közös létesítményeket (például *Kern* és *Daemon*). Az elérhető adatforrások listáját és a konfigurálásának részleteit a [Azure monitor ügynök adatforrásaiban](../platform/agent-data-sources.md) találhatja meg. 

![Események konfigurálása](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> A teljesítményszámlálók begyűjthetők a munkaterület konfigurációjától, ez azonban redundáns lehet a Azure Monitor for VMs által gyűjtött teljesítményszámlálók esetében. Azure Monitor for VMs a leggyakoribb számlálókat percenként egyszer gyűjti. Csak a munkaterületet összegyűjtő teljesítményszámlálók konfigurálása, ha a Azure Monitor for VMs által még nem gyűjtött számlálókat szeretné összegyűjteni, vagy ha a teljesítményadatokat használó meglévő lekérdezésekkel rendelkezik.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Diagnosztikai bővítmény és a-Graf ügynök engedélyezése
A Azure Monitor for VMs a Log Analytics ügynökön alapul, amely adatokat gyűjt egy Log Analytics munkaterületen. Ez az [Azure monitor több funkcióját](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) támogatja, például a [naplózási lekérdezéseket](../log-query/log-query-overview.md), a [naplózási riasztásokat](../platform/alerts-log.md)és a [munkafüzeteket](../platform/workbooks-overview.md). A [diagnosztikai bővítmény](../platform/diagnostics-extension-overview.md) teljesítményadatokat gyűjt a Windows rendszerű virtuális gépek vendég operációs rendszeréről az Azure Storage-ba, és szükség esetén teljesítményadatokat küld [Azure monitor mérőszámoknak](../platform/data-platform-metrics.md). A Linux rendszerű virtuális gépek esetében a-hez készült [Grafi ügynöknek](../platform/collect-custom-metrics-linux-telegraf.md) adatokat kell küldenie az Azure-mérőszámoknak.  Ez lehetővé teszi a Azure Monitor egyéb funkcióit, például a [metrikák Explorer](../platform/metrics-getting-started.md) és a [metrikák riasztásait](../platform/alerts-metric.md). A diagnosztikai bővítményt úgy is beállíthatja, hogy a Azure Monitoron kívüli eseményeket és teljesítményadatokat küldjön az Azure Event Hubs használatával.

Telepítse a diagnosztikai bővítményt egyetlen Windowsos virtuális géphez a Azure Portal a virtuális gép menüjének **diagnosztika beállítása** lehetőségével. Válassza ki a **Azure monitor** engedélyezésének lehetőségét a **mosogatók** lapon. A bővítmény sablonból vagy parancssorból való engedélyezéséhez több virtuális gép esetén lásd: [telepítés és konfigurálás](../platform/diagnostics-extension-overview.md#installation-and-configuration). A Log Analytics ügynöktől eltérően a gyűjteni kívánt adatok az egyes virtuális gépeken a bővítmény konfigurációjában vannak meghatározva.

![Diagnosztikai beállítás](media/monitor-vm-azure/diagnostic-setting.png)

A következő témakörben talál további információt: a (z) és a. [configure](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) A **diagnosztikai beállítások** menüpont Linux rendszeren érhető el, de csak az Azure Storage-ba való adatküldést teszi lehetővé.

### <a name="collect-platform-metrics-and-activity-log"></a>Platform metrikáinak és tevékenységi naplójának gyűjtése
Megtekintheti a Azure Portalban lévő egyes virtuálisgép-gazdagépek által gyűjtött platform-metrikákat és a tevékenység naplóját. Gyűjtsön adatokat ugyanabba a Log Analytics munkaterületre, mint Azure Monitor for VMs, hogy elemezze a virtuális gép által összegyűjtött többi figyelési adattal. Ez a gyűjtemény [diagnosztikai beállítással](../platform/diagnostic-settings.md)van konfigurálva. Gyűjtsön a tevékenység naplóját [az előfizetés diagnosztikai beállításával](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal).

Platform-metrikák gyűjtése a virtuális gép diagnosztikai beállításával. A többi Azure-erőforrástól eltérően nem hozhat létre diagnosztikai beállítást a Azure Portal virtuális géphez, de [más módszert](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell)kell használnia. Az alábbi példák azt mutatják be, hogyan gyűjthetők be a virtuális gépek metrikái a PowerShell és a parancssori felület használatával.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace
```

## <a name="monitoring-in-the-azure-portal"></a>Figyelés a Azure Portal 
Miután konfigurálta a figyelési adatok gyűjtését egy virtuális géphez, több lehetőség is van a Azure Portalhoz való hozzáférésre:

- Az összes figyelt erőforrásból származó adatok eléréséhez használja a **Azure monitor** menüt. 
- Azure Monitor for VMs használata a nagy léptékű virtuális gépek monitorozási csoportjaihoz.
- Egyetlen virtuális gép adatainak elemzése a Azure Portal menüjéből. Az alábbi táblázat a virtuális gépek menü figyelésének különböző lehetőségeit sorolja fel.

![Figyelés a Azure Portal](media/monitor-vm-azure/monitor-menu.png)

| Menüpont | Leírás |
|:---|:---|
| Áttekintés | Megjeleníti a virtuális gép gazdagépének [platform metrikáit](../platform/data-platform-metrics.md) . Kattintson egy gráfra, hogy működjön együtt ezekkel az adatokkal a [mérőszámok Explorerben](../platform/metrics-getting-started.md). |
| Tevékenységnapló | Az aktuális virtuális gép számára szűrt [tevékenység-naplóbejegyzések](../platform/activity-log-view.md) . |
| Insights | [Azure monitor for VMS](../insights/vminsights-overview.md) megnyitása az aktuális virtuális gép térképével. |
| Riasztások | Az aktuális virtuális géphez tartozó [riasztások](../platform/alerts-overview.md) megtekintése.  |
| Mérőszámok | Nyissa meg a [metrikák Explorert](../platform/metrics-getting-started.md) az aktuális virtuális géphez beállított hatókörrel. |
| Diagnosztikai beállítások | Engedélyezze és konfigurálja a [diagnosztikai bővítményt](../platform/diagnostics-extension-overview.md) az aktuális virtuális géphez. |
| Tanácsadói ajánlások | Javaslatok az aktuális virtuális géphez [Azure Advisor](/azure/advisor/). |
| Naplók | Nyissa meg [log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) az aktuális virtuális géphez beállított [hatókörrel](../log-query/scope.md) . |
| Csatlakozáskezelő | Az aktuális virtuális gép és más virtuális gépek közötti kapcsolatok figyeléséhez nyissa meg [Network Watcher-figyelőt](../../network-watcher/connection-monitor-preview.md) . |


## <a name="analyzing-metric-data"></a>Metrikus adatok elemzése
A metrikák a virtuális gép menüjéből **való megnyitásával** elemezheti a metrikákat a virtuális gépekhez. Az eszköz használatával kapcsolatos részletekért lásd: az [Azure Metrikaböngésző használatának első lépései](../platform/metrics-getting-started.md) . 

A virtuális gépek három névteret használnak a metrikák számára:

| Névtér | Leírás | Követelmény |
|:---|:---|:---|
| Virtuálisgép-gazda | Az összes Azure-beli virtuális gép számára automatikusan összegyűjtött gazdagép-metrikák. A metrikák részletes listája a [Microsoft. számítás/virtualMachines](../platform/metrics-supported.md#microsoftcomputevirtualmachines). | Automatikusan összegyűjtött konfiguráció nélkül. |
| Vendég (klasszikus) | A vendég operációs rendszerek és az alkalmazások teljesítményének korlátozott készlete. Elérhető a metrikák Explorerben, de nem más Azure Monitor-funkciók, például a metrikus riasztások.  | A [diagnosztikai bővítmény](../platform/diagnostics-extension-overview.md) telepítve van. Az adatok beolvasása az Azure Storage-ból történik.  |
| Virtuális gép vendége | A vendég operációs rendszer és az alkalmazás teljesítményére vonatkozó adatok a metrikák használatával minden Azure Monitor szolgáltatás számára elérhetők. | Windows esetén a Azure Monitor fogadóval telepített [diagnosztikai bővítmény](../platform/diagnostics-extension-overview.md) engedélyezve van. A Linux, a- [Graf ügynök telepítve van](../platform/collect-custom-metrics-linux-telegraf.md). |

![Mérőszámok](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Naplózási adatok elemzése
Az Azure Virtual Machines a következő adatokat gyűjti össze a naplók Azure Monitor. 

A Azure Monitor for VMs lehetővé teszi a *InsightsMetrics* táblába írt teljesítményszámlálók előre meghatározott készletének gyűjteményét. Ez ugyanaz a tábla, amelyet a [Azure monitor a tárolók számára](container-insights-overview.md)használ. 

| Adatforrás | Követelmények | Táblák |
|:---|:---|:---|
| Azure Monitor virtuális gépekhez | Engedélyezés az egyes virtuális gépeken. | InsightsMetrics<br>VMBoundPort<br>VMComputer<br>VMConnection<br>VMProcess<br>További információ: a [naplók lekérdezése Azure monitor for VMsról](vminsights-log-search.md) . |
| Tevékenységnapló | Az előfizetés diagnosztikai beállítása. | AzureActivity |
| Gazdagép metrikái | A virtuális gép diagnosztikai beállítása. | AzureMetrics |
| A vendég operációs rendszer adatforrásai | Engedélyezze Log Analytics-ügynököt, és konfigurálja az adatforrásokat. | Tekintse meg az egyes adatforrások dokumentációját. |


> [!NOTE]
> A Log Analytics ügynök által gyűjtött teljesítményadatok a *perf* táblába kerülnek, miközben Azure monitor for VMS a *InsightsMetrics* táblába gyűjti. Ez ugyanaz az adat, de a táblák eltérő struktúrával rendelkeznek. Ha a *perf*alapján már vannak lekérdezések, a rendszernek újra kell írnia a *InsightsMetrics*használatára.


## <a name="alerts"></a>Riasztások
A Azure Monitor [riasztásai](../platform/alerts-overview.md) proaktívan értesítik Önt, ha fontos feltételek találhatók a figyelési adataiban, és esetleg olyan műveletet indítanak el, mint például a logikai alkalmazás elindítása vagy webhook hívása. A riasztási szabályok határozzák meg, hogy milyen logikát kell létrehozni a riasztások létrehozásakor. Azure Monitor gyűjti a riasztási szabályok által használt adatokat, de szabályokat kell létrehoznia az Azure-előfizetése riasztási feltételeinek meghatározásához.

A következő szakaszok ismertetik a riasztási szabályok típusait, valamint azt, hogy mikor érdemes használni ezeket. Ez a javaslat a riasztási szabály típusának működésére és díjszabására épül. A riasztások részletes díjszabását itt tekintheti meg: [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Tevékenységnapló-riasztási szabályok
A [műveletnapló riasztási szabályai](../platform/alerts-activity-log.md) akkor jönnek létre, ha az adott feltételnek megfelelő bejegyzés létrejön a tevékenység naplójában. Nem számítanak fel díjat, ha a szükséges logika a tevékenység naplójában van. 

A műveletnapló-riasztások cél erőforrása lehet egy adott virtuális gép, egy erőforráscsoport összes virtuális gépe vagy egy előfizetésben lévő összes virtuális gép.

Hozzon létre például egy riasztást, ha egy kritikus virtuális gépet leállítanak, ha kiválasztja a kikapcsolt *virtuális gépet* a jel nevéhez.

![Tevékenység naplójának riasztása](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Metrika riasztási szabályai
[Metrikus riasztási szabályok](../platform/alerts-metric.md) akkor aktiválódnak, ha egy metrika értéke meghaladja a küszöbértéket. Meghatározhatja az adott küszöbértéket, vagy engedélyezheti a Azure Monitor számára, hogy a korábbi értékek alapján dinamikusan meghatározhassa a küszöbértéket.  Metrikus riasztások használata, amikor lehetséges a metrikus adatokkal, mivel azok kisebbek, és rugalmasabbak, mint a naplózási riasztási szabályok. Ők is állapottal bírnak, ha a metrika a küszöbérték alá csökken.

A műveletnapló-riasztások cél erőforrása lehet egy adott erőforráscsoport adott virtuális gépe vagy az összes virtuális gép.

Ha például olyan riasztást szeretne létrehozni, amikor egy virtuális gép processzora túllép egy adott értéket, hozzon létre egy metrikus riasztási szabályt, amely a *százalékos CPU* -t használja a jel típusaként. Adjon meg egy adott küszöbértéket, vagy engedélyezze a Azure Monitor a dinamikus küszöbérték beállítását. 

![Metrikai riasztás](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Naplóriasztások
[Riasztási szabályok naplózása](../platform/alerts-log.md) , amikor egy ütemezett napló lekérdezésének eredményei megfelelnek bizonyos feltételeknek. A naplózási lekérdezések riasztásai a legdrágábbak és a legkevésbé reagálnak a riasztási szabályokra, de hozzáférnek a legkülönbözőbb adatokhoz, és olyan összetett logikát hajthatnak végre, amelyet a többi riasztási szabály nem tud végrehajtani. 

A naplózási lekérdezés céljának erőforrása Log Analytics munkaterület. A lekérdezésben megadott számítógépek szűrése.

Ha például olyan riasztást szeretne létrehozni, amely ellenőrzi, hogy egy adott erőforráscsoport valamelyik virtuális gépe offline állapotban van-e, használja a következő lekérdezést, amely az elmúlt tíz percben egy szívverést kihagyó számítógép rekordját adja vissza. Használjon 1 küszöbértéket, amely akkor aktiválódik, ha legalább egy számítógép kihagyott szívveréssel rendelkezik.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Napló riasztása](media/monitor-vm-azure/log-alert-01.png)

Ha riasztást szeretne létrehozni, ha túlzott számú sikertelen bejelentkezés történt az előfizetésben található összes Windows rendszerű virtuális gépen, használja a következő lekérdezést, amely az elmúlt órában minden sikertelen bejelentkezési esemény rekordját visszaadja. Használjon egy küszöbértéket az engedélyezni kívánt sikertelen bejelentkezések számának megadásához. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Napló riasztása](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
System Center Operations Manager (SCOM) a virtuális gépeken a számítási feladatok részletes figyelését teszi lehetővé. Tekintse meg a [felhőalapú figyelési útmutatót](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) a figyelési platformok és a különböző implementációs stratégiák összehasonlításához.

Ha rendelkezik egy meglévő SCOM-környezettel, amelyet továbbra is használni kíván, integrálhatja azt Azure Monitor segítségével további funkciók biztosításához. A Azure Monitor által használt Log Analytics-ügynök ugyanaz, mint a SCOM, így a felügyelt virtuális gépek is elküldhetik az adatküldést. Továbbra is hozzá kell adnia az ügynököt, hogy Azure Monitor for VMs és konfigurálja a munkaterületet, hogy további adatokat gyűjtsön a fent megadott módon, de a virtuális gépek módosítás nélkül továbbra is futtathatják a meglévő felügyeleti csomagjaikat egy SCOM környezetben.

A meglévő SCOM-funkciókat bővítő Azure Monitor szolgáltatásai többek között a következők:

- Az Log Analytics használatával interaktív módon elemezheti a napló-és teljesítményadatokat.
- A naplózási riasztások használatával több virtuális gépen is meghatározhat riasztási feltételeket, és olyan hosszú távú trendeket használhat, amelyek nem használhatók a riasztások SCOM.   

A meglévő SCOM-felügyeleti csoport Log Analytics-munkaterülethez való csatlakoztatásáról további információt a [Operations Manager csatlakoztatása a Azure monitorhoz](../platform/om-agents.md) című témakörben talál.


## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan elemezheti Azure Monitor naplókban lévő adatelemzéseket a naplók használatával.](../log-query/get-started-queries.md)
* [Tudnivalók a riasztásokról Azure Monitor mérőszámokkal és naplókkal.](../platform/alerts-overview.md)

