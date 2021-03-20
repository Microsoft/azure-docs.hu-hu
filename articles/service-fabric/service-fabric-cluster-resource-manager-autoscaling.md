---
title: Azure-Service Fabric automatikus skálázási szolgáltatások és tárolók
description: Az Azure Service Fabric lehetővé teszi az automatikus skálázási szabályzatok beállítását a szolgáltatások és a tárolók számára.
author: radicmilos
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: miradic
ms.custom: devx-track-csharp
ms.openlocfilehash: 060bb9dcdd504846c76ab4c782b2857fdddfa394
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91354802"
---
# <a name="introduction-to-auto-scaling"></a>Az automatikus skálázás bemutatása
Az automatikus skálázás az Service Fabric egy további funkciója, amellyel dinamikusan méretezheti a szolgáltatásokat a szolgáltatások által jelentett terhelés vagy az erőforrások használata alapján. Az automatikus skálázás nagy rugalmasságot biztosít, és igény szerint lehetővé teszi a szolgáltatás további példányainak vagy partícióinak üzembe helyezését. A teljes automatikus skálázási folyamat automatizált és átlátható, és a szabályzatok szolgáltatáshoz való beállítása után a szolgáltatás szintjén nem szükséges manuális skálázási művelet. Az automatikus skálázás a szolgáltatás létrehozási idején vagy a szolgáltatás frissítésével bármikor bekapcsolható.

Gyakori forgatókönyv, ahol az automatikus skálázás hasznos lehet, ha egy adott szolgáltatás terhelése az idő múlásával változik. Például az átjárók a bejövő kérelmek kezeléséhez szükséges erőforrások mennyisége alapján méretezhetők. Vessünk egy példát arra, hogy a skálázási szabályok hogyan néznek ki:
* Ha az átjáró összes példánya kettőnél több magot használ átlagon, akkor egy újabb példány hozzáadásával méretezheti az átjáró szolgáltatást. Ezt óránként elvégezheti, de összesen több mint hét példánya van.
* Ha az átjáró összes példánya átlagosan 0,5-nél kevesebb magot használ, akkor egy példány eltávolításával méretezheti a szolgáltatást. Ezt óránként elvégezheti, de nem kevesebb mint három példánya van összesen.

A tárolók és a rendszeres Service Fabric szolgáltatások esetében az automatikus skálázás is támogatott. Az automatikus skálázás használatához a Service Fabric futtatókörnyezet 6,2-es vagy újabb verziójával kell futnia. 

A cikk további részében a skálázási szabályzatokat, az automatikus skálázás engedélyezésének és letiltásának módjait ismertetjük, valamint példákat is talál a szolgáltatás használatára.

## <a name="describing-auto-scaling"></a>Az automatikus skálázás leírása
Az automatikus skálázási házirendek a Service Fabric-fürt minden egyes szolgáltatásához meghatározhatók. Az egyes skálázási házirendek két részből állnak:
* A **skálázási trigger** a szolgáltatás skálázásának elvégzése után következik be. Az triggerben definiált feltételeket a rendszer rendszeres időközönként ellenőrzi, hogy a szolgáltatás méretezhető-e.

* A **skálázási mechanizmus** azt ismerteti, hogyan történik a skálázás végrehajtása az indításkor. A mechanizmust csak akkor alkalmazza a rendszer, ha az trigger feltételei teljesülnek.

Minden jelenleg támogatott eseményindító [logikai betöltési metrikákkal](service-fabric-cluster-resource-manager-metrics.md)vagy fizikai mérőszámokkal, például CPU vagy memóriahasználat használatával működik. Mindkét esetben a Service Fabric figyeli a metrika jelentett terhelését, és rendszeres időközönként kiértékeli az triggert annak meghatározásához, hogy szükség van-e skálázásra.

