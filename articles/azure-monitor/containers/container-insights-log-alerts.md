---
title: Riasztások naplózása Azure Monitorről a tárolók számára | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre egyéni naplózási riasztásokat a memóriához és a CPU-használathoz a Azure Monitor for containers szolgáltatásban.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: 4239567c60afda6ca165e097562cb888c731f15a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614308"
---
# <a name="how-to-create-log-alerts-from-azure-monitor-for-containers"></a>Naplóriasztások létrehozása a Tárolókhoz készült Azure Monitorból

A tárolók Azure Monitor figyeli a felügyelt vagy önállóan felügyelt Kubernetes-fürtökön üzembe helyezett tároló-munkaterhelések teljesítményét. Ahhoz, hogy riasztást kapjon, a cikk azt ismerteti, hogyan hozhat létre log-alapú riasztásokat a következő esetekben az AK-fürtökkel:

- Ha a fürt csomópontjain a CPU vagy a memória kihasználtsága meghaladja a küszöbértéket
- Ha a processzor vagy a memória kihasználtsága egy vezérlőn belül bármely tárolón meghaladja a küszöbértéket, a megfelelő erőforráson beállított korláthoz képest
- A nem *Feltaposott* állapot-csomópontok száma
- *Sikertelen*, *függőben lévő*, *ismeretlen*, *futó* vagy *sikeres* Pod-Phase Counts
- Ha a fürtcsomópontok szabad lemezterülete meghaladja a küszöbértéket

Ha magas CPU-vagy memóriahasználat-kihasználtságot szeretne használni, vagy kevés a szabad lemezterület a fürtcsomópontokon, használja a metrikai riasztás vagy metrika-mérési riasztás létrehozásához megadott lekérdezéseket. Habár a metrikák riasztásai alacsonyabb késéssel rendelkeznek, mint a naplózási riasztások, a riasztások speciális lekérdezéseket és kifinomultabb kifinomultságot biztosítanak. A naplózási riasztási lekérdezések összehasonlítják a DateTime-et a jelen értékkel a *Now* operátor használatával, és egy óra múlva. (A tárolók Azure Monitor az összes dátumot az egyezményes világidő (UTC) formátumában tárolja.)

Ha nem ismeri a Azure Monitor riasztásokat, a Kezdés előtt tekintse meg [a Microsoft Azure riasztások áttekintése](../platform/alerts-overview.md) című témakört. Ha többet szeretne megtudni a naplózási lekérdezéseket használó riasztásokról, tekintse meg a következő témakörben [szereplő riasztásokat: Azure monitor](../alerts/alerts-unified-log.md). A metrikus riasztásokról további információt a [metrikus riasztások Azure monitorban](../alerts/alerts-metric-overview.md)című témakörben talál.

## <a name="resource-utilization-log-search-queries"></a>Erőforrás-felhasználási naplók keresési lekérdezései

