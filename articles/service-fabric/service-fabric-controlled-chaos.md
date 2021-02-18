---
title: A káosz indukálása Service Fabric-fürtökben
description: A hibák befecskendezése és a cluster Analysis Service API-k használatával kezelheti a káoszt a fürtben.
ms.topic: conceptual
ms.date: 02/05/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ff3c88b7f86294badb76ec1e8dde0c34b942509
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588988"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Vezérelt káosz indukálása Service Fabric-fürtökben
A nagy léptékű elosztott rendszerek, például a felhőalapú infrastruktúrák eleve megbízhatatlanok. Az Azure Service Fabric lehetővé teszi a fejlesztők számára, hogy megbízható elosztott szolgáltatásokat írjanak egy megbízhatatlan infrastruktúrán felül. Ahhoz, hogy megbízható, megbízható infrastruktúrán alapuló elosztott szolgáltatásokat lehessen írni, a fejlesztőknek képesnek kell lenniük a szolgáltatásaik stabilitásának tesztelésére, miközben a mögöttes megbízhatatlan infrastruktúra a hibák miatt bonyolult állapotú átmeneteken megy keresztül.

A [hibák befecskendezése és a fürt Analysis Service](./service-fabric-testability-overview.md) (más néven a fault Analysis Service) lehetővé teszi a fejlesztők számára, hogy a szolgáltatásaik teszteléséhez hibákat idézzenek. Ezek a meghívott szimulált hibák, például a [partíciók újraindítása](/powershell/module/servicefabric/start-servicefabricpartitionrestart), segíthetnek a leggyakoribb állapot-váltások végrehajtásában. A megfogalmazott szimulált hibák azonban a definíciók szerint torzulnak, így a hibák kimaradnak, amelyek csak nehezen megjósolható, hosszú és bonyolult állapotú átmeneteket jeleznek. Elfogulatlan teszteléshez használhatja a káoszt.

A káosz szimulálja az időszakos, összekapcsolt hibákat (mind a kecses, mind a szégyenletes) a fürtben a hosszabb időtartamon belül. Egy kecses hiba Service Fabric API-hívásokból áll, például az újraindítási replika hibája egy kecses hiba, amelyet egy replikán nyitott meg. Távolítsa el a replikát, helyezze át az elsődleges replikát, és helyezze át a másodlagos replikát a káosz által gyakorolt egyéb kecses hibákra. A hibás hibák a folyamat bezárása, például a csomópont újraindítása és a Code csomag újraindítása. 

Miután beállította a káoszt a ráta és a hibák típusával, elindíthatja a káoszt a C#, a PowerShell vagy a REST API használatával, és megkezdheti a hibák létrehozását a fürtben és a szolgáltatásaiban. Beállíthatja, hogy a káosz egy adott időszakra fusson (például egy órára), amely után a káosz automatikusan leáll, vagy meghívhatja a StopChaos API-t (C#, PowerShell vagy REST), így bármikor leállíthatja.

> [!NOTE]
> A Chaos jelenleg csak biztonságos hibákat idéz elő, ami azt jelenti, hogy a külső hibák hiányában a kvórum elvesztése vagy az adatvesztés soha nem fordul elő.
>

