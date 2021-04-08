---
title: Reliable Actors időzítők és emlékeztetők
description: Az időzítők és emlékeztetők bevezetője Service Fabric Reliable Actorshoz, beleértve az egyes eszközök használatára vonatkozó útmutatást.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: f77eb29c9146fe66d5d2b6073c33e30fbab649c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791794"
---
# <a name="actor-timers-and-reminders"></a>Színészi időzítők és emlékeztetők
A szereplők rendszeres munkát ütemezhetnek az időzítők vagy emlékeztetők regisztrálásával. Ez a cikk bemutatja, hogyan használhatók az időzítők és a emlékeztetők, valamint ismertetik a köztük lévő különbségeket.

## <a name="actor-timers"></a>Színészi időzítők
A színészi időzítők egy egyszerű burkolót biztosítanak egy .NET-vagy Java-időzítőhöz, így biztosítva, hogy a visszahívási módszerek tiszteletben tartsák a többtényezős futtatókörnyezet által biztosított párhuzamossági garanciákat.

A szereplők a `RegisterTimer` (c#) vagy `registerTimer` (Java) és ( `UnregisterTimer` c#) vagy `unregisterTimer` (Java) metódusok használatával regisztrálhatják és nem regisztrálhatják az időzítőket. Az alábbi példa az időzítő API-k használatát mutatja be. Az API-k nagyon hasonlóak a .NET-időzítőhöz vagy a Java-időzítőhöz. Ebben a példában, amikor az időzítő esedékes, a szereplők futtatókörnyezete a `MoveObject` (C#) vagy a `moveObject` (Java) metódust hívja meg. A metódus garantált a turn-based Egyidejűség tiszteletben tartására. Ez azt jelenti, hogy ha a visszahívás nem fejeződik be, a rendszer nem hajtja végre a többi szereplő metódust vagy időzítő/emlékeztető visszahívást.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

Az időzítő következő időszaka a visszahívás befejeződése után kezdődik. Ez azt jelenti, hogy az időzítő leáll a visszahívás végrehajtása közben, és a visszahívás befejeződése után elindul.

A szereplők futtatókörnyezete a visszahívás befejeződése után menti a színész State managerének változásait. Ha az állapot mentésekor hiba történik, akkor a rendszer inaktiválja a Actor objektumot, és egy új példányt aktivál.

Az [emlékeztetők](#actor-reminders)eltérően nem frissíthetők az időzítők. Ha `RegisterTimer` a hívása újra megtörténik, egy új időzítő lesz regisztrálva.

Minden időzítő leáll, ha a színész inaktiválva van a szemetet tartalmazó gyűjtemény részeként. Ezt követően nem hívhat meg időzítő-visszahívást. Emellett a szereplők futtatókörnyezete nem őrzi meg az Inaktiválás előtt futó időzítők adatait. Ez a színész a jövőben az újraaktiváláskor szükséges időzítők regisztrálására szolgál. További információkért lásd a [Actors Garbage gyűjtemény](service-fabric-reliable-actors-lifecycle.md)című szakaszát.

## <a name="actor-reminders"></a>Színészi emlékeztetők
Az emlékeztetők olyan mechanizmusok, amelyekkel az állandó visszahívás aktiválható egy adott színészen a megadott időpontokban. A funkcióik hasonlóak az időzítőhöz. Az időzítővel ellentétben azonban az emlékeztetők minden körülmények között aktiválva lesznek, amíg a színész kifejezetten törli a regisztrációt, vagy a szereplő explicit módon törölve lett. Az emlékeztetőket kifejezetten a színészek inaktiválásai és feladatátvétele váltja ki, mert a szereplők futtatókörnyezete továbbra is információt tartalmaz a színészi szolgáltató által használt emlékeztetőről. Az időzítővel ellentétben a meglévő emlékeztetőket úgy is frissítheti, ha a regisztrációs metódust ( `RegisterReminderAsync` ) újra meghívja ugyanazzal a *reminderName*.

> [!NOTE]
> Az emlékeztetők megbízhatósága a szereplők állami szolgáltatója által biztosított állapot-megbízhatósági garanciákkal van kötve. Ez azt jelenti, hogy azoknál a szereplőknél, amelyek állapotának megőrzése nem értékre van *állítva, a* rendszer nem hajtja végre az emlékeztetőket a feladatátvétel után.

Egy emlékeztető regisztrálásához a színész meghívja az [`RegisterReminderAsync`](/dotnet/api/microsoft.servicefabric.actors.runtime.actorbase.registerreminderasync#remarks) alaposztályon megadott metódust, ahogy az az alábbi példában is látható:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

Ebben a példában `"Pay cell phone bill"` a emlékeztető neve. Ez egy karakterlánc, amelyet a színész az emlékeztetők egyedi azonosítására használ. `BitConverter.GetBytes(amountInDollars)`A (C#) Az emlékeztetőhöz társított környezet. A rendszer az emlékeztető visszahívására, azaz `IRemindable.ReceiveReminderAsync` (C#) vagy `Remindable.receiveReminderAsync` (Java) argumentumként adja vissza a szereplőnek.

Az emlékeztetőket használó résztvevőknek be kell vezetniük a `IRemindable` felületet az alábbi példában látható módon.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Emlékeztető kiváltásakor a Reliable Actors futtatókörnyezet a  `ReceiveReminderAsync` (z) (C#) vagy `receiveReminderAsync` (Java) metódust hívja meg a színészen. Egy színész több emlékeztetőt is regisztrálhat, a `ReceiveReminderAsync` (C#) vagy a `receiveReminderAsync` (Java) metódust pedig akkor hívja meg, ha bármelyik emlékeztetőt elindítják. A színész használhatja a `ReceiveReminderAsync` (C#) vagy `receiveReminderAsync` (Java) metódusban átadott emlékeztető nevét, hogy kiderítse, melyik emlékeztetőt aktiválták.

A szereplők futtatókörnyezete a `ReceiveReminderAsync` (C#) vagy `receiveReminderAsync` (Java) hívás befejeződése után menti a szereplő állapotát. Ha az állapot mentésekor hiba történik, akkor a rendszer inaktiválja a Actor objektumot, és egy új példányt aktivál.

Egy emlékeztető regisztrációjának megszüntetéséhez a színész meghívja a `UnregisterReminderAsync` (C#) vagy a `unregisterReminderAsync` (Java) metódust, ahogy az alábbi példákban is látható.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Ahogy fent látható, a `UnregisterReminderAsync` (c#) vagy a `unregisterReminderAsync` (Java) metódus fogad egy `IActorReminder` (c#) vagy `ActorReminder` (Java) felületet. A Actor alaposztálya a `GetReminder` (c#) vagy `getReminder` (Java) metódust támogatja, amely a (z `IActorReminder` ) (c#) vagy (Java) felület lekérésére használható a `ActorReminder` felszólítás nevének megadásával. Ez azért hasznos, mert a szereplőnek nem kell megtartania a (c#) vagy (Java `IActorReminder` `ActorReminder` `RegisterReminder` `registerReminder` ) metódus hívásával visszaadott (c#) vagy (Java) felületet.

## <a name="next-steps"></a>Következő lépések
Ismerje meg a megbízható Actors eseményeket és újbóli belépés:
* [Színészi események](service-fabric-reliable-actors-events.md)
* [Actor újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
