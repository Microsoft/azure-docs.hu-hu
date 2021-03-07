---
title: A rangsor használata a keresési eredmények megjelenítéséhez
titleSuffix: Azure Cognitive Services
description: Azt mutatja be, hogyan használható a Bing RankingResponse válasz a keresési eredmények rangsorolási sorrendben való megjelenítéséhez.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 907899d8c5be2cbba7ff3eee4a59713718357a5c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430088"
---
# <a name="build-a-console-app-search-client-in-c"></a>A konzol alkalmazás keresési ügyfelének létrehozása C-ben #

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy egyszerű .NET Core Console-alkalmazást, amely lehetővé teszi a felhasználóknak a Bing Web Search API lekérdezését és a rangsorolt eredmények megjelenítését.

Ez az oktatóanyag a következőket mutatja be:

- Egyszerű lekérdezés készítése a Bing Web Search API
- Lekérdezési eredmények megjelenítése rangsorolt sorrendben

## <a name="prerequisites"></a>Előfeltételek

Ahhoz, hogy követni tudja az oktatóanyagot, a következőkre lesz szüksége:

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" hozzon létre egy Bing Search erőforrást, "  target="_blank"> és hozzon létre egy Bing Search-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
* A [Visual Studio ide](https://www.visualstudio.com/downloads/).

## <a name="create-a-new-console-app-project"></a>Új konzolos alkalmazás projekt létrehozása

Hozzon létre egy projektet a Visual Studióban a `Ctrl`+`Shift`+`N` billentyűparancs lenyomásával.

Az **új projekt** párbeszédpanelen kattintson a **Visual C# > Windows klasszikus asztali > Console app (.NET-keretrendszer)** elemre.

Nevezze el az alkalmazás **MyConsoleSearchApp**, majd kattintson **az OK** gombra.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>A JSON.net NuGet-csomag hozzáadása a projekthez

A JSON.net lehetővé teszi az API által visszaadott JSON-válaszok használatát. Adja hozzá a NuGet-csomagot a projekthez:

- **Megoldáskezelő** kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése...** lehetőséget.
- A  **Tallózás** lapon keressen rá a kifejezésre `Newtonsoft.Json` . Válassza ki a legújabb verziót, majd kattintson a **telepítés** gombra.
- Kattintson a **módosítások áttekintése** ablak **OK** gombjára.
- Zárjuk be a Visual Studio fület a **NuGet: MyConsoleSearchApp** címen.

## <a name="add-a-reference-to-systemweb"></a>A System. Web referenciájának hozzáadása

Ez az oktatóanyag a `System.Web` szerelvényre támaszkodik. Adjon hozzá egy hivatkozást ehhez a szerelvényhez a projekthez:

- A **megoldáskezelő** kattintson a jobb gombbal a **hivatkozások** elemre, és válassza a **hivatkozás hozzáadása..** . lehetőséget.
- Válassza a **szerelvények > Framework** elemet, majd görgessen le, és jelölje be a **System. Web**
- Válassza az **OK** gombot.

## <a name="add-some-necessary-using-statements"></a>Néhány szükséges utasítás hozzáadása

Az oktatóanyagban szereplő kódnak három további utasítást kell használnia. Adja hozzá a következő utasításokat a `using` **program.cs** tetején található meglévő utasításokhoz:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Kérje meg a felhasználót a lekérdezésre

**Megoldáskezelő** nyissa meg a **program.cs**. A `Main()` metódus frissítése:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Ez a metódus:

- Kéri a felhasználót a lekérdezésre
- `RunQueryAndDisplayResults(userQuery)`A lekérdezés végrehajtásának és az eredmények megjelenítésének meghívása
- Megvárja a felhasználói bevitelt, hogy megakadályozza a konzol ablakának azonnali bezárását.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Lekérdezési eredmények keresése a Bing Web Search API használatával

Ezután adjon hozzá egy metódust, amely lekérdezi az API-t, és megjeleníti az eredményeket:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Ez a metódus:

- Létrehoz egy `HttpClient` -t a Web Search API lekérdezéséhez
- Beállítja a `Ocp-Apim-Subscription-Key` http-fejlécet, amelyet a Bing a kérelem hitelesítéséhez használ
- Végrehajtja a kérést, és a JSON.net használatával deszerializálja az eredményeket
- Az `DisplayAllRankedResults(responseObjects)` összes eredmény rangsorolt sorrendben való megjelenítésének meghívása

Ügyeljen arra, hogy az `Ocp-Apim-Subscription-Key` előfizetési kulcs értékét állítsa be.

## <a name="display-ranked-results"></a>Rangsorolt eredmények megjelenítése

Mielőtt bemutatjuk, hogyan jeleníti meg az eredményeket rangsorolt sorrendben, tekintse meg a minta webes keresési választ:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

A `rankingResponse` JSON-objektum ([dokumentáció](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) a keresési eredmények megfelelő megjelenítési sorrendjét ismerteti. A következő, rangsorolt csoportok közül egyet vagy többet tartalmaz:

- `pole`: A keresési eredmények a legjobban látható kezelést kapják meg (például a fővonalon és az oldalsávon).
- `mainline`: A fővonalon megjelenítendő keresési eredmények.
- `sidebar`: Az oldalsávon megjelenítendő keresési eredmények. Ha nincs oldalsáv, jelenítse meg az eredményeket a fővonal alatt.

A besorolási válasz JSON-je tartalmazhat egy vagy több csoportot is.

A **program.cs**-ben adja hozzá a következő metódust az eredmények megfelelően rangsorolt sorrendben való megjelenítéséhez:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Ez a metódus:

- Hurkok a `rankingResponse` Válasz által tartalmazott csoportok felett
- Megjeleníti az egyes csoportok elemeit a hívással `DisplaySpecificResults(...)`

A **program.cs**-ben adja hozzá a következő két módszert:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Ezek a módszerek együttműködve a keresési eredményeket a konzolra exportálják.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást. A kimenetnek a következőképpen kell kinéznie:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>Következő lépések

További információ a [rangsor használatáról az eredmények megjelenítéséhez](rank-results.md).