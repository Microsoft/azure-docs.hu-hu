---
title: 'Gyors útmutató: keresési index létrehozása a PowerShellben REST API-k használatával'
titleSuffix: Azure Cognitive Search
description: Ebben a REST API útmutatóban megtudhatja, hogyan hozhat létre indexet, tölthet be és hogyan futtathat lekérdezéseket a PowerShell Invoke-RestMethod és az Azure Cognitive Search REST API használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9dd9de9dcb01e9be200e07e5925d8b856432b620
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94742378"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-powershell-using-rest-apis"></a>Rövid útmutató: Azure Cognitive Search index létrehozása a PowerShellben REST API-k használatával
> [!div class="op_single_selector"]
> * [PowerShell (REST)]()
> * [C#](./search-get-started-dotnet.md)
> * [REST](search-get-started-rest.md)
> * [Python](search-get-started-python.md)
> * [Portál](search-get-started-portal.md)
> 

Ez a cikk végigvezeti egy Azure Cognitive Search index létrehozásának, betöltésének és lekérdezésének lépésein a PowerShell és az [azure Cognitive Search REST API](/rest/api/searchservice/)-k használatával. Ez a cikk azt ismerteti, hogyan lehet interaktív módon futtatni a PowerShell-parancsokat. Azt is megteheti, hogy [letölti és futtat egy PowerShell-parancsfájlt](https://github.com/Azure-Samples/azure-search-powershell-samples/tree/master/Quickstart) , amely ugyanezeket a műveleteket hajtja végre.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következő szolgáltatások és eszközök szükségesek. 

+ [PowerShell 5,1 vagy újabb](https://github.com/PowerShell/PowerShell), a [Meghívási-RestMethod](/powershell/module/Microsoft.PowerShell.Utility/Invoke-RestMethod) használatával szekvenciális és interaktív lépésekhez.

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

## <a name="copy-a-key-and-url"></a>Kulcs és URL-cím másolása

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

2. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-rest/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-cognitive-search"></a>Kapcsolódás az Azure Cognitive Searchhoz

1. A PowerShellben hozzon létre egy **$headers** objektumot a Content-Type és az API-kulcs tárolásához. Cserélje le a felügyeleti API-kulcsot (a-ADMIN-API-KEY) egy olyan kulccsal, amely érvényes a keresési szolgáltatáshoz. Ezt a fejlécet csak egyszer kell beállítania a munkamenet időtartamára, de minden kérelemhez hozzá kell adnia. 

    ```powershell
    $headers = @{
    'api-key' = '<YOUR-ADMIN-API-KEY>'
    'Content-Type' = 'application/json' 
    'Accept' = 'application/json' }
    ```

2. Hozzon létre egy **$URL** objektumot, amely meghatározza a szolgáltatás indexeit tartalmazó gyűjteményt. Cserélje le a szolgáltatás nevét (a-SEARCH-SERVICE-NAME) egy érvényes keresési szolgáltatással.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name"
    ```

3. Futtassa a **meghívó-RestMethod** parancsot, hogy küldjön egy Get kérelmet a szolgáltatásnak, és ellenőrizze a kapcsolódást. Adja hozzá a **ConvertTo-JSON-** t, hogy megtekintse a szolgáltatásból visszaküldött válaszokat.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

   Ha a szolgáltatás üres, és nem rendelkezik indexekkel, az eredmények az alábbi példához hasonlóak. Ellenkező esetben az index-definíciók JSON-ábrázolását fogja látni.

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes",
        "value":  [

                ]
    }
    ```

## <a name="1---create-an-index"></a>1 – Index létrehozása

Ha nem használja a portált, akkor az adatgyűjtés előtt léteznie kell egy indexnek a szolgáltatáson. Ez a lépés határozza meg az indexet, és leküldi azt a szolgáltatásnak. Ehhez a lépéshez a [create Index REST API](/rest/api/searchservice/create-index) van használatban.

Az index kötelező elemei közé tartozik a név és a mezők gyűjteménye. A mezők gyűjteménye meghatározza a *dokumentumok* szerkezetét. Minden mező rendelkezik egy névvel, típussal és attribútummal, amely meghatározza, hogyan használják a rendszer (például hogy teljes szöveges kereshető, szűrhető vagy kereshető a keresési eredmények között). Egy indexen belül az egyik típusú mezőt `Edm.String` meg kell jelölni a dokumentum-identitás *kulcsaként* .

Az index neve "Hotels-Gyorsindítás", és az alább látható mező-definíciók szerepelnek. Ez egy nagyobb, az útmutatóban szereplő cikkekből álló [index](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotels/Hotels_IndexDefinition.JSON) Ebben a rövid útmutatóban a mezők definíciói a rövid útmutatóhoz lettek kimetszve.

1. Illessze be a példát a PowerShellbe, és hozzon létre egy **$Body** objektumot, amely tartalmazza az index sémát.

    ```powershell
    $body = @"
    {
        "name": "hotels-quickstart",  
        "fields": [
            {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
            {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
            {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
            {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
            {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
            {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
            {"name": "Address", "type": "Edm.ComplexType", 
            "fields": [
            {"name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true},
            {"name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
            {"name": "Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true}
            ]
         }
      ]
    }
    "@
    ```

2. Állítsa be az URI-t a szolgáltatás indexek gyűjteményére és a *Hotels-Gyorsindítás* indexre.

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30"
    ```

3. Futtassa a parancsot **$URL**, **$headers** és **$Body** használatával az index létrehozásához a szolgáltatásban. 

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
    ```

    Az eredményeknek a következőhöz hasonlóan kell kinéznie (rövidítve az első két mezőnél):

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag":  "\"0x8D6EDE28CFEABDA\"",
        "name":  "hotels-quickstart",
        "defaultScoringProfile":  null,
        "fields":  [
                    {
                        "name":  "HotelId",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  true,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  true,
                        "key":  true,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
                    {
                        "name":  "HotelName",
                        "type":  "Edm.String",
                        "searchable":  true,
                        "filterable":  false,
                        "retrievable":  true,
                        "sortable":  true,
                        "facetable":  false,
                        "key":  false,
                        "indexAnalyzer":  null,
                        "searchAnalyzer":  null,
                        "analyzer":  null,
                        "synonymMaps":  ""
                    },
    . . .
    ```

> [!Tip]
> Az ellenőrzéshez az indexek listáját is megtekintheti a portálon.

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

A dokumentumok leküldéséhez használjon HTTP POST-kérést az index URL-címének végpontján. A feladathoz tartozó REST API [dokumentumok hozzáadása, frissítése vagy törlése](/rest/api/searchservice/addupdate-or-delete-documents).

1. Illessze be a példát a PowerShellbe, és hozzon létre egy **$Body** objektumot, amely tartalmazza a feltölteni kívánt dokumentumokat. 

    Ez a kérelem két teljes és egy részleges rekordot tartalmaz. A részleges rekord azt mutatja be, hogy nem teljes dokumentumokat tölthet fel. A `@search.action` paraméter határozza meg, hogyan történjen az indexelés. Az érvényes értékek közé tartozik a feltöltés, az egyesítés, a mergeOrUpload és a törlés. A mergeOrUpload viselkedése vagy létrehoz egy új dokumentumot a hotelId = 3 számára, vagy frissíti a tartalmat, ha az már létezik.

    ```powershell
    $body = @"
    {
        "value": [
        {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
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
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": 
            {
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
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": 
            {
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
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "1960-02-06T00:00:00Z",
        "Rating": 4.60,
        "Address": 
            {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
            }
        }
    ]
    }
    "@
    ```

1. Állítsa a végpontot a *hotelek –* gyors dokumentumok gyűjteményre, és adja meg az index műveletet (indexek/Hotels-Gyorsindítás/docs/index).

    ```powershell
    $url = "https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30"
    ```

1. Futtassa a parancsot **$URL**, **$headers** és **$Body** használatával a dokumentumok betöltéséhez a Hotels-Gyorsindítás indexbe.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers -Method Post -Body $body | ConvertTo-Json
    ```
    Az eredményeknek az alábbi példához hasonlóan kell kinéznie. Ekkor meg kell jelennie a 201-as [állapotkódot](/rest/api/searchservice/HTTP-status-codes).

    ```
    {
        "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#Collection(Microsoft.Azure.Search.V2019_05_06.IndexResult)",
        "value":  [
                    {
                        "key":  "1",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "2",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "3",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    },
                    {
                        "key":  "4",
                        "status":  true,
                        "errorMessage":  null,
                        "statusCode":  201
                    }
                ]
    }
    ```

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Ez a lépés bemutatja, hogyan kérdezheti le az indexeket a [Search Documents API](/rest/api/searchservice/search-documents)használatával.

Ügyeljen arra, hogy egyetlen idézőjelet használjon a keresési $urls. A lekérdezési karakterláncok tartalmazhatnak **$** karaktereket, és kihagyhatják, hogy a teljes karakterlánc egyetlen idézőjelbe van-e zárva.

1. Állítsa a végpontot a *Hotels-* gyors dokumentumok gyűjteményre, és adjon hozzá egy **keresési** paramétert egy lekérdezési karakterláncba való továbbításhoz. 
  
   Ez a karakterlánc egy üres keresést hajt végre (Search = *), és nem rangsorolt listát (keresési pontszám = 1,0) ad vissza tetszőleges dokumentumokhoz. Alapértelmezés szerint az Azure Cognitive Search a 50-es egyezést adja vissza egyszerre. Strukturált módon a lekérdezés egy teljes dokumentum-struktúrát és-értéket ad vissza. Adja hozzá a **$Count = True** értéket az eredményekben található összes dokumentum számának beolvasásához.

    ```powershell
    $url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$count=true'
    ```

1. Futtassa a parancsot a **$URL** a szolgáltatásnak való elküldéséhez.

    ```powershell
    Invoke-RestMethod -Uri $url -Headers $headers | ConvertTo-Json
    ```

    Az eredményeknek az alábbi kimenethez hasonlóan kell kinéznie.

    ```
    {
    "@odata.context":  "https://mydemo.search.windows.net/indexes(\u0027hotels-quickstart\u0027)/$metadata#docs(*)",
    "@odata.count":  4,
    "value":  [
                  {
                      "@search.score":  0.1547872,
                      "HotelId":  "2",
                      "HotelName":  "Twin Dome Motel",
                      "Description":  "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                      "Category":  "Boutique",
                      "Tags":  "pool free wifi concierge",
                      "ParkingIncluded":  false,
                      "LastRenovationDate":  "1979-02-18T00:00:00Z",
                      "Rating":  3.6,
                      "Address":  "@{StreetAddress=140 University Town Center Dr; City=Sarasota; StateProvince=FL; PostalCode=34243; Country=USA}"
                  },
                  {
                      "@search.score":  0.009068266,
                      "HotelId":  "3",
                      "HotelName":  "Triple Landscape Hotel",
                      "Description":  "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel\u0027s restaurant services.",
                      "Category":  "Resort and Spa",
                      "Tags":  "air conditioning bar continental breakfast",
                      "ParkingIncluded":  true,
                      "LastRenovationDate":  "2015-09-20T00:00:00Z",
                      "Rating":  4.8,
                      "Address":  "@{StreetAddress=3393 Peachtree Rd; City=Atlanta; StateProvince=GA; PostalCode=30326; Country=USA}"
                  },
                . . . 
    ```

Néhány további lekérdezési példát is kipróbálhat a szintaxis megszerzéséhez. Megteheti a karakterláncos keresést, a Verbatim $filter lekérdezéseket, korlátozhatja az eredmények készletét, kihasználhatja a keresést adott mezőkre, és így tovább.

```powershell
# Query example 1
# Search the entire index for the terms 'restaurant' and 'wifi'
# Return only the HotelName, Description, and Tags fields
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=restaurant wifi&$count=true&$select=HotelName,Description,Tags'

# Query example 2 
# Apply a filter to the index to find hotels rated 4 or higher
# Returns the HotelName and Rating. Two documents match.
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=*&$filter=Rating gt 4&$select=HotelName,Rating'

# Query example 3
# Take the top two results, and show only HotelName and Category in the results
$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=boutique&$top=2&$select=HotelName,Category'

# Query example 4
# Sort by a specific field (Address/City) in ascending order

$url = 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?api-version=2020-06-30&search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating'
```
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a PowerShellt használta a tartalom Azure Cognitive Searchban való létrehozásához és eléréséhez szükséges alapszintű munkafolyamathoz. A fogalmakat figyelembe véve javasoljuk, hogy olyan speciális forgatókönyvekre váltson át, mint például az Azure-adatforrásokból való indexelés.

> [!div class="nextstepaction"]
> [REST-oktatóanyag: részben strukturált adatok (JSON-Blobok) indexelése és keresése az Azure Cognitive Search](search-semi-structured-data.md)