Két olyan mechanizmus létezik, amely jelenleg támogatja az automatikus skálázást. Az első az állapot nélküli szolgáltatások, illetve olyan tárolók esetében jelent meg, amelyeknél az automatikus skálázást [példányok](service-fabric-concepts-replica-lifecycle.md)hozzáadásával vagy eltávolításával hajtják végre. Mind az állapot-nyilvántartó, mind az állapot nélküli szolgáltatások esetében az automatikus skálázást a szolgáltatás nevesített [partícióinak](service-fabric-concepts-partitioning.md) hozzáadásával vagy eltávolításával is el lehet elvégezni.

> [!NOTE]
> Jelenleg csak egy méretezési házirend támogatott a szolgáltatásokhoz, és a skálázási házirendben csak egy skálázási trigger használható.

## <a name="average-partition-load-trigger-with-instance-based-scaling"></a>Átlagos partíciós betöltési trigger példányon alapuló skálázással
Az első típusú trigger az állapot nélküli szolgáltatás partíciójában lévő példányok terhelésén alapul. A metrikus terhelések először a partíció minden példánya terhelésének beszerzéséhez szükségesek, majd ezek az értékek a partíció összes példányán átlagban jelennek meg. A szolgáltatás skálázásakor három tényezőt kell meghatározni:

* Az _alacsonyabb terhelési küszöbérték_ olyan érték, amely meghatározza, hogy a szolgáltatás hogyan **méretezhető**. Ha a partíciók összes példányának átlagos terhelése ennél az értéknél kisebb, akkor a szolgáltatás skálázása megtörténik.
* A _felső terhelés küszöbértéke_ egy olyan érték, amely meghatározza, hogy a szolgáltatás Mikor lesz **felskálázásra**. Ha a partíció összes példányának átlagos terhelése meghaladja ezt az értéket, a rendszer kibővíti a szolgáltatást.
* A _skálázási időköz_ határozza meg, hogy az trigger milyen gyakran lesz bejelölve. Ha a trigger be van jelölve, a skálázásra van szükség, a rendszer alkalmazza azt. Ha nincs szükség skálázásra, a rendszer nem végez műveletet. Mindkét esetben a rendszer nem ellenőrzi újra az aktiválást a skálázási intervallum újbóli lejárata előtt.

Ez az trigger csak állapot nélküli szolgáltatásokkal használható (vagy állapot nélküli tárolók vagy Service Fabric szolgáltatások). Ha egy szolgáltatás több partícióval rendelkezik, a rendszer külön kiértékeli az triggert az egyes partíciók esetében, és mindegyik partíción a megadott mechanizmustól függetlenül lesz alkalmazva. Ezért ebben az esetben előfordulhat, hogy a szolgáltatás néhány partíciója ki lesz bővítve, néhányat pedig a-ben, és néhányat a terhelésük alapján nem lehet egyszerre méretezni.

A triggerrel használható egyetlen mechanizmus a PartitionInstanceCountScaleMechanism. A mechanizmus alkalmazási módját három tényező határozza meg:
* A _skálázási növekmény_ meghatározza, hogy a rendszer hány példányt fog hozzáadni vagy eltávolítani a mechanizmus indításakor.
* A _példányok maximális száma_ határozza meg a skálázás felső korlátját. Ha a partíció példányainak száma eléri ezt a korlátot, a rendszer a terheléstől függetlenül nem bővíti a szolgáltatást. Ezt a korlátot a-1 érték megadásával lehet kihagyni, és ebben az esetben a szolgáltatás a lehető legnagyobb mértékben lesz kibővítve (ez a korlát a fürtben elérhető csomópontok száma).
* A _példányok minimális száma_ határozza meg a skálázás legalacsonyabb korlátját. Ha a partíció példányainak száma eléri ezt a korlátot, a rendszer a terheléstől függetlenül nem fogja méretezni a szolgáltatást.

## <a name="setting-auto-scaling-policy-for-instance-based-scaling"></a>Az automatikus skálázási szabályzat beállítása a példányon alapuló skálázáshoz

