---
title: A Actor-alapú Azure Service Fabric Actors eseményei
description: Ismerje meg Service Fabric Reliable Actors eseményeit, amelyek hatékony módszert biztosítanak a szereplők és az ügyfelek közötti kommunikációhoz.
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 591e3539715ba0873e44f56a93d05df6552bb1ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96571368"
---
# <a name="actor-events"></a>Színészi események
A színészi események lehetővé teszik a legjobb erőfeszítést jelző értesítések küldését a színésztől az ügyfeleknek. A Actors-események a színészek közötti kommunikációhoz készültek, és nem használhatók a színészek közötti kommunikációhoz.

A következő kódrészletek azt mutatják be, hogyan használhatók a Actor Events az alkalmazásban.

Definiáljon egy felületet, amely leírja a színész által közzétett eseményeket. Ezt az illesztőfelületet az illesztőfelületből kell származtatni `IActorEvents` . A metódusok argumentumai csak [szerializálható adategyezmények](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)lehetnek. A metódusoknak Void értéket kell visszaadnia, mivel az eseményekre vonatkozó értesítések az egyik módszer és a legjobb erőfeszítés.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Deklarálja a színészi felületen közzétett eseményeket.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
Az ügyfél oldalon hajtsa végre az eseménykezelőt.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

Az ügyfélen hozzon létre egy proxyt az eseményt közzétevő színész számára, és fizessen elő az eseményeire.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

Feladatátvétel esetén a szereplő egy másik folyamathoz vagy csomóponthoz is átadhatja a feladatokat. A Actor proxy kezeli az aktív előfizetéseket, és automatikusan újra Előfizeti őket. Az előfizetések intervallumát az API használatával szabályozhatja `ActorProxyEventExtensions.SubscribeAsync<TEvent>` . A leiratkozáshoz használja az API-t `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` .

A színészen tegye közzé az eseményeket, ahogy történnek. Ha vannak előfizetők az eseményhez, a szereplők futtatókörnyezete elküldi nekik az értesítést.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Következő lépések
* [Actor újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
* [Színészi diagnosztika és Teljesítményfigyelés](service-fabric-reliable-actors-diagnostics.md)
* [A Actor API-referenciájának dokumentációja](/previous-versions/azure/dn971626(v=azure.100))
* [C# mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core-mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)
