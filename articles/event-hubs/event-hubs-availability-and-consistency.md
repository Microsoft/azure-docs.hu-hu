---
title: Rendelkezésre állás és konzisztencia – Azure Event Hubs | Microsoft Docs
description: Hogyan biztosítható a maximális rendelkezésre állás és konzisztencia az Azure Event Hubs partíciók használatával.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 325cc80daba2a44dedbd5e09ac4858ae2815c1cd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425923"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Rendelkezésre állás és konzisztencia az Event Hubsban
Ez a cikk az Azure Event Hubs által támogatott rendelkezésre állásról és konzisztenciaról nyújt információkat. 

## <a name="availability"></a>Rendelkezésre állás
Az Azure Event Hubs az egyes gépek katasztrofális meghibásodásának kockázatát, vagy akár az adatközponton belüli több meghibásodási tartományt átfedő fürtökön futó állványokat is teljesít. Az eszköz átlátható meghibásodások észlelését és feladatátvételi mechanizmusokat valósít meg, így a szolgáltatás továbbra is a biztos szolgáltatási szinten fog működni, és jellemzően nem észlelhető megszakítások, ha ilyen hibák történnek. 

Ha egy Event Hubs névteret engedélyeztek a [rendelkezésre állási zónákkal](../availability-zones/az-overview.md) , a kiesési kockázat több, mint három fizikailag elkülönített létesítmény között van, és a szolgáltatás rendelkezik elegendő kapacitási tartalékokkal, hogy azonnal megbirkózjon a teljes létesítmény teljes, katasztrofális elvesztésével. További információ: [Azure Event Hubs-geo-vész-helyreállítás](event-hubs-geo-dr.md).

Ha egy ügyfélalkalmazás egy partíció megadása nélkül küld eseményeket egy Event hubhoz, a rendszer automatikusan elosztja az eseményeket az Event hub partíciói között. Ha egy partíció valamilyen okból nem érhető el, az események a fennmaradó partíciók között oszlanak meg. Ez a viselkedés lehetővé teszi a legnagyobb mennyiségű időt. A maximális Felskálázási időt igénylő használati esetekben ez a modell inkább előnyben részesített, ha az eseményeket egy adott partícióra küldi. 

### <a name="availability-considerations-when-using-a-partition-id-or-key"></a>Rendelkezésre állási megfontolások partíciós azonosító vagy kulcs használatakor
Partíció-azonosító vagy partíciós kulcs használata nem kötelező. Alaposan gondolja át, hogy használja-e az egyiket. Ha nem ad meg partíció-azonosítót/kulcsot egy esemény közzétételekor, Event Hubs egyensúlyt a partíciók közötti terheléssel. Partíciós azonosító/kulcs használata esetén ezek a partíciók a rendelkezésre állást egy adott csomóponton igénylik, és az idő múlásával kimaradások léphetnek fel. Előfordulhat például, hogy a számítási csomópontokat újra kell indítani vagy javítani kell. Ha tehát egy partíció AZONOSÍTÓját/kulcsát állítja be, és a partíció valamilyen okból elérhetetlenné válik, akkor a partíción lévő adat elérésére tett kísérlet sikertelen lesz. Ha a magas rendelkezésre állás a legfontosabb, ne határozza meg a partíció AZONOSÍTÓját/kulcsát. Ebben az esetben az eseményeket egy belső terheléselosztási algoritmus használatával küldik el a partícióknak. Ebben az esetben a rendelkezésre állás (partíciós azonosító/kulcs) és a konzisztencia (az események rögzítése egy adott partícióra) között explicit módon választhat. A partíció-AZONOSÍTÓk/kulcsok használata leértékeli az Event hub partíciós szintre való rendelkezésre állását. 

### <a name="availability-considerations-when-handling-delays-in-processing-events"></a>Rendelkezésre állási megfontolások a feldolgozási események késésének kezelésekor
Egy másik szempont a feldolgozási események késését kezelő fogyasztói alkalmazás. Bizonyos esetekben jobb lehet, ha a fogyasztói alkalmazás eldobja az adatvesztést, és nem szeretné megtartani a feldolgozást, ami esetleg további későbbi feldolgozási késéseket eredményezhet. Egy tőzsdei ticker esetében például érdemes megvárni a teljes naprakész adatok megjelenítését, azonban egy élő csevegésben vagy VOIP-forgatókönyvben inkább az adatok gyors, még akkor is, ha nem fejeződött be.

Ezeknek a rendelkezésre állási szempontoknak a figyelembe vételekor a fogyasztói alkalmazás a következő hibakezelő stratégiák egyikét választhatja:

- Leállítás (az Event hub olvasásának leállítása a problémák kijavítása előtt)
- Drop (az üzenetek nem fontosak, eldobása)
- Újrapróbálkozás (próbálja megismételni az üzeneteket, ahogy jónak látja)


