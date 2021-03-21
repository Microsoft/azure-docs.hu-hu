---
title: Helytelenül beírt szavak – LUIS
titleSuffix: Azure Cognitive Services
description: Javítsa a hibásan írt szavakat a hosszúságú kimondott szöveg-ben a Bing Spell Check API v7 és LUIS végponti lekérdezések hozzáadásával.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: 509d1dc0b94bdfa9be5185df0bad793f7702eb26
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731034"
---
# <a name="correct-misspelled-words-with-bing-resource"></a>Hibásan írt szavak kijavítása a Bing-erőforrással

A v3 előrejelzési API mostantól támogatja a [Bing helyesírási API](/bing/search-apis/bing-spell-check/overview)-t. Adja hozzá a helyesírás-ellenőrzést az alkalmazáshoz úgy, hogy a kérések fejlécében a Bing keresési erőforrás kulcsát is tartalmazza. Meglévő Bing-erőforrást is használhat, ha már rendelkezik ilyennel, vagy [létrehozhat egy újat](https://portal.azure.com/#create/Microsoft.BingSearch) a funkció használatához. 

Egy hibásan írt lekérdezésre vonatkozó előrejelzési kimeneti példa:

```json
{
  "query": "bouk me a fliht to kayro",
  "prediction": {
    "alteredQuery": "book me a flight to cairo",
    "topIntent": "book a flight",
    "intents": {
      "book a flight": {
        "score": 0.9480589
      }
      "None": {
        "score": 0.0332136229
      }
    },
    "entities": {}
  }
}
```

A helyesírási javítások a LUIS-felhasználó teljes előrejelzése előtt történnek. A válaszban megtekintheti az eredeti szöveg összes módosítását, beleértve a helyesírást is.

## <a name="create-bing-search-resource"></a>Bing Search erőforrás létrehozása

Ha Bing Search erőforrást szeretne létrehozni a Azure Portalban, kövesse az alábbi utasításokat:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).

2. Válassza az **erőforrás létrehozása** lehetőséget a bal felső sarokban.

3. A keresőmezőbe írja be `Bing Search V7` és válassza ki a szolgáltatást.

4. Egy információs panel jelenik meg a jogot tartalmazó információkkal, beleértve a jogi értesítést is. Válassza a **Létrehozás** lehetőséget az előfizetés-létrehozási folyamat megkezdéséhez.

> [!div class="mx-imgBorder"]
> ![Bing Spell Check API v7-erőforrás](./media/luis-tutorial-bing-spellcheck/bing-search-resource-portal.png)

5. A következő panelen adja meg a szolgáltatás beállításait. Várjon, amíg a szolgáltatás-létrehozási folyamat befejeződik.

6. Az erőforrás létrehozása után nyissa meg a bal oldali **kulcsok és végpont** panelt. 

7. Másolja be az egyik kulcsot, amelyet fel szeretne venni az előrejelzési kérelem fejlécébe. Csak a két kulcs egyikére lesz szüksége.

<!--
## Using the key in LUIS test panel
There are two places in LUIS to use the key. The first is in the [test panel](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel). The key isn't saved into LUIS but instead is a session variable. You need to set the key every time you want the test panel to apply the Bing Spell Check API v7 service to the utterance. See [instructions](luis-interactive-test.md#view-bing-spell-check-corrections-in-test-panel) in the test panel for setting the key.
-->
## <a name="enable-spell-check-from-ui"></a>Helyesírás-ellenőrzés engedélyezése a felhasználói felületen 
A példaként szolgáló lekérdezéshez a [Luis portálon](https://www.luis.ai)engedélyezheti a helyesírást. Válassza a **kezelés** lehetőséget a képernyő tetején, valamint az **Azure-erőforrásokat** a bal oldali navigációs sávon. Miután hozzárendelt egy előrejelzési erőforrást az alkalmazáshoz, az oldal alján található **lekérdezési paraméterek módosítása** lehetőségre kattintva beillesztheti az erőforrás-kulcsot a **Helyesírás-ellenőrzés engedélyezése** mezőbe.
    
   > [!div class="mx-imgBorder"]
   > ![Helyesírás-ellenőrzés engedélyezése](./media/luis-tutorial-bing-spellcheck/spellcheck-query-params.png)


## <a name="adding-the-key-to-the-endpoint-url"></a>A kulcs hozzáadása a végpont URL-címéhez
Minden olyan lekérdezésnél, amelynél helyesírás-módosítást kíván alkalmazni, a végponti lekérdezéshez a lekérdezési fejléc paraméterében átadott Bing helyesírás-ellenőrzési erőforrás kulcsa szükséges. Lehet, hogy van egy Csevegőrobot, amely meghívja a LUIS-t, vagy meghívhatja közvetlenül a LUIS Endpoint API-t. Függetlenül attól, hogy a végpont hogyan legyen meghívva, minden egyes hívásnak tartalmaznia kell a szükséges információkat a fejléc kérésében a helyesírás-helyesbítések megfelelő működéséhez. A **MKT-Bing-Spell-Check-Key** értékkel kell megadnia a kulcs értékét.

|Fejléc kulcsa|Fejléc értéke|
|--|--|
|`mkt-bing-spell-check-key`|Az erőforrás **kulcsok és végpont** paneljén található kulcsok|

## <a name="send-misspelled-utterance-to-luis"></a>Hibásan írt kifejezés elküldése LUIS-re
1. Adjon hozzá egy hibásan írt kiírást az előrejelzési lekérdezésben, amely a következőt küldi el: "milyen messze van a mountainn?". Az angol nyelvben `mountain` a, az egyik `n` , a helyes helyesírás.

2. LUIS egy JSON-eredménnyel válaszol `How far is the mountain?` . Ha Bing Spell Check API v7 hibát észlel, akkor a `query` Luis alkalmazás JSON-válaszában szereplő mező tartalmazza az eredeti lekérdezést, és a `alteredQuery` mező tartalmazza a Luis-nak küldött javított lekérdezést.

```json
{
  "query": "How far is the mountainn?",
  "alteredQuery": "How far is the mountain?",
  "topScoringIntent": {
    "intent": "Concierge",
    "score": 0.183866
  },
  "entities": []
}
```

## <a name="ignore-spelling-mistakes"></a>Helyesírási hibák figyelmen kívül hagyása

Ha nem szeretné használni a Bing Search API v7 szolgáltatást, hozzá kell adnia a helyes és helytelen helyesírást.

Két megoldás a következők:

* Címkézheti például az összes különböző helyesírású hosszúságú kimondott szöveg, hogy LUIS el tudja sajátítani a megfelelő helyesírást és elírásokat. Ez a beállítás több címkézési erőfeszítést igényel, mint a spell-ellenőrző használata.
* Hozzon létre egy kifejezés listát a szó összes változatával. Ezzel a megoldással nem kell megcímkézni a példában szereplő hosszúságú kimondott szöveg.


> [!div class="nextstepaction"]
> [További információ a példa hosszúságú kimondott szöveg](./luis-how-to-add-entities.md)