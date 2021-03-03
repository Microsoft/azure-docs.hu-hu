---
title: Naplók lekérdezése a Container-elemzésből | Microsoft Docs
description: A Container-elemzések mérőszámokat és naplózási adatokat gyűjtenek, és ez a cikk ismerteti a rekordokat, és példákat tartalmaz a lekérdezésekre.
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: 79efa714548adbde67774cab741bf953a4ff1e83
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711110"
---
# <a name="how-to-query-logs-from-container-insights"></a>Naplók lekérdezése a Container-elemzésből

A Container-elemzések teljesítménymutatókat, leltározási adatokat és állapotadatokat gyűjtenek a tároló gazdagépei és tárolói között. Az adatok gyűjtése három percenként történik, és a rendszer továbbítja azokat a Azure Monitor Log Analytics munkaterületére. Ezek az adatAzure Monitor [lekérdezésekhez](../logs/log-query-overview.md) érhetők el. Ezeket az információkat olyan forgatókönyvekre alkalmazhatja, amelyek tartalmazzák az áttelepítés megtervezését, a kapacitás elemzését, a felderítést és az igény szerinti teljesítménnyel kapcsolatos hibaelhárítást.

## <a name="container-records"></a>Tároló rekordjai

A következő táblázatban a Container-információk által gyűjtött rekordok részletei vannak megadva. Az oszlop leírását a [ContainerInventory](/azure/azure-monitor/reference/tables/containerinventory) és a [ContainerLog](/azure/azure-monitor/reference/tables/containerlog) táblákra vonatkozó referenciában tekintheti meg.

| Adatok | Adatforrás | Adattípus | Mezők |
|------|-------------|-----------|--------|
| Tároló leltározása | Kubelet | `ContainerInventory` | TimeGenerated, számítógép, név, ContainerHostname, rendszerkép, ImageTag, ContainerState, ExitCode, EnvironmentVar, parancs, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Tároló naplója | Docker | `ContainerLog` | TimeGenerated, számítógép, rendszerkép azonosítója, név, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Tároló-csomópont leltározása | Kube API | `ContainerNodeInventory`| TimeGenerated, számítógép, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Hüvelyek leltározása egy Kubernetes-fürtben | Kube API | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, szolgáltatásnév, ControllerKind, ControllerName, tároló állapota:, ContainerStatusReason, ContainerID, ContainerName, név, PodLabel, névtér, PodStatus, ClusterName, képe, SourceSystem |
| Kubernetes-fürt csomópontjainak leltára | Kube API | `KubeNodeInventory` | TimeGenerated, számítógép, ClusterName, ClusterId, LastTransitionTimeReady, címkék, állapot, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-események | Kube API | `KubeEvents` | TimeGenerated, számítógép, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, üzenet, SourceSystem | 
| Szolgáltatások a Kubernetes-fürtben | Kube API | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| A Kubernetes-fürt csomópontjainak teljesítmény-mérőszámai | A használati metrikák a cAdvisor és a Kube API korlátaiból szerezhetők be. | A Teljesítményfigyelő &#124;, ahol a ObjectName = = "K8SNode" | Számítógép, ObjectName, CounterName &#40;cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, kártyabirtokos számlájának megterhelését, TimeGenerated, CounterPath, SourceSystem | 
| A Kubernetes-fürthöz tartozó tárolók teljesítmény-mérőszámai | A használati metrikák a cAdvisor és a Kube API korlátaiból szerezhetők be. | A Teljesítményfigyelő &#124;, ahol a ObjectName = = "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, kártyabirtokos számlájának megterhelését, TimeGenerated, CounterPath, SourceSystem | 
| Egyéni metrikák ||`InsightsMetrics` | Számítógép, név, névtér, forrás, SourceSystem, címkék<sup>1</sup>, TimeGenerated, típus, Va, _ResourceId | 

