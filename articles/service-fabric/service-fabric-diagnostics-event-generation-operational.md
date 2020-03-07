---
title: Azure Service Fabric-események listája
description: Az Azure Service Fabric által biztosított események átfogó listája a fürtök figyeléséhez.
author: srrengar
ms.topic: reference
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: e69b407bc7d58a83616daa44272ec008ccff9fad
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361222"
---
# <a name="list-of-service-fabric-events"></a>Service Fabric események listája 

A Service Fabric lehetővé teszi, hogy a fürt állapotáról [Service Fabric eseményként](service-fabric-diagnostics-events.md)tájékoztassa a fürt állapotát. Ezek a csomópontok Service Fabric által végrehajtott műveletek, valamint a fürt tulajdonosai vagy kezelője által a fürthöz vagy a felügyeleti döntésekhez kapcsolódó döntések alapján történnek. Ezek az események számos módon érhetők el, többek között a [Azure monitor naplók konfigurálása a fürthöz](service-fabric-diagnostics-oms-setup.md), vagy a [EventStore](service-fabric-diagnostics-eventstore.md)lekérdezése. Windows rendszerű gépeken ezek az események bekerülnek az eseménynaplóba – így megtekintheti a Eseménynapló Service Fabric eseményeit. 

Íme néhány jellemző ezekről az eseményekről
* Minden esemény a fürt egy adott entitásához van kötve, például: alkalmazás, szolgáltatás, csomópont, replika.
* Minden esemény a következő általános mezőket tartalmazza: EventInstanceId, EventName és category.
* Minden esemény olyan mezőket tartalmaz, amelyek az eseményt a hozzá társított entitáshoz köti. Például a ApplicationCreated esemény olyan mezőket tartalmaz, amelyek meghatározzák a létrehozott alkalmazás nevét.
* Az események úgy vannak strukturálva, hogy több eszközön is felhasználhatók legyenek további elemzés céljából. Emellett az eseményekre vonatkozó információk külön tulajdonságokként vannak definiálva, a hosszú karakterláncok helyett. 
* Az eseményeket a Service Fabric különböző alrendszerei írják a forrás (feladat) alapján. A [Service Fabric architektúrában](service-fabric-architecture.md) további információk érhetők el, és a [technikai áttekintés Service Fabric](service-fabric-technical-overview.md).

Itt látható az entitások által rendezett Service Fabric események listája.

## <a name="cluster-events"></a>Fürtesemények

**Fürt frissítési eseményei**

A fürt frissítéseivel kapcsolatos további részletek [itt](service-fabric-cluster-upgrade-windows-server.md)találhatók.

| EventId | Name (Név) | Kategória | Leírás |Forrás (feladat) | Szint | 
| --- | --- | --- | --- | --- | --- | 
| 29627 | ClusterUpgradeStarted | Frissítés | A fürt frissítése megkezdődött | CM | Tájékoztató |
| 29628 | ClusterUpgradeCompleted | Frissítés | A fürt frissítése befejeződött | CM | Tájékoztató | 
| 29629 | ClusterUpgradeRollbackStarted | Frissítés | A fürt frissítése megkezdődött a visszaállításhoz  | CM | Figyelmeztetés | 
| 29630 | ClusterUpgradeRollbackCompleted | Frissítés | A fürt frissítése befejezte a visszaállítást | CM | Figyelmeztetés | 
| 29631 | ClusterUpgradeDomainCompleted | Frissítés | A frissítési tartomány frissítése befejeződött a fürt frissítése során | CM | Tájékoztató | 

## <a name="node-events"></a>Csomópont eseményei

**Csomópont-Életciklus eseményei** 

