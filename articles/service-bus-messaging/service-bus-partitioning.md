---
title: Particionált Azure Service Bus-várólisták és-témakörök létrehozása | Microsoft Docs
description: Ismerteti, hogyan lehet Service Bus várólistákat és témaköröket több Message Broker használatával particionálni.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c500a69f853b11437a0dcaa48213fe3a84da53b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796635"
---
# <a name="partitioned-queues-and-topics"></a>Particionált üzenetsorok és témakörök

Az Azure Service Bus több üzenetkezelőt alkalmaz az üzenetek feldolgozására és több üzenetkezelési tárolóra az üzenetek tárolásához. Egy hagyományos üzenetsor vagy témakör kezelése egyetlen Message Broker által történik, és egy üzenetkezelő tárolóban tárolódik. A Service Bus *partíciók* lehetővé teszik a várólisták és témakörök, vagy az *üzenetkezelési entitások* particionálását több üzenet-átvitelszervező és üzenetkezelési tároló között. A particionálás azt jelenti, hogy egy particionált entitás teljes átviteli sebességét már nem korlátozza egyetlen Message Broker vagy üzenetküldési tároló teljesítménye. Emellett az üzenetküldési tároló átmeneti kimaradása nem jeleníti meg a particionált üzenetsor vagy a témakör nem érhető el. A particionált várólisták és témakörök az összes speciális Service Bus funkciót tartalmazhatják, például a tranzakciók és a munkamenetek támogatását.

> [!NOTE]
> A particionálás az alapszintű vagy standard SKU-ban található összes várólista és témakör esetében elérhető az entitások létrehozásakor. A prémium szintű üzenetkezelési SKU esetében nem érhető el, de a prémium szintű névterekben korábban már meglévő particionált entitások továbbra is a várt módon működnek.
 
A particionálási lehetőség nem módosítható bármely meglévő várólistán vagy témakörön; az entitás létrehozásakor csak a beállítást állíthatja be.

## <a name="how-it-works"></a>Működés

Minden particionált üzenetsor vagy témakör több partíciót tartalmaz. Az egyes partíciókat egy másik üzenetkezelő tároló tárolja, és egy másik Message Broker kezeli. Amikor üzenetet küld egy particionált várólistára vagy témakörre, Service Bus az üzenetet az egyik partícióhoz rendeli. A kijelölést véletlenszerűen Service Bus vagy egy, a küldő által megadható partíciós kulcs használatával végzi.

Ha egy ügyfél egy particionált várólistáról vagy egy particionált témakör előfizetéséről szeretne üzenetet kapni, Service Bus lekérdezi az összes partíciót az üzenetekhez, majd visszaadja az összes üzenetküldési áruházból a fogadónak beszerzett első üzenetet. Service Bus gyorsítótárazza a többi üzenetet, és visszaadja őket, ha további fogadási kéréseket kap. A fogadó ügyfél nem ismeri a particionálást; egy particionált várólista vagy témakör ügyféloldali viselkedése (például olvasás, Befejezés, késleltetés, kézbesítetlen levelek, előre beolvasás) azonos a normál entitás viselkedésével.

A nem particionált entitások betekintés művelete mindig a legrégebbi üzenetet adja vissza, de nem particionált entitáson. Ehelyett egy olyan partíció legrégebbi üzenetét adja vissza, amelynek a Message Broker először válaszol. Nem garantálható, hogy a visszaadott üzenet az összes partíció legrégebbi tagja. 

Az üzenetek elküldésekor vagy egy particionált várólistából vagy témakörből való üzenetküldés esetén nincs további díj.

> [!NOTE]
> A betekintés művelet a partíció legrégebbi üzenetét adja vissza a sorszám alapján. Particionált entitások esetén a sorozatszám a partícióhoz viszonyítva jelenik meg. További információ: [üzenetek sorrendbe rendezése és időbélyegek](../service-bus-messaging/message-sequencing.md).

## <a name="enable-partitioning"></a>Particionálás engedélyezése

Ha Azure Service Bus használatával particionált várólistákat és témaköröket szeretne használni, használja az Azure SDK 2,2-as vagy újabb verzióját, vagy adja meg a `api-version=2013-10` http-kérelmekben vagy azt később.

