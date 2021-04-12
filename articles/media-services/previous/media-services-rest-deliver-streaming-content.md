---
title: Azure Media Services tartalom közzététele REST használatával
description: Megtudhatja, hogyan hozhat létre egy streaming URL-cím létrehozásához használt lokátort. A kód REST API használ.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: ff332c30-30c6-4ed1-99d0-5fffd25d4f23
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: b8733d499b2396160a73906f16a69291cf0b9d71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015420"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Azure Media Services tartalom közzététele REST használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portál](media-services-portal-publish.md)
> 
> 

Az adaptív sávszélességű MP4-készleteket egy OnDemand streaming-lokátor létrehozásával és egy streaming URL-cím megadásával is továbbíthatja. Az [eszköz kódolása](media-services-rest-encode-asset.md) című cikk bemutatja, hogyan kódolhat egy adaptív sávszélességű MP4-készletbe. Ha a tartalom titkosítva van, a lokátor létrehozása előtt konfigurálja az eszköz kézbesítési szabályzatát (a [jelen](media-services-rest-configure-asset-delivery-policy.md) cikkben leírtak szerint). 

A OnDemand streaming-lokátor használatával olyan URL-címeket is létrehozhat, amelyek a fokozatosan letöltött MP4-fájlokra mutatnak.  

Ez a cikk bemutatja, hogyan hozhat létre egy OnDemand streaming-keresőt az adategység közzétételéhez, valamint egy sima, MPEG DASH és HLS streaming URL-címek létrehozásához. Azt is bemutatja, hogyan készíthet progresszív letöltési URL-címeket.

A [következő](#types) szakasz azokat a felsorolási típusokat mutatja be, amelyek értékeit a REST-hívásokban használják.   

> [!NOTE]
> A Media Servicesban lévő entitásokhoz való hozzáféréskor meg kell adnia a HTTP-kérelmekben megadott fejléc-mezőket és-értékeket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md).
> 

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

További információ az AMS API-hoz való kapcsolódásról: [a Azure Media Services API Azure ad-hitelesítéssel való elérése](media-services-use-aad-auth-to-access-ams-api.md). 

>[!NOTE]
>A sikeres csatlakozás után https://media.windows.net egy 301-es átirányítást fog kapni, amely egy másik Media Services URI azonosítót ad meg. Ezt követően meg kell adnia a további hívásokat az új URI-hoz.

## <a name="create-an-ondemand-streaming-locator"></a>OnDemand-kereső létrehozása
A OnDemand streaming-lokátor létrehozásához és az URL-címek lekéréséhez a következőket kell tennie:

1. Ha a tartalom titkosítva van, adjon meg egy hozzáférési szabályzatot.
2. Hozzon létre egy OnDemand streaming lokátort.
3. Ha adatfolyamot szeretne készíteni, szerezze be a streaming manifest-fájlt (. ISM) az eszközben. 
   
   Ha azt tervezi, hogy fokozatosan letölti a fájlt, olvassa be az MP4-fájlok nevét az objektumban. 
4. URL-címeket hozhat létre a jegyzékfájlhoz vagy MP4-fájlokhoz. 
5. Írási vagy törlési engedélyeket tartalmazó AccessPolicy nem hozhat létre streaming-lokátort.

### <a name="create-an-access-policy"></a>Hozzáférési szabályzat létrehozása

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Használja ugyanazt a házirend-azonosítót, ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja, például olyan lokátorokra vonatkozó házirendeket, amelyek hosszú ideig maradnak érvényben (nem feltöltési szabályzatok). További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

Kérés:

```console
POST https://media.windows.net/api/AccessPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
Host: media.windows.net
Content-Length: 68

{"Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
```

Válasz:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 311
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https:/media.windows.net/api/AccessPolicies('nb%3Apid%3AUUID%3A69c80d98-7830-407f-a9af-e25f4b0d3e5f')
Server: Microsoft-IIS/8.5
request-id: a877528a-bdb4-4414-9862-273f8e64f882
x-ms-request-id: a877528a-bdb4-4414-9862-273f8e64f882
x-ms-client-request-id: 6bcfd511-a561-448d-a022-a319a89ecffa
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 18 Feb 2015 06:52:09 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#AccessPolicies/@Element","Id":"nb:pid:UUID:69c80d98-7830-407f-a9af-e25f4b0d3e5f","Created":"2015-02-18T06:52:09.8862191Z","LastModified":"2015-02-18T06:52:09.8862191Z","Name":"access policy","DurationInMinutes":43200.0,"Permissions":1}
```

### <a name="create-an-ondemand-streaming-locator"></a>OnDemand-kereső létrehozása
Hozza létre a lokátort a megadott eszköz-és eszköz-házirendhez.

Kérés:

```console
POST https://media.windows.net/api/Locators HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
Host: media.windows.net
Content-Length: 181

{"AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872Z","Type":2}
```
Válasz:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 637
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/Locators('nb%3Alid%3AUUID%3Abe245661-2bbd-4fc6-b14f-9cf9a1492e5e')
Server: Microsoft-IIS/8.5
request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
x-ms-request-id: 5bd5864a-0afd-44c0-a67a-4044a2c9043b
x-ms-client-request-id: ac159492-9a0c-40c3-aacc-551b1b4c5f62
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 18 Feb 2015 06:58:37 GMT

{"odata.metadata":"https://media.windows.net/api/$metadata#Locators/@Element","Id":"nb:lid:UUID:be245661-2bbd-4fc6-b14f-9cf9a1492e5e","ExpirationDateTime":"2015-03-20T06:34:47.267872+00:00","Type":2,"Path":"https://amstest1.streaming.mediaservices.windows.net/be245661-2bbd-4fc6-b14f-9cf9a1492e5e/","BaseUri":"https://amstest1.streaming.mediaservices.windows.net","ContentAccessComponent":"be245661-2bbd-4fc6-b14f-9cf9a1492e5e","AccessPolicyId":"nb:pid:UUID:1480030d-c481-430a-9687-535c6a5cb272","AssetId":"nb:cid:UUID:cc1e445d-1500-80bd-538e-f1e4b71b465e","StartTime":"2015-02-18T06:34:47.267872+00:00","Name":null}
```

### <a name="build-streaming-urls"></a>Streaming URL-címek összeállítása
Használja a lokátor létrehozása után visszaadott **path** értéket a sima, HLS és MPEG Dash URL-címek létrehozásához. 

Smooth Streaming: **elérési út** + manifest-fájl neve + "/manifest"

Például:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`

HLS: **elérési út** + manifest-fájl neve + "/manifest (Format = m3u8-AAPL)"

Például:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`


DASH: **path** + manifest-fájl neve + "/manifest (Format = mpd-Time-CSF)"

Például:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


### <a name="build-progressive-download-urls"></a>Progresszív letöltési URL-címek készítése
Használja a lokátor létrehozása után visszaadott **path** értéket a progresszív letöltési URL-cím létrehozásához.   

URL: **elérési út** + fájl MP4-neve

Például:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

## <a name="enum-types"></a><a id="types"></a>Enumerálási típusok

```console
[Flags]
public enum AccessPermissions
{
    None = 0,
    Read = 1,
    Write = 2,
    Delete = 4,
    List = 8,
}

public enum LocatorType
{
    None = 0,
    Sas = 1,
    OnDemandOrigin = 2,
}
```

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még
[Media Services Operations REST API áttekintése](media-services-rest-how-to-use.md)

[Objektumtovábbítási szabályzat konfigurálása](media-services-rest-configure-asset-delivery-policy.md)

