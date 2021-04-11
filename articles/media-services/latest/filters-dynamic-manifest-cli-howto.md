---
title: Szűrők létrehozása a parancssori felület használatával Azure Media Services
description: Ez a cikk bemutatja, hogyan hozhatók létre szűrők a parancssori felülettel Azure Media Services v3 használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: c3b58981c3b76f86397b7f2724ac5da71c7ad3dc
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960214"
---
# <a name="creating-filters-with-cli"></a>Szűrők létrehozása a parancssori felülettel

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ha az ügyfelek számára továbbít tartalmat (élő vagy igény szerinti közvetítést), az ügyfélnek nagyobb rugalmasságra lehet szüksége, mint amit az alapértelmezett eszköz jegyzékfájljában ismertetünk. A Azure Media Services segítségével meghatározhatja a tartalomhoz tartozó fiókok szűrőit és a hozzájuk tartozó szűrőket.

A funkció részletes ismertetését és a használatban lévő forgatókönyveket lásd: [dinamikus jegyzékfájlok](filters-dynamic-manifest-overview.md) és [szűrők](filters-concept.md).

Ebből a témakörből megtudhatja, hogyan konfigurálhat egy szűrőt egy igény szerinti videóhoz, és hogyan használhatja a CLI-t a Media Services v3-hoz a [fiókok szűrőinek](/cli/azure/ams/account-filter) és az [eszközök szűrőinek](/cli/azure/ams/asset-filter)létrehozásához.

> [!NOTE]
> Ügyeljen rá, hogy ellenőrizze a [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Media Services fiókot](./account-create-how-to.md). Ügyeljen rá, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét.

## <a name="define-a-filter"></a>Szűrő definiálása

Az alábbi példa a végső jegyzékfájlhoz hozzáadott kiválasztási feltételeket határozza meg. Ez a szűrő minden olyan hangsávot magában foglal, amely EC-3, valamint az 0-1000000-es tartományon belüli bitrátával rendelkező videók.

> [!TIP]
> Ha meg szeretné határozni a **szűrőket** a REST-ben, figyelje meg, hogy meg kell adnia a "Properties" burkoló JSON-objektumot.  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Fiókok szűrőinek létrehozása

A következő az [AMS Account-Filter](/cli/azure/ams/account-filter) parancs egy olyan fiók szűrőt hoz létre, amely a [korábban definiált](#define-a-filter)szűrési nyomon követési beállításokkal rendelkezik.

A parancs lehetővé teszi, hogy egy opcionális `--tracks` paramétert adjon át, amely a követési beállításokat jelképező JSON-t tartalmazza.  A JSON fájlból való betöltéséhez használja a @ {file} fájlt. Ha helyileg használja az Azure CLI-t, a fájl teljes elérési útját kell megadnia:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Lásd még: [JSON-példák szűrőkhöz](/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Eszközcsoport-szűrők létrehozása

A következő az [AMS Asset-Filter](/cli/azure/ams/asset-filter) parancs létrehoz egy objektum szűrőt a [korábban definiált](#define-a-filter)szűrő követési beállításokkal. 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Lásd még: [JSON-példák szűrőkhöz](/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Szűrők hozzárendelése a folyamatos átviteli Lokátorhoz

Megadhatja az eszköz vagy a fiók szűrőinek listáját, amely a folyamatos átviteli Lokátorra vonatkozik. A [dinamikus csomagoló (streaming Endpoint)](dynamic-packaging-overview.md) a szűrők ezen listáját alkalmazza, az ügyfél által megadott URL-címen. Ez a kombináció létrehoz egy [dinamikus jegyzékfájlt](filters-dynamic-manifest-overview.md), amely a streaming keresőben megadott URL + szűrők szűrői alapján történik. Azt javasoljuk, hogy használja ezt a funkciót, ha szűrőket kíván alkalmazni, de nem szeretné kitenni a szűrő nevét az URL-címben.

A következő CLI-kód bemutatja, hogyan hozhat létre adatfolyam-keresőt, és hogyan adható meg `filters` . Ez egy nem kötelezően megadandó tulajdonság, amely az eszközök és/vagy a fiókok szűrési neveinek szóközzel tagolt listáját veszi figyelembe.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Stream szűrők használatával

A szűrők meghatározása után az ügyfelek a streaming URL-ben használhatják őket. A szűrők alkalmazhatók az adaptív sávszélességű adatfolyam-továbbítási protokollokra: Apple HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming.

Az alábbi táblázat néhány példát mutat be a szűrőket tartalmazó URL-címekre:

|Protokoll|Példa|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Következő lépés

[Stream-videók](stream-files-tutorial-with-api.md)

## <a name="see-also"></a>Lásd még

[Azure CLI](/cli/azure/ams)
