---
title: 'Gyors útmutató: Hajtsa végre egy keresést a Pythonnal – a Bing Web Search API'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével-kérelmeket küldjön a Bing Web Search REST API-be a Python, és a egy JSON-választ kap
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 03/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 2f26392bdac34dd831e04c772e5357f5e41fc746
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390213"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Gyors útmutató: A Python használatával a Bing Web Search API meghívása  

Ez a rövid útmutató segítségével a Bing Web Search API az első hívását, és a JSON-választ kapnak. A Python-alkalmazás egy keresési kérelmet küld az API-t, és bemutatja a választ. Bár ez az alkalmazás Python nyelven lett íródott, az API egy RESTful-webszolgáltatás, azaz kompatibilis a legtöbb programnyelvvel.

Ez a példa Jupyter-notebookként van futtatva a [MyBinderben](https://mybinder.org). Válassza ki a indítási binder jelvény:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Előfeltételek

* [Python 2.x vagy 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Változók meghatározása

Cserélje le a `subscription_key` értékét egy érvényes előfizetői azonosítóra az Azure-fiókjából.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Deklarálja a Bing Web Search API végpontját. Ha hitelesítési hibákba ütközik, ellenőrizze újra ezt az értéket az Azure-irányítópulton lévő Bing-keresési végponttal összevetve.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Nyugodtan testreszabhatja a keresési lekérdezést a `search_term` értékének lecserélésével.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Kérés indítása

Ez a blokk a `requests` kódtárat használja a Bing Web Search API meghívásához, és JSON-objektumként adja vissza az eredményeket. Az API-kulcs a `headers` szótárban lesz átadva, a keresési kifejezést és a lekérdezés paramétereit pedig a `params` szótárban. A beállítások és paraméterek teljes listáját a [Bing Web Search API 7-es verziójának](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference) dokumentációjában találja meg.

```python
import requests

headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations":True, "textFormat":"HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>A válasz formázása és megjelenítése

A `search_results` objektum tartalmazza a keresési eredmények és azok metaadatait, például a lapok és a kapcsolódó lekérdezések. Ez a kód az `IPython.display` kódtár segítségével formázza és jeleníti meg a választ a böngészőjében.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"],v["name"],v["snippet"]) \
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>A mintakód a Githubon

Ha szeretné ezt a kódot helyben futtatni, a teljes [mintát megtalálja a GitHubon](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Egyoldalas alkalmazás-oktatóanyag a Bing Web Search használatához](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
