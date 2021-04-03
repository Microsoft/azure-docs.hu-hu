---
title: Az üzenetek átvitelének, zárolásának és elszámolásának Azure Service Bus
description: Ez a cikk áttekintést nyújt Azure Service Bus üzenetek átviteléről, zárolásáról és elszámolási műveleteiről.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: fd71edd12e478bcd5f14815c105c14482cf7e2bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89020031"
---
# <a name="message-transfers-locks-and-settlement"></a>Üzenetek átvitele, zárolása és elszámolása

Egy Message Broker (például Service Bus) központi funkciója, hogy fogadja az üzeneteket egy várólistába vagy témakörbe, és azokat a későbbi lekérésekhez elérhetővé fogja tenni. A *Send* kifejezés azt a kifejezést használja, amelyet általában egy üzenetnek az üzenet-átvitelszervezőbe történő átviteléhez használnak. A *Receive* kifejezés általában az üzenetek lekéréses ügyfélnek történő átviteléhez használatos.

Amikor egy ügyfél üzenetet küld, általában tudni szeretné, hogy az üzenet megfelelően át lett-e adva a közvetítőnek, vagy valamilyen hiba történt-e. Ez a pozitív vagy negatív nyugtázás megrendezi az ügyfelet és a közvetítőt az üzenet átviteli állapotáról, és ennek megfelelően a *rendezésnek* nevezzük.

Hasonlóképpen, amikor a közvetítő üzenetet továbbít egy ügyfélnek, a közvetítő és az ügyfél szeretné megállapítani, hogy az üzenet feldolgozása sikeres volt-e, ezért el lehet-e távolítani, illetve hogy az üzenet kézbesítése vagy feldolgozása sikertelen volt-e, így az üzenetet újra kell kézbesíteni.

## <a name="settling-send-operations"></a>Küldési műveletek rendezése

A támogatott Service Bus API-ügyfelek bármelyikének használatakor a rendszer mindig explicit módon küldi el a műveleteket a Service Busba, ami azt jelenti, hogy az API-művelet megvárja, amíg Service Bus érkezik az elfogadási eredmény, majd befejezi a küldési műveletet.

Ha Service Bus elutasítja az üzenetet, az elutasítás egy hibaüzenetet és egy "nyomkövetési azonosítót" tartalmazó szöveget tartalmaz. Az elutasítás azt is tartalmazza, hogy a művelet újrapróbálkozhat-e a siker várható sikerességével. Az ügyfélben ez az információ kivételbe kerül, és a küldési művelet hívója számára lett kiemelve. Ha az üzenet el lett fogadva, a művelet csendben befejeződött.

A AMQP protokoll használata esetén, amely a .NET Standard ügyfélhez és a Java-ügyfélhez tartozó kizárólagos protokoll, és [amely a .NET-keretrendszer ügyfelének egyik beállítása](service-bus-amqp-dotnet.md), az üzenetek átvitele és a kiegyenlítések a folyamattal és teljesen aszinkron módon használhatók, és javasoljuk, hogy használja az aszinkron programozási modell API-variánsait.

A küldő a gyors egymásutánban több üzenetet is helyezhet a huzalon anélkül, hogy meg kellene várnia az egyes üzenetek elismerését, ahogyan azt egyébként a SBMP protokoll vagy a HTTP 1,1 esetében is tenné. Az aszinkron küldési műveletek elvégzése a megfelelő üzenetek elfogadásának és tárolásának, particionált entitások esetén, illetve a különböző entitások küldési műveletének átfedésével történik. Előfordulhat, hogy a Befejezés az eredeti küldési sorrendben is előfordulhat.

A küldési műveletek eredményének kezelésére szolgáló stratégia azonnali és jelentős teljesítménybeli hatással lehet az alkalmazására. Az ebben a szakaszban szereplő példák a C# nyelven íródtak, és a Java-határidővel egyenértékűek.

Ha az alkalmazás egy egyszerű hurokból álló burst üzenetet hoz létre, és az összes küldési művelet befejezését várta, mielőtt a következő üzenetet, szinkron vagy aszinkron API-alakzatokat küld, a 10 üzenet csak a 10 szekvenciális teljes körutazás után fejeződik be a kiegyenlítéshez.

Ha feltételezett 70 milliszekundumos TCP-késési távolságot használ egy helyszíni helyről Service Bus, és mindössze 10 MS-ot ad a Service Bus számára az egyes üzenetek elfogadásához és tárolásához, a következő hurok legalább 8 másodpercet vesz igénybe, és nem számítja fel a hasznos adatátviteli időt vagy a potenciális útvonal zsúfoltságának hatásait:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Ha az alkalmazás megkezdi a 10 aszinkron küldési műveletet azonnali örökléssel, és külön vár a megfelelő befejezésre, akkor a 10 küldési művelet átfedésben van. A 10 üzenet átadása azonnali egymásutánban történik, ami akár TCP-kereteket is megoszthat, és a teljes átvitel időtartama nagy mértékben függ a közvetítőnek továbbított üzenetek lekéréséhez szükséges hálózati időről.

