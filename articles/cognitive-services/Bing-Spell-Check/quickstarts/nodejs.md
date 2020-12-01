---
title: 'Gyors útmutató: helyesírás ellenőrzése a REST API és Node.js-Bing Spell Check'
titleSuffix: Azure Cognitive Services
description: A Bing Spell Check REST API és a Node.js használatának első lépései a helyesírás és a nyelvtan ellenőrzéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 182ccc972fce40adc88ed66b01c92e778f6479a9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352694"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-nodejs"></a>Gyors útmutató: helyesírás ellenőrzése a Bing Spell Check REST API és Node.js

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ezzel a rövid útmutatóval megteheti az első hívást a Bing Spell Check REST API. Ez az egyszerű JavaScript-alkalmazás egy kérelmet küld az API-nak, és a javasolt javítások listáját adja vissza. 

Bár ez az alkalmazás JavaScript nyelven íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. Az alkalmazás forráskódja elérhető a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/nodejs/Search/BingSpellCheckv7.js).

## <a name="prerequisites"></a>Előfeltételek

* [Node.js 6](https://nodejs.org/en/download/) vagy újabb.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új JavaScript-fájlt a kedvenc IDE vagy szerkesztőben. Állítsa be a szigorúság és a szükséges értékeit `https` . Ezután hozzon létre változókat az API-végpont gazdagépéhez, elérési útjához és az előfizetési kulcshoz. Használhatja a globális végpontot a következő kódban, vagy használhatja az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) -végpontot.

    ```javascript
    'use strict';
    let https = require ('https');

    let host = 'api.cognitive.microsoft.com';
    let path = '/bing/v7.0/spellcheck';
    let key = '<ENTER-KEY-HERE>';
    ```

2. Hozzon létre változókat a keresési paraméterekhez és a megtekinteni kívánt szöveghez: 

   1. Rendelje hozzá a piaci kódot a `mkt` paraméterhez az `=` operátorral. A piaci kód annak az országnak/régiónak a kódja, amelyre a kérést végzi. 

   1. Adja hozzá a `mode` paramétert a `&` kezelőhöz, majd rendelje hozzá a helyesírás-ellenőrzési módot. A mód lehet (a `proof` legtöbb helyesírási/nyelvtani hibát felhasználhatja) `spell` , vagy (a legtöbb helyesírási hibát kigyűjti, de nem annyi nyelvtani hibát).

    ```javascript
    let mkt = "en-US";
    let mode = "proof";
    let text = "Hollo, wrld!";
    let query_string = "?mkt=" + mkt + "&mode=" + mode;
    ```

## <a name="create-the-request-parameters"></a>A kérés paramétereinek létrehozása

A kérés paramétereinek létrehozásához hozzon létre egy új objektumot egy `POST` metódussal. Adja meg az elérési utat a végpont elérési útjának hozzáfűzésével és a lekérdezési karakterlánccal. Ezután adja hozzá az előfizetési kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

```javascript
let request_params = {
   method : 'POST',
   hostname : host,
   path : path + query_string,
   headers : {
   'Content-Type' : 'application/x-www-form-urlencoded',
   'Content-Length' : text.length + 5,
      'Ocp-Apim-Subscription-Key' : key,
   }
};
```

## <a name="create-a-response-handler"></a>Válaszkezelő létrehozása

Hozzon létre egy nevű függvényt, `response_handler` amely a JSON-választ az API-ból veszi át, és nyomtassa ki. Hozzon létre egy változót a válasz törzse számára. Fűzze hozzá a választ, ha a `data` használatával jelzőt fogad `response.on()` . `end`A jelző fogadása után nyomtassa ki a JSON-törzset a konzolon.

```javascript
let response_handler = function (response) {
    let body = '';
    response.on ('data', function (d) {
        body += d;
    });
    response.on ('end', function () {
        let body_ = JSON.parse (body);
        console.log (body_);
    });
    response.on ('error', function (e) {
        console.log ('Error: ' + e.message);
    });
};
```

## <a name="send-the-request"></a>A kérelem elküldése

Hívja meg az API `https.request()` -t a kérelem paramétereinek és a Response Handler használatával. Írja be a szöveget az API-ba, majd fejezze be a kérést.

```javascript
let req = https.request (request_params, response_handler);
req.write ("text=" + text);
req.end ();
```


## <a name="run-the-application"></a>Az alkalmazás futtatása

1. Hozza létre és futtassa a projektet.

1. Ha a parancssort használja, az alábbi paranccsal hozhatja létre és futtathatja az alkalmazást:

   ```bash
   node <FILE_NAME>.js
   ```


## <a name="example-json-response"></a>Példa JSON-válaszra

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas webalkalmazás létrehozása](../tutorials/spellcheck.md)

- [Mi a Bing Spell Check API?](../overview.md)
- [Bing Spell Check API v7-dokumentáció](/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)