---
title: Újbóli belépés az Azure Service Fabric Actors szolgáltatásban
description: A Service Fabric Reliable Actors újbóli belépés bemutatása, amely logikailag elkerülheti a blokkolást a hívási környezet alapján.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 90a8e9146d18d628fbf124307f1b79b912c9cef9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789380"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors újbóli belépés
A Reliable Actors futtatókörnyezet alapértelmezés szerint lehetővé teszi a logikai hívás kontextus-alapú újbóli belépés. Ez lehetővé teszi, hogy a szereplők újra bejelentkeznek, ha ugyanabban a hívási környezeti láncban vannak. Az A-Actor például üzenetet küld a B színésznek, aki üzenetet küld a C-nek. Az üzenet feldolgozásának részeként, ha a színész A "A" résztvevőt hívja meg, az üzenet újra bejelentkező, így engedélyezve lesz. A másik hívási környezet részét képező más üzeneteket a rendszer letiltja a (z)

Két lehetőség érhető el az enumerálásban definiált Actor újbóli belépés `ActorReentrancyMode` :

* `LogicalCallContext` (alapértelmezett viselkedés)
* `Disallowed` – letiltja a újbóli belépés

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
A újbóli belépés a regisztráció során konfigurálható a `ActorService` beállításokban. A beállítás a Actors szolgáltatásban létrehozott összes színészi példányra vonatkozik.

A következő példa egy Actor szolgáltatást mutat be, amely a újbóli belépés módot állítja be `ActorReentrancyMode.Disallowed` . Ebben az esetben, ha egy színész visszaküldési üzenetet küld egy másik szereplőnek, a rendszer a típus kivételét `FabricException` fogja eldobni.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Következő lépések
* További információ a újbóli belépés a [Actor API-dokumentációjában](/dotnet/api/microsoft.servicefabric.actors))
