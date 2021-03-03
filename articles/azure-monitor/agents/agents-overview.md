---
title: Az Azure monitoring Agent áttekintése | Microsoft Docs
description: Ez a cikk részletes áttekintést nyújt az Azure-ban vagy hibrid környezetben üzemeltetett virtuális gépek figyelését támogató Azure-ügynökökről.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/12/2021
ms.openlocfilehash: af18356ef42f8796b972626da4567aac68a6de5a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719984"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Monitor ügynökök áttekintése

A virtuális gépeknek és más számítási erőforrásoknak egy ügynökkel kell rendelkezniük a vendég operációs rendszer és a munkaterhelések teljesítményének és rendelkezésre állásának méréséhez szükséges figyelési adatok gyűjtéséhez. Ez a cikk az Azure Monitor által használt ügynököket ismerteti, és segít megállapítani, hogy mely igényeknek kell megfelelnie az adott környezet követelményeinek.

> [!NOTE]
> Azure Monitor jelenleg több ügynököt tartalmaz a Azure Monitor és a Log Analytics legutóbbi összevonása miatt. Előfordulhat, hogy a funkciók átfedésben vannak, és mindegyik egyedi képességgel rendelkezik. A követelményektől függően szükség lehet egy vagy több ügynökre a gépeken. 

Előfordulhat, hogy a követelmények meghatározott készlete nem teljesíthető teljes mértékben egyetlen ügynökkel egy adott gépen. Előfordulhat például, hogy olyan metrikai riasztásokat szeretne használni, amelyek az Azure Diagnostics bővítményt igénylik, de azt is szeretnék, hogy kihasználják a Log Analytics ügynök és a függőségi ügynök által igényelt virtuálisgép-elemzések funkcióit. Ilyen esetekben több ügynököt is használhat, és ez egy gyakori forgatókönyv azon ügyfelek számára, akik a funkcióit igénylik.

## <a name="summary-of-agents"></a>Ügynökök összefoglalása

Az alábbi táblázatok a Windows és a Linux rendszerhez készült Azure Monitor ügynökök gyors összehasonlítását ismertetik. A további részleteket az alábbi szakaszban találja.

> [!NOTE]
> A Azure Monitor ügynök jelenleg előzetes verzióban érhető el korlátozott képességekkel. Ez a tábla frissülni fog 

### <a name="windows-agents"></a>Windows-ügynökök

| | Azure Monitor-ügynök (előzetes verzió) | Diagnosztika<br>kiterjesztés (WAD) | Log Analytics<br>ügynök | Függőség<br>ügynök |
|:---|:---|:---|:---|:---|
| **Támogatott környezetek** | Azure<br>Egyéb felhő (Azure arc)<br>Helyszíni (Azure arc)  | Azure | Azure<br>Egyéb felhő<br>Helyszíni követelmények | Azure<br>Egyéb felhő<br>Helyszíni | 
| **Ügynökre vonatkozó követelmények**  | Nincs | Nincs | Nincs | Log Analytics-ügynököt igényel |
| **Összegyűjtött adatok** | Eseménynaplók<br>Teljesítmény | Eseménynaplók<br>ETW események<br>Teljesítmény<br>Fájl alapú naplók<br>IIS-naplók<br>.NET-alkalmazás naplói<br>Összeomlási memóriaképek<br>Ügynök diagnosztikai naplói | Eseménynaplók<br>Teljesítmény<br>Fájl alapú naplók<br>IIS-naplók<br>Bepillantást és megoldásokat<br>Egyéb szolgáltatások | Folyamatok függőségei<br>Hálózati kapcsolatok metrikái |
| **Adatküldés** | Azure Monitor-naplók<br>Azure Monitor-metrikák | Azure Storage<br>Azure Monitor-metrikák<br>Eseményközpont | Azure Monitor-naplók | Azure Monitor-naplók<br>(Log Analytics ügynökön keresztül) |
| **Szolgáltatások és**<br>**szolgáltatások**<br>**támogatott** | Log Analytics<br>Metrikaböngésző | Metrikaböngésző | VM-ismeretek<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM-ismeretek<br>Szolgáltatástérkép |

