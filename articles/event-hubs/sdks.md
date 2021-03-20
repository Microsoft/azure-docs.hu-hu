---
title: Azure Event Hubs – ügyféloldali SDK-k | Microsoft Docs
description: Ez a cikk az Azure Event Hubs-hez készült ügyféloldali SDK-k adatait ismerteti.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 9f4eec603245f1e4ea6fa4d97b843ca6a770f2ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88930876"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure Event Hubs – ügyfél SDK-k
Ez a cikk az Azure Event Hubs által támogatott SDK-k alábbi információit tartalmazza: 

- Az alkalmazásokban használható csomag helye 
- GitHub-hely, ahol a forráskód, a minták, a readme, a változási napló, a jelentett problémák és az új problémák is megtalálhatók 
- Hivatkozások a gyors útmutatóhoz 

## <a name="client-sdks"></a>Ügyfél-SDK-k
Az alábbi táblázat az összes jelenleg elérhető Azure Event Hubs Runtime-ügyfelet ismerteti. Habár a kódtárak némelyike korlátozott felügyeleti funkciókat is tartalmaz, a felügyeleti műveletekhez külön könyvtárak is tartoznak. Ezen könyvtárak fő témája az, hogy **üzeneteket küldjön és fogadjon** az Event hub-ból.

| Nyelv | Csomag | Referencia | 
| -------- | ------- | --------------- | 
| . NET Standard (a **legújabb** és a .net Core és a .NET Framework is támogatja) | [Azure. Messaging. EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[GitHub helye](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[Oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
|       | [Azure. Messaging. EventHubs. Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[GitHub helye](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[Oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Standard (**örökölt** és támogatja a .net Core és a .NET-keretrendszert is) | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[GitHub helye](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[Oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[GitHub helye](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[Oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework (**régi**) | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[Oktatóanyag](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [Azure-üzenetkezelés – eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[GitHub helye](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[Oktatóanyag](event-hubs-java-get-started-send.md)</li></ul> |
|      | [Azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **(örökölt)** | <ul><li>[GitHub helye](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[Oktatóanyag](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [Azure – eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[GitHub helye](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[Oktatóanyag](event-hubs-python-get-started-send.md)</li></ul> |
|        | [Azure-eventhub-checkpointstoreblob-AIO](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[GitHub helye](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[Oktatóanyag](event-hubs-python-get-started-send.md)</li></ul> |
| JavaScript | [Azure/Event – hubok](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[GitHub helye](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[Oktatóanyag](event-hubs-node-get-started-send.md)</li></ul> |
|            | [Azure/eventhubs – checkpointstore – blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[GitHub helye](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[Oktatóanyag](event-hubs-node-get-started-send.md)</li></ul> |
| Go | [Azure-Event-hubok – ugrás](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[GitHub helye](https://github.com/Azure/azure-event-hubs-go)</li><li>[Oktatóanyag](event-hubs-go-get-started-send.md)</li></ul> |
| C | [Azure-Event-hubok-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[GitHub helye](https://github.com/Azure/azure-event-hubs-c)</li><li>[Oktatóanyag](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>Felügyeleti SDK-k
A következő táblázat felsorolja az aktuálisan elérhető felügyeleti könyvtárakat. Ezen függvénytárak egyike sem tartalmaz futtatókörnyezeti műveleteket, és kizárólag **Event Hubs entitások kezelésének** céljára szolgál.

| Nyelv | Csomag | Referencia | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[GitHub helye](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[Oktatóanyag](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |


## <a name="next-steps"></a>Következő lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs áttekintése](./event-hubs-about.md)
* [Event hub létrehozása](event-hubs-create.md)
* [Event Hubs – gyakori kérdések](event-hubs-faq.md)
