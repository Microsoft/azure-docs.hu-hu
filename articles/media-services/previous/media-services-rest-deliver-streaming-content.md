---
title: Tartalom Azure Media Services REST használatával
description: Útmutató streamelési URL-cím létrehozásához használt lokátor létrehozásához. A kód az REST API.
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
ms.openlocfilehash: 650c0847942635e2a6a901db40ed0e51e9412057
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600046"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Tartalom Azure Media Services REST használatával

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [Portál](media-services-portal-publish.md)
> 
> 

Adaptív átviteli átvitelt érő MP4-készletet streamelhet egy onDemand streamelési lokátor létrehozásával és egy streamelési URL-cím létrehozásával. Az [adateszköz kódolását](media-services-rest-encode-asset.md) bemutató cikk bemutatja, hogyan kódolható adaptív bitrate MP4-készletbe. Ha a tartalom titkosítva van, a lokátor létrehozása előtt konfigurálja az eszköztovábbító szabályzatot (a cikkben leírtak szerint). [](media-services-rest-configure-asset-delivery-policy.md) 

Az OnDemand streamelési lokátor használatával olyan URL-címeket is felépíthet, amelyek fokozatosan letölthető MP4-fájlokra mutatnak.  

Ez a cikk bemutatja, hogyan hozhat létre onDemand streamelési lokátort az adateszköz közzétételéhez, valamint Smooth, MPEG DASH és HLS streamelési URL-címek létrehozásához. Azt is bemutatja, hogyan lehet progresszív letöltési URL-címeket összeépíteni.

A [következő szakasz](#types) azokat az enumtípusokat mutatja be, amelyeknek az értékeit a REST-hívások használják.   

> [!NOTE]
> Amikor entitásokat fér hozzá a Media Services, meghatározott fejlécmezőket és értékeket kell beállítania a HTTP-kérelmekben. További információ: [Media Services REST API beállítása.](media-services-rest-how-to-use.md)
> 

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

További információ az AMS API-hoz való csatlakozásról: [Access the Azure Media Services API with Azure AD authentication (Az Azure Media Services API elérése Azure AD-hitelesítéssel).](media-services-use-aad-auth-to-access-ams-api.md) 

>[!NOTE]
>Miután sikeresen csatlakozott a-hoz, egy 301-es átirányítást fog kapni, amely egy másik Media Services `https://media.windows.net` URI-t. Az új URI-hoz további hívásokat kell kezdeményeznie.

## <a name="create-an-ondemand-streaming-locator"></a>OnDemand streamelési lokátor létrehozása
Az OnDemand streamelési lokátor létrehozásához és az URL-címek lekért létrehozásához a következőket kell megtennie:

1. Ha a tartalom titkosítva van, határozzon meg egy hozzáférési szabályzatot.
2. Hozzon létre egy OnDemand streamelési lokátort.
3. Ha streamelést tervez, szerezze be a streamelési jegyzékfájlt (.ism) az adatban. 
   
   Ha fokozatosan tervezi letölteni a fájlokat, szerezze be az MP4-fájlok nevét az adateszközben. 
4. Buildelési URL-címek a jegyzékfájlhoz vagy MP4-fájlokhoz. 
5. Írási vagy törlési engedélyeket tartalmazó AccessPolicy használatával nem hozhat létre streamelési lokátort.

### <a name="create-an-access-policy"></a>Hozzáférési szabályzat létrehozása

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Akkor használja ugyanazt a szabályzatazonosítót, ha mindig ugyanazokkal a napokkal/hozzáférési engedélyekkel rendelkezik, például olyan keresőkre vonatkozó szabályzatokat, amelyek hosszú ideig érvényben maradnak (nem feltöltési szabályzatok). További információt ebben a [cikkben](media-services-dotnet-manage-entities.md#limit-access-policies) talál.

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

### <a name="create-an-ondemand-streaming-locator"></a>OnDemand streamelési lokátor létrehozása
Hozza létre a megadott eszköz- és eszköz-szabályzat lokátorát.

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

### <a name="build-streaming-urls"></a>Streamelési URL-címek összeállítása
Használja a **lokátor** létrehozása után visszaadott Elérési út értéket a Smooth, HLS és MPEG DASH URL-címek létrehozásához. 

Smooth Streaming: **Elérési út** + jegyzékfájl neve + "/manifest"

Például:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest`

HLS: **Elérési út** + jegyzékfájl neve + "/manifest(format=m3u8-aapl)"

Például:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)`


DASH: **Elérési út** + jegyzékfájl neve + "/manifest(format=mpd-time-csf)"

Például:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)`


### <a name="build-progressive-download-urls"></a>Progresszív letöltési URL-címek összeállítása
Használja a **lokátor** létrehozása után visszaadott Elérési út értéket a progresszív letöltési URL-cím létrehozásához.   

URL: **Elérési út** + eszközfájl mp4 neve

Például:

`https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

## <a name="enum-types"></a><a id="types"></a>Felsorolástípusok

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
[Media Services műveletek áttekintése REST API áttekintés](media-services-rest-how-to-use.md)

[Objektumtovábbítási szabályzat konfigurálása](media-services-rest-configure-asset-delivery-policy.md)

