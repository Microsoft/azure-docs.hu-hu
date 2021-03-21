---
title: Entitás típusának listázása – LUIS
description: Az entitások listája a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem észlel további értékeket a List entitások számára. Az ajánlott funkció használatával megtekintheti az új szavakra vonatkozó javaslatokat az aktuális lista alapján.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 410b33b5c6078d096fa4b2acaa7b49bc14c95e31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97608272"
---
# <a name="list-entity"></a>Listaentitás

Az entitások listája a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem észlel további értékeket a List entitások számára. Az **ajánlott** funkció használatával megtekintheti az új szavakra vonatkozó javaslatokat az aktuális lista alapján. Ha egynél több azonos értékű listaelem szerepel, a rendszer az összes entitást visszaadja a végponti lekérdezésben.

Egy lista entitás nem gépi megtanult. Ez egy pontos szöveges egyezés. LUIS a válaszban entitásként jelöli meg a listában szereplő elemek bármelyikének egyezését.

**Az entitás jól illeszkedik a szöveges adatként:**

* Ismert készlet.
* Nem változik gyakran. Ha módosítania kell a listát, vagy ha szeretné, hogy a lista kibontható legyen, egy egyszerű entitás jobb választás egy kifejezési listával.
* A készlet nem haladja meg a LUIS maximális [határait](luis-limits.md) ezen entitástípus esetében.
* A kiírásban szereplő szöveg a kis-és nagybetűk megkülönböztetése egy szinonimával vagy a kanonikus névvel. LUIS nem használja az egyezésen felüli listát. A zavaros egyezés, a létrehozás, a többes szám és az egyéb változatok nem oldhatók meg egy listával rendelkező entitással. A változatok kezeléséhez érdemes [mintát](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) használni a választható szöveges szintaxissal.

![entitás listázása](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Példa. JSON a List entitásba való importáláshoz

  A következő. JSON formátum használatával importálhat értékeket egy meglévő listára:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>Példa JSON-válaszra

Tegyük fel, hogy az alkalmazás rendelkezik egy nevű listával, `Cities` amely lehetővé teszi a városi nevek (például a Sea-Tac), a repülőtéri kód (Sea), a irányítószám (98101) és a telefonos körzetszám (206) változatának módosítását.

|Listaelem|Elemek szinonimái|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

Az előző részletben a Word a `paris` lista entitás részeként van leképezve a párizsi elemre `Cities` . A lista entitás megegyezik az elem normalizált nevével és az elem szinonimákkal.

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

Ez a JSON, ha `verbose=false` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Ez a JSON, ha `verbose=true` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

|Adatobjektum|Entitás neve|Érték|
|--|--|--|
|Entitás listázása|`Cities`|`paris`|

## <a name="next-steps"></a>Következő lépések

További információ az entitásokról:

* [Alapelvek](luis-concept-entity-types.md)
* [Létrehozás módja](luis-how-to-add-entities.md)