A korábbi hurokhoz hasonlóan a következő hurok teljes átfedésben lévő végrehajtási ideje is maradhat egy másodperc alatt:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Fontos megjegyezni, hogy az összes aszinkron programozási modell a memória-alapú, a rejtett munkavárólista valamilyen formáját használja, amely a függőben lévő műveleteket tartalmazza. Ha a [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) vagy a **Send** (Java) értéket adja vissza, a küldési feladat várólistára kerül a munkavárólistában, de a protokoll kézmozdulata csak akkor indul el, ha a feladat futása megkezdődött. Az üzenetek kitörését és a megbízhatóságot érintő kód esetében ügyelni kell arra, hogy ne legyen túl sok üzenet a "berepülésben", mert az összes elküldött üzenet a memóriába való behelyezésük előtt felveszi a memóriát.

A következő kódrészletekben látható, a C#-ban bemutatott szemaforok olyan szinkronizációs objektumok, amelyek szükség esetén lehetővé teszik az alkalmazás-szintű szabályozást. A szemaforok ilyen használata lehetővé teszi, hogy a legfeljebb 10 üzenetet egyszerre lehessen járatni. A rendszer a küldés után a 10 rendelkezésre álló szemafor zárolást is elvégzi, és a Küldés befejeződött. A ciklus 11. lépése egészen addig várakozik, amíg a korábbi küldések legalább egyike be nem fejeződik, majd a zárolás elérhetővé válik:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Az alkalmazások **soha nem** indíthatnak aszinkron küldési műveletet a művelet eredményének lekérése nélkül "tűz és elfelejtés" módon. Így a belső és a láthatatlan feladat-várólistát akár a memória teljes terhelése is betöltheti, és megakadályozhatja, hogy az alkalmazás a küldési hibákat észlelje:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Alacsony szintű AMQP-ügyféllel a Service Bus "előre letelepedett" átviteleket is elfogad. Az előre elszámolt átvitel egy olyan tűz-és leállási művelet, amelynek eredményét a rendszer nem küldi vissza az ügyfélnek, és az üzenetet a küldéskor rendezi a rendszer. Az ügyfélre vonatkozó visszajelzés hiánya azt is jelenti, hogy a diagnosztika nem érhető el, ami azt jelenti, hogy ez a mód nem felel meg az Azure-támogatáson keresztüli segítségnek.

## <a name="settling-receive-operations"></a>Fogadási műveletek rendezése

A fogadási műveletek esetében a Service Bus API-ügyfelek két különböző explicit módot tesznek lehetővé: *fogadás és törlés* és *betekintés – zárolás*.

### <a name="receiveanddelete"></a>ReceiveAndDelete

A [fogadási és törlési](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód arra utasítja a közvetítőt, hogy az elküldéskor megtekintse a fogadó ügyfélnek küldött összes üzenetet. Ez azt jelenti, hogy az üzenetet azonnal felhasználjuk, amint a közvetítő behelyezi azt a huzalba. Ha az üzenet átvitele meghiúsul, az üzenet elvész.

Ennek a módnak az a célja, hogy a fogadónak ne kelljen további műveleteket végeznie az üzeneten, és a rendezés eredményére való várakozással sem lassul. Ha az egyes üzenetekben tárolt adat alacsony értékkel rendelkezik, és/vagy csak nagyon rövid idő alatt értelmezhető, ez a mód ésszerű választás.

### <a name="peeklock"></a>PeekLock

A [betekintési zárolási](/dotnet/api/microsoft.servicebus.messaging.receivemode) mód közli a közvetítővel, hogy a fogadó ügyfél explicit módon kívánja rendezni a fogadott üzeneteket. Az üzenet elérhetővé válik a fogadó számára, miközben a szolgáltatás kizárólagos zárolása alatt áll, így a többi versengő fogadó nem látja azt. A zárolás időtartama kezdetben a várólista vagy az előfizetés szintjén van meghatározva, és a zárolást birtokló ügyfél kiterjeszthető a [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) művelettel.

Ha egy üzenet zárolva van, az azonos várólistából vagy előfizetésből érkező többi ügyfél zárolja a zárolásokat, és lekéri a következő elérhető üzeneteket, amelyek nem aktív zárolás alatt találhatók. Ha az üzenet zárolása explicit módon fel van szabadítva, vagy ha lejár a zárolás, az üzenet a lekérési megrendelés elején vagy annak közelében jelenik meg.

Ha az üzenetet a fogadók ismételten kiadják, vagy lehetővé teszik, hogy a zárolás egy meghatározott számú alkalommal legyen eltelni ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), a rendszer automatikusan eltávolítja az üzenetet a várólistából vagy az előfizetésből, és elhelyezi a kapcsolódó kézbesítetlen levelek várólistáján.

