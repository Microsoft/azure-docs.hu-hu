---
title: Az Azure Service Fabric állapotának kezelése
description: Ismerje meg, hogyan érheti el, mentheti és távolíthatja el az Azure Service Fabric megbízható színész állapotát, valamint az alkalmazások tervezésekor megfontolandó szempontokat.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 1e796232719342883efc2aa5dd377d586f3039ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96571317"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Reliable Actors állapotának elérése, mentése és eltávolítása
A [Reliable Actors](service-fabric-reliable-actors-introduction.md) olyan egyszálas objektumok, amelyek képesek a logika és az állapot beágyazására, valamint megbízható állapot fenntartására. Minden színészi példány saját [State Managerrel](service-fabric-reliable-actors-state-management.md)rendelkezik: egy olyan szótárhoz hasonló adatstruktúra, amely megbízhatóan tárolja a kulcs/érték párokat. A State Manager egy állami szolgáltató körüli burkoló. Az [adatmegőrzési beállítástól](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) függetlenül is tárolhatja az adattárolást.

Az állapot-kezelő kulcsai csak karakterláncok lehetnek. Az értékek általánosak, és bármilyen típus lehet, beleértve az egyéni típusokat is. Az állapot-kezelőben tárolt értékeknek szerializálható adategyezménynek kell lenniük, mert előfordulhat, hogy a hálózaton keresztül továbbítják őket más csomópontoknak a replikálás során, és előfordulhat, hogy a szereplő állapotának megőrzési beállításától függően lemezre íródnak.

A State Manager a megbízható szótárban találhatóhoz hasonló általános szótári módszereket tesz elérhetővé az állapot kezeléséhez.

További információ: [ajánlott eljárások a színészi állapot kezelésében](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Hozzáférési állapot
Az állapot a Key Manageren keresztül érhető el. Az állami kezelői módszerek mindegyike aszinkron módon történik, mivel előfordulhat, hogy lemezes I/O-műveletek szükségesek, ha a szereplők megőrzött állapotban vannak. Az első hozzáféréskor az állapotjelző objektumok a memóriában vannak gyorsítótárazva. Megismételheti a hozzáférési műveleteket közvetlenül a memóriából, és szinkron módon visszatérhet a lemez I/O-vagy aszinkron környezet-váltási terhelése nélkül. Az állapot objektum a következő esetekben törlődik a gyorsítótárból:

* A Actors metódus kezeletlen kivételt jelez, miután lekért egy objektumot az állapot-kezelőből.
* A rendszer újraaktiválja a szereplőt, vagy az inaktiválás vagy a meghibásodás után.
* Az állapot-szolgáltató lapok állapota lemezre. Ez a viselkedés az állami szolgáltató implementációtól függ. A beállítás alapértelmezett állapot-szolgáltatója `Persisted` ezt a viselkedést adja meg.

Lekérheti az állapotot standard *Get* művelettel `KeyNotFoundException` (C#) vagy `NoSuchElementException` (Java), ha nem létezik bejegyzés a kulcshoz:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Lekérheti az állapotot olyan *TryGet* metódus használatával is, amely nem dobja el, ha nem létezik bejegyzés a kulcshoz:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Állapot mentése
A State Manager lekérési metódusok egy helyi memóriában lévő objektumra mutató hivatkozást adnak vissza. Ha csak a helyi memóriában módosítja ezt az objektumot, a rendszer nem okoz tartósan mentést. Ha egy objektum beolvasása a State managerből történik, és módosul, akkor azt újra be kell szúrni a tartósan.

Az állapotot egy feltétel nélküli *készlet* használatával szúrhatja be, amely a `dictionary["key"] = value` szintaxisnak megfelelő:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Az állapotot hozzáadhatja egy *Add* metódus használatával. Ez a metódus `InvalidOperationException` (C#) vagy `IllegalStateException` (Java), ha olyan kulcsot próbál felvenni, amely már létezik.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

*TryAdd* metódus használatával is hozzáadhat állapotot. Ez a metódus nem dobja el, ha olyan kulcsot próbál hozzáadni, amely már létezik.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

A Actors metódus végén a State Manager automatikusan menti az INSERT vagy a Update művelet által hozzáadott vagy módosított értékeket. A "Mentés" a használt beállításoktól függően a lemezre és a replikálásra is kiterjedhet. A nem módosított értékek nem maradnak meg és nem replikálódnak. Ha nincs módosítva érték, a mentési művelet nem tesz semmit. Ha a Mentés meghiúsul, a rendszer elveti a módosított állapotot, és újratölti az eredeti állapotot.

Azt is megteheti, hogy manuálisan menti az állapotot, ha meghívja a `SaveStateAsync` metódust a Actor Base-ben:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Állapot eltávolítása
A *Remove* metódus meghívásával véglegesen eltávolíthatja az állapotot egy Actors State managerből. Ez a metódus `KeyNotFoundException` (C#) vagy `NoSuchElementException` (Java), amikor olyan kulcsot próbál meg eltávolítani, amely nem létezik.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Az állapotot véglegesen is eltávolíthatja a *TryRemove* metódus használatával. Ez a metódus nem dobja el, ha nem létező kulcsot próbál meg eltávolítani.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Következő lépések

A Reliable Actorsban tárolt állapotot szerializálni kell a lemezre írás előtt, és a magas rendelkezésre állás érdekében replikálni kell őket. További információ a [Actor típusú szerializálásról](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Következő lépésként Ismerkedjen meg a [Actor Diagnostics szolgáltatással és a teljesítmény monitorozásával](service-fabric-reliable-actors-diagnostics.md).