| EventId | Name (Név) | Kategória | Leírás |Forrás (feladat) | Szint |
| --- | --- | ---| --- | --- | --- | 
| 18602 | NodeDeactivateCompleted | Statetransition sablontípus | A csomópont inaktiválása befejeződött | FM | Tájékoztató | 
| 18603 | NodeUp | Statetransition sablontípus | A fürt egy olyan csomópontot észlelt, amely elindult | FM | Tájékoztató | 
| 18604 | NodeDown | Statetransition sablontípus | A fürt észlelt egy csomópontot. A csomópontok újraindításakor egy NodeDown eseményt fog látni, amelyet egy NodeUp esemény követ |  FM | Hiba | 
| 18605 | NodeAddedToCluster | Statetransition sablontípus |  Új csomópont lett hozzáadva a fürthöz, és Service Fabric alkalmazásokat telepíthet erre a csomópontra. | FM | Tájékoztató | 
| 18606 | NodeRemovedFromCluster | Statetransition sablontípus |  Egy csomópont el lett távolítva a fürtből. Service Fabric többé nem fog alkalmazásokat telepíteni erre a csomópontra | FM | Tájékoztató | 
| 18607 | NodeDeactivateStarted | Statetransition sablontípus |  A csomópont inaktiválása megkezdődött | FM | Tájékoztató | 
| 25621 | NodeOpenSucceeded | Statetransition sablontípus |  Egy csomópont sikeresen elindult | FabricNode | Tájékoztató | 
| 25622 | NodeOpenFailed | Statetransition sablontípus |  Egy csomópont nem tudott elindulni és csatlakoztatni a gyűrűt | FabricNode | Hiba | 
| 25624 | NodeClosed | Statetransition sablontípus |  Egy csomópont leállítása sikerült | FabricNode | Tájékoztató | 
| 25626 | NodeAborted | Statetransition sablontípus |  Egy csomópontot nem sikerült leállítani | FabricNode | Hiba | 

## <a name="application-events"></a>Alkalmazás eseményei

**Alkalmazás-Életciklus eseményei**

| EventId | Name (Név) | Kategória | Leírás |Forrás (feladat) | Szint | 
| --- | --- | --- | --- | --- | --- | 
| 29620 | ApplicationCreated | Életciklus | Új alkalmazás lett létrehozva | CM | Tájékoztató | 
| 29625 | ApplicationDeleted | Életciklus | Meglévő alkalmazás törölve | CM | Tájékoztató | 
| 23083 | ApplicationProcessExited | Életciklus | Egy alkalmazáson belüli folyamat kilépett | Üzemeltetés | Tájékoztató | 

**Alkalmazás-frissítési események**

Az alkalmazások frissítéseiről [itt](service-fabric-application-upgrade.md)talál további információt.

| EventId | Name (Név) | Kategória | Leírás |Forrás (feladat) | Szint | 
| --- | --- | ---| --- | --- | --- | 
| 29621 | ApplicationUpgradeStarted | Frissítés | Egy alkalmazás frissítése megkezdődött | CM | Tájékoztató | 
| 29622 | ApplicationUpgradeCompleted | Frissítés | Egy alkalmazás frissítése befejeződött | CM | Tájékoztató | 
| 29623 | ApplicationUpgradeRollbackStarted | Frissítés | Egy alkalmazás frissítése megkezdődött a visszaállításhoz |CM | Figyelmeztetés | 
| 29624 | ApplicationUpgradeRollbackCompleted | Frissítés | Egy alkalmazás frissítése befejezte a visszaállítást | CM | Figyelmeztetés | 
| 29626 | ApplicationUpgradeDomainCompleted | Frissítés | Egy frissítési tartomány befejezte a frissítést az alkalmazás frissítése során | CM | Tájékoztató | 

## <a name="service-events"></a>Szolgáltatási események

**Szolgáltatás-Életciklus eseményei**

| EventId | Name (Név) | Kategória | Leírás |Forrás (feladat) | Szint | 
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Életciklus | Új szolgáltatás lett létrehozva | FM | Tájékoztató | 
| 18658 | ServiceDeleted | Életciklus | Meglévő szolgáltatás törölve | FM | Tájékoztató | 

## <a name="partition-events"></a>Partíciós események

**Partíciók áthelyezése események**

| EventId | Name (Név) | Kategória | Leírás |Forrás (feladat) | Szint | 
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Életciklus | A partíció újrakonfigurálása befejeződött | RA | Tájékoztató | 

## <a name="replica-events"></a>Replika eseményei

**Replika életciklusával kapcsolatos események**

