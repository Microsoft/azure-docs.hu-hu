---
title: Bing Web Search Python ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/05/2020
ms.author: aahi
ms.openlocfilehash: 5d5aaf84482dae6786ac7fd9f9ee837efca71b34
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104856"
---
Az Bing Web Search ügyféloldali kódtár megkönnyíti a Bing Web Search integrálását a Python-alkalmazásba. Ebben a rövid útmutatóban elsajátíthatja a kérésküldésnek, a JSON-válaszok fogadásának, valamint az eredmények szűrésének és elemzésének módját.

Szeretné most rögtön megtekinteni a kódot? A [Pythonhoz készült Bing Search ügyféloldali kódtárak](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/search) mintái a githubon érhetők el.


## <a name="prerequisites"></a>Előfeltételek
A Bing Web Search SDK kompatibilis a Python 2,7 vagy a 3.6 + verzióval. Jelen rövid útmutatóhoz ajánlott egy virtuális környezet használata.

* Python 2,7 vagy 3.6 +
* [virtualenv](https://docs.python.org/3/tutorial/venv.html) a Python 2.7 esetében
* [venv](https://pypi.python.org/pypi/virtualenv) a Python 3.x esetében

[!INCLUDE [bing-web-search-quickstart-signup](~/includes/bing-web-search-quickstart-signup.md)]

## <a name="create-and-configure-your-virtual-environment"></a>A virtuális környezet létrehozása és konfigurálása

A virtuális környezet létrehozására és konfigurálására vonatkozó útmutatás eltérő a Python 2.x és a Python 3.x használata esetében. Az alábbi lépéseket követve hozza létre és inicializálja a virtuális környezetet.

### <a name="python-2x"></a>Python 2.x

Virtuális környezet létrehozása a `virtualenv` segítségével Python 2.7 használata esetén:

```console
virtualenv mytestenv
```

Aktiválja a környezetet:

```console
cd mytestenv
source bin/activate
```

Telepítse a Bing Web Search SDK függőségeit:

```console
python -m pip install azure-cognitiveservices-search-websearch
```

### <a name="python-3x"></a>Python 3.x

Virtuális környezet létrehozása a `venv` segítségével Python 3.x használata esetén:

```console
python -m venv mytestenv
```

Aktiválja a környezetet:

```console
mytestenv\Scripts\activate.bat
```

Telepítse a Bing Web Search SDK függőségeit:

```console
cd mytestenv
python -m pip install azure-cognitiveservices-search-websearch
```

## <a name="create-a-client-and-print-your-first-results"></a>Ügyfél létrehozása és az első eredmények megjelenítése

Most, hogy beállítottuk a virtuális környezetet, és telepítettük a függőségeket, ideje létrehozni az ügyfelet. Az ügyfél fogja kezelni a Bing Web Search API-ra küldött kéréseket és az onnan érkező válaszokat.

Ha a válasz tartalmaz weblapokat, képeket, cikkeket vagy videókat, a rendszer mindegyikből megjeleníti az elsőt.

1. Hozzon létre egy új Python-projektet a kedvenc IDE-környezetében vagy szerkesztőjében.

1. Másolja a mintát a projektbe. `endpoint` az az alábbi globális végpont lehet, vagy az [Egyéni altartomány](~/articles/cognitive-services/cognitive-services-custom-subdomains.md) végpontja jelenik meg az erőforrás Azure Portalban.:  

    ```python
    # Import required modules.
    from azure.cognitiveservices.search.websearch import WebSearchClient
    from azure.cognitiveservices.search.websearch.models import SafeSearch
    from msrest.authentication import CognitiveServicesCredentials

    # Replace with your subscription key.
    subscription_key = "YOUR_SUBSCRIPTION_KEY"

    # Instantiate the client and replace with your endpoint.
    client = WebSearchClient(endpoint="YOUR_ENDPOINT", credentials=CognitiveServicesCredentials(subscription_key))

    # Make a request. Replace Yosemite if you'd like.
    web_data = client.web.search(query="Yosemite")
    print("\r\nSearched for Query# \" Yosemite \"")

    '''
    Web pages
    If the search response contains web pages, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.web_pages, 'value'):

        print("\r\nWebpage Results#{}".format(len(web_data.web_pages.value)))

        first_web_page = web_data.web_pages.value[0]
        print("First web page name: {} ".format(first_web_page.name))
        print("First web page URL: {} ".format(first_web_page.url))

    else:
        print("Didn't find any web pages...")

    '''
    Images
    If the search response contains images, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.images, 'value'):

        print("\r\nImage Results#{}".format(len(web_data.images.value)))

        first_image = web_data.images.value[0]
        print("First Image name: {} ".format(first_image.name))
        print("First Image URL: {} ".format(first_image.url))

    else:
        print("Didn't find any images...")

    '''
    News
    If the search response contains news, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.news, 'value'):

        print("\r\nNews Results#{}".format(len(web_data.news.value)))

        first_news = web_data.news.value[0]
        print("First News name: {} ".format(first_news.name))
        print("First News URL: {} ".format(first_news.url))

    else:
        print("Didn't find any news...")

    '''
    If the search response contains videos, the first result's name and url
    are printed.
    '''
    if hasattr(web_data.videos, 'value'):

        print("\r\nVideos Results#{}".format(len(web_data.videos.value)))

        first_video = web_data.videos.value[0]
        print("First Videos name: {} ".format(first_video.name))
        print("First Videos URL: {} ".format(first_video.url))

    else:
        print("Didn't find any videos...")
    ```

1. Cserélje le a `SUBSCRIPTION_KEY` értékét egy érvényes előfizetői azonosítóra.

1. Cserélje le a kifejezést a `YOUR_ENDPOINT` portálon a végpont URL-címére, és távolítsa el a "Bing/v 7.0" szakaszt a végpontról.

1. Futtassa a programot. Példa: `python your_program.py`.

## <a name="define-functions-and-filter-results"></a>Függvények definiálása és az eredmények szűrése

Most, hogy elvégezte az első hívását a Bing Web Search APIra, nézzük meg néhány funkciót. A következő részekben a lekérdezések finomítására és az eredmények szűrésére szolgáló SDK-funkciók vannak kiemelve. Az egyes függvények hozzáadhatók az előző szakaszban létrehozott Python-programhoz.

### <a name="limit-the-number-of-results-returned-by-bing"></a>A Bing által visszaadott eredmények számának korlátozása

Ez a példa a `count` és a paramétereket használja az `offset` SDK [ `search` metódusának](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations)használatával visszaadott eredmények számának korlátozására. Az első eredményhez tartozó `name` és `url` értékét a rendszer megjeleníti.

1. Adja hozzá ezt a kódot a Python-projekthez:

   ```python
    # Declare the function.
    def web_results_with_count_and_offset(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, offset, and count using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="Best restaurants in Seattle", offset=10, count=20)
            print("\r\nSearching for \"Best restaurants in Seattle\"")

            if web_data.web_pages.value:
                '''
                If web pages are available, print the # of responses, and the first and second
                web pages returned.
                '''
                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't find any web pages...")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Futtassa a programot.

### <a name="filter-for-news-and-freshness"></a>Hírek és frissesség szűrése

Ez a példa a `response_filter` és a paramétereket használja a `freshness` keresési eredmények szűrésére az SDK [ `search` metódusának](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations)használatával. A visszaadott keresési eredmények a sajtóhírekre, azon belül pedig a Bing által az elmúlt 24 órában észlelt oldalakra van korlátozva. Az első eredményhez tartozó `name` és `url` értékét a rendszer megjeleníti.

1. Adja hozzá ezt a kódot a Python-projekthez:

    ```python
    # Declare the function.
    def web_search_with_response_filter(subscription_key):
        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))
        try:
            '''
            Set the query, response_filter, and freshness using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(query="xbox",
                response_filter=["News"],
                freshness="Day")
            print("\r\nSearching for \"xbox\" with the response filter set to \"News\" and freshness filter set to \"Day\".")

            '''
            If news articles are available, print the # of responses, and the first and second
            articles returned.
            '''
            if web_data.news.value:

                print("# of news results: {}".format(len(web_data.news.value)))

                first_web_page = web_data.news.value[0]
                print("First article name: {} ".format(first_web_page.name))
                print("First article URL: {} ".format(first_web_page.url))

                print("")

                second_web_page = web_data.news.value[1]
                print("\nSecond article name: {} ".format(second_web_page.name))
                print("Second article URL: {} ".format(second_web_page.url))

            else:
                print("Didn't find any news articles...")

        except Exception as err:
            print("Encountered exception. {}".format(err))

    # Call the function.
    web_search_with_response_filter(subscription_key)
    ```

1. Futtassa a programot.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>A biztonságos keresés, a válaszszám és az előléptetés szűrő használata

Ez a példa a `answer_count` , a és a paramétereket használja a `promote` `safe_search` keresési eredmények szűrésére az SDK [ `search` metódusának](/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations)használatával. A kód megjeleníti az első eredmény `name` és `url` értékét.

1. Adja hozzá ezt a kódot a Python-projekthez:

    ```python
    # Declare the function.
    def web_search_with_answer_count_promote_and_safe_search(subscription_key):

        client = WebSearchAPI(CognitiveServicesCredentials(subscription_key))

        try:
            '''
            Set the query, answer_count, promote, and safe_search parameters using the SDK's search method. See:
            https://docs.microsoft.com/python/api/azure-cognitiveservices-search-websearch/azure.cognitiveservices.search.websearch.operations.weboperations?view=azure-python.
            '''
            web_data = client.web.search(
                query="Niagara Falls",
                answer_count=2,
                promote=["videos"],
                safe_search=SafeSearch.strict  # or directly "Strict"
            )
            print("\r\nSearching for \"Niagara Falls\"")

            '''
            If results are available, print the # of responses, and the first result returned.
            '''
            if web_data.web_pages.value:

                print("Webpage Results#{}".format(len(web_data.web_pages.value)))

                first_web_page = web_data.web_pages.value[0]
                print("First web page name: {} ".format(first_web_page.name))
                print("First web page URL: {} ".format(first_web_page.url))

            else:
                print("Didn't see any Web data..")

        except Exception as err:
            print("Encountered exception. {}".format(err))
    ```

1. Futtassa a programot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel a projekttel, ne felejtse el eltávolítani az előfizetői azonosítót a program kódjából és inaktiválni a virtuális környezetet.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Cognitive Services Python SDK-minták](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)

## <a name="see-also"></a>Lásd még

* [Azure Python SDK-referencia](/python/api/azure-cognitiveservices-search-websearch/)