### <a name="using-application-manifest"></a>Az alkalmazás jegyzékfájljának használata
``` xml
<LoadMetrics>
<LoadMetric Name="MetricB" Weight="High"/>
</LoadMetrics>
<ServiceScalingPolicies>
<ScalingPolicy>
    <AveragePartitionLoadScalingTrigger MetricName="MetricB" LowerLoadThreshold="1" UpperLoadThreshold="2" ScaleIntervalInSeconds="100"/>
    <InstanceCountScalingMechanism MinInstanceCount="3" MaxInstanceCount="4" ScaleIncrement="1"/>
</ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API-k használata
```csharp
FabricClient fabricClient = new FabricClient();
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//set up the rest of the ServiceDescription
AveragePartitionLoadScalingTrigger trigger = new AveragePartitionLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new PartitionInstanceCountScaleMechanism();
mechanism.MaxInstanceCount = 3;
mechanism.MinInstanceCount = 1;
mechanism.ScaleIncrement = 1;
trigger.MetricName = "servicefabric:/_CpuCores";
trigger.ScaleInterval = TimeSpan.FromMinutes(20);
trigger.LowerLoadThreshold = 1.0;
trigger.UpperLoadThreshold = 2.0;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceDescription.ScalingPolicies.Add(policy);
//as we are using scaling on a resource this must be exclusive service
//also resource monitor service needs to be enabled
serviceDescription.ServicePackageActivationMode = ServicePackageActivationMode.ExclusiveProcess
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```
### <a name="using-powershell"></a>A PowerShell használata
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.PartitionInstanceCountScaleMechanism
$mechanism.MinInstanceCount = 1
$mechanism.MaxInstanceCount = 6
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AveragePartitionLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_CpuCores"
$trigger.LowerLoadThreshold = 0.3
$trigger.UpperLoadThreshold = 0.8
$trigger.ScaleInterval = New-TimeSpan -Minutes 10
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
Update-ServiceFabricService -Stateless -ServiceName "fabric:/AppName/ServiceName" -ScalingPolicies $scalingpolicies
```

## <a name="average-service-load-trigger-with-partition-based-scaling"></a>A szolgáltatás átlagos betöltési triggere partíció-alapú skálázással
A második trigger az egyik szolgáltatás összes partíciójának betöltésén alapul. A metrikák terhelése először a partíciók minden replikájának vagy példányának terhelésének beszerzésére szolgál. Az állapot-nyilvántartó szolgáltatások esetében a partíció terhelése az elsődleges replika terhelésének tekintendő, míg az állapot nélküli szolgáltatások esetében a partíció terhelése a partíció összes példányának átlagos terhelése. Ezek az értékek a szolgáltatás összes partícióján átlagosan megtalálhatók, és ez az érték az automatikus skálázás aktiválására szolgál. Ugyanaz, mint a korábbi mechanizmusokban, három tényezőt határozhat meg a szolgáltatás skálázásakor:

* Az _alacsonyabb terhelési küszöbérték_ olyan érték, amely meghatározza, hogy a szolgáltatás hogyan **méretezhető**. Ha a szolgáltatás összes partíciójának átlagos terhelése ennél az értéknél kisebb, akkor a szolgáltatás skálázása megtörténik.
* A _felső terhelés küszöbértéke_ egy olyan érték, amely meghatározza, hogy a szolgáltatás Mikor lesz **felskálázásra**. Ha a szolgáltatás összes partíciójának átlagos terhelése meghaladja ezt az értéket, a rendszer kibővíti a szolgáltatást.
* A _skálázási időköz_ határozza meg, hogy az trigger milyen gyakran lesz bejelölve. Ha a trigger be van jelölve, a skálázásra van szükség, a rendszer alkalmazza azt. Ha nincs szükség skálázásra, a rendszer nem végez műveletet. Mindkét esetben a rendszer nem ellenőrzi újra az aktiválást a skálázási intervallum újbóli lejárata előtt.

