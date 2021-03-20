---
title: Fürtcsomópontok elindítása és leállítása
description: Megtudhatja, hogyan használhatja a hibák befecskendezését Service Fabric alkalmazások teszteléséhez a fürtcsomópontok elindításával és leállításával.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c31040ec13084f9e4b08bbc9a347e4ad44975bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89021255"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>A csomópont elindítása és a csomópont API-k leállítása a Node áttérési API-val

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Mi a csomópont leállítása és a Node API-k elindítása?

A Node API leállítása (felügyelt: [StopNodeAsync ()][stopnode], PowerShell: [stop-ServiceFabricNode][stopnodeps]) leállítja a Service Fabric csomópontot.  Service Fabric csomópont a folyamat, nem virtuális gép vagy számítógép – a virtuális gép vagy a gép továbbra is fut.  A "Node" többi dokumentuma Service Fabric csomópontot fog érteni.  Egy csomópont leállítása *leállított* állapotba kerül, ahol nem tagja a fürtnek, és nem képes a szolgáltatások üzemeltetésére, így szimulálva a *lefelé* mutató csomópontot.  Ez akkor hasznos, ha a rendszerbe beinjektálja a hibákat az alkalmazás teszteléséhez.  A Node API elindítása (felügyelt: [StartNodeAsync ()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) megfordítja a csomópont leállítása API-t, amely visszaállítja a csomópontot egy normál állapotba.

## <a name="why-are-we-replacing-these"></a>Miért cseréljük ezeket?

A korábban leírtaknak megfelelően egy *leállított* Service Fabric csomópont egy olyan csomópont, amely szándékosan célozza meg a Node API leállítását.  A *le* csomópontok olyan csomópontok, amelyek bármely más okból leálltak (például a virtuális gép vagy a számítógép ki van kapcsolva).  A Node API leállításakor a rendszeren nem jelenik meg információ a *leállított* csomópontok és a *lefelé* irányuló csomópontok megkülönböztetéséhez.

Emellett az API-k által visszaadott hibák nem leíró jellegűek.  Ha például egy már *leállított* csomóponton meghívja a Node API-t, a rendszer visszaküldi a hibát a *InvalidAddress*.  Ez a tapasztalat javítható.

Emellett a csomópont leállításának időtartama a "végtelen", amíg meg nem történik a Start Node API meghívása.  Azt találtuk, hogy ez problémákat okozhat, és lehetséges, hogy hiba történt.  Például olyan problémák merültek fel, amikor egy felhasználó meghívja a Node API leállítását egy csomóponton, majd elfelejtette.  Később nem volt egyértelmű, ha a csomópont *le* lett állítva vagy *leállt*.


## <a name="introducing-the-node-transition-apis"></a>A csomópont-áttérési API-k bemutatása

Ezeket a problémákat az API-k egy új készletében tárgyaljuk.  Az új csomópont-átváltási API (felügyelt: [StartNodeTransitionAsync ()][snt]) felhasználható egy Service Fabric csomópont *leállított* állapotba való átváltására, vagy egy *leállított* állapotból a normál állapotba való átállásra.  Vegye figyelembe, hogy az API neveként a "Start" nem hivatkozik csomópont indítására.  Egy aszinkron művelet megkezdését jelenti, amelyet a rendszer végrehajt, hogy a csomópontot *leállított* vagy elindított állapotba váltson.

**Használat**

Ha a csomópont-átváltási API nem kivételt jelez a meghívásakor, akkor a rendszer elfogadta az aszinkron műveletet, és végrehajtja.  A sikeres hívás nem jelenti azt, hogy a művelet még nem fejeződött be.  Ha információt szeretne kapni a művelet aktuális állapotáról, hívja meg a csomópont-áttérési folyamat API-ját (felügyelt: [GetNodeTransitionProgressAsync ()][gntp]) a csomópont-áttérési API-nak a művelethez való meghívásakor használt GUID azonosítóval.  A csomópont-áttérési folyamat API egy NodeTransitionProgress objektumot ad vissza.  Az objektum State tulajdonsága a művelet aktuális állapotát adja meg.  Ha az állapot "fut", akkor a művelet végrehajtása folyamatban van.  Ha befejeződött, a művelet hiba nélkül befejeződött.  Ha hibás, hiba történt a művelet végrehajtásakor.  Az eredmény tulajdonság kivétel tulajdonsága jelzi, hogy mi volt a probléma.  https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstateTovábbi információ az állapot tulajdonságról: példák a "minta felhasználás" szakaszra.


**Leállított csomópont és egy lefelé mutató csomópont közötti különbségtétel** Ha egy csomópontot *leállítanak* a csomópont-áttérési API használatával, a csomópont-lekérdezés (felügyelt: [GetNodeListAsync ()][nodequery], a PowerShell: [Get-ServiceFabricNode][nodequeryps]) kimenete azt mutatja, hogy ez a csomópont igaz értékű *IsStopped* tulajdonságot tartalmaz.  Vegye figyelembe, hogy ez eltér a *NodeStatus* tulajdonság értékétől, amely a következőt fogja *lemondani*:.  Ha a *NodeStatus* tulajdonság értéke *lefelé* van, de a *IsStopped* hamis, akkor a csomópont nem állt le a csomópont-áttérési API-val, és valamilyen más ok miatt *leáll* .  Ha a *IsStopped* tulajdonság értéke TRUE (igaz), és a *NodeStatus* tulajdonság nem érhető *el, akkor a csomópont-* áttérési API használatával leállt.

Ha egy *leállított* csomópontot indít el a csomópont-áttérési API használatával, akkor a rendszer visszaküldi a fürt normális tagjaként való működésre.  A csomópont-lekérdezési API kimenete hamis értékként jeleníti meg a *IsStopped* , és *NodeStatus* a nem lefelé (például fel).


**Korlátozott időtartam** Ha a csomópont-áttérési API-t egy csomópont leállítására használja, az egyik kötelező paraméter, a *stopNodeDurationInSeconds* pedig azt az időtartamot jelenti, ameddig a csomópontot *le kell állítani*.  Ennek az értéknek a megengedett tartományba kell esnie, amely legalább 600, és legfeljebb 14400.  Az idő lejárta után a csomópont automatikusan újraindul az állapotba.  Tekintse át az alábbi 1. mintát a használati példaként.

> [!WARNING]
> Ne keverje a csomópont-áttérési API-kat és a csomópont leállítása és a Node API-k elindítása.  Javasoljuk, hogy csak a csomópont-áttérési API-t használja.  >, hogy egy csomópontot már leállítottak-e a Node API leállítása szolgáltatással, először a Node API elindítása előtt kell elindítania az > Node Transition API-k használata előtt.

> [!WARNING]
> Több csomópontos áttérési API-hívás nem hajtható végre párhuzamosan ugyanazon a csomóponton.  Ebben az esetben a csomópont-átváltási API > egy FabricException a NodeTransitionInProgress ErrorCode tulajdonságának értékével.  Ha egy csomópontra való áttérés egy adott csomóponton > elindult, várjon, amíg a művelet el nem éri a terminál állapotát (befejezett, hibás vagy ForceCancelled), mielőtt megkezdené a > új átmenetet ugyanarra a csomópontra.  A párhuzamos csomópontok átmeneti hívásait különböző csomópontokon lehet engedélyezni.


#### <a name="sample-usage"></a>Példa a használatra


**1. minta** – a következő minta a csomópont-áttérési API-t használja a csomópont leállításához.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**2. minta** – az alábbi példa egy *leállított* csomópontot indít el.  Az első mintából néhány segítő módszert használ.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**3. minta** – az alábbi példa a helytelen használatot mutatja be.  Ez a használat helytelen, mert az általa biztosított *stopDurationInSeconds* nagyobb, mint az engedélyezett tartomány.  Mivel a StartNodeTransitionAsync () végzetes hiba miatt meghiúsul, a rendszer nem fogadta el a műveletet, és a Progress API-t nem kell meghívni.  Ez a példa néhány segítő módszert használ az első mintában.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**4. minta** – a következő minta azokat a hibaüzeneteket mutatja be, amelyeket a csomópont-áttérési folyamat API-nak kell visszaadnia, ha a csomópont-áttérési API által kezdeményezett műveletet elfogadják, de később a végrehajtás közben nem sikerül.  Ebben az esetben a művelet meghiúsul, mert a csomópont-áttérési API megpróbál olyan csomópontot elindítani, amely nem létezik.  Ez a példa néhány segítő módszert használ az első mintában.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[stopnodeps]: /previous-versions/azure/mt125982(v=azure.100)
[startnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[startnodeps]: /previous-versions/azure/mt163520(v=azure.100)
[nodequery]: /dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: /powershell/module/servicefabric/get-servicefabricnode
[snt]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
