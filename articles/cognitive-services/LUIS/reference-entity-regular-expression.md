---
title: Reguláris kifejezés típusú entitás – LUIS
description: A reguláris kifejezés a legjobb a nyers szövegekhez. Figyelmen kívül hagyja az esetet, és figyelmen kívül hagyja a kulturális változatot.  A reguláris kifejezések egyeztetése a karakter szintjén, nem pedig a jogkivonat szintjén történik.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 18e44ec43e1169aa054e6e5b4591ccd8611a7f4d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025225"
---
# <a name="regular-expression-entity"></a>Reguláriskifejezés-entitás

A reguláris kifejezés entitás kinyeri az entitást az Ön által megadott reguláris kifejezési minta alapján.

A reguláris kifejezés a legjobb a nyers szövegekhez. Figyelmen kívül hagyja az esetet, és figyelmen kívül hagyja a kulturális változatot.  A reguláris kifejezések egyeztetése a karakter szintjén, nem pedig a jogkivonat szintjén történik. Ha a reguláris kifejezés túl összetett, például sok szögletes zárójelet használ, nem tudja felvenni a kifejezést a modellbe. A nem az összes [.net regex](/dotnet/standard/base-types/regular-expressions) függvénytár részét használja.

**Az entitás jól illeszkedik, ha:**

* Az adat konzisztens módon formázható minden olyan változattal, amely szintén konzisztens.
* A reguláris kifejezéshez nem szükséges több, mint 2 beágyazási szint.

![Reguláriskifejezés-entitás](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Használati megfontolások

A reguláris kifejezések nem egyeznek meg a vártnál. Ilyen például a és a numerikus szó egyeztetése `one` `two` . Ilyen például a következő regex, amely megfelel a számnak a `one` többi számmal együtt:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Ez a regex kifejezés minden olyan szót tartalmaz, amely ezekkel a számokkal végződik, például: `phone` . A probléma megoldása érdekében győződjön meg arról, hogy a regex-egyezések figyelembe veszik a Word határait. A jelen példában a Word-határok használatára szolgáló regex a következő regexben van használatban:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Példa JSON-ra

Ha a használatakor a `kb[0-9]{6}` reguláris kifejezés entitásának definícióját használja, a következő JSON-válasz egy példa az eredményül kapott reguláris kifejezés entitásokra a lekérdezéshez:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)


Ez a JSON, ha `verbose=false` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Ez a JSON, ha `verbose=true` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Következő lépések

További információ az entitásokról:

* [Alapelvek](luis-concept-entity-types.md)
* [Létrehozás módja](luis-how-to-add-entities.md)