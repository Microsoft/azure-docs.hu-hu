---
title: Szűrők létrehozása az Azure Media Services a CLI használatával |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozhat létre szűrők a Media Services a parancssori felület használatával.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 26350479f0f066f45c143e1a35061b3a409de309
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786493"
---
# <a name="creating-filters-with-cli"></a>Szűrők létrehozása a CLI-vel 

Kézbesítse a tartalmakat az ügyfelek számára (élő eseményeket vagy igény szerinti Videószolgáltatás streaming), amikor az ügyfél az alapértelmezett eszköz jegyzékfájl leírtnál nagyobb rugalmasságot előfordulhat, hogy kell. Az Azure Media Services lehetővé teszi, hogy meghatározza a fiók és a tartalom eszköz szűrőket. 

Ez a szolgáltatás és a felhasználási forgatókönyvek részletes ismertetését lásd: [dinamikus jegyzékfájlok](filters-dynamic-manifest-overview.md) és [szűrők](filters-concept.md).

Ez a témakör bemutatja, hogyan beállít egy szűrőt az igény szerinti videó eszköz és a CLI a Media Services v3 használatával létrehozhat [Fiókszűrők](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) és [eszköz szűrők](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

> [!NOTE]
> Mindenképpen tekintse át [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Előfeltételek 

- [A Media Services-fiók létrehozása](create-account-cli-how-to.md). Ellenőrizze, hogy ne felejtse el az erőforráscsoport nevét és a Media Services-fiók nevét. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Szűrő megadásához. 

Az alábbi példa meghatározza a végső jegyzékfájl hozzáadott követése kiválasztási feltételek. Ez a szűrő tartalmazza, bármilyen, amelyek EC-3 hangsáv és bármely videó nyomon követi a 0-1000000 sávszélességű rendelkező tartományt.

> [!TIP]
> Ha azt tervezi, hogy definiálása **szűrők** REST, figyelje meg, hogy meg kell adni a "Tulajdonságok" burkolót JSON-objektum.  

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

## <a name="create-account-filters"></a>Fiók szűrők létrehozása

A következő [az ams-fiók-szűrő](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) parancs létrehoz egy szűrő szűrő nyomon követése beállításokat, amelyek korábban [korábban meghatározott](#define-a-filter). 

A parancs lehetővé teszi, hogy egy nem kötelező adnia `--tracks` JSON a track beállításokat jelölő paraméterben.  Használat @{file} betölteni a JSON-fájlból. Ha az Azure CLI helyileg használ, adja meg a fájl teljes elérési útja:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Lásd még [JSON példák a szűrők](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter).

## <a name="create-asset-filters"></a>Az eszközintelligencia-szűrők létrehozásához

A következő [az ams az eszközintelligencia-szűrő](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) parancs létrehoz egy adategység-szűrő szűrő nyomon követése beállításokat, amelyek korábban [korábban meghatározott](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Lásd még [JSON példák a szűrők](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter).

## <a name="associate-filters-with-streaming-locator"></a>Streamelési lokátor szűrők társítása

Megadhatja, hogy az eszköz vagy a fiók szűrők, a Streamelési lokátor is érvényesek listáját. A [dinamikus Packager (folyamatos átviteli végponton)](dynamic-packaging-overview.md) vonatkozik ez a lista azokat az URL-címet adja meg az ügyfél és-szűrők. Állít elő, ez a kombináció egy [dinamikus Manifest](filters-dynamic-manifest-overview.md), amely alapján az URL-címben szűrők + szűrők megad a Streamelési lokátor. Azt javasoljuk, hogy a szolgáltatás használata, ha alkalmazza a szűrőket, de nem szeretné elérhetővé tenni az URL-szűrő nevét.

A következő CLI-kód bemutatja, hogyan hozzon létre egy Streamelési Lokátort, és adja meg `filters`. Ez a tulajdonságot nem kötelező, amely az eszközintelligencia szűrő nevét és/vagy a szűrő nevének szóközzel elválasztott listáját.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Stream-szűrők használata

Miután meghatározott szűrőket, az ügyfelek lehetett használni őket a streamelési URL-CÍMÉT. Az adaptív sávszélességű streamelési protokollok szűrőket is lehet alkalmazni: Apple HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming.

Az alábbi táblázatban néhány példa az URL-címek szűrőket jeleníti meg:

|Protocol|Példa|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Következő lépés

[Stream-videók](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
