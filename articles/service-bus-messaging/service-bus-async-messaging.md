---
title: Aszinkron üzenetkezelés Service Bus | Microsoft Docs
description: Megtudhatja, hogyan támogatja a Azure Service Bus a asynchronism a várólistákkal, témakörökkel és előfizetésekkel rendelkező tárolási és továbbítási mechanizmussal.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e37c18b95bca7ef1e6e8f0d74976bb73b214624a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102500631"
---
# <a name="asynchronous-messaging-patterns-and-high-availability"></a>Aszinkron üzenetkezelési minták és magas rendelkezésre állás

Az aszinkron üzenetkezelés számos különböző módon valósítható meg. A várólisták, témakörök és előfizetések esetében Azure Service Bus az asynchronism-t egy áruházbeli és egy továbbítási mechanizmuson keresztül támogatja. A normál (szinkron) művelet során üzeneteket küld a várólistáknak és témaköröknek, és üzeneteket fogadhat a várólistákból és előfizetésekről. Az írási alkalmazások attól függnek, hogy ezek az entitások mindig elérhetők-e. Ha az entitások állapota számos körülmény miatt megváltoznak, a lehető legkevesebb képességet biztosító entitást kell biztosítania, amely kielégíti a legtöbb igényt.

Az alkalmazások jellemzően aszinkron üzenetkezelési mintákat használnak számos kommunikációs forgatókönyv engedélyezéséhez. Olyan alkalmazásokat hozhat létre, amelyekben az ügyfelek üzeneteket küldhetnek a szolgáltatásoknak, még akkor is, ha a szolgáltatás nem fut. Az olyan alkalmazások esetében, amelyek a kommunikációt tapasztalják, a várólista a pufferek közötti kommunikációhoz szükséges hely biztosításával segítheti a terhelést. Végül egy egyszerű, de hatékony terheléselosztó használatával több gépen is terjesztheti az üzeneteket.

Ezen entitások rendelkezésre állásának fenntartása érdekében vegye figyelembe, hogy az entitások számos különböző módon jelenhetnek meg tartós üzenetküldési rendszer esetén. Általánosságban azt láthatjuk, hogy az entitás elérhetetlenné válik az alábbi módon írt alkalmazások számára:

* Nem lehet üzeneteket küldeni.
* Nem lehet üzeneteket fogadni.
* Az entitások nem kezelhetők (entitások létrehozása, lekérése, frissítése vagy törlése).
* Nem lehet kapcsolódni a szolgáltatáshoz.

Ezeknél a hibáknál különböző meghibásodási módok léteznek, amelyek lehetővé teszik, hogy az alkalmazások bizonyos szinten továbbra is végezzenek munkát. Például egy olyan rendszer, amely képes üzeneteket küldeni, de nem fogadja őket, továbbra is fogadhat rendeléseket az ügyfelektől, de nem tudja feldolgozni ezeket a rendeléseket. Ez a témakör a lehetséges problémákat ismerteti, valamint a problémák enyhítésének módját. Service Bus számos olyan megoldást vezetett be, amelyeknek be kell jelentkezniük, és ez a témakör a beléptetési megoldások használatának szabályozására vonatkozó szabályokat is tárgyalja.

## <a name="reliability-in-service-bus"></a>Megbízhatóság a Service Busban
Az üzenetek és az entitások problémáinak kezeléséhez számos lehetőség áll rendelkezésre, és vannak olyan irányelvek, amelyek az ilyen enyhítések megfelelő használatát szabályozzák. Az irányelvek megismeréséhez először meg kell ismernie, hogy milyen hibák jelentkezhetnek Service Bus. Az Azure-rendszerek kialakítása miatt ezek a problémák általában rövid életűek. Magas szinten a nem rendelkezésre állás különböző okai a következők:

* Olyan külső rendszer szabályozása, amelyen Service Bus függ. A szabályozás a tárolási és számítási erőforrásokkal folytatott interakciók során történik.
* Probléma olyan rendszer esetén, amelyen Service Bus függ. A tárterület adott része például problémákat okozhat.
* Service Bus meghibásodása egyetlen alrendszeren. Ebben az esetben a számítási csomópontok inkonzisztens állapotba kerülhetnek, és újra kell indítani magukat, ami minden entitás számára elérhetővé teszi a többi csomópontnak való terheléselosztást. Ez pedig rövid ideig tartó lassú feldolgozást eredményezhet.
* Az Azure-adatközponton belüli Service Bus meghibásodása. Ez egy "katasztrofális hiba", amelynek során a rendszer több percig vagy néhány órán keresztül nem érhető el.

> [!NOTE]
> A **tárolás** kifejezés az Azure Storage és a SQL Azure is jelentheti.
> 
> 

A Service Bus számos megoldást tartalmaz a problémákra. A következő fejezetek tárgyalják az egyes problémákat és azok megfelelő megoldásait.