| EventId | Name (Név) | Kategória | Leírás |Forrás (feladat) | Szint |
| --- | --- | ---| --- | --- | --- |
| 61701 | ReliableDictionaryOpened | Életciklus | Megbízható szótár megnyitva | DistributedDictionary | Tájékoztató |
| 61702 | ReliableDictionaryClosed | Életciklus | A megbízható szótár bezárult | DistributedDictionary | Tájékoztató |
| 61703 | ReliableDictionaryCheckpointRecovered | Életciklus | A megbízható szótár visszaállította az ellenőrzőpontot | DistributedDictionary | Tájékoztató |
| 61704 | ReliableDictionaryCheckpointFilesSent | Életciklus | A replika megbízható szótár ellenőrzőpont-fájljait elküldtük | DistributedDictionary | Tájékoztató |
| 61705 | ReliableDictionaryCheckpointFilesReceived | Életciklus | A replika megbízható szótár ellenőrzőpont-fájljait kapta | DistributedDictionary | Tájékoztató |
| 61963 | ReliableQueueOpened | Életciklus | Megbízható várólista megnyitva | DistributedQueue | Tájékoztató |
| 61964 | ReliableQueueClosed | Életciklus | A megbízható üzenetsor bezárult | DistributedQueue | Tájékoztató |
| 61965 | ReliableQueueCheckpointRecovered | Életciklus | A megbízható üzenetsor visszaállította az ellenőrzőpontot | DistributedQueue | Tájékoztató |
| 61966 | ReliableQueueCheckpointFilesSent | Életciklus | A replika megbízható várólista ellenőrzőpont-fájljait elküldtük | DistributedQueue | Tájékoztató |
| 63647 | ReliableQueueCheckpointFilesReceived | Életciklus | A replika megbízható várólista ellenőrzőpont-fájljait fogadta | DistributedQueue | Tájékoztató |
| 63648 | ReliableConcurrentQueueOpened | Életciklus | A megbízható párhuzamos üzenetsor megnyitotta | ReliableConcurrentQueue | Tájékoztató |
| 63649 | ReliableConcurrentQueueClosed | Életciklus | A megbízható párhuzamos üzenetsor bezárult | ReliableConcurrentQueue | Tájékoztató |
| 63650 | ReliableConcurrentQueueCheckpointRecovered | Életciklus | A megbízható párhuzamos üzenetsor visszaállította az ellenőrzőpontot | ReliableConcurrentQueue | Tájékoztató |
| 61687 | TStoreError | Hiba | A megbízható gyűjtemény váratlan hibát kapott | TStore | Hiba |
| 63831 | PrimaryFullCopyInitiated | Életciklus | Az elsődleges replika teljes másolatot kezdeményezett | TReplicator | Tájékoztató |
| 63832 | PrimaryPartialCopyInitiated | Életciklus | Az elsődleges replika részleges másolatot kezdeményezett | TReplicator | Tájékoztató |
| 16831 | BuildIdleReplicaStarted | Életciklus | Az elsődleges replika megkezdte a tétlen replika kiépítése | Replikáció | Tájékoztató |
| 16832 | BuildIdleReplicaCompleted | Életciklus | Az elsődleges replika befejezte az üresjárati replika kiépítése | Replikáció | Tájékoztató |
| 16833 | BuildIdleReplicaFailed | Életciklus | Az elsődleges replika nem tudta felépíteni a tétlen replikát | Replikáció | Figyelmeztetés |
| 16834 | PrimaryReplicationQueueFull | Állapot | Az elsődleges replika replikációs várólistája megtelt | Replikáció | Figyelmeztetés |
| 16835 | PrimaryReplicationQueueWarning | Állapot | Az elsődleges replika replikációs várólistája majdnem megtelt | Replikáció | Figyelmeztetés |
| 16836 | PrimaryReplicationQueueWarningMitigated | Állapot | Az elsődleges replika replikációs várólistája rendben van | Replikáció | Tájékoztató |
| 16837 | SecondaryReplicationQueueFull | Állapot | A másodlagos replika replikációs várólistája megtelt | Replikáció | Figyelmeztetés |
| 16838 | SecondaryReplicationQueueWarning | Állapot | A másodlagos replika replikációs várólistája majdnem megtelt | Replikáció | Figyelmeztetés |
| 16839 | SecondaryReplicationQueueWarningMitigated | Állapot | A másodlagos replika replikációs várólistája rendben van | Replikáció | Tájékoztató |
| 16840 | PrimaryFaultedSlowSecondary | Állapot | Az elsődleges replika meghibásodott egy lassú másodlagos replika | Replikáció | Figyelmeztetés |
| 16841 | ReplicatorFaulted | Állapot | A replika hibát jelzett | Replikáció | Figyelmeztetés |

