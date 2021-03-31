---
title: Képmoderálás – Content Moderator
titleSuffix: Azure Cognitive Services
description: A felnőtteknek és a zamatos tartalomnak az Content Moderator géppel támogatott lemezkép-moderálási és a hurok-áttekintő eszköz használatával mérsékelt képeket használhat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: fe76e32bfd9b1734f3c84a400f897b7af7e3168b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800995"
---
# <a name="learn-image-moderation-concepts"></a>A képek moderálásával kapcsolatos fogalmak megismerése

A felnőtteknek és a zamatos tartalmaknak a Content Moderator géppel támogatott lemezkép-moderálását és [felülvizsgálati eszközét](Review-Tool-User-Guide/human-in-the-loop.md) használva mérsékelt képeket használhat. Képek beolvasása szöveges tartalomhoz és a szöveg kinyerése és az arcok észlelése. A képeket megtekintheti az egyéni listán, és további műveleteket hajthat végre.

## <a name="evaluating-for-adult-and-racy-content"></a>A felnőtt és a zamatos tartalom kiértékelése

A **kiértékelési** művelet 0 és 1 közötti megbízhatósági pontszámot ad vissza. Az érték igaz vagy HAMIS logikai értéket ad vissza. Ezek az értékek azt jelzik, hogy a rendszerkép tartalmaz-e potenciális felnőtt vagy zamatos tartalmat. Ha az API-t a rendszerképpel (fájl vagy URL) hívja meg, a visszaadott válasz a következő információkat tartalmazza:

```json
"ImageModeration": {
    .............
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    ............
    ],
```

> [!NOTE]
> 
> - Az `isImageAdultClassified` az olyan képek esetleges előfordulását jelzi, amelyek bizonyos esetekben szexuálisan explicit vagy felnőtt tartalomnak minősülhetnek.
> - Az `isImageRacyClassified` az olyan képek esetleges előfordulását jelzi, amelyek bizonyos esetekben szexuális utalásokat tartalmazó vagy felnőtt tartalomnak minősülhetnek.
> - A pontszámok 0 és 1 között vannak. Minél magasabb a pontszám, annál nagyobb a modell, ami azt jelzi, hogy a kategória is alkalmazható. Ez az előzetes verzió egy statisztikai modellre támaszkodik, nem pedig manuálisan kódolt eredményeket. Javasoljuk, hogy tesztelje a saját tartalmait annak meghatározásához, hogy az egyes kategóriák hogyan illeszkednek a követelményekhez.
> - A logikai értékek értéke TRUE (igaz) vagy FALSE (hamis) lehet a belső pontszám küszöbértéke alapján. Az ügyfeleknek fel kell mérniük, hogy ezt az értéket használják-e, vagy egyéni küszöbértékeket határoznak meg a tartalmi szabályzatok alapján.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Szöveg észlelése optikai karakterfelismeréssel (OCR)

Az **optikai karakterfelismerési (OCR)** művelettel előre megjósolható, hogy a szöveg tartalma szerepel-e egy képben, és Kinyeri a szöveg moderálását más felhasználások között. Megadhatja a nyelvet. Ha nem ad meg nyelvet, az észlelés alapértelmezett értéke az angol.

A válasz a következő információkat tartalmazza:
- Az eredeti szöveg.
- Az észlelt szöveges elemek megbízhatósági pontszámai.

Példa kivonatra:

```json
"TextDetection": {
    "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
    },
    .........
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
},
```

## <a name="detecting-faces"></a>Arcészlelés

Az arcok észlelése segít a személyes, például a képekben lévő arcok észlelésében. Felderítheti az esetleges arcokat és a lehetséges arcok számát az egyes képekben.

A válasz a következő információkat tartalmazza:

- Arcok száma
- Az észlelt arcok helyeinek listája

Példa kivonatra:

```json
"FaceDetection": {
    ......
    "result": true,
    "count": 2,
    "advancedInfo": [
        .....
    ],
    "faces": [
        {
            "bottom": 598,
            "left": 44,
            "right": 268,
            "top": 374
        },
        {
            "bottom": 620,
            "left": 308,
            "right": 532,
            "top": 396
        }
    ]
}
```

## <a name="creating-and-managing-custom-lists"></a>Egyéni listát hozhat létre és kezelhet

Számos online Közösségben, miután a felhasználók képeket vagy más típusú tartalmakat töltenek fel, a támadó elemek többször is megoszthatók a következő napokon, heteken és hónapokban. A több helyről származó rendszerképek ismételt vizsgálatával és szűrésével kapcsolatos költségek költségesek és hibákkal is rendelkezhetnek.

Ahelyett, hogy ugyanazt a rendszerképet többször is felhasználja, a támadó lemezképeket hozzáadja a blokkolt tartalom egyéni listájához. Így a Content moderációs rendszer összehasonlítja a beérkező képeket az egyéni listára, és leállítja a további feldolgozást.

> [!NOTE]
> A maximális korlát **5 képlista**, amelyek egyenként **nem haladhatják meg a 10 000 képet**.
>

A Content Moderator egy teljes [képlista-felügyeleti API](try-image-list-api.md) -t biztosít az egyéni lemezképek listáinak kezelésére szolgáló műveletekkel. Kezdje a [képlista API-konzollal](try-image-list-api.md) , és használja a REST API kódot. Ha ismeri a Visual studiót és a C#-t, tekintse meg a [rendszerkép-lista .net](image-lists-quickstart-dotnet.md) gyors útmutatóját is.

## <a name="matching-against-your-custom-lists"></a>Az Egyéni listák egyeztetése

Az egyeztetési művelettel a beérkező képeknek a lista műveleteivel létrehozott és felügyelt egyéni listákkal való megkülönböztetése is lehetővé válik.

Ha egyezést talál, a művelet visszaadja az egyező rendszerkép azonosítóját és moderálási címkéit. A válasz a következő információkat tartalmazza:

- Egyezési pontszám (0 és 1 között)
- Egyeztetett rendszerkép
- Képcímkék (az előző moderálás során kiosztva)
- Képfeliratok

Példa kivonatra:

```json
{
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
}
```

## <a name="review-tool"></a>Felülvizsgálati eszköz

Árnyaltabb esetekben a Content Moderator [felülvizsgálati eszközzel](Review-Tool-User-Guide/human-in-the-loop.md) és az API-val felszínre teheti a moderátorok eredményeit és tartalmát az emberi moderátorok felülvizsgálatában. Áttekintik a gép által hozzárendelt címkéket, és megerősítik a végső döntéseket.

![Képek felülvizsgálata emberi moderátorok által](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Következő lépések

Tesztelje a [rendszerkép moderálási API-konzolját](try-image-api.md) , és használja a REST API-kód mintáit. Tekintse meg az [értékeléseket, a munkafolyamatokat és a feladatokat](./review-api.md) , amelyekből megtudhatja, hogyan állíthatja be az emberi felülvizsgálatokat.
