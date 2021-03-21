---
title: Fordítói szótár – példák módszer
titleSuffix: Azure Cognitive Services
description: A fordítói szótár példái példákat mutatnak be arra vonatkozóan, hogy a szótárban hogyan használják a kifejezéseket a kontextusban.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: lajanuar
ms.openlocfilehash: e7f0e106c1ca154dcd54990395430b3e0f6c536f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895509"
---
# <a name="translator-30-dictionary-examples"></a>Translator 3,0: példák a szótárra

Olyan példákat tartalmaz, amelyek bemutatják, hogyan használják a szótárban található kifejezéseket a kontextusban. Ez a művelet a [szótárbeli kereséssel](./v3-0-dictionary-lookup.md)párhuzamosan használatos.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `POST` a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Kérelmek paramétereinek megadása

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

| Lekérdezési paraméter | Description |
| --------- | ----------- |
| api-verzió <img width=200/> | **Kötelező paraméter**.<br/>Az ügyfél által kért API-verzió. Az értéknek a számnak kell lennie `3.0` . |
| a | **Kötelező paraméter**.<br/>Megadja a bemeneti szöveg nyelvét. A forrás nyelvének a hatókörben szereplő [támogatott nyelvek](./v3-0-languages.md) egyikének kell lennie `dictionary` . |
| felhasználóként a(z) | **Kötelező paraméter**.<br/>Megadja a kimeneti szöveg nyelvét. A célként megadott nyelvnek a hatókörben szereplő [támogatott nyelvek](./v3-0-languages.md) egyikének kell lennie `dictionary` .  | 

A kérelem fejlécei a következők:

| Fejlécek  | Description |
| ------ | ----------- |
| Hitelesítési fejléc (ek) <img width=200/>  | **Kötelező kérelem fejléce**<br/>Tekintse <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">meg a hitelesítés elérhető beállításait</a>. |
| Content-Type | **Kötelező kérelem fejléce**<br/>Megadja az adattartalom tartalomtípusát. A lehetséges értékek a következők: `application/json` . |
| Content-Length   | **Kötelező kérelem fejléce**<br/>A kérelem törzsének hossza |
| X – ClientTraceId   | Nem **kötelező**.<br/>Ügyfél által generált GUID a kérelem egyedi azonosításához. Kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban szerepel a nyomkövetési azonosító a nevű lekérdezési paraméter használatával `ClientTraceId` . |

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömb elem egy JSON-objektum, amely a következő tulajdonságokkal rendelkezik:

  * `Text`: A keresett kifejezést megadó karakterlánc. Ennek egy mezőnek kell lennie egy `normalizedText` korábbi [szótárbeli keresési](./v3-0-dictionary-lookup.md) kérelem visszafordításakor. A mező értéke is lehet `normalizedSource` .

  * `Translation`: A [szótár keresési](./v3-0-dictionary-lookup.md) művelete által korábban visszaadott lefordított szöveget megadó karakterlánc. Ennek a `normalizedTarget` `translations` [szótár keresési](./v3-0-dictionary-lookup.md) válasza mezőjében szereplő értéknek kell lennie. A szolgáltatás példákat ad vissza az adott forrás-cél Word-pair számára.

Példa:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 10 elemet tartalmazhat.
* Egy tömb elemének szöveges értéke nem lehet hosszabb 100 karakternél, beleértve a szóközöket is.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden karakterláncához. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `normalizedSource`: Egy karakterlánc, amely a forrás kifejezés normalizált formáját adja. Ez általában azonos a `Text` kérelem törzsében lévő egyező lista index mezőjében szereplő értékkel.
    
  * `normalizedTarget`: Egy karakterlánc, amely a célként megadott kifejezés normalizált formáját adja. Ez általában azonos a `Translation` kérelem törzsében lévő egyező lista index mezőjében szereplő értékkel.
  
  * `examples`: A (forrás kifejezés, a célként megadott kifejezés) párokra vonatkozó példák listája. A lista minden eleme egy olyan objektum, amely a következő tulajdonságokkal rendelkezik:

    * `sourcePrefix`: Az a karakterlánc, amely a teljes példa _megkezdése előtt_ összefűzi az értéket `sourceTerm` . Ne adjon hozzá szóközt, mert már ott van. Ez az érték lehet üres karakterlánc.

    * `sourceTerm`: A tényleges kifejezéssel megegyező sztring. A rendszer hozzáadja a karakterláncot a `sourcePrefix` és a `sourceSuffix` teljes példa formájában. Az értéke el van különítve, így egy felhasználói felületen is megadható, például félkövérrel.

    * `sourceSuffix`: Az a karakterlánc, amelyet a rendszer az érték _után_ ÖSSZEFŰZ a `sourceTerm` teljes példa formájában. Ne adjon hozzá szóközt, mert már ott van. Ez az érték lehet üres karakterlánc.

    * `targetPrefix`: A célhoz hasonló sztring `sourcePrefix` .

    * `targetTerm`: A célhoz hasonló sztring `sourceTerm` .

    * `targetSuffix`: A célhoz hasonló sztring `sourceSuffix` .

    > [!NOTE]
    > Ha a szótárban nincsenek példák, a válasz 200 (OK), de a `examples` lista egy üres lista.

## <a name="examples"></a>Példák

Ez a példa bemutatja, hogyan lehet keresni példákat az angol kifejezésből és a `fly` spanyol fordításból álló párokra `volar` .

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

A válasz törzse (az egyértelműség rövidítése) a következő:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```