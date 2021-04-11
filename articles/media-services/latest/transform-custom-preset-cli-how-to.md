---
title: Egyéni átalakítás parancssori felületének kódolása
description: Ebből a témakörből megtudhatja, hogyan kódolhat egyéni átalakításokat az Azure CLI használatával a Azure Media Services v3 használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: d3977b02bffd49762bdcb69fa4940052518c9d25
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498996"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Kódolás egyéni átalakítással – Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ha Azure Media Services kódolást használ, gyorsan megkezdheti az egyik javasolt beépített beállításkészletet az iparági ajánlott eljárások alapján, ahogyan azt a [streaming Files](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) rövid útmutatója mutatja. Létrehozhat egy egyéni beállításkészletet is, amely az adott forgatókönyv vagy eszköz követelményeit célozza meg.

## <a name="considerations"></a>Megfontolandó szempontok

Egyéni beállításkészletek létrehozásakor a következő szempontokat kell figyelembe venni:

* Az AVC-tartalom magasságának és szélességének összes értékének a 4-es többszörösének kell lennie.
* A Azure Media Services V3 esetében az összes kódolási bitrátát bit/másodpercben kell kiszámítani. Ez különbözik az előre beállított v2 API-kkal, amelyek az egységhez kilobit/másodpercet használnak. Ha például a v2-es bitrátát 128 (kilobit/másodperc) értékre adták, a v3-as verzióban a 128000 (BITS/Second) értéket adja meg.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Media Services fiókot](./account-create-how-to.md).

Ügyeljen rá, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét.

## <a name="define-a-custom-preset"></a>Egyéni beállításkészlet definiálása

Az alábbi példa egy új átalakító kérelem törzsét határozza meg. Definiáljuk azokat a kimeneteket, amelyeket a rendszer a transzformáció használatakor szeretne generálni.

Ebben a példában először adunk hozzá egy AacAudio réteget a hangkódoláshoz, és két H264Video réteget a videó kódolásához. A videó rétegekben címkéket rendelünk hozzá, hogy használhatók legyenek a kimeneti fájlnevekben. Ezután azt szeretnénk, hogy a kimenet bélyegképeket is tartalmazzon. Az alábbi példában a bemeneti videó felbontásának 50%-ában, valamint három időbélyeggel ({25%, 50%, 75}) adjuk meg a képeket PNG formátumban. Végül megadjuk a kimeneti fájlok formátumát – az egyiket a videó + hang, a másik pedig a miniatűrökhöz. Mivel több H264Layers is rendelkezünk, olyan makrókat kell használnia, amelyek egyedi neveket hoznak létre rétegként. Használhatunk egy vagy egy `{Label}` `{Bitrate}` makrót is, a példa az előzőt mutatja.

Ezt a transzformációt egy fájlba fogjuk menteni. Ebben a példában a fájlt nevezjük `customPreset.json` .

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}
```

## <a name="create-a-new-transform"></a>Új átalakítás létrehozása  

Ebben a példában egy olyan **átalakítót** hozunk létre, amely a korábban definiált egyéni beállításkészlet alapján lett létrehozva. Átalakítás létrehozásakor először ellenőrizze, hogy már létezik-e. Ha az átalakítás létezik, használja újra. A következő `show` parancs visszaadja az `customTransformName` átalakítást, ha létezik:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

A következő Azure CLI-parancs létrehozza az átalakítást az egyéni beállításkészlet alapján (korábban definiálva).

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Ahhoz Media Services, hogy az átalakítást a megadott videóra vagy hangra alkalmazza, el kell küldenie egy feladatot az átalakítás alatt. Ha egy teljes példát mutat be, amely bemutatja, hogyan küldhet el egy feladatot egy átalakítás alatt, tekintse meg a gyors útmutató [: Stream video Files – Azure CLI](stream-files-cli-quickstart.md)című témakört.

## <a name="see-also"></a>Lásd még

[Azure CLI](/cli/azure/ams)
