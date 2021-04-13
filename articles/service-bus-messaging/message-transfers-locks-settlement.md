---
title: Az üzenetek átvitelének, zárolásának és elszámolásának Azure Service Bus
description: Ez a cikk áttekintést nyújt Azure Service Bus üzenetek átviteléről, zárolásáról és elszámolási műveleteiről.
ms.topic: article
ms.date: 04/12/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6fbbcbf4a1920ee0e66a956443dcfb8a6a17af43
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306772"
---
# <a name="message-transfers-locks-and-settlement"></a>Üzenetek átvitele, zárolása és elszámolása

Egy Message Broker (például Service Bus) központi funkciója, hogy fogadja az üzeneteket egy várólistába vagy témakörbe, és azokat a későbbi lekérésekhez elérhetővé fogja tenni. A *Send* kifejezés azt a kifejezést használja, amelyet általában egy üzenetnek az üzenet-átvitelszervezőbe történő átviteléhez használnak. A *Receive* kifejezés általában az üzenetek lekéréses ügyfélnek történő átviteléhez használatos.

Amikor egy ügyfél üzenetet küld, általában tudni szeretné, hogy az üzenet megfelelően át lett-e adva a közvetítőnek, vagy valamilyen hiba történt-e. Ez a pozitív vagy negatív nyugtázás rendezi az ügyfelet és a közvetítőt az üzenet átvitelének állapotával kapcsolatban. Tehát ez az úgynevezett *elszámolás*.

Hasonlóképpen, amikor a közvetítő üzenetet továbbít egy ügyfélnek, a közvetítő és az ügyfél szeretné megállapítani, hogy az üzenet feldolgozása sikeres volt-e, ezért el lehet-e távolítani, illetve hogy az üzenet kézbesítése vagy feldolgozása sikertelen volt-e, így az üzenetet újra kell kézbesíteni.

## <a name="settling-send-operations"></a>Küldési műveletek rendezése

A támogatott Service Bus API-ügyfelek bármelyikének használatakor a rendszer mindig explicit módon küldi el a műveleteket a Service Busba, ami azt jelenti, hogy az API-művelet megvárja, amíg Service Bus érkezik az elfogadási eredmény, majd befejezi a küldési műveletet.

Ha Service Bus elutasítja az üzenetet, az elutasítás egy hibaüzenetet és egy, a **nyomkövetési azonosítóval** ellátott szöveget tartalmaz. Az elutasítás azt is tartalmazza, hogy a művelet újrapróbálkozhat-e a siker várható sikerességével. Az ügyfélben ez az információ kivételbe kerül, és a küldési művelet hívója számára lett kiemelve. Ha az üzenet el lett fogadva, a művelet csendben befejeződött.

A AMQP protokoll használata esetén, amely a .NET Standard, a Java, a JavaScript, a Python és a go-ügyfelek kizárólagos protokollja, valamint [a .net-keretrendszerbeli ügyfélhez tartozó lehetőség, az](service-bus-amqp-dotnet.md)üzenetek átvitele és a kiegyenlítések folyamata és aszinkron módon történik. Javasoljuk, hogy használja az aszinkron programozási modell API-variánsait.

A küldő a gyors egymásutánban több üzenetet is helyezhet a huzalon anélkül, hogy meg kellene várnia az egyes üzenetek elismerését, ahogyan azt egyébként a SBMP protokoll vagy a HTTP 1,1 esetében is tenné. Az aszinkron küldési műveletek elvégzése a megfelelő üzenetek elfogadásának és tárolásának, particionált entitások esetén, illetve a különböző entitások küldési műveletének átfedésével történik. Előfordulhat, hogy a Befejezés az eredeti küldési sorrendben is előfordulhat.

A küldési műveletek eredményének kezelésére szolgáló stratégia azonnali és jelentős teljesítménybeli hatással lehet az alkalmazására. Az ebben a szakaszban szereplő példák a C# nyelven íródtak, és a Java-határidős, a Java Monos, a JavaScript-ígéretek és a más nyelveken egyenértékű fogalmakra vonatkoznak.

Ha az alkalmazás egy egyszerű hurokból álló burst üzenetet hoz létre, és az összes küldési művelet befejezését várta, mielőtt a következő üzenetet, szinkron vagy aszinkron API-alakzatokat küld, a 10 üzenet csak a 10 szekvenciális teljes körutazás után fejeződik be a kiegyenlítéshez.

