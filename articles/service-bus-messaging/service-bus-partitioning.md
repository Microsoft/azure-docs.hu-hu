---
title: Particionált Azure Service Bus üzenetsorok és témakörök létrehozása | Microsoft Docs
description: Leírja, hogyan particionálhat Service Bus üzenetsorok és témakörök particionálását több üzenetközvetítő használatával.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: bc41bcf31102b19dd35f62452b956faf4f029551
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750913"
---
# <a name="partitioned-queues-and-topics"></a>Particionált üzenetsorok és témakörök

Azure Service Bus több üzenetközvetítőt alkalmaz az üzenetek feldolgozásához, és több üzenetkezelési tárolót az üzenetek tárolására. A hagyományos üzenetsorokat vagy témaköröket egyetlen üzenetközvetítő kezeli, és egy üzenetkezelési tárolóban tárolja. Service Bus *partíciók* lehetővé teszik az üzenetsorok és témakörök, más szóval az üzenetküldési entitások több üzenetközvetítő és üzenetkezelési tároló közötti particionálását.  A particionálás azt jelenti, hogy a particionált entitások teljes átviteli sebességét már nem korlátozza egyetlen üzenetközvetítő vagy üzenetkezelési tároló teljesítménye. Emellett az üzenetkezelési tároló ideiglenes kimaradása nem teszi elérhetetlenné a particionált üzenetsort vagy témakört. A particionált üzenetsorok és témakörök tartalmazhatnak minden speciális Service Bus funkciót, például a tranzakciók és munkamenetek támogatását.

> [!NOTE]
> A particionálás az entitások létrehozásakor érhető el az alapszintű vagy standard SKUS-ban lévő összes üzenetsorhoz és témakörhez. A prémium szintű üzenetkezelési termékváltozathoz nem érhető el, de a premium névterek korábban már meglévő particionált entitásai továbbra is a várt módon működnek.
 
Meglévő üzenetsoron vagy témakören nem lehet módosítani a particionálási beállítást; A beállítást csak az entitás létrehozásakor állíthatja be.

## <a name="how-it-works"></a>Működés

Minden particionált üzenetsor vagy témakör több partícióból áll. Minden partíció egy másik üzenetkezelési tárolóban van tárolva, és egy másik üzenetközvetítő kezeli. Amikor üzenetet küld egy particionált üzenetsorba vagy témakörbe, a Service Bus hozzárendeli az üzenetet az egyik partícióhoz. A kiválasztás véletlenszerűen történik a Service Bus vagy egy, a küldő által megadható partíciókulcs használatával.

Ha egy ügyfél üzenetet szeretne kapni egy particionált üzenetsorból vagy egy előfizetésből egy particionált témakörbe, az Service Bus lekérdezi az összes partíciót az üzenetekért, majd visszaadja az első üzenetet, amely az üzenetkezelési tárolók bármelyikéből a fogadóhoz kerül. Service Bus gyorsítótárazza a többi üzenetet, és visszaadja azokat, amikor további fogadási kéréseket kap. A fogadó ügyfél nem ismeri a particionálást; a particionált üzenetsorok vagy témakörök ügyféloldali viselkedése (például olvasás, teljes, halasztás, holtmag, előzetes befogyás) megegyezik a normál entitások viselkedésével.

A nem particionált entitások betekintés művelete mindig a legrégebbi üzenetet adja vissza, particionált entitáson azonban nem. Ehelyett az egyik olyan partíció legrégebbi üzenetét adja vissza, amelynek üzenetközvetítőja először válaszolt. Nem garantálható, hogy a visszaadott üzenet a legrégebbi az összes partíción. 

Ha üzenetet küld egy particionált üzenetsornak vagy témakörnek, vagy üzenetet fogad onnan, nincs további költség.

> [!NOTE]
> A betekintés művelet a legrégebbi üzenetet adja vissza a partícióból a sorszáma alapján. Particionált entitások esetében a sorszám a partícióhoz viszonyítva lesz kibocsátva. További információ: [Üzenet-szekvenálás és időbélyegek.](../service-bus-messaging/message-sequencing.md)

