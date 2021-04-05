---
title: GIF-képek keresése a Bing Image Search API használatával
titleSuffix: Azure Cognitive Services
description: A Bing Image Search API lehetővé teszi, hogy a teljes weben is keressen a legrelevánsabb. gif-képekre.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: d1fcac891db240def2e7bbdcb45b45caf7f49a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96341903"
---
# <a name="search-for-gif-images"></a>GIF-képek keresése 

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Image Search API lehetővé teszi, hogy a teljes weben is keressen a legrelevánsabb. gif-képekre.  A fejlesztők különféle beszélgetési forgatókönyvekben integrálhatók a bevonási GIF-fájlokba. 

A következő URL-cím az animált. gif rendszerképek lekérdezése.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us
```
A [q](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query) paraméter határozza meg a keresési kifejezéseket.  Az előző lekérdezés `animatedGif` a [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) Filter paraméter használatát is megadja.

A találatok megjelenítéséhez használja a következő URL-címet a bing.com kereséséhez.
```
https://www.bing.com/images/search?q=interesting&qft=%20filterui%3Aphoto-animatedgif

```
## <a name="query-parameters"></a>Lekérdezési paraméterek

A lekérdezési paraméterekkel és beállításokkal kapcsolatos további információkért tekintse meg a [Image Search API-referenciát](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query-parameters). Egy példa a címsor alatt [, például az animált GIF keresése a Java használatával](#gifExample)című szakaszban látható.

## <a name="tips-and-suggestions"></a>Tippek és javaslatok

- Megadhatja a [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) és a [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) paramétereket. Azt javasoljuk, hogy a maxFileSize = 2000000 beállítása a GIF-fájlok többsége az indexben a 2 MB-ot.  Ez az adatok méretének szabályozását is lehetővé teszi, ha a sávszélesség aggodalomra ad okot, például mobil celluláris helyzetekben.
- Az érzékelt teljesítmény javítása érdekében először töltse be a miniatűrt a forrás URL-cím betöltése előtt.  
- Ha még nem rendelkezik felhasználói lekérdezéssel, próbálja ki a trendi GIF-kereséseket a Trends [images API](trending-images.md)segítségével.
- A [safeSearch](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#safesearch) paraméternek három beállítása van.  A `strict` lehetőség letiltja a felnőtt tartalmat.
- A támogatott nyelvek és helyszínek teljes listájáért lásd: [MKT](./language-support.md) .
- A *AnimatedGifHttps* csak a https-címről érkező animált GIF-képeket adja vissza. A biztonság érdekében számos alkalmazás igényli a https-kapcsolaton keresztüli külső webes hivatkozásokhoz való kapcsolódást. Például az Apple App Store-nak HTTPS-kapcsolaton keresztül kell kapcsolódnia a webszolgáltatásokhoz, amely az átvitel során titkosítja a felhasználói adatvédelmet.

<a name="gifExample"></a>

## <a name="example-search-for-animated-gif-using-java"></a>Példa az animált GIF keresésére a Javával

A következő URL-cím megkeresi az animált. gif képeket: `q=interesting`
```
https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=interesting&imageType=AnimatedGif&mkt=en-us

```
Ahogy az az alábbi példában is látható, az URL-lekérdezéshez [OCP-APIM-Subscription-Key](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#headers) fejléc szükséges.

A következő Java-példa létrehozza és elküldi a kérést.

```
package gifSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (BingImageSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac GIFsearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar GIFsearch
 */


public class GIFsearch {

    // Replace the subscriptionKey string value with your valid subscription key.
    static String subscriptionKey = "YOUR-ACCESS-KEY";

    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";

    static String searchTerm = "interesting";

    public static SearchResults SearchImages (String searchQuery) throws Exception {
        // construct URL of search request (endpoint + query string)
        URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&imageType=AnimatedGif&mkt=en-us");
        HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
        connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

        // receive JSON body
        InputStream stream = connection.getInputStream();
        String response = new Scanner(stream).useDelimiter("\\A").next();

        // construct result object for return
        SearchResults results = new SearchResults(new HashMap<String, String>(), response);

        // extract Bing-related HTTP headers
        Map<String, List<String>> headers = connection.getHeaderFields();
        for (String header : headers.keySet()) {
            if (header == null) continue;      // may have null key
            if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
                results.relevantHeaders.put(header, headers.get(header).get(0));
            }
        }

        stream.close();
        return results;
    }

    // pretty-printer for JSON; uses GSON parser to parse and re-serialize
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main (String[] args) {
        if (subscriptionKey.length() != 32) {
            System.out.println("Invalid Bing Search API subscription key!");
            System.out.println("Please paste yours into the source code.");
            System.exit(1);
        }

        try {
            System.out.println("Searching the Web for: " + searchTerm);

            SearchResults result = SearchImages(searchTerm);

            System.out.println("\nRelevant HTTP Headers:\n");
            for (String header : result.relevantHeaders.keySet())
                System.out.println(header + ": " + result.relevantHeaders.get(header));

            System.out.println("\nJSON Response:\n");
            System.out.println(prettify(result.jsonResponse));
        }
        catch (Exception e) {
            e.printStackTrace(System.out);
            System.exit(1);
        }
    }

}

//Container class for search results encapsulates relevant headers and JSON data
class SearchResults{
 HashMap<String, String> relevantHeaders;
 String jsonResponse;
 SearchResults(HashMap<String, String> headers, String json) {
     relevantHeaders = headers;
     jsonResponse = json;
 }
}

```

## <a name="results"></a>Results (Eredmények)
A kód a következő eredményeket kéri JSON-objektumokként:

```json
    {
      "webSearchUrl": "https://www.bing.com/images/search?view\u003ddetai...",
      "name": "Very Interesting GIF - Thats Very Interesting - ...",
      "thumbnailUrl": "https://tse1.mm.bing.net/th?id\u003dOIP.yJX6Vz345JPK...",
      "datePublished": "2017-03-12T01:35:00.0000000Z",
      "contentUrl": "https://media.contoso.co/images/c895fa573df8e493ca8d0dec7d93b/raw",
      "hostPageUrl": "https://www.contoso.co/view/thats-very-interesting-christi...",
      "contentSize": "1295633 B",
      "encodingFormat": "animatedgif",
      "hostPageDisplayUrl": "https://www.contoso.co/view/thats-very-christian...",
      "width": 440,
      "height": 186,
      "thumbnail": {
        "width": 474,
        "height": 200
      },
      "imageInsightsToken": "ccid_yJX6Vz34*mid_9FF0FFA42AADA1357F042443D2103B40EA...",
      "insightsMetadata": {
        "recipeSourcesCount": 0,
        "bestRepresentativeQuery": {
          "text": "That\u0027s Very Interesting",
          "displayText": "That\u0027s Very Interesting",
          "webSearchUrl": "https://www.bing.com/images/search?q\u003dThat..."
        },
        "pagesIncludingCount": 19,
        "availableSizesCount": 2
      },
      "imageId": "9FF0FFA42AADA1357F042443D21030EAAA225F",
      "accentColor": "62452D"
    },

```

## <a name="next-steps"></a>Következő lépések
- [C# – rövid útmutató](quickstarts/csharp.md)
- [Oktatóanyag Image Search egyoldalas alkalmazás](tutorial-bing-image-search-single-page-app.md)