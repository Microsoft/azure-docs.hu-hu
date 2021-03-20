---
title: Translator Átbetűzés metódus
titleSuffix: Azure Cognitive Services
description: Egy adott nyelvű szöveg átalakítása egy parancsfájlból egy másik parancsfájlba a Translator Átbetűzés metódussal.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 0d5a7f8df0c722ca52780ba254e9af9608f26b54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895424"
---
# <a name="translator-30-transliterate"></a>Translator 3,0: Átbetűzés

A szöveget egy adott nyelven alakítja át egy másik parancsfájlba.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `POST` a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
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
  <tr>
    <td>language</td>
    <td>*Kötelező paraméter*.<br/>Megadja az egyik parancsfájlból a másikba konvertálandó szöveg nyelvét. A lehetséges nyelvek a `transliteration` szolgáltatásnak a [támogatott nyelvekre](./v3-0-languages.md)való lekérdezésével a kapott hatókörben vannak felsorolva.</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Kötelező paraméter*.<br/>Megadja a bemeneti szöveg által használt parancsfájlt. A hatókör használatával kereshet [támogatott nyelveket](./v3-0-languages.md) a `transliteration` kiválasztott nyelvhez elérhető bemeneti parancsfájlok kereséséhez.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Kötelező paraméter*.<br/>Megadja a kimeneti parancsfájlt. A hatókör használatával megkeresheti a [támogatott nyelveket](./v3-0-languages.md) `transliteration` , hogy megtalálja a bemeneti nyelv és a bemeneti parancsfájl kiválasztott kombinációjával elérhető kimeneti parancsfájlokat.</td>
  </tr>
</table> 

A kérelem fejlécei a következők:

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Description</th>
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

A kérelem törzse egy JSON-tömb. Minden tömb elem egy nevű JSON `Text` -objektum, amely az átalakítandó karakterláncot jelöli.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 10 elemet tartalmazhat.
* Egy tömb elemének szöveges értéke nem lehet hosszabb 1 000 karakternél, beleértve a szóközöket is.
* A kérelemben szereplő teljes szöveg nem lehet hosszabb 5 000 karakternél, beleértve a szóközöket is.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden eleméhez. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `text`: Egy karakterlánc, amely a bemeneti karakterlánc kimeneti parancsfájlba való átalakításának eredményét eredményezi.
  
  * `script`: A kimenetben használt parancsfájlt megadó karakterlánc.

Példa JSON-válaszra:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>Válaszfejlécek

<table width="100%">
  <th width="20%">Fejlécek</th>
  <th>Description</th>
  <tr>
    <td>X – kérelemazonosító</td>
    <td>A szolgáltatás által a kérelem azonosítására generált érték. Hibaelhárítási célokra szolgál.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Válasz-állapotkódok

A kérelem által visszaadott lehetséges HTTP-állapotkódok a következők: 

<table width="100%">
  <th width="20%">Állapotkód</th>
  <th>Description</th>
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

Ha hiba történik, a kérelem JSON-hibát is jelez. A hibakód egy 6 számjegyből álló szám, amely a 3 számjegyből álló HTTP-állapotkódot kombinálja, majd egy 3 számjegyű számot, amely további kategorizálja a hibát. Gyakori hibakódok a [v3 Translator Reference oldalon](./v3-0-reference.md#errors)találhatók. 

## <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan alakíthat át két japán karakterláncot a római Japánra.

A kérelem JSON-adattartalma a következő példában:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Ha a fürtöket olyan parancssori ablakban használja, amely nem támogatja a Unicode-karaktereket, hajtsa végre a következő JSON-adattartalmat, és mentse egy nevű fájlba `request.txt` . Ügyeljen arra, hogy a fájlt `UTF-8` kódolással mentse.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```