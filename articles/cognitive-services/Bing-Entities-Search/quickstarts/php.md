---
title: 'Rövid útmutató: Bing Entity Search API a PHP használatával'
titlesuffix: Azure Cognitive Services
description: Információk és kódminták segítségével gyorsan megismerkedhet a Bing Entity Search API használatának első lépéseivel.
services: cognitive-services
author: v-jaswel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: quickstart
ms.date: 11/28/2017
ms.author: v-jaswel
ms.openlocfilehash: 30cfa98fc38f92d834e908b6d77306bed1d2b5cb
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815528"
---
# <a name="quickstart-for-bing-entity-search-api-with-php"></a>A Bing Entity Search API PHP használatával rövid útmutatója

Ez a cikk bemutatja, hogyan használható a [Bing Entity Search](https://docs.microsoft.com/azure/cognitive-services/bing-entities-search/search-the-web) API a PHP használatával.

## <a name="prerequisites"></a>Előfeltételek

A kód futtatásához [PHP 5.6.x](http://php.net/downloads.php) szükséges.

Egy **Bing Entity Search API-kat** tartalmazó [Cognitive Services API-fiókkal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) kell rendelkeznie. Az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-entity-search-api) elegendő ehhez a rövid útmutatóhoz. Szüksége lesz az ingyenes próbaverzió aktiválásakor kapott hozzáférési kulcsra, vagy beszerezhet egy fizetős előfizetői azonosítót az Azure-irányítópultról.

## <a name="search-entities"></a>Entitások keresése

Az alkalmazás futtatásához kövesse az alábbi lépéseket:

1. Hozzon létre egy új PHP-projektet a kedvenc IDE-környezetében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```php
<?php

// NOTE: Be sure to uncomment the following line in your php.ini file.
// ;extension=php_openssl.dll

// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
$subscriptionKey = 'ENTER KEY HERE';

$host = "https://api.cognitive.microsoft.com";
$path = "/bing/v7.0/entities";

$mkt = "en-US";
$query = "italian restaurants near me";

function search ($host, $path, $key, $mkt, $query) {

    $params = '?mkt=' . $mkt . '&q=' . urlencode ($query);

    $headers = "Ocp-Apim-Subscription-Key: $key\r\n";

    // NOTE: Use the key 'http' even if you are making an HTTPS request. See:
    // http://php.net/manual/en/function.stream-context-create.php
    $options = array (
        'http' => array (
            'header' => $headers,
            'method' => 'GET'
        )
    );
    $context  = stream_context_create ($options);
    $result = file_get_contents ($host . $path . $params, false, $context);
    return $result;
}

$result = search ($host, $path, $subscriptionKey, $mkt, $query);

echo json_encode (json_decode ($result), JSON_PRETTY_PRINT);
?>
```

**Válasz**

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon: 

```json
{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "italian restaurant near me",
    "askUserForLocation": true
  },
  "places": {
    "value": [
      {
        "_type": "LocalBusiness",
        "webSearchUrl": "https://www.bing.com/search?q=sinful+bakery&filters=local...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "https://www.contoso.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98112",
          "addressCountry": "US",
          "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
      },

      . . .
      {
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Pickles+and+Preserves...",
        "name": "Munson's Pickles and Preserves Farm",
        "url": "http://www.princi.com/",
        "entityPresentationInfo": {
          "entityScenario": "ListItem",
          "entityTypeHints": [
            "Place",
            "LocalBusiness",
            "Restaurant"
          ]
        },
        "address": {
          "addressLocality": "Seattle",
          "addressRegion": "WA",
          "postalCode": "98101",
          "addressCountry": "US",
          "neighborhood": "Capitol Hill"
        },
        "telephone": "(800) 555-1212"
      },
      
      . . .
    ]
  }
}
```

[Vissza a tetejére](#HOLTop)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A Bing Entity Search oktatóanyag](../tutorial-bing-entities-search-single-page-app.md)
> [A Bing Entity Search áttekintése](../search-the-web.md )
> [API-referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)