### <a name="linux-agents"></a>Linux-ügynökök

| | Azure Monitor-ügynök (előzetes verzió) | Diagnosztika<br>bővítmény (LAD) | Telegraf<br>ügynök | Log Analytics<br>ügynök | Függőség<br>ügynök |
|:---|:---|:---|:---|:---|:---|
| **Támogatott környezetek** | Azure<br>Egyéb felhő (Azure arc)<br>Helyszíni (Azure arc) | Azure | Azure<br>Egyéb felhő<br>Helyszíni követelmények | Azure<br>Egyéb felhő<br>Helyszíni követelmények | Azure<br>Egyéb felhő<br>Helyszíni |
| **Ügynökre vonatkozó követelmények**  | Nincs | Nincs | Nincs | Nincs | Log Analytics-ügynököt igényel |
| **Összegyűjtött adatok** | Rendszernapló<br>Teljesítmény | Rendszernapló<br>Teljesítmény | Teljesítmény | Rendszernapló<br>Teljesítmény| Folyamatok függőségei<br>Hálózati kapcsolatok metrikái |
| **Adatküldés** | Azure Monitor-naplók<br>Azure Monitor-metrikák | Azure Storage<br>Eseményközpont | Azure Monitor-metrikák | Azure Monitor-naplók | Azure Monitor-naplók<br>(Log Analytics ügynökön keresztül) |
| **Szolgáltatások és**<br>**szolgáltatások**<br>**támogatott** | Log Analytics<br>Metrikaböngésző | | Metrikaböngésző | VM-ismeretek<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM-ismeretek<br>Szolgáltatástérkép |


## <a name="azure-monitor-agent-preview"></a>Azure Monitor-ügynök (előzetes verzió)

A [Azure monitor ügynök](azure-monitor-agent-overview.md) jelenleg előzetes verzióban érhető el, és a Windows és Linux rendszerű gépeken egyaránt lecseréli a log Analytics Agent és a-Graf Agent ügynököt. Adatokat küldhet Azure Monitor naplókba és Azure Monitor metrikákat, és az [adatgyűjtési szabályokat (DCR)](data-collection-rule-overview.md) használja, amely méretezhető módszert biztosít az adatgyűjtési és-célhelyek konfigurálásához az egyes ügynökök számára.

Ha a következőkre van szüksége, használja a Azure Monitor-ügynököt:

- A vendég naplóit és mérőszámait összegyűjtheti bármely Azure-beli, más Felhőbeli vagy helyszíni gépen lévő gépről. ([Azure arc-kompatibilis kiszolgálók](../../azure-arc/servers/overview.md) szükségesek az Azure-on kívüli gépekhez.) 
- Adatok küldése Azure Monitor naplókba és Azure Monitor metrikák elemzéshez a Azure Monitor használatával. 
- Az Azure Storage-ba történő adatküldés archiválás céljából.
- Adatküldés harmadik féltől származó eszközökre az [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md)használatával.
- A gépek biztonságát [Azure Security Center](../../security-center/security-center-introduction.md)  vagy az [Azure Sentinel](../../sentinel/overview.md)használatával kezelheti. (Előzetes verzióban nem érhető el.)

A Azure Monitor-ügynök korlátai a következők:

