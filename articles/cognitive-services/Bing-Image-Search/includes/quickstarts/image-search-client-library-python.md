---
title: Bing Image Search Python ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 338e4f0c1c47a7786e21ad98bb3e56a1505f9bb2
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104749"
---
Ezzel a rövid útmutatóval elvégezheti az első képkeresést az Bing Image Search ügyféloldali kódtár használatával, amely az API burkolója, és ugyanazokat a szolgáltatásokat tartalmazza. Ez az egyszerű Python-alkalmazás elküld egy képkeresési lekérdezést, elemzi a JSON-választ, és megjeleníti az első visszaadott kép URL-címét.

A minta forráskódja további hibakezeléssel és megjegyzésekkel együtt elérhető a [GitHubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py).

## <a name="prerequisites"></a>Előfeltételek

* [Python 2,7 vagy 3.6 +](https://www.python.org/).

* Az [Azure Image Search](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) a Pythonhoz készült ügyféloldali kódtár
    * Végezze el a telepítést a `pip install azure-cognitiveservices-search-imagesearch` paranccsal

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Az alkalmazás létrehozása és inicializálása

1. Hozzon létre egy új Python-szkriptet a kedvenc IDE-környezetében vagy szerkesztőjében, az alábbi importálásokkal:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Hozza létre az előfizetési kulcs és a keresett kifejezés változóit.

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>A képkeresési ügyfél létrehozása

1. Hozza létre a `CognitiveServicesCredentials` egy példányát, és példányosítsa az ügyfelet:

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```
1. Küldjön egy keresési lekérdezést a Bing Image Search API-nak:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>Dolgozza fel és tekintse meg az eredményeket

Elemezze a válaszban visszaadott képtalálatokat.


Ha a válasz tartalmaz keresési eredményeket, tárolja az első találatot, és jelenítse meg annak részleteit, például a miniatűr URL-címét és az eredeti URL-címet a visszaadott képek teljes számával együtt.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Image Search használatához](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Lásd még

* [Mi a Bing Image Search?](../../overview.md)  
* [Online interaktív bemutató kipróbálása](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Python-minták az Azure Cognitive Services SDK-hoz](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Az Azure Cognitive Services dokumentációja](../../../index.yml)
* [Bing Image Search API – referencia](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)