Ez az trigger használható állapot-nyilvántartó és állapot nélküli szolgáltatásokkal is. A triggerrel használható egyetlen mechanizmus a AddRemoveIncrementalNamedPartitionScalingMechanism. Ha a szolgáltatás ki van bővítve, egy új partíció lesz hozzáadva, és a szolgáltatás skálázása a meglévő partíciók egyikében megszűnik. A szolgáltatás létrehozásakor vagy frissítésekor a rendszer ellenőrzi a korlátozásokat, és a szolgáltatás létrehozása/frissítése sikertelen lesz, ha ezek a feltételek nem teljesülnek:
* A szolgáltatáshoz nevesített partíciós sémát kell használni.
* A partíciók nevének egymást követő egész számnak kell lennie, például "0", "1",...
* Az első partíció nevének "0"-nek kell lennie.

Ha például egy szolgáltatás kezdetben három partícióval jön létre, a partíciók neveinek egyetlen érvényes lehetősége a "0", "1" és "2".

A ténylegesen végrehajtott automatikus skálázási művelet a következő elnevezési sémát is figyelembe veszi:
* Ha a szolgáltatás aktuális partíciói "0", "1" és "2", akkor a felskálázáshoz hozzáadott partíció neve "3" lesz.
* Ha a szolgáltatás jelenlegi partíciói a következők: "0", "1" és "2", akkor a méretezéshez eltávolítandó partíció a "2" nevű partíció.

Ugyanaz, mint a méretezést használó mechanizmusokhoz példányok hozzáadásával vagy eltávolításával, három paraméterrel határozható meg a mechanizmus alkalmazása:
* A _skálázási növekmény_ meghatározza, hogy a rendszer hány partíciót ad hozzá vagy távolít el a mechanizmus indításakor.
* A _partíciók maximális száma_ határozza meg a skálázás felső korlátját. Ha a szolgáltatás partícióinak száma eléri ezt a korlátot, a rendszer a terheléstől függetlenül nem bővíti a szolgáltatást. Ezt a korlátot a-1 érték megadásával lehet kihagyni, és ebben az esetben a szolgáltatás a lehető legnagyobb mértékben lesz kibővítve (ez a korlát a fürt tényleges kapacitása).
* A _példányok minimális száma_ határozza meg a skálázás legalacsonyabb korlátját. Ha a szolgáltatás partícióinak száma eléri ezt a korlátot, a rendszer a terheléstől függetlenül nem fogja méretezni a szolgáltatást.

> [!WARNING] 
> A AddRemoveIncrementalNamedPartitionScalingMechanism állapot-nyilvántartó szolgáltatásokkal való használata esetén Service Fabric **értesítés vagy figyelmeztetés nélkül** fogja hozzáadni vagy eltávolítani a partíciókat. A skálázási mechanizmus elindításakor a rendszer nem hajtja végre az adatparticionálást. Felskálázási művelet esetén az új partíciók üresek lesznek, és a méretezési művelet esetén a **partíció a benne található összes adattal együtt törölve** lesz.

## <a name="setting-auto-scaling-policy-for-partition-based-scaling"></a>Az automatikus skálázási szabályzat beállítása a particionálásra alapuló skálázáshoz

