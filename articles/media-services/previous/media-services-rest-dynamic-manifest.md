---
title: Szűrők létrehozása Azure Media Services REST API használatával | Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, hogy az ügyfél egy stream adott szakaszait továbbítsa. Media Services REST API dinamikus jegyzékfájlokat hoz létre a szelektív streaming eléréséhez.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: f7d23daf-7cd2-49c7-a195-ab902912ab3c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewr: cenkdin
ms.openlocfilehash: b9286260d0fead11c7f55eff213c8ad0241a8cba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008790"
---
# <a name="creating-filters-with-azure-media-services-rest-api"></a>Szűrők létrehozása Azure Media Services REST API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

A 2,17-es verziótól kezdődően a Media Services lehetővé teszi az eszközökhöz tartozó szűrők megadását. Ezek a szűrők olyan kiszolgálóoldali szabályok, amelyek lehetővé teszik az ügyfelek számára a következő műveleteket: csak a videó egy szakaszának lejátszása (a teljes videó lejátszása helyett), vagy csak az eszköz által kezelhető hang-és videó-átadások egy részhalmazát kell megadnia (az objektumhoz társított összes kiadatás helyett). Az eszközök szűrését archiváljuk a **dinamikus jegyzékfájlok** használatával, amelyek a megadott szűrő (k) alapján a videó streamre való továbbítására szolgálnak.

A szűrőkkel és a dinamikus Jegyzéktel kapcsolatos részletesebb információkért lásd: a [dinamikus jegyzékfájlok áttekintése](media-services-dynamic-manifest-overview.md).

Ez a cikk bemutatja, hogyan használhatók a REST API-k szűrők létrehozásához, frissítéséhez és törléséhez. 

## <a name="types-used-to-create-filters"></a>Szűrők létrehozásához használt típusok
Szűrők létrehozásakor a következő típusok használatosak:  

* [Szűrő](/rest/api/media/operations/filter)
* [AssetFilter](/rest/api/media/operations/assetfilter)
* [PresentationTimeRange](/rest/api/media/operations/presentationtimerange)
* [FilterTrackSelect és FilterTrackPropertyCondition](/rest/api/media/operations/filtertrackselect)

