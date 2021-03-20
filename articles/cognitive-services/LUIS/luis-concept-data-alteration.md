---
title: Adatváltozás – LUIS
description: Megtudhatja, hogyan változtathatók meg az adatváltozások a Language Understanding (LUIS) előrejelzések előtt
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 621a41f743b751a8c24bf6f6ad8497fb5c79775c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026010"
---
# <a name="alter-utterance-data-before-or-during-prediction"></a>A kiírási adatértékek módosítása az előrejelzés előtt vagy közben
LUIS lehetővé teszi a Kimondás kezelését a jóslat előtt vagy közben. Ezek közé tartozik a [helyesírás javítása](luis-tutorial-bing-spellcheck.md), valamint az előre elkészített [datetimeV2](luis-reference-prebuilt-datetimev2.md)időzóna-problémáinak javítása.

## <a name="correct-spelling-errors-in-utterance"></a>Helyesírási hibák javítása a teljes szövegben


### <a name="v3-runtime"></a>V3 futtatókörnyezet

Szöveg előfeldolgozása helyesírás-helyesbítésekhez, mielőtt elküldi a kiírást a LUIS-nek. Használjon például hosszúságú kimondott szöveg a megfelelő helyesírással, így biztosítva a megfelelő előrejelzések megszerzését.

A [Bing Spell Check](../bing-spell-check/overview.md) használatával javítsa a szöveget, mielőtt ELKÜLDENÉ a Luis-nek.

### <a name="prior-to-v3-runtime"></a>A v3 futtatókörnyezet előtt

LUIS [Bing Spell Check API v7](../Bing-Spell-Check/overview.md) használatával javítsa ki a helyesírási hibákat a teljes szövegben. A LUIS-nek szüksége van a szolgáltatáshoz társított kulcsra. Hozza létre a kulcsot, majd adja hozzá a kulcsot querystring paraméterként a [végpontnál](https://go.microsoft.com/fwlink/?linkid=2092356).

A végpontnak két paramétert kell megadnia a helyesírási javítások működéséhez:

|Param|Érték|
|--|--|
|`spellCheck`|boolean|
|`bing-spell-check-subscription-key`|[Bing Spell Check API v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) -végpont kulcsa|

Ha [Bing Spell Check API v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) hibát észlel, az eredeti kimaradás és a korrigált Kimondás a végponton lévő előrejelzések mellett történik.

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)

```JSON
{
    "query": "Book a flite to London?",
    "prediction": {
        "normalizedQuery": "book a flight to london?",
        "topIntent": "BookFlight",
        "intents": {
            "BookFlight": {
                "score": 0.780123
            }
        },
        "entities": {},
    }
}
```

* * *

### <a name="list-of-allowed-words"></a>Engedélyezett szavak listája
A LUIS-ben használt Bing Spell Check API nem támogatja a helyesírás-ellenőrzési változtatások során figyelmen kívül hagyó szavak listáját. Ha engedélyezni szeretné a szavak vagy rövidítések listáját, akkor a Kimondás előtt dolgozza fel a kilépést az ügyfélalkalmazás számára, mielőtt elküldi a megfogalmazást a LUIS for szándék előrejelzésére.

## <a name="change-time-zone-of-prebuilt-datetimev2-entity"></a>Előre elkészített datetimeV2 entitás időzónájának módosítása
Ha egy LUIS-alkalmazás az előre elkészített [datetimeV2](luis-reference-prebuilt-datetimev2.md) entitást használja, az előrejelzési válaszban egy datetime érték adható vissza. A kérelem időzónája a helyes datetime érték meghatározására szolgál. Ha a kérelem egy robotból vagy egy másik központosított alkalmazásból érkezik, mielőtt elkezdené a LUIS-t, javítsa ki az időzóna LUIS használatát.

### <a name="v3-prediction-api-to-alter-timezone"></a>V3 előrejelzési API az időzóna módosításához

A v3-as verzióban a `datetimeReference` meghatározza az időzóna eltolását. További információ a [v3-előrejelzések](luis-migration-api-v3.md#v3-post-body)használatáról.

### <a name="v2-prediction-api-to-alter-timezone"></a>V2 előrejelzési API az időzóna módosításához
Az időzóna kijavítása úgy történik, hogy a felhasználó időzónáját hozzáadja a végponthoz az `timezoneOffset` API-verzió alapján a paraméter használatával. A paraméter értékének az idő módosításához a pozitív vagy negatív számnak kell lennie percben.

#### <a name="v2-prediction-daylight-savings-example"></a>V2 előrejelzési nyári megtakarítások – példa
Ha a visszaadott előre elkészített datetimeV2 kell beállítani a nyári időszámításhoz, akkor a querystring paramétert a [végpont](https://go.microsoft.com/fwlink/?linkid=2092356) lekérdezéséhez percben kell használni.

60 perc hozzáadása:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

60 perc eltávolítása:

`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q=Turn the lights on?timezoneOffset=-60&verbose={boolean}&spellCheck={boolean}&staging={boolean}&bing-spell-check-subscription-key={string}&log={boolean}`

#### <a name="v2-prediction-c-code-determines-correct-value-of-parameter"></a>A v2 előrejelzés C#-kódja meghatározza a paraméter helyes értékét

A következő C#-kód a [TimeZoneInfo](/dotnet/api/system.timezoneinfo) osztály [FindSystemTimeZoneById](/dotnet/api/system.timezoneinfo.findsystemtimezonebyid#examples) metódusát használja a megfelelő eltolási érték meghatározásához a rendszeridő alapján:

```csharp
// Get CST zone id
TimeZoneInfo targetZone = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time");

// Get local machine's value of Now
DateTime utcDatetime = DateTime.UtcNow;

// Get Central Standard Time value of Now
DateTime cstDatetime = TimeZoneInfo.ConvertTimeFromUtc(utcDatetime, targetZone);

// Find timezoneOffset/datetimeReference
int offset = (int)((cstDatetime - utcDatetime).TotalMinutes);
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Helyesírási hibák javítása ezzel az Oktatóanyaggal](luis-tutorial-bing-spellcheck.md)