Ha 70 feltételezhető, hogy egy helyszíni helyről Service Busre, és csak 10 MS Service Bus az egyes üzenetek elfogadásához és tárolásához, a következő hurok legalább 8 másodpercet vesz igénybe, és nem számítja fel a hasznos adatátviteli időt vagy a potenciális útvonal-torlódási hatásokat:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Ha az alkalmazás elindítja a 10 aszinkron küldési műveletet azonnali örökléssel, és külön vár a megfelelő befejezésre, akkor a 10 küldési művelet átfedésben van. A 10 üzenet átadása azonnali egymásutánban történik, ami akár TCP-kereteket is megoszthat, és a teljes átvitel időtartama nagy mértékben függ a közvetítőnek továbbított üzenetek lekéréséhez szükséges hálózati időről.

A korábbi hurokhoz hasonlóan a következő hurok teljes átfedésben lévő végrehajtási ideje is maradhat egy másodperc alatt:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Fontos megjegyezni, hogy az összes aszinkron programozási modell a memória-alapú, a rejtett munkavárólista valamilyen formáját használja, amely a függőben lévő műveleteket tartalmazza. Ha a Send API visszatér, a küldési feladat várólistára kerül a munkavárólistában, de a protokoll kézmozdulata csak akkor indul el, ha a feladat futtatása folyamatban van. Az üzenetek kitörését és a megbízhatóságot érintő kód esetében ügyelni kell arra, hogy ne legyen túl sok üzenet a "berepülésben", mert az összes elküldött üzenet a memóriába való behelyezésük előtt felveszi a memóriát.

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

A **fogadási és törlési** mód arra utasítja a közvetítőt, hogy az elküldéskor megtekintse a fogadó ügyfélnek küldött összes üzenetet. Ez azt jelenti, hogy az üzenetet azonnal felhasználjuk, amint a közvetítő behelyezi azt a huzalba. Ha az üzenet átvitele meghiúsul, az üzenet elvész.

Ennek a módnak az a célja, hogy a fogadónak ne kelljen további műveleteket végeznie az üzeneten, és a rendezés eredményére való várakozással sem lassul. Ha az egyes üzenetekben tárolt adat alacsony értékkel rendelkezik, és/vagy csak nagyon rövid idő alatt értelmezhető, ez a mód ésszerű választás.

### <a name="peeklock"></a>PeekLock

