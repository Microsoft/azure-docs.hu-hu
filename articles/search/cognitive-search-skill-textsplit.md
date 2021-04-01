---
title: Szöveg felosztása kognitív képességgel
titleSuffix: Azure Cognitive Search
description: Szöveg felosztása darabokra vagy szöveges lapokra az Azure Cognitive Search AI-dúsítási folyamatának hossza alapján.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 52aaeb01fef551eee350c6db662c2690ef7b3e78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "84981948"
---
# <a name="text-split-cognitive-skill"></a>Szöveg felosztása kognitív képességgel

A **szöveg felosztása** készséggel szövegrészek szövegét bontja. Megadhatja, hogy a szöveget mondatokra vagy adott hosszúságú lapokra kívánja-e bontani. Ez a képesség különösen akkor hasznos, ha az egyéb szakismeretekben legfeljebb egy szöveges hosszra vonatkozó követelmény van. 

> [!NOTE]
> Ez a képesség nem kötődik Cognitive Services API-hoz, és nem kell fizetnie a használatért. Továbbra is [csatlakoztatnia kell egy Cognitive Services-erőforrást](cognitive-search-attach-cognitive-services.md), hogy felülírja az **ingyenes** erőforrás-beállítást, amely naponta csak kis mennyiségű napi dúsítást korlátozza.

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. SplitSkill 

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| `textSplitMode`    | Vagy "Pages" vagy "mondatok" | 
| `maximumPageLength` | Ha a textSplitMode "Pages" (lapok) értékre van állítva, ez az oldal maximális hosszára vonatkozik `String.Length` . A minimális érték 300.  Ha a textSplitMode "Pages" értékre van állítva, az algoritmus megpróbálja felosztani a szöveget a legtöbb "maximumPageLength" méretű darabokra. Ebben az esetben az algoritmus elvégzi a legjobbat, hogy a mondatot egy mondat határán megtörje, így a tömb mérete valamivel kisebb lehet, mint a "maximumPageLength". | 
| `defaultLanguageCode` | választható A következő nyelvi kódok egyike: `da, de, en, es, fi, fr, it, ko, pt` . Az alapértelmezett érték az angol (en). Néhány megfontolandó szempont:<ul><li>Ha languagecode-országhívószám formátumot továbbít, a rendszer csak a formátum languagecode-részét használja.</li><li>Ha a nyelv nem szerepel az előző listán, a felosztott képesség megszakítja a szöveget a karakterek határain.</li><li>A nyelvi kód megadásával elkerülhető, hogy a szó ne legyen több, mint például a kínai, a japán és a koreai nyelv.</li><li>Ha nem ismeri a nyelvet (azaz a szöveget a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)való bevitelre kell bontania), az alapértelmezett angol (en) értéknek elegendőnek kell lennie. </li></ul>  |


## <a name="skill-inputs"></a>Szaktudás bemenetei

| Paraméter neve       | Leírás      |
|----------------------|------------------|
| `text`    | Az alsztringbe feldarabolt szöveg |
| `languageCode`    | Választható A dokumentumhoz tartozó nyelvi kód. Ha nem ismeri a nyelvet (azaz a szöveget fel kell osztania a [LanguageDetectionSkill](cognitive-search-skill-language-detection.md)), akkor a rendszer biztonságosan eltávolítja a bemenetet.  |

## <a name="skill-outputs"></a>Szaktudás kimenetei 

| Paraméter neve     | Leírás |
|--------------------|-------------|
| `textItems`   | A kinyert alsztringek tömbje. |


##  <a name="sample-definition"></a>Minta definíciója

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "text",
            "source": "/document/content"
        },
        {
            "name": "languageCode",
            "source": "/document/language"
        }
    ],
    "outputs": [
        {
            "name": "textItems",
            "targetName": "mypages"
        }
    ]
}
```

##  <a name="sample-input"></a>Mintabemenet

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>Kimenetpélda

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>Hibák esetei
Ha a nyelv nem támogatott, a rendszer figyelmeztetést generál, és a szöveg a karakter határain belülre van bontva.

## <a name="see-also"></a>Lásd még

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