### <a name="throttling"></a>Throttling
A Service Bus a szabályozás lehetővé teszi a kooperatív üzenetek arányának kezelését. Minden egyes Service Bus csomópont számos entitást ad meg. Ezek az entitások a rendszeren a CPU, a memória, a tárolás és az egyéb aspektusok tekintetében igénylik a rendszer követelményeit. Ha ezen aspektusok bármelyike észleli a meghatározott küszöbértékeket meghaladó használatot, Service Bus megtagadhatja egy adott kérést. A hívó [ServerBusyException][ServerBusyException] kap, és 10 másodperc elteltével újrapróbálkozik.

Enyhítő megoldásként a kódnak el kell olvasnia a hibát, és legalább 10 másodpercig le kell állítani az üzenet újrapróbálkozásait. Mivel a hiba az ügyfélalkalmazás különböző részeiben fordulhat elő, a rendszer azt várja, hogy mindegyik darab önállóan végrehajtja az újrapróbálkozási logikát. A kód csökkentheti annak valószínűségét, hogy a particionálás a várólistán vagy a témakörben engedélyezve van.

### <a name="issue-for-an-azure-dependency"></a>Azure-függőséggel kapcsolatos probléma
Az Azure-on belüli egyéb összetevők időnként szolgáltatási problémákat okozhatnak. Ha például egy Service Bus által használt rendszer frissítése folyamatban van, a rendszer átmenetileg csökkentheti a képességeket. Az ilyen típusú problémák megoldásához Service Bus rendszeresen vizsgálja és implementálja a mérsékléseket. Megjelennek az ilyen enyhítések mellékhatásai. Ha például a tárterülettel kapcsolatos átmeneti problémákat szeretné kezelni, Service Bus megvalósít egy olyan rendszer megvalósítását, amely lehetővé teszi, hogy az üzenetek küldésének műveletei következetesen működjenek. A mérséklés jellegéből adódóan az elküldött üzenetek akár 15 percet is igénybe vehetnek az érintett várólistában vagy előfizetésben, és készen állnak a fogadási műveletre. Általánosságban elmondható, hogy a legtöbb entitás nem fogja tapasztalni ezt a problémát. Az Azure-ban Service Busban lévő entitások száma miatt azonban ez a megoldás esetenként Service Bus ügyfelek kis részhalmazára is szükség van.

### <a name="service-bus-failure-on-a-single-subsystem"></a>Egyetlen alrendszer meghibásodása Service Bus
Bármely alkalmazással a körülmények a Service Bus belső összetevőjének inkonzisztensvé válását okozhatják. Ha Service Bus észleli ezt, az adatokat gyűjt az alkalmazásból, hogy segítséget nyújtsanak a történtek diagnosztizálásában. Az adatok összegyűjtése után az alkalmazás újraindul egy konzisztens állapotba való visszatérési kísérlet során. Ez a folyamat meglehetősen gyorsan zajlik, és egy entitás, amely úgy tűnik, hogy legfeljebb néhány percig elérhetetlenné válik, bár a jellemző leállási idő sokkal rövidebb.

Ezekben az esetekben az ügyfélalkalmazás egy [System. timeoutexception osztályról][System.TimeoutException] -vagy [MessagingException][MessagingException] -kivételt hoz létre. Service Bus a probléma megoldását automatikus ügyfél-újrapróbálkozási logika formájában tartalmazza. Ha az újrapróbálkozási időszak kimerült, és nem érkezik meg az üzenet, a cikk a leállások és a [katasztrófák kezelésével][handling outages and disasters]foglalkozó cikkben említettek alapján is megismerheti.

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte az aszinkron üzenetkezelés alapjait Service Busban, olvassa el az [kimaradások és a katasztrófák kezelésével][handling outages and disasters]kapcsolatos további részleteket.

[ServerBusyException]: /dotnet/api/microsoft.servicebus.messaging.serverbusyexception
[System.TimeoutException]: /dotnet/api/system.timeoutexception
[MessagingException]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Microsoft.ServiceBus.Messaging.MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[MessageReceiver]: /dotnet/api/microsoft.servicebus.messaging.messagereceiver
[QueueClient]: /dotnet/api/microsoft.servicebus.messaging.queueclient
[TopicClient]: /dotnet/api/microsoft.servicebus.messaging.topicclient
[Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.pairednamespaceoptions
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[SendAvailabilityPairedNamespaceOptions]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[PairNamespaceAsync]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[EnableSyphon]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[System.TimeSpan.Zero]: /dotnet/api/system.timespan.zero
[IsTransient]: /dotnet/api/microsoft.servicebus.messaging.messagingexception
[UnauthorizedAccessException]: /dotnet/api/system.unauthorizedaccessexception
[BacklogQueueCount]: /dotnet/api/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions
[handling outages and disasters]: service-bus-outages-disasters.md