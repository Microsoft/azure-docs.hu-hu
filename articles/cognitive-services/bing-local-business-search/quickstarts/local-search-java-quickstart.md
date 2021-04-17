---
title: Rövid útmutató – Lekérdezés küldése az API-nak a Java használatával – Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megismerheti, hogyan küldhető kérés Java nyelven a Bing Local Business Search API-nak, amely egy Azure Cognitive Services-szolgáltatás.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.date: 05/12/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: 001fc80b30eaa736db27ba76384aaf273bdec903
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536660"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-java"></a>Rövid útmutató: Lekérdezés küldése a Bing Local Business Search API-ba a Java használatával

> [!WARNING]
> Bing Search API-k az Cognitive Services-Bing Search szolgáltatásokba költöznek. **2020.** október 30-ától a Bing Search új példányait az itt dokumentált folyamat szerint kell [kiépítenünk.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Bing Search használatával üzembe Cognitive Services API-kat a következő három évre vagy a Nagyvállalati Szerződés végéig támogatni fogjuk.
> A migrálásra vonatkozó utasításokért [lásd: Bing Search Services.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Ebből a rövid útmutatóból megtudhatja, hogyan küldhet kéréseket a Bing Local Business Search API-nak, amely egy Azure Cognitive Service. Bár ez az egyszerű alkalmazás Java nyelven lett megírva, az API egy RESTful-webszolgáltatás, amely kompatibilis minden olyan programozási nyelvvel, amely képes HTTP-kérések igénylésére és JSON-adatokat elemezni.

Ez a példaalkalmazás helyi válaszadatokat kap az API-tól egy keresési lekérdezéshez.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* A [Java fejlesztői készlet (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Bing Search-erőforrást, és hozzon létre egy Bing Search-erőforrást a Azure Portal a kulcs és a <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás gombra.**

## <a name="create-the-request"></a>A kérés létrehozása 

Az alábbi kód létrehoz egy et, beállítja a hozzáférési kulcs fejlécét, és hozzáad egy lekérdezési sztringet `WebRequest` a *bellevue-i szálloda számára.*  Ezután elküldi a kérést, és hozzárendeli a választ egy sztringhez, hogy az tartalmazza a JSON-szöveget.

```java
    // construct URL of search request (endpoint + query string)
     URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + &mkt=en-us");
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);

    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();

    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
```

## <a name="run-the-complete-application"></a>A teljes alkalmazás futtatása

A következő kód a Bing Local Business Search API használatával adja vissza a Bing keresőmotor keresési eredményeit. Futtassa ezt a kódot az alábbi lépésekkel:
1. Töltse le vagy telepítse a gson-kódtárat.
2. Hozzon létre egy új Java-projektet a kedvenc IDE-környezetében vagy szerkesztőjében.
3. Adja hozzá az alábbi kódot.
4. A `subscriptionKey` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
5. Futtassa a programot.

```java
package localSearch;
import java.net.*;
import java.util.*;
import java.io.*;
import javax.net.ssl.HttpsURLConnection;

/*
 * Gson: https://github.com/google/gson
 * Maven info:
 *     groupId: com.google.code.gson
 *     artifactId: gson
 *     version: 2.8.1
 *
 * Once you have compiled or downloaded gson-2.8.1.jar, assuming you have placed it in the
 * same folder as this file (localSearch.java), you can compile and run this program at
 * the command line as follows.
 *
 * javac localSearch.java -classpath .;gson-2.8.1.jar -encoding UTF-8
 * java -cp .;gson-2.8.1.jar localSearch
 */
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

public class LocalSearchCls {

    // ***********************************************
    // *** Update or verify the following values. ***
    // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        static String subscriptionKey = "YOUR-ACCESS-KEY";

        static String host = "https://api.cognitive.microsoft.com/bing";
        static String path = "/v7.0/localbusinesses/search";

        static String searchTerm = "Hotel in Bellevue";

        public static SearchResults SearchLocal (String searchQuery) throws Exception {
            // construct URL of search request (endpoint + query string)
            URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8") + "&mkt=en-us");
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
            try {
                System.out.println("Searching the Web for: " + searchTerm);

                SearchResults result = SearchLocal(searchTerm);

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

    // Container class for search results encapsulates relevant headers and JSON data
    class SearchResults{
        HashMap<String, String> relevantHeaders;
        String jsonResponse;
        SearchResults(HashMap<String, String> headers, String json) {
            relevantHeaders = headers;
            jsonResponse = json;
        }
    }

```

## <a name="next-steps"></a>Következő lépések
- [Local Business Search C# – rövid útmutató](local-quickstart.md)
- [Local Business Search Node.js rövid útmutató](local-search-node-quickstart.md)
- [Local Business Search Python – rövid útmutató](local-search-python-quickstart.md)
