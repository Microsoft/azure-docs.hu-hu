---
title: Rendelkezésre állás és konzisztencia – Azure Event Hubs | Microsoft Docs
description: Hogyan biztosítható a maximális rendelkezésre állás és konzisztencia az Azure Event Hubs partíciók használatával.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882195"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Rendelkezésre állás és konzisztencia az Event Hubsban
Ez a cikk az Azure Event Hubs által támogatott rendelkezésre állásról és konzisztenciaról nyújt információkat. 

## <a name="availability"></a>Rendelkezésre állás
Az Azure Event Hubs az egyes gépek katasztrofális meghibásodásának kockázatát, vagy akár az adatközponton belüli több meghibásodási tartományt átfedő fürtökön futó állványokat is teljesít. Az eszköz átlátható meghibásodások észlelését és feladatátvételi mechanizmusokat valósít meg, így a szolgáltatás továbbra is a biztos szolgáltatási szinten fog működni, és jellemzően nem észlelhető megszakítások, ha ilyen hibák történnek. Ha egy Event Hubs névtér lett létrehozva a [rendelkezésre állási zónák](../availability-zones/az-overview.md)számára engedélyezett beállítással, a kiesési kockázat tovább terjed három, fizikailag elkülönített létesítmény között, és a szolgáltatás elegendő kapacitási tartalékokkal rendelkezik, hogy azonnal megbirkózzanak a teljes létesítmény teljes, katasztrofális elvesztésével. További információ: [Azure Event Hubs-geo-vész-helyreállítás](event-hubs-geo-dr.md).

Amikor egy ügyfélalkalmazás eseményeket küld az Event hubhoz, a rendszer automatikusan elosztja az eseményeket az Event hub partíciói között. Ha egy partíció valamilyen okból nem érhető el, az események a fennmaradó partíciók között oszlanak meg. Ez a viselkedés lehetővé teszi a legnagyobb mennyiségű időt. A maximális Felskálázási időt igénylő használati esetekben ez a modell inkább előnyben részesített, ha az eseményeket egy adott partícióra küldi. További információ: [Partitions (partíciók](event-hubs-scalability.md#partitions)).

## <a name="consistency"></a>Konzisztencia
Bizonyos helyzetekben fontos lehet az események rendezése. Előfordulhat például, hogy azt szeretné, hogy a háttérrendszer a DELETE parancs előtt dolgozza fel a frissítési parancsot. Ebben az esetben az ügyfélalkalmazás az eseményeket egy adott partícióra küldi, hogy megőrizzék a megrendelést. Ha egy fogyasztói alkalmazás ezeket az eseményeket a partícióból használja fel, akkor a rendszer a sorrendben olvassa be őket. 

Ha ezt a konfigurációt használja, vegye figyelembe, hogy ha az adott partíció, amely számára a Küldés nem érhető el, hibaüzenetet fog kapni. Ha összehasonlítási pontként nem rendelkezik egyetlen partícióval, a Event Hubs szolgáltatás elküldi az eseményt a következő rendelkezésre álló partíciónak.

Az egyik lehetséges megoldás a rendezés biztosítására, ugyanakkor a maximális idő maximalizálása az eseményeknek az Event Processing-alkalmazás részeként való összesítése lenne. Ennek a legegyszerűbb módja, ha az eseményt egy egyéni sorozatszám-tulajdonsággal pecsételi le.

Ebben a forgatókönyvben a termelői ügyfél eseményeket küld az Event hub egyik rendelkezésre álló partíciójának, és beállítja a megfelelő sorozatszámot az alkalmazásból. Ehhez a megoldáshoz a feldolgozó alkalmazásnak meg kell őriznie az állapotot, de a küldők számára nagyobb valószínűséggel elérhetőnek kell lennie.

## <a name="appendix"></a>Függelék

### <a name="net-examples"></a>.NET-példák

#### <a name="send-events-to-a-specific-partition"></a>Események küldése egy adott partícióra
Állítsa be a partíciós kulcsot egy eseményre, vagy használjon egy `PartitionSender` objektumot (ha a régi Microsoft. Azure. Messaging függvénytárat használja), hogy csak egy adott partícióra küldjön eseményeket. Így biztosíthatja, hogy ha ezek az események beolvashatók a partícióból, a rendszer a sorrendben olvassa be őket. 

Ha az újabb **Azure. Messaging. EventHubs** függvénytárat használja, olvassa el a [kód áttelepítése a PartitionSender-ről a EventHubProducerClient-re című témakört az események partícióba való közzétételéhez](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 vagy újabb)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 vagy korábbi)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

### <a name="set-a-sequence-number"></a>Sorszám megadása
Az alábbi példa az eseményt egy egyéni sorszám tulajdonsággal pecsételi le. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 vagy újabb)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 vagy korábbi)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

Ez a példa az eseményt az Event hub egyik elérhető partíciójában küldi el, és beállítja az alkalmazáshoz tartozó sorozatszámot. Ehhez a megoldáshoz a feldolgozó alkalmazásnak meg kell őriznie az állapotot, de a küldők számára nagyobb valószínűséggel elérhetőnek kell lennie.

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs szolgáltatás áttekintése](./event-hubs-about.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
