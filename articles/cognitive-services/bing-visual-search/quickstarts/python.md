---
title: 'Gyors útmutató: képelemzések beolvasása a REST API és a Python-Bing Visual Search használatával'
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan tölthet fel képet a Bing Visual Search API, és hogyan szerezhet be információkat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 4/02/2019
ms.author: scottwhi
ms.openlocfilehash: 6fafc35d9d74927789fee3f3fea3014ff3be5717
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383180"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Gyors útmutató: képelemzések beolvasása a Bing Visual Search REST API és a Python használatával

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Visual Search API és megtekintheti az eredményeket. Ez a Python-alkalmazás feltölt egy rendszerképet az API-hoz, és megjeleníti a visszaadott adatokat. Bár ez az alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

Helyi rendszerkép feltöltésekor az űrlapon szerepelnie kell a `Content-Disposition` fejlécnek. A `name` paramétert a "rendszerkép" értékre kell állítani, és a `filename` paramétert bármilyen sztringre állíthatja. Az űrlap tartalma tartalmazza a rendszerkép bináris értékeit. A feltölthető maximális képméret 1 MB lehet.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## <a name="prerequisites"></a>Előfeltételek

* [Python 3. x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és adja hozzá a következő `import` utasítást:

    ```python
    import requests, json
    ```

2. Hozzon létre változókat az előfizetési kulcshoz, a végponthoz és a feltölteni kívánt rendszerkép elérési útjához:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Hozzon létre egy szótár objektumot a kérelem fejlécére vonatkozó információk tárolásához. Az előfizetési kulcsot a `Ocp-Apim-Subscription-Key`karakterlánchoz kell kötni, ahogy az az alábbi ábrán látható:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Hozzon létre egy másik szótárt, amely tartalmazza a képet, amelyet a rendszer a kérelem elküldésekor megnyit és feltölt:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>A JSON-válasz elemzése

1. Hozzon létre egy `print_json()` nevű metódust az API-válasz elvégzéséhez, és nyomtassa ki a JSON-t:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>A kérelem elküldése

1. A `requests.post()` használatával küldhet egy kérelmet a Bing Visual Search APInak. Adja meg a végpont, a fejléc és a fájl adatait tartalmazó karakterláncot. `response.json()` nyomtatása `print_json()`:

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Visual Search egyoldalas Webalkalmazás létrehozása](../tutorial-bing-visual-search-single-page-app.md)
