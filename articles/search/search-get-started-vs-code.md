---
title: 'Gyors útmutató: Azure Cognitive Search használatának első lépései a Visual Studio Code használatával'
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan telepítheti és használhatja a Visual Studio Code-bővítményt az Azure Cognitive Searchhoz.
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/10/2021
ms.openlocfilehash: 3237a32a90e3964644ff84958a065656cdf7f3ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015845"
---
# <a name="get-started-with-azure-cognitive-search-using-visual-studio-code"></a>Ismerkedés az Azure Cognitive Search a Visual Studio Code használatával

Ez a cikk azt ismerteti, hogyan lehet interaktív módon kialakítani REST API kérelmeket az [Azure Cognitive Search REST API](/rest/api/searchservice) -k és a [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)használatával. Az [Azure Cognitive Search (előzetes verzió) Visual Studio Code bővítménnyel](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) és ezekkel az utasításokkal bármilyen kód írása előtt elküldheti a kérelmeket, és megtekintheti a válaszokat.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!IMPORTANT] 
> Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következő szolgáltatások és eszközök szükségesek. 

+ [Visual Studio Code](https://code.visualstudio.com/download)

+ [Azure Cognitive Search a Visual Studio Code-hoz (előzetes verzió)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

## <a name="install-the-extension"></a>A bővítmény telepítése

Első lépésként nyissa meg a [vs Code](https://code.visualstudio.com)-ot. Válassza a **bővítmények** fület a tevékenység sávján, majd keressen rá az *Azure Cognitive Search* elemre. Keresse meg a bővítményt a keresési eredmények között, és válassza a **telepítés** lehetőséget.

![VS Code bővítmény panel](media/search-get-started-rest/download-extension.png "A VS Code bővítmény letöltése")

Azt is megteheti, hogy az [Azure Cognitive Search bővítményt](https://aka.ms/vscode-search) a vs Code piactérről is telepítheti egy böngészőben.

Ha még nem tette volna meg, megjelenik egy új Azure-lap a tevékenység sávján.

![VS Code Azure panel](media/search-get-started-rest/azure-pane.png "Azure-panel a VS Code-ban")

## <a name="connect-to-your-subscription"></a>Csatlakozás az előfizetéshez

Válassza a bejelentkezés az Azure-ba **..** . lehetőséget, és jelentkezzen be az Azure-fiókjába.

Ekkor megjelenik az előfizetések. Válassza ki az előfizetést az előfizetésben található keresési szolgáltatások listájának megtekintéséhez.

![VS Code Azure-előfizetések](media/search-get-started-rest/subscriptions.png "Előfizetések a VS Code-ban")

A megjelenő előfizetések korlátozásához nyissa meg a parancssort (CTRL + SHIFT + P vagy cmd + SHIFT + P), és keressen az *Azure* -ra, vagy *válassza az előfizetések lehetőséget*. Az Azure-fiókkal való bejelentkezéshez és az onnan való kijelentkezéshez is elérhetők parancsok.

A keresési szolgáltatás kibontásakor a rendszer faelemeket fog látni az egyes Cognitive Search erőforrásokhoz: indexek, adatforrások, indexelő elemek, szakértelmével és szinonima térképek.

![VS Code Azure keresési fa](media/search-get-started-rest/search-tree.png "VS Code Azure keresési fa")

Ezek a faelemek kiterjeszthetők a keresési szolgáltatásban található erőforrások megjelenítéséhez

## <a name="1---create-an-index"></a>1 – Index létrehozása

Az Azure Cognitive Search megkezdéséhez először létre kell hoznia egy keresési indexet. Ezt a [create Index REST API](/rest/api/searchservice/create-index)használatával végezheti el. 

A VS Code bővítménnyel csak a kérés törzsére kell aggódnia. Ebben a rövid útmutatóban egy minta index-definíciót és megfelelő dokumentumokat biztosítunk.

### <a name="index-definition"></a>Index definíciója

Az alábbi index-definíció a fiktív szállodákhoz tartozó minta séma.

A `fields` gyűjtemény a dokumentumok szerkezetét határozza meg a keresési indexben. Az egyes mezők adattípussal és számos további attribútummal rendelkeznek, amelyek meghatározzák a mező használatának módját.

```json
{
    "name": "hotels-quickstart",
    "fields": [
        {
            "name": "HotelId",
            "type": "Edm.String",
            "key": true,
            "filterable": true
        },
        {
            "name": "HotelName",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": true,
            "facetable": false
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "en.lucene"
        },
        {
            "name": "Description_fr",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "sortable": false,
            "facetable": false,
            "analyzer": "fr.lucene"
        },
        {
            "name": "Category",
            "type": "Edm.String",
            "searchable": true,
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Tags",
            "type": "Collection(Edm.String)",
            "searchable": true,
            "filterable": true,
            "sortable": false,
            "facetable": true
        },
        {
            "name": "ParkingIncluded",
            "type": "Edm.Boolean",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "LastRenovationDate",
            "type": "Edm.DateTimeOffset",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Rating",
            "type": "Edm.Double",
            "filterable": true,
            "sortable": true,
            "facetable": true
        },
        {
            "name": "Address",
            "type": "Edm.ComplexType",
            "fields": [
                {
                    "name": "StreetAddress",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true
                },
                {
                    "name": "City",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "StateProvince",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "PostalCode",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
                {
                    "name": "Country",
                    "type": "Edm.String",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                }
            ]
        }
    ],
    "suggesters": [
        {
            "name": "sg",
            "searchMode": "analyzingInfixMatching",
            "sourceFields": [
                "HotelName"
            ]
        }
    ]
}
```

Új index létrehozásához kattintson a jobb gombbal az **indexek** elemre, majd válassza az **új index létrehozása** lehetőséget. Egy hasonló nevű szerkesztő jelenik meg `indexes-new-28c972f661.azsindex` . 

Illessze be a fenti index-definíciót az ablakba. Mentse a fájlt, és válassza a **feltöltés** lehetőséget, ha a rendszer rákérdez, hogy szeretné-e frissíteni az indexet. Ezzel létrehozza az indexet, és elérhető lesz a fanézetben.

![Index létrehozásával kapcsolatos GIF](media/search-get-started-rest/create-index.gif)

Ha probléma merül fel az index definíciójában, megjelenik egy hibaüzenet, amely leírja a hibát.

![Tárgymutató-hibaüzenet létrehozása](media/search-get-started-rest/create-index-error.png)

Ha ez történik, javítsa ki a problémát, és mentse újra a fájlt.

## <a name="2---load-documents"></a>2 – dokumentumok betöltése

Az index létrehozása és adatokkal való feltöltése két különböző lépés. Az Azure Cognitive Searchban az index tartalmazza az összes kereshető adatértéket. Ebben a forgatókönyvben az információk JSON-dokumentumként vannak megadva. Ehhez a feladathoz a [Hozzáadás, frissítés vagy törlés dokumentumok REST API](/rest/api/searchservice/addupdate-or-delete-documents) használják. 

Új dokumentumok hozzáadása a VS Code-ban:

1. Bontsa ki a `hotels-quickstart` létrehozott indexet. Kattintson a jobb gombbal a **dokumentumok** elemre, és válassza az **új dokumentum létrehozása** lehetőséget.

    ![Dokumentum létrehozása](media/search-get-started-rest/create-document.png)

2. Ekkor megnyílik egy olyan JSON-szerkesztő, amely az index sémáját kikövetkeztette.

    ![Dokumentum létrehozása JSON](media/search-get-started-rest/create-document-2.png)

3. Illessze be az alábbi JSON-t, majd mentse a fájlt. Ekkor megnyílik egy üzenet, amely felszólítja, hogy erősítse meg a módosításokat. A módosítások mentéséhez válassza a **feltöltés** lehetőséget.

    ```json
    {
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
        "Category": "Boutique",
        "Tags": [ "pool", "air conditioning", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1970-01-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
        } 
    }
    ```

4. Ismételje meg ezt a folyamatot a három fennmaradó dokumentum esetében

    2. dokumentum:
    ```json
    {
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
        "Category": "Boutique",
        "Tags": [ "pool", "free wifi", "concierge" ],
        "ParkingIncluded": false,
        "LastRenovationDate": "1979-02-18T00:00:00Z",
        "Rating": 3.60,
        "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
        } 
    }
    ```

    3. dokumentum:
    ```json
    {
        "HotelId": "3",
        "HotelName": "Triple Landscape Hotel",
        "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
        "Category": "Resort and Spa",
        "Tags": [ "air conditioning", "bar", "continental breakfast" ],
        "ParkingIncluded": true,
        "LastRenovationDate": "2015-09-20T00:00:00Z",
        "Rating": 4.80,
        "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
        } 
    }
    ```

    4. dokumentum:
    ```json
    {
        "HotelId": "4",
        "HotelName": "Sublime Cliff Hotel",
        "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
        "Category": "Boutique",
        "Tags": [ "concierge", "view", "24-hour front desk service" ],
        "ParkingIncluded": true,
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
    ```

Ezen a ponton a dokumentumok szakaszban mind a négy dokumentumot meg kell tekinteni.

![az összes dokumentum feltöltésének állapota](media/search-get-started-rest/create-document-finish.png)

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Most, hogy az index és a dokumentumkészlet be van töltve, a [keresési dokumentumok REST API](/rest/api/searchservice/search-documents)használatával küldhet lekérdezéseket.

Ehhez a VS Code-ban:

1. Kattintson a jobb gombbal a keresni kívánt indexre, és válassza a **keresési index** lehetőséget. Ekkor megnyílik egy hasonló nevű szerkesztő `sandbox-b946dcda48.azs` .

    ![bővítmény keresési nézete](media/search-get-started-rest/search-vscode.png)

2. Egy egyszerű lekérdezés automatikusan feltöltve. A lekérdezés elküldéséhez nyomja le a **CTRL + ALT + r** vagy a **cmd + Alt + r** billentyűkombinációt. Ekkor megjelenik az eredmények egy ablakban a bal oldalon.

    ![keresési eredmények a bővítményben](media/search-get-started-rest/search-results.png)


### <a name="example-queries"></a>Példa a lekérdezésekre

Néhány további lekérdezési példát is kipróbálhat a szintaxis megszerzéséhez. Az alábbi négy további lekérdezéssel próbálkozhat. Több lekérdezést is hozzáadhat ugyanahhoz a szerkesztőhöz. Ha lenyomja a **CTRL + ALT + r** vagy a **cmd + Alt + r** billentyűkombinációt, a kurzor határozza meg, hogy melyik lekérdezést küldje el a rendszer.

![lekérdezések és eredmények egymás mellé](media/search-get-started-rest/all-searches.png)

Az első lekérdezésben `boutique` `select` csak bizonyos mezőkben keresünk. Az ajánlott eljárás csak `select` a szükséges mezőkre vonatkozik, mert a szükségtelen adat visszahúzásával késleltetést adhat a lekérdezésekhez. A lekérdezés azt is beállítja `$count=true` , hogy az eredmények teljes számát adja vissza a keresési eredményekkel együtt.

```
// Query example 1 - Search `boutique` with select and return count
search=boutique&$count=true&$select=HotelId,HotelName,Rating,Category
```

A következő lekérdezésben megadjuk a keresési kifejezést, `wifi` és egy szűrőt is tartalmaznak, amely csak azokat az eredményeket adja vissza, amelyekben az állapot egyenlő `'FL'` . Az eredményeket a szálloda is megrendezi `Rating` .

```
// Query example 2 - Search with filter, orderBy, select, and count
search=wifi&$filter=Address/StateProvince eq 'FL'&$select=HotelId,HotelName,Rating&$orderby=Rating desc
```

Ezt követően a keresés egyetlen kereshető mezőre korlátozódik a `searchFields` paraméter használatával. Ez nagyszerű lehetőség a lekérdezés hatékonyabbá tételére, ha tudja, hogy csak bizonyos mezőkben lévő egyezések érdeklik.

```
// Query example 3 - Limit searchFields
search=submlime cliff&$select=HotelId,HotelName,Rating&searchFields=HotelName
```

Egy másik gyakori lehetőség a lekérdezésbe való felvételre `facets` . A dimenziók lehetővé teszik szűrők kiépítését a felhasználói felületen, hogy a felhasználók könnyebben tudják, milyen értékeket tudnak szűrni.

```
// Query example 4 - Take the top two results, and show only HotelName and Category in the results
search=*&$select=HotelId,HotelName,Rating&searchFields=HotelName&facet=Category
```

## <a name="open-index-in-the-portal"></a>Index megnyitása a portálon

Ha a keresési szolgáltatást a portálon szeretné megtekinteni, kattintson a jobb gombbal a keresési szolgáltatás nevére, és válassza a **Megnyitás a portálon** lehetőséget. Ekkor megnyílik a Azure Portal keresési szolgáltatása.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan hajthat végre alapszintű feladatokat, további REST API hívásokat is megadhat további speciális funkciókhoz, például indexelő elemekhez, vagy [egy olyan dúsítási folyamat beállításához](cognitive-search-tutorial-blob.md) , amely tartalom-átalakításokat ad át az indexeléshez. A következő lépésként a következő hivatkozást javasoljuk:

> [!div class="nextstepaction"]
> [Oktatóanyag: az Azure-Blobok kereshető tartalmának létrehozásához használja a REST és a AI használatát](cognitive-search-tutorial-blob.md)