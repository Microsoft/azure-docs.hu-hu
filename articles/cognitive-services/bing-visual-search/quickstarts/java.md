---
title: 'Gyors útmutató: képelemzések beolvasása a REST API és a Java-Bing Visual Search használatával'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan tölthet fel képet a Bing Visual Search API, és hogyan szerezhet be információkat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: fe323fc27062ad1bee9abdfaf3408430e28523a9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446627"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-java"></a>Gyors útmutató: képelemzések lekérése a Bing Visual Search REST API és a Java használatával

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Visual Search API és megtekintheti az eredményeket. Ez a Java-alkalmazás feltölt egy rendszerképet az API-hoz, és megjeleníti a visszaadott adatokat. Bár ez az alkalmazás Java-ban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* A [Java fejlesztői készlet (JDK) 7 vagy 8](https://aka.ms/azure-jdks)
* A [Gson Java-könyvtára](https://github.com/google/gson)
* [Apache HttpComponents](https://hc.apache.org/downloads.cgi)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Projekt létrehozása és inicializálása

1. Hozzon létre egy új Java-projektet a kedvenc IDE vagy szerkesztőben, és importálja a következő könyvtárakat:

    ```java
    import java.util.*;
    import java.io.*;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    
    // HttpClient libraries
    
    import org.apache.http.HttpEntity;
    import org.apache.http.HttpResponse;
    import org.apache.http.client.methods.HttpPost;
    import org.apache.http.entity.ContentType;
    import org.apache.http.entity.mime.MultipartEntityBuilder;
    import org.apache.http.impl.client.CloseableHttpClient;
    import org.apache.http.impl.client.HttpClientBuilder;
    ```

2. Hozzon létre változókat az API-végponthoz, az előfizetési kulcshoz és a rendszerkép elérési útjához. `endpoint` lehet az alábbi globális végpont, vagy az erőforrás Azure Portal megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpontja:

    ```java
    static String endpoint = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
    static String subscriptionKey = "your-key-here";
    static String imagePath = "path-to-your-image";
    ```

    
    Helyi rendszerkép feltöltésekor az űrlapon szerepelnie kell a `Content-Disposition` fejlécnek. A `name` paramétert a "rendszerkép" értékre kell állítani, és a `filename` paramétert bármilyen sztringre állíthatja. Az űrlap tartalma tartalmazza a rendszerkép bináris értékeit. A feltölthető maximális képméret 1 MB lehet.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

## <a name="create-the-json-parser"></a>A JSON-elemző létrehozása

Hozzon létre egy metódust, amellyel a JSON-válasz a `JsonParser`használatával olvashatóbb lehet az API-tól:

```java
public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonObject json = parser.parse(json_text).getAsJsonObject();
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
```

## <a name="construct-the-search-request-and-query"></a>A keresési kérelem és a lekérdezés összeállítása

1. Az alkalmazás fő metódusában hozzon létre egy HTTP-ügyfelet `HttpClientBuilder.create().build();`használatával:

    ```java
    CloseableHttpClient httpClient = HttpClientBuilder.create().build();
    ```

2. Hozzon létre egy `HttpEntity` objektumot a rendszerkép API-ba való feltöltéséhez:

    ```java
    HttpEntity entity = MultipartEntityBuilder
        .create()
        .addBinaryBody("image", new File(imagePath))
        .build();
    ```

3. Hozzon létre egy `httpPost` objektumot a végponttal, és állítsa be a fejlécet az előfizetési kulcs használatára:

    ```java
    HttpPost httpPost = new HttpPost(endpoint);
    httpPost.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
    httpPost.setEntity(entity);
    ```

## <a name="receive-and-process-the-json-response"></a>A JSON-válasz fogadása és feldolgozása

1. A `HttpClient.execute()` metódussal küldhet egy kérést az API-nak, és tárolhatja a választ egy `InputStream` objektumban:
    
    ```java
    HttpResponse response = httpClient.execute(httpPost);
    InputStream stream = response.getEntity().getContent();
    ```

2. Tárolja a JSON-karakterláncot, és nyomtassa ki a választ:

    ```java
    String json = new Scanner(stream).useDelimiter("\\A").next();
    System.out.println("\nJSON Response:\n");
    System.out.println(prettify(json));
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Visual Search egyoldalas Webalkalmazás létrehozása](../tutorial-bing-visual-search-single-page-app.md)