## <a name="container-events"></a>Tároló eseményei

**Tároló életciklusával kapcsolatos események** 

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Egy tároló elindult | Üzemeltetés | Tájékoztató | 1 |
| 23075 | ContainerDeactivated | Egy tároló leállt | Üzemeltetés | Tájékoztató | 1 |
| 23082 | ContainerExited | Egy tároló kilépett – jelölje be a UnexpectedTermination jelzőt. | Üzemeltetés | Tájékoztató | 1 |

## <a name="health-reports"></a>Állapotjelentés

A [Service Fabric Health Model](service-fabric-health-introduction.md) sokoldalú, rugalmas és bővíthető egészségügyi értékelést és jelentéskészítést biztosít. A Service Fabric 6,2-es verziójának elindítása után az állapotadatok platform-eseményekként vannak beírva, hogy a korábbi adatokat szolgáltassanak. Az állapot-események alacsony mennyiségének megőrzése érdekében csak a következőket írunk Service Fabric eseményekként:

* Az összes `Error` vagy `Warning` Health-jelentés
* `Ok` állapot-jelentések az átmenetek során
* Ha egy `Error` vagy `Warning` állapot esemény lejár. Ezzel határozható meg, hogy az entitás mennyi ideig volt sérült

**Fürt állapotáról szóló jelentés eseményei**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Új fürt állapot-jelentés érhető el | HM | Tájékoztató | 1 |
| 54437 | ClusterHealthReportExpired | Egy meglévő fürterőforrás-jelentés lejárt | HM | Tájékoztató | 1 |

**Csomópont állapotáról szóló jelentés eseményei**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Új Node Health-jelentés érhető el | HM | Tájékoztató | 1 |
| 54432 | NodeHealthReportExpired | Egy meglévő Node Health-jelentés lejárt | HM | Tájékoztató | 1 |

**Az alkalmazás állapotáról szóló jelentés eseményei**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Új alkalmazás-állapotjelentés lett létrehozva. Ez a nem telepített alkalmazások esetében használható. | HM | Tájékoztató | 1 |
| 54426 | DeployedApplicationNewHealthReport | A rendszer létrehozta egy új, központilag telepített Application Health-jelentést | HM | Tájékoztató | 1 |
| 54427 | DeployedServicePackageNewHealthReport | A rendszer létrehozta az új központilag telepített Service Health-jelentést | HM | Tájékoztató | 1 |
| 54434 | ApplicationHealthReportExpired | Egy meglévő alkalmazás állapotáról szóló jelentés lejárt | HM | Tájékoztató | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Egy meglévő telepített alkalmazás állapotáról szóló jelentés lejárt | HM | Tájékoztató | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Egy meglévő telepített szolgáltatás állapotáról szóló jelentés lejárt | HM | Tájékoztató | 1 |

**Szolgáltatás állapotáról szóló jelentés eseményei**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Új szolgáltatás-állapotjelentés lett létrehozva | HM | Tájékoztató | 1 |
| 54433 | ServiceHealthReportExpired | Egy meglévő Service Health-jelentés lejárt | HM | Tájékoztató | 1 |

**Partíciós állapot jelentésének eseményei**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Új Partition Health-jelentés lett létrehozva | HM | Tájékoztató | 1 |
| 54431 | PartitionHealthReportExpired | Egy meglévő Partition Health-jelentés lejárt | HM | Tájékoztató | 1 |

**Replika állapotáról szóló jelentés eseményei**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Állapot-nyilvántartó replika állapotáról készült jelentés létrehozva | HM | Tájékoztató | 1 |
| 54430 | StatelessInstanceNewHealthReport | Új állapot nélküli példány állapotára vonatkozó jelentés lett létrehozva. | HM | Tájékoztató | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Egy meglévő állapot-nyilvántartó replika állapotáról szóló jelentés lejárt | HM | Tájékoztató | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Egy meglévő állapot nélküli példány állapotáról szóló jelentés lejárt | HM | Tájékoztató | 1 |

## <a name="chaos-testing-events"></a>Chaos-tesztelési események 

**Chaos-munkamenet eseményei**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | A Chaos-tesztelési munkamenet elindult | Testability | Tájékoztató | 1 |
| 50023 | ChaosStopped | A káosz tesztelési munkamenete leállt | Testability | Tájékoztató | 1 |

