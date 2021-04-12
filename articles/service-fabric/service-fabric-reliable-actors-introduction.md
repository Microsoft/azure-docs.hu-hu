---
title: Service Fabric Reliable Actors áttekintése
description: Bevezetés a Service Fabric Reliable Actors programozási modellbe a virtuális színész mintája alapján.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 1a8a7003a69deaf6b74d6fbb8a3cf84b0a78eecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576383"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Service Fabric Reliable Actors bemutatása
A Reliable Actors a [virtuális színész](https://research.microsoft.com/en-us/projects/orleans/) mintáján alapuló Service Fabric alkalmazás-keretrendszer. A Reliable Actors API egy egyszálas programozási modellt biztosít, amely a Service Fabric által biztosított méretezhetőségi és megbízhatósági garanciára épül.

## <a name="what-are-actors"></a>Mik azok a szereplők?
A színész egy elkülönített, különálló számítási és állapotú egység, egyszálas végrehajtással. A [Actor minta](https://en.wikipedia.org/wiki/Actor_model) egy egyidejű vagy elosztott rendszerek számítási modellje, amelyben nagy mennyiségű ilyen szereplő futhat egyszerre, egymástól függetlenül. A szereplők képesek kommunikálni egymással, és több résztvevőt is létrehozhatnak.

### <a name="when-to-use-reliable-actors"></a>Mikor kell használni a Reliable Actors
A Service Fabric Reliable Actors a színész kialakítási mintájának implementációja. Ahogy a szoftveres kialakítási minta esetében is, az adott minta használatának döntése attól függ, hogy a szoftver tervezési problémája megfelel-e a mintának.

Bár a színész kialakítási mintája alkalmas lehet számos elosztott rendszerbeli problémára és forgatókönyvre, körültekintően figyelembe kell venni a minta korlátait és a megvalósítási keretrendszert. Általános útmutatásként vegye fontolóra a probléma vagy forgatókönyv modellezésére a színész mintáját, ha:

* A problémás terület nagy számú (több ezer vagy több) kis, független és elszigetelt állapotú és logikai egységből áll.
* Olyan egyszálas objektumokkal szeretne dolgozni, amelyek nem igényelnek jelentős interakciót a külső összetevőktől, többek között a lekérdezési állapotot a szereplők egy csoportján belül.
* Az I/O-műveletek kiadásával a színész példányai nem tudják előre nem látható késéssel letiltani a hívókat.

## <a name="actors-in-service-fabric"></a>A Service Fabric szereplői
Service Fabric a szereplők a Reliable Actors-keretrendszerben valósulnak meg: egy, a [Service Fabric Reliable Servicesra](service-fabric-reliable-services-introduction.md)épülő Actor-Pattern-alapú alkalmazás-keretrendszer. Az Ön által írt megbízható Actors szolgáltatás tulajdonképpen egy particionált, állapot-nyilvántartó megbízható szolgáltatás.

Minden szereplő egy Actor típusú példányként van definiálva, amely megegyezik azzal, ahogyan a .NET-objektum egy .NET-típusú példány. Előfordulhat például, hogy egy olyan Actor-típust alkalmaz, amely egy adott számológép funkcióit valósítja meg, és számos, a fürtben lévő csomóponton terjesztett résztvevő lehet. Minden ilyen szereplőt egyedileg azonosít egy színész azonosítója.

## <a name="actor-lifetime"></a>Színész élettartama
Service Fabric szereplők virtuálisak, ami azt jelenti, hogy az élettartamuk nem kötődik a memóriában tárolt ábrázoláshoz. Ennek eredményeképpen nem kell explicit módon létrehozni vagy megsemmisíteni. A Reliable Actors futtatókörnyezet automatikusan aktiválja a szereplőt, amikor az első alkalommal kérelmet kap az adott színész AZONOSÍTÓjának. Ha egy szereplőt nem használ egy adott időszakra, akkor a Reliable Actors futtatókörnyezet a memóriában lévő objektumot gyűjti. Emellett a szereplő létezéséről is gondoskodni fog arról, hogy később újra kell aktiválni. További részletek: [Actors Lifecycle and szemetet Collection](service-fabric-reliable-actors-lifecycle.md).

A virtuális színészek élettartamának absztrakciója bizonyos kikötéseket hajt végre a virtuális színészi modell miatt, és valójában a Reliable Actors megvalósítás a modelltől függően eltér.

* A színész automatikusan aktiválódik (egy színészi objektum kiépítésének előidézése), amikor a rendszer első alkalommal küld el egy üzenetet a színészi AZONOSÍTÓjának. Néhány idő elteltével a Actor objektum a beszedett szemetet gyűjti. A jövőben a színész AZONOSÍTÓjának újbóli használata egy új Actor objektum kiépítését eredményezi. A színész állapota kiadja az objektum élettartamát a State Managerben való tárolás során.
* A Actors-azonosító bármely Actor metódusának meghívása aktiválja ezt a szereplőt. Emiatt a szereplők típusait a futtatókörnyezet implicit módon hívja meg. Ezért az ügyfél kódja nem tud paramétereket átadni a színész típusa konstruktorának, bár a paramétereket a szolgáltatás maga is átadhatja a szereplő konstruktorának. Ennek eredményeképpen a szereplők részlegesen inicializált állapotban lehetnek, ha más módszerekkel is meghívja őket, ha a színész inicializálási paramétereket igényel az ügyféltől. Nem áll rendelkezésre egyetlen belépési pont a szereplő ügyféltől való aktiválására.
* Bár a Reliable Actors implicit módon hozzon létre Actor objektumokat; lehetősége van arra, hogy explicit módon törölje a szereplőt és annak állapotát.

## <a name="distribution-and-failover"></a>Terjesztés és feladatátvétel
A méretezhetőség és a megbízhatóság biztosítása érdekében a Service Fabric a fürtön keresztül osztja el a résztvevőket, és a szükséges módon automatikusan áttelepíti azokat a meghibásodott csomópontokból. Ez egy [particionált, állapot-nyilvántartó megbízható szolgáltatáson](service-fabric-concepts-partitioning.md)alapuló absztrakció. A terjesztést, a méretezhetőséget, a megbízhatóságot és az automatikus feladatátvételt mind az a tény biztosítja, hogy a szereplők egy, a *Actors szolgáltatásnak* nevezett, állapot-nyilvántartó megbízható szolgáltatáson belül futnak.

A szereplők a Actors szolgáltatás partíciói között oszlanak el, és ezek a partíciók a Service Fabric-fürt csomópontjai között oszlanak meg. Minden szolgáltatás-partíció tartalmaz egy készletet. Service Fabric kezeli a szolgáltatási partíciók eloszlását és feladatátvételét.

Például egy, az alapértelmezett Actor Partition-elhelyezést használó három csomóponton üzembe helyezett kilenc partíciót tartalmazó Actor-szolgáltatás a következő módon terjeszthető:

![Reliable Actors eloszlás][2]

A Actor Framework a particionálási sémát és a kulcs tartományának beállításait kezeli. Ez egyszerűsíti az egyes döntéseket, de a következőket is figyelembe veszi:

* Reliable Services lehetővé teszi a particionálási séma, a kulcs tartomány (tartomány particionálási séma használata esetén) és a partíciók számának kiválasztását. Reliable Actors a tartomány particionálási sémája (egységes Int64 séma) használatára korlátozódik, és a teljes Int64-tartomány használatát igényli.
* Alapértelmezés szerint a szereplők véletlenszerűen vannak elhelyezve a partíciókban, ami egységes eloszlást eredményez.
* Mivel a szereplők véletlenszerűen vannak elhelyezve, várható, hogy a színészi műveletek mindig hálózati kommunikációt igényelnek, beleértve a metódus hívási adatok szerializálását és deszerializálását, a késést és a terhelést.
* A speciális forgatókönyvek esetében lehetséges, hogy a Int64 Actors-azonosítók használatával vezérli a szereplők partíciójának elhelyezését. Ez azonban a különböző partíciókban lévő szereplők kiegyensúlyozatlan eloszlásával járhat.

A Actor Services particionálásával kapcsolatos további információkért tekintse meg a [szereplők particionálási fogalmai](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)című témakört.

## <a name="actor-communication"></a>Színészi kommunikáció
A színészek interakciói olyan felületen vannak definiálva, amelyet az illesztőfelületet megvalósító színész közösen használ, és az ügyfél, amely egy adott felületen keresztül kap proxyt egy szereplőnek. Mivel ez az interfész a Actor metódusok aszinkron meghívására használatos, az illesztőfelület minden metódusának tevékenység-visszaküldési műveletnek kell lennie.

A metódus-meghívások és a válaszok végső soron hálózati kérelmeket eredményeznek a fürtben, ezért az általuk visszaadott feladatok argumentumait és az eredmény típusát a platformnak szerializálni kell. Különösen fontos, hogy az [adategyezmény szerializálható](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)legyen.

### <a name="the-actor-proxy"></a>A Actor proxy
A Reliable Actors ügyfél API kommunikációt biztosít egy Actor-példány és egy Actor-ügyfél között. Egy szereplővel folytatott kommunikációhoz az ügyfél létrehoz egy Actors proxy objektumot, amely megvalósítja a színészi felületet. A-ügyfél a proxy objektumon metódusok meghívásával kommunikál a szereplővel. A Actor proxy használható az ügyfél és a színész közötti kommunikációhoz.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Vegye figyelembe, hogy a Actor proxy objektum létrehozásához használt két adat a színész azonosítója és az alkalmazás neve. A színész azonosítója egyedileg azonosítja a szereplőt, míg az alkalmazás neve azonosítja azt a [Service Fabric alkalmazást](service-fabric-reliable-actors-platform.md#application-model) , amelyben a szereplő üzembe lett helyezve.

Az `ActorProxy` ügyfél oldalán található (C#)/ `ActorProxyBase` (Java) osztály végrehajtja a szükséges megoldást, hogy az azonosító alapján keresse meg a szereplőt, és nyisson meg egy kommunikációs csatornát. A kommunikációs hibák és feladatátvételek esetén is megpróbálja megkeresni a szereplőt. Ennek eredményeképpen az üzenetek kézbesítése a következő jellemzőkkel rendelkezik:

* Az üzenetek kézbesítése a legjobb megoldás.
* Előfordulhat, hogy a szereplők duplikált üzeneteket kapnak ugyanarról az ügyfélről.

## <a name="concurrency"></a>Egyidejűség
A Reliable Actors Runtime egy egyszerű, kulcsrakész hozzáférési modellt biztosít a Actor metódusok eléréséhez. Ez azt jelenti, hogy a Actor objektum kódjában egyszerre legfeljebb egy szál aktív lehet. A turn-based Access nagy mértékben leegyszerűsíti az egyidejű rendszereket, mivel nincs szükség az adathozzáféréshez szükséges szinkronizálási mechanizmusokra. Emellett azt is jelenti, hogy a rendszereknek speciális szempontokat kell megfontolniuk az egyes Actors-példányok egyszálas hozzáférési jellegének biztosításához.

* Egyetlen Actor-példány egyszerre csak egy kérést tud feldolgozni. Egy Actor-példány az átviteli sebesség szűk keresztmetszetét okozhatja, ha az egyidejű kérelmek kezelésére várható.
* A szereplők egymás közötti holtpontra válthatnak, ha a két szereplő között körkörös kérelem van, míg egy külső kérést egy egyidejű résztvevőnek kell elvégeznie. A színészi futtatókörnyezet automatikusan időtúllépést jelez a színészi hívásoknál, és kivételt jelez a hívónak a lehetséges holtponti helyzetek megszakítására.

![Reliable Actors kommunikáció][3]

### <a name="turn-based-access"></a>Kulcsrakész hozzáférés
A turn egy Actor metódus teljes végrehajtását jelenti, amely a többi résztvevőtől vagy ügyféltől érkező kérésre reagál, vagy egy [időzítő/emlékeztető](service-fabric-reliable-actors-timers-reminders.md) visszahívás teljes végrehajtása. Annak ellenére, hogy ezek a metódusok és visszahívások aszinkron módon működnek, a szereplők futtatókörnyezete nem áll le. Az új turn engedélyezése előtt a turnön teljesen el kell fejezni. Más szóval a jelenleg végrehajtás alatt álló Actor metódus vagy időzítő/emlékeztető visszahívás csak akkor végezhető el, ha a metódus vagy a visszahívás új hívása engedélyezett. A metódus vagy visszahívás akkor tekinthető befejezettnek, ha a végrehajtás visszakapott a metódusból vagy visszahívásból, és a metódus vagy visszahívás által visszaadott feladat befejeződött. Érdemes kiemelni, hogy a többtényezős párhuzamosságot a különböző metódusok, időzítők és visszahívások között is figyelembe veszi.

A szereplők futtatókörnyezete kikényszeríti a Turn-alapú párhuzamosságot azáltal, hogy egy fordulat elején beszerezz egy színészi zárolást, és a turn végén kiszabadítja a zárolást. Ennek megfelelően a turn-based Egyidejűség kikényszeríthető egy színészi alapon, és nem a szereplők között. A színészi metódusok és az időzítő/emlékeztető visszahívásai a különböző szereplők nevében egyidejűleg hajthatók végre.

Az alábbi példa szemlélteti a fenti fogalmakat. Vegyünk egy olyan színészi típust, amely két aszinkron módszert valósít meg (mondjuk, *Method1* és *Method2*), egy időzítőt és egy emlékeztetőt. Az alábbi ábrán egy példa látható a metódusok végrehajtásához és a visszahívásokhoz két, a *ActorId1* és a *ActorId2* tartozó résztvevő nevében.

![Reliable Actors Runtime turn-based Egyidejűség és hozzáférés][1]

Ez az ábra a következő konvenciókat követi:

* Mindegyik függőleges vonal egy metódus vagy egy adott szereplő nevében történő visszahívás logikai áramlását mutatja.
* Az egyes függőleges vonalakon megjelenő események időrendi sorrendben történnek, és az újabb események a régebbiek alatt történnek.
* Különböző színek használatosak a különböző szereplőkhöz tartozó ütemtervekhez.
* A kiemelés azt jelzi, hogy milyen időtartamot kell megtartani a színészi zárolás egy metódus vagy visszahívás nevében.

Néhány fontos szempontot figyelembe kell venni:

* Míg a *Method1* a *ActorId2* nevében hajtja végre az ügyfél kérésére *xyz789*, egy másik ügyfél-kérelem (*abc123*) érkezik, amely megköveteli, hogy *a Method1 is* végrehajtsa a *ActorId2* . A *Method1* második végrehajtása azonban addig nem kezdődik meg, amíg az előző végrehajtás nem fejeződött be. Hasonlóképpen, a *ActorId2* által regisztrált emlékeztető, miközben a *Method1* az ügyfél-kérelem *xyz789* válaszol. Az emlékeztető visszahívása csak a *Method1* végrehajtásának befejeződése után hajtható végre. Ennek az az oka, hogy a *ActorId2* esetében a turn-based Egyidejűség kényszerített.
* Ehhez hasonlóan a *ActorId1* is kényszeríteni kell a párhuzamos párhuzamosságot, ahogy azt a *Method1*, a *Method2* és a *ActorId1* nevében az időzítő visszahívása is mutatja, soros módon történik.
* A *Method1* a *ActorId1* nevében való végrehajtása átfedésben van a *ActorId2* nevében végrehajtott végrehajtással. Ennek az az oka, hogy a turn-based Egyidejűség csak egy színészen belül, és nem a különböző szereplőkön van érvényben.
* Néhány módszer/visszahívás végrehajtás esetén a metódus `Task` `CompletableFuture` /visszahívás által visszaadott (C#)/(Java)/(Java) a metódus visszatérése után fejeződik be. Másokban az aszinkron művelet már befejeződött a metódus/visszahívás visszatérési ideje szerint. Mindkét esetben a rendszer csak a metódus/visszahívás visszaadása és az aszinkron művelet befejezése után szabadítja fel a per-Actor zárolást.

### <a name="reentrancy"></a>Újbóli belépés
A Actors futtatókörnyezet alapértelmezés szerint engedélyezi a újbóli belépés. Ez azt jelenti, hogy ha a *Actors* metódusa egy metódust hív meg a " *B" szereplőn*, amely egy másik metódust hív meg az *A actorn*, akkor ez a metódus futhat. Ennek az az oka, hogy ez a logikai hívási lánc környezetének része. Az időzítő és az emlékeztető összes hívása az új logikai hívási környezettel kezdődik. További részletekért tekintse meg a [Reliable Actors újbóli belépés](service-fabric-reliable-actors-reentrancy.md) .

### <a name="scope-of-concurrency-guarantees"></a>Egyidejűségi garanciák hatóköre
A szereplők futtatókörnyezete biztosítja ezeket a párhuzamossági garanciákat olyan helyzetekben, amikor a módszerek meghívását vezérli. Ezek a garanciák például az ügyfélre vonatkozó kérelemre adott válaszként, valamint az időzítő és az emlékeztető visszahívásai esetén is. Ha azonban a színészi kód közvetlenül az Actors Runtime által biztosított mechanizmusokon kívül hívja meg ezeket a metódusokat, akkor a futtatókörnyezet nem tud egyidejűségi garanciákat biztosítani. Ha például a metódust egy olyan feladat kontextusában hívja meg, amely nincs társítva a Actor metódusok által visszaadott feladathoz, akkor a futtatókörnyezet nem tud egyidejűségi garanciákat biztosítani. Ha a metódust olyan szálon hívja meg, amelyet a színész saját maga hoz létre, akkor a futtatókörnyezet nem tud egyidejűségi garanciákat biztosítani. Ezért a háttérbeli műveletek végrehajtásához a szereplőknek olyan [színészi időzítőket és színészi emlékeztetőket](service-fabric-reliable-actors-timers-reminders.md) kell használniuk, amelyek figyelembe veszik a párhuzamos párhuzamosságot.

## <a name="next-steps"></a>Következő lépések
Ismerkedjen meg az első Reliable Actors szolgáltatás létrehozásával:
   * [Első lépések a Reliable Actors a .NET-keretrendszerben](service-fabric-reliable-actors-get-started.md)
   * [Első lépések a Reliable Actors Javával](./service-fabric-create-your-first-linux-application-with-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
