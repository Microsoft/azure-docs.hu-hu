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
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: b56f6743b642904349797ac5b6167194f7916b45
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75446591"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Gyors útmutató: képelemzések beolvasása a Bing Visual Search REST API és a Python használatával

Ezzel a rövid útmutatóval elvégezheti az első hívását a Bing Visual Search API és megtekintheti az eredményeket. Ez a Python-alkalmazás feltölt egy rendszerképet az API-hoz, és megjeleníti a visszaadott adatokat. Bár ez az alkalmazás Pythonban íródott, az API egy REST-alapú webszolgáltatás, amely kompatibilis a legtöbb programozási nyelvvel.

## <a name="prerequisites"></a>Előfeltételek

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Az alkalmazás inicializálása

1. Hozzon létre egy új Python-fájlt a kedvenc IDE vagy szerkesztőben, és `import` adja hozzá a következő utasítást:

    ```python
    import requests, json
    ```

2. Hozzon létre változókat az előfizetési kulcshoz, a végponthoz és a feltöltött rendszerkép elérési útjához. `BASE_URI`az alábbi globális végpont lehet, vagy az erőforráshoz tartozó Azure Portalban megjelenő [Egyéni altartomány](../../../cognitive-services/cognitive-services-custom-subdomains.md) végpontja:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
    Helyi rendszerkép feltöltésekor az űrlapon szerepelnie kell a `Content-Disposition` fejlécnek. A `name` paramétert a "rendszerkép" értékre kell állítania, és a `filename` paramétert bármilyen sztringre állíthatja. Az űrlap tartalma tartalmazza a rendszerkép bináris értékeit. A feltölthető maximális képméret 1 MB lehet.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

3. Hozzon létre egy szótár objektumot a kérelem fejlécére vonatkozó információk tárolásához. Kösse az előfizetési kulcsot a karakterlánchoz `Ocp-Apim-Subscription-Key`az alább látható módon:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Hozzon létre egy másik szótárt, amely tartalmazza a képet, amelyet a rendszer a kérelem elküldésekor megnyit és feltölt:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>A JSON-válasz elemzése

1. Hozzon létre egy `print_json()` nevű metódust az API-válaszban, és nyomtassa ki a JSON-t:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>A kérelem elküldése

1. `requests.post()` Kérelem küldése a Bing Visual Search APInak. Adja meg a végpont, a fejléc és a fájl adatait tartalmazó karakterláncot. Nyomtatás `response.json()` `print_json()`:

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
