---
title: Service Fabric-fürt figyelése az Azure-ban
description: Ebből az oktatóanyagból megtudhatja, hogyan figyelheti a fürtöket Service Fabric események megtekintésével, a EventStore API-k lekérdezésével, a teljesítményszámláló-számlálók figyelésével és az állapotadatok megtekintésével.
author: srrengar
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: ecd05a838425d57e0eaff2fa571d72b5a87e92a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791778"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>Oktatóanyag: Service Fabric-fürt figyelése az Azure-ban

A monitorozás és a diagnosztika kritikus fontosságú a számítási feladatok bármilyen felhőalapú környezetben való fejlesztéséhez, teszteléséhez és üzembe helyezéséhez. Ez az oktatóanyag egy sorozat második része, amely bemutatja, hogyan figyelheti és diagnosztizálhatja a Service Fabric-fürtöket események, teljesítményszámlálók és állapotjelentés használatával.   További információért olvassa el a [fürt figyelése](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) és az [infrastruktúra figyelése](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring)című témakört.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Service Fabric események megtekintése
> * EventStore API-k lekérdezése a fürt eseményeihez
> * Infrastruktúra figyelése/teljesítményszámláló-számlálók összegyűjtése
> * Fürt állapotával kapcsolatos jelentések megtekintése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Biztonságos Windows- [fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása az Azure-ban sablon használatával
> * Fürt figyelése
> * [Fürt horizontális fel- és leskálázása](service-fabric-tutorial-scale-cluster.md)
> * [Fürt futtatókörnyezetének frissítése](service-fabric-tutorial-upgrade-cluster.md)
> * [Fürt törlése](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Telepítse [Azure PowerShell](/powershell/azure/install-az-ps) vagy az [Azure CLI](/cli/azure/install-azure-cli)-t.
* Biztonságos Windows- [fürt](service-fabric-tutorial-create-vnet-and-windows-cluster.md) létrehozása 
* A fürt telepítési [diagnosztikai gyűjteménye](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor)
* A [EventStore szolgáltatás](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor) engedélyezése a fürtben
* [Azure monitor naplók és a fürt log Analytics ügynökének](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor) konfigurálása

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Service Fabric események megtekintése Azure Monitor naplók használatával

Azure Monitor naplók gyűjti és elemzi a felhőben üzemeltetett alkalmazások és szolgáltatások telemetria, és elemzési eszközöket biztosít a rendelkezésre állás és a teljesítmény maximalizálása érdekében. Azure Monitor naplókban lekérdezéseket futtathat, hogy betekintést nyerjen, és hárítsa el, mi történik a fürtben.

A Service Fabric Analytics megoldás eléréséhez nyissa meg a [Azure Portal](https://portal.azure.com) , és válassza ki azt az erőforráscsoportot, amelyben létrehozta a Service Fabric Analytics megoldást.

Válassza ki az erőforrás **-ServiceFabric (mysfomsworkspace)**.

Az **Áttekintés** lapon láthatók a csempék egy gráf formájában az összes engedélyezett megoldáshoz, beleértve az egyiket a Service Fabric. A Service Fabric Analytics megoldás folytatásához kattintson a **Service Fabric** gráfra.

![A Service Fabric gráfot bemutató képernyőkép.](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

Az alábbi képen a Service Fabric Analytics-megoldás kezdőlapja látható. Ez a Kezdőlap pillanatkép-nézetet jelenít meg a fürtben zajló eseményekről.

![Service Fabric megoldás](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 Ha a fürt létrehozásakor engedélyezte a diagnosztika szolgáltatást, a következő eseményeket tekintheti meg: 

* [Service Fabric-fürt eseményei](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors programozási modell eseményei](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services programozási modell eseményei](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>A Service Fabric események mellett a [diagnosztikai bővítmény konfigurációjának frissítésével](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)részletesebb rendszereseményeket is gyűjthet.

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>Service Fabric események megtekintése, beleértve a csomópontokon végrehajtott műveleteket is

A Service Fabric Analytics lapon kattintson a **fürt eseményeinek** gráfra.  Megjelenik az összes összegyűjtött rendszeresemény naplója. Ezek az Azure Storage-fiók **WADServiceFabricSystemEventsTable** származnak, és hasonlóképpen a megbízható szolgáltatások és Actors-események, amelyek a következőket látják, a megfelelő táblákból származnak.
    
![Működési csatorna lekérdezése](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

A lekérdezés a Kusto lekérdezési nyelvét használja, amelyet módosíthatja, hogy pontosítsa, amit keres. A fürt csomópontjain végrehajtott összes művelet megkereséséhez például a következő lekérdezést használhatja. Az alább használt eseményazonosító az [operatív csatorna eseményeinek referenciájában](service-fabric-diagnostics-event-generation-operational.md)található.

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

A Kusto lekérdezési nyelve nagy teljesítményű. Íme néhány hasznos lekérdezés.

*ServiceFabricEvent* keresési táblázat létrehozása felhasználó által definiált függvényként a lekérdezés ServiceFabricEvent aliasként való mentésével:

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

Az elmúlt órában rögzített operatív események visszaadása:
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

Működési események visszaadása a következővel: Napszállta = = 18604 és EventName = = ' NodeDownOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

Működési események visszaadása a következővel: Napszállta = = 18604 és EventName = = ' NodeUpOperational ':
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 

Az HealthState = = 3 (hiba) állapotú állapotjelentést adja vissza, és további tulajdonságokat is Kinyer a EventMessage mezőből:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

Események idődiagramjának visszaadása a Napszállta! = 17523:

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

Az adott szolgáltatással és csomóponttal összesített Service Fabric működési események beszerzése:

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

Service Fabric események számának megjelenítése Napszállta/EventName használatával egy több erőforrást használó lekérdezéssel:

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Service Fabric alkalmazás eseményeinek megtekintése

Megtekintheti a fürtön üzembe helyezett megbízható szolgáltatások és megbízható Actors-alkalmazások eseményeit.  A Service Fabric Analytics lapon kattintson az **alkalmazás eseményeinek** gráfra.

Az alábbi lekérdezés futtatásával tekintheti meg a megbízható szolgáltatások alkalmazásaiból származó eseményeket:
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

A szolgáltatás `runasync` elindításakor és befejezésekor különböző eseményeket tekinthet meg, amelyek általában az üzembe helyezéseken és a frissítéseken történnek.

![Service Fabric megoldás Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

A megbízható szolgáltatáshoz tartozó eseményeket a szolgáltatásnév = = "Fabric:/Watchdog/WatchdogService" használatával is megtalálhatja:

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
A megbízható színészek eseményei hasonló módon tekinthetők meg:

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
A megbízható szereplők részletes eseményeinek konfigurálásához a `scheduledTransferKeywordFilter` fürt sablonjában módosíthatja a diagnosztikai bővítmény konfigurációját. Ezeknek az értékeknek a részletei a [megbízható szereplőkkel kapcsolatos események hivatkozásában](service-fabric-reliable-actors-diagnostics.md#keywords)találhatók.

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Teljesítményszámlálók megtekintése Azure Monitor naplókkal
A teljesítményszámlálók megjelenítéséhez nyissa meg a [Azure Portal](https://portal.azure.com) és az erőforráscsoportot, amelyben létrehozta a Service Fabric Analytics megoldást. 

Válassza ki az erőforrás- **ServiceFabric (mysfomsworkspace)**, majd **log Analytics munkaterületet**, majd a **speciális beállításokat**.

Kattintson **az adat**, majd a **Windows-teljesítményszámlálók** elemre. Az alapértelmezett számlálók listája lehetővé teszi az engedélyezést, és beállíthatja a gyűjtemény intervallumát is. [További teljesítményszámlálókat](service-fabric-diagnostics-event-generation-perf.md) is hozzáadhat a gyűjtéshez. Ennek a [cikknek](/windows/desktop/PerfCtrs/specifying-a-counter-path)a megfelelő formátumra hivatkozik. Kattintson a **Mentés**, majd **az OK** gombra.

A speciális beállítások panel bezárásához és az **általános** fejléc alatt válassza ki a **munkaterület összegzése** elemet. Minden olyan megoldás esetében, amelynél engedélyezve van egy grafikus csempe, például egy Service Fabric. A Service Fabric Analytics megoldás folytatásához kattintson a **Service Fabric** gráfra.

Az operatív csatorna és a megbízható szolgáltatások eseményeihez grafikus csempék tartoznak. A kiválasztott számlálóhoz tartozó adatok grafikus ábrázolása a **csomópont metrikái** alatt fog megjelenni. 

A további részletek megtekintéséhez válassza a **Container metrika** diagramot. A teljesítményszámláló adataihoz hasonlóan is lekérdezheti a fürt eseményeinek adatait, és szűrheti a csomópontok, a teljesítményszámláló nevét és az értékeket a Kusto lekérdezési nyelv használatával.

## <a name="query-the-eventstore-service"></a>A EventStore szolgáltatás lekérdezése
A [EventStore szolgáltatás](service-fabric-diagnostics-eventstore.md) lehetővé teszi a fürt vagy a számítási feladatok állapotának megismerését egy adott időpontban. A EventStore olyan állapot-nyilvántartó Service Fabric szolgáltatás, amely az eseményeket a fürtből tartja karban. Az események a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), a REST és az API-kon keresztül érhetők el. A EventStore lekérdezi a fürtöt közvetlenül a fürt bármely entitásán található diagnosztikai adat beszerzéséhez, hogy megtekintse a EventStore elérhető események teljes listáját. lásd: [események Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

A EventStore API-kat programozott módon lehet lekérdezni az [Service Fabric ügyféloldali kódtár](/dotnet/api/overview/azure/service-fabric#client-library)használatával.

Íme egy példa a 2018-04-03T18:00:00Z és 2018-04-04T18:00:00Z közötti összes fürt eseményeire a GetClusterEventListAsync függvény használatával.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

Íme egy másik példa, amely lekérdezi a fürt állapotát és az összes Node eseményt a 2018 szeptemberében, és kinyomtatja őket.

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>A fürt állapotának figyelése
A Service Fabric olyan állapot- [modelleket](service-fabric-health-introduction.md) vezet be, amelyekben a rendszerösszetevők és a watchdogok jelenthetik a figyelt helyi feltételeket. Az állapotfigyelő az összes [állapotadatok összesítésével](service-fabric-health-introduction.md#health-store) megállapítja, hogy az entitások állapota Kifogástalan-e.

A rendszer automatikusan kitölti a fürtöt a rendszerösszetevők által elküldett állapot-jelentésekkel. További információ: [rendszerállapot-jelentések használata a hibakereséshez](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

A Service Fabric az egyes támogatott [entitások típusaihoz](service-fabric-health-introduction.md#health-entities-and-hierarchy)tartozó állapot-lekérdezéseket teszi elérhetővé. Ezek az API-n keresztül érhetők el, a [FabricClient. HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager), a PowerShell-parancsmagok és a REST metódusok használatával. Ezek a lekérdezések a teljes állapotra vonatkozó információkat adják vissza az entitásról: az összesített állapot, az entitás állapotával kapcsolatos események, a gyermek állapot állapota (ha alkalmazható), a nem kifogástalan állapotú értékelések (ha az entitás nem kifogástalan) és a gyermek állapot statisztikája (ha alkalmazható).

### <a name="get-cluster-health"></a>Fürt állapotának beolvasása
A [Get-ServiceFabricClusterHealth parancsmag](/powershell/module/servicefabric/get-servicefabricclusterhealth) a fürt entitásának állapotát adja vissza, és tartalmazza az alkalmazások és a csomópontok állapotát (a fürt gyermekei).  Először kapcsolódjon a fürthöz a [Kapcsolódás-ServiceFabricCluster parancsmag](/powershell/module/servicefabric/connect-servicefabriccluster)használatával.

A fürt állapota 11 csomópont, a Rendszeralkalmazás és a háló:/szavazás konfigurálva a leírtak szerint.

Az alábbi példa az alapértelmezett állapotházirendek használatával szerzi be a fürt állapotát. A 11 csomópont kifogástalan állapotú, de a fürt összesített állapota hiba, mert a Fabric:/szavazó alkalmazás hibát jelzett. Vegye figyelembe, hogy a nem kifogástalan állapotú értékelések részletesen ismertetik az összesített állapotot kiváltó feltételek részleteit.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

A következő példa egy egyéni alkalmazás-házirend használatával lekéri a fürt állapotát. Az eredményeket úgy szűri, hogy csak az alkalmazásokat és a csomópontokat kapja meg hiba vagy figyelmeztetés alapján. Ebben a példában a rendszer nem ad vissza csomópontokat, mivel azok kifogástalanok. Csak a Fabric:/szavazó alkalmazás veszi figyelembe az alkalmazások szűrőt. Mivel az egyéni házirend azt határozza meg, hogy a figyelmeztetések a háló:/szavazó alkalmazás hibáiként legyenek kiértékelve, a rendszer hibát jelez, és így a fürt.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>Csomópont állapotának beolvasása
A [Get-ServiceFabricNodeHealth parancsmag](/powershell/module/servicefabric/get-servicefabricnodehealth) egy csomópont entitás állapotát adja vissza, és tartalmazza a csomóponton jelentett állapotú eseményeket. Először kapcsolódjon a fürthöz a [Kapcsolódás-ServiceFabricCluster parancsmag](/powershell/module/servicefabric/connect-servicefabriccluster)használatával. Az alábbi példa egy adott csomópont állapotát kéri le az alapértelmezett állapot-házirendek használatával:

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

A következő példa a fürt összes csomópontjának állapotát lekéri:
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>Rendszerszolgáltatás állapotának beolvasása 

A rendszerszolgáltatások összesített állapotának beolvasása:

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Service Fabric események megtekintése
> * EventStore API-k lekérdezése a fürt eseményeihez
> * Infrastruktúra figyelése/teljesítményszámláló-számlálók összegyűjtése
> * Fürt állapotával kapcsolatos jelentések megtekintése

Ezután folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan méretezheti a fürtöt.
> [!div class="nextstepaction"]
> [Fürt skálázása](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
