---
title: Bing Custom Search Python ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: 3019881c42e0f7b64cc766b8b9e575eb60612432
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947572"
---
Ismerkedés a Bing Custom Search a Pythonhoz készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Bing Custom Search API lehetővé teszi, hogy testreszabott, ad-ingyenes keresési élményeket hozzon létre az Ön számára fontos témakörökhöz. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py)található.

A Pythonhoz készült Bing Custom Search ügyféloldali kódtára a következőre használható:
* Keresési eredmények keresése a weben a Bing Custom Search-példányból.

[Dokumentáció](/python/api/azure-cognitiveservices-search-customsearch/)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch)  |  [Csomag (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Előfeltételek

- Egy Bing Custom Search példány. További információért tekintse [meg a rövid útmutató: az első Bing Custom Search példány létrehozása](../../quick-start.md) című témakört.
- Python [2. x vagy 3. x](https://www.python.org/) 
- A [Pythonhoz készült BING Custom Search SDK](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>A Python ügyféloldali kódtár telepítése

Telepítse az Bing Custom Search ügyféloldali függvénytárat a következő paranccsal.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Új alkalmazás létrehozása

Hozzon létre egy új Python-fájlt a kedvenc szerkesztőjében vagy az IDE-ben, és adja hozzá a következő importálásokat.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Keresési ügyfél létrehozása és kérelem küldése

1. Hozzon létre egy változót az előfizetési kulcs és a végpont számára.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Hozzon létre egy példányt az `CustomSearchClient` `CognitiveServicesCredentials` előfizetési kulccsal rendelkező objektum használatával. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Keresési kérelem küldése a szolgáltatással `client.custom_instance.search()` . Fűzze hozzá a keresési kifejezést a `query` paraméterhez, és állítsa az `custom_config` egyéni konfigurációs azonosítót a keresési példány használatára. Az azonosítót a [Bing Custom Search-portálon](https://www.customsearch.ai/)szerezheti be, az **éles** lapon kattintva.

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>A keresési eredmények megtekintése

Ha a rendszer bármelyik weblap keresési eredményét észlelte, töltse ki az elsőt, és nyomtassa ki a megtalált nevet, URL-címet és összes weblapot.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni keresési Webalkalmazás létrehozása](../../tutorials/custom-search-web-page.md)
