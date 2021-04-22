---
title: 'Rövid útmutató: A Bing Custom Search hívása C#-| Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti a keresési eredmények lekért kérését a Bing Custom Search C# használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 1d3accdb20073bd1e9b29988b78d7eacd49976b8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862308"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-c"></a>Rövid útmutató: A Bing Custom Search hívása a C használatával # 

> [!WARNING]
> Bing Search API-k a Cognitive Services Bing Search szolgáltatásokba. **2020. október 30-ig** minden új Bing Search az itt dokumentált folyamat szerint kell [kiépítve.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Bing Search használatával üzembe Cognitive Services API-k a következő három évre vagy a Nagyvállalati Szerződés végéig lesznek támogatva.
> A migrálásra vonatkozó utasításokért [lásd: Bing Search Services.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Ebből a rövid útmutatóból megtudhatja, hogyan kérhet keresési eredményeket a Bing Custom Search példányról. Bár ez az alkalmazás C# nyelven íródott, a Bing Custom Search API egy RESTful-webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel. A minta forráskódja elérhető a [GitHubon.](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Search/BingCustomSearchv7.cs)

## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. További információ: [Rövid útmutató: Az első Bing Custom Search létrehozása.](quick-start.md)
- [Microsoft .NET Core](https://dotnet.microsoft.com/download).
- A [2019-es Visual Studio bármely kiadása.](https://www.visualstudio.com/downloads/)
- Linux/MacOS használata esetén ez az alkalmazás a Mono használatával [futtatható.](https://www.mono-project.com/)
- A [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/2.0.0) NuGet-csomag. 

   A csomag telepítése a következő Visual Studio: 
     1. Kattintson a jobb gombbal a projektre a Megoldáskezelő, majd válassza a **Manage NuGet Packages (NuGet-csomagok kezelése) lehetőséget.**  
     2. Keresse meg és válassza ki *a Microsoft.Azure.CognitiveServices.Search.CustomSearch* szolgáltatásokat, majd telepítse a csomagot.

   A Bing Custom Search NuGet-csomag telepítésekor a Visual Studio a következő csomagokat is telepíti:
     - **Microsoft.Rest.ClientRuntime**
     - **Microsoft.Rest.ClientRuntime.Azure**
     - **Newtonsoft.Json**


[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új C#-konzolalkalmazást a Visual Studio. Ezután adja hozzá a következő csomagokat a projekthez:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Web;
    using Newtonsoft.Json;
    ```

2. Hozza létre a következő osztályokat a Bing Custom Search API által visszaadott keresési eredmények tárolására:

    ```csharp
    public class BingCustomSearchResponse {        
        public string _type{ get; set; }            
        public WebPages webPages { get; set; }
    }

    public class WebPages {
        public string webSearchUrl { get; set; }
        public int totalEstimatedMatches { get; set; }
        public WebPage[] value { get; set; }        
    }

    public class WebPage {
        public string name { get; set; }
        public string url { get; set; }
        public string displayUrl { get; set; }
        public string snippet { get; set; }
        public DateTime dateLastCrawled { get; set; }
        public string cachedPageUrl { get; set; }
    }
    ```

3. A projekt fő metódusában hozza létre a következő változókat a Bing Custom Search API előfizetői azonosítóhoz, a példány egyéni konfigurációs azonosítójának és a keresési kifejezésnek a létrehozásához:

    ```csharp
    var subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    var customConfigId = "YOUR-CUSTOM-CONFIG-ID";
    var searchTerm = args.Length > 0 ? args[0]:"microsoft";
    ```

4. A kérelem URL-címét úgy hozhatja létre, hogy hozzáfűzi a keresési kifejezést a lekérdezési paraméterhez, és a keresési példány egyéni konfigurációs `q=` azonosítóját fűzi hozzá a `customconfig=` paraméterhez. Válassza el a paramétereket egy és `&` () értékekkel. A változó értékeként használhatja a globális végpontot a következő kódban, vagy használhatja az erőforráshoz Azure Portal egyéni `url` altartományvégpontot. [](../../cognitive-services/cognitive-services-custom-subdomains.md)

    ```csharp
    var url = "https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?" +
                "q=" + searchTerm + "&" +
                "customconfig=" + customConfigId;
    ```

## <a name="send-and-receive-a-search-request"></a>Keresési kérés küldése és fogadása 

1. Hozzon létre egy kérési ügyfelet, és adja hozzá az előfizetői kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

    ```csharp
    var client = new HttpClient();
    client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

2. Hajtsa végre a keresési kérést, és kérje le a választ JSON-objektumként.

    ```csharp
    var httpResponseMessage = client.GetAsync(url).Result;
    var responseContent = httpResponseMessage.Content.ReadAsStringAsync().Result;
    BingCustomSearchResponse response = JsonConvert.DeserializeObject<BingCustomSearchResponse>(responseContent);
    ```
## <a name="process-and-view-the-results"></a>Dolgozza fel és tekintse meg az eredményeket

- Iteráljon a válaszobjektumon, és jelenítse meg az egyes keresési eredmények adatait, beleértve a nevét, URL-címét és a weblap utolsó bejárási dátumát.

    ```csharp
    for(int i = 0; i < response.webPages.value.Length; i++) {                
        var webPage = response.webPages.value[i];
        
        Console.WriteLine("name: " + webPage.name);
        Console.WriteLine("url: " + webPage.url);                
        Console.WriteLine("displayUrl: " + webPage.displayUrl);
        Console.WriteLine("snippet: " + webPage.snippet);
        Console.WriteLine("dateLastCrawled: " + webPage.dateLastCrawled);
        Console.WriteLine();
    }
    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Custom Search-webalkalmazás létrehozása](./tutorials/custom-search-web-page.md)