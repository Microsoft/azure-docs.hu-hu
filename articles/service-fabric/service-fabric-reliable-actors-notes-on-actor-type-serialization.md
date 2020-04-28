---
title: Reliable Actors a szereplők típusának szerializálására vonatkozó megjegyzések
description: A Service Fabric Reliable Actors állapotok és felületek definiálásához használható szerializálható osztályok definiálásának alapvető követelményeit tárgyalja
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 876c4f5f45ff6c81a53274cf32e8bebecc1acfce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75349308"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Megjegyzések Service Fabric Reliable Actors típus szerializálásához
Az összes metódus argumentumai, a színészi felületen az egyes metódusok által visszaadott tevékenységek eredményei, valamint a színészek állapot-kezelőjében tárolt objektumok esetében a [szerializálható adategyezménynek](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)kell szerepelnie. Ez a [színészi esemény-illesztőfelületekben](service-fabric-reliable-actors-events.md)definiált metódusok argumentumait is érinti. (A színészi esemény felületi módszerei mindig semmisnek térnek vissza.)

## <a name="custom-data-types"></a>Egyéni adattípusok
Ebben a példában a következő Actor Interface olyan metódust határoz meg, amely egy nevű `VoicemailBox`egyéni adattípust ad vissza:

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

Ebben a példában az objektum `VoicemailBox` szerializálása a következő esetekben történik:

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


## <a name="next-steps"></a>További lépések
* [A Actor életciklusa és a szemét gyűjtése](service-fabric-reliable-actors-lifecycle.md)
* [Színészi időzítők és emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Színészi események](service-fabric-reliable-actors-events.md)
* [Actor újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
* [Actor-polimorfizmus és objektumorientált tervezési minták](service-fabric-reliable-actors-polymorphism.md)
* [Színészi diagnosztika és Teljesítményfigyelés](service-fabric-reliable-actors-diagnostics.md)
