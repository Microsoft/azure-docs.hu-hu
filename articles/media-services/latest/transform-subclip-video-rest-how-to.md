---
title: Videó kivágása Media Services REST-kódolással
description: Ez a témakör azt ismerteti, hogyan lehet videót alklipet használni Azure Media Services REST használatával történő kódoláskor
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
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: 9c568963355068b744820407e6bdd6b1b6721128
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281884"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Videó kivágása Media Services-REST kódolással

Ha [feladatokkal](/rest/api/media/jobs)kódolja a videót, levágja vagy alklipet készíthet. Ez a funkció a [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -előállítók vagy a [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) -előállítók használatával létrehozott bármely [átalakítással](/rest/api/media/transforms) működik. 

A témakör REST példája egy olyan feladatot hoz létre, amely egy kódolási feladatot elküldő videót metsz el. 

## <a name="prerequisites"></a>Előfeltételek

A jelen témakörben ismertetett lépések végrehajtásához a következőket kell tennie:

- [Hozzon létre egy Azure Media Services fiókot](./account-create-how-to.md).
- [A Poster beállítása Azure Media Services REST API-hívásokhoz](setup-postman-rest-how-to.md).
    
    Ügyeljen arra, hogy kövesse az [Azure ad-token beszerzése](setup-postman-rest-how-to.md#get-azure-ad-token)című témakör utolsó lépését. 
- Hozzon létre egy átalakítót és egy kimeneti eszközt. Megtudhatja, hogyan hozhat létre átalakítót és kimeneti eszközöket a [Távoli fájl kódolása URL-cím alapján, és hogyan továbbíthatja a videó – Rest](stream-files-tutorial-with-rest.md) oktatóanyagot.
- Tekintse át a [kódolási koncepcióval](encode-concept.md) foglalkozó témakört.

## <a name="create-a-subclipping-job"></a>Kivágási feladatok létrehozása

1. A letöltött Poster-gyűjteményben válassza az **átalakítások és feladatok**  ->  **létrehozási feladat az alárendelt levágással** lehetőséget.
    
    A **put** -kérés így néz ki:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Frissítse a "transformName" környezeti változó értékét az átalakító nevével. 
1. Válassza a **törzs** fület, és frissítse a "MyOutputAsset" értéket a kimeneti objektum nevével.

    ```json
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
            "Ignite-short.mp4"
          ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Kattintson a **Küldés** gombra.

    Ekkor megjelenik a **Válasz** a létrehozott és elküldött feladatra vonatkozó információkkal, valamint a feladatok állapotával. 

## <a name="next-steps"></a>Következő lépések

[Kódolás egyéni átalakítással](encode-custom-preset-rest-how-to.md) 
