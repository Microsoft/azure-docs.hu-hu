---
title: Az Azure Service Fabric-entitások összesített állapotának megtekintése
description: Ismerteti, hogyan lehet lekérdezni, megtekinteni és kiértékelni az Azure Service Fabric entitások összesített állapotát, az állapot lekérdezéseit és az általános lekérdezéseket.
ms.topic: conceptual
ms.date: 2/28/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 261343537b6d18c98c27c9cc9453d2a64f064216
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628067"
---
# <a name="view-service-fabric-health-reports"></a>Service Fabric állapottal kapcsolatos jelentések megtekintése
Az Azure Service Fabric olyan egészségügyi [modelleket](service-fabric-health-introduction.md) vezet be, amelyekben a rendszerösszetevők és a watchdogok jelenthetik a figyelt helyi feltételeket. Az állapotfigyelő az összes [állapotadatok összesítésével](service-fabric-health-introduction.md#health-store) megállapítja, hogy az entitások állapota Kifogástalan-e.

A rendszer automatikusan kitölti a fürtöt a rendszerösszetevők által elküldett állapot-jelentésekkel. További információ: [rendszerállapot-jelentések használata a hibakereséshez](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Service Fabric több módszert biztosít az entitások összesített állapotának lekéréséhez:

* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) vagy más vizualizációs eszközök
* Állapot-lekérdezések (a PowerShell, az API vagy a REST használatával)
* Általános lekérdezések, amelyek a tulajdonságok egyikének megfelelő állapotú entitások listáját adják vissza (a PowerShell, az API vagy a REST használatával)

A beállítások megjelenítéséhez használjon egy helyi fürtöt öt csomóponttal és a [Fabric:/WordCount alkalmazással](https://github.com/Azure-Samples/service-fabric-wordcount/raw/master/WordCountV1.sfpkg). A **Fabric:/WordCount** alkalmazás két alapértelmezett szolgáltatást tartalmaz, a típusú állapot-nyilvántartó szolgáltatást `WordCountServiceType` és a típus állapot nélküli szolgáltatását `WordCountWebServiceType` . Megváltoztattam a `ApplicationManifest.xml` -t, hogy az állapot-nyilvántartó szolgáltatáshoz és egy partícióhoz hét cél replikát kelljen megadni. Mivel a fürtben csak öt csomópont található, a rendszerösszetevők figyelmeztetést jelentenek a szolgáltatás partícióján, mivel az a cél száma alatt van.

```xml
<Service Name="WordCountService">
  <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
    <UniformInt64Partition PartitionCount="[WordCountService_PartitionCount]" LowKey="1" HighKey="26" />
  </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Állapot Service Fabric Explorer
Service Fabric Explorer a fürtöt vizuálisan jeleníti meg. Az alábbi képen láthatja, hogy:

* Az Application **Fabric:/WordCount** vörös (hiba), mert a **MyWatchdog** által jelentett hibaüzenetet tartalmaz a tulajdonság **rendelkezésre állása** esetén.
* Egyik szolgáltatása, a **Fabric:/WordCount/wordcountservice partícióinak listáját** sárga (figyelmeztetés). A szolgáltatás hét replikával van konfigurálva, és a fürt öt csomóponttal rendelkezik, így két replika nem helyezhető el. Bár itt nem látható, a szolgáltatás partíciója sárga, mert egy rendszerjelentés azt jelzi, `System.FM` hogy `Partition is below target replica or instance count` . A sárga partíció elindítja a sárga szolgáltatást.
* A fürt piros alkalmazása miatt vörös.

A kiértékelés az alapértelmezett házirendeket használja a fürt jegyzékfájlja és az alkalmazás jegyzékfájlja alapján. Ezek szigorú szabályzatok, és nem tolerálják a hibát.

A fürt nézete Service Fabric Explorer:

![A fürt nézete Service Fabric Explorer.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [!NOTE]
> További információ a [Service Fabric Explorerról](service-fabric-visualizing-your-cluster.md).
>
>

## <a name="health-queries"></a>Állapot-lekérdezések
A Service Fabric az egyes támogatott [entitások típusaihoz](service-fabric-health-introduction.md#health-entities-and-hierarchy)tartozó állapot-lekérdezéseket teszi elérhetővé. Ezek az API-n keresztül érhetők el, a [FabricClient. HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager), a PowerShell-parancsmagok és a REST metódusok használatával. Ezek a lekérdezések a teljes állapotra vonatkozó információkat adják vissza az entitásról: az összesített állapot, az entitás állapotával kapcsolatos események, a gyermek állapot állapota (ha alkalmazható), a nem kifogástalan állapotú értékelések (ha az entitás nem kifogástalan) és a gyermek állapot statisztikája (ha alkalmazható).

> [!NOTE]
> A rendszer akkor adja vissza az állapotot, amikor a rendszer teljes mértékben kitölti az állapotfigyelő tárolóban. Az entitásnak aktívnak kell lennie (nincs törölve), és rendelkeznie kell egy rendszerjelentéssel. A hierarchia láncában lévő szülő entitásoknak rendszerjelentésekkel is rendelkezniük kell. Ha ezek a feltételek nem teljesülnek, az állapot-lekérdezések olyan [FabricException](/dotnet/api/system.fabric.fabricexception) adnak vissza [](/dotnet/api/system.fabric.fabricerrorcode) , `FabricHealthEntityNotFound` amely azt mutatja, hogy az entitás miért nem ad vissza FabricErrorCode.
>
>

Az állapot-lekérdezéseknek meg kell felelnie az entitás-azonosítótól, amely az entitás típusától függ. A lekérdezések elfogadják a választható állapotházirendek paramétereit. Ha nem ad meg állapotházirend-szabályzatot, a rendszer a fürt vagy az alkalmazás jegyzékfájljának [állapotát](service-fabric-health-introduction.md#health-policies) használja a kiértékeléshez. Ha a jegyzékfájlok nem tartalmaznak definíciót az állapotfigyelő házirendekhez, a rendszer az alapértelmezett állapot-házirendeket használja az értékeléshez. Az alapértelmezett állapot-házirendek nem tolerálják a hibákat. A lekérdezések olyan szűrőket is elfogadnak, amelyek csak részleges gyermekeket vagy eseményeket adnak vissza – a megadott szűrőket figyelembe vevőket is. Egy másik szűrő lehetővé teszi a gyermekek statisztikáinak kizárását.

> [!NOTE]
> A kimeneti szűrők a kiszolgálói oldalon lesznek alkalmazva, így az üzenet válaszának mérete csökken. Javasoljuk, hogy a kimeneti szűrőket használva korlátozza a visszaadott adatokat ahelyett, hogy szűrőket alkalmazzon az ügyfél oldalán.
>
>

Az entitások állapota a következőket tartalmazza:

* Az entitás összesített állapotának állapota. Az állapotfigyelő alapján számítjuk ki a Health Store-jelentések, a gyermek állapota (ha alkalmazható) és az állapotfigyelő házirendek alapján. További információ az [entitások állapotának kiértékeléséről](service-fabric-health-introduction.md#health-evaluation).  
* Az entitás állapotával kapcsolatos események.
* A gyermekekkel rendelkező entitások összes gyermeke állapotának gyűjteménye. Az állapot az entitás-azonosítókat és az összesített állapotot tartalmazza. Egy gyermek teljes állapotának lekéréséhez hívja meg a gyermek entitás típusának lekérdezési állapotát, és adja meg a gyermek azonosítóját.
* A nem kifogástalan állapotú értékelések, amelyek az entitás állapotát kiváltó jelentésre mutatnak, ha az entitás állapota nem megfelelő. Az értékelések rekurzívak, és a gyermek állapotra vonatkozó, a jelenlegi állapotot kiváltó értékeléseket tartalmazzák. A watchdog például hibát jelzett egy replikán. Az alkalmazás állapota nem kifogástalan állapotú kiértékelést mutat a nem megfelelő állapotú szolgáltatás miatt; a szolgáltatás nem kifogástalan állapotú, mert hiba történt egy partíció miatt. a partíció nem megfelelő állapotú, mert hiba történt a replika miatt. a replika állapota sérült a watchdog Error Health jelentés miatt.
* A gyermekekkel rendelkező entitások összes gyermek típusára vonatkozó állapot statisztikája. A fürt állapota például a fürtben található alkalmazások, szolgáltatások, partíciók, replikák és telepített entitások teljes számát jeleníti meg. A szolgáltatás állapota a megadott szolgáltatásban lévő partíciók és replikák teljes számát jeleníti meg.

## <a name="get-cluster-health"></a>Fürt állapotának beolvasása
A fürt entitásának állapotát adja vissza, és tartalmazza az alkalmazások és a csomópontok állapotát (a fürt gyermekei). Bemenet:

* Választható A csomópontok és a fürt eseményeinek kiértékeléséhez használt fürt állapotházirend.
* Választható Az alkalmazás állapot-szabályzatának leképezése az alkalmazás-jegyzékfájl-házirendek felülbírálására használt állapotfigyelő házirendekkel.
* Választható Olyan események, csomópontok és alkalmazások szűrése, amelyek megadják, hogy mely bejegyzéseket kell megadni, és az eredményben kell visszaadni (például hibák, vagy a figyelmeztetések és a hibák). A rendszer minden eseményt, csomópontot és alkalmazást használ az entitás összesített állapotának kiértékelésére, a szűrőtől függetlenül.
* Választható Szűrő az állapotadatok kizárásához.
* Választható Az állapot statisztikájában a háló:/rendszerállapot statisztikáit tartalmazó szűrő. Csak akkor alkalmazható, ha az állapot statisztikái nincsenek kizárva. Alapértelmezés szerint az állapot statisztikái csak a felhasználói alkalmazások statisztikáit tartalmazzák, nem a rendszeralkalmazást.

### <a name="api"></a>API
A fürt állapotának lekéréséhez hozzon létre egy `FabricClient` és hívja meg a [GetClusterHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthasync) metódust a **HealthManager**.

A következő hívás lekéri a fürt állapotát:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

A következő kód beolvassa a fürt állapotát egy egyéni fürt állapot-házirend és a csomópontok és alkalmazások szűrőinek használatával. Azt határozza meg, hogy az állapot statisztikái tartalmazzák a háló:/rendszer statisztikáit. Létrehozza a bemeneti adatokat tartalmazó [ClusterHealthQueryDescription](/dotnet/api/system.fabric.description.clusterhealthquerydescription).

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var healthStatisticsFilter = new ClusterHealthStatisticsFilter()
{
    ExcludeHealthStatistics = false,
    IncludeSystemApplicationHealthStatistics = true
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
    HealthStatisticsFilter = healthStatisticsFilter
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A fürt állapotának beolvasására szolgáló parancsmag a [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth). Először kapcsolódjon a fürthöz a [Kapcsolódás-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag használatával.

A fürt állapota öt csomópont, a Rendszeralkalmazás és a háló:/WordCount konfigurálva a leírtak szerint.

A következő parancsmag az alapértelmezett állapotházirendek használatával szerzi be a fürt állapotát. Az összesített állapot figyelmeztetés, mert a Fabric:/WordCount alkalmazás figyelmeztetésben van. Vegye figyelembe, hogy a nem kifogástalan állapotú értékelések részletesen ismertetik az összesített állapotot kiváltó feltételek részleteit.

```xml
PS D:\ServiceFabric> Get-ServiceFabricClusterHealth


AggregatedHealthState   : Warning
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.


NodeHealthStates        : 
                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
HealthStatistics        : 
                          Node                  : 5 Ok, 0 Warning, 0 Error
                          Replica               : 6 Ok, 0 Warning, 0 Error
                          Partition             : 1 Ok, 1 Warning, 0 Error
                          Service               : 1 Ok, 1 Warning, 0 Error
                          DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                          DeployedApplication   : 5 Ok, 0 Warning, 0 Error
                          Application           : 0 Ok, 1 Warning, 0 Error
```

A következő PowerShell-parancsmag beolvassa a fürt állapotát egy egyéni alkalmazás-házirend használatával. Az eredményeket úgy szűri, hogy csak az alkalmazásokat és a csomópontokat kapja meg hiba vagy figyelmeztetés alapján. Ennek eredményeképpen a rendszer nem ad vissza csomópontokat, mivel azok kifogástalanok. Csak a Fabric:/WordCount alkalmazás veszi figyelembe az alkalmazások szűrőt. Mivel az egyéni házirend azt határozza meg, hogy a figyelmeztetések a Fabric:/WordCount alkalmazáshoz tartozó hibákként legyenek felszámítva, az alkalmazás hibát jelez, és így a fürt.

```powershell
PS D:\ServiceFabric> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics


AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                            Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                            Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                    Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None
```

### <a name="rest"></a>REST
Lekérheti a fürt állapotát egy [Get-kéréssel](/rest/api/servicefabric/get-the-health-of-a-cluster) vagy egy [post-kéréssel](/rest/api/servicefabric/get-the-health-of-a-cluster-by-using-a-health-policy) , amely tartalmazza a törzsben ismertetett állapotházirend-szabályzatokat is.

## <a name="get-node-health"></a>Csomópont állapotának beolvasása
Egy csomópont entitás állapotát adja vissza, és a csomóponton jelentett állapotú eseményeket tartalmazza. Bemenet:

* Szükséges A csomópontot azonosító csomópont neve.
* Választható Az állapot kiértékeléséhez használt fürt állapotházirend-beállításai.
* Választható Olyan események szűrése, amelyek meghatározzák, hogy mely bejegyzések érdeklik, és melyeket kell visszaadni az eredményben (például csak hibák, vagy a figyelmeztetések és a hibák). A rendszer minden eseményt felhasznál az entitás összesített állapotának kiértékelésére, a szűrőtől függetlenül.

### <a name="api"></a>API
A csomópont állapotának API-n keresztüli beszerzéséhez hozzon létre egy `FabricClient` és hívja meg a [GetNodeHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getnodehealthasync) metódust a HealthManager.

A következő kód beolvassa a csomópont állapotát a megadott csomópont neve számára:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

A következő kód beolvassa a csomópont állapotát a megadott csomópont neve számára, és az események szűrője és az egyéni házirend áthalad az [NodeHealthQueryDescription](/dotnet/api/system.fabric.description.nodehealthquerydescription)használatával:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A csomópont állapotának lekéréséhez szükséges parancsmag a [Get-ServiceFabricNodeHealth](/powershell/module/servicefabric/get-servicefabricnodehealth). Először kapcsolódjon a fürthöz a [Kapcsolódás-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag használatával.
A következő parancsmag az alapértelmezett állapotházirend használatával szerzi be a csomópont állapotát:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 7/13/2017 4:39:23 PM
                        ReceivedAt            : 7/13/2017 4:40:47 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 4:40:47 PM, LastWarning = 1/1/0001 12:00:00 AM
```

A következő parancsmag a fürt összes csomópontjának állapotát lekéri:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_4                     Ok
_Node_3                     Ok
_Node_2                     Ok
_Node_1                     Ok
_Node_0                     Ok
```

### <a name="rest"></a>REST
Lekérheti a Node Health-t egy [Get-kérelemmel](/rest/api/servicefabric/get-the-health-of-a-node) vagy egy [post-kéréssel](/rest/api/servicefabric/get-the-health-of-a-node-by-using-a-health-policy) , amely tartalmazza a törzsben ismertetett állapotházirend-szabályzatokat is.

## <a name="get-application-health"></a>Alkalmazás állapotának beolvasása
Egy alkalmazás entitásának állapotát adja vissza. Tartalmazza az üzembe helyezett alkalmazás és a szolgáltatás gyermekeinek állapotát. Bemenet:

* Szükséges Az alkalmazást azonosító alkalmazás neve (URI).
* Választható Az alkalmazás-jegyzékfájl-házirendek felülbírálására használt alkalmazás-állapotfigyelő házirend.
* Választható Olyan események, szolgáltatások és központilag telepített alkalmazások szűrése, amelyek meghatározzák, hogy mely bejegyzések érdeklik, és melyeket kell visszaadni az eredményben (például csak hibák, vagy a figyelmeztetések és a hibák). A rendszer minden eseményt, szolgáltatást és központilag telepített alkalmazást használ az entitás összesített állapotának kiértékelésére, a szűrőtől függetlenül.
* Választható Szűréssel kizárhatja az állapot statisztikáit. Ha nincs megadva, az állapot statisztikái közé tartozik az OK, a figyelmeztetés és a hibák száma az alkalmazás összes gyermeke számára: szolgáltatások, partíciók, replikák, telepített alkalmazások és telepített szervizcsomagok.

### <a name="api"></a>API
Az alkalmazás állapotának lekéréséhez hozzon létre egy `FabricClient` és hívja meg a [GetApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getapplicationhealthasync) metódust a HealthManager.

A következő kód lekéri az alkalmazás állapotát a megadott alkalmazásnév (URI) számára:

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

A következő kód beolvassa a megadott alkalmazásnév (URI) alkalmazásának állapotát a [ApplicationHealthQueryDescription](/dotnet/api/system.fabric.description.applicationhealthquerydescription)használatával megadott szűrőkkel és egyéni házirendekkel.

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
Az alkalmazás állapotának lekéréséhez szükséges parancsmag a [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth). Először kapcsolódjon a fürthöz a [Kapcsolódás-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag használatával.

A következő parancsmag a **Fabric:/WordCount** alkalmazás állapotát adja vissza:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/13/2017 5:57:05 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                : 
                                  Replica               : 6 Ok, 0 Warning, 0 Error
                                  Partition             : 1 Ok, 1 Warning, 0 Error
                                  Service               : 1 Ok, 1 Warning, 0 Error
                                  DeployedServicePackage : 6 Ok, 0 Warning, 0 Error
                                  DeployedApplication   : 5 Ok, 0 Warning, 0 Error
```

A következő PowerShell-parancsmag egyéni házirendekben halad át. A gyermekeket és eseményeket is szűri.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=true.

ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>REST
Lekérheti az alkalmazás állapotát egy [Get-kérelemmel](/rest/api/servicefabric/get-the-health-of-an-application) vagy egy [post-kéréssel](/rest/api/servicefabric/get-the-health-of-an-application-by-using-an-application-health-policy) , amely tartalmazza a törzsben ismertetett állapotházirend-szabályzatokat is.

## <a name="get-service-health"></a>Szolgáltatás állapotának beolvasása
Egy szolgáltatás entitás állapotát adja vissza. Tartalmazza a partíció állapotát. Bemenet:

* Szükséges A szolgáltatás azonosítására szolgáló szolgáltatás neve (URI).
* Választható Az alkalmazás jegyzékfájl-házirendjének felülbírálására használt alkalmazás-állapotfigyelő házirend.
* Választható Olyan események és partíciók szűrése, amelyek meghatározzák, hogy mely bejegyzéseket kell megadni, és az eredményben kell visszaadni (például hibák, vagy a figyelmeztetések és a hibák). Az összes esemény és partíció az entitás összesített állapotának kiértékelésére szolgál, a szűrőtől függetlenül.
* Választható Szűrő az állapotadatok kizárásához. Ha nincs megadva, az állapot statisztikái a szolgáltatás összes partíciójának és replikájának az OK, a figyelmeztetés és a hibák számát mutatják.

### <a name="api"></a>API
A szolgáltatás állapotának API-n keresztüli lekéréséhez hozzon létre egy, `FabricClient` és hívja meg a [GetServiceHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getservicehealthasync) metódust a HealthManager.

A következő példa egy szolgáltatás állapotának beolvasása a megadott szolgáltatásnév (URI) szerint:

```csharp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

A következő kód beolvassa a megadott szolgáltatásnév (URI) szolgáltatás állapotát, a szűrők és az egyéni szabályzatok megadását a [ServiceHealthQueryDescription](/dotnet/api/system.fabric.description.servicehealthquerydescription)használatával:

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A szolgáltatás állapotának lekéréséhez szükséges parancsmag a [Get-ServiceFabricServiceHealth](/powershell/module/servicefabric/get-servicefabricservicehealth). Először kapcsolódjon a fürthöz a [Kapcsolódás-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag használatával.

A következő parancsmag a szolgáltatás állapotát alapértelmezett állapot-házirendek használatával szerzi be:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                        Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Warning'.

                            Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

PartitionHealthStates : 
                        PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                        AggregatedHealthState : Warning

HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 15
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
                        Partition             : 0 Ok, 1 Warning, 0 Error
```

### <a name="rest"></a>REST
Lekérheti a szolgáltatás állapotát egy [Get-kéréssel](/rest/api/servicefabric/get-the-health-of-a-service) vagy egy [post-kéréssel](/rest/api/servicefabric/get-the-health-of-a-service-by-using-a-health-policy) , amely tartalmazza a törzsben ismertetett állapotházirend-szabályzatokat is.

## <a name="get-partition-health"></a>Partíció állapotának beolvasása
Egy partíciós entitás állapotát adja vissza. Tartalmazza a replika állapotait. Bemenet:

* Szükséges A partíciót azonosító partíció-azonosító (GUID).
* Választható Az alkalmazás jegyzékfájl-házirendjének felülbírálására használt alkalmazás-állapotfigyelő házirend.
* Választható Olyan események és replikák szűrése, amelyek meghatározzák, hogy mely bejegyzéseket kell megadni, és az eredményben kell visszaadni (például hibák, vagy a figyelmeztetések és a hibák). A rendszer minden eseményt és replikát használ az entitás összesített állapotának kiértékelésére, a szűrőtől függetlenül.
* Választható Szűrő az állapotadatok kizárásához. Ha nincs megadva, az állapot statisztikái megmutatják, hány replika van az OK, a figyelmeztetés és a hiba állapotában.

### <a name="api"></a>API
A partíciós állapot API-n keresztüli beszerzéséhez hozzon létre egy `FabricClient` és hívja meg a [GetPartitionHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getpartitionhealthasync) metódust a HealthManager. Ha nem kötelező paramétereket szeretne megadni, hozzon létre [PartitionHealthQueryDescription](/dotnet/api/system.fabric.description.partitionhealthquerydescription).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
A partíció állapotának lekéréséhez szükséges parancsmag a [Get-ServiceFabricPartitionHealth](/powershell/module/servicefabric/get-servicefabricpartitionhealth). Először kapcsolódjon a fürthöz a [Kapcsolódás-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag használatával.

A következő parancsmag beolvassa a **Fabric:/WordCount/wordcountservice partícióinak listáját** szolgáltatás összes partíciójának állapotát, és kiszűri a replika állapotának állapotát:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 72
                        SentAt                : 7/13/2017 5:57:29 PM
                        ReceivedAt            : 7/13/2017 5:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/P RD _Node_2 Up 131444422260002646
                          N/S RD _Node_4 Up 131444422293113678
                          N/S RD _Node_3 Up 131444422293113679
                          N/S RD _Node_1 Up 131444422293118720
                          N/S RD _Node_0 Up 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5.)

                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 7/13/2017 5:57:48 PM, LastError = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131444445174851664
                        SentAt                : 7/13/2017 6:35:17 PM
                        ReceivedAt            : 7/13/2017 6:35:18 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A

                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None

                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/13/2017 5:57:48 PM, LastOk = 1/1/0001 12:00:00 AM

HealthStatistics      : 
                        Replica               : 5 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
Lekérheti a partíció állapotát egy [Get-kéréssel](/rest/api/servicefabric/sfclient-api-getpartitionhealth) vagy egy [post-kéréssel](/rest/api/servicefabric/get-the-health-of-a-partition-by-using-a-health-policy) , amely tartalmazza a törzsben ismertetett állapotházirend-szabályzatokat is.

## <a name="get-replica-health"></a>Replika állapotának beolvasása
Állapot-nyilvántartó szolgáltatás replikájának vagy állapot nélküli szolgáltatási példányának állapotát adja vissza. Bemenet:

* Szükséges A replikát azonosító partíció-azonosító (GUID) és replika-azonosító.
* Választható Az alkalmazás-jegyzékfájlra vonatkozó szabályzatok felülbírálásához használt alkalmazás-állapotfigyelő paraméterek.
* Választható Olyan események szűrése, amelyek meghatározzák, hogy mely bejegyzések érdeklik, és melyeket kell visszaadni az eredményben (például csak hibák, vagy a figyelmeztetések és a hibák). A rendszer minden eseményt felhasznál az entitás összesített állapotának kiértékelésére, a szűrőtől függetlenül.

### <a name="api"></a>API
A replika állapotának API-n keresztüli lekéréséhez hozzon létre egy, `FabricClient` és hívja meg a [GetReplicaHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getreplicahealthasync) metódust a HealthManager. A speciális paraméterek megadásához használja a [ReplicaHealthQueryDescription](/dotnet/api/system.fabric.description.replicahealthquerydescription).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
A replika állapotának beolvasására szolgáló parancsmag a [Get-ServiceFabricReplicaHealth](/powershell/module/servicefabric/get-servicefabricreplicahealth). Először kapcsolódjon a fürthöz a [Kapcsolódás-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag használatával.

A következő parancsmag lekéri az elsődleges replika állapotát a szolgáltatás összes partíciója esetében:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
A replika állapota Get- [kéréssel](/rest/api/servicefabric/get-the-health-of-a-replica) vagy [post-kéréssel](/rest/api/servicefabric/get-the-health-of-a-replica-by-using-a-health-policy) szerezhető be, amely tartalmazza a törzsben leírt állapot-szabályzatokat.

## <a name="get-deployed-application-health"></a>Üzembe helyezett alkalmazás állapotának beolvasása
Egy csomópont entitáson üzembe helyezett alkalmazás állapotát adja vissza. Tartalmazza a telepített szervizcsomag állapotait. Bemenet:

* Szükséges Az alkalmazás neve (URI) és a csomópont neve (karakterlánc), amely az üzembe helyezett alkalmazást azonosítja.
* Választható Az alkalmazás-jegyzékfájl-házirendek felülbírálására használt alkalmazás-állapotfigyelő házirend.
* Választható Olyan események és központilag telepített szervizcsomagok szűrése, amelyek meghatározzák, hogy mely bejegyzések érdeklik, és vissza kell őket adni az eredményben (például hibák, vagy a figyelmeztetések és a hibák). Az összes eseményt és központilag telepített szervizcsomagot a rendszer az entitás összesített állapotának kiértékelésére használja, a szűrőtől függetlenül.
* Választható Szűrő az állapotadatok kizárásához. Ha nincs megadva, az állapot statisztikái a telepített szervizcsomagok számát jelenítik meg az OK, a figyelmeztetés és a hiba állapotában.

### <a name="api"></a>API
Egy csomóponton az API-n keresztül telepített alkalmazások állapotának lekéréséhez hozzon létre egy, `FabricClient` és hívja meg a [GetDeployedApplicationHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync) metódust a HealthManager. A választható paraméterek megadásához használja a [DeployedApplicationHealthQueryDescription](/dotnet/api/system.fabric.description.deployedapplicationhealthquerydescription).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
Az üzembe helyezett alkalmazás állapotának beolvasására szolgáló parancsmag a [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth). Először kapcsolódjon a fürthöz a [Kapcsolódás-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag használatával. Ha szeretné megtudni, hogy az alkalmazás hol van telepítve, futtassa a [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth) , és tekintse meg az üzembe helyezett alkalmazások gyermekeit.

A következő parancsmag beolvassa a **_Node_2**-on üzembe helyezett **Fabric:/WordCount** alkalmazás állapotát.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_0


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_0
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_0
                                     AggregatedHealthState : Ok

HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131444422261848308
                                     SentAt                : 7/13/2017 5:57:06 PM
                                     ReceivedAt            : 7/13/2017 5:57:17 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/13/2017 5:57:17 PM, LastWarning = 1/1/0001 12:00:00 AM

HealthStatistics                   : 
                                     DeployedServicePackage : 2 Ok, 0 Warning, 0 Error
```

### <a name="rest"></a>REST
A üzembe helyezett alkalmazás állapota [Get-kéréssel](/rest/api/servicefabric/get-the-health-of-a-deployed-application) vagy [post-kéréssel](/rest/api/servicefabric/get-the-health-of-a-deployed-application-by-using-a-health-policy) szerezhető be, amely tartalmazza a törzsben ismertetett állapotházirend-szabályzatokat.

## <a name="get-deployed-service-package-health"></a>Üzembe helyezett szolgáltatáscsomag állapotának beolvasása
Egy üzembe helyezett szolgáltatáscsomag entitás állapotát adja vissza. Bemenet:

* Szükséges Az alkalmazás neve (URI), a csomópont neve (karakterlánc) és a szolgáltatás jegyzékfájljának neve (karakterlánc), amely a telepített szervizcsomagot azonosítja.
* Választható Az alkalmazás jegyzékfájl-házirendjének felülbírálására használt alkalmazás-állapotfigyelő házirend.
* Választható Olyan események szűrése, amelyek meghatározzák, hogy mely bejegyzések érdeklik, és melyeket kell visszaadni az eredményben (például csak hibák, vagy a figyelmeztetések és a hibák). A rendszer minden eseményt felhasznál az entitás összesített állapotának kiértékelésére, a szűrőtől függetlenül.

### <a name="api"></a>API
A telepített szervizcsomagok API-n keresztüli állapotának lekéréséhez hozzon létre egy, `FabricClient` és hívja meg a [GetDeployedServicePackageHealthAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync) metódust a saját HealthManager. A választható paraméterek megadásához használja a [DeployedServicePackageHealthQueryDescription](/dotnet/api/system.fabric.description.deployedservicepackagehealthquerydescription).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
Az üzembe helyezett szervizcsomag állapotának beolvasására szolgáló parancsmag a [Get-ServiceFabricDeployedServicePackageHealth](/powershell/module/servicefabric/get-servicefabricdeployedservicepackagehealth). Először kapcsolódjon a fürthöz a [Kapcsolódás-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag használatával. Ha szeretné megtekinteni, hogy az alkalmazás hol van telepítve, futtassa a [Get-ServiceFabricApplicationHealth](/powershell/module/servicefabric/get-servicefabricapplicationhealth) , és tekintse meg a telepített alkalmazásokat. Ha szeretné megtekinteni, hogy mely szervizcsomagok szerepelnek egy alkalmazásban, tekintse meg a [Get-ServiceFabricDeployedApplicationHealth](/powershell/module/servicefabric/get-servicefabricdeployedapplicationhealth) kimenetben található telepített szervizcsomagok gyermekeket.

A következő parancsmag beolvassa a **_Node_2**-on üzembe helyezett **Fabric:/WordCount** alkalmazás **WordCountServicePkg** szolgáltatási csomagjának állapotát. Az entitás rendelkezik **System. hosting** jelentésekkel a sikeres Service-Package és a belépési pont aktiválásához, valamint a sikeres szolgáltatás típusú regisztrációhoz.

```powershell
PS D:\ServiceFabric> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_2
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131444422267693359
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131444422267903345
                             SentAt                : 7/13/2017 5:57:06 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131444422272458374
                             SentAt                : 7/13/2017 5:57:07 PM
                             ReceivedAt            : 7/13/2017 5:57:18 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>REST
A központilag telepített szervizcsomag állapota [Get-kéréssel](/rest/api/servicefabric/get-the-health-of-a-service-package) vagy post- [kéréssel](/rest/api/servicefabric/get-the-health-of-a-service-package-by-using-a-health-policy) szerezhető be, amely tartalmazza a törzsben ismertetett állapotházirend-szabályzatokat.

## <a name="health-chunk-queries"></a>Állapotadatok lekérdezése
Az állapotadatok lekérdezései többszintű fürthöz tartozó gyermekeket (rekurzív módon) adhatnak vissza bemeneti szűrőkként. Olyan speciális szűrőket támogat, amelyek nagy rugalmasságot biztosítanak a visszaadott gyermekek kiválasztásában. A szűrők megadhatnak gyermekeket az egyedi azonosító vagy más csoportazonosító és/vagy állapotadatok alapján. Alapértelmezés szerint a rendszer nem tartalmaz gyermekeket, ellentétben az olyan állapot-parancsokkal, amelyek mindig tartalmazzák az első szintű gyermekeket.

Az [állapot-lekérdezések](service-fabric-view-entities-aggregated-health.md#health-queries) csak a megadott entitás első szintű gyermekeit adják vissza a szükséges szűrők alapján. A gyermekek gyermekeinek beszerzéséhez további Health API-kat kell meghívnia minden fontos entitáshoz. Hasonlóképpen, az egyes entitások állapotának lekéréséhez minden egyes kívánt entitáshoz meg kell hívnia egy Health API-t. A többrészes lekérdezés speciális szűrése lehetővé teszi, hogy több érdekes elemet kérjen egy lekérdezésben, minimalizálva az üzenetek méretét és számát.

Az adattömbök lekérdezésének értéke az, hogy az adott hívásban megadhatja, hogy a fürt több entitása (esetleg az összes, a szükséges gyökértől kezdődően a fürt entitása) állapotú legyen. Összetett állapot-lekérdezéseket is használhat, például:

* Csak a hibás alkalmazásokat adja vissza, és ezeknél az alkalmazásoknál figyelmeztetés vagy hiba esetén az összes szolgáltatás szerepel. A visszaadott szolgáltatások esetében adja meg az összes partíciót.
* Csak négy alkalmazás állapotát adja vissza, nevükben megadva.
* Csak a kívánt alkalmazás típusú alkalmazások állapotának visszaadása.
* Az összes telepített entitás visszaadása egy csomóponton. Az összes alkalmazást, a megadott csomóponton lévő összes telepített alkalmazást és a csomóponton lévő összes telepített szervizcsomagot adja vissza.
* A hiba összes replikájának visszaadása. Az összes alkalmazást, szolgáltatást, partíciót és csak replikát adja vissza hibásként.
* Minden alkalmazás visszaadása. Egy adott szolgáltatás esetében adja meg az összes partíciót.

Jelenleg az állapotadatok lekérdezése csak a fürt entitására van kitéve. Egy, a fürt állapotát tartalmazó adathalmazt ad vissza, amely a következőket tartalmazza:

* A fürt összesített állapotának állapota.
* A bemeneti szűrőket tiszteletben tartó csomópontok állapot-összedarabolási listája.
* A bemeneti szűrőket tiszteletben tartó alkalmazások állapotának adathalmazának listája. Minden egyes alkalmazás állapot-adathalmaz tartalmaz egy adathalmaz-listát a bemeneti szűrőket és a szűrőket tiszteletben tartó összes telepített alkalmazással rendelkező összes szolgáltatással. Ugyanez a szolgáltatások és az üzembe helyezett alkalmazások gyermekei esetében is. Így a fürtben lévő összes entitást, ha szükséges, hierarchikusan vissza lehet adni.

### <a name="cluster-health-chunk-query"></a>Fürt állapotának adathalmazának lekérdezése
A fürt entitásának állapotát adja vissza, és tartalmazza a szükséges gyermekek hierarchikus állapotának adathalmazait. Bemenet:

* Választható A csomópontok és a fürt eseményeinek kiértékeléséhez használt fürt állapotházirend.
* Választható Az alkalmazás állapot-szabályzatának leképezése az alkalmazás-jegyzékfájl-házirendek felülbírálására használt állapotfigyelő házirendekkel.
* Választható Azok a csomópontok és alkalmazások szűrői, amelyek meghatározzák, hogy mely bejegyzések érdeklik, és melyeket vissza kell adni az eredményben. A szűrők az entitások/csoportok csoportjaira vonatkoznak, vagy az adott szinten lévő összes entitásra érvényesek. A szűrők listája egy általános szűrőt és/vagy szűrőket tartalmazhat a lekérdezés által visszaadott, adott azonosítók esetében. Ha üres, a rendszer alapértelmezés szerint nem adja vissza a gyermekeket.
  További információ a szűrőkről: [NodeHealthStateFilter](/dotnet/api/system.fabric.health.nodehealthstatefilter) és [ApplicationHealthStateFilter](/dotnet/api/system.fabric.health.applicationhealthstatefilter). Az alkalmazás szűrőinek rekurzív módon adhatók meg speciális szűrők a gyermekekhez.

Az adattömb eredménye a szűrőket figyelembe vevő gyermekeket is tartalmazza.

Az adathalmaz-lekérdezés jelenleg nem ad vissza nem megfelelő állapotú értékeléseket vagy entitási eseményeket. A meglévő fürt állapot-lekérdezéssel további információk szerezhetők be.

### <a name="api"></a>API
A fürt állapotának lekéréséhez hozzon létre egy `FabricClient` és hívja meg a [GetClusterHealthChunkAsync](/dotnet/api/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync) metódust a **HealthManager**. Az [ClusterHealthQueryDescription](/dotnet/api/system.fabric.description.clusterhealthchunkquerydescription) átadhatja az Állapotházirendek és a speciális szűrők leírására.

A következő kód speciális szűrőkkel beolvassa a fürt állapotának adathalmazát.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
A fürt állapotának beolvasására szolgáló parancsmag a [Get-ServiceFabricClusterChunkHealth](/powershell/module/servicefabric/get-servicefabricclusterhealthchunk). Először kapcsolódjon a fürthöz a [Kapcsolódás-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag használatával.

A következő kód csak akkor kapja meg a csomópontokat, ha a hiba egy adott csomópont esetében, amelyet mindig vissza kell adni.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters


HealthState                  : Warning
NodeHealthStateChunks        : 
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

A következő parancsmag beolvassa a fürt adathalmazát az alkalmazás szűrőinek használatával.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks : 
                                TotalCount            : 1

                                ServiceName           : fabric:/WordCount/WordCountService
                                HealthState           : Error
                                PartitionHealthStateChunks : 
                                    TotalCount            : 1

                                    PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
                                    HealthState           : Error
                                    ReplicaHealthStateChunks : 
                                        TotalCount            : 5

                                        ReplicaOrInstanceId   : 131444422293118720
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293118721
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113678
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422293113679
                                        HealthState           : Ok

                                        ReplicaOrInstanceId   : 131444422260002646
                                        HealthState           : Error
```

A következő parancsmag az összes telepített entitást visszaadja egy csomóponton.

```xml
PS D:\ServiceFabric> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks : 
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : FAS
                                    ServicePackageActivationId : 
                                    HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks : 
                                TotalCount            : 1

                                NodeName              : _Node_2
                                HealthState           : Ok
                                DeployedServicePackageHealthStateChunks :
                                    TotalCount            : 1

                                    ServiceManifestName   : WordCountServicePkg
                                    ServicePackageActivationId : 
                                    HealthState           : Ok
```

### <a name="rest"></a>REST
Lekérheti a fürt állapotának részleteit egy [Get-kérelemmel](/rest/api/servicefabric/get-the-health-of-a-cluster-using-health-chunks) vagy egy [post-kéréssel](/rest/api/servicefabric/health-of-cluster) , amely tartalmazza a törzsben ismertetett állapotházirend-és speciális szűrőket is.

## <a name="general-queries"></a>Általános lekérdezések
Az általános lekérdezések egy adott típusú Service Fabric entitások listáját adják vissza. Ezek az API-n keresztül érhetők el (a **FabricClient. QueryManager** metódusokon keresztül), a PowerShell-parancsmagok és a REST használatával. Ezek a lekérdezések több összetevőből összesítik az allekérdezéseket. Ezek közül az egyik az a [Health Store](service-fabric-health-introduction.md#health-store), amely az egyes lekérdezési eredmények összesített állapotát tölti fel.  

> [!NOTE]
> Az általános lekérdezések az entitás összesített állapotát adják vissza, és nem tartalmaznak Rich Health adatokat. Ha egy entitás nem kifogástalan állapotú, nyomon követheti a Health-lekérdezéseket, és lekérheti az összes állapotadatokat, beleértve az eseményeket, a gyermek állapotot és a nem megfelelő állapotú értékeléseket.
>
>

Ha az általános lekérdezések ismeretlen állapotot adnak vissza egy entitáshoz, előfordulhat, hogy az állapotfigyelő nem rendelkezik teljes adattal az entitásról. Az is előfordulhat, hogy az állapotadatok allekérdezése nem volt sikeres (például kommunikációs hiba történt, vagy az állapotfigyelő eszköz szabályozása megtörtént). Kövesse az entitás állapot-lekérdezését. Ha az allekérdezés átmeneti hibákba ütközött, például hálózati problémákba ütközik, akkor ez a követő lekérdezés sikeres lehet. Emellett további részleteket is megtudhat az Health áruházból arról, hogy az entitás miért nem érhető el.

Az entitások **HealthState** tartalmazó lekérdezések a következők:

* Csomópontok listája: a fürt listájának csomópontjait adja vissza (lapozva).
  * API: [FabricClient. QueryClient. GetNodeListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getnodelistasync)
  * PowerShell: Get-ServiceFabricNode
* Alkalmazások listája: a fürtben található alkalmazások listáját adja vissza (lapozva).
  * API: [FabricClient. QueryClient. GetApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getapplicationlistasync)
  * PowerShell: Get-ServiceFabricApplication
* Szolgáltatások listája: egy alkalmazásban található szolgáltatások listáját adja vissza (lapozható).
  * API: [FabricClient. QueryClient. GetServiceListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getservicelistasync)
  * PowerShell: Get-ServiceFabricService
* Partíciók listája: egy szolgáltatásban található partíciók listáját adja vissza.
  * API: [FabricClient. QueryClient. GetPartitionListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getpartitionlistasync)
  * PowerShell: Get-ServiceFabricPartition
* Replika listája: egy partíció replikáinak listáját adja vissza (lapozható).
  * API: [FabricClient. QueryClient. GetReplicaListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getreplicalistasync)
  * PowerShell: Get-ServiceFabricReplica
* Telepített alkalmazások listája: a csomóponton telepített alkalmazások listáját adja vissza.
  * API: [FabricClient. QueryClient. GetDeployedApplicationListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication
* Központilag telepített szervizcsomagok listája: egy telepített alkalmazásban található szolgáltatási csomagok listáját adja vissza.
  * API: [FabricClient. QueryClient. GetDeployedServicePackageListAsync](/dotnet/api/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync)
  * PowerShell: Get-ServiceFabricDeployedApplication

> [!NOTE]
> Néhány lekérdezés lapozható eredményeket adott vissza. A lekérdezések visszaküldése a [PagedList \<T> ](/dotnet/api/system.fabric.query.pagedlist-1)származtatott lista. Ha az eredmények nem illeszkednek az üzenethez, csak egy oldal lesz visszaadva, és egy Continuationtoken argumentumot használja, amely nyomon követi az enumerálást. Folytassa ugyanezt a lekérdezést, és adja át a folytatási tokent az előző lekérdezésből a következő eredmények eléréséhez.

### <a name="examples"></a>Példák
A következő kód lekéri a nem kifogástalan állapotú alkalmazásokat a fürtben:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

A következő parancsmag beolvassa a Fabric:/WordCount alkalmazás alkalmazásának részleteit. Figyelje meg, hogy az állapot figyelmeztetéssel van elküldve.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

A következő parancsmag egy hiba állapotával szerzi be a szolgáltatásokat:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Error"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Error
```

## <a name="cluster-and-application-upgrades"></a>Fürtök és alkalmazások frissítése
A fürt és az alkalmazás figyelt frissítése során Service Fabric ellenőrzi az állapotot, hogy minden kifogástalan maradjon. Ha egy entitás nem kifogástalan állapotú, mivel a konfigurált állapotfigyelő házirendek használatával lett kiértékelve, a frissítés a következő művelet meghatározására alkalmazza a verziófrissítésre vonatkozó házirendeket. Előfordulhat, hogy a frissítés szüneteltetve van a felhasználói beavatkozás engedélyezése érdekében (például a hibák kijavítása vagy a házirendek módosítása), vagy pedig automatikusan visszatérhet az előző jó verzióra.

A *fürt* frissítése során lekérheti a fürt frissítési állapotát. A frissítési állapot tartalmazza a nem kifogástalan állapotú értékeléseket, amelyek a fürt sérült állapotára mutatnak. Ha a frissítés állapot-problémák miatt visszaáll vissza, a frissítés állapota az utolsó nem megfelelő állapotba kerül. Ezek az információk segíthetnek a rendszergazdáknak a frissítés visszavonása vagy leállítása után a hiba kivizsgálásában.

Hasonlóképpen, az *alkalmazások* frissítése során a rendszer a nem megfelelő állapotú értékeléseket az alkalmazás verziófrissítési állapotában tárolja.

A következő ábrán egy módosított háló alkalmazás-frissítési állapota látható:/WordCount alkalmazás. A watchdog hibát jelzett az egyik replikáján. A frissítés visszakerül, mert a rendszer nem veszi figyelembe az állapot-ellenőrzéseket.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2017 5:23:26 PM
FailureTimestampUtc           : 4/21/2017 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

További információ a [Service Fabric alkalmazás frissítéséről](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Az állapot-értékelések használata a hibák megoldásához
Ha probléma merül fel a fürttel vagy egy alkalmazással kapcsolatban, tekintse meg a fürt vagy az alkalmazás állapotát a probléma kijavításához. A nem megfelelő állapotú értékelések részletesen ismertetik, hogy az aktuálisan nem megfelelő állapotot váltott ki. Ha szükséges, a nem kifogástalan állapotú gyermek entitások részletezésével azonosíthatja a kiváltó okot.

Tegyük fel például, hogy egy alkalmazás nem megfelelő állapotú, mert az egyik replikáján hiba történt a jelentésben. A következő PowerShell-parancsmag megjeleníti a nem megfelelő állapotú értékeléseket:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricApplicationHealth fabric:/WordCount -EventsFilter None -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                    Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                    Unhealthy partition: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', AggregatedHealthState='Error'.

                                        Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                        Unhealthy replica: PartitionId='af2e3e44-a8f8-45ac-9f31-4093eb897600', ReplicaOrInstanceId='131444422260002646', AggregatedHealthState='Error'.

                                            Error event: SourceId='MyWatchdog', Property='Memory'.

ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : None
```

További információért tekintse meg a replikát:

```powershell
PS D:\ServiceFabric> Get-ServiceFabricReplicaHealth -ReplicaOrInstanceId 131444422260002646 -PartitionId af2e3e44-a8f8-45ac-9f31-4093eb897600


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422260002646
AggregatedHealthState : Error
UnhealthyEvaluations  : 
                        Error event: SourceId='MyWatchdog', Property='Memory'.

HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131444422263668344
                        SentAt                : 7/13/2017 5:57:06 PM
                        ReceivedAt            : 7/13/2017 5:57:18 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_2
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : MyWatchdog
                        Property              : Memory
                        HealthState           : Error
                        SequenceNumber        : 131444451657749403
                        SentAt                : 7/13/2017 6:46:05 PM
                        ReceivedAt            : 7/13/2017 6:46:05 PM
                        TTL                   : Infinite
                        Description           : 
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 7/13/2017 6:46:05 PM, LastOk = 1/1/0001 12:00:00 AM
```

> [!NOTE]
> A nem kifogástalan állapotú értékelések az entitás aktuális állapotra való kiértékelésének első okát mutatják be. Lehet, hogy több más esemény is aktiválhatja ezt az állapotot, de az értékelések nem tükrözik őket. További információkért tekintse meg az állapot entitásokat, hogy kiderítse a fürtben található összes nem megfelelő állapotú jelentést.
>
>

## <a name="next-steps"></a>Következő lépések
[Rendszerállapot-jelentések használata a hibaelhárítás során](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Egyéni Service Fabric állapotjelentés hozzáadása](service-fabric-report-health.md)

[A szolgáltatás állapotának jelentése és ellenõrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[A szolgáltatások helyi figyelése és diagnosztikája](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric alkalmazás frissítése](service-fabric-application-upgrade.md)