> [!NOTE]
> 
> A Media Servicesban lévő entitásokhoz való hozzáféréskor meg kell adnia a HTTP-kérelmekben megadott fejléc-mezőket és-értékeket. További információ: [Media Services REST API-fejlesztés beállítása](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Kapcsolódás a Media Services szolgáltatáshoz

További információ az AMS API-hoz való kapcsolódásról: [a Azure Media Services API Azure ad-hitelesítéssel való elérése](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-filters"></a>Szűrők létrehozása
### <a name="create-global-filters"></a>Globális szűrők létrehozása
Globális szűrő létrehozásához használja a következő HTTP-kérelmeket:  

#### <a name="http-request"></a>HTTP-kérés
Kérések fejlécei

```console
POST https://media.windows.net/API/Filters HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host:media.windows.net 
```

A kérés törzse 

```console
{  
   "Name":"GlobalFilter",
   "PresentationTimeRange":{  
      "StartTimestamp":"0",
      "EndTimestamp":"9223372036854775807",
      "PresentationWindowDuration":"12000000000",
      "LiveBackoffDuration":"0",
      "Timescale":"10000000"
   },
   "Tracks":[  
      {  
         "PropertyConditions":
              [  
            {  
               "Property":"Type",
               "Value":"audio",
               "Operator":"Equal"
            },
            {  
               "Property":"Bitrate",
               "Value":"0-2147483647",
               "Operator":"Equal"
            }
         ]
      }
   ]
}
```



#### <a name="http-response"></a>HTTP-válasz

```console
HTTP/1.1 201 Created 
```

### <a name="create-local-assetfilters"></a>Helyi AssetFilters létrehozása
Helyi AssetFilter létrehozásához használja a következő HTTP-kérelmeket:  

#### <a name="http-request"></a>HTTP-kérés
Kérések fejlécei

```console
POST https://media.windows.net/API/AssetFilters HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net  
```

A kérés törzse 

```console
{   
   "Name":"AssetFilter", 
   "ParentAssetId":"nb:cid:UUID:536e555d-1500-80c3-92dc-f1e4fdc6c592", 
   "PresentationTimeRange":{   
      "StartTimestamp":"0", 
      "EndTimestamp":"9223372036854775807", 
      "PresentationWindowDuration":"12000000000", 
      "LiveBackoffDuration":"0", 
      "Timescale":"10000000" 
   }, 
   "Tracks":[   
      {   
         "PropertyConditions": 
              [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

#### <a name="http-response"></a>HTTP-válasz

```console
HTTP/1.1 201 Created 
. . . 
```

## <a name="list-filters"></a>Szűrők listázása
### <a name="get-all-global-filters-in-the-ams-account"></a>Az összes globális **szűrő** lekérdezése az AMS-fiókban
A szűrők listázásához használja az alábbi HTTP-kérelmeket: 

#### <a name="http-request"></a>HTTP-kérés

```console
GET https://media.windows.net/API/Filters HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
Host: media.windows.net 
```

### <a name="get-assetfilters-associated-with-an-asset"></a>Eszközhöz társított **AssetFilter**-k beolvasása
#### <a name="http-request"></a>HTTP-kérés

```console
GET https://media.windows.net/API/Assets('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592')/AssetFilters HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
```

### <a name="get-an-assetfilter-based-on-its-id"></a>**AssetFilter** beolvasása az azonosító alapján
#### <a name="http-request"></a>HTTP-kérés

```console
GET https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter') HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000
```

## <a name="update-filters"></a>Frissítési szűrők
Egy szűrő új tulajdonságértékek használatával történő frissítéséhez használja a PATCH, a PUT vagy az MERGE értéket.  További információ ezekről a műveletekről: [javítás, Put, Egyesítés](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893).

Ha frissít egy szűrőt, akár két percet is igénybe vehet, amíg a streaming-végpont frissíti a szabályokat. Ha a tartalom a szűrő használatával lett kézbesítve (és gyorsítótárazva van a proxys és a CDN-gyorsítótárban), akkor a szűrő frissítése a Player meghibásodását eredményezheti. A szűrő frissítése után törölje a gyorsítótárat. Ha ez a beállítás nem lehetséges, érdemes lehet egy másik szűrőt használni.  

### <a name="update-global-filters"></a>Globális szűrők frissítése
Globális szűrő frissítéséhez használja a következő HTTP-kérelmeket: 

#### <a name="http-request"></a>HTTP-kérés
Kérelem fejlécei: 

```console
MERGE https://media.windows.net/API/Filters('filterName') HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
Content-Length: 384
```

Kérés törzse: 

```console
{ 
   "Tracks":[   
      {   
         "PropertyConditions": 
         [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

### <a name="update-local-assetfilters"></a>Helyi AssetFilters frissítése
Helyi szűrő frissítéséhez használja a következő HTTP-kérelmeket: 

#### <a name="http-request"></a>HTTP-kérés
Kérelem fejlécei: 

```console
MERGE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__TestFilter')  HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Content-Type: application/json 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
x-ms-client-request-id: 00000000-0000-0000-0000-000000000000 
Host: media.windows.net 
```

Kérés törzse: 

```console
{ 
   "Tracks":[   
      {   
         "PropertyConditions": 
         [   
            {   
               "Property":"Type", 
               "Value":"audio", 
               "Operator":"Equal" 
            }, 
            {   
               "Property":"Bitrate", 
               "Value":"0-2147483647", 
               "Operator":"Equal" 
            } 
         ] 
      } 
   ] 
} 
```

## <a name="delete-filters"></a>Szűrők törlése
### <a name="delete-global-filters"></a>Globális szűrők törlése
Globális szűrő törléséhez használja a következő HTTP-kérelmeket:

#### <a name="http-request"></a>HTTP-kérés

```console
DELETE https://media.windows.net/api/Filters('GlobalFilter') HTTP/1.1 
DataServiceVersion:3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN>  
x-ms-version: 2.19 
Host: media.windows.net 
```

### <a name="delete-local-assetfilters"></a>Helyi AssetFilters törlése
Helyi AssetFilter törléséhez használja a következő HTTP-kérelmeket:

#### <a name="http-request"></a>HTTP-kérés

```console
DELETE https://media.windows.net/API/AssetFilters('nb%3Acid%3AUUID%3A536e555d-1500-80c3-92dc-f1e4fdc6c592__%23%23%23__LocalFilter') HTTP/1.1 
DataServiceVersion: 3.0 
MaxDataServiceVersion: 3.0 
Accept: application/json 
Accept-Charset: UTF-8 
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19 
Host: media.windows.net 
```

## <a name="build-streaming-urls-that-use-filters"></a>Szűrőket használó streaming URL-címek összeállítása
Az eszközök közzétételével és továbbításával kapcsolatos információkért lásd: [tartalom továbbítása az ügyfeleknek áttekintés](media-services-deliver-content-overview.md).

Az alábbi példák bemutatják, hogyan adhat hozzá szűrőket a folyamatos átviteli URL-címekhez.

**MPEG DASH** 

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v4**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)`

**Apple HTTP Live Streaming (HLS) v3**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)`

**Smooth Streaming**

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)`

    
## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Dinamikus jegyzékfájlok áttekintése](media-services-dynamic-manifest-overview.md)