**Chaos Node-események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Egy csomópont egy Chaos-tesztelési munkamenet részeként ütemezett újraindításra van ütemezve | Testability | Tájékoztató | 1 |
| 50087 | ChaosNodeRestartCompleted | Egy csomópont befejezte az újraindítást egy Chaos-tesztelési munkamenet részeként | Testability | Tájékoztató | 1 |

**Chaos-alkalmazás eseményei**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | A rendszer újraindítást végez egy Chaos-tesztelési munkamenet során. | Testability | Tájékoztató | 1 |
| 50101 | ChaosCodePackageRestartCompleted | A kód-csomag újraindítása a Chaos-tesztelési munkamenet során befejeződött | Testability | Tájékoztató | 1 |

**Chaos Partition-események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Egy elsődleges partíció egy Chaos-tesztelési munkamenet részeként van ütemezve. | Testability | Tájékoztató | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Egy másodlagos partíció egy Chaos-tesztelési munkamenet részeként van ütemezve. | Testability | Tájékoztató | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Az elsődleges partíció mozgatásának mélyebb elemzése elérhető | Testability | Tájékoztató | 1 |

**Chaos replika eseményei**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | A replika újraindítása a Chaos-tesztelési munkamenet részeként lett ütemezve | Testability | Tájékoztató | 1 |
| 50051 | ChaosReplicaRemovalScheduled | A replika eltávolítása egy Chaos-tesztelési munkamenet részeként lett ütemezve | Testability | Tájékoztató | 1 |
| 50093 | ChaosReplicaRemovalCompleted | A replika eltávolítása egy Chaos-tesztelési munkamenet részeként fejeződött be | Testability | Tájékoztató | 1 |

## <a name="other-events"></a>Egyéb események

**Korrelációs események**

| EventId | Name (Név) | Leírás |Forrás (feladat) | Szint | Verzió |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | A rendszer korrelációt észlelt | Testability | Tájékoztató | 1 |

## <a name="events-prior-to-version-62"></a>Az 6,2-es verzió előtti események

Itt látható a 6,2-es verzió előtti Service Fabric által biztosított események átfogó listája.

| EventId | Name (Név) | Forrás (feladat) | Szint |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Tájékoztató |
| 25621 | NodeOpenedSuccess | FabricNode | Tájékoztató |
| 25622 | NodeOpenedFailed | FabricNode | Tájékoztató |
| 25623 | NodeClosing | FabricNode | Tájékoztató |
| 25624 | NodeClosed | FabricNode | Tájékoztató |
| 25625 | NodeAborting | FabricNode | Tájékoztató |
| 25626 | NodeAborted | FabricNode | Tájékoztató |
| 29627 | ClusterUpgradeStart | CM | Tájékoztató |
| 29628 | ClusterUpgradeComplete | CM | Tájékoztató |
| 29629 | ClusterUpgradeRollback | CM | Tájékoztató |
| 29630 | ClusterUpgradeRollbackComplete | CM | Tájékoztató |
| 29631 | ClusterUpgradeDomainComplete | CM | Tájékoztató |
| 23074 | ContainerActivated | Üzemeltetés | Tájékoztató |
| 23075 | ContainerDeactivated | Üzemeltetés | Tájékoztató |
| 29620 | ApplicationCreated | CM | Tájékoztató |
| 29621 | ApplicationUpgradeStart | CM | Tájékoztató |
| 29622 | ApplicationUpgradeComplete | CM | Tájékoztató |
| 29623 | ApplicationUpgradeRollback | CM | Tájékoztató |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Tájékoztató |
| 29625 | ApplicationDeleted | CM | Tájékoztató |
| 29626 | ApplicationUpgradeDomainComplete | CM | Tájékoztató |
| 18566 | ServiceCreated | FM | Tájékoztató |
| 18567 | ServiceDeleted | FM | Tájékoztató |

## <a name="next-steps"></a>Következő lépések

* A [diagnosztika áttekintése Service Fabric](service-fabric-diagnostics-overview.md)
* További információ a EventStore [Service Fabric EventStore – áttekintés](service-fabric-diagnostics-eventstore.md)
* [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) konfiguráció módosítása további naplók gyűjtéséhez
* A [Application Insights beállítása](service-fabric-diagnostics-event-analysis-appinsights.md) az operatív csatorna naplófájljainak megtekintéséhez
