---
title: Reliable Actors a szereplők típusának szerializálására vonatkozó megjegyzések
description: A Service Fabric Reliable Actors állapotok és felületek definiálásához használható szerializálható osztályok definiálásának alapvető követelményeit tárgyalja
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2a1118e394046b217a288663659a2c910098e992
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576009"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Megjegyzések Service Fabric Reliable Actors típus szerializálásához
Az összes metódus argumentumai, a színészi felületen az egyes metódusok által visszaadott tevékenységek eredményei, valamint a színészek állapot-kezelőjében tárolt objektumok esetében a [szerializálható adategyezménynek](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)kell szerepelnie. Ez a [színészi esemény-illesztőfelületekben](service-fabric-reliable-actors-events.md)definiált metódusok argumentumait is érinti. (A színészi esemény felületi módszerei mindig semmisnek térnek vissza.)

## <a name="custom-data-types"></a>Egyéni adattípusok
Ebben a példában a következő Actor Interface olyan metódust határoz meg, amely egy nevű egyéni adattípust ad vissza `VoicemailBox` :

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

A felületet egy olyan színész valósítja meg, amely a State Managert használja az `VoicemailBox` objektumok tárolásához:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

Ebben a példában az `VoicemailBox` objektum szerializálása a következő esetekben történik:

* Az objektum egy Actor-példány és egy hívó között lesz továbbítva.
* Az objektum a State Managerbe kerül, ahol a lemez tárolva van, és más csomópontokra replikálva van.

A megbízható szereplők keretrendszere DataContract szerializálást használ. Ezért az egyéni adatobjektumokat és azok tagjait a **DataContract** és a **DataMember** attribútummal kell kiegészíteni.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Következő lépések
* [A Actor életciklusa és a szemét gyűjtése](service-fabric-reliable-actors-lifecycle.md)
* [Színészi időzítők és emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Színészi események](service-fabric-reliable-actors-events.md)
* [Actor újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
* [Actor-polimorfizmus és objektumorientált tervezési minták](service-fabric-reliable-actors-polymorphism.md)
* [Színészi diagnosztika és Teljesítményfigyelés](service-fabric-reliable-actors-diagnostics.md)
