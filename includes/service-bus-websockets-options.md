---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 04/08/2021
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 08fccf366321b075542f36b86c9bf22d5d877167
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304417"
---
A AMQP-over-WebSockets protokoll beállítása a 443-as TCP-porton keresztül fut a HTTP-/REST API-hez hasonlóan, de egyébként az egyszerű AMQP eltérően működik. Ez a beállítás nagyobb kezdeti kapcsolati késést biztosít az extra kézfogások miatt, és valamivel nagyobb terhelést jelent, mint a HTTPS-port megosztásának kompromisszuma. Ha ez a mód be van jelölve, a 443-es TCP-port elegendő a kommunikációhoz. A következő lehetőségek lehetővé teszik a AMQP WebSockets mód kiválasztását. 

| Nyelv | Beállítás   |
| -------- | ----- |
| .NET (Azure.Messaging.ServiceBus)    | Hozzon létre [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient.-ctor) egy olyan konstruktor használatával, amely paraméterként [ServiceBusClientOptions](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions) fogad. A [ServiceBusClientOptions. TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclientoptions.transporttype) beállítása a [ServiceBusTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.servicebus.servicebustransporttype) |
| .NET (Microsoft.Azure.ServiceBus)    | Ügyfélalkalmazások létrehozásakor használja a [TransportType](/dotnet/api/microsoft.azure.servicebus.transporttype), [ServiceBusConnection](/dotnet/api/microsoft.azure.servicebus.servicebusconnection)vagy [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder) paramétereket használó konstruktorokat. <p>A paraméterként igénybe vevő szerkezetnél `transportType` állítsa a paramétert a [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype)értékre.</p> <p>A paraméterként igénybe vevő konstruktor esetében `ServiceBusConnection` állítsa a [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype)értékre.</p> <p>Ha használja `ServiceBusConnectionStringBuilder` , használja a konstruktorokat, amelyekkel megadhatja a következőt: `transportType` .</p> |
| Java (com. Azure. Messaging. servicebus)     | Ügyfelek létrehozásakor a [ServiceBusClientBuilder. transportType](/java/api/com.azure.messaging.servicebus.servicebusclientbuilder.transporttype) beállítása a következőre: [AmqpTransportType.AMQP.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Java (com. microsoft. Azure. servicebus)    | Ügyfelek létrehozásakor állítsa be `transportType` a következőt: [com. microsoft. Azure. Servicebus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings#com_microsoft_azure_servicebus_ClientSettings_ClientSettings_com_microsoft_azure_servicebus_security_TokenProvider_com_microsoft_azure_servicebus_primitives_RetryPolicy_java_time_Duration_com_microsoft_azure_servicebus_primitives_TransportType_)  , hogy [com.microsoft.Azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| JavaScript  | Service Bus-objektumok létrehozásakor használja a `webSocketOptions` tulajdonságot a [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions). |
| Python | Service Bus-ügyfelek létrehozásakor állítsa be a [ServiceBusClient.transport_typet](/python/api/azure-servicebus/azure.servicebus.servicebusclient) a [TransportType. AmqpOverWebSocket](/python/api/azure-servicebus/azure.servicebus.transporttype) értékre. |

