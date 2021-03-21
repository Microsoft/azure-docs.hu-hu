---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: fe80a71125d43220e408eab7b07aeedcafa0a526
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102473624"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x és újabb

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTimeUtc": ""
            }
        }
    }
}  
```

|Tulajdonság  |Alapértelmezett | Description |
|---------|---------|---------|
|batchCheckpointFrequency|1|Az EventHub kurzor ellenőrzőpontjának létrehozása előtt feldolgozandó eseményvezérelt kötegek száma.|
|eventProcessorOptions/maxBatchSize|10|A fogadott események száma fogadási hurokban.|
|eventProcessorOptions/prefetchCount|300|A mögöttes alapértelmezett előzetes lehívási szám `EventProcessorHost` . A minimálisan megengedett érték 10.|
|initialOffsetOptions/Type<sup>1</sup>|fromStart|Annak az esemény-adatfolyamnak a helye, amelyből a feldolgozást el kell kezdeni, ha egy ellenőrzőpont nem létezik a tárolóban. A lehetőségek a következők: `fromStart` `fromEnd` vagy `fromEnqueuedTime` . `fromEnd` a Function app elindítása után várólistán lévő új eseményeket dolgoz fel. Az összes partícióra vonatkozik.  További információkért tekintse meg a [EventProcessorOptions dokumentációját](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider).|
|initialOffsetOptions/enqueuedTimeUtc<sup>1</sup>|N/A| Megadja annak az eseménynek a várólistán lévő időpontját, amelyből a feldolgozást kezdeni kívánja. Ha a `initialOffsetOptions/type` be van állítva `fromEnqueuedTime` , ez a beállítás kötelező. A [DateTime. Parse ()](/dotnet/standard/base-types/parsing-datetime)által támogatott bármilyen formátumú időt támogat, például:  `2020-10-26T20:31Z` . Az egyértelműség érdekében meg kell adnia egy időzónát is. Ha nincs megadva az időzóna, a functions a Function alkalmazást futtató számítógép helyi időzónáját feltételezi, amely az Azure-on való futtatáskor UTC. További információkért tekintse meg a [EventProcessorOptions dokumentációját](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider).|

<sup>1</sup> támogatás a `intitialOffsetOptions` [EventHubs v 4.2.0](https://github.com/Azure/azure-functions-eventhubs-extension/releases/tag/v4.2.0)megkezdéséhez.

> [!NOTE]
> A host.jsAzure Functions 2. x vagy újabb verziójában való hivatkozáshoz lásd: [host.jsAzure functions](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Tulajdonság  |Alapértelmezett | Description |
|---------|---------|---------| 
|maxBatchSize|64|A fogadott események száma fogadási hurokban.|
|prefetchCount|n.a.|Az alapul szolgáló alapértelmezett előzetes lekérési művelet `EventProcessorHost` .| 
|batchCheckpointFrequency|1|Az EventHub kurzor ellenőrzőpontjának létrehozása előtt feldolgozandó eseményvezérelt kötegek száma.| 

> [!NOTE]
> A host.jsAzure Functions 1. x verzióban való hivatkozását lásd: [host.jsAzure functions 1. x-re](../articles/azure-functions/functions-host-json-v1.md).
