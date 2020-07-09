---
title: Egyéni átalakítás kódolása Media Services v3 REST-Azure használatával | Microsoft Docs
description: Ez a témakör azt mutatja be, hogyan használható a Azure Media Services v3 egy egyéni átalakítás a REST használatával történő kódolásához.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "65761797"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Kódolás egyéni átalakítással – REST

Ha Azure Media Services kódolást használ, gyorsan megkezdheti az egyik javasolt beépített beállításkészletet az iparági ajánlott eljárások alapján, ahogyan azt a [streaming Files](stream-files-tutorial-with-rest.md#create-a-transform) oktatóanyag mutatja. Létrehozhat egy egyéni beállításkészletet is, amely az adott forgatókönyv vagy eszköz követelményeit célozza meg.

## <a name="considerations"></a>Megfontolandó szempontok

Egyéni beállításkészletek létrehozásakor a következő szempontokat kell figyelembe venni:

* Az AVC-tartalom magasságának és szélességének összes értékének a 4-es többszörösének kell lennie.
* A Azure Media Services V3 esetében az összes kódolási bitrátát bit/másodpercben kell kiszámítani. Ez különbözik az előre beállított v2 API-kkal, amelyek az egységhez kilobit/másodpercet használnak. Ha például a v2-es bitrátát 128 (kilobit/másodperc) értékre adták, a v3-as verzióban a 128000 (BITS/Second) értéket adja meg.

## <a name="prerequisites"></a>Előfeltételek 

- [Hozzon létre egy Media Services fiókot](create-account-cli-how-to.md). <br/>Ügyeljen rá, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét. 
- [A Poster beállítása Azure Media Services REST API-hívásokhoz](media-rest-apis-with-postman.md).<br/>Ügyeljen arra, hogy kövesse az [Azure ad-token beszerzése](media-rest-apis-with-postman.md#get-azure-ad-token)című témakör utolsó lépését. 

## <a name="define-a-custom-preset"></a>Egyéni beállításkészlet definiálása

Az alábbi példa egy új átalakító kérelem törzsét határozza meg. Definiáljuk azokat a kimeneteket, amelyeket a rendszer a transzformáció használatakor szeretne generálni. 

Ebben a példában először adunk hozzá egy AacAudio réteget a hangkódoláshoz, és két H264Video réteget a videó kódolásához. A videó rétegekben címkéket rendelünk hozzá, hogy használhatók legyenek a kimeneti fájlnevekben. Ezután azt szeretnénk, hogy a kimenet bélyegképeket is tartalmazzon. Az alábbi példában a bemeneti videó felbontásának 50%-ában, valamint három időbélyeggel ({25%, 50%, 75}) adjuk meg a képeket PNG formátumban. Végül megadjuk a kimeneti fájlok formátumát – az egyiket a videó + hang, a másik pedig a miniatűrökhöz. Mivel több H264Layers is rendelkezünk, olyan makrókat kell használnia, amelyek egyedi neveket hoznak létre rétegként. Használhatunk egy vagy egy `{Label}` `{Bitrate}` makrót is, a példa az előzőt mutatja.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
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
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Új átalakítás létrehozása  

Ebben a példában egy olyan **átalakítót** hozunk létre, amely a korábban definiált egyéni beállításkészlet alapján lett létrehozva. Átalakítás létrehozásakor először a [Get](https://docs.microsoft.com/rest/api/media/transforms/get) paranccsal ellenőrizze, hogy az egyik már létezik-e. Ha az átalakítás létezik, használja újra. 

A letöltött Poster gyűjteményében válassza az **átalakítások és feladatok** -> **Létrehozás vagy frissítés átalakítás**lehetőséget.

A **put** HTTP-kérelem módszere a következőhöz hasonló:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Válassza a **törzs** fület, és cserélje le a törzset a [korábban megadott](#define-a-custom-preset)JSON-kódra. Ahhoz Media Services, hogy az átalakítást a megadott videóra vagy hangra alkalmazza, el kell küldenie egy feladatot az átalakítás alatt.

Válassza a **Küldés**lehetőséget. 

Ahhoz Media Services, hogy az átalakítást a megadott videóra vagy hangra alkalmazza, el kell küldenie egy feladatot az átalakítás alatt. Ha egy teljes példát mutat be, amely bemutatja, hogyan küldhet el egy feladatot egy átalakítás alatt, tekintse meg az [oktatóanyag: Stream video Files – Rest](stream-files-tutorial-with-rest.md)című témakört.

## <a name="next-steps"></a>További lépések

[További Rest-műveletek](https://docs.microsoft.com/rest/api/media/)
