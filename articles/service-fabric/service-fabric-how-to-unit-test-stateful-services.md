---
title: Egység-tesztek fejlesztése állapot-nyilvántartó szolgáltatásokhoz
description: Ismerje meg az Azure-Service Fabric az állapot-nyilvántartó szolgáltatásokkal kapcsolatos egység-tesztelést, valamint a fejlesztés során figyelembe veendő speciális szempontokat.
ms.topic: conceptual
ms.date: 09/04/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 287c0544daa3c44d91fd336b502c496b9b4bb266
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89011395"
---
# <a name="create-unit-tests-for-stateful-services"></a>Egységbeli tesztek létrehozása állapot-nyilvántartó szolgáltatásokhoz
Az egység tesztelése Service Fabric állapot-nyilvántartó szolgáltatások felfedik azokat a gyakori hibákat, amelyeket a hagyományos alkalmazások vagy a tartományra vonatkozó egységek tesztelése nem feltétlenül okoz. Az állapot-nyilvántartó szolgáltatások esetében az egység-tesztek fejlesztése során néhány különleges szempontot figyelembe kell venni.

1. Minden replika végrehajtja az alkalmazás kódját, de különböző kontextusban. Ha a szolgáltatás három replikát használ, a szolgáltatási kód három csomóponton, párhuzamosan, különböző kontextusban/szerepkörben lesz végrehajtva.
2. Az állapot-nyilvántartó szolgáltatáson belül tárolt állapotnak konzisztensnek kell lennie az összes replika között. A State Manager és a megbízható gyűjtemények ezt az egységességet fogják biztosítani. A memóriában lévő állapotot azonban az alkalmazás kódjával kell felügyelni.
3. Az egyes replikák a fürtön való futás közben bizonyos pontokon módosíthatják a szerepköröket. A másodlagos replika elsődleges lesz, abban az esetben, ha az elsődleges helyet futtató csomópont elérhetetlenné válik, vagy túlterhelt. Ez természetes viselkedést mutat Service Fabric ezért a szolgáltatásoknak meg kell tervezniük, hogy végül egy másik szerepkör alatt hajtsák végre a végrehajtást.

Ez a cikk azt feltételezi, hogy az [egység tesztelési állapot-nyilvántartó szolgáltatásait Service Fabric](service-fabric-concepts-unit-testing.md) olvasta.

## <a name="the-servicefabricmocks-library"></a>A ServiceFabric. mocks könyvtár
A 3.3.0 verziótól kezdve a [ServiceFabric. mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) olyan API-t biztosít, amely a replikák és az állapotadatok előkészítését is lehetővé teszi. Ezt a példákban fogjuk használni.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/) 
 [GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*A ServiceFabric. mocks nem a Microsoft tulajdona vagy tartja karban. Ez azonban jelenleg a Microsoft által javasolt, az állapot-nyilvántartó szolgáltatások tesztelésére szolgáló könyvtár.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Az alakzat előkészítésének és állapotának beállítása
A tesztek elrendezési részének részeként létrejön egy modell-replika és egy állapotfigyelő. A replikakészlet ezután létrehozza a tesztelt szolgáltatás egy példányát az egyes replikák esetében. Emellett olyan életciklus-eseményeket is végrehajt, mint a `OnChangeRole` és a `RunAsync` . Az ál-állapot kezelője gondoskodik arról, hogy az állami kezelőn végrehajtott összes művelet fusson, és a tényleges State Manager maradjon.

1. Hozzon létre egy Service Factory-delegált, amely létrehozza a tesztelni kívánt szolgáltatást. Ehhez hasonlónak vagy azonosnak kell lennie, mint a Service Factory visszahívása, általában a `Program.cs` Service Fabric szolgáltatás vagy színész számára. Ennek a következő aláírást kell követnie:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Hozzon létre egy osztály egy példányát `MockReliableStateManager` . Ez az állapot-kezelővel folytatott összes interakciót kigúnyolja.
3. Hozzon létre egy példányt, ahol a a `MockStatefulServiceReplicaSet<TStatefulService>` `TStatefulService` tesztelt szolgáltatás típusa. Ehhez létre kell hoznia a delegált a következő lépésben: #1 és a State Manager példánya #2
4. Hozzon létre replikákat a kópiakészlet számára. A szerepkör (például elsődleges, ActiveSecondary, IdleSecondary) és a replika AZONOSÍTÓjának meghatározása
   > Tartsa be a replika-azonosítókat! Ezeket a rendszer valószínűleg felhasználja a művelet során, és az adott egység tesztelésének egyes részeit.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Szolgáltatási kérelmek végrehajtása
A szolgáltatási kérelmek egy adott replikán hajthatók végre a kényelmi tulajdonságok és a keresések használatával.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Szolgáltatás áthelyezésének végrehajtása
Az ál-replika számos kényelmi módszert tesz elérhetővé a különböző típusú szolgáltatások elindításához.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Végső összeállítás
A következő teszt egy három csomópontos replikakészlet beállítását mutatja be, és azt ellenőrzi, hogy az adatok elérhetők-e egy másodlagos szerepkör változása után. Ez egy jellemző probléma, amely akkor fordulhat elő, ha a korábban hozzáadott adatmennyiséget a `InsertAsync` memóriában vagy egy megbízható gyűjteményben mentik a futtatása nélkül `CommitAsync` . Mindkét esetben a másodlagos nem lesz szinkronban az elsődlegesvel. Ez a szolgáltatás áthelyezése után inkonzisztens válaszokat eredményezne.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan tesztelheti a [szolgáltatás és a szolgáltatás közötti kommunikációt](service-fabric-testability-scenarios-service-communication.md) , és hogyan [szimulálhatja a hibákat az irányított káosz használatával](service-fabric-controlled-chaos.md).
