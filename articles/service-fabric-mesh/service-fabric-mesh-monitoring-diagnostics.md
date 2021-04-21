---
title: Monitorozás és diagnosztika Azure Service Fabric Mesh alkalmazásokban
description: További információ az Azure-beli alkalmazások Service Fabric Mesh diagnosztizálásról.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: d8859293b4853cbfa8c3b3dd0e7d1bfe4f75fc40
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766168"
---
# <a name="monitoring-and-diagnostics"></a>Monitorozás és diagnosztika

> [!IMPORTANT]
> A Azure Service Fabric Mesh előzetes verziója ki lettvezetve. Az új üzemelő példányok a továbbiakban nem lesznek engedélyezettek a Service Fabric Mesh API-n keresztül. A meglévő üzemelő példányok támogatása 2021. április 28-ig folytatódik.
> 
> Részletekért lásd a [Azure Service Fabric Mesh előzetes verzió kieső kiadását.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Az Azure Service Fabric Mesh egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a fejlesztők számára a mikroszolgáltatás-alkalmazások üzembe helyezését a virtuális gépek, a tárolók és a hálózat kezelése nélkül. A diagnosztikai adatok Service Fabric Mesh és diagnosztikák három fő diagnosztikai típusba vannak besorolva:

- Alkalmazásnaplók – ezek a tárolóba sorolt alkalmazások naplóiként vannak meghatározva az alkalmazás (például Docker-naplók) rendszerezett határozzák meg.
- Platformesemények – a tárolóművelethez kapcsolódó, a Mesh platformról származó események, beleértve a tároló aktiválását, inaktiválását és megszüntetését.
- Tárolómetrikák – erőforrás-kihasználtsági és teljesítménymetrikák a tárolókhoz (Docker-statisztikák)

Ez a cikk a legújabb elérhető előzetes verzió monitorozási és diagnosztikai lehetőségeit ismerteti.

## <a name="application-logs"></a>Alkalmazásnaplók

A Docker-naplókat az üzembe helyezett tárolókból, tárolónként használhatja. A Service Fabric Mesh modellben minden tároló egy kódcsomag az alkalmazásban. A kódcsomaggal társított naplók megtekintéséhez használja a következő parancsot:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> A replika nevét az "az mesh service-replica" paranccsal kaphatja meg. A replikanevek 0-tól növelik az egész számokat.

Így néz ki a szavazóalkalmazás VotingWeb.Code tároló naplói:

```azurecli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Tárolómetrikák 

A Mesh-környezet néhány metrikát biztosít, amelyek jelzik a tárolók teljesítményével kapcsolatos adatokat. A következő metrikák érhetők el a Azure Portal Azure Monitor CLI-n keresztül:

| Metric | Leírás | Egység|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu százalékban | % |
| MemoryUtilization | ActualMem/AllocatedMem százalékban | % |
| Lefoglalt memória | A processzor a sablon alapján Azure Resource Manager van lefoglalva | Millicores (Millicore) |
| AllocatedMemory (Lefoglalt memória) | Sablononként lefoglalt Azure Resource Manager | MB |
| ActualCpu (Ténylegespu) | Processzorhasználat | Millicores (Millicore) |
| ActualMemory (Tényleges megjegyezés) | Memóriahasználat | MB |
| ContainerStatus | 0 – Érvénytelen: A tároló állapota ismeretlen <br> 1 – Függőben: A tároló az ütemezés szerint elindul <br> 2 – Indítás: A tároló indítása folyamatban van <br> 3 – Elindítva: A tároló sikeresen elindult <br> 4 – Leállítás: A tároló leáll <br> 5 – Leállítva: A tároló sikeresen leállt | N/A |
| ApplicationStatus (Alkalmazásállapot) | 0 – Ismeretlen: Az állapot nem olvasható le <br> 1 – Kész: Az alkalmazás sikeresen fut <br> 2 – Frissítés: Frissítés van folyamatban <br> 3 – Létrehozás: Az alkalmazás létrehozása folyamatban van <br> 4 – Törlés: Az alkalmazás törlése folyamatban van <br> 5 – Sikertelen: Az alkalmazás üzembe helyezése sikertelen volt | N/A |
| ServiceStatus (Szolgáltatásállapot) | 0 – Érvénytelen: A szolgáltatás jelenleg nem rendelkezik állapottal <br> 1 – OK: A szolgáltatás kifogástalan állapotú  <br> 2 – Figyelmeztetés: Előfordulhat, hogy hiba történt, amely vizsgálatot igényel <br> 3 – Hiba: Hiba lépett fel, amely miatt vizsgálatot kell folytatni <br> 4 – Ismeretlen: Az állapot nem olvasható le | N/A |
| ServiceReplicaStatus | 0 – Érvénytelen: A replika jelenleg nem rendelkezik állapottal <br> 1 – OK: A szolgáltatás kifogástalan állapotú  <br> 2 – Figyelmeztetés: Előfordulhat, hogy hiba történt, amely vizsgálatot igényel <br> 3 – Hiba: Hiba lépett fel, amely miatt vizsgálatot kell folytatni <br> 4 – Ismeretlen: Az állapot nem olvasható le | N/A | 
| RestartCount (Újraindítás száma) | Tároló-újraindítások száma | N/A |

> [!NOTE]
> A ServiceStatus és a ServiceReplicaStatus értékek ugyanazok, mint a [Service Fabric.](/dotnet/api/system.fabric.health.healthstate)

Az egyes metrikák különböző dimenziókban érhetők el, így különböző szinteken láthatja az összesítéseket. A dimenziók aktuális listája a következő:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> A CodePackageName dimenzió Linux-alkalmazások esetén nem érhető el. 

Minden dimenzió az alkalmazásmodell különböző [összetevőinek Service Fabric felel meg](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Azure Monitor CLI

A parancsok teljes listája megtalálható [](/cli/azure/monitor/metrics#az_monitor_metrics_list) a Azure Monitor CLI-dokumentumokban, de az alábbiakban néhány hasznos példát is bemutattunk 

Az erőforrás-azonosító minden példában ezt a mintát követi

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Egy alkalmazásban található tárolók CPU-kihasználtsága

```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Az egyes szolgáltatásreplikák memóriahasználata
```azurecli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Minden tároló újraindítása egy 1 órás időablakban 
```azurecli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Átlagos CPU-kihasználtság a "VotingWeb" nevű szolgáltatások között egy 1 órás időszakban
```azurecli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Metrikaböngésző

A Metrikák explorer egy panel a portálon, amelyen a Mesh-alkalmazás összes metrikát vizualizálhatja. Ez a panel az alkalmazás oldalán érhető el a portálon és az Azure Monitor panelen, amelyek közül az utóbbival megtekintheti az összes olyan Azure-erőforrás metrikát, amely támogatja a Azure Monitor. 

![Metrikaböngésző](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Következő lépések
* A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
* Ha többet szeretne megtudni a Azure Monitor metrikák parancsairól, tekintse meg az Azure Monitor [CLI-dokumentumokban.](/cli/azure/monitor/metrics#az_monitor_metrics_list)
