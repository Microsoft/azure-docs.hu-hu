---
title: Rövid útmutató – Lekérdezés küldése az API-nak Pythonban – Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval elkezdi használni a Bing Local Business Search API-t a Pythonban.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.date: 05/12/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: adbf3d9abddf01ba67046cfa433ffd46f713ff83
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536679"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Rövid útmutató: Lekérdezés küldése a Bing Local Business Search API-ba Pythonban

> [!WARNING]
> Bing Search API-k a Cognitive Services Bing Search szolgáltatásokba. **2020.** október 30-ától a Bing Search új példányait az itt dokumentált folyamat szerint kell [kiépítenünk.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Bing Search használatával üzembe Cognitive Services API-kat a következő három évre vagy a Nagyvállalati Szerződés végéig támogatni fogjuk.
> A migrálásra vonatkozó utasításokért [lásd: Bing Search Services.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Ebből a rövid útmutatóból megtudhatja, hogyan küldhet kéréseket a Bing Local Business Search API-nak, amely egy Azure Cognitive Service. Bár ez az egyszerű alkalmazás Python nyelven lett megírva, az API egy RESTful-webszolgáltatás, amely kompatibilis minden olyan programozási nyelvvel, amely képes HTTP-kérések igénylésére és JSON-adatokat elemezni.

Ez a példaalkalmazás helyi válaszadatokat kap az API-tól egy keresési lekérdezéshez.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2.x vagy 3.x.
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Bing Search-erőforrást, és hozzon létre egy Bing Search-erőforrást a Azure Portal a kulcs és a <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás gombra.**

## <a name="run-the-complete-application"></a>A teljes alkalmazás futtatása

Az alábbi példa honosított eredményeket kap, amelyeket a következő lépésekben valósítanak meg:
1. Deklarálja a változókat a végpont gazdagép és útvonal szerinti megadásához.
2. Adja meg a lekérdezési paramétert. 
3. Definiálja a kérést meghatározó és a fejlécet hozzáadó keresési `Ocp-Apim-Subscription-Key` függvényt.
4. Állítsa be a `Ocp-Apim-Subscription-Key` fejlécet. 
5. Létesítsen kapcsolatot, és küldje el a kérést.
6. Megjeleníti a JSON-eredményeket.

A bemutató teljes kódja a következő:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Következő lépések
- [Local Business Search Java – rövid útmutató](local-search-java-quickstart.md)
- [Local Business Search C# – rövid útmutató](local-quickstart.md)
- [Local Business Search Node.js rövid útmutató](local-search-node-quickstart.md)