## <a name="consistency"></a>Konzisztencia
Bizonyos helyzetekben fontos lehet az események rendezése. Előfordulhat például, hogy azt szeretné, hogy a háttérrendszer a DELETE parancs előtt dolgozza fel a frissítési parancsot. Ebben az esetben az ügyfélalkalmazás az eseményeket egy adott partícióra küldi, hogy megőrizzék a megrendelést. Ha egy fogyasztói alkalmazás ezeket az eseményeket a partícióból használja fel, akkor a rendszer a sorrendben olvassa be őket. 

Ha ezt a konfigurációt használja, vegye figyelembe, hogy ha az adott partíció, amely számára a Küldés nem érhető el, hibaüzenetet fog kapni. Ha összehasonlítási pontként nem rendelkezik egyetlen partícióval, a Event Hubs szolgáltatás elküldi az eseményt a következő rendelkezésre álló partíciónak.


## <a name="appendix"></a>Függelék

### <a name="send-events-to-a-specific-partition"></a>Események küldése egy adott partícióra
Ebből a szakaszból megtudhatja, hogyan küldhet eseményeket egy adott partícióra a C#, a Java, a Python és a JavaScript használatával. 

### <a name="net"></a>[.NET](#tab/dotnet)
A teljes mintakód, amely bemutatja, hogyan küldhet esemény-köteget egy Event hub-ba (partíció-azonosító/kulcs beállítása nélkül), lásd: [események küldése és fogadása az azure Event Hubs-.net-ről (Azure. Messaging. EventHubs)](event-hubs-dotnet-standard-getstarted-send.md).

Ha egy adott partícióra szeretne eseményeket küldeni, hozza létre a köteget a [EventHubProducerClient. CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) metódussal a `PartitionId` vagy a `PartitionKey` in [CreateBatchOptions](//dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions)megadásával. A következő kód a partíciós kulcs megadásával küldi el az események kötegét egy adott partícióra. 

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

A [EventHubProducerClient. SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) metódust a [SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions) **PartitionID** vagy **PartitionKey** megadásával is használhatja.

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```

### <a name="java"></a>[Java](#tab/java)
A teljes mintakód, amely bemutatja, hogyan küldhet esemény-köteget egy Event hub-nak (partíció-azonosító/kulcs beállítása nélkül), lásd: a [Java használata események küldésére és fogadására az azure Event Hubs (Azure-Messaging-eventhubs) használatával](event-hubs-java-get-started-send.md).

Ha egy adott partícióra szeretne eseményeket küldeni, hozza létre a Batch-t a [createBatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) metódussal a **partíció-azonosító** vagy a **partíciós kulcs** megadásával a [createBatchOptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions)-ben. A következő kód a partíciós kulcs megadásával küldi el az események kötegét egy adott partícióra. 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

A [EventHubProducerClient. Send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) metódust a **partíció-azonosító** vagy a **partíciós kulcs** megadásával is elvégezheti a [SendOptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions)-ben.

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```

### <a name="python"></a>[Python](#tab/python) 
A teljes mintakód, amely bemutatja, hogyan küldhet esemény-köteget egy Event hub-ba (partíció-azonosító/kulcs beállítása nélkül), lásd: [események küldése vagy fogadása az Event huboktól a Python (Azure-eventhub) használatával](event-hubs-python-get-started-send.md).

Ha a metódus használatával szeretne eseményeket küldeni egy adott partícióra, akkor [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) a `partition_id` vagy a `partition_key` . Ezután a metódus használatával [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) küldje el a köteget az Event hub partíciójának. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

A [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) metódust is használhatja az értékek `partition_id` vagy paraméterek megadásával `partition_key` .

```python
producer.send_batch(event_data_batch, partition_key="cities")
```


### <a name="javascript"></a>[JavaScript](#tab/javascript)
A teljes mintakód, amely bemutatja, hogyan küldhet esemény-köteget egy Event hub-ba (partíció-azonosító/kulcs beállítása nélkül), lásd: [események küldése az Event hubokból vagy események fogadása a JavaScript használatával (Azure/Event-hubok)](event-hubs-node-get-started-send.md).

Ha egy adott partícióra szeretne eseményeket küldeni, [hozzon létre egy köteget](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) az [EventHubProducerClient. CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) objektum használatával a vagy a érték megadásával `partitionId` `partitionKey` . Ezután küldje el a köteget az Event hubhoz a [EventHubProducerClient. SendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) metódus használatával. 

Tekintse meg a következő példát.

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

A [EventHubProducerClient. sendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) metódust a **partíció-azonosító** vagy a **partíciós kulcs** megadásával is elvégezheti a [SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions)-ben.

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---


## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs szolgáltatás áttekintése](./event-hubs-about.md)
* [Eseményközpont létrehozása](event-hubs-create.md)
