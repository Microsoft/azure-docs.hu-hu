---
title: Azure Relay portbeállítások | Microsoft Docs
description: Ez a cikk egy táblázatot tartalmaz, amely leírja a Azure Relay portszámának szükséges konfigurációját.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 81551055d967babaac6f12c3a23ce6b1e78afbd5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314167"
---
# <a name="azure-relay-port-settings"></a>Azure Relay portbeállítások

A következő táblázat ismerteti a Azure Relay portszámának szükséges konfigurációját.

## <a name="hybrid-connections"></a>Hibrid kapcsolatok

A Hibrid kapcsolatok WebSockets-t használ a 443-as porton a TLS-vel, amely az alapul szolgáló átviteli mechanizmus, amely kizárólag **https** protokollt használ. 

## <a name="wcf-relays"></a>WCF-továbbítók
  
|Kötés|Adatátviteli biztonság|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (ügyfél)|Yes|HTTPS| 
|" |No|HTTP|  
|[BasicHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (szolgáltatás)|Bármelyik|9351/HTTP|  
|[NetEventRelayBinding osztály](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (ügyfél)|Yes|9351/HTTPS|  
|" |No|9350/HTTP|  
|[NetEventRelayBinding osztály](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (szolgáltatás)|Bármelyik|9351/HTTP|  
|[NetTcpRelayBinding osztály](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (ügyfél/szolgáltatás)|Bármelyik|5671/9352/HTTP (9352/9353 hibrid használata esetén)|  
|[NetOnewayRelayBinding osztály](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (ügyfél)|Yes|9351/HTTPS|  
|" |No|9350/HTTP|  
|[NetOnewayRelayBinding osztály](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (szolgáltatás)|Bármelyik|9351/HTTP|  
|[WebHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (ügyfél)|Yes|HTTPS|  
|" |No|HTTP|  
|[WebHttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (szolgáltatás)|Bármelyik|9351/HTTP|  
|[WS2007HttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (ügyfél)|Yes|HTTPS|  
|" |No|HTTP|  
|[WS2007HttpRelayBinding osztály](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (szolgáltatás)|Bármelyik|9351/HTTP|

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a Azure Relayről, tekintse meg a következő hivatkozásokat:
* [Mi az Azure Relay?](relay-what-is-it.md)
* [Relay – gyakori kérdések](relay-faq.yml)