---
title: Üzenetkezelés – várólisták, témakörök és előfizetések Azure Service Bus
description: Ez a cikk áttekintést nyújt Azure Service Bus üzenetkezelési entitásokról (Üzenetsor, témakörök és előfizetések).
ms.topic: article
ms.date: 11/04/2020
ms.openlocfilehash: 7c1d22605e841eac42f2b47aab38777a622bfb90
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359425"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus queues, topics, and subscriptions (Service Bus-üzenetsorok, -témakörök és -előfizetések)
Azure Service Bus támogatja a felhőalapú, Message-orientált middleware-technológiákat, beleértve a megbízható üzenetsor-kezelési és tartós közzétételi/előfizetési üzeneteket. Ezek a felügyelt üzenetkezelési képességek olyan leválasztott üzenetkezelési funkciók, amelyek támogatják a közzététel-előfizetést, az időbeli leválasztást és a terheléselosztási forgatókönyveket a Service Bus üzenetkezelési számítási feladattal. A leválasztott kommunikáció számos előnnyel jár. Például az ügyfelek és a kiszolgálók szükség szerint kapcsolódhatnak, és aszinkron módon végezhetik el a műveleteiket.

A Service Bus üzenetkezelési képességeinek magját alkotó üzenetkezelési entitások **várólisták** , **témakörök és előfizetések** , valamint szabályok/műveletek.

## <a name="queues"></a>Üzenetsorok
A várólisták egy vagy több versengő fogyasztó számára biztosítanak **első, első kimenő** (FIFO) üzenet kézbesítését. Ez azt eredményezi, hogy a fogadók általában abban a sorrendben fogadják és dolgozzák fel az üzeneteket, ahogy azokat a várólistához adták. És csak egy üzenet fogyasztója fogad és dolgoz fel minden üzenetet. A várólisták használatának egyik fő előnye az alkalmazás- **összetevők időbeli leválasztása**. Más szóval a gyártóknak (küldőknek) és a fogyasztóknak (fogadóknak) nem kell egyszerre küldeniük és fogadniuk az üzeneteket. Ennek az az oka, hogy az üzenetek a várólistán tárolódnak a tartósan. Emellett a gyártónak nem kell várnia a fogyasztótól érkező válaszra, hogy továbbra is feldolgozza és elküldhesse az üzeneteket.

A kapcsolódó juttatás a **terheléselosztás** , amely lehetővé teszi a termelők és a fogyasztók számára, hogy különböző díjszabású üzeneteket küldjenek és fogadnak. Számos alkalmazásban a rendszerterhelés az idő múlásával változik. Azonban az egyes munkaegységekhez szükséges feldolgozási idő jellemzően állandó. Az közvetítő és a felhasználók egy üzenetsor használatával azt jelzik, hogy a felhasználó alkalmazásnak csak az átlagos terhelést kell tudni kezelnie a maximális terhelés helyett. A bejövő terhelés változásával az üzenetsor hossza nő vagy csökken. Ez a funkció közvetlenül az alkalmazás terhelésének kiszolgálásához szükséges infrastruktúra mennyiségével kapcsolatos pénzt takarít meg. A terhelés növekedésével több munkavégző folyamat is felvehető a várólistából való olvasáshoz. Az egyes üzeneteket a feldolgozó folyamatoknak csak az egyike dolgozza fel. Emellett ez a lekéréses terheléselosztás lehetővé teszi a munkavégző számítógépek lehető legjobb használatát, még akkor is, ha a feldolgozó számítógépek a saját maximális sebességű lekéréses üzeneteket dolgozzák fel. Ezt a mintát gyakran a **versengő felhasználó** mintának hívják.

A várólisták és a felhasználók közti közti kapcsolat lehetővé teszi az összetevők közötti, laza összekapcsolást. Mivel a termelők és a fogyasztók nem ismerik egymást, a fogyasztókat a gyártóra gyakorolt hatás nélkül lehet frissíteni.

### <a name="create-queues"></a>Várólisták létrehozása
A várólistákat a [Azure Portal](service-bus-quickstart-portal.md), a [PowerShell](service-bus-quickstart-powershell.md), a [CLI](service-bus-quickstart-cli.md)vagy a [Resource Manager-sablonok](service-bus-resource-manager-namespace-queue.md)segítségével hozhatja létre. Ezután a C#, a [Java](service-bus-java-how-to-use-queues.md), a [Python](service-bus-python-how-to-use-queues.md), a [JavaScript](service-bus-nodejs-how-to-use-queues-new-package.md), a [php](service-bus-php-how-to-use-queues.md)és a [Ruby](service-bus-ruby-how-to-use-queues.md) [nyelveken](service-bus-dotnet-get-started-with-queues.md)írt ügyfelekkel küldhet és fogadhat üzeneteket. 