Amíg a káosz fut, különböző eseményeket hoz létre, amelyek rögzítik a Futtatás állapotát. Például egy ExecutingFaultsEvent tartalmazza az összes olyan hibát, amelyet a káosz úgy döntött, hogy az adott iterációban végrehajtja a végrehajtást. A ValidationFailedEvent a fürt érvényesítése során talált érvényesítési hibák (állapot vagy stabilitási problémák) részleteit tartalmazza. A GetChaosReport API (C#, PowerShell vagy REST) meghívásával lekérheti a káosz futtatásának jelentését. Ezek az események egy [megbízható szótárban](./service-fabric-reliable-services-reliable-collections.md)maradnak, amely a következő két konfiguráció által diktált csonkolt házirendtel rendelkezik: **MaxStoredChaosEventCount** (az alapértelmezett érték 25000) és a **StoredActionCleanupIntervalInSeconds** (az alapértelmezett érték a 3600). Minden *StoredActionCleanupIntervalInSeconds* , de a legutóbbi *MaxStoredChaosEventCount* -események törlődnek a megbízható szótárból.

## <a name="faults-induced-in-chaos"></a>A káoszban kiváltott hibák
A Chaos a teljes Service Fabric fürt hibáit állítja elő, és a hónapokban vagy években megtekintett hibákat néhány órára tömöríti. A magas hibatűrési aránnyal rendelkező, egymással megszakított hibák kombinációja olyan sarki eseteket talál, amelyek egyébként kihagyhatók. A káosz gyakorlása a szolgáltatás programkód-minőségének jelentős javulását eredményezi.

A Chaos a következő kategóriákból származó hibákat idézi elő:

* Csomópont újraindítása
* Telepített kód újraindítása
* Replika eltávolítása
* Replika újraindítása
* Elsődleges replika áthelyezése (konfigurálható)
* Másodlagos replika áthelyezése (konfigurálható)

A káosz több iterációban fut. Minden iteráció a megadott időszakban hibákból és fürt-ellenőrzésből áll. Beállíthatja, hogy a fürt milyen ideig legyen stabilizálva, és az érvényesítés sikeres legyen. Ha a fürt érvényesítése során hiba található, a Chaos létrehoz és megtart egy ValidationFailedEvent az UTC időbélyeggel és a hiba részleteivel. Tegyük fel például, hogy a káosz egy olyan példánya, amely egy órára van beállítva, és legfeljebb három egyidejű hibával rendelkezik. A Chaos három hibát idéz elő, majd ellenőrzi a fürt állapotát. Az előző lépéssel közelíti meg, amíg a StopChaosAsync API-n vagy egy órás továbbításon keresztül explicit módon nem áll le. Ha a fürt sérült állapotba kerül bármilyen iterációban (azaz nem stabilizálódik, vagy nem lesz kifogástalan az átadott MaxClusterStabilizationTimeout belül), a Chaos létrehoz egy ValidationFailedEvent. Ez az esemény azt jelzi, hogy valamilyen hiba történt, és további vizsgálatra lehet szükség.

A GetChaosReport API-t (PowerShell, C# vagy REST) használva megtekintheti, hogy milyen hibákat okoz a káosz. Az API beolvassa a Chaos-jelentés következő szegmensét az átadott folytatási jogkivonat vagy az átadott időtartomány alapján. Megadhatja a Continuationtoken argumentumot használja a Chaos-jelentés következő szegmensének beszerzéséhez, vagy megadhatja az időtartományt a StartTimeUtc és a EndTimeUtc, de nem adhatja meg egyszerre a Continuationtoken argumentumot használja és az időtartományt is ugyanabban a hívásban. Ha több mint 100 Chaos-eseményt talál, a rendszer a Chaos-jelentést olyan szegmensekben adja vissza, amelyekben a szegmensek nem tartalmaznak több mint 100 Chaos-eseményt.

## <a name="important-configuration-options"></a>Fontos konfigurációs beállítások
* **TimeToRun**: a káosz futásának teljes ideje, mielőtt a művelet sikeresen befejeződik. Leállíthatja a káoszt, mielőtt a TimeToRun időszakra futtatná a StopChaos API-n keresztül.

* **MaxClusterStabilizationTimeout**: az a maximális időtartam, ameddig a fürt Kifogástalan állapotba kerül, mielőtt ValidationFailedEvent hozna létre. Ez a várakozás a fürt terhelésének csökkentése a helyreállítás közben. A végrehajtott ellenőrzések a következők:
  * Ha a fürt állapota rendben van
  * Ha a szolgáltatás állapota rendben van
  * Ha a megcélzott másodpéldány-készlet mérete elérhető a szolgáltatás partíciójában
  * Nem léteznek inbuild replikák
* **Timestamputcinticks**: az egyes iterációk által kiváltott egyidejű hibák maximális száma. Minél nagyobb a szám, annál agresszívebb a káosz, és a feladatátvétel és a fürt által áthaladó állapot-átmenet kombinációk is összetettebbek. 

> [!NOTE]
> Függetlenül attól, hogy milyen magas a *timestamputcinticks* érték, a káosz garantálja – a külső hibák hiányában – nincs kvórum elvesztése vagy adatvesztés.
>

* **EnableMoveReplicaFaults**: engedélyezheti vagy letilthatja azokat a hibákat, amelyek az elsődleges vagy másodlagos replikák áthelyezését okozzák. Ezek a hibák alapértelmezés szerint engedélyezve vannak.
* **WaitTimeBetweenIterations**: az iterációk közötti várakozási idő. Ez azt az időtartamot mutatja, ameddig a káosz szünetel, miután elvégezte a hibák körét, és befejezte a fürt állapotának megfelelő érvényesítését. Minél nagyobb az érték, annál alacsonyabb az átlagos hibák befecskendezési sebessége.
* **WaitTimeBetweenFaults**: a két egymást követő hiba közötti várakozási idő egyetlen iterációban. Minél nagyobb az érték, annál alacsonyabb a (vagy átfedésben lévő) hibák párhuzamossága.
* **ClusterHealthPolicy**: a fürt állapotára vonatkozó házirend használatával ellenőrizhető a fürt állapota a Chaos iterációk között. Ha a fürt állapota hibát jelez, vagy ha váratlan kivétel történik a hibák végrehajtása során, akkor a Chaos a következő állapot-ellenőrzés előtt 30 perccel megvárja, hogy a fürt egy kis idő elteltével bekerüljön a helyreállításra.
* **Környezet**: (karakterlánc, karakterlánc) típusú kulcs-érték párokat tartalmazó gyűjtemény. A Térkép használatával rögzítheti a káosz futtatásával kapcsolatos információkat. Nem lehet több mint 100 ilyen pár, és mindegyik sztring (kulcs vagy érték) legfeljebb 4095 karakter hosszúságú lehet. Ezt a leképezést a Chaos Run indítója állítja be, hogy opcionálisan tárolja a környezetet az adott futtatásról.
* **ChaosTargetFilter**: Ez a szűrő felhasználható arra, hogy a Chaos-hibákat csak bizonyos csomópont-típusokra, vagy csak bizonyos alkalmazási példányokra célozza. Ha a ChaosTargetFilter nincs használatban, a Chaos hibát jelzett a fürt összes entitása esetében. Ha ChaosTargetFilter használ, a Chaos csak azokat az entitásokat használja, amelyek megfelelnek a ChaosTargetFilter specifikációjának. A Chaostargetfilter és a ApplicationInclusionList csak a Union szemantikai használatát teszi lehetővé. Más szóval nem lehet megadni a Chaostargetfilter és a ApplicationInclusionList metszetét. Például nem adható meg "az alkalmazás hibája csak akkor, ha az adott csomópont típusa van." Ha egy entitást a Chaostargetfilter vagy a ApplicationInclusionList tartalmaz, az entitás nem zárható ki a ChaosTargetFilter használatával. Ha a applicationX nem jelenik meg a ApplicationInclusionList-ben, néhány Chaos iterációs applicationX hibát okozhat, mert az a Chaostargetfilter részét képező nodeTypeY csomópontján történik. Ha a Chaostargetfilter és a ApplicationInclusionList egyaránt null értékű vagy üres, akkor egy ArgumentException kerül.
    * **Chaostargetfilter**: a Chaos-hibákba foglalni kívánt csomópont-típusok listája. Minden típusú hiba (csomópont újraindítása, codepackage újraindítása, replika eltávolítása, replika újraindítása, elsődleges áthelyezése és másodlagos áthelyezés) engedélyezve van a csomópontok csomópontjain. Ha a NodeType (NodeTypeX) nem jelenik meg a Chaostargetfilter, akkor a csomóponti szintű hibák (például a NodeRestart) nem lesznek engedélyezve a NodeTypeX csomópontjain, de a kód-és a replika-hibák továbbra is engedélyezhetők a NodeTypeX, ha a ApplicationInclusionList egyik alkalmazása a NodeTypeX csomópontján történik. Ezen a listán legfeljebb 100 csomópont típusú név szerepelhet, így a szám növeléséhez a MaxNumberOfNodeTypesInChaosTargetFilter konfigurálásához szükség van egy konfigurációs frissítésre.
    * **ApplicationInclusionList**: a Chaos-hibákba foglalandó alkalmazás-URI azonosítók listája. Az alkalmazások szolgáltatásaihoz tartozó összes replika a replika hibáira (a replika újraindítására, a replika eltávolítására, az elsődleges áthelyezésre és a másodlagos áthelyezésre) irányul. A káosz csak akkor indítható el, ha a kód csak az alkalmazások replikáit tárolja. Ha egy alkalmazás nem jelenik meg ezen a listán, akkor is hibás lehet az egyes Chaos-iterációkban, ha az alkalmazás egy csomópont típusú csomóponton végződik, amely a Chaostargetfilter része. Ha azonban a applicationX az elhelyezési korlátozásokon keresztül nodeTypeY, és a applicationX hiányzik a ApplicationInclusionList, és a nodeTypeY hiányzik a chaostargetfilter, akkor a applicationX soha nem fog hibát okozni. Ebben a listában legfeljebb 1000 alkalmazás neve szerepelhet a MaxNumberOfApplicationsInChaosTargetFilter-konfigurációhoz szükséges konfigurációk frissítéséhez.

## <a name="how-to-run-chaos"></a>A káosz futtatása

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster
            // entities. 
            // During validation if a cluster entity is not stable and healthy within
            // MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex
            // series of states to uncover bugs.
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be
            // used to record information about the Chaos run. There cannot be more than 100 such pairs and
            // each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the
            // lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration.
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of
            // state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica,
            // and move secondary replica will happen for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList,
            // restart code package, restart replica, move primary replica, move secondary replica faults will
            // happen for 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing
            // on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy.
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities.
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds,
# Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration.
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase.
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of
# states to uncover bugs.
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the
# fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration.
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state
# transitions that the cluster goes through.
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record
# information about the Chaos run.
# There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move
# secondary replica will happen for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for
# 'fabric:/TestApp2' even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is
# not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