### <a name="standard"></a>Standard

A normál üzenetkezelési szinten Service Bus várólistákat és témaköröket hozhat létre 1, 2, 3, 4 vagy 5 GB méretű méretben (az alapértelmezett érték 1 GB). Ha engedélyezve van a particionálás, a Service Bus 16 példányt (16 partíciót) hoz létre az entitásból, és mindegyiket azonos méretben adja meg. Így ha egy 5 GB méretű, 16 partíciót tartalmazó várólistát hoz létre, akkor a maximális várólista mérete (5 \* 16) = 80 GB lesz. Az adott entitás **Áttekintés** paneljén megtekintheti a particionált üzenetsor vagy témakör maximális méretét a [Azure Portal][Azure portal]bejegyzésének megkeresésével.

### <a name="premium"></a>Prémium

A prémium szintű névtérben a particionálási entitások nem támogatottak. Azonban továbbra is létrehozhat Service Bus várólistákat és témaköröket 1, 2, 3, 4, 5, 10, 20, 40 vagy 80 GB méretű méretben (az alapértelmezett érték 1 GB). A várólista vagy témakör méretének megtekintéséhez tekintse meg az adott entitás **Áttekintés** paneljén a [Azure Portal][Azure portal]bejegyzését.

### <a name="create-a-partitioned-entity"></a>Particionált entitás létrehozása

Particionált üzenetsor vagy témakör több módon is létrehozható. Amikor létrehozza az üzenetsor vagy a témakört az alkalmazásból, engedélyezheti a várólista vagy a témakör particionálását a [QueueDescription. EnablePartitioning][QueueDescription.EnablePartitioning] vagy a [TopicDescription. EnablePartitioning][TopicDescription.EnablePartitioning] tulajdonság **true** értékre állításával. Ezeket a tulajdonságokat a várólista vagy a témakör létrehozásakor kell megadni, és csak a régebbi [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) könyvtárban érhetők el. Ahogy korábban már említettük, nem lehet módosítani ezeket a tulajdonságokat egy meglévő várólistán vagy témakörben. Például:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Azt is megteheti, hogy létrehoz egy particionált várólistát vagy témakört a [Azure Portal][Azure portal]. Amikor létrehoz egy várólistát vagy témakört a portálon, a rendszer alapértelmezés szerint bejelöli a **particionálás engedélyezése** lehetőséget a várólista vagy a témakör **létrehozása** párbeszédpanelen. Ezt a beállítást csak a standard szintű entitásokban lehet letiltani; a prémium szintű particionálás nem támogatott, és a jelölőnégyzetnek nincs hatása. 

## <a name="use-of-partition-keys"></a>Partíciós kulcsok használata

Ha egy üzenetet particionált várólistába vagy témakörbe várólistán lévő, Service Bus ellenőrzi a partíciós kulcs jelenlétét. Ha az egyiket találja, a partíció a kulcs alapján van kiválasztva. Ha nem talál partíciós kulcsot, a partíció a belső algoritmus alapján van kiválasztva.

### <a name="using-a-partition-key"></a>Partíciós kulcs használata

Bizonyos forgatókönyvek, például a munkamenetek vagy a tranzakciók esetében az üzenetek egy adott partíción való tárolásához szükségesek. Az összes forgatókönyvhöz szükség van egy partíciós kulcs használatára. Az azonos partíciós kulcsot használó összes üzenet ugyanahhoz a partícióhoz van rendelve. Ha a partíció átmenetileg nem érhető el, Service Bus hibát ad vissza.

A forgatókönyvtől függően a rendszer különböző üzenet-tulajdonságokat használ partíciós kulcsként:

**Munkamenet**-azonosító: Ha egy üzenetben a [munkamenet](/dotnet/api/microsoft.azure.servicebus.message.sessionid) -azonosító tulajdonság be van állítva, akkor Service Bus a **munkamenet** -azonosítóját használja partíciós kulcsként. Így az ugyanahhoz a munkamenethez tartozó összes üzenetet ugyanaz az üzenet-átvitelszervező kezeli. A munkamenetek lehetővé teszik a Service Bus számára az üzenetek rendezésének biztosítását, valamint a munkamenet-állapotok egységességét.

