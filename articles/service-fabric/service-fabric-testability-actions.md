---
title: Hibák szimulálása az Azure-beli szolgáltatásokban
description: Ez a cikk a Microsoft Azure Service Fabric található tesztelési műveletekre mutat.
author: georgewallace
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: gwallace
ms.custom: devx-track-csharp
ms.openlocfilehash: aea918f75ad9c8aa58b5201d2fdd967794085438
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547077"
---
# <a name="testability-actions"></a>Tesztelési műveletek
A megbízhatatlan infrastruktúra szimulálása érdekében az Azure Service Fabric biztosítja a fejlesztőket, és lehetővé teszi a különböző valós hibák és az állami váltások szimulálása. Ezek tesztelési műveleteknek vannak kitéve. A műveletek az alacsony szintű API-k, amelyek egy adott hiba befecskendezését, állapotának átváltását vagy érvényesítését okozzák. A műveletek kombinálásával átfogó tesztelési forgatókönyveket írhat a szolgáltatásaihoz.

Service Fabric a műveletekből álló gyakori tesztelési forgatókönyveket tartalmaz. Javasoljuk, hogy használja ezeket a beépített forgatókönyveket, amelyek körültekintően vannak kiválasztva a gyakori állapotú átmeneti és sikertelen esetek teszteléséhez. A műveletek azonban egyéni tesztelési forgatókönyvek létrehozásához is használhatók, ha olyan forgatókönyvekhez kívánja hozzáadni a lefedettséget, amelyeket nem a beépített forgatókönyvek, vagy amelyek az alkalmazáshoz vannak szabva.

A műveletek C# implementációja a System.Fabric.dll szerelvényben található. A System Fabric PowerShell-modul a Microsoft.ServiceFabric.Powershell.dll szerelvényben található. A futásidejű telepítés részeként a ServiceFabric PowerShell-modul telepítve van a könnyű használat érdekében.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Kecses és nem zökkenőmentes hibák
A tesztelési műveletek két fő gyűjtőbe sorolhatók:

* Sikertelen hibák: ezek a hibák szimulálják a hibákat, például a gép újraindítását és a folyamatok összeomlását. Ilyen meghibásodás esetén a folyamat végrehajtási környezete hirtelen leáll. Ez azt jelenti, hogy a rendszer nem tudja futtatni az állapotot az alkalmazás újraindítása előtt.
* Kecses hibák: ezek a hibák olyan kecses műveleteket szimulálnak, mint a replika mozgatása és a terheléselosztás által kiváltott adatvesztés. Ilyen esetekben a szolgáltatás értesítést kap a bezárásról, és a kilépés előtt törli az állapotot.

A jobb minőség-ellenőrzés érdekében futtassa a szolgáltatást és az üzleti számítási feladatot, miközben a különböző kecses és zavartalan hibákat okoz. A hibát okozó hibák olyan forgatókönyveket mutatnak be, amelyekben a szolgáltatási folyamat hirtelen kilép egy munkafolyamat közepéről. Ez ellenőrzi a helyreállítási útvonalat, miután a Service Fabric visszaállította a szolgáltatás replikáját. Ez segít az adatkonzisztencia tesztelésében, valamint azt, hogy a szolgáltatás állapota a hibák után megfelelően van-e karbantartva. A további hibák (a kecses meghibásodások) azt tesztelik, hogy a szolgáltatás megfelelően áthelyezi a replikákat Service Fabric. Ez a RunAsync metódus lemondásának kezelését ellenőrzi. A szolgáltatásnak ellenőriznie kell a beállított lemondási tokent, helyesen kell mentenie az állapotát, és ki kell lépnie a RunAsync metódusból.

