---
title: 'Oktatóanyag: rendszerkép kivágása a Bing Visual Search SDK-val'
description: A Bing Visual Search SDK-val lekérheti a rendszerkép adott Ares-adatait.
services: cognitive-services
titleSuffix: Azure Cognitive Services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/31/2019
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ad73d7086a2066517c24a8b10a7f54503535942
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490295"
---
# <a name="tutorial-crop-an-image-with-the-bing-visual-search-sdk-for-c"></a>Oktatóanyag: kép körülvágása a C Bing Visual Search SDK-val #

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Bing Visual Search SDK lehetővé teszi, hogy egy képet a hasonló online képek keresése előtt levágja. Ez az alkalmazás egyetlen személyt termeszt egy több embert tartalmazó képből, majd visszaadja azokat a keresési eredményeket, amelyek a hasonló, online állapotú képeket tartalmaznak.

Az alkalmazás teljes forráskódja elérhető további hibakezelés és jegyzetek a [githubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchCropImage.cs).

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Kérelem küldése az Bing Visual Search SDK használatával
> * Kép területének kivágása a Bing Visual Search való kereséshez
> * A válasz fogadása és kezelése
> * A válaszban szereplő műveleti elemek URL-címeinek megkeresése

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2019](https://www.visualstudio.com/downloads/)bármely kiadása.
* Linux/MacOS rendszer esetében az alkalmazás a [Monóval](https://www.mono-project.com/) futtatható.
* Telepített [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) csomag.
    - Az Megoldáskezelő a Visual Studióban kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** menüpontot a menüből. Telepítse az `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot. A NuGet Custom Search csomag telepítésekor a következő szerelvények is települnek:
        - Microsoft.Rest.ClientRuntime
        - Microsoft.Rest.ClientRuntime.Azure
        - Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="specify-the-image-crop-area"></a>Kép körülvágási térségének meghatározása

Ez az alkalmazás a Microsoft vezető csapatának ezen rendszerképének egy területét termeszti. Ez a terület a bal felső és a jobb oldali koordináták használatával van definiálva, a teljes rendszerkép százalékában kifejezve:  

![A Microsoft felső vezetése](./media/MS_SrLeaders.jpg)

Ezt a képet a rendszer egy objektum létrehozásával vágja `ImageInfo` le a területről, és betölti az objektumot a- `ImageInfo` ba `VisualSearchRequest` . Az `ImageInfo` objektum a rendszerkép URL-címét is tartalmazza:

```csharp
CropArea CropArea = new CropArea(top: (float)0.01, bottom: (float)0.30, left: (float)0.01, right: (float)0.20);
string imageURL = "https://docs.microsoft.com/azure/cognitive-services/bing-visual-search/media/ms_srleaders.jpg";
ImageInfo imageInfo = new ImageInfo(cropArea: CropArea, url: imageURL);

VisualSearchRequest visualSearchRequest = new VisualSearchRequest(imageInfo: imageInfo);
```

## <a name="search-for-images-similar-to-the-crop-area"></a>A körülvágáshoz hasonló rendszerképek keresése

A változó a `VisualSearchRequest` kép körülvágási felületére és URL-címére vonatkozó információkat tartalmaz. A `VisualSearchMethodAsync()` metódus az eredményeket kapja:

```csharp
Console.WriteLine("\r\nSending visual search request with knowledgeRequest that contains URL and crop area");
var visualSearchResults = client.Images.VisualSearchMethodAsync(knowledgeRequest: visualSearchRequest).Result;

```

## <a name="get-the-url-data-from-imagemoduleaction"></a>Az URL-cím adatainak beolvasása `ImageModuleAction`

Bing Visual Search eredmények `ImageTag` objektumok. Mindegy egyes címke `ImageAction` objektumok listáját tartalmazza. Mindegyik `ImageAction` tartalmaz egy `Data` mezőt, amely a művelet típusától függő értékek listáját tartalmazza.

A különböző típusokat a következő kóddal lehet kinyomtatni:

```csharp
Console.WriteLine("\r\n" + "ActionType: " + i.ActionType + " -> WebSearchUrl: " + i.WebSearchUrl);
```

A teljes alkalmazás a következőket adja vissza:

|ActionType  |URL-cím  |
|---------|---------|
|PagesIncluding WebSearchURL     |         |
|MoreSizes WebSearchURL     |         |  
|VisualSearch WebSearchURL    |         |
|ImageById WebSearchURL     |         |  
|RelatedSearches WebSearchURL     |         |
|Entitás – > WebSearchUrl     | https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID = 03DCF882D7386A442137F49BD6596BEF&Rd = 1&h = BvvDoRtmZ35Xc_UZE4lZx6_eg7FHgcCkigU1D98NHQo&v = 1&r = HTTPS %3 a %2 f %2 f www. Bing. com% 2fsearch% 3fq% 3dSatya% 2bNadella&p = DevEx, 5380.1        |
|TopicResults – > WebSearchUrl    |  https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID = 03DCF882D7386A442137F49BD6596BEF&Rd = 1&h = 3QGtxPb3W9LemuHRxAlW4CW7XN4sPkUYCUynxAqI9zQ&v = 1&r = HTTPS %3 a %2 f %2 f www. Bing. com% 2fdiscover% 2fnadella% 2bsatya&p = DevEx, 5382.1        |
|ImageResults – > WebSearchUrl    |  https \: //www.bing.com/cr?IG=E40D0E1A13404994ACB073504BC937A4&CID = 03DCF882D7386A442137F49BD6596BEF&Rd = 1&h = l-WNHO89Kkw69AmIGe2MhlUp6MxR6YsJszgOuM5sVLs&v = 1&r = HTTPS %3 a %2 f %2 f www. Bing. com% 2fimages% 2fsearch% 3fq% 3dSatya% 2bNadella&p = DevEx, 5384.1        |

Ahogy az a fentiekben is látható, a `Entity` Művelettípus olyan Bing keresési lekérdezést tartalmaz, amely egy felismerhető személy, hely vagy dolog adatait adja vissza. A `TopicResults` és az `ImageResults` típus a kapcsolódó képekre vonatkozó lekérdezéseket tartalmazza. A listában lévő URL-ek a Bing keresési eredményeire mutatnak.

## <a name="get-urls-for-pagesincluding-actiontype-images"></a>Képek URL-címeinek beolvasása `PagesIncluding` `ActionType`

A képek tényleges URL-jeinek a lekéréséhez szükség van egy olyan átalakításra, amely egy `ActionType` típust egy értéklistával rendelkező `Data` elemet tartalmazó `ImageModuleAction` műveletként olvas be. Minden egyes érték egy kép URL-je. A következő `PagesIncluding` művelet a műveletet írja be, `ImageModuleAction` és beolvassa az értékeket:

```csharp
    if (i.ActionType == "PagesIncluding")
    {
        foreach(ImageObject o in (i as ImageModuleAction).Data.Value)
        {
            Console.WriteLine("ContentURL: " + o.ContentUrl);
        }
    }
```

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Visual Search egyoldalas Webalkalmazás létrehozása](tutorial-bing-visual-search-single-page-app.md)

## <a name="see-also"></a>Lásd még
> [Mi az a Bing Visual Search API?](./overview.md)