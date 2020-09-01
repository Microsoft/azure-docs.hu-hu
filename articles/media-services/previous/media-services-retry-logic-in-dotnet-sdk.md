---
title: Újrapróbálkozási logika a .NET-hez készült Media Services SDK-ban | Microsoft Docs
description: A témakör áttekintést nyújt az újrapróbálkozási logikáról a .NET-hez készült Media Services SDK-ban.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 144db6a5ceaf56a35d3ce11dd54e1dfb4c97d7e3
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89264113"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Újrapróbálkozási logika a .NET-hez készült Media Services SDK-ban

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Microsoft Azure szolgáltatások használatakor átmeneti hibák léphetnek fel. Ha átmeneti hiba történik, a legtöbb esetben a művelet sikeres újrapróbálkozása után. A .NET-hez készült Media Services SDK implementálja az újrapróbálkozási logikát a webes kérelmek által okozott kivételekhez és hibákhoz kapcsolódó átmeneti hibák kezeléséhez, lekérdezések végrehajtásához, a módosítások mentéséhez és a tárolási műveletekhez.  Alapértelmezés szerint a .NET-hez készült Media Services SDK négy újrapróbálkozást hajt végre, mielőtt újra eldobja a kivételt az alkalmazásba. Ezután az alkalmazás kódjának megfelelően kell kezelnie ezt a kivételt.  

 A következő rövid útmutató a webes kérelem, a tárolási, a lekérdezési és a SaveChanges szabályzatokhoz:  

* A tárolási házirend a blob Storage-műveletekhez (az adatfájlok feltöltéséhez vagy letöltéséhez) használatos.  
* A webkérési házirend általános webes kérelmekhez használatos (például hitelesítési jogkivonat beszerzése és a felhasználók fürt végpontjának feloldása).  
* A lekérdezési házirend az entitások REST-ből való lekérdezésére szolgál (például mediaContext. assets. where (...)).  
* A SaveChanges szabályzat a szolgáltatáson belüli adatmódosításra szolgál (például egy entitást frissítő entitás létrehozásával, egy művelet szolgáltatásbeli függvény meghívásával).  
  
  Ez a témakör felsorolja a .NET-hez készült Media Services SDK által az újrapróbálkozási logikához kezelt kivétel-és hibakódokat.  

## <a name="exception-types"></a>Kivételek típusai
A következő táblázat azokat a kivételeket ismerteti, amelyek miatt a .NET-hez készült Media Services SDK kezeli vagy nem kezeli az átmeneti hibákat okozó műveletek némelyikét.  

| Kivétel | Webes kérelem | Tárolás | Lekérdezés | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>További információ: a [Webkivételi állapotkódok](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) szakasz. |Igen |Igen |Igen |Igen |
| DataServiceClientException<br/> További információ: http- [hibák állapotának kódjai](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceQueryException<br/> További információ: http- [hibák állapotának kódjai](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceRequestException<br/> További információ: http- [hibák állapotának kódjai](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Nem |Igen |Igen |Igen |
| DataServiceTransportException |Nem |Nem |Igen |Igen |
| Timeoutexception osztályról |Igen |Igen |Igen |Nem |
| SocketException |Igen |Igen |Igen |Igen |
| StorageException |Nem |Igen |Nem |Nem |
| IOException |Nem |Igen |Nem |Nem |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a> Webkivételi állapotkódok
A következő táblázat azt mutatja be, hogy az újrapróbálkozási logikát milyen webkivételi hibakódok implementálják. A [WebExceptionStatus](/dotnet/api/system.net.webexceptionstatus?view=netcore-3.1) enumerálás meghatározza az állapotkódot.  

| status | Webes kérelem | Tárolás | Lekérdezés | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Igen |Igen |Igen |Igen |
| NameResolutionFailure |Igen |Igen |Igen |Igen |
| ProxyNameResolutionFailure |Igen |Igen |Igen |Igen |
| SendFailure |Igen |Igen |Igen |Igen |
| PipelineFailure |Igen |Igen |Igen |Nem |
| ConnectionClosed |Igen |Igen |Igen |Nem |
| KeepAliveFailure |Igen |Igen |Igen |Nem |
| UnknownError |Igen |Igen |Igen |Nem |
| ReceiveFailure |Igen |Igen |Igen |Nem |
| RequestCanceled |Igen |Igen |Igen |Nem |
| Időtúllépés |Igen |Igen |Igen |Nem |
| ProtocolError <br/>Az újrapróbálkozást a ProtocolError a HTTP-állapotkód kezelésére szolgáló kód vezérli. További információ: http- [hibák állapotának kódjai](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Igen |Igen |Igen |Igen |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a> HTTP-hibák állapotának kódjai
Ha a lekérdezési és SaveChanges műveletek eldobják a DataServiceClientException, a DataServiceQueryException vagy a DataServiceQueryException, a rendszer a HTTP-hiba állapotkódot adja vissza a StatusCode tulajdonságban.  Az alábbi táblázat bemutatja, hogy az újrapróbálkozási logikát milyen hibakódok implementálják.  

| status | Webes kérelem | Tárolás | Lekérdezés | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Nem |Igen |Nem |Nem |
| 403 |Nem |Igen<br/>Újrapróbálkozások kezelésére hosszú várakozásokkal. |Nem |Nem |
| 408 |Igen |Igen |Igen |Igen |
| 429 |Igen |Igen |Igen |Igen |
| 500 |Igen |Igen |Igen |Nem |
| 502 |Igen |Igen |Igen |Nem |
| 503 |Igen |Igen |Igen |Igen |
| 504 |Igen |Igen |Igen |Nem |

Ha szeretné megtekinteni az Media Services SDK for .NET újrapróbálkozási logikájának tényleges megvalósítását, tekintse meg az [Azure-SDK-for-Media-Services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling)című témakört.

## <a name="next-steps"></a>További lépések
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
