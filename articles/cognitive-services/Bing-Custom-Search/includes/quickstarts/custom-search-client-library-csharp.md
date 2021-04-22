---
title: Bing Custom Search C# ügyféloldali kódtár – rövid útmutató
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 504057be85902307a121a57dd421254d94167341
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107880717"
---
Első lépések a C# Bing Custom Search ügyféloldali kódtárával. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapszintű feladatokhoz szükséges példakódot. A Bing Custom Search API-val személyre szabott, hirdetésmentes keresési felületeket hozhat létre az Ön számára fontos témakörökben. A minta forráskódja a [GitHubon található.](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch)

Használja a Bing Custom Search C# ügyféloldali kódtárát a következőre:
* A keresési eredményeket a weben, a saját Bing Custom Search találja.

[Referenciadokumentáció](/dotnet/api/overview/azure/cognitiveservices/bing-custom-search-readme)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch)  |  [Package (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)  |  [Minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)


## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. További információ: Rövid [útmutató: Az első Bing Custom Search létrehozása.](../../quick-start.md)
- Microsoft [.NET Core](https://dotnet.microsoft.com/download)
- A [2017-es Visual Studio bármely kiadása](https://www.visualstudio.com/downloads/)
- Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
- A [Bing Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)  NuGet-csomag. 
    - A **Megoldáskezelő** jobb Visual Studio kattintson a projektre, és válassza a **Menü NuGet-csomagok** kezelése parancsát. Telepítse az `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot. A NuGet Custom Search csomag telepítésekor a következő szerelvények is települnek:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új C#-konzolalkalmazást a Visual Studio. Ez után adja hozzá a projekthez az alábbi csomagokat.

    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
    ```

2. Az alkalmazás fő metódusában példányosíthatja a keresési ügyfelet az API-kulccsal.

    ```csharp
    var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));
    ```

## <a name="send-the-search-request-and-receive-a-response"></a>A keresési kérés küldése és válasz fogadása
    
1. Küldjön keresési lekérdezést az ügyfél `SearchAsync()` metódusával, és mentse a választ. Cserélje le a helyére a példánya konfigurációs azonosítóját (az azonosítót a Bing Custom Search `YOUR-CUSTOM-CONFIG-ID` [találja).](https://www.customsearch.ai/) Ebben a példában az "Xbox" kifejezésre keres.

    ```csharp
    // This will look up a single query (Xbox).
    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
    ```

2. A `SearchAsync()` metódus egy `WebData` objektumot ad vissza. A objektummal iterálhatja a `WebPages` talált összeset. Ez a kód megtalálja az első weboldal eredményt, és megjeleníti a weboldal `Name` és `URL` tulajdonságát.

    ```csharp
    if (webData?.WebPages?.Value?.Count > 0)
    {
        // find the first web page
        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

        if (firstWebPagesResult != null)
        {
            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
        }
        else
        {
            Console.WriteLine("Couldn't find web results!");
        }
    }
    else
    {
        Console.WriteLine("Didn't see any Web data..");
    }
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Custom Search-webalkalmazás létrehozása](../../tutorials/custom-search-web-page.md)