## <a name="testability-actions-list"></a>Tesztelési műveletek listája
| Művelet | Leírás | Felügyelt API | PowerShell-parancsmag | Kecses/nem zökkenőmentes hibák |
| --- | --- | --- | --- | --- |
| CleanTestState |Eltávolítja az összes teszt állapotot a fürtből a tesztelési illesztőprogram hibás leállítása esetén. |CleanTestStateAsync |Remove-ServiceFabricTestState |Nem alkalmazható |
| InvokeDataLoss |Adatvesztést okoz a szolgáltatás partíciójában. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |Biztonságos |
| InvokeQuorumLoss |Egy adott állapot-nyilvántartó szolgáltatási partíciót helyez el kvórum elvesztéseként. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |Biztonságos |
| MovePrimary |Áthelyezi egy állapot-nyilvántartó szolgáltatás megadott elsődleges replikáját a fürt megadott csomópontjára. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |Biztonságos |
| MoveSecondary |Egy állapot-nyilvántartó szolgáltatás aktuális másodlagos replikáját áthelyezi egy másik fürtcsomópont-csomópontra. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |Biztonságos |
| RemoveReplica |Replika meghibásodásának szimulálása egy replika fürtből való eltávolításával. Ez lezárta a replikát, és átvált a "None" szerepkörre, és eltávolítja az összes állapotát a fürtből. |RemoveReplicaAsync |Remove-ServiceFabricReplica |Biztonságos |
| RestartDeployedCodePackage |A kód-előkészítési folyamat hibájának szimulálása egy fürt csomópontjain telepített kód újraindításával. Ezzel megszakítja a kód-csomag folyamatát, amely újraindítja a folyamatban lévő összes felhasználói szolgáltatás replikáját. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |Biztonságos |
| RestartNode |Egy csomópont újraindításával szimulálja Service Fabric fürtcsomópont meghibásodását. |RestartNodeAsync |Restart-ServiceFabricNode |Biztonságos |
| RestartPartition |Egy adott partíció egy vagy több replikájának újraindításával szimulálja az adatközpont áramszünetét vagy a fürt kimaradási forgatókönyvét. |RestartPartitionAsync |Restart-ServiceFabricPartition |Biztonságos |
| RestartReplica |Nem szimulálja a replikát egy megőrzött replika újraindításával egy fürtben, bezárja a replikát, majd újból megnyitja. |RestartReplicaAsync |Restart-ServiceFabricReplica |Biztonságos |
| StartNode |Egy olyan csomópontot indít el a fürtben, amely már le van állítva. |StartNodeAsync |Start-ServiceFabricNode |Nem alkalmazható |
| Stopnode parancs |Csomópont meghibásodásának szimulálása egy fürt csomópontjainak leállításával. A csomópont a StartNode meghívása után marad lefelé. |StopNodeAsync |Stop-ServiceFabricNode |Biztonságos |
| ValidateApplication |Érvényesíti az alkalmazáson belüli összes Service Fabric szolgáltatás rendelkezésre állását és állapotát, általában azt követően, hogy valamilyen hibát okoz a rendszernek. |ValidateApplicationAsync |Test-ServiceFabricApplication |Nem alkalmazható |
| ValidateService |Ellenőrzi a Service Fabric szolgáltatás rendelkezésre állását és állapotát, általában azt követően, hogy valamilyen hibát okoz a rendszernek. |ValidateServiceAsync |Test-ServiceFabricService |Nem alkalmazható |

## <a name="running-a-testability-action-using-powershell"></a>Tesztelési művelet futtatása a PowerShell használatával
Ez az oktatóanyag bemutatja, hogyan futtathat tesztelési műveletet a PowerShell használatával. Megtudhatja, hogyan futtathat tesztelési műveletet egy helyi (egyablakos) fürtön vagy egy Azure-fürtön. Microsoft.Fabric.Powershell.dll – a Service Fabric PowerShell-modul – a Microsoft Service Fabric MSI telepítésekor automatikusan települ. A modul automatikusan betöltődik, amikor megnyit egy PowerShell-parancssort.

Oktatóanyag-szegmensek:

