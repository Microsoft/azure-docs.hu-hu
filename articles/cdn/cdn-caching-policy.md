---
title: Azure CDN gyorsítótárazási szabályzat kezelése Azure Media Servicesban | Microsoft Docs
description: Ez a cikk a Azure Media Services Azure CDN gyorsítótárazási szabályzatának kezelését ismerteti.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 5807d38e22d8cecf40b5ad4262f9e4662b77ec4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92779135"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Azure CDN gyorsítótárazási házirend kezelése Azure Media Services
A Azure Media Services HTTP-alapú adaptív streaming és progresszív letöltést biztosít. A HTTP-alapú folyamatos átvitel a proxy-és CDN-rétegek gyorsítótárazásának előnyei, valamint az ügyféloldali gyorsítótárazás előnyeit kínálja. A folyamatos átviteli végpontok általános átviteli képességeket biztosítanak, valamint a HTTP-gyorsítótár fejléceit is konfigurálják. A folyamatos átviteli végpontok beállítja a HTTP Cache-Control: Max-Age és a fejlécek elévülését. További információt a HTTP-gyorsítótár fejlécek a [w3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)-ből című témakörben kaphat.

## <a name="default-caching-headers"></a>Alapértelmezett gyorsítótárazási fejlécek
Alapértelmezés szerint a streaming-végpontok 3 napos gyorsítótár-fejlécet alkalmaznak az igény szerinti folyamatos átvitelű adatokhoz (tényleges adathordozó-töredékek/tömbök) és a jegyzékfájlhoz (Playlist). Élő közvetítés esetén a streaming-végpontok 3 napos gyorsítótár-fejlécet alkalmaznak az adatokhoz (tényleges adathordozó-töredékek/adattömbök) és 2 másodperces gyorsítótár-fejlécet a jegyzékfájl (Playlist) kérelmekhez. Ha az élő program igény szerint (élő Archívum) vált, az igény szerinti streaming cache-fejlécek is érvényesek lesznek.

## <a name="azure-cdn-integration"></a>Azure CDN integráció
A Azure Media Services [integrált CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) -t biztosít a streaming-végpontokhoz. A Cache-Control fejlécek ugyanúgy érvényesek, mint a streaming-végpontok és a CDN-kompatibilis streaming-végpontok. A Azure CDN a streaming Endpoint konfigurált gyorsítótár-értékeit használja a belső gyorsítótárban lévő objektumok élettartamának meghatározásához, és ezt az értéket használja a kézbesítési gyorsítótár fejlécének beállításához is. CDN-kompatibilis adatfolyam-végpontok használata esetén nem ajánlott kis gyorsítótár-értékeket beállítani. A kis értékek beállítása csökkenti a teljesítményt, és csökkenti a CDN előnyeit. A CDN-kompatibilis streaming-végpontok esetében nem engedélyezett a 600 másodpercnél kisebb gyorsítótár-fejlécek beállítása.

> [!IMPORTANT]
>A Azure Media Services Azure CDNsal való integrációt végez. Egyetlen kattintással integrálhatja az összes rendelkezésre álló Azure CDN-szolgáltatót a streaming-végpontba, beleértve a standard és a prémium termékeket is. További információkért tekintse meg ezt a [bejelentést](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> A streaming végpontról a CDN-re irányuló adatforgalmi díj le lesz tiltva, ha a CDN engedélyezve van a streaming Endpoint API-kon vagy a Azure Portal streaming Endpoint szakaszának használatával. A manuális integráció vagy egy CDN-végpontnak a CDN API-k vagy a portál szakasz használatával történő közvetlen létrehozása nem tiltja le az adatforgalmi díjakat.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Gyorsítótár-fejlécek konfigurálása Azure Media Services
A gyorsítótár-fejléc értékeinek konfigurálásához Azure Portal vagy Azure Media Services API-kat használhat.

1. Ha Azure Portal használatával szeretné konfigurálni a gyorsítótár-fejléceket, tekintse meg a folyamatos átviteli végpontok [kezelése](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) című szakaszt a streaming Endpoint konfigurálása című részben.
2. Azure Media Services REST API, [streamvégpontok](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [StreamingEndpointCacheControl tulajdonságok](/dotnet/api/microsoft.windowsazure.mediaservices.client.streamingendpointcachecontrol).

## <a name="cache-configuration-precedence-order"></a>Gyorsítótár-konfiguráció sorrendjének sorrendje
1. Azure Media Services konfigurált gyorsítótár-érték felülbírálja az alapértelmezett értéket.
2. Ha nincs manuális konfiguráció, a rendszer az alapértelmezett értékeket alkalmazza.
3. Alapértelmezés szerint a 2 másodperces gyorsítótár-fejlécek az élő streaming jegyzékfájlra (Playlist) vonatkoznak, függetlenül az Azure adathordozótól vagy az Azure Storage-konfigurációtól, és ennek az értéknek a felülbírálása nem érhető el.