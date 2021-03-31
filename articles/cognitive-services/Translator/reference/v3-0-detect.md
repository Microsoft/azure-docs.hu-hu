---
title: Fordítói észlelési módszer
titleSuffix: Azure Cognitive Services
description: Azonosítsa egy szöveg nyelvét az Azure Cognitive Services Translator Detect metódussal.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: cb6660585b5f2b9ab56eaf863f1ec431e5e85109
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895526"
---
# <a name="translator-30-detect"></a>Translator 3,0: észlelés

Egy szöveg nyelvét azonosítja.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `POST` a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>Kérelmek paramétereinek megadása

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

<table width="100%">
  <th width="20%">Lekérdezési paraméter</th>
  <th>Leírás</th>
  <tr>
    <td>api-verzió</td>
    <td>*Kötelező paraméter*.<br/>Az ügyfél által kért API-verzió. Az értéknek a számnak kell lennie `3.0` .</td>
  </tr>
</table> 

A kérelem fejlécei a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>Hitelesítési fejléc (ek)</td>
    <td><em>Kötelező kérelem fejléce</em><br/>Tekintse <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">meg a hitelesítés elérhető beállításait</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Kötelező kérelem fejléce*<br/>Megadja az adattartalom tartalomtípusát. A lehetséges értékek a következők: `application/json` .</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Kötelező kérelem fejléce*<br/>A kérelem törzsének hossza</td>
  </tr>
  <tr>
    <td>X – ClientTraceId</td>
    <td>Nem *kötelező*.<br/>Ügyfél által generált GUID a kérelem egyedi azonosításához. Vegye figyelembe, hogy kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban szerepel a nyomkövetési azonosító a nevű lekérdezési paraméter használatával `ClientTraceId` .</td>
  </tr>
</table> 

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömb elem egy nevű JSON-objektum `Text` . A nyelvfelismerés a tulajdonság értékére lesz alkalmazva `Text` . A nyelv automatikus észlelése hatékonyabban működik a hosszú bemeneti szöveggel. A mintául szolgáló kérelem törzse így néz ki:

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 100 elemet tartalmazhat.
* A kérelemben szereplő teljes szöveg nem lehet hosszabb 50 000 karakternél, beleértve a szóközöket is.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden karakterláncához. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `language`: Az észlelt nyelv kódja.

  * `score`: Egy lebegőpontos érték, amely az eredmény megbízhatóságát jelzi. A pontszám nulla és egy, az alacsony pontszám pedig alacsony megbízhatóságot jelez.

  * `isTranslationSupported`: Logikai érték, amely akkor igaz, ha az észlelt nyelv a szöveges fordításhoz támogatott nyelvek egyike.

  * `isTransliterationSupported`: Egy logikai érték, amely akkor igaz, ha az észlelt nyelv az írásos nyelvek egyike.
  
  * `alternatives`: Más lehetséges nyelvek tömbje. A tömb minden eleme egy másik objektum, amely ugyanazokat a tulajdonságokat tartalmazza: `language` , `score` `isTranslationSupported` és `isTransliterationSupported` .

Példa JSON-válaszra:

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Leírás</th>
  <tr>
    <td>X – kérelemazonosító</td>
    <td>A szolgáltatás által a kérelem azonosítására generált érték. Hibaelhárítási célokra szolgál.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők: 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Leírás</th>
  <tr>
    <td>200</td>
    <td>Sikeres művelet.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>A lekérdezési paraméterek egyike hiányzik vagy érvénytelen. Az újrapróbálkozás előtt javítsa a kérelmek paramétereit.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>A kérést nem lehetett hitelesíteni. Győződjön meg arról, hogy a hitelesítő adatok meg vannak adva és érvényesek.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>A kérés nincs engedélyezve. Olvassa el a részletek hibaüzenetét. Ez gyakran azt jelzi, hogy a próbaverziós előfizetéssel biztosított összes ingyenes fordítás fel lett használva.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>A kiszolgáló elutasította a kérelmet, mert az ügyfél túllépte a kérelmek korlátait.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Váratlan hiba történt. Ha a hiba továbbra is fennáll, jelentse a következőt: a hiba dátuma és időpontja, a kérelem azonosítója a válasz fejlécből `X-RequestId` és az ügyfél azonosítója a kérelem fejlécében `X-ClientTraceId` .</td>
  </tr>
  <tr>
    <td>503</td>
    <td>A kiszolgáló átmenetileg nem érhető el. Próbálja megismételni a kérelmet. Ha a hiba továbbra is fennáll, jelentse a következőt: a hiba dátuma és időpontja, a kérelem azonosítója a válasz fejlécből `X-RequestId` és az ügyfél azonosítója a kérelem fejlécében `X-ClientTraceId` .</td>
  </tr>
</table> 

Ha hiba történik, a kérés JSON-hibaüzenetet is ad vissza. A hibakód egy 6 számjegyből álló szám, amely a 3 számjegyből álló HTTP-állapotkódot kombinálja, majd egy 3 számjegyű számot, amely további kategorizálja a hibát. Gyakori hibakódok a [v3 Translator Reference oldalon](./v3-0-reference.md#errors)találhatók. 

## <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan kérheti le a szöveg fordításához támogatott nyelveket.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```