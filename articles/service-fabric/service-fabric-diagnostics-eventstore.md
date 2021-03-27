---
title: Azure Service Fabric Event Store
description: Ismerkedjen meg az Azure Service Fabric EventStore, amelyekkel bármikor megismerheti és figyelheti a fürt vagy a számítási feladatok állapotát.
ms.topic: conceptual
ms.date: 6/6/2019
ms.openlocfilehash: ef5049fd934a29fa1d96514c334b13358e6600cf
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626554"
---
# <a name="eventstore-overview"></a>A EventStore áttekintése

>[!NOTE]
>A Service Fabric 6,4-es verziójának megfelelően. a EventStore API-k csak az Azure-on futó Windows-fürtök esetén érhetők el. Dolgozunk ezen funkciónak a Linuxon és a különálló fürtökön való portolása során.

## <a name="overview"></a>Áttekintés

Az 6,2-es verzióban bevezetett EventStore szolgáltatás egy figyelési lehetőség a Service Fabric. A EventStore lehetővé teszi a fürt vagy a számítási feladatok állapotának megértését egy adott időpontban.
A EventStore olyan állapot-nyilvántartó Service Fabric szolgáltatás, amely az eseményeket a fürtből tartja karban. Az eseményt a Service Fabric Explorer, a REST és az API-k teszik elérhetővé. A EventStore közvetlenül lekérdezi a fürtöt a fürtben lévő bármely entitás diagnosztikai adataihoz, és a következő segítségére használható:

* A fejlesztési vagy tesztelési problémák diagnosztizálása, illetve a figyelési folyamat használata
* Győződjön meg arról, hogy a fürtön végzett felügyeleti műveletek megfelelően vannak feldolgozva
* Az Service Fabric egy adott entitással való interakciójának pillanatképe

![Képernyőfelvétel: a csomópontok ablaktábla események lapja, amely több eseményt is magában foglal, például egy NodeDown eseményt.](media/service-fabric-diagnostics-eventstore/eventstore.png)

A EventStore elérhető események teljes listájának megjelenítéséhez tekintse meg a [Service Fabric eseményeket](service-fabric-diagnostics-event-generation-operational.md).

>[!NOTE]
>A Service Fabric 6,4-es verziójának megfelelően. a EventStore API-k és az UX általánosan elérhetők az Azure Windows-fürtökhöz. Dolgozunk ezen funkciónak a Linuxon és a különálló fürtökön való portolása során.

A EventStore szolgáltatás lekérdezhető olyan eseményekhez, amelyek elérhetők az egyes entitások és entitások típusához a fürtben. Ez azt jelenti, hogy az események lekérdezése a következő szinteken végezhető el:
* Fürt: magához a fürthöz tartozó események (például a fürt frissítése)
* Csomópontok: az összes csomópont szintű esemény
* Csomópont: egy csomóponthoz tartozó, az által azonosított események `nodeName`
* Alkalmazások: az összes alkalmazás szintű esemény
* Alkalmazás: az egy alkalmazás által azonosított eseményekre vonatkozó események `applicationId`
* Szolgáltatások: a fürtök összes szolgáltatásának eseményei
* Szolgáltatás: adott szolgáltatás által azonosított események `serviceId`
* Partíciók: az összes partíció eseményei
* Partition: a által azonosított adott partíció eseményei `partitionId`
* Partíciós replikák: az adott partíción belüli összes replika/példány eseményei `partitionId`
* Partíciós replika: a és a által azonosított adott replika/példány eseményei `replicaId``partitionId`

Ha többet szeretne megtudni az API-ról, tekintse meg a [EVENTSTORE API-referenciát](/rest/api/servicefabric/sfclient-index-eventsstore).