<sup>1</sup> a *címkék* tulajdonság a megfelelő metrika [több dimenzióját](../essentials/data-platform-metrics.md#multi-dimensional-metrics) jelöli. A táblázatban gyűjtött és tárolt metrikákkal `InsightsMetrics` és a rekordok tulajdonságainak leírásával kapcsolatos további információkért lásd: [InsightsMetrics – áttekintés](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

## <a name="search-logs-to-analyze-data"></a>Naplók keresése az adatelemzéshez

Azure Monitor naplók segítségével megkeresheti a trendeket, diagnosztizálhatja a szűk keresztmetszeteket, az előrejelzéseket, vagy korrelálhat olyan adatokkal, amelyek segítségével meghatározhatja, hogy az aktuális fürtkonfiguráció optimális teljesítményű-e. Az előre definiált naplók segítségével azonnal megkezdheti a használatát, vagy testre szabhatja az adatokat a kívánt módon.

A munkaterületen lévő adatok interaktív elemzését a nézet Kubernetes- **eseménynaplók** megtekintése vagy a **tároló-naplók** megtekintése lehetőség kiválasztásával végezheti el az előnézet ablaktáblán, az **elemzések** legördülő listában. A **naplóbeli keresés** lap a Azure Portal lap jobb oldalán jelenik meg.

![Adatok elemzése a Log Analyticsben](./media/container-insights-analyze/container-health-log-search-example.png)

A tároló a munkaterületre továbbított kimenetet naplózza STDOUT és STDERR. Mivel Azure Monitor figyeli az Azure által felügyelt Kubernetes (ak), a Kube-System szolgáltatást a rendszer a nagy mennyiségű generált adatok miatt ma nem gyűjti. 

### <a name="example-log-search-queries"></a>Példa naplóbeli keresési lekérdezésekre

Gyakran hasznos olyan lekérdezéseket létrehozni, amelyek egy példával vagy kettővel kezdődnek, majd az igényeinek megfelelően módosítják őket. A fejlettebb lekérdezések létrehozásához a következő példa-lekérdezésekkel lehet kísérletezni:

| Lekérdezés | Leírás | 
|-------|-------------|
| ContainerInventory<br> &#124; Project számítógép, név, rendszerkép, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; renderelési táblázat | Egy tároló életciklus-információinak listázása| 
| KubeEvents_CL<br> &#124;, ahol nem (IsEmpty (Namespace_s))<br> &#124; rendezés TimeGenerated desc szerint<br> &#124; renderelési táblázat | Kubernetes események|
| ContainerImageInventory<br> &#124; AggregatedValue = count () összefoglalása rendszerkép, ImageTag, futás szerint | Rendszerkép leltározása | 
| **Válassza ki a diagram megjelenítési beállítását**:<br> Teljesítmény<br> &#124;, ahol a ObjectName = = "K8SContainer" és a CounterName = = "cpuUsageNanoCores" &#124; összegzi a AvgCPUUsageNanoCores = AVG (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 30m), példánynév | Tároló PROCESSZORa | 
| **Válassza ki a diagram megjelenítési beállítását**:<br> Teljesítmény<br> &#124;, ahol a ObjectName = = "K8SContainer" és a CounterName = = "memoryRssBytes" &#124; összegzi a AvgUsedRssMemoryBytes = AVG (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 30m), példánynév | Tároló memóriája |
| InsightsMetrics<br> &#124;, ahol a Name = = "requests_count"<br> &#124; összefoglaló val = any (val) by TimeGenerated = bin (TimeGenerated, 1M)<br> &#124; rendezés TimeGenerated ASC szerint<br> &#124; Project RequestsPerMinute = val-Prev (val), TimeGenerated <br> &#124; Render barchart  | Percenkénti kérések egyéni metrikákkal |

## <a name="query-prometheus-metrics-data"></a>A Prometheus-metrikai adatok lekérdezése

A következő példa egy olyan Prometheus-metrikai lekérdezés, amely percenkénti lemezeket jelenít meg másodpercenként.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

A Azure Monitor névtér alapján megszűrt Prometheus-metrikák megtekintéséhez a "Prometheus" kifejezést kell megadnia. Íme egy példa egy lekérdezésre a Prometheus-metrikák megtekintéséhez a `default` kubernetes-névtérből.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

A Prometheus-adatlekérdezéseket a név alapján is közvetlenül lehet lekérdezni.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Lekérdezési konfiguráció vagy karcolási hibák

A konfigurációs vagy a selejtes hibák vizsgálatához a következő példa a tábla tájékoztató eseményeit adja vissza `KubeMonAgentEvents` .

```
KubeMonAgentEvents | where Level != "Info" 
```

A kimenet az alábbi példához hasonló eredményeket mutat:

![Az ügynöktől származó tájékoztató események lekérdezési eredményeinek naplózása](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Következő lépések

A tároló-felismerések nem tartalmazzák a riasztások előre meghatározott készletét. Tekintse át a [teljesítmény-riasztások létrehozása a Container-információkkal](./container-insights-log-alerts.md) című témakört, amelyből megtudhatja, hogyan hozhat létre ajánlott riasztásokat magas CPU-és memóriahasználat esetén a DevOps vagy működési folyamatainak és eljárásainak támogatásához.