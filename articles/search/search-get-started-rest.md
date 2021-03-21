---
title: 'Gyors útmutató: keresési index létrehozása REST API-k használatával'
titleSuffix: Azure Cognitive Search
description: Ebből a REST API útmutatóból megtudhatja, hogyan hívhatja meg az Azure Cognitive Search REST API-kat a Poster vagy a Visual Studio Code használatával.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: b701a80c3c3b7e5a558d875aca2fb34aea89edff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98119628"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Rövid útmutató: Azure Cognitive Search index létrehozása REST API-k használatával

Ez a cikk azt ismerteti, hogyan lehet interaktív módon kialakítani REST API kérelmeket az [Azure Cognitive Search REST API](/rest/api/searchservice) -kkal és egy API-ügyféllel a kérelmek küldéséhez és fogadásához. Az API-ügyféllel és ezekkel az utasításokkal bármilyen kód írása előtt elküldheti a kérelmeket, és megtekintheti a válaszokat.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

A cikk a Poster Desktop alkalmazást használja. Ha előre definiált kéréseket szeretne használni, [letöltheti és importálhatja a Poster-gyűjteményeket](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) .

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következő szolgáltatások és eszközök szükségesek. 

+ A [Poster Desktop alkalmazás](https://www.getpostman.com/) a kérelmek Azure-Cognitive Search való küldésére szolgál.

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

## <a name="copy-a-key-and-url"></a>Kulcs és URL-cím másolása

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-rest/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="connect-to-azure-cognitive-search"></a>Kapcsolódás az Azure Cognitive Searchhoz

Ebben a szakaszban a saját webes eszközével állíthatja be az Azure Cognitive Search kapcsolatait. Minden eszköz megőrzi a kérelmek fejlécére vonatkozó információkat a munkamenethez, ami azt jelenti, hogy csak egyszer kell megadnia az API-kulcs és a Content-Type típust.

Bármelyik eszközhöz ki kell választania egy parancsot (GET, POST, PUT stb.), meg kell adnia egy URL-végpontot, és egyes feladatokhoz meg kell adni a JSON-t a kérelem törzsében. Cserélje le a keresési szolgáltatás nevét (a-SEARCH-SERVICE-NAME) érvényes értékre. A Hozzáadás `$select=name` gombra kattintva csak az egyes indexek nevét adja vissza. 

> `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes?api-version=2020-06-30&$select=name`

Figyelje meg a HTTPS-előtagot, a szolgáltatás nevét, az objektum nevét (ebben az esetben az indexek gyűjteményét) és az [API-verziót](search-api-versions.md). Az API-Version egy kötelező, kisbetűs karakterlánc, amely `?api-version=2020-06-30` a jelenlegi verzióhoz hasonlóan van megadva. Az API-verziókat a rendszer rendszeresen frissíti. Az API-verziót minden kérelemnél adja meg, hogy teljes mértékben szabályozhassa, a rendszer mikor melyik verziót használja.  

A kérelem fejlécének összetétele két elemet tartalmaz: `Content-Type` az `api-key` Azure Cognitive Search való hitelesítéshez használt. Cserélje le a felügyeleti API-kulcsot (az-AZURE-SEARCH-ADMIN-API-KEY) érvényes értékre. 

```http
api-key: <YOUR-AZURE-SEARCH-ADMIN-API-KEY>
Content-Type: application/json
```

A Poster-ben állítson össze egy, az alábbi képernyőképen láthatóhoz hasonló kérelmet. Válassza a **lekérés** parancsot, adja meg az URL-címet, majd kattintson a **Küldés** gombra. Ez a parancs csatlakozik az Azure Cognitive Searchhoz, beolvassa az indexek gyűjteményét, és az 200-as HTTP-állapotkódot egy sikeres kapcsolaton keresztül adja vissza. Ha a szolgáltatás már rendelkezik indexekkel, a válasz tartalmazni fog index-definíciókat is.

![Poster-kérelem URL-címe és fejléce](media/search-get-started-rest/postman-url.png "Poster-kérelem URL-címe és fejléce")

## <a name="1---create-an-index"></a>1 – Index létrehozása

Az Azure Cognitive Search általában az indexet hozza létre az adatbevitel előtt. Ehhez a feladathoz a [create Index REST API](/rest/api/searchservice/create-index) van használatban. 

Az URL-cím kiterjeszthető, hogy tartalmazza az `hotels` index nevét.

Ehhez tegye a következőket:

1. Módosítsa a parancsot a **put** értékre.

2. Másolás ebben az URL-ben `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart?api-version=2020-06-30` .

3. Adja meg az index definícióját (a másolásra kész kód a lenti) a kérelem törzsében.

4. Kattintson a **Küldés** gombra.

![JSON-dokumentum indexelése a kérelem törzsében](media/search-get-started-rest/postman-request.png "JSON-dokumentum indexelése a kérelem törzsében")

### <a name="index-definition"></a>Index definíciója

A mezők gyűjteménye határozza meg a dokumentum szerkezetét. Minden dokumentumnak tartalmaznia kell ezeket a mezőket, és minden mezőnek adattípussal kell rendelkeznie. A karakterlánc mezők teljes szöveges keresésben használatosak. Ha numerikus adatkeresésre van szüksége, a numerikus értékeket karakterláncként kell bevezetni.

Az egyes mezők attribútumai határozzák meg az engedélyezett műveleteket. A REST API-k alapértelmezés szerint számos műveletet lehetővé tesznek. Például alapértelmezés szerint minden sztring kereshető, lekérdezhető, szűrhető és kategorizálható. Gyakran csak akkor kell attribútumokat beállítania, ha ki kell kapcsolni a viselkedést.

```json
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
```

A kérelem elküldését követően megjelenik a 201-es HTTP-válasz, amely azt jelzi, hogy az index sikeresen létrejött. A műveletet ellenőrizheti a portálon, azonban vegye figyelembe, hogy a portáloldal frissítési időköze miatt esetleg egy-két percet várakoznia kell.

> [!TIP]
> Ha 504-es HTTP-választ kap, ellenőrizze, hogy az URL-címben a HTTPS előtag van-e megadva. Ha 400-as vagy 404-es HTTP-válasz jelenik meg, akkor ellenőrizze, hogy a kérelem törzsében nincsenek-e beillesztési hibák. A 403-as HTTP-válasz általában az API-kulccsal kapcsolatos hibát jelez (érvénytelen a kulcs vagy szintaktikai hiba van az API-kulcs meghatározásában).

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Az index létrehozása és adatokkal való feltöltése két különböző lépés. Az Azure Cognitive Searchban az index tartalmazza az összes kereshető adatértéket. Ebben a forgatókönyvben az információk JSON-dokumentumként vannak megadva. Ehhez a feladathoz a [Hozzáadás, frissítés vagy törlés dokumentumok REST API](/rest/api/searchservice/addupdate-or-delete-documents) használják. 

Az URL-cím kiterjeszthető a `docs` gyűjtemények és a művelet belefoglalására `index` .

Ehhez tegye a következőket:

1. Módosítsa a parancsot **post** értékre.

2. Másolás ebben az URL-ben `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs/index?api-version=2020-06-30` .

3. Adja meg a kérelem törzsében a JSON-dokumentumokat (a másolásra kész kód alatt).

4. Kattintson a **Küldés** gombra.

![JSON-dokumentumok a kérelem törzsében](media/search-get-started-rest/postman-docs.png "JSON-dokumentumok a kérelem törzsében")

### <a name="json-documents-to-load-into-the-index"></a>Az indexbe betöltendő JSON-dokumentumok

A kérelem törzse négy, a szállodák indexéhez hozzáadandó dokumentumot tartalmaz.

```json
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
```

Néhány másodpercen belül megjelenik egy HTTP 201-válasz a munkamenetek listájában. Ez azt jelenti, hogy a dokumentumok sikeresen létrejöttek. 

Ha a 207-es HTTP-válasz jelenik meg, legalább egy dokumentumot nem sikerült feltölteni. Ha a 404-es válasz jelenik meg, akkor a kérelem fejlécében vagy törzsében szintaktikai hiba van: ellenőrizze, hogy valóban módosította-e a végpontot, hogy tartalmazza a `/docs/index` útvonalat.

> [!Tip]
> A kiválasztott adatforrások esetében választhatja az alternatív *indexelő* megközelítést, amely egyszerűbb, és csökkenti az indexáláshoz szükséges kódot. További információkért lásd: [Indexelőműveletek](/rest/api/searchservice/indexer-operations).


## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Most, hogy az index és a dokumentumkészlet be van töltve, a [keresési dokumentumok REST API](/rest/api/searchservice/search-documents)használatával küldhet lekérdezéseket.

Az URL-cím kiterjesztése a keresési operátor használatával megadott lekérdezési kifejezésre.

Ehhez tegye a következőket:

1. Módosítsa a parancsot a **lekéréshez**.

2. Másolás ebben az URL-ben `https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$count=true&api-version=2020-06-30` .

3. Kattintson a **Küldés** gombra.

Ez a lekérdezés üres, és a keresési eredményekben szereplő dokumentumok számát adja vissza. A kérésnek és a válasznak a Poster következő képernyőképéhez hasonlóan kell kinéznie, miután rákattintott a **Send (Küldés**) gombra. Az állapotkódnak 200-nak kell lennie.

 ![Keresési karakterlánc beolvasása az URL-címen](media/search-get-started-rest/postman-query.png "Keresési karakterlánc beolvasása az URL-címen")

Néhány további lekérdezési példát is kipróbálhat a szintaxis megszerzéséhez. Megteheti a karakterláncos keresést, a Verbatim $filter lekérdezéseket, korlátozhatja az eredmények készletét, kihasználhatja a keresést adott mezőkre, és így tovább.

A jelenlegi URL-címet cserélje le az alábbira, majd kattintson a **Küldés** minden alkalommal elemre az eredmények megtekintéséhez.

```
# Query example 1 - Search on restaurant and wifi
# Return only the HotelName, Description, and Tags fields
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=restaurant wifi&$count=true&$select=HotelName,Description,Tags&api-version=2020-06-30

# Query example 2 - Apply a filter to the index to find hotels rated 4 or highter
# Returns the HotelName and Rating. Two documents match
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=*&$filter=Rating gt 4&$select=HotelName,Rating&api-version=2020-06-30

# Query example 3 - Take the top two results, and show only HotelName and Category in the results
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=boutique&$top=2&$select=HotelName,Category&api-version=2020-06-30

# Query example 4 - Sort by a specific field (Address/City) in ascending order
https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/hotels-quickstart/docs?search=pool&$orderby=Address/City asc&$select=HotelName, Address/City, Tags, Rating&api-version=2020-06-30
```

## <a name="get-index-properties"></a>Index tulajdonságainak beolvasása

A [lekérési statisztikákat](/rest/api/searchservice/get-index-statistics) is használhatja a dokumentumok számának és az index méretének lekérdezéséhez: 

```http
https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/indexes/hotels-quickstart/stats?api-version=2020-06-30
```

Az `/stats` URL-címhez való Hozzáadás az index adatait adja vissza. A Postmanben a kérelem az alábbihoz fog hasonlítani, a válaszban pedig szerepleni fog a dokumentumok száma és a felhasznált lemezterület mérete bájtban megadva.

 ![Index információinak lekérése](media/search-get-started-rest/postman-system-query.png "Index információinak lekérése")

Figyelje meg, hogy az api-version szintaxisa eltér. Ebben a kérelemben a `?` karakterrel fűzheti hozzá az api-version paramétert. A `?` leválasztja az URL-útvonalat a lekérdezési karakterláncból, míg & elválasztja az egyes "Name = Value" párokat a lekérdezési karakterláncban. Ebben a lekérdezésben az api-version az első és egyetlen lekérdezési sztring.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan hajthat végre alapszintű feladatokat, további REST API hívásokat is megadhat további speciális funkciókhoz, például indexelő elemekhez, vagy [egy olyan dúsítási folyamat beállításához](cognitive-search-tutorial-blob.md) , amely tartalom-átalakításokat ad át az indexeléshez. A következő lépésként a következő hivatkozást javasoljuk:

> [!div class="nextstepaction"]
> [Oktatóanyag: az Azure-Blobok kereshető tartalmának létrehozásához használja a REST és a AI használatát](cognitive-search-tutorial-blob.md)