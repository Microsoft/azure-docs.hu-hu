---
title: Adatbányászat – LUIS
description: Adatok kinyerése a teljes szövegből szándékok és entitások alapján. Ismerje meg, hogy milyen típusú adatok nyerhetők ki Language Understandingból (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: e6f01354bb5aa2b78d3c9962bac49be39dd2c81f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025993"
---
# <a name="extract-data-from-utterance-text-with-intents-and-entities"></a>Adatok kinyerése a teljes szövegből szándékok és entitások alapján
A LUIS lehetővé teszi a felhasználók természetes nyelvi hosszúságú kimondott szöveg származó információk beszerzését. Az adatok kinyerése oly módon történik, hogy egy program, egy alkalmazás vagy egy csevegési bot is felhasználja a műveletet. A következő részekben megismerheti, hogy a rendszer milyen adatokhoz adja vissza a leképezéseket és entitásokat a JSON-példákkal.

A kinyerni kívánt legnehezebb adatok a gépi tanulási adatok, mert nem pontos szöveges egyezés. A gépi tanulási [entitások](luis-concept-entity-types.md) kinyerésének a [létrehozási ciklus](luis-concept-app-iteration.md) részét kell képeznie, amíg biztos abban, hogy megkapja a várt adatmennyiséget.

## <a name="data-location-and-key-usage"></a>Adatok helye és kulcshasználat
LUIS a közzétett [végponton](luis-glossary.md#endpoint)kinyeri az adatokból a felhasználó teljes kivonását. A **https-kérés** (post vagy Get) tartalmazza a kiválasztást, valamint néhány opcionális konfigurációt, például átmeneti vagy éles környezeteket.

**V2 előrejelzési végpont kérése**

`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&q=book 2 tickets to paris`

**V3 előrejelzési végpont kérése**

`https://westus.api.cognitive.microsoft.com/luis/v3.0-preview/apps/<appID>/slots/<slot-type>/predict?subscription-key=<subscription-key>&verbose=true&timezoneOffset=0&query=book 2 tickets to paris`

A a `appID` Luis-alkalmazás **Beállítások** lapján, valamint a Luis-alkalmazás szerkesztésekor az URL-cím részeként is elérhető `/apps/` . A az `subscription-key` alkalmazás lekérdezéséhez használt végponti kulcs. Habár a LUIS megismerése közben ingyenes authoring/Starter-kulcsot is használhat, fontos, hogy módosítsa a végpont kulcsát egy olyan kulcsra, amely támogatja a [várt Luis-használatot](luis-limits.md#key-limits). Az `timezoneOffset` egység perc.

A **https-válasz** tartalmazza az összes olyan szándékot és entitási információt, amelyet a Luis az előkészítési vagy a termelési végpont aktuális közzétett modellje alapján meghatároz. A végpont URL-címe a [Luis](luis-reference-regions.md) webhelyén, a **kezelés** szakaszban, a **kulcsok és végpontok** oldalon található.

## <a name="data-from-intents"></a>Adatok a szándékokból
Az elsődleges érték a legfontosabb pontozási **leképezés neve**. A végpont válasza:

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

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * *

|Adatobjektum|Adattípus|Adatok helye|Érték|
|--|--|--|--|
|Szándék|Sztring|topScoringIntent. szándék|"GetStoreInfo"|

Ha a Csevegőrobot vagy a LUIS-Calling alkalmazás egynél több leképezési pontszámon alapuló döntést tesz, adja vissza az összes cél pontszámát.


#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

Állítsa be a querystring paramétert `verbose=true` . A végpont válasza:

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

Állítsa be a querystring paramétert `show-all-intents=true` . A végpont válasza:

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

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * *

A szándékok a legmagasabbtól a legalacsonyabb pontszámig vannak rendezve.

|Adatobjektum|Adattípus|Adatok helye|Érték|Pontszám|
|--|--|--|--|:--|
|Szándék|Sztring|leképezések [0]. szándék|"GetStoreInfo"|0,984749258|
|Szándék|Sztring|leképezések [1]. szándék|NEz egy|0,0168218873|

Ha előre elkészített tartományokat ad hozzá, a cél neve a tartományt jelöli, például a `Utilties` vagy `Communication` a szándékot:

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

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * *

|Tartomány|Adatobjektum|Adattípus|Adatok helye|Érték|
|--|--|--|--|--|
|Segédprogramok|Szándék|Sztring|leképezések [0]. szándék|"<b>Segédprogramok</b>. ShowNext"|
|Kommunikáció|Szándék|Sztring|leképezések [1]. szándék|<b>Kommunikáció</b>. StartOver"|
||Szándék|Sztring|leképezések [2]. szándék|NEz egy|


## <a name="data-from-entities"></a>Entitásokból származó adatok
A legtöbb chat-robotnak és alkalmazásnak nagyobbnak kell lennie a cél nevénél. Ez a kiegészítő, opcionális adatok a kiválasztásban felderített entitásokból származnak. Az egyes entitások különböző adatokat adnak vissza a egyezésről.

Egy kifejezésben szereplő egyetlen szó vagy kifejezés több entitással is megegyező lehet. Ebben az esetben minden egyező entitás a pontszámával lesz visszaadva.

A rendszer az összes entitást visszaadja a végpont válaszának **entitások** tömbje alapján.

## <a name="tokenized-entity-returned"></a>Visszaadott jogkivonat-entitás

Tekintse át a LUIS-beli [jogkivonat-támogatást](luis-language-support.md#tokenization) .


## <a name="prebuilt-entity-data"></a>Előre elkészített entitások
Az előre [elkészített](luis-concept-entity-types.md) entitásokat a rendszer a nyílt forráskódú [felismerők – Text](https://github.com/Microsoft/Recognizers-Text) projekt használatával reguláris kifejezési egyezés alapján deríti fel. Az előre elkészített entitásokat a rendszer az entitások tömbben adja vissza, és a (z) előtagú nevet használja `builtin::` .

## <a name="list-entity-data"></a>Entitások listájának listázása

Az [entitások listája](reference-entity-list.md) a kapcsolódó szavak rögzített, lezárt készletét jelöli a szinonimákkal együtt. A LUIS nem észlel további értékeket a List entitások számára. Az **ajánlott** funkció használatával megtekintheti az új szavakra vonatkozó javaslatokat az aktuális lista alapján. Ha egynél több azonos értékű listaelem szerepel, a rendszer az összes entitást visszaadja a végponti lekérdezésben.

## <a name="regular-expression-entity-data"></a>Reguláris kifejezés típusú entitások

A [reguláris kifejezés entitás](reference-entity-regular-expression.md) az Ön által megadott reguláris kifejezés alapján kigyűjt egy entitást.

## <a name="extracting-names"></a>Nevek kinyerése
A nevek kiírása nehéz, mert a név szinte bármilyen betű és szó kombinációja lehet. Attól függően, hogy milyen típusú nevet szeretne kinyerni, több lehetőség közül választhat. A következő javaslatok nem szabályok, hanem további irányelvek.

### <a name="add-prebuilt-personname-and-geographyv2-entities"></a>Előre elkészített PersonName-és GeographyV2-entitások hozzáadása

A [PersonName](luis-reference-prebuilt-person.md) és a [GeographyV2](luis-reference-prebuilt-geographyV2.md) entitások bizonyos [nyelvi kultúrákban](luis-reference-prebuilt-entities.md)is elérhetők.

### <a name="names-of-people"></a>Személyek nevei

A nyelvtől és a kulturális környezettől függően előfordulhat, hogy a személyek neve némileg eltérő lehet. Használjon egy előre elkészített **[personName](luis-reference-prebuilt-person.md)** -entitást vagy egy olyan **[egyszerű entitást](luis-concept-entity-types.md)** , amely a vezetéknév és az utónév szerepkörrel rendelkezik.

Ha az egyszerű entitást használja, ügyeljen arra, hogy olyan példákat adjon meg, amelyek az utónév és a vezetéknév különböző részeiben használják a hosszúságú kimondott szöveg, és hosszúságú kimondott szöveg az összes szándékot, beleértve a none szándékot is. Rendszeresen [tekintse át](./luis-how-to-review-endpoint-utterances.md) a végpontok hosszúságú kimondott szöveg a nem megfelelően előre jelzett nevek címkézéséhez.

### <a name="names-of-places"></a>Helyek nevei

A helyek nevei be vannak állítva és ismertek, például városok, megyék, Államok, tartományok és országok/régiók. Az előre elkészített entitások **[geographyV2](luis-reference-prebuilt-geographyv2.md)** kinyerheti a hely adatait.

### <a name="new-and-emerging-names"></a>Új és feltörekvő nevek

Néhány alkalmazásnak képesnek kell lennie új és újonnan megjelenő nevek, például termékek vagy vállalatok megtalálására. Az ilyen típusú nevek a legnehezebb kinyerési típus. Kezdje egy **[egyszerű entitással](luis-concept-entity-types.md#simple-entity)** , és adjon hozzá egy [kifejezést tartalmazó listát](luis-concept-feature.md). Rendszeresen [tekintse át](./luis-how-to-review-endpoint-utterances.md) a végpontok hosszúságú kimondott szöveg a nem megfelelően előre jelzett nevek címkézéséhez.

## <a name="patternany-entity-data"></a>Minta. az entitások összes adathalmaza

[Minta.](reference-entity-pattern-any.md) a változó hosszúságú helyőrző csak a minta sablonjának megjelölésére szolgál, amely jelzi, hogy az entitás hol kezdődik és végződik. A mintában használt entitásnak a minta alkalmazásához szükségesnek kell lennie.

## <a name="sentiment-analysis"></a>Hangulatelemzés
Ha a visszajelzések elemzése a [Közzététel](luis-how-to-publish-app.md#sentiment-analysis)során van konfigurálva, a Luis JSON-válasz tartalmazza a hangulat elemzését. További információ az [text Analytics](../text-analytics/index.yml) dokumentációjában található érzelmek elemzéséről.

## <a name="key-phrase-extraction-entity-data"></a>A Key kifejezés kinyerő entitások adathalmaza
A [kulcs kifejezés](luis-reference-prebuilt-keyphrase.md) kinyerő entitása a [text Analytics](../text-analytics/index.yml)által megadott, a teljes szövegben szereplő kulcsfontosságú kifejezéseket adja vissza.

## <a name="data-matching-multiple-entities"></a>Több entitásnak megfelelő adathalmaz

LUIS visszaadja az összes felderített entitást a teljes verzióban. Ennek eredményeképpen előfordulhat, hogy a csevegési robotnak az eredmények alapján kell döntést hoznia.

## <a name="data-matching-multiple-list-entities"></a>Több lista entitások megfeleltetése

Ha egy szó vagy kifejezés több listával egyezik, a végpont lekérdezése az egyes listák entitásokat adja vissza.

A lekérdezéshez `when is the best time to go to red rock?` , és az alkalmazás `red` több listában is szerepel, a Luis felismeri az összes entitást, és az entitások tömbjét adja vissza a JSON-végpont válaszának részeként.

## <a name="next-steps"></a>Következő lépések

Az entitások LUIS-alkalmazáshoz való hozzáadásával kapcsolatos további tudnivalókért tekintse meg az [entitások hozzáadása](luis-how-to-add-entities.md) című témakört.
