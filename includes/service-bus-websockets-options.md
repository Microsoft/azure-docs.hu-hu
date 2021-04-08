---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-messaging
author: clemensv
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/24/2020
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: ca483d0b71bde945a7e46da785dd6a76b3a8f177
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98693399"
---
A AMQP-over-WebSockets protokoll beállítása a 443-as TCP-porton keresztül fut a HTTP-/REST API-hez hasonlóan, de egyébként az egyszerű AMQP eltérően működik. Ez a beállítás valamivel nagyobb kezdeti kapcsolati késést biztosít a további kézfogások miatt, és valamivel nagyobb terhelést jelent, mint a HTTPS-port megosztásának kompromisszuma. Ha ez a mód be van jelölve, a 443-es TCP-port elegendő a kommunikációhoz. A következő lehetőségek lehetővé teszik az egyszerű AMQP vagy a AMQP WebSockets mód kiválasztását:

| Nyelv | Beállítás   |
| -------- | ----- |
| .NET     | [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) tulajdonság a [TransportType. Amqp](/dotnet/api/microsoft.azure.servicebus.transporttype) vagy a [TransportType. AmqpWebSockets](/dotnet/api/microsoft.azure.servicebus.transporttype) |
| Java     | [com. microsoft. Azure. servicebus. ClientSettings](/java/api/com.microsoft.azure.servicebus.clientsettings.clientsettings) a [com. microsoft. Azure. servicebus. primitívs. TransportType. AMQP](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) vagy [com.microsoft.Azure.servicebus.primitives.TransportType.AMQP_WEB_SOCKETS](/java/api/com.microsoft.azure.servicebus.primitives.transporttype) |
| Csomópont  | A [ServiceBusClientOptions](/javascript/api/@azure/service-bus/servicebusclientoptions) `webSocket` konstruktor argumentummal rendelkezik. |
| Python | [ServiceBusClient.transport_type](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.ServiceBusClient) [TransportType. Amqp](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) vagy [TransportType. AmqpOverWebSocket](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-servicebus/latest/azure.servicebus.html#azure.servicebus.TransportType) |