---
title: Az Azure Media Services-tartalom közzététele rest használatával
description: Ismerje meg, hogyan hozhat létre egy lokátort, amely egy streamelési URL-cím létrehozásához használható. A kód REST API-t használ.
author: Juliako
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
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 787336f00a83d9403e3069754787743b9be6c5b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77050006"
---
# <a name="publish-azure-media-services-content-using-rest"></a>Az Azure Media Services-tartalom közzététele rest használatával 
> [!div class="op_single_selector"]
> * [.NET](media-services-deliver-streaming-content.md)
> * [Többi](media-services-rest-deliver-streaming-content.md)
> * [Portál](media-services-portal-publish.md)
> 
> 

Adaptív sávszélességű MP4-készletet streamelhet egy OnDemand streamelési lokátor létrehozásával és egy streamelési URL-cím létrehozásával. Az [eszközcikk kódolása](media-services-rest-encode-asset.md) bemutatja, hogyan kódolhatot egy adaptív sávszélességű MP4-készletbe. Ha a tartalom titkosítva van, konfigurálja az eszközkézbesítési házirendet [(a](media-services-rest-configure-asset-delivery-policy.md) jelen cikkben leírtak szerint) a lokátor létrehozása előtt. 

Az OnDemand streamelési lokátorsegítségével olyan URL-címeket is létrehozhat, amelyek fokozatosan letölthető MP4-fájlokra mutatnak.  

Ez a cikk bemutatja, hogyan hozhat létre egy OnDemand streamelési lokátort az eszköz közzététele és a sima, MPEG DASH és HLS streamelési URL-címek létrehozása érdekében. Azt is bemutatja, hogyan kell építeni progresszív letöltési URL-eket.

A [következő](#types) szakasz azokat a felsoradástípusokat mutatja be, amelyek értékeit a REST-hívások ban használják.   

> [!NOTE]
> Amikor a Media Services entitásait éri el, meghatározott fejlécmezőket és értékeket kell beállítania a HTTP-kérelmekben. További információt a [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md)című témakörben talál.
> 

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

Az AMS API-hoz való csatlakozásról az [Access the Azure Media Services API azure AD-hitelesítéssel című témakörben](media-services-use-aad-auth-to-access-ams-api.md)talál további információt. 

>[!NOTE]
>A sikeres csatlakozás https://media.windows.netután egy 301-es átirányítást kap, amely egy másik Media Services URI-t ad meg. További hívásokat kell kezdeményeznie az új URI-ba.

## <a name="create-an-ondemand-streaming-locator"></a>OnDemand streamelési lokátor létrehozása
Az OnDemand streamelési lokátor létrehozásához és az URL-címek lekéréséhez a következőket kell tennie:

1. Ha a tartalom titkosított, adjon meg egy hozzáférési szabályzatot.
2. Hozzon létre egy OnDemand streamelési lokátort.
3. Ha azt tervezi, hogy streamelés, a streamelési jegyzékfájl (.ism) az eszköz. 
   
   Ha azt tervezi, hogy fokozatosan letölti, kap a nevét MP4 fájlokat az eszköz. 
4. URL-címeket hozhat létre a jegyzékfájlba vagy mp4-fájlokba. 
5. Írási vagy törlési engedélyeket tartalmazó AccessPolicy használatával nem hozható létre adatfolyam-lokátor.

### <a name="create-an-access-policy"></a>Hozzáférési házirend létrehozása

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Használja ugyanazt a házirend-azonosítót, ha mindig ugyanazokat a napokat / hozzáférési engedélyeket használja, például a helymeghatározókra vonatkozó házirendeket, amelyek hosszú ideig a helyükön maradnak (nem feltöltési házirendek). További információt [ebben a cikkben](media-services-dotnet-manage-entities.md#limit-access-policies) talál.

Kérés:

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

Válasz:

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

### <a name="create-an-ondemand-streaming-locator"></a>OnDemand streamelési lokátor létrehozása
Hozza létre a lokátort a megadott eszköz- és eszközházirendhöz.

Kérés:

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

Válasz:

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

### <a name="build-streaming-urls"></a>Streamelési URL-ek létrehozása
A lokátor létrehozása után visszaadott **elérési út** értékkel hozza létre a Simított, HLS és MPEG DASH URL-címeket. 

Sima streaming: **Elérési út** + jegyzékfájl neve + "/manifest"

példa:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest

HLS: **Path** + manifest fájlnév + "/manifest(format=m3u8-aapl)"

példa:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)


DASH: **Elérési út** + jegyzékfájl neve + "/manifest(format=mpd-time-csf)"

példa:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


### <a name="build-progressive-download-urls"></a>Progresszív letöltési URL-címek létrehozása
Használja a lokátor létrehozása után visszaadott **elérési út** értékét a progresszív letöltési URL létrehozásához.   

URL: **Path** + eszközfájl mp4-neve

példa:

    https://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

## <a name="enum-types"></a><a id="types"></a>Felsoratípusok
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

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még
[Media Services-műveletek REST API – áttekintés](media-services-rest-how-to-use.md)

[Objektumtovábbítási szabályzat konfigurálása](media-services-rest-configure-asset-delivery-policy.md)