**PartitionKey**: Ha egy üzenet rendelkezik a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonsággal, de a [munkamenet](/dotnet/api/microsoft.azure.servicebus.message.sessionid) -azonosító tulajdonság nem lett beállítva, akkor a Service Bus a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság értékét használja partíciós kulcsként. Ha az üzenetben a [munkamenet](/dotnet/api/microsoft.azure.servicebus.message.sessionid) -azonosító és a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság is meg van adva, mindkét tulajdonságnak azonosnak kell lennie. Ha a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság értéke eltérő értékre van állítva, mint a [munkamenet](/dotnet/api/microsoft.azure.servicebus.message.sessionid) -tulajdonság, a Service Bus érvénytelen műveleti kivételt ad vissza. A [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonságot akkor kell használni, ha a küldő nem munkamenet-kompatibilis tranzakciós üzeneteket küld. A partíciós kulcs biztosítja, hogy a tranzakción belül küldött összes üzenetet ugyanazon üzenetkezelő közvetítő kezeli.

**MessageID**: Ha a várólista [vagy a témakör](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) értéke **true (igaz** ), és a [munkamenet](/dotnet/api/microsoft.azure.servicebus.message.sessionid) -azonosító vagy a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonságok nincsenek beállítva, akkor a [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) tulajdonság értéke a partíciós kulcsként szolgál. (A Microsoft .NET és a AMQP könyvtárak automatikusan hozzárendelnek egy üzenet-azonosítót, ha a küldő alkalmazás nem.) Ebben az esetben az azonos üzenet összes példányát ugyanaz az üzenet-átvitelszervező kezeli. Ez az azonosító lehetővé teszi az Service Bus számára az ismétlődő üzenetek észlelését és eltávolítását. Ha a [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) tulajdonság értéke nem True ( **igaz**), Service Bus nem veszi figyelembe a [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) tulajdonságot partíciós kulcsként.

### <a name="not-using-a-partition-key"></a>Nem partíciós kulcs használata

Partíciós kulcs hiányában a Service Bus a particionált üzenetsor vagy témakör összes partíciója számára kiosztja az üzeneteket egy ciklikus időszeletelési módon. Ha a kiválasztott partíció nem érhető el, Service Bus hozzárendeli az üzenetet egy másik partícióhoz. Így a küldési művelet az üzenetküldési tároló ideiglenes nem rendelkezésre állása ellenére is sikeres lesz. Azonban nem fogja megvalósítani a partíciós kulcs által biztosított garantált sorrendet.

A rendelkezésre állás (partíciós kulcs) és a konzisztencia (partíciós kulcs használatával) közötti kompromisszum alaposabb megvitatására tekintse meg [ezt a cikket](../event-hubs/event-hubs-availability-and-consistency.md). Ezek az információk egyformán érvényesek a particionált Service Bus entitásokra.

Ahhoz, hogy Service Bus elegendő idő legyen ahhoz, hogy az üzenetet egy másik partícióba sorba helyezni, az üzenetet küldő ügyfél által megadott [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) -értéknek 15 másodpercnél nagyobbnak kell lennie. Javasoljuk, hogy a [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) tulajdonságot az alapértelmezett 60 másodperc értékre állítsa be.

Egy partíciós kulcs "PIN-kód" üzenetet küld egy adott partícióra. Ha a partíciót tároló üzenetkezelő áruház nem érhető el, Service Bus hibát ad vissza. Partíciós kulcs hiányában Service Bus választhat egy másik partíciót, és a művelet sikeres lesz. Ezért azt javasoljuk, hogy csak akkor adjon meg egy partíciós kulcsot, ha szükséges.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Speciális témakörök: tranzakciók használata particionált entitásokkal

A tranzakciók keretében küldött üzenetekben meg kell adni egy partíciókulcsot. A kulcs a következő tulajdonságok egyike lehet: [munkamenet](/dotnet/api/microsoft.azure.servicebus.message.sessionid)-azonosító, [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)vagy [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid). A tranzakció részeként küldött összes üzenetnek ugyanazt a partíciós kulcsot kell megadnia. Ha egy tranzakción belül partíciós kulcs nélkül próbál üzenetet küldeni, Service Bus érvénytelen műveleti kivételt ad vissza. Ha ugyanazon a tranzakción belül próbál több üzenetet küldeni, amely különböző partíciós kulcsokkal rendelkezik, Service Bus érvénytelen műveleti kivételt ad vissza. Például:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

Ha a partíciós kulcsként szolgáló tulajdonságok bármelyike be van állítva, Service Bus az üzenetet egy adott partícióhoz. Ez a viselkedés akkor fordul elő, ha a rendszer tranzakciót használ. Azt javasoljuk, hogy ne határozzon meg partíciós kulcsot, ha nincs rá szükség.

## <a name="using-sessions-with-partitioned-entities"></a>Munkamenetek használata particionált entitásokkal

Ha tranzakciós üzenetet szeretne küldeni egy munkamenet-kompatibilis témakörbe vagy várólistába, az üzenetnek a [munkamenet-azonosító tulajdonságot](/dotnet/api/microsoft.azure.servicebus.message.sessionid) kell beállítania. Ha a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság is meg van adva, a paraméternek meg kell egyeznie a [munkamenet](/dotnet/api/microsoft.azure.servicebus.message.sessionid) -azonosító tulajdonsággal. Ha különböznek, Service Bus érvénytelen műveleti kivételt ad vissza.

A normál (nem particionált) várólistákkal vagy témakörökkel ellentétben egyetlen tranzakció használatával nem lehet több üzenetet küldeni a különböző munkameneteknek. Ha a rendszer megkísérelte, a Service Bus érvénytelen műveleti kivételt ad vissza. Például:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatikus üzenet-továbbítás particionált entitásokkal

A Service Bus támogatja az automatikus üzenetküldést a, a rendszerre vagy a particionált entitások között. Az automatikus üzenetküldés engedélyezéséhez állítsa be a [QueueDescription. továbbítás][QueueDescription.ForwardTo] tulajdonságot a forrás-várólistán vagy az előfizetésen. Ha az üzenet egy partíciós kulcsot ([munkamenet](/dotnet/api/microsoft.azure.servicebus.message.sessionid)-azonosító, [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)vagy [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid)) határoz meg, akkor a rendszer a cél entitáshoz használja a partíciós kulcsot.

## <a name="considerations-and-guidelines"></a>Szempontok és irányelvek
* **Magas konzisztencia-funkciók**: Ha egy entitás olyan szolgáltatásokat használ, mint például a munkamenetek, a duplikált észlelés vagy a particionálási kulcs explicit vezérlése, akkor az üzenetkezelési műveletek mindig adott partícióhoz lesznek irányítva. Ha a partíciók bármelyike nagy forgalmú, vagy a mögöttes tároló nem kifogástalan állapotú, akkor ezek a műveletek meghiúsulnak, és a rendelkezésre állás csökken. Összességében a konzisztencia még jóval meghaladja a nem particionált entitásokat. a forgalomnak csak egy részhalmaza tapasztal problémát, az összes adatforgalomtól függetlenül. További információkért tekintse meg a [rendelkezésre állási és konzisztencia-vitát](../event-hubs/event-hubs-availability-and-consistency.md).
* **Felügyelet**: a létrehozás, frissítés és törlés műveleteit az entitás összes partícióján el kell elvégezni. Ha bármelyik partíció állapota nem megfelelő, a művelet hibákat eredményezhet. A Get művelethez az egyes partíciók adatait, például az üzenetek számát összesíteni kell. Ha bármelyik partíció sérült, az entitás rendelkezésre állási állapota korlátozottként jelent meg.
* **Kis mennyiségű üzenet forgatókönyvei**: ilyen esetekben – különösen a HTTP protokoll használata esetén – előfordulhat, hogy több fogadási műveletet kell végrehajtania az összes üzenet beszerzéséhez. A fogadási kérelmek esetében az előtér végrehajt egy fogadást az összes partíción, és gyorsítótárazza az összes kapott választ. Egy további fogadási kérelem ugyanezen a kapcsolatban is kihasználhatja ezt a gyorsítótárazást, és a fogadási késések alacsonyabbak lesznek. Ha azonban több kapcsolattal rendelkezik, vagy HTTP-t használ, minden kérelemhez új kapcsolatot létesít. Ezért nem garantálható, hogy az ugyanazon a csomóponton található. Ha az összes meglévő üzenet zárolva van, és egy másik előtérben van gyorsítótárazva, a fogadási művelet **Null** értéket ad vissza. Az üzenetek végül lejárnak, és újra megkapják őket. A HTTP Keep-Alive használata ajánlott. Kis mennyiségű forgatókönyvek particionálásakor a fogadási műveletek a vártnál több időt vehetnek igénybe. Ezért javasoljuk, hogy ne használjon particionálást ezekben a forgatókönyvekben. Törölje a meglévő particionált entitásokat, és a teljesítmény növelése érdekében a particionálás letiltásával hozza létre újra.
* **Tallózás/betekintés üzenetek**: csak a régebbi [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) könyvtárban érhető el. A [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nem mindig adja vissza a [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) tulajdonságban megadott üzenetek számát. Ennek a viselkedésnek két gyakori oka van. Ennek egyik oka az, hogy az üzenetek gyűjteményének összesített mérete meghaladja a maximálisan megengedett 256 KB-os méretet. A másik ok az, hogy ha az üzenetsor vagy a témakör [EnablePartitioning tulajdonsága](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) **igaz** értékre van állítva, akkor előfordulhat, hogy a partíciók nem rendelkeznek elegendő üzenettel a kért számú üzenet végrehajtásához. Általánosságban elmondható, hogy ha egy alkalmazás adott számú üzenetet szeretne kapni, a [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) -t ismételten meg kell hívnia, amíg meg nem jelenik az üzenetek száma, vagy nincs több betekintést igénylő üzenet. További információk, például a kódok mintái: [QueueClient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) vagy [SubscriptionClient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API-dokumentáció.

## <a name="latest-added-features"></a>Legújabb hozzáadott funkciók

* A hozzáadási vagy eltávolítási szabály már támogatja a particionált entitásokat. A nem particionált entitások eltérően ezek a műveletek nem támogatottak a tranzakciók alatt. 
* A AMQP mostantól támogatja a particionált entitásokból érkező és onnan érkező üzenetek küldését és fogadását.
* A AMQP mostantól támogatja a következő műveleteket: [kötegelt küldés](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [kötegelt fogadás](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [fogadás sorszám alapján](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [betekintés](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [megújítás zárolása](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [üzenet ütemezése](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [ütemezett üzenet megszakítása](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [szabály hozzáadása](/dotnet/api/microsoft.azure.servicebus.ruledescription), [szabály eltávolítása](/dotnet/api/microsoft.azure.servicebus.ruledescription), [munkamenet megújítása zárolás](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [munkamenet állapotának](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate) [beolvasása, munkamenet-állapot](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)és [enumerálási](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions)munkamenetek.

## <a name="partitioned-entities-limitations"></a>Particionált entitások korlátai

Jelenleg Service Bus a következő korlátozásokat szabja meg a particionált várólisták és témakörök esetében:

* A particionált várólisták és témakörök nem támogatottak a prémium szintű üzenetkezelési szinten. A Premier szintű munkamenetek az munkamenet-azonosítók használatával támogatottak. 
* A particionált várólisták és témakörök nem támogatják a különböző munkamenetekhez tartozó üzenetek küldését egyetlen tranzakcióban.
* A Service Bus névterenként jelenleg 100 particionált üzenetsort vagy témakört képes kezelni. Minden particionált üzenetsor vagy témakör a 10 000 entitások névterében számítja fel a kvótát (a prémium szintű csomagra nem vonatkozik).

## <a name="next-steps"></a>Következő lépések

Olvassa el a AMQP 1,0 üzenetküldési specifikációjának alapvető fogalmait a [AMQP 1,0 protokoll útmutatójában](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