* [Művelet futtatása egy egymezős fürtön](#run-an-action-against-a-one-box-cluster)
* [Művelet futtatása Azure-fürtön](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Művelet futtatása egy egymezős fürtön
Ha a tesztelési műveletet egy helyi fürtön szeretné futtatni, először kapcsolódjon a fürthöz, és nyissa meg a PowerShell-parancssort rendszergazdai módban. Nézzük meg a **restart-ServiceFabricNode** műveletet.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Itt a művelet **újraindítása – a ServiceFabricNode** egy "csomópont1" nevű csomóponton fut. A befejezési mód azt adja meg, hogy az nem ellenőrzi, hogy a csomópont-újraindítási művelet végrehajtása sikeres volt-e. A befejezési mód megadásával az "ellenőrzés" hatására a rendszer ellenőrzi, hogy az újraindítási művelet sikeresen befejeződött-e. Ahelyett, hogy a csomópontot a neve alapján közvetlenül megadta, megadhatja azt egy partíciós kulccsal és a replika típusával, a következőképpen:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode** használatával újra kell indítani egy Service Fabric csomópontot a fürtben. Ezzel a művelettel leállítja a Fabric.exe folyamatot, amely újraindítja az adott csomóponton futtatott összes rendszerszolgáltatási és felhasználói szolgáltatás replikáját. Ha ezt az API-t használja a szolgáltatás tesztelésére, segít felfedni a hibákat a feladatátvételi helyreállítási útvonalak mentén. Segít szimulálni a csomópontok hibáit a fürtben.

A következő képernyőképen az **Újraindítás-ServiceFabricNode** tesztelési parancs látható működés közben.

![Képernyőkép a Restart-ServiceFabricNode parancs futtatásáról a PowerShellben.](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

Az első **Get-ServiceFabricNode** (a Service Fabric PowerShell-modulból származó parancsmag) kimenete azt mutatja, hogy a helyi fürt öt csomóponttal rendelkezik: Node. 1 – Node. 5. A tesztelési művelet (parancsmag) **újraindítása** után a ServiceFabricNode a Node. 4 nevű csomóponton fut, láthatjuk, hogy a csomópont üzemidőe alaphelyzetbe áll.

### <a name="run-an-action-against-an-azure-cluster"></a>Művelet futtatása Azure-fürtön
A tesztelési művelet (PowerShell használatával) egy Azure-fürtön való futtatása hasonló a művelet helyi fürtön való futtatásához. Az egyetlen különbség, hogy a művelet futtatása előtt a helyi fürthöz való csatlakozás helyett először csatlakoznia kell az Azure-fürthöz.

## <a name="running-a-testability-action-using-c35"></a>Tesztelési művelet futtatása C&#35; használatával
A tesztelési műveletek C# használatával történő futtatásához először a FabricClient használatával kell csatlakoznia a fürthöz. Ezután szerezze be a művelet futtatásához szükséges paramétereket. Ugyanazon művelet futtatására különböző paraméterek használhatók.
A RestartServiceFabricNode művelettel a futtatásának egyik módja a csomópont információinak (a csomópont neve és a csomópont-példány azonosítója) használata a fürtben.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Paraméter magyarázata:

* A **CompleteMode** azt adja meg, hogy a mód nem ellenőrzi, hogy az újraindítási művelet ténylegesen sikeres volt-e. A befejezési mód megadásával az "ellenőrzés" hatására a rendszer ellenőrzi, hogy az újraindítási művelet sikeresen befejeződött-e.  
* A **OperationTimeout** Beállítja azt az időtartamot, ameddig a művelet befejeződik, mielőtt timeoutexception osztályról a kivételt.
* A **CancellationToken** lehetővé teszi, hogy a függőben lévő hívás megszakítható legyen.

Ahelyett, hogy a csomópontot a neve alapján közvetlenül megadja, megadhatja azt egy partíciós kulccsal és a replika típusával.

További információ: PartitionSelector és ReplicaSelector.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector és ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
A PartitionSelector egy tesztelési segédeszköz, amely egy adott partíció kiválasztására szolgál, amely a tesztelési műveletek bármelyikét végrehajtja. Egy adott partíció kiválasztására szolgál, ha a partíció-azonosító előre ismert. Vagy megadhatja a partíciós kulcsot, és a művelet belsőleg fogja megoldani a partíció-azonosítót. Lehetőség van egy véletlenszerű partíció kiválasztására is.

Ennek a segítőnek a használatához hozza létre a PartitionSelector objektumot, és válassza ki a partíciót a Select * metódusok egyikének használatával. Ezután továbbítsa a PartitionSelector objektumot a szükséges API-nak. Ha nincs kiválasztva beállítás, az alapértelmezett érték egy véletlenszerű partíció.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
A ReplicaSelector egy tesztelési segédeszköz, amely a tesztelési műveletek elvégzésére szolgáló replika kiválasztását segíti. Egy adott replika kiválasztására használható, ha a replika AZONOSÍTÓjának előzetes ismerete. Emellett lehetősége van egy elsődleges replika vagy egy véletlenszerű másodlagos lehetőség kiválasztására. A ReplicaSelector a PartitionSelector származtatja, ezért ki kell választania a replikát és azt a partíciót, amelyen el szeretné végezni a tesztelési műveletet.

A segítő használatához hozzon létre egy ReplicaSelector objektumot, és állítsa be, hogyan szeretné kijelölni a replikát és a partíciót. Ezután átadhatja azt az API-nak, amelyhez szükség van. Ha nincs kiválasztva beállítás, az alapértelmezett érték egy véletlenszerű replika és egy véletlenszerű partíció.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Következő lépések
* [Tesztelési forgatókönyvek](service-fabric-testability-scenarios.md)
* A szolgáltatás tesztelése
  * [Hibák szimulálása a szolgáltatás munkaterhelései során](service-fabric-testability-workload-tests.md)
  * [Szolgáltatások közötti kommunikációs hibák](service-fabric-testability-scenarios-service-communication.md)

