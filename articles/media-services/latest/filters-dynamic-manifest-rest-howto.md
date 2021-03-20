---
title: Szűrők létrehozása Azure Media Services v3 REST API
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, hogy az ügyfél egy stream adott szakaszait továbbítsa. A Media Services v3 REST API dinamikus jegyzékfájlokat hoz létre a szelektív streaming eléréséhez.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c1d7bf933b487c40d571f1912341b5ef771e4e67
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90527326"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Szűrők létrehozása Media Services REST API

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ha a tartalmat az ügyfeleknek (élő vagy igény szerinti közvetítéssel közvetíti), az ügyfélnek nagyobb rugalmasságra lehet szüksége, mint amit az alapértelmezett eszköz jegyzékfájljában ismertetünk. A Azure Media Services segítségével meghatározhatja a tartalomhoz tartozó fiókok szűrőit és a hozzájuk tartozó szűrőket. 

A funkció részletes ismertetését és a használatban lévő forgatókönyveket lásd: [dinamikus jegyzékfájlok](filters-dynamic-manifest-overview.md) és [szűrők](filters-concept.md).

Ebből a témakörből megtudhatja, hogyan határozhat meg egy igény szerinti videóhoz tartozó szűrőt, és hogyan hozhat létre REST API-kat a [fiókok](/rest/api/media/accountfilters) és az [eszközök szűrőinek](/rest/api/media/assetfilters)létrehozásához. 

> [!NOTE]
> Ügyeljen rá, hogy ellenőrizze a [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Előfeltételek 

A jelen témakörben ismertetett lépések végrehajtásához a következőket kell tennie:

- Tekintse át [a szűrőket és a dinamikus jegyzékfájlokat](filters-dynamic-manifest-overview.md).
- [A Poster beállítása Azure Media Services REST API-hívásokhoz](media-rest-apis-with-postman.md).

    Ügyeljen arra, hogy kövesse az [Azure ad-token beszerzése](media-rest-apis-with-postman.md#get-azure-ad-token)című témakör utolsó lépését. 

## <a name="define-a-filter"></a>Szűrő definiálása  

A következő példa a **kérelem szövegtörzsét** határozza meg, amely meghatározza a jegyzékfájlhoz hozzáadott kiválasztási feltételeket. Ez a szűrő minden olyan hangsávot magában foglal, amely EC-3, valamint az 0-1000000-es tartományon belüli bitrátával rendelkező videók.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Fiókok szűrőinek létrehozása

A letöltött Poster gyűjteményében válassza a **fiókok** -> **szűrői létrehozása vagy frissítése fiók szűrőt**.

A **put** HTTP-kérelem módszere a következőhöz hasonló:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Válassza a **törzs** fület, és illessze be a [korábban megadott](#define-a-filter)JSON-kódot.

Kattintson a **Küldés** gombra. 

A szűrő létrejött.

További információ: [Létrehozás vagy frissítés](/rest/api/media/accountfilters/createorupdate). Lásd még: [JSON-példák szűrőkhöz](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Eszközcsoport-szűrők létrehozása  

A letöltött "Media Services v3" Poster-gyűjteményben válassza az **eszközök** -> **Létrehozás vagy frissítés eszköz szűrő** lehetőséget.

A **put** HTTP-kérelem módszere a következőhöz hasonló:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Válassza a **törzs** fület, és illessze be a [korábban megadott](#define-a-filter)JSON-kódot.

Kattintson a **Küldés** gombra. 

Az eszköz szűrője létrejött.

Az eszközoldali szűrők létrehozásával vagy frissítésével kapcsolatos részletekért lásd: [Létrehozás vagy frissítés](/rest/api/media/assetfilters/createorupdate). Lásd még: [JSON-példák szűrőkhöz](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter). 

## <a name="associate-filters-with-streaming-locator"></a>Szűrők hozzárendelése a folyamatos átviteli Lokátorhoz

Megadhatja az eszköz vagy a fiók szűrőinek listáját, amely a folyamatos átviteli Lokátorra vonatkozik. A [dinamikus csomagoló (streaming Endpoint)](dynamic-packaging-overview.md) a szűrők ezen listáját alkalmazza, az ügyfél által megadott URL-címen. Ez a kombináció létrehoz egy [dinamikus jegyzékfájlt](filters-dynamic-manifest-overview.md), amely a streaming keresőben megadott URL + szűrők szűrői alapján történik. Azt javasoljuk, hogy használja ezt a funkciót, ha szűrőket kíván alkalmazni, de nem szeretné kitenni a szűrő nevét az URL-címben.

Ha a REST használatával szeretne szűrőket létrehozni és hozzárendelni egy streaming-Lokátorhoz, használja a [streaming-lokátorok – API létrehozása](/rest/api/media/streaminglocators/create) lehetőséget, majd `properties.filters` a [kérelem törzsében](/rest/api/media/streaminglocators/create#request-body).
                                
## <a name="stream-using-filters"></a>Stream szűrők használatával

A szűrők meghatározása után az ügyfelek a streaming URL-ben használhatják őket. A szűrők alkalmazhatók az adaptív sávszélességű adatfolyam-továbbítási protokollokra: Apple HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming.

Az alábbi táblázat néhány példát mutat be a szűrőket tartalmazó URL-címekre:

|Protokoll|Példa|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>Következő lépések

[Stream-videók](stream-files-tutorial-with-rest.md) 
