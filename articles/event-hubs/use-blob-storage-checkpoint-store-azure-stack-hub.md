---
title: Blob Storage használata ellenőrzőpont-tárolóként Azure Stack hub-on (előzetes verzió)
description: Ez a cikk azt ismerteti, hogyan használható a Blob Storage ellenőrzőpont-tárolóként a Event Hubs Azure Stack hub (előzetes verzió) szolgáltatásban.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1c876ed36be6aec9395a1acba3a1deb25a47de2c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87039225"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub-preview"></a>Blob Storage használata ellenőrzőpont-tárolóként – Event Hubs az Azure Stack hub-on (előzetes verzió)
Ha az Azure Blob Storaget használja ellenőrzőpont-tárolóként olyan környezetben, amely támogatja a Storage blob SDK eltérő verzióját, mint amilyeneket az Azure-ban általában elérhető, akkor programkódot kell használnia a Storage szolgáltatás API-verziójának módosítására az adott környezet által támogatott adott verzióra. Ha például [egy 2002-es Azure stack hub-os verzióban](/azure-stack/user/event-hubs-overview)futtatja az Event Hubs-t, a Storage szolgáltatás legmagasabb rendelkezésre álló verziója a 2017-11-09-es verzió. Ebben az esetben programkódot kell használnia a Storage szolgáltatás API-verziójának 2017-11-09-re való célzásához. Az adott tárolási API-verzió célzására vonatkozó példát a GitHubon található példákban talál: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) vagy [írógéppel](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python – [szinkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [aszinkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

> [!IMPORTANT]
> A Azure Stack hub Event Hubs jelenleg [előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verzióban érhető el, és ingyenes. 

Ha olyan Event Hubs-fogadót futtat, amely az Azure Stack hub által támogatott verzió megcélzása nélkül Blob Storageot használja ellenőrzőpont-tárolóként, a következő hibaüzenet jelenik meg:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Példa hibaüzenet a Pythonban
Python esetén a rendszer hibát ad `azure.core.exceptions.HttpResponseError` át a következőhöz: `on_error(partition_context, error)` `EventHubConsumerClient.receive()` . A metódus azonban `receive()` nem emel kivételt. `print(error)`a következő kivételi adatokat fogja kinyomtatni:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

A naplózó két figyelmeztetést fog naplózni, például a következő esetekben:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>További lépések

Tekintse meg az alábbi cikket a particionálással és az ellenőrzőponttal kapcsolatban: [a partíciók terhelésének elosztása az alkalmazás több példánya között](event-processor-balance-partition-load.md)