### <a name="using-application-manifest"></a>Az alkalmazás jegyzékfájljának használata
``` xml
<NamedPartition>
    <Partition Name="0" />
</NamedPartition>
<ServiceScalingPolicies>
    <ScalingPolicy>
        <AverageServiceLoadScalingTrigger MetricName="servicefabric:/_MemoryInMB" LowerLoadThreshold="300" UpperLoadThreshold="500" ScaleIntervalInSeconds="600"/>
        <AddRemoveIncrementalNamedPartitionScalingMechanism MinPartitionCount="1" MaxPartitionCount="3" ScaleIncrement="1"/>
    </ScalingPolicy>
</ServiceScalingPolicies>
```
### <a name="using-c-apis"></a>C# API-k használata
```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceUpdateDescription serviceUpdate = new StatefulServiceUpdateDescription();
AveragePartitionLoadScalingTrigger trigger = new AverageServiceLoadScalingTrigger();
PartitionInstanceCountScaleMechanism mechanism = new AddRemoveIncrementalNamedPartitionScalingMechanism();
mechanism.MaxPartitionCount = 4;
mechanism.MinPartitionCount = 1;
mechanism.ScaleIncrement = 1;
//expecting that the service already has metric NumberOfConnections
trigger.MetricName = "NumberOfConnections";
trigger.ScaleInterval = TimeSpan.FromMinutes(15);
trigger.LowerLoadThreshold = 10000;
trigger.UpperLoadThreshold = 20000;
ScalingPolicyDescription policy = new ScalingPolicyDescription(mechanism, trigger);
serviceUpdate.ScalingPolicies = new List<ScalingPolicyDescription>;
serviceUpdate.ScalingPolicies.Add(policy);
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/AppName/ServiceName"), serviceUpdate);
```
### <a name="using-powershell"></a>A PowerShell használata
```posh
$mechanism = New-Object -TypeName System.Fabric.Description.AddRemoveIncrementalNamedPartitionScalingMechanism
$mechanism.MinPartitionCount = 1
$mechanism.MaxPartitionCount = 3
$mechanism.ScaleIncrement = 2
$trigger = New-Object -TypeName System.Fabric.Description.AverageServiceLoadScalingTrigger
$trigger.MetricName = "servicefabric:/_MemoryInMB"
$trigger.LowerLoadThreshold = 5000
$trigger.UpperLoadThreshold = 10000
$trigger.ScaleInterval = New-TimeSpan -Minutes 25
$scalingpolicy = New-Object -TypeName System.Fabric.Description.ScalingPolicyDescription
$scalingpolicy.ScalingMechanism = $mechanism
$scalingpolicy.ScalingTrigger = $trigger
$scalingpolicies = New-Object 'System.Collections.Generic.List[System.Fabric.Description.ScalingPolicyDescription]'
$scalingpolicies.Add($scalingpolicy)
#as we are using scaling on a resource this must be exclusive service
#also resource monitor service needs to be enabled
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -TargetReplicaSetSize 3 -MinReplicaSetSize 2 -HasPersistedState true -PartitionNames @("0","1") -ServicePackageActivationMode ExclusiveProcess -ScalingPolicies $scalingpolicies
```

## <a name="auto-scaling-based-on-resources"></a>Automatikus skálázás erőforrások alapján

Annak lehetővé tétele, hogy az erőforrás-figyelő szolgáltatás a tényleges erőforrások alapján méretezhető legyen

``` json
"fabricSettings": [
...      
],
"addonFeatures": [
    "ResourceMonitorService"
],
```
Két metrika van, amelyek tényleges fizikai erőforrásokat képviselnek. Ezek egyike servicefabric:/_CpuCores, amely a tényleges CPU-használatot jelöli (tehát a 0,5 fél mag), a másik pedig a servicefabric:/_MemoryInMB, amely az MBs-ban használt memóriát jelöli.
A ResourceMonitorService feladata a felhasználói szolgáltatások CPU-és memória-használatának nyomon követése. Ez a szolgáltatás súlyozott mozgóátlagot alkalmaz a lehetséges rövid élettartamú tüskék kiszámításához. Az erőforrás-figyelés mind a tároló, mind a nem tároló alkalmazások esetében támogatott a Windows rendszeren, és a tárolók Linux rendszeren is. Az erőforrásokra vonatkozó automatikus skálázás csak az [exkluzív folyamatmodell](service-fabric-hosting-model.md#exclusive-process-model)által aktivált szolgáltatások esetében engedélyezett.

## <a name="next-steps"></a>Következő lépések
További információ az [alkalmazások méretezhetőségéről](service-fabric-concepts-scalability.md).
