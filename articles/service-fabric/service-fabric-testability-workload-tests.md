---
title: Hibák szimulálása az Azure Service Fabric-alkalmazásokban
description: Ismerje meg, hogyan erősítheti meg Azure Service Fabric szolgáltatásait a kecses és a nem zökkenőmentes meghibásodások ellen.
ms.topic: conceptual
ms.date: 06/15/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: c714ae30c64ea073cbac521eac5e15a8d968b7ea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91531310"
---
# <a name="simulate-failures-during-service-workloads"></a>Hibák szimulálása a szolgáltatás munkaterhelései során
Az Azure Service Fabric tesztelési forgatókönyvei lehetővé teszik a fejlesztők számára, hogy ne aggódjanak az egyes hibák kezelésével kapcsolatban. Vannak azonban olyan forgatókönyvek, amelyekben szükség lehet az ügyfél-munkaterhelések és a hibák explicit módon történő elvégzésére. Az ügyfél-munkaterhelések és a hibák elhagyása biztosítja, hogy a szolgáltatás ténylegesen végezzen valamilyen műveletet, ha a hiba történik. A tesztelés által biztosított szabályozás szintje miatt ezek a számítási feladatok végrehajtásának pontos pontjai lehetnek. Az alkalmazás különböző állapotában lévő hibák levonása hibákat talál, és javíthatja a minőséget.

## <a name="sample-custom-scenario"></a>Példa egyéni forgatókönyvre
Ez a teszt egy olyan forgatókönyvet mutat be, amely összekapcsolja az üzleti munkaterhelést [kecses és zavartalan hibákkal](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions). A hibákat a szolgáltatási műveletek közepén vagy a legjobb eredmények érdekében kell kiszámítani.

Lássunk egy példát a szolgáltatásra, amely négy munkaterhelést tesz elérhetővé: A, B, C és D. Mindegyik a munkafolyamatok egy készletének felel meg, amely lehet számítási, tárolási vagy vegyes. Az egyszerűség kedvéért a példában elküldjük a számítási feladatokat. Az ebben a példában szereplő különböző hibák a következők:

* RestartNode: nem megfelelő hiba a gép újraindításának szimulálása érdekében.
* RestartDeployedCodePackage: hiba a Service Host-folyamat összeomlásának szimulálása közben.
* RemoveReplica: kecses hiba a replika eltávolításának szimulálása érdekében.
* MovePrimary: az Service Fabric Load Balancer által aktivált replika-áthelyezések szimulálására szolgáló kecses hiba.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.TestManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.FaultManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.FaultManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.FaultManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.FaultManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```
