---
title: Chaos-és feladatátvételi tesztek létrehozása az Azure Service Fabric
description: A Service Fabric Chaos test és a feladatátvételi teszt forgatókönyvek használatával a hibák kiváltása és a szolgáltatások megbízhatóságának ellenőrzése.
ms.topic: conceptual
ms.date: 10/1/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 03df86c5ee27815679d72983e95f40ec7e79a11e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105728232"
---
# <a name="testability-scenarios"></a>Tesztelési forgatókönyvek
A nagyméretű elosztott rendszerek, például a felhőalapú infrastruktúrák eleve megbízhatatlanok. Az Azure Service Fabric lehetővé teszi a fejlesztők számára, hogy a megbízható infrastruktúrák alapján futtassák a szolgáltatásokat. A magas színvonalú szolgáltatások írásához a fejlesztőknek képesnek kell lenniük az ilyen megbízhatatlan infrastruktúra kiváltására a szolgáltatásaik stabilitásának teszteléséhez.

A hiba-elemzési szolgáltatás lehetővé teszi a fejlesztők számára, hogy a hibák észlelése érdekében a szolgáltatások tesztelésére képesek legyenek. A megcélzó szimulált hibák azonban csak eddig kapnak. A teszteléshez használhatja a Service Fabric: a Chaos-tesztet és a feladatátvételi tesztet. Ezek a forgatókönyvek a folyamatos és a zökkenőmentes működést szimulálják, és a fürt teljes egészében, hosszabb ideig. Ha egy tesztet a hibák arányával és típusával konfiguráltak, akkor a C# API-kon vagy a PowerShellen keresztül is elindítható, hogy hibákat lehessen előállítani a fürtben és a szolgáltatásban.

> [!WARNING]
> A ChaosTestScenario egy rugalmasabb, szolgáltatás-alapú káosz váltja fel. További részletekért tekintse meg az új, [szabályozott káoszt](service-fabric-controlled-chaos.md) ismertető cikket.
> 
> 

## <a name="chaos-test"></a>Chaos-teszt
A Chaos-forgatókönyv hibákat generál a teljes Service Fabric fürtön. A forgatókönyvek általában hónapok vagy évek szerint, néhány óráig tömörítik a hibákat. A magas hibatűrési aránnyal rendelkező, egymással megszakított hibák kombinációja megkeresi azokat a sarokban lévő eseteket, amelyek egyébként kimaradtak. Ez jelentős javulást eredményez a szolgáltatás programkódjának minőségében.

### <a name="faults-simulated-in-the-chaos-test"></a>A Chaos-tesztben szimulált hibák
* Csomópont újraindítása
* Telepített kód újraindítása
* Replika eltávolítása
* Replika újraindítása
* Elsődleges replika áthelyezése (nem kötelező)
* Másodlagos replika áthelyezése (nem kötelező)

A Chaos-teszt a hibák és a fürt érvényességi idejének több ismétlését futtatja a megadott időszakban. A fürt stabilizálására fordított idő, valamint az érvényesítés sikeressége is konfigurálható. A forgatókönyv meghiúsul, ha egyetlen hibát észlel a fürt érvényesítése során.

Vegyünk például egy olyan tesztet, amely egy órára van beállítva, és legfeljebb három egyidejű hibát hajt végre. A teszt három hibát fog kiváltani, majd ellenőrzi a fürt állapotát. A teszt megismétli az előző lépést, amíg a fürt nem Kifogástalan állapotba kerül, vagy egy óra halad át. Ha a fürt nem Kifogástalan állapotba kerül bármilyen iterációban, azaz a beállított időn belül nem stabilizálódik, a teszt kivételt jelez. Ez a kivétel azt jelzi, hogy valamilyen hiba történt, és további vizsgálatot igényel.

Az aktuális formában a Chaos-tesztben a hiba-előállítási motor csak a biztonságos hibákat okozta. Ez azt jelenti, hogy a külső hibák hiányában a kvórum vagy az adatvesztés soha nem fog történni.

### <a name="important-configuration-options"></a>Fontos konfigurációs beállítások
* **TimeToRun**: a tesztelés befejezésének teljes ideje, mielőtt a művelet sikeres lesz. A teszt egy érvényesítési hiba helyett korábban is befejeződik.
* **MaxClusterStabilizationTimeout**: maximális időtartam, ameddig a fürt Kifogástalan állapotba kerül, mielőtt a teszt sikertelen lesz. A végrehajtott ellenőrzések azt jelzik, hogy a fürt állapota OK-e, a szolgáltatás állapota OK, a megcélzott másodpéldány-készlet mérete a szolgáltatás partíciója számára elérhető, és nem léteznek beépített replikák.
* **Timestamputcinticks**: az egyes iterációk által kiváltott egyidejű hibák maximális száma. Minél nagyobb a szám, annál agresszívebb a teszt, így összetettebb feladatátvételeket és áttűnési kombinációkat eredményez. A teszt garantálja, hogy a külső hibák hiányában nem lesz kvórum vagy adatvesztés, függetlenül attól, hogy milyen magas a konfiguráció.
* **EnableMoveReplicaFaults**: engedélyezheti vagy letilthatja azokat a hibákat, amelyek az elsődleges vagy másodlagos replikák áthelyezését okozzák. Ezek a hibák alapértelmezés szerint le vannak tiltva.
* **WaitTimeBetweenIterations**: az iterációk közötti várakozási idő, azaz a hibák kerekítése és a megfelelő érvényesítés.

