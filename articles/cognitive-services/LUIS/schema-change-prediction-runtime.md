---
title: Alkalmazás kiterjesztése futásidőben – LUIS
description: Ismerje meg, hogyan terjeszthető ki egy már közzétett előrejelzési végpont az új információk továbbítására.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 1d94e9f59062e4d730b8f3b71022442e81e6eeda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98953021"
---
# <a name="extend-app-at-prediction-runtime"></a>Alkalmazás kiterjesztése előrejelzési futtatókörnyezetben

Az alkalmazás sémája (modelljei és funkciói) ki van tanítva, és közzé van téve az előrejelzési végponton. Ez a közzétett modell az előrejelzési futtatókörnyezetben használatos. Az előrejelzési futtatókörnyezethez új információkat adhat meg, valamint a felhasználó teljes kifejezését is.

Az előrejelzési futtatókörnyezet két sémájának módosítása többek között:
* [Külső entitások](#external-entities)
* [Dinamikus felsorolások](#dynamic-lists)

<a name="external-entities-passed-in-at-prediction-time"></a>

## <a name="external-entities"></a>Külső entitások

A külső entitások lehetővé teszi, hogy a LUIS-alkalmazás képes legyen az entitások azonosítására és címkézésére a futtatókörnyezet során, amely funkciókként használható a meglévő entitásokhoz. Ez lehetővé teszi a saját különálló és egyéni entitás-kinyerő használatát, mielőtt lekérdezést küld az előrejelzési végpontnak. Mivel ez a lekérdezés-előrejelzési végponton történik, nincs szükség a modell újratanítására és közzétételére.

Az ügyfélalkalmazás saját entitást biztosít az entitások megfeleltetésével, és meghatározza az egyező entitások kiválasztásának helyét, majd elküldi ezeket az adatokat a kérelemmel.

A külső entitások az entitások típusának kibővítésének mechanizmusa, miközben más modellek számára is jelekként használatosak.

Ez olyan entitások esetében hasznos, amelyek csak a lekérdezés-előrejelzési futtatókörnyezetben érhetők el. Ilyen típusú adattípusok például folyamatosan változnak az adathalmazok vagy felhasználónként. A LUIS Contact entitást külső információkkal bővítheti egy felhasználó névjegyalbuma alapján.

A külső entitások a v3 authoring API részét képezik. További információ az [áttelepítésről](luis-migration-api-v3.md) erre a verzióra.

### <a name="entity-already-exists-in-app"></a>Az entitás már létezik az alkalmazásban

A kérelem elküldésekor a `entityName` betanított és a közzétett alkalmazásban már léteznie kell a külső entitás értéke, amelyet a rendszer a végponti kérelem post törzsében átadott. Az entitás típusa nem számít, az összes típus támogatott.

### <a name="first-turn-in-conversation"></a>A beszélgetés első bekapcsolása

Vegye fontolóra egy csevegési bot-beszélgetés első kitöltését, ahol a felhasználó a következő hiányos adatokat írja be:

`Send Hazem a new message`

A csevegési robottól a LUIS-re irányuló kérés a POST törzsben található információkkal is átadható, `Hazem` így közvetlenül a felhasználó névjegyeinek felel meg.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Az előrejelzési válasz tartalmazza azt a külső entitást, amely az összes többi előre jelzett entitáshoz tartozik, mert a kérelemben van definiálva.

### <a name="second-turn-in-conversation"></a>Második bekapcsolási beszélgetés

A csevegési robot következő felhasználójának kimondása homályosan működik:

`Send him a calendar reminder for the party.`

A beszélgetés ezen részében a Kimondás a következőre `him` hivatkozik: `Hazem` . A POST szövegtörzsben lévő beszélgetési csevegési robot a `him` kinyert entitás értékére képezhető le `Hazem` .

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Az előrejelzési válasz tartalmazza azt a külső entitást, amely az összes többi előre jelzett entitáshoz tartozik, mert a kérelemben van definiálva.

### <a name="override-existing-model-predictions"></a>Meglévő modell-előrejelzések felülbírálása

A `preferExternalEntities` Beállítások tulajdonság azt adja meg, hogy ha a felhasználó olyan külső entitást küld, amely átfedésben van egy azonos nevű előre jelzett entitással, a Luis kiválasztja az átadott entitást vagy a modellben meglévő entitást.

Vegyük például a lekérdezést `today I'm free` . A LUIS a `today` következő választ datetimeV2 észleli:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Ha a felhasználó a külső entitást küldi el:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Ha a értéke `preferExternalEntities` , a `false` Luis egy választ ad vissza, mintha a külső entitás nem lett elküldve.

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Ha a értéke `preferExternalEntities` , a `true` Luis egy választ ad vissza, amely a következőket tartalmazza:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Feloldás

A _választható_ `resolution` tulajdonság visszaadja az előrejelzési választ, amely lehetővé teszi a külső entitáshoz társított metaadatok átadását, majd visszaküldi a válaszban.

Az elsődleges cél az előre elkészített entitások kiterjesztése, de nem korlátozódik az adott entitás típusára.

A `resolution` tulajdonság lehet szám, karakterlánc, objektum vagy tömb:

* Dallas
* {"text": "value"}
* 12345
* ["a", "b", "c"]

<a name="dynamic-lists-passed-in-at-prediction-time"></a>

## <a name="dynamic-lists"></a>Dinamikus felsorolások

A dinamikus listák segítségével kiterjesztheti a már meglévő betanított és közzétett lista entitást, amely már a LUIS-alkalmazásban van.

Akkor használja ezt a funkciót, ha a lista entitás értékeit időnként módosítani kell. Ez a funkció lehetővé teszi egy már betanított és közzétett lista entitás kiterjesztését:

* A lekérdezés-előrejelzési végpont kérelmének időpontjában.
* Egyetlen kérelem esetén.

A List entitás lehet üres a LUIS-alkalmazásban, de léteznie kell. A LUIS-alkalmazás listázási entitása nem változik, de a végponton megjelenő előrejelzési képesség kiterjeszthető úgy, hogy legfeljebb 2 listát tartalmazzon a 1 000 elemekkel.

### <a name="dynamic-list-json-request-body"></a>Dinamikus lista JSON-kérelmének törzse

A következő JSON-törzsbe való küldéssel adjon hozzá egy új, szinonimákkal ellátott allistát a listához, és Tippelje meg a szöveg lista entitását, `LUIS` a `POST` lekérdezés-előrejelzési kérelemmel együtt:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntity*":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Az előrejelzési válasz tartalmazza a List entitást az összes többi előre jelzett entitással együtt, mert a kérelemben van definiálva.

## <a name="next-steps"></a>Következő lépések

* [Előrejelzési pontszám](luis-concept-prediction-score.md)
* [Az API v3-változások készítése](luis-migration-api-v3.md)