### <a name="receive-modes"></a>Fogadási módok
Két különböző módot is megadhat, amelyekben a Service Bus üzeneteket fogad: **ReceiveAndDelete** vagy **PeekLock**. Ha a [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) módban Service Bus fogadja a fogyasztótól érkező kérést, az üzenetet felhasználva jelzi, és visszaadja a fogyasztó alkalmazásnak. Ez a mód a legegyszerűbb modell. A legjobban olyan helyzetekben működik, amikor az alkalmazás meghibásodás esetén nem dolgozza fel az üzenetet. A forgatókönyv megértéséhez vegye fontolóra azt a forgatókönyvet, amelyben a fogyasztó kiadja a fogadási kérelmet, majd összeomlik a feldolgozás előtt. Ahogy Service Bus az üzenetet felhasználva jelzi, az alkalmazás újraindításkor elkezdi az üzenetek felhasználását. A hiba miatt nem fog megjelenni az összeomlás előtt felhasznált üzenet.

A [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) módban a fogadási művelet kétfázisú lesz, ami lehetővé teszi az olyan alkalmazások támogatását, amelyek nem tudják elviselni a hiányzó üzeneteket. Ha Service Bus fogadja a kérést, az a következő műveleteket végzi el:

1. Megkeresi a következő felhasználandó üzenetet.
1. Zárolja, hogy a többi fogyasztó ne fogadja el.
1. Ezután adja vissza az üzenetet az alkalmazásnak. 

Miután az alkalmazás befejezte az üzenet feldolgozását, vagy megbízhatóan tárolja azt a későbbi feldolgozás érdekében, az üzenet meghívásával végrehajtja a fogadási folyamat második szakaszát [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) . Ha Service Bus fogadja a **CompleteAsync** kérelmet, az üzenetet a rendszer felhasználva jelöli meg.