A **betekintési zárolási** mód közli a közvetítővel, hogy a fogadó ügyfél explicit módon kívánja rendezni a fogadott üzeneteket. Az üzenet elérhetővé válik a fogadó számára, miközben a szolgáltatás kizárólagos zárolása alatt áll, így a többi versengő fogadó nem látja azt. A zárolás időtartama kezdetben a várólista vagy az előfizetés szintjén van meghatározva, és a zárolást birtokló ügyfél kiterjeszthető. A zárolások megújításával kapcsolatos részletekért tekintse meg a jelen cikk [zárolások megújítása](#renew-locks) című szakaszát. 

Ha egy üzenet zárolva van, az azonos várólistából vagy előfizetésből érkező többi ügyfél zárolja a zárolásokat, és lekéri a következő elérhető üzeneteket, amelyek nem aktív zárolás alatt találhatók. Ha az üzenet zárolása explicit módon fel van szabadítva, vagy ha lejár a zárolás, az üzenet a lekérési megrendelés elején vagy annak közelében jelenik meg.

Ha az üzenetet a fogadók ismételten kiadják, vagy lehetővé teszik a zárolás elteltét a megadott számú alkalommal (a[maximális kézbesítések száma](service-bus-dead-letter-queues.md#maximum-delivery-count)), az üzenet automatikusan törlődik a sorból vagy az előfizetésből, és a hozzá tartozó kézbesítetlen levelek várólistába kerül.

A fogadó ügyfél egy pozitív nyugtával kezdeményezi a kapott üzenet rendezését, amikor meghívja az `Complete` API-t az üzenethez. Azt jelzi, hogy a közvetítő sikeresen feldolgozta az üzenetet, és az üzenet el lett távolítva a sorból vagy az előfizetésből. A közvetítő választ küld a fogadó elszámolási céljára egy olyan választal, amely jelzi, hogy a rendezés elvégezhető-e.

Ha a fogadó ügyfél nem tud feldolgozni egy üzenetet, de azt szeretné, hogy a rendszer visszakézbesítse az üzenetet, explicit módon megkérheti az üzenet felszabadítását és zárolását azonnal, ha meghívja az API-t `Abandon` az üzenethez, vagy ha nem tesz semmit, és hagyja a zárolást.

Ha egy fogadó ügyfél nem tud feldolgozni egy üzenetet, és tudja, hogy az üzenet újbóli kézbesítése és a művelet újrapróbálása nem segít, elutasítja az üzenetet, amely a kézbesítetlen levelek várólistába helyezi az API-t az `DeadLetter` üzenetben, amely lehetővé teszi az egyéni tulajdonság beállítását is, amely a kézbesítetlen levelek várólistáján található üzenettel kérhető le.

A kiegyenlítés egy különleges esete a halasztás, amely [külön cikkben](message-deferral.md)van tárgyalva.

A `Complete` , a `Deadletter` vagy a `RenewLock` műveletek hálózati problémák miatt sikertelenek lehetnek, ha a tárolt zárolás lejárt, vagy más, a letelepedést akadályozó szolgáltatási feltételek is megtalálhatók. Az utóbbi esetek egyikében a szolgáltatás negatív visszaigazolást küld, amely a felületek kivételként szolgál az API-ügyfeleken. Ha az OK sérült hálózati kapcsolat, a rendszer elveti a zárolást, mivel Service Bus nem támogatja a meglévő AMQP-hivatkozások helyreállítását egy másik kapcsolaton.

Ha a `Complete` művelet sikertelen, ami általában az üzenetkezelés legvégén fordul elő, és bizonyos esetekben a feldolgozást követő percek elteltével a fogadó alkalmazás eldöntheti, hogy megőrzi-e a munka állapotát, és figyelmen kívül hagyja az üzenetet, amikor másodszor is kézbesítik, vagy felveszi a munka eredményét, és újrapróbálkozik az üzenet újrakézbesítésével.

Az ismétlődő üzenetek azonosítására szolgáló jellemző mechanizmus az üzenet-azonosító ellenőrzése, amelyet a küldőnek egy egyedi értékre kell beállítania, amely valószínűleg a kezdeményező folyamat azonosítójával van összhangban. A Feladatütemező valószínűleg azt a feladatot állítja be az üzenet-azonosító értékre, amelyet a munkavégzőhöz a megadott feldolgozóhoz hozzárendelni próbál, és a feldolgozó figyelmen kívül hagyja a feladat-hozzárendelés második előfordulását, ha a feladat már elkészült.

> [!IMPORTANT]
> Fontos megjegyezni, hogy a PeekLock által az üzenetben beszerzett zárolás illékony, és az alábbi feltételekkel elveszhet:
>   * Szolgáltatás frissítése
>   * Operációs rendszer frissítése
>   * Az entitás (Üzenetsor, témakör, előfizetés) tulajdonságainak módosítása a zárolás közben.
>
> A zárolás elvesztése után Azure Service Bus egy LockLostException fog előállítani, amely az ügyfélalkalmazás kódján lesz feldolgozva. Ebben az esetben az ügyfél alapértelmezett újrapróbálkozási logikájának automatikusan be kell jelentkeznie, és újra kell próbálkoznia a művelettel.

## <a name="renew-locks"></a>Zárolások megújítása
A zárolás időtartamának alapértelmezett értéke **30 másodperc**. A zárolás időtartamához eltérő értéket adhat meg a várólista vagy az előfizetés szintjén. A zárolást birtokló ügyfél megújíthatja az üzenet zárolását a fogadó objektum metódusok használatával. Ehelyett használhatja az automatikus zárolás-megújítási funkciót, ahol megadhatja azt az időtartamot, ameddig szeretné megőrizni a zárolást. 

## <a name="next-steps"></a>Következő lépések
- A kiegyenlítés egy speciális esete a halasztás. A részletekért tekintse meg az [üzenet halasztását](message-deferral.md) . 
- A kézbesítetlen levelekről a kézbesítetlen [levelek várólistái](service-bus-dead-letter-queues.md)című témakörben olvashat bővebben.
- Ha többet szeretne megtudni a Service Bus üzenetkezeléssel kapcsolatban, tekintse meg a [Service Bus várólisták, témakörök és előfizetések](service-bus-queues-topics-subscriptions.md) című témakört.