## <a name="enable-partitioning"></a>Particionálás engedélyezése

Ha particionált üzenetsorokat és témaköröket Azure Service Bus, használja az Azure SDK 2.2-es vagy újabb verzióját, vagy adja meg a HTTP-kérések között az vagy újabb `api-version=2013-10` verziót.

### <a name="standard"></a>Standard

A Standard üzenetkezelési szinten 1 Service Bus, 2, 3, 4 vagy 5 GB méretű üzenetsorokat és témaköröket hozhat létre (az alapértelmezett érték 1 GB). Ha engedélyezve van a particionálás, a Service Bus 16 példányt (16 partíciót) hoz létre az entitásból, amelyek mindegyikének azonos mérete van megadva. Így ha 5 GB méretű üzenetsort hoz létre, 16 partícióval a maximális üzenetsorméret (5 \* 16) = 80 GB lesz. A particionált üzenetsor vagy témakör maximális méretét az entitás [][Azure portal]Áttekintés paneljének Azure Portal  bejegyzésében láthatja.

### <a name="premium"></a>Prémium

Prémium szintű névtérben az entitások particionálása nem támogatott. Service Bus Azonban továbbra is létrehozhat 1, 2, 3, 4, 5, 10, 20, 40 vagy 80 GB méretű üzenetsorokat és témaköröket (az alapértelmezett érték 1 GB). Az üzenetsor vagy témakör méretét az entitás Áttekintés [][Azure portal]paneljének Azure Portal bejegyzésében  láthatja.

### <a name="create-a-partitioned-entity"></a>Particionált entitás létrehozása

Particionált üzenetsort vagy témakört többféleképpen is létrehozhat. Amikor az alkalmazásból hozza létre az üzenetsort vagy témakört, engedélyezheti a particionálást az üzenetsor vagy témakör számára a [QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] vagy [a TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] tulajdonság true (igaz) értékre való beállításával.  Ezeket a tulajdonságokat az üzenetsor vagy témakör létrehozásakor kell beállítani, és csak a régebbi [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) kódtárban érhetők el. Ahogy korábban már említettük, ezek a tulajdonságok nem változtathatóak meg egy meglévő üzenetsoron vagy témakörben. Például:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Másik lehetőségként létrehozhat egy particionált üzenetsort vagy témakört a [Azure Portal.][Azure portal] Amikor létrehoz egy üzenetsort vagy  témakört a portálon,  az üzenetsor vagy témakör Létrehozás párbeszédpanelén alapértelmezés szerint be van jelölve a Particionálás engedélyezése lehetőség. Ezt a beállítást csak Standard szintű entitásban tilthatja le; A prémium szintű particionálás nem támogatott, és a jelölőnégyzetnek nincs hatása. 

## <a name="use-of-partition-keys"></a>Partíciókulcsok használata

Ha egy üzenetet particionált üzenetsorba vagy témakörbe sorba Service Bus, a rendszer ellenőrzi, hogy van-e partíciókulcs. Ha talál ilyet, a kulcs alapján választja ki a partíciót. Ha nem talál partíciókulcsot, egy belső algoritmus alapján választja ki a partíciót.

### <a name="using-a-partition-key"></a>Partíciókulcs használata

Bizonyos forgatókönyvekhez, például munkamenetekhez vagy tranzakciókhoz egy adott partíción kell tárolni az üzeneteket. Ezekhez a forgatókönyvekhez partíciókulcsra van szükség. Minden olyan üzenet, amely ugyanazt a partíciókulcsot használja, ugyanhez a partícióhoz van rendelve. Ha a partíció átmenetileg nem érhető el, Service Bus hibaüzenetet ad vissza.

A forgatókönyvtől függően a rendszer különböző üzenettulajdonságokat használ partíciókulcsként:

**SessionId:** Ha egy üzenet [sessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonsága be van állítva, Service Bus a **SessionID** partíciókulcsot használja. Így az ugyanerhez a munkamenethez tartozó összes üzenetet ugyanaz az üzenetközvetítő kezeli. A munkamenetek Service Bus biztosítják az üzenetek sorrendjét, valamint a munkamenet-államok konzisztenciáját.

**PartitionKey:** Ha egy üzenet [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonságot tartalmaz, de a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonságot nem, akkor a Service Bus [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonságértéket használja partíciókulcsként. Ha az üzenetben a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) és a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság is be van állítva, mindkét tulajdonságnak azonosnak kell lennie. Ha a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) tulajdonságtól eltérő értékre van beállítva, Service Bus művelet kivételt ad vissza. A [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonságot akkor kell használni, ha a küldő nem munkamenet-adatokat küldő tranzakciós üzeneteket küld. A partíciókulcs biztosítja, hogy a tranzakción belül küldött összes üzenetet ugyanaz az üzenetközvetítő kezeli.

**MessageId:** Ha az üzenetsor vagy témakör [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) tulajdonsága **true (igaz)** értékre van állítva, és a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) vagy [a PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság nincs beállítva, akkor a [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) tulajdonság értéke szolgál partíciókulcsként. (A Microsoft .NET AMQP-kódtárak automatikusan hozzárendelnek egy üzenetazonosítót, ha a küldő alkalmazás nem.) Ebben az esetben ugyanannak az üzenetnek az összes példányát ugyanaz az üzenetközvetítő kezeli. Ez az azonosító lehetővé Service Bus az ismétlődő üzenetek észlelését és kiküszöbölését. Ha a [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) tulajdonság nincs true (igaz) értékre **állítva,** Service Bus a [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) tulajdonságot nem veszi partíciókulcsnak.

### <a name="not-using-a-partition-key"></a>Nem használ partíciókulcsot

Partíciókulcs hiányában a Service Bus ciklikus időbesorolt módon osztja el az üzeneteket a particionált üzenetsor vagy témakör összes partíciója között. Ha a kiválasztott partíció nem érhető el, Service Bus az üzenetet egy másik partícióhoz rendeli. Így a küldési művelet akkor is sikeres lesz, ha egy üzenettároló átmenetileg nem érhető el. Azonban nem éri el a partíciókulcs által biztosított garantált sorrendet.

A rendelkezésre állás (partíciókulcs nélkül) és a konzisztencia (partíciókulcs használatával) közötti különbségeket ebben a cikkben talál [részletesen.](../event-hubs/event-hubs-availability-and-consistency.md) Ez az információ egyformán vonatkozik a particionált Service Bus entitásra.

Ahhoz, Service Bus időt biztosítsunk az üzenet egy másik partícióba való végrehajtásához, az üzenetet jelölő ügyfél által megadott [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) értéknek 15 másodpercnél nagyobbnak kell lennie. Javasoljuk, hogy állítsa az [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) tulajdonságot az alapértelmezett 60 másodperces értékre.

A partíciókulcs "rögzít" egy üzenetet egy adott partíción. Ha a partíciót tároló üzenettároló nem érhető el, Service Bus hibát ad vissza. Partíciókulcs hiányában a Service Bus másik partíciót is választhat, és a művelet sikeres lesz. Ezért azt javasoljuk, hogy csak akkor ad meg partíciókulcsot, ha az szükséges.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Speciális témakörök: tranzakciók használata particionált entitásokkal

A tranzakciók keretében küldött üzenetekben meg kell adni egy partíciókulcsot. A kulcs a következő tulajdonságok egyike lehet: [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)vagy [MessageId.](/dotnet/api/microsoft.azure.servicebus.message.messageid) Az ugyanannak a tranzakciónak a részeként küldött összes üzenetnek ugyanazt a partíciókulcsot kell megadnia. Ha partíciókulcs nélkül próbál üzenetet küldeni egy tranzakción belül, a Service Bus érvénytelen műveleti kivételt ad vissza. Ha több üzenetet próbál küldeni ugyanazon a tranzakción belül, amelyek eltérő partíciókulcsokkal Service Bus érvénytelen műveleti kivételt ad vissza. Például:

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

Ha a partíciókulcsként szolgáló tulajdonságok bármelyike be van állítva, a Service Bus egy adott partícióra tűzi az üzenetet. Ez a viselkedés akkor is előfordul, ha tranzakciót használ. Ha nem szükséges, ne adjon meg partíciókulcsot.

## <a name="using-sessions-with-partitioned-entities"></a>Munkamenetek használata particionált entitásokkal

Ha tranzakciós üzenetet küld egy munkamenet-et kezelő témakörbe vagy üzenetsorba, az [üzenetnek be](/dotnet/api/microsoft.azure.servicebus.message.sessionid) kell állítania a SessionId tulajdonságot. Ha a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) tulajdonság is meg van adva, akkor meg kell egyeznie a [SessionId tulajdonságéval.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Ha eltérnek, a Service Bus érvénytelen műveleti kivételt ad vissza.

A hagyományos (nem particionált) üzenetsorokkal vagy témakörökvel ellentétben egyetlen tranzakcióval nem lehet több üzenetet küldeni különböző munkamenetekbe. Kísérlet esetén a Service Bus érvénytelen műveleti kivételt ad vissza. Például:

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

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatikus üzenetátirányítás particionált entitásokkal

Service Bus támogatja az automatikus üzenetátirányítást a particionált entitások között, illetve az entitások között. Az automatikus üzenetátirányítás engedélyezéséhez állítsa be a [QueueDescription.ForwardTo][QueueDescription.ForwardTo] tulajdonságot a forrás üzenetsoron vagy előfizetésen. Ha az üzenet partíciókulcsot[(SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)vagy [MessageId)](/dotnet/api/microsoft.azure.servicebus.message.messageid)ad meg, a célentitás ezt a partíciókulcsot használja.

## <a name="considerations-and-guidelines"></a>Megfontolandó szempontok és irányelvek
* **Magas** konzisztencia-funkciók: Ha egy entitás olyan szolgáltatásokat használ, mint a munkamenetek, a duplikált elemek észlelése vagy a particionálási kulcs explicit vezérlése, akkor az üzenetkezelési műveletek mindig adott partícióra vannak irányítva. Ha a partíciók bármelyike nagy forgalmat tapasztal, vagy a mögöttes tároló nem megfelelő, akkor ezek a műveletek meghiúsulnak, és a rendelkezésre állás csökken. Összességében a konzisztencia még mindig sokkal magasabb, mint a nem particionált entitások; csak a forgalom egy része tapasztal problémákat, nem pedig az összes forgalom. További információkért tekintse meg a rendelkezésre állás [és a konzisztencia ezen megbeszélését.](../event-hubs/event-hubs-availability-and-consistency.md)
* **Felügyelet:** Az olyan műveleteket, mint a Létrehozás, a Frissítés és a Törlés, az entitás összes partícióját el kell végezni. Ha bármelyik partíció nem működik, akkor ezek a műveletek sikertelenek is lehetnek. A Get művelethez az olyan információkat, mint az üzenetek száma, összesíteni kell az összes partícióból. Ha valamelyik partíció állapota nem megfelelő, az entitás rendelkezésre állási állapota korlátozottként lesz jelentve.
* **Kis mennyiségű üzenetforgatókönyvek:** Ilyen esetekben, különösen a HTTP protokoll használata esetén előfordulhat, hogy több fogadási műveletet kell végrehajtania az összes üzenet beszerzéséhez. A fogadási kérések esetén az előoldal egy fogadást hajt végre az összes partíción, és gyorsítótárazza az összes fogadott választ. Egy későbbi, ugyanazon a kapcsolaton keresztüli fogadási kérés esetén előnyös lenne a gyorsítótárazás, és a késések fogadása alacsonyabb lesz. Ha azonban több kapcsolattal rendelkezik, vagy HTTP-t használ, az minden kéréshez új kapcsolatot hoz létre. Ezért nem garantálható, hogy ugyanazon a csomóponton fog landni. Ha minden meglévő üzenet zárolva van, és egy másik előtere gyorsítótárazva van, a fogadási művelet **null értéket ad vissza.** Az üzenetek végül lejárnak, és újra fogadhatja őket. HTTP életben tartás ajánlott. Ha kis mennyiségű forgatókönyvben használ particionálást, a fogadási műveletek a vártnál tovább tarthatnak. Ezért javasoljuk, hogy ezekben a forgatókönyvekben ne használjon particionálást. A teljesítmény javítása érdekében törölje a meglévő particionált entitásokat, és hozza létre őket úgy, hogy a particionálás le van tiltva.
* **Üzenetek tallózása/betekintés:** Csak a régebbi [WindowsAzure.ServiceBus kódtárban](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) érhető el. [A PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nem mindig adja vissza a [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) tulajdonságban megadott üzenetek számát. Ennek a viselkedésnek két gyakori oka van. Ennek egyik oka az, hogy az üzenetek gyűjteményének összesített mérete meghaladja a maximális 256 KB-os méretet. Egy másik ok az, hogy ha az üzenetsor vagy témakör [EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) tulajdonsága **true**(igaz) értékre van állítva, akkor előfordulhat, hogy egy partíció nem rendelkezik elegendő üzenetekkel a kért számú üzenet befejezéséhez. Általánosságban elmondható, hogy ha egy alkalmazás adott számú üzenetet szeretne kapni, akkor újra és újra meg kell hívnia a [PeekBatchet,](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) amíg meg nem kapja ezt a számú üzenetet, vagy nincs több betekintő üzenet. További információért, beleértve a kódmintákat is, tekintse meg a [QueueClient.PeekBatch vagy](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) [a SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) API dokumentációját.

## <a name="latest-added-features"></a>A legújabb hozzáadott funkciók

* A szabály hozzáadása vagy eltávolítása mostantól particionált entitásokkal is támogatott. A nem particionált entitástól eltérő műveletek nem támogatottak a tranzakciókban. 
* Az AMQP mostantól támogatott az üzenetek particionált entitásba és entitásból való küldésére és fogadására.
* Az AMQP mostantól támogatott a következő műveletekhez: [Batch Send,](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch) [Batch Receive,](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch)Receive [by Sequence Number](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Peek](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [Renew Lock,](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock)Schedule [Message,](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync)Cancel [Scheduled Message,](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync) [Add Rule,](/dotnet/api/microsoft.azure.servicebus.ruledescription) [Remove Rule,](/dotnet/api/microsoft.azure.servicebus.ruledescription) [Session Renew Lock,](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock) [Set Session State](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), Get [Session State](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate), és [Enumerate Sessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Particionált entitások korlátozásai

A Service Bus a következő korlátozásokat szabja meg a particionált üzenetsorok és témakörök számára:

* A particionált üzenetsorok és témakörök nem támogatottak a prémium szintű üzenetkezelési szinten. A munkamenetek a Premier szinten a SessionId használatával támogatottak. 
* A particionált üzenetsorok és témakörök nem támogatják az olyan üzenetek küldését, amelyek egyetlen tranzakció különböző munkameneteibe tartoznak.
* A Service Bus névterenként jelenleg 100 particionált üzenetsort vagy témakört képes kezelni. Minden particionált üzenetsor vagy témakör beleszámol a névtérenkénti 10 000 entitás kvótába (a prémium szintű csomagokra nem vonatkozik).

## <a name="next-steps"></a>Következő lépések
A particionálást az Azure Portal, a PowerShell, a parancssori felület, Resource Manager sablon, a .NET, a Java, a Python és a JavaScript használatával engedélyezheti. További információ: [Particionálás engedélyezése.](enable-partitions.md) 

Az AMQP 1.0 üzenetkezelési specifikáció alapvető fogalmait az [AMQP 1.0 protokoll-útmutatóban olvashatja el.](service-bus-amqp-protocol-guide.md)

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