Ha az alkalmazás valamilyen okból nem tudja feldolgozni az üzenetet, meghívja a [`AbandonAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) metódust az üzeneten (a helyett [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) ). Ez a módszer lehetővé teszi az Service Bus számára az üzenet zárolásának feloldását, és elérhetővé tételét, akár ugyanazon fogyasztó, akár egy másik versengő fogyasztó számára. Másodszor a zároláshoz időtúllépés van társítva. Ha az alkalmazás nem tudja feldolgozni az üzenetet a zárolási időtúllépés lejárta előtt, Service Bus feloldja az üzenet zárolását, és lehetővé teszi, hogy ismét elérhető legyen.

Ha az alkalmazás összeomlik az üzenet feldolgozása után, de a hívása előtt [`CompleteAsync`](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) , Service Bus az újraindításkor visszaküldi az üzenetet az alkalmazásnak. Ezt a folyamatot gyakran **legalább egyszeri** feldolgozásnak nevezik. Azaz minden üzenet feldolgozása legalább egyszer megtörténik. Bizonyos helyzetekben azonban előfordulhat, hogy ugyanazt az üzenetet újra lehet küldeni. Ha a forgatókönyv nem tudja elviselni az ismétlődő feldolgozást, adjon hozzá további logikát az alkalmazásban az ismétlődések észleléséhez. Ez az üzenet [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) tulajdonságával érhető el, amely állandó marad a kézbesítési kísérletek között. Ezt a szolgáltatást **pontosan egyszer** kell feldolgozni.

## <a name="topics-and-subscriptions"></a>Témakörök és előfizetések
A várólista lehetővé teszi az üzenetek egyetlen fogyasztó általi feldolgozását. A várólistákkal ellentétben a témakörök és az előfizetések egy-a-többhöz típusú kommunikációt biztosítanak egy **közzétételi és** előfizetési mintában. Hasznos nagy számú címzett méretezésére. Minden közzétett üzenet elérhetővé válik minden, a témakörben regisztrált előfizetés számára. A közzétevő üzenetet küld egy témakörnek, és egy vagy több előfizető megkapja az üzenet másolatát az ezen előfizetéseken beállított szűrési szabályoktól függően. Az előfizetések további szűrőket is használhatnak a fogadni kívánt üzenetek korlátozására. A közzétevők üzeneteket küldenek egy témakörbe ugyanúgy, ahogy üzeneteket küldenek egy várólistába. A felhasználók azonban nem kapnak üzeneteket közvetlenül a témakörből. Ehelyett a felhasználók üzeneteket kapnak a témakör előfizetései között. A témakör-előfizetés egy olyan virtuális várólistára hasonlít, amely megkapja a témakörbe küldött üzenetek másolatait. A felhasználók azonos módon fogadnak üzeneteket az előfizetéstől az üzenetek várólistából való fogadásával.

A várólista üzenetküldési funkciója közvetlenül a témakörre és az üzenet által fogadott funkciókra mutat egy előfizetéshez. Ez a funkció többek között azt jelenti, hogy az előfizetések az ebben a szakaszban korábban ismertetett mintákat támogatják a várólistákkal kapcsolatban: a versengő fogyasztók, az időbeli leválasztás, a terheléselosztás és a terheléselosztás.

### <a name="create-topics-and-subscriptions"></a>Témakörök és előfizetések létrehozása
A témakör létrehozása hasonló a várólista létrehozásához az előző szakaszban leírtak szerint. Témaköröket és előfizetéseket a [Azure Portal](service-bus-quickstart-topics-subscriptions-portal.md), a [PowerShell](service-bus-quickstart-powershell.md), a [CLI](service-bus-tutorial-topics-subscriptions-cli.md)vagy a [Resource Manager-sablonok](service-bus-resource-manager-namespace-topic.md)használatával hozhat létre. Ezután üzeneteket küldhet a témakörnek, és üzeneteket fogadhat az előfizetésből a [C#](service-bus-dotnet-how-to-use-topics-subscriptions.md), a [Java](service-bus-java-how-to-use-topics-subscriptions.md), a [Python](service-bus-python-how-to-use-topics-subscriptions.md), a [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md), a [php](service-bus-php-how-to-use-topics-subscriptions.md)és a [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)nyelven írt ügyfelek használatával. 

### <a name="rules-and-actions"></a>Szabályok és műveletek
Számos esetben a konkrét tulajdonságokkal rendelkező üzeneteket különböző módokon kell feldolgozni. A feldolgozás engedélyezéséhez megadhatja az előfizetéseket, hogy megkeresse a kívánt tulajdonságokkal rendelkező üzeneteket, majd végrehajtsa a tulajdonságok bizonyos módosításait. Míg Service Bus előfizetések a témakörbe küldött összes üzenetet láthatják, az üzenetek egy részhalmazát csak a virtuális előfizetési várólistára másolhatja. Ez a szűrés előfizetési szűrők használatával valósítható meg. Ezeket a módosításokat **szűrési műveleteknek** nevezzük. Előfizetés létrehozásakor megadhat egy szűrési kifejezést, amely az üzenet tulajdonságainál működik. A tulajdonságok lehetnek a Rendszertulajdonságok (például a **label** ) és az egyéni alkalmazások tulajdonságai (például **StoreName** ). Ebben az esetben az SQL FILTER kifejezés nem kötelező. SQL-szűrő kifejezés nélkül az előfizetésben definiált összes szűrési műveletet az adott előfizetés összes üzenetén végrehajtjuk.

Teljes körű működésre példaként tekintse meg a [TopicSubscriptionWithRuleOperationsSample mintát](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample) a githubon.

A lehetséges szűrési értékekkel kapcsolatos további információkért tekintse meg a [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) és a [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) osztály dokumentációját.

## <a name="java-message-service-jms-20-entities-preview"></a>Java Message Service (JMS) 2,0 entitások (előzetes verzió)
A következő entitások érhetők el a Java Message Service (JMS) 2,0 API-n keresztül.

  * Ideiglenes várólisták
  * Ideiglenes témakörök
  * Közös tartós előfizetések
  * Nem megosztott tartós előfizetések
  * Megosztott nem tartós előfizetések
  * Nem megosztva nem tartós előfizetések

További információ a [JMS 2,0-entitásokról](java-message-service-20-entities.md) és [azok használatáról](how-to-use-java-message-service-20.md).

## <a name="next-steps"></a>Következő lépések

További információt és példákat a Service Bus üzenetkezelés használatával kapcsolatban a következő speciális témakörökben talál:

* [Service Bus üzenetkezelés áttekintése](service-bus-messaging-overview.md)
* [Rövid útmutató: Üzenetek küldése és fogadása az Azure Portallal és a .NET-tel](service-bus-quickstart-portal.md)
* [Oktatóanyag: Leltár frissítése az Azure Portal és témakörök/előfizetések használatával](service-bus-tutorial-topics-subscriptions-portal.md)