### <a name="how-to-run-the-chaos-test"></a>A Chaos-teszt futtatása
C#-minta

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

A Service Fabric PowerShell-modul két módszert tartalmaz a Chaos-forgatókönyvek megkezdésére. `Invoke-ServiceFabricChaosTestScenario` ügyfél-alapú, és ha az ügyfélszámítógép leállítása a teszten keresztül történik, további hibák nem lesznek bevezetve. Azt is megteheti, hogy a teszt futtatása a gép leállításakor is megmarad. `Start-ServiceFabricChaos` egy FaultAnalysisService nevű állapot-nyilvántartó és megbízható rendszerszolgáltatást használ, amely biztosítja, hogy a hibák addig maradnak érvényben, amíg a TimeToRun be nem fejeződik. `Stop-ServiceFabricChaos` felhasználható a forgatókönyv manuális leállítására és `Get-ServiceFabricChaosReport` egy jelentés beszerzésére is. További információkért tekintse meg az [Azure Service Fabric PowerShell-referenciát](/powershell/module/ServiceFabric/New-ServiceFabricService?preserve-view=true&view=azureservicefabricps) , és [a Service Fabric-fürtökben lévő szabályozott káoszt](service-fabric-controlled-chaos.md).

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>Feladatátvételi teszt
A feladatátvételi teszt forgatókönyv az a káosz-tesztelési forgatókönyv verziója, amely egy adott szolgáltatási partíciót céloz meg. Ellenőrzi a feladatátvétel hatását egy adott szolgáltatási partíción, miközben a többi szolgáltatást nem érinti. Miután konfigurálta a megcélzott partícióval kapcsolatos információkat és egyéb paramétereket, az olyan ügyféloldali eszközként fut, amely a C# API-kat vagy a PowerShell-t használja a szolgáltatás-partíciók hibáinak létrehozásához. A forgatókönyv szimulált hibák és szolgáltatás-érvényesítések sorozatából áll, miközben az üzleti logikája az oldalon fut a számítási feladatok biztosításához. A szolgáltatás érvényesítésének hibája olyan problémát jelez, amely további vizsgálatot igényel.

### <a name="faults-simulated-in-the-failover-test"></a>A feladatátvételi tesztben szimulált hibák
* Egy telepített kód újraindítása, amelyben a partíció üzemel
* Elsődleges/másodlagos replika vagy állapot nélküli példány eltávolítása
* Elsődleges másodlagos replika újraindítása (ha megőrzött szolgáltatás)
* Elsődleges replika áthelyezése
* Másodlagos replika áthelyezése
* A partíció újraindítása

A feladatátvételi teszt egy kiválasztott hibát okoz, majd az érvényesítést futtatja a szolgáltatásban a stabilitásának biztosítása érdekében. A feladatátvételi teszt egyszerre csak egy hibát okoz, a káosz tesztben esetlegesen több hibát is kihasználva. Ha a szolgáltatás partíciója nem stabilizálódik a beállított időkorláton belül az egyes hibák után, a teszt sikertelen lesz. A teszt csak biztonságos hibákat okoz. Ez azt jelenti, hogy a külső meghibásodások hiányában a kvórum vagy az adatvesztés nem fog történni.

### <a name="important-configuration-options"></a>Fontos konfigurációs beállítások
* **PartitionSelector**: választó objektum, amely meghatározza a megcélozni kívánt partíciót.
* **TimeToRun**: a tesztelés befejezésének teljes ideje.
* **MaxServiceStabilizationTimeout**: maximális időtartam, ameddig a fürt Kifogástalan állapotba kerül, mielőtt a teszt sikertelen lesz. A végrehajtott ellenőrzések az, hogy a szolgáltatás állapota rendben van-e, a rendszer az összes partícióhoz a cél másodpéldány-készlet méretét adja meg, és nem létezik inbuild replika.
* **WaitTimeBetweenFaults**: ennyi idő szükséges a hibák és az érvényesítési ciklusok közötti várakozáshoz.

### <a name="how-to-run-the-failover-test"></a>A feladatátvételi teszt futtatása
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
