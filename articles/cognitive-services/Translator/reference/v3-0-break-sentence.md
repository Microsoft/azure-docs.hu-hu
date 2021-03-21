---
title: Translator BreakSentence metódus
titleSuffix: Azure Cognitive Services
description: A Translator BreakSentence metódus azonosítja a mondatok határait egy szövegben.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/06/2020
ms.author: lajanuar
ms.openlocfilehash: 2da614fe829d0aa82bfa57337baf44491993c68f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98895543"
---
# <a name="translator-30-breaksentence"></a>Translator 3,0: BreakSentence

Meghatározza a mondatok szegélyének elhelyezését egy szövegben.

## <a name="request-url"></a>URL-cím kérése

Kérelem küldése `POST` a következőnek:

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>Kérelmek paramétereinek megadása

A lekérdezési karakterláncon átadott kérési paraméterek a következők:

| Lekérdezési paraméter | Description |
| -------| ----------- |
| api-verzió <img width=200/>   | **Szükséges lekérdezési paraméter**.<br/>Az ügyfél által kért API-verzió. Az értéknek a számnak kell lennie `3.0` . |
| language | **Opcionális lekérdezési paraméter**.<br/>A szövegbeviteli szöveg nyelvét azonosító nyelvi címke. Ha nincs megadva kód, az automatikus nyelvfelismerés lesz alkalmazva. |
| parancsfájl    | **Opcionális lekérdezési paraméter**.<br/>A bemeneti szöveg által használt parancsfájlt azonosító szkript címkéje. Ha nincs megadva parancsfájl, a rendszer a nyelv alapértelmezett parancsfájlját fogja feltételezni.  | 

A kérelem fejlécei a következők:

| Fejlécek | Description |
| ------- | ----------- |
| Hitelesítési fejléc (ek) <img width=200/>  | **Kötelező kérelem fejléce**<br/>Tekintse <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">meg a hitelesítés elérhető beállításait</a>. |
| Content-Type | **Kötelező kérelem fejléce**<br/>Megadja az adattartalom tartalomtípusát. A lehetséges értékek a következők: `application/json` . |
| Content-Length    | **Kötelező kérelem fejléce**<br/>A kérelem törzsének hossza  | 
| X – ClientTraceId   | Nem **kötelező**.<br/>Ügyfél által generált GUID a kérelem egyedi azonosításához. Vegye figyelembe, hogy kihagyhatja ezt a fejlécet, ha a lekérdezési karakterláncban szerepel a nyomkövetési azonosító a nevű lekérdezési paraméter használatával `ClientTraceId` .  | 

## <a name="request-body"></a>A kérés törzse

A kérelem törzse egy JSON-tömb. Minden tömb elem egy nevű JSON-objektum `Text` . A rendszer kiszámítja a mondatok határait a tulajdonság értékeként `Text` . A minta kérések törzse egy szöveggel így néz ki:

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

Az alábbi korlátozások érvényesek:

* A tömb legfeljebb 100 elemet tartalmazhat.
* Egy tömb elemének szöveges értéke nem lehet hosszabb 50 000 karakternél, beleértve a szóközöket is.
* A kérelemben szereplő teljes szöveg nem lehet hosszabb 50 000 karakternél, beleértve a szóközöket is.
* Ha a `language` lekérdezési paraméter meg van adva, akkor az összes tömb elemnek ugyanabban a nyelven kell lennie. Ellenkező esetben a rendszer minden egyes tömb elemhez külön alkalmazza a nyelv automatikus észlelését.

## <a name="response-body"></a>Választörzs

A sikeres válasz egy JSON-tömb, amely egyetlen eredménnyel rendelkezik a bemeneti tömb minden karakterláncához. Az eredmény objektum a következő tulajdonságokat tartalmazza:

  * `sentLen`: Egész számok tömbje, amely a mondatok hosszát jelképezi a szöveges elemben. A tömb hossza a mondatok száma, az értékek pedig az egyes mondatok hossza. 

  * `detectedLanguage`: Az észlelt nyelvet leíró objektum a következő tulajdonságokkal:

     * `language`: Az észlelt nyelv kódja.

     * `score`: Egy lebegőpontos érték, amely az eredmény megbízhatóságát jelzi. A pontszám nulla és egy, az alacsony pontszám pedig alacsony megbízhatóságot jelez.
     
    Vegye figyelembe, hogy a `detectedLanguage` tulajdonság csak akkor jelenik meg az eredmény objektumban, ha a rendszer automatikus észlelést kér.

Példa JSON-válaszra:

```json
[
  {
    "sentLen": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
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

Ha hiba történik, a kérés JSON-hibaüzenetet is ad vissza. A hibakód egy 6 számjegyből álló szám, amely a 3 számjegyből álló HTTP-állapotkódot kombinálja, majd egy 3 számjegyű számot, amely további kategorizálja a hibát. Gyakori hibakódok a [v3 Translator Reference oldalon](./v3-0-reference.md#errors)találhatók. 

## <a name="examples"></a>Példák

Az alábbi példa bemutatja, hogyan szerezhet be mondatokat egy mondathoz. A szolgáltatás automatikusan észleli a mondat nyelvét.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```