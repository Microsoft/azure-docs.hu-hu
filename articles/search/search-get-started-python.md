---
title: 'Gyors útmutató: keresési index létrehozása a Pythonban'
titleSuffix: Azure Cognitive Search
description: Megtudhatja, hogyan hozhat létre keresési indexet, tölthet be és futtathat lekérdezéseket Python, Jupyter Notebook és a Azure.Documents használatával. Keresés a Pythonhoz készült ügyféloldali kódtáraban.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2021
ms.custom: devx-track-python
ms.openlocfilehash: eb5de33fd41d3a454f4d0b8d44325ed30f9c5d47
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071630"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-python-using-jupyter-notebook"></a>Gyors útmutató: Azure Cognitive Search index létrehozása a Pythonban Jupyter Notebook használatával

> [!div class="op_single_selector"]
> * [Python](search-get-started-python.md)
> * [PowerShell (REST)](search-get-started-powershell.md)
> * [C#](search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Portál](search-get-started-portal.md)
>

Hozzon létre egy jegyzetfüzetet, amely létrehoz, betölt és lekérdez egy Azure Cognitive Search indexet a Python és az [Azure-Search-Documents Library](/python/api/overview/azure/search-documents-readme) használatával a Pythonhoz készült Azure SDK-ban. Ez a cikk bemutatja, hogyan hozhat létre egy jegyzetfüzetet lépésről lépésre. Azt is megteheti, hogy [letölti és futtatja a kész Jupyter Python notebookot](https://github.com/Azure-Samples/azure-search-python-samples).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következő szolgáltatások és eszközök szükségesek.

* [Anaconda 3. x](https://www.anaconda.com/distribution/#download-section), amely Python 3. x és Jupyter notebook.

* [Azure-Search – dokumentumok csomag](https://pypi.org/project/azure-search-documents/)

* [Hozzon létre egy keresési szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz az ingyenes szintet használhatja. 

## <a name="copy-a-key-and-url"></a>Kulcs és URL-cím másolása

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   ![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-rest/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-cognitive-search"></a>Kapcsolódás az Azure Cognitive Searchhoz

Ebben a feladatban indítsa el Jupyter Notebook és ellenőrizze, hogy tud-e csatlakozni az Azure Cognitive Searchhoz. Ezt úgy teheti meg, hogy az indexek listáját kéri le a szolgáltatásból. A Anaconda3-mel rendelkező Windows rendszeren a anaconda Navigator használatával indíthat el egy jegyzetfüzetet.

1. Hozzon létre egy új Python3 notebookot.

1. Az első cellában töltse be a kódtárakat a Pythonhoz készült Azure SDK-ból, beleértve az [Azure-Search-Documents](/python/api/azure-search-documents)elemet is.

   ```python
    !pip install azure-search-documents --pre
    !pip show azure-search-documents
    
    import os
    from azure.core.credentials import AzureKeyCredential
    from azure.search.documents.indexes import SearchIndexClient 
    from azure.search.documents import SearchClient
    from azure.search.documents.indexes.models import (
        ComplexField,
        CorsOptions,
        SearchIndex,
        ScoringProfile,
        SearchFieldDataType,
        SimpleField,
        SearchableField
    )
   ```

1. A második cellában adja meg azokat a kérelmeket, amelyek minden kérelemnél állandók lesznek. Adja meg a keresési szolgáltatás nevét, a felügyeleti API-kulcsot és a lekérdezési API-kulcsot, amelyet az előző lépésben másolt. Ez a cella az adott műveletekhez használt ügyfeleket is beállítja: [SearchIndexClient](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient) index létrehozásához és [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) az index lekérdezéséhez.

   ```python
    service_name = "YOUR-SEARCH-SERIVCE-NAME"
    admin_key = "YOUR-SEARCH-SERVICE-ADMIN-API-KEY"
    
    index_name = "hotels-quickstart"
    
    # Create an SDK client
    endpoint = "https://{}.search.windows.net/".format(service_name)
    admin_client = SearchIndexClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
    
    search_client = SearchClient(endpoint=endpoint,
                          index_name=index_name,
                          credential=AzureKeyCredential(admin_key))
   ```

1. A harmadik cellában futtasson egy delete_index műveletet, amellyel törölheti a szolgáltatását a meglévő *szállodákban –* gyors üzembe helyezési indexek. Az index törlése lehetővé teszi, hogy egy másik *Hotels-* rövid útmutató-indexet hozzon létre ugyanazon a néven.

   ```python
    try:
        result = admin_client.delete_index(index_name)
        print ('Index', index_name, 'Deleted')
    except Exception as ex:
        print (ex)
   ```

1. Futtassa az egyes lépéseket.

## <a name="1---create-an-index"></a>1 – Index létrehozása

Az index kötelező elemei közé tartozik a név, a mezők gyűjteménye és a kulcs. A mezők gyűjteménye határozza meg egy logikai *keresési dokumentum* szerkezetét, amely a betöltési és az eredmény-visszaküldési eredményekhez egyaránt használatos. 

Minden mező rendelkezik egy névvel, típussal és attribútummal, amely meghatározza a mező használatát (például hogy teljes szöveges kereshető, szűrhető vagy kereshető a keresési eredmények között). Egy indexen belül az egyik típusú mezőt `Edm.String` meg kell jelölni a dokumentum-identitás *kulcsaként* .

Az index neve "Hotels-Gyorsindítás", és az alább látható mező-definíciók szerepelnek. Ez egy nagyobb, más forgatókönyvekben használt [szállodák indexének](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) részhalmaza. Ebben a rövid útmutatóban lerövidítjük.

1. A következő cellában illessze be a következő példát egy cellába a séma megadásához.

    ```python
    # Specify the index schema
    name = index_name
    fields = [
            SimpleField(name="HotelId", type=SearchFieldDataType.String, key=True),
            SearchableField(name="HotelName", type=SearchFieldDataType.String, sortable=True),
            SearchableField(name="Description", type=SearchFieldDataType.String, analyzer_name="en.lucene"),
            SearchableField(name="Description_fr", type=SearchFieldDataType.String, analyzer_name="fr.lucene"),
            SearchableField(name="Category", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
        
            SearchableField(name="Tags", collection=True, type=SearchFieldDataType.String, facetable=True, filterable=True),
    
            SimpleField(name="ParkingIncluded", type=SearchFieldDataType.Boolean, facetable=True, filterable=True, sortable=True),
            SimpleField(name="LastRenovationDate", type=SearchFieldDataType.DateTimeOffset, facetable=True, filterable=True, sortable=True),
            SimpleField(name="Rating", type=SearchFieldDataType.Double, facetable=True, filterable=True, sortable=True),
    
            ComplexField(name="Address", fields=[
                SearchableField(name="StreetAddress", type=SearchFieldDataType.String),
                SearchableField(name="City", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="StateProvince", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="PostalCode", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
                SearchableField(name="Country", type=SearchFieldDataType.String, facetable=True, filterable=True, sortable=True),
            ])
        ]
    cors_options = CorsOptions(allowed_origins=["*"], max_age_in_seconds=60)
    scoring_profiles = []
    suggester = [{'name': 'sg', 'source_fields': ['Tags', 'Address/City', 'Address/Country']}]
    ```

1. Egy másik cellában alakítsa ki a kérelmet. Ez a create_index kérelem célja a keresési szolgáltatás indexek gyűjteménye, és egy [SearchIndex](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) hoz létre az előző cellában megadott index séma alapján.

    ```python
    index = SearchIndex(
        name=name,
        fields=fields,
        scoring_profiles=scoring_profiles,
        suggesters = suggester,
        cors_options=cors_options)
    
    try:
        result = admin_client.create_index(index)
        print ('Index', result.name, 'created')
    except Exception as ex:
        print (ex)
    ```

1. Futtassa az egyes lépéseket.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Dokumentumok betöltéséhez hozzon létre egy dokumentum-gyűjteményt a művelet típusához tartozó [index művelettel](/python/api/azure-search-documents/azure.search.documents.models.indexaction) (feltöltés, Egyesítés és feltöltés stb.). A dokumentumok a GitHubon lévő [HotelsData](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/HotelsData_toAzureSearch.JSON) származnak.

1. Egy új cellában négy olyan dokumentumot adjon meg, amelyek megfelelnek az index sémának. Minden dokumentumhoz meg kell adni egy feltöltési műveletet.

    ```python
    documents = [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": "false",
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel's restaurant services.",
        "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
            }
        },
        {
        "@search.action": "upload",
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": "true",
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    ```  

1. Egy másik cellában alakítsa ki a kérelmet. Ez a upload_documents kérelem célja a Hotels-rövid útmutató tárgymutatójának docs-gyűjteménye, és az előző lépésben megadott dokumentumok leküldése a Cognitive Search indexbe.

    ```python
    try:
        result = search_client.upload_documents(documents=documents)
        print("Upload of new document succeeded: {}".format(result[0].succeeded))
    except Exception as ex:
        print (ex.message)
    ```

1. Futtassa az egyes lépéseket a dokumentumok a keresési szolgáltatásban lévő indexbe való leküldéséhez.

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Ez a lépés bemutatja, hogyan kérdezheti le az indexeket a [keresési dokumentumok (REST)](/rest/api/searchservice/search-documents)használatával.

1. Ehhez a művelethez használja a search_client. Ez a lekérdezés egy üres keresést () hajt végre `search=*` , és nem rangsorolt listát (keresési pontszám = 1,0) ad vissza tetszőleges dokumentumokhoz. Mivel nincsenek feltételek, a rendszer az összes dokumentumot belefoglalja az eredményekbe. Ez a lekérdezés csak két mezőt nyomtat ki az egyes dokumentumokban. Emellett hozzáadja az `include_total_count=True` összes dokumentum (4) számát az eredményekben.

    ```python
    results =  search_client.search(search_text="*", include_total_count=True)
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. A következő lekérdezés teljes kifejezést ad hozzá a keresési kifejezéshez ("WiFi"). Ez a lekérdezés azt adja meg, hogy az eredmények csak az utasításban szereplő mezőket tartalmazzák `select` . A visszaadott mezők korlátozása csökkenti a huzalon visszaadott adatmennyiséget, és csökkenti a keresési késést.

    ```python
    results =  search_client.search(search_text="wifi", include_total_count=True, select='HotelId,HotelName,Tags')
    
    print ('Total Documents Matching Query:', results.get_count())
    for result in results:
        print("{}: {}: {}".format(result["HotelId"], result["HotelName"], result["Tags"]))
    ```

1. Ezután alkalmazzon egy szűrési kifejezést, és csak azokat a szállodákat adja vissza, amelyeknek a értéke 4, a csökkenő sorrendben rendezve.

    ```python
    results =  search_client.search(search_text="hotels", select='HotelId,HotelName,Rating', filter='Rating gt 4', order_by='Rating desc')
    
    for result in results:
        print("{}: {} - {} rating".format(result["HotelId"], result["HotelName"], result["Rating"]))
    ```

1. Hozzáadás `search_fields` a hatókör-lekérdezéshez, amely egyetlen mezőhöz illeszkedik.

    ```python
    results =  search_client.search(search_text="sublime", search_fields='HotelName', select='HotelId,HotelName')
    
    for result in results:
        print("{}: {}".format(result["HotelId"], result["HotelName"]))
    ```

1. Az aspektusok olyan címkék, amelyek dimenziós navigációs struktúra összeállítására használhatók. Ez a lekérdezés a kategória aspektusait és számát adja vissza.

    ```python
    results =  search_client.search(search_text="*", facets=["Category"])
    
    facets = results.get_facets()
    
    for facet in facets["Category"]:
        print("    {}".format(facet))
    ```

1. Ebben a példában egy adott dokumentumot keresünk a kulcsa alapján. Általában egy dokumentumot kell visszaadnia, amikor a felhasználó egy keresési eredményben lévő dokumentumra kattint.

    ```python
    result = search_client.get_document(key="3")
    
    print("Details for hotel '3' are:")
    print("Name: {}".format(result["HotelName"]))
    print("Rating: {}".format(result["Rating"]))
    print("Category: {}".format(result["Category"]))
    ```

1. Ebben a példában az automatikus kiegészítés funkciót fogjuk használni. Ezt általában egy keresőmező használja, hogy a felhasználó a keresőmezőbe írja be a lehetséges egyezések automatikus kiegészítését.

   Az index létrehozásakor a rendszer az "SG" nevű javaslatot is létrehozta a kérelem részeként. A javaslat definíciója határozza meg, hogy mely mezők használhatók a javaslatokra vonatkozó lehetséges egyezések kereséséhez. Ebben a példában ezek a mezők a következők: "Tags", "címe/város", "címe/ország". Az automatikus kiegészítés szimulálása érdekében a "SA" betűket adja át részleges karakterláncként. A [SearchClient](/python/api/azure-search-documents/azure.search.documents.searchclient) automatikus kiegészítési metódusa visszaküldi a lehetséges kifejezéseket.

    ```python
    search_suggestion = 'sa'
    results = search_client.autocomplete(search_text=search_suggestion, suggester_name="sg", mode='twoTerms')
    
    print("Autocomplete for:", search_suggestion)
    for result in results:
        print (result['text'])
    ```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Ennek egyszerűsítése érdekében ez a rövid útmutató a szállodák indexének rövidített verzióját használja. A teljes verziót a további érdekes lekérdezések kipróbálásához is létrehozhatja. A teljes verzió és az összes 50-dokumentum lekéréséhez futtassa az **adatok importálása** varázslót, és válassza a *Hotels-Sample* lehetőséget a beépített mintául szolgáló adatforrásokból.

> [!div class="nextstepaction"]
> [Gyors útmutató: index létrehozása a Azure Portalban](search-get-started-portal.md)