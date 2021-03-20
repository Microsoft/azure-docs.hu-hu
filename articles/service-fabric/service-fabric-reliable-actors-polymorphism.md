---
title: Polimorfizmus a Reliable Actors-keretrendszerben
description: A Reliable Actors-keretrendszerben lévő .NET-felületek és-típusok hierarchiáit felépítve újrahasznosíthatja a funkcionalitást és az API-definíciókat.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 568e306979e862c325264a4e12a64d95e6d13c5f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96575992"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfizmus a Reliable Actors-keretrendszerben
A Reliable Actors-keretrendszer lehetővé teszi, hogy többek között olyan technikákat hozzon létre, amelyeket az objektum-orientált kialakításban is használ. Az egyik ilyen módszer a polimorfizmus, amely lehetővé teszi, hogy a típusok és a felületek több általánosított szülőtől örökölnek. A Reliable Actors-keretrendszer öröklése általában a .NET-modellt követi, néhány további korlátozással. Java/Linux esetén a Java-modellt követi.

## <a name="interfaces"></a>Interfészek
A Reliable Actors-keretrendszerhez meg kell határoznia legalább egy olyan felületet, amelyet a színész típusa szerint kell megvalósítani. Ez az interfész olyan proxy osztály létrehozásához használható, amelyet az ügyfelek használhatnak a szereplőkkel való kommunikációhoz. A felületek más felületektől örökölnek, ha a színész típusa és az összes szülője a IActor (C#) vagy a színész (Java) által megvalósított összes felülettel rendelkezik. A IActor (C#) és a Actor (Java) a platform által definiált alapinterfészek, amelyek a .NET és a Java keretrendszerbeli szereplőkkel foglalkoznak. Így az alakzatokat használó klasszikus polimorfizmus például a következőhöz hasonló lehet:

![Alakzat szereplőinek illesztőfelület-hierarchiája][shapes-interface-hierarchy]

## <a name="types"></a>Típusok
Létrehozhatja a Actor típusú hierarchiát is, amely a platform által biztosított alapszintű Actor osztályból származik. Az alakzatok esetében előfordulhat, hogy egy alap `Shape` (C#) vagy `ShapeImpl` (Java) típusú típust tartalmaz:

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

A `Shape` (C#) vagy `ShapeImpl` (Java) altípusok felülbírálják a metódusokat az alapból.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Jegyezze fel az `ActorService` attribútumot a színész típusán. Ez az attribútum tájékoztatja a megbízható szereplői keretrendszert arról, hogy automatikusan létre kell hoznia egy szolgáltatást az ilyen típusú szereplők üzemeltetéséhez. Bizonyos esetekben előfordulhat, hogy olyan alaptípust szeretne létrehozni, amely kizárólag altípusokkal való megosztásra szolgál, és soha nem lesz felhasználva konkrét szereplők létrehozásához. Ezekben az esetekben a `abstract` kulcsszó használatával jelezze, hogy soha nem fog létrehozni egy szereplőt az adott típus alapján.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, [hogyan használja ki a Reliable Actors Framework a Service Fabric platformot](service-fabric-reliable-actors-platform.md) a megbízhatóság, a méretezhetőség és a konzisztens állapot biztosításához.
* Ismerkedjen meg a [színészi életciklussal](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