- Jelenleg nyilvános előzetes verzióban érhető el. Tekintse meg a korlátozások listájának [aktuális korlátozásait](azure-monitor-agent-overview.md#current-limitations) a nyilvános előzetes verzióban.

## <a name="log-analytics-agent"></a>Log Analytics-ügynök

A [log Analytics ügynök](./log-analytics-agent.md) a vendég operációs rendszer és a virtuális gépek számítási feladatait az Azure-ban, más felhőalapú szolgáltatókban és a helyszíni gépeken is gyűjti. Adatokat küld egy Log Analytics munkaterületre. A Log Analytics ügynök ugyanaz az ügynök, amelyet az System Center Operations Manager használ, és a többkiszolgálós ügynökök számítógépei kommunikálhatnak a felügyeleti csoporttal, és Azure Monitor egyidejűleg. Ezt az ügynököt a Azure Monitor és az Azure egyéb szolgáltatásainak bizonyos bepillantást is igénylik.

> [!NOTE]
> A Windows Log Analytics ügynöke gyakran a Microsoft monitoring Agent (MMA) néven is ismert. A Linux Log Analytics-ügynökét gyakran nevezik OMS-ügynöknek.

Ha a következőkre van szüksége, használja a Log Analytics-ügynököt:

* Naplókat és teljesítményadatokat gyűjthet az Azure-on kívül üzemeltetett Azure-beli virtuális gépekről vagy hibrid gépekről.
* Adatküldés egy Log Analytics munkaterületre, hogy kihasználhassa a [Azure monitor naplók](../logs/data-platform-logs.md) , például a [naplózási lekérdezések](../logs/log-query-overview.md)által támogatott funkciókat.
* A virtuálisgép- [információk segítségével nagy](../vm/vminsights-overview.md) mennyiségű gépen figyelheti a gépeket, és nyomon követheti a folyamatokat és a függőségeket más erőforrásokra és külső folyamatokra.  
* A gépek biztonságát [Azure Security Center](../../security-center/security-center-introduction.md)  vagy az [Azure Sentinel](../../sentinel/overview.md)használatával kezelheti.
* Az Azure-és nem Azure-alapú gépek teljes körű felügyeletének biztosításához használja [Azure Automation Update Management](../../automation/update-management/overview.md), [Azure Automation állapot konfigurációját](../../automation/automation-dsc-overview.md)vagy [Azure Automation Change Tracking és leltárt](../../automation/change-tracking/overview.md) .
* Különböző [megoldásokat](../monitor-reference.md#insights-and-core-solutions) használhat egy adott szolgáltatás vagy alkalmazás figyelésére.

A Log Analytics-ügynök korlátai a következők:

- Az adatok nem küldhetők Azure Monitor metrikába, Azure Storage-ba vagy Azure-Event Hubsba.
- Nehéz egyedi figyelési definíciókat konfigurálni az egyes ügynökökhöz.
- Nehezen kezelhető nagy léptékben, mivel minden egyes virtuális gép egyedi konfigurációval rendelkezik.

## <a name="azure-diagnostics-extension"></a>Azure diagnosztikai bővítmény

A [Azure Diagnostics-bővítmény](./diagnostics-extension-overview.md) a vendég operációs rendszer és az Azure-beli virtuális gépek számítási feladatainak, valamint más számítási erőforrások munkaterhelésének figyelési adatait gyűjti. Elsősorban adatokat gyűjt az Azure Storage-ba, de lehetővé teszi az adattárolók definiálását is, így adatokat is küldhet más célhelyekre, például Azure Monitor metrikára és az Azure Event Hubsra.

Ha a következőkre van szüksége, használja az Azure diagnosztikai bővítményt:

- Adatküldés az Azure Storage-ba archiválásra vagy elemzésre olyan eszközökkel, mint például a [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Adatokat küldhet [Azure monitor mérőszámoknak](../essentials/data-platform-metrics.md) , hogy elemezze a [metrikák Explorerrel](../essentials/metrics-getting-started.md) , és kihasználja az olyan funkciók előnyeit, mint a közel valós idejű [metrikus riasztások](../alerts/alerts-metric-overview.md) és az [autoscale](../autoscale/autoscale-overview.md) (csak Windows).
- Adatküldés harmadik féltől származó eszközökre az [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md)használatával.
- [Rendszerindítási diagnosztika](../../virtual-machines/troubleshooting/boot-diagnostics.md) gyűjtése a virtuális gépek rendszerindítási problémáinak vizsgálatához.

Az Azure Diagnostics bővítmény korlátai a következők:

- Csak az Azure-erőforrásokkal használható.
- Az Azure Monitor naplókba való adatküldésre korlátozott képesség.

## <a name="telegraf-agent"></a>A Grafi ügynök

A [InfluxData-Grafi ügynök](../essentials/collect-custom-metrics-linux-telegraf.md) a Linux rendszerű számítógépek teljesítményadatokat gyűjti a Azure monitor metrikák használatával.

Ha a következőkre van szüksége, használja a-Graf Agent ügynököt:

* Adatokat küldhet [Azure monitor mérőszámoknak](../essentials/data-platform-metrics.md) , hogy elemezze a [metrikák Explorerrel](../essentials/metrics-getting-started.md) , és kihasználja az olyan funkciók előnyeit, mint a közel valós idejű [metrikus riasztások](../alerts/alerts-metric-overview.md) és az [autoscale](../autoscale/autoscale-overview.md) (csak Linux).

## <a name="dependency-agent"></a>Függőségi ügynök

A függőségi ügynök felderített adatokat gyűjt a gépen futó folyamatokról és a külső folyamatok függőségeiről. 

Ha a következőkre van szüksége, használja a függőségi ügynököt:

* Használja a Térkép funkció virtuálisgép- [bepillantást](../vm/vminsights-overview.md) vagy a [Service Map](../vm/service-map.md) megoldást.

A függőségi ügynök használatakor vegye figyelembe a következőket:

- A függőségi ügynök megköveteli, hogy a Log Analytics ügynök ugyanarra a gépre legyen telepítve.
- Linux rendszerű számítógépeken az Log Analytics-ügynököt az Azure diagnosztikai bővítmény előtt kell telepíteni.

## <a name="virtual-machine-extensions"></a>Virtuálisgép-bővítmények

A [Windows](../../virtual-machines/extensions/oms-windows.md) és a [Linux](../../virtual-machines/extensions/oms-linux.md) log Analytics bővítménye telepíti az log Analytics ügynököt az Azure Virtual Machines szolgáltatásban. A Windows és a [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) [rendszerhez](../../virtual-machines/extensions/agent-dependency-windows.md) készült Azure monitor függőségi ügynök telepíti a függőségi ügynököt az Azure Virtual Machines szolgáltatásban. Ezek ugyanazok a fent ismertetett ügynökök, de lehetővé teszik a [virtuális gépek bővítményein](../../virtual-machines/extensions/overview.md)keresztüli felügyeletét. Amikor csak lehet, használja a bővítményeket az ügynökök telepítéséhez és kezeléséhez.

Hibrid gépeken az [Azure arc-kompatibilis kiszolgálók](../../azure-arc/servers/manage-vm-extensions.md) segítségével telepítse a Log Analytics és Azure monitor függőségi virtuálisgép-bővítményeket.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A következő táblázatok a Azure Monitor ügynökök által támogatott operációs rendszereket sorolja fel. Tekintse meg az egyes ügynökök dokumentációját egyedi szempontokkal és a telepítési folyamattal kapcsolatban. A támogatott operációs rendszerekkel kapcsolatban lásd a következő témakört: a-Graf dokumentációja. Az összes operációs rendszer x64-ként van feltételezve. az x86 semmilyen operációs rendszer esetén nem támogatott.

### <a name="windows"></a>Windows

| Operációs rendszer | Azure Monitor-ügynök | Log Analytics-ügynök | Függőségi ügynök | Diagnosztikai bővítmény | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2 SP1                               | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Enterprise<br>(több munkamenetet is beleértve) és Pro<br>(Csak kiszolgálói forgatókönyvek)  | X | X | X | X |
| Windows 8 Enterprise és Pro<br>(Csak kiszolgálói forgatókönyvek)  |   | X | X |   |
| Windows 7 SP1<br>(Csak kiszolgálói forgatókönyvek)                 |   | X | X |   |

### <a name="linux"></a>Linux

| Operációs rendszer | Azure Monitor-ügynök | Log Analytics-ügynök | Függőségi ügynök | Diagnosztikai bővítmény | 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017,09                                        |   | X |   |   |
| CentOS Linux 8 <sup>1</sup> <sup>2</sup>                    | X | X | X |   |
| CentOS Linux 7                                              | X | X | X | X |
| CentOS Linux 6                                              |   | X |   |   |
| CentOS Linux 6.5 +                                           |   | X | X | X |
| Debian 10 <sup>1</sup>                                      | X |   |   |   |
| Debian 9                                                    | X | X | x | X |
| Debian 8                                                    |   | X | X |   |
| Debian 7                                                    |   |   |   | X |
| OpenSUSE 13.1 +                                              |   |   |   | X |
| Oracle Linux 8 <sup>1</sup> <sup>2</sup>                    | X | X |   |   |
| Oracle Linux 7                                              | X | X |   | X |
| Oracle Linux 6                                              |   | X |   |   |
| Oracle Linux 6.4 +                                           |   | X |   | X |
| Red Hat Enterprise Linux Server 8 <sup>1</sup> <sup>2</sup> | X | X | X |   |
| Red Hat Enterprise Linux Server 7                           | X | X | X | X |
| Red Hat Enterprise Linux Server 6                           |   | X | X |   |
| Red Hat Enterprise Linux Server 6.7 +                        |   | X | X | X |
| SUSE Linux Enterprise Server 15,2 <sup>1</sup> <sup>2</sup> | X |   |   |   |
| SUSE Linux Enterprise Server 15,1 <sup>1</sup> <sup>2</sup> | X | X |   |   |
| SUSE Linux Enterprise Server 15                             | X | X | X |   |
| SUSE Linux Enterprise Server 12                             | X | X | X | X |
| Ubuntu 20,04 LTS <sup>1</sup>                               | X | X | X |   |
| Ubuntu 18.04 LTS                                            | X | X | X | X |
| Ubuntu 16.04 LTS                                            | X | X | X | X |
| Ubuntu 14,04 LTS                                            |   | X |   | X |

<sup>1</sup> szükséges, hogy a Python 3 telepítve legyen a gépen.

<sup>2</sup> ismert probléma a syslog-események gyűjtésével. Jelenleg csak a teljesítményadatok támogatottak.
#### <a name="dependency-agent-linux-kernel-support"></a>Függőségi ügynök Linux kernel-támogatás

Mivel a függőségi ügynök a kernel szintjén működik, a támogatás a kernel verziójától is függ. A következő táblázat felsorolja a függőségi ügynök fő és alverziójának Linux operációsrendszer-kiadását és támogatott kernel-verzióit.

| Disztribúció | Operációs rendszer verziója | Kernel verziója |
|:---|:---|:---|
|  Red Hat Linux 8   | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80. \* el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
|  Red Hat Linux 7   | 7.9     | 3.10.0 – 1160 |
|                    | 7,8     | 3.10.0 – 1136 |
|                    | 7.7     | 3.10.0 – 1062 |
|                    | 7.6     | 3.10.0 – 957  |
|                    | 7,5     | 3.10.0 – 862  |
|                    | 7,4     | 3.10.0 – 693  |
| Red Hat Linux 6    | 6,10    | 2.6.32 – 754 |
|                    | 6.9     | 2.6.32 – 696  |
| CentOS Linux 8     | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80. \* el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
| CentOS Linux 7     | 7.9     | 3.10.0 – 1160 |
|                    | 7,8     | 3.10.0 – 1136 |
|                    | 7.7     | 3.10.0 – 1062 |
| CentOS Linux 6     | 6,10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 20,04   | 5.4\* |
|                    | 18,04   | 5.3.0 – 1020<br>5,0 (tartalmazza az Azure által hangolt kernelt)<br>4,18 *<br> 4,15* |
|                    | 16.04.3 | 4,15.\* |
|                    | 16,04   | 4,13.\*<br>4,11.\*<br>4,10.\*<br>4,8.\*<br>4,4.\* |
| SUSE Linux 12 Enterprise Server | 15     | 4.12.14 – 150\*
|                                 | 12 SP4 CSOMAG | 4,12. * (tartalmazza az Azure által hangolt kernelt) |
|                                 | 12 SP3 | 4,4. * |
|                                 | 12 SP2 | 4,4. * |
| Debian                          | 9      | 4.9  | 

## <a name="next-steps"></a>Következő lépések

További részleteket az egyes ügynökökről a következő címen talál:

- [A Log Analytics ügynök áttekintése](./log-analytics-agent.md)
- [Az Azure Diagnostics bővítmény áttekintése](./diagnostics-extension-overview.md)
- [Egyéni metrikák gyűjtése Linux rendszerű virtuális gépekhez a InfluxData-ben-Graf ügynökkel](../essentials/collect-custom-metrics-linux-telegraf.md)