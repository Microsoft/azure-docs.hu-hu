---
title: .NET programozási útmutató – Azure Event Hubs (örökölt) | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan írhat kódot az Azure Event Hubs az Azure .NET SDK használatával.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: de731d591c367e386fe8ef1eef03f1b90e0fa126
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85314538"
---
# <a name="net-programming-guide-for-azure-event-hubs-legacy-microsoftazureeventhubs-package"></a>.NET programozási útmutató az Azure Event Hubshoz (örökölt Microsoft. Azure. EventHubs csomag)
Ez a cikk néhány gyakori forgatókönyvet ismertet a kód az Azure Event Hubs használatával történő írásához. A témakör feltételezi az Event Hubs szolgáltatással kapcsolatos előzetes ismeretek meglétét. Az Event Hubs fogalmi áttekintése: [Event Hubs – áttekintés](event-hubs-what-is-event-hubs.md).

> [!WARNING]
> Ez az útmutató a régi **Microsoft. Azure. EventHubs** csomaghoz készült. Javasoljuk, hogy a legújabb [Azure. Messaging. EventHubs](get-started-dotnet-standard-send-v2.md) csomag használatára [telepítse át](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md) a kódot.  


## <a name="event-publishers"></a>Esemény-közzétevők

A HTTP POST használatával vagy egy AMQP 1,0-kapcsolaton keresztül küldhet eseményeket az Event hubhoz. Válassza ki, hogy melyiket használja, és ha az adott forgatókönyvtől függ. Az AMQP 1.0-kapcsolatok mérése közvetített kapcsolatként történik a Service Bus szolgáltatásban, és az olyan forgatókönyvekben megfelelőbbek, ahol gyakoriak a nagyobb üzenetmennyiségek és alacsony késés szükséges, mivel ezek állandó üzenetkezelési csatornát biztosítanak.

A .NET által felügyelt API-k használatakor az adatoknak az Event Hubs számára történő közzétételére szolgáló elsődleges szerkezetek az [EventHubClient][] és az [EventData][] osztály. A [EventHubClient][] biztosítja azt a AMQP kommunikációs csatornát, amelyen keresztül az Event hub az eseményeket elküldi. A [EventData][] osztály egy eseményt jelöl, és az üzenetek az Event hub-ba való közzétételére szolgál. Ebbe az osztályba beletartozik a törzs, az egyes metaadatok (Tulajdonságok) és a fejléc információi (SystemProperties) az eseményről. A [EventData][] objektumhoz más tulajdonságok is bekerülnek, ahogy az egy Event hub-n halad át.

## <a name="get-started"></a>Bevezetés
A Event Hubst támogató .NET-osztályok a [Microsoft. Azure. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) NuGet csomagban találhatók. A Visual Studio Solution Explorer vagy a [Package Manager konzol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) használatával is telepíthető. Ehhez adja ki a következő parancsot a [Csomagkezelő konzol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) ablakában:

```shell
Install-Package Microsoft.Azure.EventHubs
```

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

A Event Hubs létrehozásához használhatja a Azure Portal, Azure PowerShell vagy az Azure CLI-t. Részletekért lásd: [Event Hubs névtér és az Event hub létrehozása a Azure Portal használatával](event-hubs-create.md).

## <a name="create-an-event-hubs-client"></a>Event Hubs-ügyfél létrehozása

A Event Hubshoz való interakció elsődleges osztálya a [Microsoft. Azure. EventHubs. EventHubClient][EventHubClient]. Ezt az osztályt a [CreateFromConnectionString](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createfromconnectionstring) metódussal hozhatja létre, ahogy az az alábbi példában is látható:

```csharp
private const string EventHubConnectionString = "Event Hubs namespace connection string";
private const string EventHubName = "event hub name";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
{
    EntityPath = EventHubName

};
eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
```

## <a name="send-events-to-an-event-hub"></a>Események küldése az Event hub-nak