Az ebben a szakaszban található lekérdezések támogatják az egyes riasztási forgatókönyveket. A cikk a [riasztás létrehozása](#create-an-alert-rule) szakasz 7. lépésében használatos.

A következő lekérdezés az átlagos CPU-kihasználtságot számítja ki percenként a tagok CPU-kihasználtságának átlaga alapján.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

A következő lekérdezés minden percben kiszámítja az átlagos memória kihasználtságát a tag csomópontjainak átlagos kihasználtsága alapján.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>A következő lekérdezések a helyőrző értékeket használják, \<your-cluster-name> és a \<your-controller-name> fürtöt és a vezérlőt jelölik. A riasztások beállításakor cserélje le őket a környezetre jellemző értékekre.

A következő lekérdezés kiszámítja a vezérlőben lévő összes tároló átlagos CPU-kihasználtságát a vezérlő minden tároló példányának átlagos CPU-kihasználtsága alapján percenként. A mérték a tárolóhoz beállított korlát százalékát adja meg.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

A következő lekérdezés kiszámítja a vezérlőben lévő összes tároló átlagos memóriájának kihasználtságát egy vezérlő minden egyes tároló-példányának átlagos memória-kihasználtsága alapján percenként. A mérték a tárolóhoz beállított korlát százalékát adja meg.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

A következő lekérdezés visszaadja az összes olyan csomópontot, amely *kész* és nem megfelelő állapotú *.*

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
A következő lekérdezés a pod fázisok számát adja vissza az összes fázis alapján: *sikertelen*, *függőben lévő*, *ismeretlen*, *futó* vagy *sikeres*.  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Bizonyos Pod fázisok (például *függő*, *sikertelen* vagy *ismeretlen*) riasztásához módosítsa a lekérdezés utolsó sorát. Például a *FailedCount* való riasztáshoz használja a következőt: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

A következő lekérdezés a fürtcsomópontok lemezeit adja vissza, amelyek meghaladják a felhasznált 90%-os szabad területet. A fürt AZONOSÍTÓjának lekéréséhez először futtassa a következő lekérdezést, és másolja az értéket a `ClusterId` tulajdonságból:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása

Ez a szakasz egy mérőszám-mérési riasztási szabály létrehozását mutatja be a Azure Monitorről származó teljesítményadatok használatával. Ezt az alapszintű folyamatot számos különböző naplózási lekérdezéssel használhatja, amelyek riasztást küldenek a különböző teljesítményszámlálók esetében. Az első lépésekhez használja a korábban megadott log keresési lekérdezések egyikét. ARM-sablon használatával történő létrehozáshoz tekintse meg [Az Azure-erőforrás-sablon használatával történő naplózási riasztások létrehozásának mintáit](../alerts/alerts-log-create-templates.md).

>[!NOTE]
>A tárolók erőforrás-felhasználására vonatkozó riasztási szabály létrehozásához a következő eljárással kell váltania egy új naplózási riasztási API-ra, amelyet a [váltás API-beállítások a naplók számára](../alerts/alerts-log-api-switch.md)című témakörben talál.
>

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A Azure Portal keresse meg és válassza ki **log Analytics munkaterületeket**.
3. Log Analytics munkaterületek listájában válassza ki a tárolók Azure Monitor támogató munkaterületet. 
4. A bal oldali ablaktáblán válassza a **naplók** lehetőséget a Azure monitor naplók lap megnyitásához. Ezt a lapot használhatja Azure-naplók írásához és végrehajtásához.
5. A **naplók** lapon illessze be a korábban megadott [lekérdezések](#resource-utilization-log-search-queries) egyikét a **keresési lekérdezés** mezőbe, majd kattintson a **Futtatás** elemre az eredmények ellenőrzéséhez. Ha nem hajtja végre ezt a lépést, a **+ új riasztási** lehetőség nem választható ki.
6. A log-riasztás létrehozásához válassza az **+ új riasztás** elemet.
7. A **feltétel** szakaszban válassza ki azt az esetet, **amikor az egyéni \<logic undefined> naplók keresése** előre definiált egyéni naplózási feltétel. Az **Egyéni napló keresési** jel típusa automatikusan ki van választva, mert közvetlenül a Azure monitor naplók lapról hozunk létre riasztási szabályt.  
8. Illessze be a korábban megadott [lekérdezések](#resource-utilization-log-search-queries) egyikét a **keresési lekérdezés** mezőbe.
9. A riasztást a következőképpen állíthatja be:

    1. A legördülő lista **alapján** válassza a **metrika mértékét**. A metrikák mérése riasztást hoz létre a lekérdezés minden olyan objektumához, amelynek értéke a megadott küszöbérték felett van.
    1. A **feltétel** beállításnál válassza a **nagyobb, mint** lehetőséget, és adja meg a **75** értéket kezdeti alapértékként **a CPU** -és memória-kihasználtsági riasztásokhoz. A kevés lemezterülettel kapcsolatos riasztáshoz adja meg a **90** értéket. Vagy adjon meg egy másik értéket, amely megfelel a feltételnek.
    1. Az **trigger riasztás alapján** szakaszban válassza az **egymást követő szabálysértések** lehetőséget. A legördülő listában válassza a nagyobb, **mint** lehetőséget, és írja be a **2** értéket.
    1. Ha riasztást szeretne beállítani a tároló PROCESSZORához vagy a memória kihasználtságához, az Összesítés területen válassza **a** **ContainerName** lehetőséget. A fürt csomópontjának alacsony lemezes riasztásának konfigurálásához válassza a **ClusterId** lehetőséget.
    1. A **kiértékelt alapján** szakaszban állítsa **60 percre** az **időszak** értékét. A szabály 5 percenként fut, és az aktuális időponton belül az elmúlt órában létrehozott rekordokat adja vissza. Az időszak beállítása széles ablakos fiókok számára az esetleges adatkésés érdekében. Azt is biztosítja, hogy a lekérdezés az adatok visszaadása után elkerülje a hamis negatív értéket, ha a riasztás soha nem következik be.

10. A riasztási szabály befejezéséhez válassza a **kész** lehetőséget.
11. Adjon meg egy nevet a **riasztási szabály neve** mezőben. Adja meg azt a **leírást** , amely a riasztás részleteit tartalmazza. Válassza ki a megfelelő súlyossági szintet a megadott beállítások közül.
12. Ha azonnal aktiválni szeretné a riasztási szabályt, fogadja el az alapértelmezett értéket a **létrehozáskor a szabály engedélyezéséhez**.
13. Válasszon ki egy meglévő **műveleti csoportot** , vagy hozzon létre egy új csoportot. Ez a lépés biztosítja, hogy a rendszer minden alkalommal végrehajtson ugyanazokat a műveleteket, amikor a rendszer riasztást indít el. Konfigurálás az alapján, hogy az informatikai vagy DevOps operatív csapata hogyan kezeli az incidenseket.
14. Válassza a **riasztási szabály létrehozása** lehetőséget a riasztási szabály befejezéséhez. Azonnal el fog indulni.

## <a name="next-steps"></a>Következő lépések

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a fürtök riasztásának, megjelenítésének vagy elemzésének kiértékeléséhez és testreszabásához.

- Ha többet szeretne megtudni a Azure Monitorről és a Kubernetes-fürt egyéb szempontjainak figyeléséről, tekintse meg a [Kubernetes-fürt teljesítményének megtekintése](container-insights-analyze.md) és a [Kubernetes-fürt állapotának megtekintése](./container-insights-overview.md)című témakört
