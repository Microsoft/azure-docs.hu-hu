---
title: Riasztások a virtuális gépekről
description: Ismerteti, hogyan hozhat létre riasztási szabályokat a virtuálisgép-elemzések által gyűjtött teljesítményadatok alapján.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: e3b5f49d9a4ed7af40afba5b267ba0c7bb9cd73a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704055"
---
# <a name="how-to-create-alerts-from-vm-insights"></a>Riasztások létrehozása a virtuális gépekről
A [Azure monitor riasztásai](../alerts/alerts-overview.md) proaktívan értesítik Önt a megfigyelési adataiban található érdekes adatmennyiségekről és mintákról. A virtuálisgép-elemzések nem tartalmazzák az előre konfigurált riasztási szabályokat, de saját maga is létrehozhat saját adatokat a gyűjtött adatok alapján. Ez a cikk útmutatást nyújt a riasztási szabályok létrehozásához, többek között a példákat tartalmazó lekérdezésekhez.

> [!IMPORTANT]
> Az ebben a cikkben ismertetett riasztások a virtuális gépekről összegyűjtött adatokból származó napló lekérdezéseken alapulnak. Ez eltér a Azure monitor által a [VM Guest Health](vminsights-health-overview.md) szolgáltatáshoz létrehozott riasztások, amely jelenleg nyilvános előzetes verzióban érhető el. Mivel ez a szolgáltatás általános elérhetőséget mutat, a riasztásokra vonatkozó útmutatást összevonjuk.


## <a name="alert-rule-types"></a>Riasztási szabályok típusai
A Azure Monitor [különböző típusú riasztási szabályokkal](../alerts/alerts-overview.md#what-you-can-alert-on) rendelkeznek a riasztás létrehozásához használt adatmennyiség alapján. A VM-elemzések által összegyűjtött összes adatokat Azure Monitor-naplók tárolják, amelyek támogatják a [naplózási riasztásokat](../alerts/alerts-log.md). Jelenleg nem használhat [metrikai riasztásokat](../alerts/alerts-log.md) a virtuális gépekről összegyűjtött teljesítményadatokat tartalmazó adatokkal, mert az adatokat nem Azure monitor metrikába gyűjti. A metrikai riasztások adatainak gyűjtéséhez telepítse a [diagnosztikai bővítményt](../agents/diagnostics-extension-overview.md) a Windows rendszerű virtuális gépekhez, vagy a Linux virtuális gépekhez készült, a Microsoft számára készült [Graf-ügynököt](../essentials/collect-custom-metrics-linux-telegraf.md) , hogy teljesítményadatokat gyűjtsön a

A Azure Monitor két típusú naplózási riasztás létezik:

- [Az eredmények száma riasztások](../alerts/alerts-unified-log.md#count-of-the-results-table-rows) esetén egyetlen riasztás jön létre, ha egy lekérdezés legalább egy megadott számú rekordot ad vissza. Ezek ideálisak a nem numerikus adatokhoz, például a [log Analytics ügynök](../agents/log-analytics-agent.md) által összegyűjtött Windows-és syslog-eseményekhez, vagy a teljesítménybeli trendek elemzéséhez több számítógép között.
- A [metrikai mérési riasztások](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) külön riasztást hoznak létre minden olyan rekordhoz, amely egy olyan értékkel rendelkezik, amely meghaladja a riasztási szabályban meghatározott küszöbértéket. Ezek a riasztási szabályok ideálisak a VM-elemzések által gyűjtött teljesítményadatok számára, mivel egyéni riasztásokat hozhatnak létre az egyes számítógépekhez.


## <a name="alert-rule-walkthrough"></a>Riasztási szabály – útmutató
Ez a szakasz egy mérőszám-mérési riasztási szabály létrehozását mutatja be a virtuálisgép-elemzésekről származó teljesítményadatokat használva. Ezt az alapszintű folyamatot számos különböző naplózási lekérdezéssel használhatja, amelyek riasztást küldenek a különböző teljesítményszámlálók esetében.

Első lépésként hozzon létre egy új riasztási szabályt a [naplók létrehozása, megtekintése és kezelése a Azure monitor használatával című](../alerts/alerts-log.md)szakaszban ismertetett eljárást követve. Az **erőforráshoz** válassza ki azt a log Analytics munkaterületet, amely Azure monitor virtuális gépeket az előfizetésében. Mivel a naplózási riasztási szabályok cél erőforrása mindig Log Analytics munkaterület, a naplózási lekérdezésnek tartalmaznia kell egy szűrőt az adott virtuális gépekhez vagy virtuálisgép-méretezési csoportokhoz. 

A riasztási szabály **feltétele** a következő [szakaszban](#sample-alert-queries) található lekérdezések egyikét használja **keresési lekérdezésként**:. A lekérdezésnek egy *AggregatedValue* nevű numerikus tulajdonságot kell visszaadnia. A számítógépnek össze kell foglalnia az adatait, hogy külön riasztást hozzon létre minden olyan virtuális géphez, amely meghaladja a küszöbértéket.

A **riasztási logikában** válassza a **metrika mérése** elemet, majd adjon meg egy **küszöbértéket**. Az **trigger riasztása alapján** beállításnál határozza meg, hogy a rendszer hányszor lépje túl a küszöbértéket a riasztás létrehozása előtt. Előfordulhat például, hogy nem biztos abban, hogy a processzor túllépte a küszöbértéket, majd visszatér a normál értékre, de ha továbbra is meghaladja a küszöbértéket több egymást követő mérésnél, akkor ügyeljen rá.

A **kiértékelt szakasz alapján** határozza meg, hogy a lekérdezés milyen gyakran fusson, és a lekérdezés időablaka. Az alább látható példában a lekérdezés 15 percenként fog futni, és kiértékeli az elmúlt 15 percben összegyűjtött teljesítményadatokat.


![Metrika mértékének riasztási szabálya](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Példa riasztási lekérdezésekre
A következő lekérdezések használhatók metrikus mérési riasztási szabályokkal a VM-elemzések által gyűjtött teljesítményadatok használatával. Mindegyik összefoglalja az adatait a számítógép alapján, így minden olyan számítógép riasztást hoz létre, amelynek értéke meghaladja a küszöbértéket.

### <a name="cpu-utilization"></a>Processzorhasználat

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Rendelkezésre álló memória MB-ban

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Rendelkezésre álló memória százalékos aránya

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Használt logikai lemez – az összes lemez az egyes számítógépeken

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Használt logikai lemez – különálló lemezek

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>Logikai lemez IOPS

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Logikai lemez adatátviteli sebessége

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Hálózati adapterek fogadott bájtjai – minden csatoló

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Hálózati adapterek fogadott bájtjai – egyedi felületek

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Hálózati adapterek elküldésének bájtjai – minden csatoló

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Hálózati adapterek elküldésének bájtjai – egyedi felületek

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport
Módosítsa az előfizetés-AZONOSÍTÓját, az erőforráscsoportot és a virtuálisgép-méretezési csoport nevét.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Adott virtuális gép
Módosítsa az előfizetés-AZONOSÍTÓját, az erőforráscsoportot és a virtuális gép nevét.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>CPU-kihasználtság az előfizetésben lévő összes számítási erőforráshoz
Módosítsa az előfizetés-AZONOSÍTÓját.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>CPU-kihasználtság az erőforráscsoport összes számítási erőforrásához
Módosítsa az előfizetés-AZONOSÍTÓját és az erőforráscsoportot.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor riasztásokról](../alerts/alerts-overview.md).
- További információ a [virtuális gépekről származó adatokkal történő naplózási lekérdezésekről](vminsights-log-search.md).
