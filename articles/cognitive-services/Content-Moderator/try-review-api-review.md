---
title: Moderálási felülvizsgálatok létrehozása REST API-konzollal – Content Moderator
titleSuffix: Azure Cognitive Services
description: Az Azure Content Moderator Review API-k használatával képeket vagy szöveges értékeléseket hozhat létre az emberi moderáláshoz.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: 479c7c455f07d098edd327196803e85df24dfb6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905133"
---
# <a name="create-human-reviews-api-console"></a>Emberi értékelések létrehozása (API-konzol)

A [értékelések](./review-api.md#reviews) az emberi moderátorok számára az értékelés céljából tárolják és jelenítik meg a tartalmakat. Amikor a felhasználó elvégez egy felülvizsgálatot, az eredményeket egy megadott visszahívási végpontra küldi a rendszer. Ebből az útmutatóból megtudhatja, hogyan állíthat be felülvizsgálatokat a REST API-k segítségével az API-konzolon. Miután megértette az API-k szerkezetét, könnyedén elvégezheti ezeket a hívásokat a REST-kompatibilis platformokra.

## <a name="prerequisites"></a>Előfeltételek

- Jelentkezzen be, vagy hozzon létre egy fiókot a Content Moderator [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) webhelyén.

## <a name="create-a-review"></a>Felülvizsgálat létrehozása

A felülvizsgálat létrehozásához nyissa meg a **[felülvizsgálat – API-hivatkozás létrehozása](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** lapot, és válassza ki a kulcs régiójához tartozó gombot (ezt a végpont URL-címében találja a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) **hitelesítő adatok** lapján). Ezzel elindítja az API-konzolt, ahol könnyedén hozhat létre és futtathat REST API hívásokat.

![Áttekintés – régió kiválasztásának beolvasása](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Adja meg a REST-hívás paramétereit

Adja meg a **csapatnév** és a **OCP-APIM-Subscription-Key** értékeket:

- **csapatnév**: a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com/) fiókjának beállításakor létrehozott csoport azonosítója (a felülvizsgálati eszköz hitelesítő adatai képernyő **azonosító** mezőjében található).
- **OCP-APIM-előfizetés-kulcs**: a Content moderator kulcsa. Ez a [felülvizsgálati eszköz](https://contentmoderator.cognitive.microsoft.com) **Beállítások** lapján található.

### <a name="enter-a-review-definition"></a>Adja meg a felülvizsgálati definíciót

Szerkessze a **kérelem törzsét** , és adja meg a JSON-kérést a következő mezőkkel:

- **Metaadatok**: egyéni kulcs-érték párok, amelyeket vissza kell adni a visszahívási végpontnak. Ha a kulcs egy rövid kód, amely a [felülvizsgálati eszközben](https://contentmoderator.cognitive.microsoft.com)van meghatározva, a címkeként jelenik meg.
- **Tartalom**: a képek és videók tartalma esetén ez egy URL-karakterlánc, amely a tartalomra mutat. Szöveges tartalom esetén ez a tényleges szöveges karakterlánc.
- **ContentId**: egyéni azonosító sztring. Ezt a karakterláncot a rendszer átadja az API-nak, és visszaküldi a visszahívást. Hasznos lehet belső azonosítók vagy metaadatok társítására egy moderálási feladatok eredményeivel.
- **CallbackEndpoint**: (nem kötelező) a visszahívási információk fogadására szolgáló URL-cím a felülvizsgálat befejezésekor.

Az alapértelmezett kérelem törzse példákat mutat be a létrehozható különböző típusú felülvizsgálatokra:

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>A kérés elküldése
  
Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **Válasz állapota** `200 OK` , a **válasz tartalma** mező pedig a felülvizsgálat azonosítóját jeleníti meg. Másolja ezt az azonosítót a következő lépésekben való használatra.

![Felülvizsgálat – a konzol válaszának létrehozása mező megjeleníti a felülvizsgálati azonosítót](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>Vizsgálja meg az új felülvizsgálatot

A [felülvizsgálati eszközben](https://contentmoderator.cognitive.microsoft.com)válassza a   >   / **képszöveg** / **videó** ellenőrzése lehetőséget (a használt tartalomtól függően). A feltöltött tartalomnak szerepelnie kell az emberi felülvizsgálatra készen.

![A Soccer Ball-eszköz képének áttekintése](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>Áttekintés részleteinek beolvasása

Egy meglévő felülvizsgálat részleteinek lekéréséhez nyissa meg a [Review-Get API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) referenciát, és válassza ki a régióhoz tartozó gombot (a régiót, amelyben a kulcsot felügyeli).

![Munkafolyamat – régió kiválasztásának beolvasása](images/test-drive-region.png)

Adja meg a REST-hívás paramétereit a fenti szakaszban leírtak szerint. Ebben a lépésben a **reviewId** a felülvizsgálat létrehozásakor kapott egyedi azonosító sztring.

![Ellenőrzés – a konzol létrehozási eredményeinek létrehozása](images/test-drive-review-3.PNG)
  
Kattintson a **Küldés** gombra. Ha a művelet sikeres, a **Válasz állapota** `200 OK` , a **válasz tartalma** pedig JSON formátumban jeleníti meg a felülvizsgálati adatokat, például a következőkhöz:

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

Jegyezze fel a válaszban a következő mezőket:

- **állapota**
- **reviewerResultTags**: Ez akkor jelenik meg, ha az emberi felülvizsgálati csapat manuálisan hozzáadta a címkéket (ez a **createdBy** mező látható).
- **metaadatok**: Itt láthatók azok a címkék, amelyeket először a felülvizsgálatban adtak hozzá, mielőtt az emberi felülvizsgálati csapat megváltoztatta volna a módosításokat.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan hozhat létre a tartalom-moderálási felülvizsgálatokat a REST API használatával. Ezután integrálja a felülvizsgálatokat egy végpontok közötti moderálási forgatókönyvbe, például az [E-kereskedelmi moderálási](./ecommerce-retail-catalog-moderation.md) oktatóanyagba.