A fogadó ügyfél elindít egy pozitív nyugtával rendelkező fogadott üzenet rendezését, amikor az az API szintjén [meghívja a](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) hívást. Ez azt jelzi, hogy a közvetítő sikeresen feldolgozta az üzenetet, és az üzenet el lett távolítva a sorból vagy az előfizetésből. A közvetítő választ küld a fogadó elszámolási céljára egy olyan választal, amely jelzi, hogy a rendezés elvégezhető-e.

Ha a fogadó ügyfél nem tud feldolgozni egy üzenetet, de azt szeretné, hogy a rendszer visszakézbesítse az üzenetet, akkor explicit módon megkérheti, hogy az üzenet azonnal fel legyen szabadítva, és a [lemondás](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) meghívásával azonnal feloldja az üzenetet, ha a zárolás eltelik.

Ha a fogadó ügyfél nem tud feldolgozni egy üzenetet, és tudja, hogy az üzenet újbóli kézbesítése és a művelet újrapróbálása nem segít, elutasítja az üzenetet, amely a kézbesítetlen levelek várólistába helyezi a [kézbesítetlen levelek](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter)hívásával, amely lehetővé teszi az egyéni tulajdonság beállítását is, amely a kézbesítetlen levelek várólistáján lévő üzenettel lekérhető okkódot is tartalmazza.

A kiegyenlítés egy különleges esete a halasztás, amely külön cikkben van tárgyalva.

A **teljes** vagy **kézbesítetlen levelek** műveletek, valamint a **RenewLock** műveletek hálózati problémák miatt sikertelenek lehetnek, ha a tárolt zárolás lejárt, vagy más, a rendezést megakadályozó szolgáltatási oldali feltételek vannak. Az utóbbi esetek egyikében a szolgáltatás negatív visszaigazolást küld, amely a felületek kivételként szolgál az API-ügyfeleken. Ha az OK sérült hálózati kapcsolat, a rendszer elveti a zárolást, mivel Service Bus nem támogatja a meglévő AMQP-hivatkozások helyreállítását egy másik kapcsolaton.

Ha a **Befejezés** nem sikerül, ami általában az üzenetkezelés legvégén fordul elő, és bizonyos esetekben a feldolgozást követő percek elteltével a fogadó alkalmazás eldöntheti, hogy megőrzi-e a munka állapotát, és figyelmen kívül hagyja az üzenetet, ha másodszor is kézbesíti azt, vagy felveszi a munkát, és újrapróbálkozik az üzenet újrakézbesítésével.

Az ismétlődő üzenetek azonosítására szolgáló jellemző mechanizmus az üzenet-azonosító ellenőrzése, amelyet a küldőnek egy egyedi értékre kell beállítania, amely valószínűleg a kezdeményező folyamat azonosítójával van összhangban. A Feladatütemező valószínűleg azt a feladatot állítja be az üzenet-azonosító értékre, amelyet a munkavégzőhöz a megadott feldolgozóhoz hozzárendelni próbál, és a feldolgozó figyelmen kívül hagyja a feladat-hozzárendelés második előfordulását, ha a feladat már elkészült.

> [!IMPORTANT]
> Fontos megjegyezni, hogy a PeekLock által az üzenetben beszerzett zárolás illékony, és az alábbi feltételekkel elveszhet:
>   * Szolgáltatás frissítése
>   * Operációs rendszer frissítése
>   * Az entitás (Üzenetsor, témakör, előfizetés) tulajdonságainak módosítása a zárolás közben.
>
> A zárolás elvesztése után Azure Service Bus egy LockLostException fog előállítani, amely az ügyfélalkalmazás kódján lesz feldolgozva. Ebben az esetben az ügyfél alapértelmezett újrapróbálkozási logikájának automatikusan be kell jelentkeznie, és újra kell próbálkoznia a művelettel.

## <a name="next-steps"></a>Következő lépések

Az Service Bus üzenetkezeléssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)](service-bus-queues-topics-subscriptions.md)
* [Bevezetés a Service Bus által kezelt üzenetsorok használatába](service-bus-dotnet-get-started-with-queues.md)
* [A Service Bus-üzenettémakörök és -előfizetések használata](service-bus-dotnet-how-to-use-topics-subscriptions.md)
