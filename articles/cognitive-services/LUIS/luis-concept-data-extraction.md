---
title: Adatok kinyerése – LUIS
description: Adatok kinyerása szándékokkal és entitásokkal kimondott szövegből. Megtudhatja, hogy milyen típusú adatokat lehet kinyerni Language Understanding LUIS-ból.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: dd7d113b1c23a0afec82a346e0f7baa1254ebbed
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500141"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Adatok kinyerása kimondott szövegből szándékokkal és entitásokkal
A LUIS lehetővé teszi, hogy információt lekért egy felhasználó természetes nyelvi kimondott szövegeiből. Az információk úgy vannak kinyerve, hogy egy program, alkalmazás vagy csevegő robot fel tudja használni őket a művelethez. A következő szakaszokból megtudhatja, milyen adatokat ad vissza a szándékok és entitások JSON-példákkal.

A legnehezebb kinyerni a gépi tanulási adatokat, mert nem egyeznek pontosan a szöveggel. A gépi tanulási [](luis-concept-entity-types.md) entitások adatkinyerésének [](luis-concept-app-iteration.md) a szerzői ciklus részét kell képezi, amíg biztos nem lesz abban, hogy megkapja a várt adatokat.

## <a name="data-location-and-key-usage"></a>Adatok helye és kulcshasználat
A LUIS kinyeri az adatokat a felhasználó kimondott szövegből a közzétett [végponton.](luis-glossary.md#endpoint) A **HTTPS-kérés** (POST vagy GET) tartalmazza a kimondott szövegeket, valamint néhány választható konfigurációt, például előkészítési vagy éles környezeteket.

**V2 előrejelzési végpont kérése**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**V3 előrejelzési végpont kérése**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

A a LUIS-alkalmazás Beállítások oldalán, valamint az URL-cím (utána) részeként érhető el a `appID` LUIS-alkalmazás  `/apps/` szerkesztésekor. A `subscription-key` az alkalmazás lekérdezéséhez használt végpontkulcs. Bár a LUIS tanulása közben használhatja az ingyenes szerzői/kezdőkulcsot, fontos, hogy a végpontkulcsot olyan kulcsra módosítsa, amely támogatja a [várt LUIS-használatot.](luis-limits.md#key-limits) Az `timezoneOffset` egység perc.

A **HTTPS-válasz** tartalmazza az összes szándékot és entitásinformációt, amit a LUIS az előkészítési vagy az éles végpont aktuális közzétett modellje alapján meghatározhat. A végpont URL-címe a [LUIS](luis-reference-regions.md)  webhelyén, a Kulcsok és végpontok oldal Kezelés szakaszában **található.**

## <a name="data-from-intents"></a>Szándékok adatai
Az elsődleges adatok a legmagasabb pontszámú **szándék neve.** A végpont válasza a következő:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

```JSON
{
  "query": "when do you open next?",
  "prediction": {
    "normalizedQuery": "when do you open next?",
    "topIntent": "GetStoreInfo",
    "intents": {
        "GetStoreInfo": {
            "score": 0.984749258
        }
    }
  },
  "entities": []
}
```

További információ a [V3 előrejelzési végpontról.](luis-migration-api-v3.md)

* * *

|Adatobjektum|Adattípus|Adatok helye|Érték|
|--|--|--|--|
|Szándék|Sztring|topScoringIntent.intent|"GetStoreInfo"|

Ha a csevegőrobot vagy a LUIS-t hívó alkalmazás egynél több szándékpont alapján hoz döntést, adja vissza a szándékok összes pontszámát.


#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

Állítsa be a querystring paramétert ( `verbose=true` ). A végpont válasza a következő:

```JSON
{
  "query": "when do you open next?",
  "topScoringIntent": {
    "intent": "GetStoreInfo",
    "score": 0.984749258
  },
  "intents": [
    {
      "intent": "GetStoreInfo",
      "score": 0.984749258
    },
    {
      "intent": "None",
      "score": 0.2040639
    }
  ],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

Állítsa be a querystring paramétert ( `show-all-intents=true` ). A végpont válasza a következő:

```JSON
{
    "query": "when do you open next?",
    "prediction": {
        "normalizedQuery": "when do you open next?",
        "topIntent": "GetStoreInfo",
        "intents": {
            "GetStoreInfo": {
                "score": 0.984749258
            },
            "None": {
                 "score": 0.2040639
            }
        },
        "entities": {
        }
    }
}
```

További információ a [V3 előrejelzési végpontról.](luis-migration-api-v3.md)

* * *

A szándékok a legmagasabbtól a legalacsonyabbig vannak megrendelve.

|Adatobjektum|Adattípus|Adatok helye|Érték|Pontszám|
|--|--|--|--|:--|
|Szándék|Sztring|intents[0].intent|"GetStoreInfo"|0.984749258|
|Szándék|Sztring|intents[1].intent|"Nincs"|0.0168218873|

Ha előre összeállított tartományokat ad hozzá, a szándék neve jelzi a tartományt, például vagy `Utilties` `Communication` a szándékot:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

```JSON
{
  "query": "Turn on the lights next monday at 9am",
  "topScoringIntent": {
    "intent": "Utilities.ShowNext",
    "score": 0.07842206
  },
  "intents": [
    {
      "intent": "Utilities.ShowNext",
      "score": 0.07842206
    },
    {
      "intent": "Communication.StartOver",
      "score": 0.0239675418
    },
    {
      "intent": "None",
      "score": 0.0168218873
    }],
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

```JSON
{
    "query": "Turn on the lights next monday at 9am",
    "prediction": {
        "normalizedQuery": "Turn on the lights next monday at 9am",
        "topIntent": "Utilities.ShowNext",
        "intents": {
            "Utilities.ShowNext": {
                "score": 0.07842206
            },
            "Communication.StartOver": {
                "score": 0.0239675418
            },
            "None": {
                "score": 0.00085447653
            }
        },
        "entities": []
    }
}
```

További információ a [V3 előrejelzési végpontról.](luis-migration-api-v3.md)

* * *

|Tartomány|Adatobjektum|Adattípus|Adatok helye|Érték|
|--|--|--|--|--|
|Segédprogramok|Szándék|Sztring|intents[0].intent|"<b>Utilities</b>. ShowNext"|
|Kommunikáció|Szándék|Sztring|intents[1].intent|<b>Kommunikáció</b>: . StartOver"|
||Szándék|Sztring|intents[2].intent|"Nincs"|


## <a name="data-from-entities"></a>Entitások adatai
A legtöbb csevegő robotnak és alkalmazásnak többre van szüksége, mint a szándék neve. Ezek a kiegészítő, választható adatok a kimondott szövegben felderített entitások alapján adatokat tartalmaznak. Minden entitástípus különböző információkat ad vissza az egyezésről.

Egy kimondott szöveg egyetlen szója vagy kifejezése több entitásra is illeszkedhet. Ebben az esetben a visszaadott entitások a pontszámmal együtt térnek vissza.

A végponttól származó válasz **entitástömbje** minden entitást visszaad

## <a name="tokenized-entity-returned"></a>Jogkivonatos entitás visszaadva

Tekintse át [a jogkivonatok támogatását a](luis-language-support.md#tokenization) LUIS-ban.


## <a name="prebuilt-entity-data"></a>Előre összeállított entitásadatok
[Az előre összeállított](luis-concept-entity-types.md) entitások egy reguláriskifejezés-egyezés alapján vannak felderítve a nyílt forráskódú [Recognizers-Text projekt](https://github.com/Microsoft/Recognizers-Text) használatával. Az előre összeállított entitások az entitástömbben vannak visszaadva, és az előtaggal előtagú típusnevet `builtin::` használják.

## <a name="list-entity-data"></a>Entitásadatok listása

[A listaentitások](reference-entity-list.md) a kapcsolódó szavak rögzített, zárt halmazát és azok szinonimáit képviselik. A LUIS nem derít fel további értékeket a listaentitásokhoz. Az Ajánlás **funkcióval** az aktuális lista alapján láthatja az új szavakra vonatkozó javaslatokat. Ha egynél több listaentitás rendelkezik ugyanazokkal az értékekkel, a végpontlekérdezés minden entitást visszaad.

## <a name="regular-expression-entity-data"></a>Reguláriskifejezés-entitás adatai

A [reguláriskifejezés-entitások](reference-entity-regular-expression.md) egy ön által adott reguláris kifejezés alapján kinyernek egy entitást.

## <a name="extracting-names"></a>Nevek kinyerés
A nevek kimondott szövegből való lekért neve nehéz, mert a nevek szinte bármilyen betű- és szókombinációt tartalmaznak. A kibontott név típusától függően több lehetőség közül választhat. Az alábbi javaslatok nem szabályok, hanem további irányelvek.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Előre összeállított PersonName és GeographyV2 entitások hozzáadása

[A PersonName](luis-reference-prebuilt-person.md) és [a GeographyV2](luis-reference-prebuilt-geographyV2.md) entitások bizonyos nyelvi [kulturális szervezetekben érhetők el.](luis-reference-prebuilt-entities.md)

### <a name="names-of-people"></a>Személyek nevei

Az emberek neve a nyelvtől és a kulturális környezettől függően kissé eltérő formátumú lehet. Használhat előre összeállított **[personName](luis-reference-prebuilt-person.md)** entitást vagy egy egyszerű **[entitást](luis-concept-entity-types.md)** utónév és vezetéknév szerepkörökkel.

Ha az egyszerű entitást használja, ügyeljen arra, hogy olyan példákat adjon meg, amelyek az első és a vezetéknevet használják a kimondott szöveg különböző részeiben, különböző hosszúságú kimondott szövegben és az összes szándékban, beleértve a None szándékot is. [Rendszeresen](./luis-how-to-review-endpoint-utterances.md) tekintse át a végponti kimondott szövegeket, hogy felcímkézzen minden olyan nevet, amely nem volt megfelelően előrejelzve.

### <a name="names-of-places"></a>Helyek nevei

A helynevek be vannak állítva és ismertek, például városok, megyék, államok, tartományok és országok/régiók. Helyinformációk kinyerését az **[előre összeállított geographyV2](luis-reference-prebuilt-geographyv2.md)** entitás használatával lehet kinyerni.

### <a name="new-and-emerging-names"></a>Új és új nevek

Egyes alkalmazásoknak új és új neveket kell találnia, például termékeket vagy vállalatokat. Ezek a névtípusok az adatkinyerés legkombétebb típusai. Kezdjen egy **[egyszerű entitással, és](luis-concept-entity-types.md)** adjon hozzá egy [kifejezéslistát.](luis-concept-feature.md) [Rendszeresen](./luis-how-to-review-endpoint-utterances.md) tekintse át a végponti kimondott szövegeket, hogy felcímkézzen minden olyan nevet, amely nem volt megfelelően előrejelzve.

## <a name="patternany-entity-data"></a>Pattern.any entitásadatok

[A Pattern.any](reference-entity-pattern-any.md) egy változó hosszúságú helyőrző, amely csak a minta kimondott sablonjának kimondott szövegében használatos annak megjelöléséhez, hogy hol kezdődik és végződik az entitás. A mintában használt entitást meg kell találni a minta alkalmazásához.

## <a name="sentiment-analysis"></a>Hangulatelemzés
Ha a hangulatelemzés a közzététele során van konfigurálva, [a](luis-how-to-publish-app.md#sentiment-analysis)LUIS JSON-válasza hangulatelemzést is tartalmaz. További információ a hangulatelemzésről [a](../text-analytics/index.yml) Text Analytics dokumentációjában.

## <a name="key-phrase-extraction-entity-data"></a>Kulcskifejezések kinyerése entitásadatok
A [kulcsszókinyerési entitás](luis-reference-prebuilt-keyphrase.md) kulcskifejezéseket ad vissza a kimondott szövegben, amelyet az [Text Analytics.](../text-analytics/index.yml)

## <a name="data-matching-multiple-entities"></a>Több entitásnak megfelelő adatok

A LUIS visszaadja a kimondott szövegben felderített összes entitást. Emiatt előfordulhat, hogy a csevegő robotnak az eredmények alapján kell döntést hoznia.

## <a name="data-matching-multiple-list-entities"></a>Több listaentitásnak megfelelő adatok

Ha egy szó vagy kifejezés több listaentitásra is illeszkedik, a végpontlekérdezés minden Lista entitást visszaad.

A lekérdezés esetében, és az alkalmazás több listában is tartalmazza a szót, a LUIS felismeri az összes entitást, és visszaadja az entitások tömböt a `when is the best time to go to red rock?` `red` JSON-végpont válaszának részeként.

## <a name="next-steps"></a>Következő lépések

További [információ arról,](luis-how-to-add-entities.md) hogyan adhat entitásokat a LUIS-alkalmazáshoz az Entitások hozzáadása.
