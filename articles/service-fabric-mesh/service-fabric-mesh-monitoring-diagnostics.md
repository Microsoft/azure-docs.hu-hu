---
title: Monitorozás és diagnosztika az Azure Service Fabric Mesh-alkalmazásokban
description: Ismerkedjen meg az Azure-beli Service Fabric Meshban található alkalmazások monitorozásával és diagnosztizálásával.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: e940f0cf0d1547b317cd9e7bd15ac5486d5e70b2
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248407"
---
# <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika
Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. A Service Fabric Mesh figyelése és diagnosztikája a diagnosztikai adattípusok három fő típusa szerint vannak kategorizálva:

- Alkalmazás-naplók – ezek a tároló alkalmazások naplóiként vannak meghatározva, attól függően, hogy az alkalmazás hogyan lett kialakítva (például Docker-naplók).
- Platform eseményei – a háló platform által a tároló művelethez kapcsolódó események, amelyek jelenleg a tároló aktiválását, inaktiválását és megszüntetését foglalják magukban.
- Tároló metrikái – erőforrás-kihasználtság és a tárolók teljesítmény-mérőszámai (Docker-statisztika)

Ez a cikk a legújabb előzetes verzióhoz elérhető monitorozási és diagnosztikai lehetőségeket ismerteti.

## <a name="application-logs"></a>Alkalmazás-naplók

A Docker-naplókat tároló alapján megtekintheti az üzembe helyezett tárolókban. A Service Fabric Mesh-alkalmazás modelljében minden tároló egy kód-csomag az alkalmazásban. A következő parancs futtatásával tekintheti meg a kapcsolódó naplókat a kód csomag használatával:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Az "az Mesh Service-replika" paranccsal kérheti le a replika nevét. A replikák nevei a 0 értékről növelik az egész számokat.

Így néz ki a naplók a VotingWeb. code tárolóból a szavazási alkalmazásból:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Tároló metrikái 

A rácsvonal-környezet a tárolók működésének módját jelző néhány mérőszámot tesz elérhetővé. A következő mérőszámok érhetők el a Azure Portal és az Azure monitor CLI használatával:

| Metrika | Leírás | Egység|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu százalékként | % |
| MemoryUtilization | ActualMem/AllocatedMem százalékként | % |
| AllocatedCpu | Azure Resource Manager sablonként lefoglalt CPU | Millicores |
| AllocatedMemory | Lefoglalt memória Azure Resource Manager sablonként | MB |
| ActualCpu | CPU-használat | Millicores |
| ActualMemory | Memóriahasználat | MB |
| Tároló állapota: | 0 – érvénytelen: a tároló állapota ismeretlen <br> 1 – függőben: a tároló az indítást ütemezte <br> 2 – kezdő: a tároló a kezdési folyamatban van <br> 3 – elindítva: a tároló sikeresen elindult <br> 4 – Leállítás: a tároló leállítása folyamatban van <br> 5 – leállítva: a tároló sikeresen leállt | N/A |
| ApplicationStatus | 0 – ismeretlen: az állapot nem olvasható be. <br> 1 – kész: az alkalmazás sikeresen fut <br> 2 – verziófrissítés: folyamatban van egy frissítés <br> 3 – létrehozás: folyamatban van az alkalmazás létrehozása <br> 4 – törlés: az alkalmazás törlése folyamatban van <br> 5 – sikertelen: az alkalmazás telepítése nem sikerült | N/A |
| ServiceStatus | 0 – érvénytelen: a szolgáltatás jelenleg nem rendelkezik állapottal <br> 1 – ok: a szolgáltatás kifogástalan állapotban van  <br> 2 – figyelmeztetés: probléma lehet a vizsgálat megkövetelésével <br> 3 – hiba: van valami rossz, ami vizsgálatot igényel <br> 4 – ismeretlen: az állapot nem olvasható be. | N/A |
| ServiceReplicaStatus | 0 – érvénytelen: a replika jelenleg nem rendelkezik állapottal <br> 1 – ok: a szolgáltatás kifogástalan állapotban van  <br> 2 – figyelmeztetés: probléma lehet a vizsgálat megkövetelésével <br> 3 – hiba: van valami rossz, ami vizsgálatot igényel <br> 4 – ismeretlen: az állapot nem olvasható be. | N/A | 
| RestartCount | Tároló-újraindítások száma | N/A |

> [!NOTE]
> A ServiceStatus és a ServiceReplicaStatus értékek megegyeznek a Service Fabric [HealthState](/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) . 

Minden metrika különböző dimenziókban érhető el, így különböző szinteken láthatja az összesítéseket. A méretek aktuális listája a következő:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> A CodePackageName-dimenzió nem érhető el Linux-alkalmazásokhoz. 

Az egyes dimenziók a [Service Fabric alkalmazás modelljének](service-fabric-mesh-service-fabric-resources.md#applications-and-services) különböző összetevőinek felelnek meg

### <a name="azure-monitor-cli"></a>Azure Monitor parancssori felület

A parancsok teljes listája elérhető a [Azure monitor CLI docs](/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) -ban, de néhány hasznos példát is tartalmaz. 

Az erőforrás-azonosító például a következő mintát követi

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Egy alkalmazásban lévő tárolók CPU-kihasználtsága

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Memória kihasználtsága minden egyes szolgáltatás replikája esetében
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Az egyes tárolók újraindítása 1 órás időszakban 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* A "VotingWeb" nevű szolgáltatások átlagos CPU-kihasználtsága 1 órás időszakban
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Metrikaböngésző

A metrikák Explorer egy panel a portálon, amelyben megjelenítheti a háló alkalmazás összes mérőszámát. Ez a panel a portál alkalmazás lapján és az Azure monitor paneljén érhető el, amelynek segítségével megtekintheti az összes Azure-erőforrás metrikáit, amelyek támogatják a Azure Monitor. 

![Metrikaböngésző](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Következő lépések
* A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
* Ha többet szeretne megtudni a Azure Monitor metrikák parancsairól, tekintse meg a [Azure monitor CLI-docs](/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)című témakört.