Eseményeket küld egy [EventHubClient][] -példány létrehozásával, és aszinkron módon küldi el a [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) metódussal. Ez a metódus egyetlen [EventData][] -példány paramétert vesz igénybe, és aszinkron módon küldi el azt egy Event hub-nak.

## <a name="event-serialization"></a>Eseményszerializáció

A [EventData][] osztálynak [két túlterhelt konstruktora](/dotnet/api/microsoft.azure.eventhubs.eventdata.-ctor) van, amelyek számos paramétert, bájtot vagy byte tömböt foglalnak magukban, amelyek az esemény adattartalmát jelölik. Ha JSON-t használ az [EventData][] osztállyal, az **Encoding.UTF8.GetBytes()** használatával kérheti le a bájttömböt a JSON-kódolású sztringhez. Például:

```csharp
for (var i = 0; i < numMessagesToSend; i++)
{
    var message = $"Message {i}";
    Console.WriteLine($"Sending message: {message}");
    await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
}
```

## <a name="partition-key"></a>Partíciókulcs

> [!NOTE]
> Ha nem ismeri a partíciókat, tekintse meg [ezt a cikket](event-hubs-features.md#partitions). 

Az események küldésekor megadhat egy olyan értéket, amely kivonattal létrehoz egy partíció-hozzárendelést. A partíciót a [PartitionSender. PartitionID](/dotnet/api/microsoft.azure.eventhubs.partitionsender.partitionid) tulajdonsággal adhatja meg. A partíciók használatára vonatkozó döntés azonban a rendelkezésre állás és a konzisztencia közötti választást is magában foglalja. 

### <a name="availability-considerations"></a>Rendelkezésre állási szempontok

A partíciós kulcs használata nem kötelező, és körültekintően kell megfontolnia, hogy használja-e az egyiket. Ha nem ad meg partíciókulcsot az események közzétételekor, a rendszer ciklikus időszeleteléses hozzárendelést használ. Sok esetben a partíciós kulcs használata jó választás, ha az események rendezése fontos. Partíciós kulcs használatakor ezek a partíciók egy adott csomóponton rendelkezésre állást igényelnek, és időbeli kimaradások léphetnek fel. például a számítási csomópontok újraindításakor és javításakor. Így ha egy partíció-azonosítót állít be, és a partíció valamilyen okból elérhetetlenné válik, akkor a partíción lévő adat elérésére tett kísérlet sikertelen lesz. Ha a magas rendelkezésre állás a legfontosabb, ne határozzon meg partíciós kulcsot; Ebben az esetben az eseményeket a korábban ismertetett ciklikus multiplexelés használatával küldik el a partícióknak. Ebben az esetben a rendelkezésre állás (partíció-azonosító nélkül) és a konzisztencia (a partíciós azonosító rögzítése) között explicit módon választhat.

Egy másik szempont a feldolgozási események késésének kezelése. Bizonyos esetekben érdemes lehet eldobni az adatvesztést, és újra kell próbálkoznia, mint a feldolgozás megtartása, ami esetleg további alsóbb rétegbeli feldolgozási késéseket eredményezhet. Egy tőzsdei ticker esetében például érdemes megvárni a teljes naprakész adatok megjelenítését, azonban egy élő csevegésben vagy VOIP-forgatókönyvben inkább az adatok gyors, még akkor is, ha nem fejeződött be.

Ezeknek a rendelkezésre állási szempontoknak a figyelembe vételekor a következő hiba-kezelési stratégiák közül választhat:

- Leállítás (Event Hubs olvasásának leállítása a dolgok kijavítása után)
- Drop (az üzenetek nem fontosak, eldobása)
- Újrapróbálkozás (próbálja megismételni az üzeneteket, ahogy jónak látja)

A rendelkezésre állás és a konzisztencia közötti kompromisszumokkal kapcsolatos további információkért lásd: [rendelkezésre állás és konzisztencia a Event Hubsban](event-hubs-availability-and-consistency.md). 

## <a name="batch-event-send-operations"></a>Kötegelt eseményküldési műveletek

Az események kötegekben történő küldése segíthet az átviteli sebesség növelésében. A [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API-val létrehozhat egy köteget, amely számára az adatobjektumok később hozzáadhatók egy [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) -híváshoz.

Egyetlen köteg nem lépheti túl az esemény 1 MB-os korlátját. Továbbá a kötegben lévő egyes üzenetek ugyanazzal a közzétevői identitással rendelkezik majd. A küldő felelőssége annak biztosítása, hogy a köteg mérete nem haladja meg az események maximális méretét. Ha mégis meghaladja, az ügyfél **Küldési** hibát jelez. A [EventHubClient. CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) segítő metódussal biztosíthatja, hogy a köteg ne haladja meg az 1 MB-ot. Üres [EventDataBatch](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch) kap a [CreateBatch](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.createbatch) API-ból, majd a [TryAdd](/dotnet/api/microsoft.azure.eventhubs.eventdatabatch.tryadd) használatával hozzáadhat eseményeket a köteg létrehozásához. 

## <a name="send-asynchronously-and-send-at-scale"></a>Aszinkron küldés és nagy léptékű küldés

Az Event hub-nak aszinkron módon kell elküldeni az eseményeket. A Küldés aszinkron módon megnöveli azt a sebességet, amellyel az ügyfél képes eseményeket küldeni. A [SendAsync](/dotnet/api/microsoft.azure.eventhubs.eventhubclient.sendasync) egy [feladat](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) objektumot ad vissza. Az ügyfél újrapróbálkozási beállításainak vezérléséhez használhatja a [RetryPolicy](/dotnet/api/microsoft.servicebus.retrypolicy) osztályt.

## <a name="event-consumers"></a>Eseményfelhasználók
Az [EventProcessorHost][] osztály az eseményközpontokból származó adatokat dolgozza fel. Akkor használja ezt a megvalósítást, ha a .NET platformon hoz létre eseményolvasókat. Az [EventProcessorHost][] egy szálbiztos, több folyamatot lehetővé tevő, biztonságos futtatókörnyezetet biztosít az eseményfeldolgozói megvalósításokhoz, ami lehetővé teszi az ellenőrzőpontok használatát és a partícióbérlés-kezelést is.

Az [EventProcessorHost][] osztály használatához megvalósítható az [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor). Ez az interfész négy módszert tartalmaz:

* [OpenAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.openasync)
* [CloseAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.closeasync)
* [ProcessEventsAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processeventsasync)
* [ProcessErrorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor.processerrorasync)

Az események feldolgozásának megkezdéséhez hozza létre a [EventProcessorHost][], és adja meg az Event hub megfelelő paramétereit. Például:

> [!NOTE]
> A EventProcessorHost és a hozzá kapcsolódó osztályok a **Microsoft. Azure. EventHubs. Processor** csomagban találhatók. Adja hozzá a csomagot a Visual Studio-projekthez a [cikk](event-hubs-dotnet-framework-getstarted-send.md#add-the-event-hubs-nuget-package) utasításait követve, vagy a következő parancs kiadásával a [Package Manager konzol](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) ablakban: `Install-Package Microsoft.Azure.EventHubs.Processor` .

```csharp
var eventProcessorHost = new EventProcessorHost(
        EventHubName,
        PartitionReceiver.DefaultConsumerGroupName,
        EventHubConnectionString,
        StorageConnectionString,
        StorageContainerName);
```

Ezután hívja meg a [RegisterEventProcessorAsync](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessorhost.registereventprocessorasync) -t, hogy regisztrálja a [IEventProcessor](/dotnet/api/microsoft.azure.eventhubs.processor.ieventprocessor) implementációját a futtatókörnyezettel:

```csharp
await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();
```

Ezen a ponton a gazdagép a "kapzsi" algoritmus használatával megpróbál címbérletet beszerezni az Event hub minden partícióján. Ezek a bérletek egy adott időkeretre vonatkozóan utolsóként jelennek meg, és ezután meg kell újítani. Ahogy újabb csomópontok, esetünkben feldolgozópéldányok válnak online állapotúvá, bérletfoglalásokat végeznek, és idővel a terhelés elmozdul a csomópontok között, ahogy egyre több bérletet próbálnak szerezni.

![Event Processor Host](./media/event-hubs-programming-guide/IC759863.png)

Idővel kialakul egy egyensúlyi állapot. Ennek a dinamikus képességnek a segítségével processzoralapú automatikus skálázás alkalmazható a felhasználókra felfelé és lefelé méretezéshez egyaránt. Mivel Event Hubs nem rendelkezik az üzenetek számának közvetlen fogalmával, az átlagos CPU-kihasználtság gyakran a legjobb megoldás a háttér-vagy fogyasztói skála mérésére. Ha a közzétevők idővel több eseményt tesznek közzé, mint amennyit a felhasználók fel tudnak dolgozni, a felhasználókon a processzor növelésével indítható el a feldolgozópéldányok számának automatikus skálázása.

Az [EventProcessorHost][] osztály megvalósít továbbá egy Azure Storage-alapú ellenőrzőpont-kezelési mechanizmust is. A mechanizmus az eltolást partíciónkénti alapon tárolja, így mindegyik felhasználó megállapíthatja, hogy melyik volt az előző felhasználó utolsó ellenőrzőpontja. Ahogy a partíciók váltanak a csomópontok között a bérletek használatával, ez a szinkronizálási mechanizmus az, amely leginkább támogatja a terhelés áthelyezését.

## <a name="publisher-revocation"></a>Közzétevők visszavonása

Az Event Processor Host speciális futásidejű funkciói mellett a Event Hubs szolgáltatás lehetővé teszi a [közzétevők visszavonását](/rest/api/eventhub/revoke-publisher) annak érdekében, hogy letiltsa bizonyos közzétevők számára esemény küldését az Event hub-ba. Ezek a funkciók akkor hasznosak, ha a közzétevői tokent feltörték, vagy egy szoftverfrissítés nem megfelelő viselkedést okoz. Ilyen helyzetekben a közzétevő identitása (amely a SAS-token részét képezi) blokkolható az események közzétételének a megakadályozásához.

> [!NOTE]
> Jelenleg csak a REST API támogatja ezt a funkciót (a[közzétevő visszavonása](/rest/api/eventhub/revoke-publisher)).

A közzétevők visszavonásával és az eseményközpontokba közzétevőként való küldés módjával kapcsolatban a [Event Hubs Large Scale Secure Publishing](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) (Event Hubs nagyléptékű biztonságos közzététele) mintában tekinthet meg további információt.

## <a name="next-steps"></a>További lépések

Az Event Hubs-forgatókönyvekkel kapcsolatos további információkért látogasson el a következő hivatkozásokra:

* [Event Hubs API overview (Event Hubs API – áttekintés)](event-hubs-api-overview.md)
* [Mi az Event Hubs?](event-hubs-what-is-event-hubs.md)
* [Rendelkezésre állás és konzisztencia az Event Hubsban](event-hubs-availability-and-consistency.md)
* [Event Processor Host API-referenciája](/dotnet/api/microsoft.servicebus.messaging.eventprocessorhost)

[NamespaceManager]: /dotnet/api/microsoft.servicebus.namespacemanager
[EventHubClient]: /dotnet/api/microsoft.azure.eventhubs.eventhubclient
[EventData]: /dotnet/api/microsoft.azure.eventhubs.eventdata
[CreateEventHubIfNotExists]: /dotnet/api/microsoft.servicebus.namespacemanager.createeventhubifnotexists
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.eventdata#Microsoft_ServiceBus_Messaging_EventData_PartitionKey
[EventProcessorHost]: /dotnet/api/microsoft.azure.eventhubs.processor