A EventStore szolgáltatásnak lehetősége van a fürt eseményeinek összekapcsolására is. Ha olyan eseményeket vizsgál meg, amelyek a különböző entitásokból származnak, amelyek hatással lehetnek egymásra, a EventStore szolgáltatás össze tudja kapcsolni ezeket az eseményeket, hogy a fürt tevékenységeinek azonosításához segítséget nyújtson. Ha például az egyik alkalmazás nem megfelelő állapotba kerül, és az indukált változások nélkül is megtörténik, akkor a EventStore a platform által közzétett egyéb eseményeket is megtekintheti, és ezzel egy vagy több eseménnyel is korrelálhat `Error` `Warning` . Ez segít a gyorsabb hibák észlelésében és a kiváltó okok elemzésében.

## <a name="enable-eventstore-on-your-cluster"></a>EventStore engedélyezése a fürtön

### <a name="local-cluster"></a>Helyi fürt

A [ fürtfabricSettings.jsjában](service-fabric-cluster-fabric-settings.md)adja hozzá a EventStoreService addOn-szolgáltatásként, és hajtson végre egy fürt frissítését.

```json
    "addOnFeatures": [
        "EventStoreService"
    ],
```

### <a name="azure-cluster-version-65"></a>Azure-fürt 6.5-ös vagy újabb verziója
Ha az Azure-fürt a 6,5-es vagy újabb verzióra frissül, a EventStore automatikusan engedélyezve lesz a fürtön. A letiltáshoz frissítenie kell a fürtöt a következővel:

* `2019-03-01`Vagy újabb API-verzió használata 
* Adja hozzá a következő kódot a tulajdonságok szakaszához a fürtben
  ```json  
    "fabricSettings": [
      …
    ],
    "eventStoreServiceEnabled": false
  ```

### <a name="azure-cluster-version-64"></a>Azure-fürt 6,4-es verziója

Ha a 6,4-es verziót használja, szerkesztheti Azure Resource Manager-sablonját, hogy bekapcsolja a EventStore szolgáltatást. Ez a [fürt konfigurációs frissítésének](service-fabric-cluster-config-upgrade-azure.md) végrehajtásával és a következő kód hozzáadásával végezhető el, a PlacementConstraints segítségével a EventStore szolgáltatás replikáit egy adott NodeType helyezheti el, például a rendszerszolgáltatásokhoz dedikált NodeType. A (z `upgradeDescription` ) szakasz a konfiguráció frissítését konfigurálja úgy, hogy újraindítást indítson a csomópontokon. A szakasz egy másik frissítésből is eltávolítható.

```json
    "fabricSettings": [
          …
          …
          …,
         {
            "name": "EventStoreService",
            "parameters": [
              {
                "name": "TargetReplicaSetSize",
                "value": "3"
              },
              {
                "name": "MinReplicaSetSize",
                "value": "1"
              },
              {
                "name": "PlacementConstraints",
                "value": "(NodeType==<node_type_name_here>)"
              }
            ]
          }
        ],
        "upgradeDescription": {
          "forceRestart": true,
          "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807",
          "healthCheckWaitDuration": "00:01:00",
          "healthCheckStableDuration": "00:01:00",
          "healthCheckRetryTimeout": "00:5:00",
          "upgradeTimeout": "1:00:00",
          "upgradeDomainTimeout": "00:10:00",
          "healthPolicy": {
            "maxPercentUnhealthyNodes": 100,
            "maxPercentUnhealthyApplications": 100
          },
          "deltaHealthPolicy": {
            "maxPercentDeltaUnhealthyNodes": 0,
            "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0,
            "maxPercentDeltaUnhealthyApplications": 0
          }
        }
```


## <a name="next-steps"></a>Következő lépések
* Ismerkedés a EventStore API-val – [a EventStore API-k használata az Azure Service Fabric-fürtökben](service-fabric-diagnostics-eventstore-query.md)
* További információ a EventStore- [Service Fabric események](service-fabric-diagnostics-event-generation-operational.md) által kínált események listájáról
* A Service Fabric monitorozása és diagnosztika áttekintése [Service Fabric](service-fabric-diagnostics-overview.md)
* Az API-hívások teljes listájának megtekintése – [EventStore REST API referenciája](/rest/api/servicefabric/sfclient-index-eventsstore)
* További információ a fürt monitorozásáról – [a fürt és a platform figyelése](service-fabric-diagnostics-event-generation-infra.md).
