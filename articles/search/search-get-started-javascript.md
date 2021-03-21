---
title: 'Gyors útmutató: keresési index létrehozása a JavaScriptben'
titleSuffix: Azure Cognitive Search
description: Ebben a JavaScript-útmutatóban megtudhatja, hogyan hozhat létre indexet, tölthet be és futtathat lekérdezéseket az Azure Cognitive Search JavaScript használatával
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.devlang: javascript
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/02/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7d31201f9d1a4519538aba3ac57ddfd340d936d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99509401"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-the-javascript-sdk"></a>Gyors útmutató: Azure Cognitive Search index létrehozása a JavaScript SDK használatával
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Portál](search-get-started-portal.md)
> * [PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)


A [JavaScript/TYPSCRIPT SDK for Azure Cognitive Search](/javascript/api/overview/azure/search-documents-readme) használatával létrehozhat egy Node.js alkalmazást a JavaScriptben, amely egy keresési indexet hoz létre, tölt be és kérdez le.

Ez a cikk bemutatja, hogyan hozhatja létre az alkalmazás lépéseit. Azt is megteheti, hogy [letölti a forráskódot és](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) az adatforrást, és futtatja az alkalmazást a parancssorból.

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt a következő eszközökkel és szolgáltatásokkal rendelkezhet:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/).

+ Egy Azure Cognitive Search szolgáltatás. [Hozzon létre egy szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat. 

+ [Node.js](https://nodejs.org) és [NPM](https://www.npmjs.com)

+ [Visual Studio Code](https://code.visualstudio.com) vagy más ide


## <a name="set-up-your-project"></a>A projekt beállítása

Először beolvassa a keresési szolgáltatás végpontját és kulcsát. Ezután hozzon létre egy új projektet a NPM az alábbiakban ismertetett módon.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Kulcs és végpont másolása

A szolgáltatás felé irányuló hívások URL-végpontot és hozzáférési kulcsot igényelnek minden kérelemben. Első lépésként keresse meg a projekthez hozzáadandó API-kulcsot és URL-címet. Az ügyfél egy későbbi lépésben való létrehozásakor mindkét értéket meg kell adnia.

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

2. A **Beállítások**  >  **kulcsainál** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó teljes jogosultságokról, ha objektumokat hoz létre vagy töröl. Két felcserélhető elsődleges és másodlagos kulcs van. Bármelyiket használhatja.

   ![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-rest/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

### <a name="create-a-new-npm-project"></a>Új NPM-projekt létrehozása

Először nyissa meg a VS Code és annak [integrált terminálját](https://code.visualstudio.com/docs/editor/integrated-terminal) , vagy egy másik terminált, például a Node.js parancssort.

1. Hozzon létre egy fejlesztői könyvtárat, amely a nevét adja meg `quickstart` :

    ```cmd
    mkdir quickstart
    cd quickstart
    ```

2. Üres projekt inicializálása a NPM futtatásával 

    ```cmd
    npm init
    ```
     Fogadja el az alapértelmezett értékeket, kivéve a licencet, amelyet a "MIT" értékre kell beállítani. 

3. Telepítse `@azure/search-documents` , a [JavaScript/Typscript SDK for Azure Cognitive Search](/javascript/api/overview/azure/search-documents-readme).

    ```cmd
    npm install @azure/search-documents
    ```

4. Telepítse `dotenv` , amely a környezeti változók (például a szolgáltatás neve és az API-kulcs) importálására szolgál.
    ```cmd
    npm install dotenv
    ```

5. Győződjön meg arról, hogy konfigurálta a projekteket és annak függőségeit, és ellenőrizze, hogy a  **package.js** fájl a következő JSON-hoz hasonlóan néz ki:

    ```json
    {
      "name": "quickstart",
      "version": "1.0.0",
      "description": "Azure Cognitive Search Quickstart",
      "main": "index.js",
      "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
      },
      "keywords": [
        "Azure",
        "Search"
      ],
      "author": "Your Name",
      "license": "MIT",
      "dependencies": {
        "@azure/search-documents": "^11.0.3",
        "dotenv": "^8.2.0"
      }
    }
    ```

6. Hozzon létre egy file **. env** fájlt a keresési szolgáltatás paramétereinek tárolására:

    ```
    SEARCH_API_KEY=<search-admin-key>
    SEARCH_API_ENDPOINT=https://<search-service-name>.search.windows.net
    ```

Cserélje le az `<search-service-name>` értéket a keresési szolgáltatás nevére. Cserélje le `<search-admin-key>` a értéket a korábban feljegyzett kulcs értékére. 

### <a name="create-indexjs-file"></a>index.js fájl létrehozása

Ezután létrehozunk egy **index.js** fájlt, amely a kód tárolására szolgáló fő fájl.

A fájl elején importáljuk a `@azure/search-documents` könyvtárat:

```javascript
const { SearchIndexClient, SearchClient, AzureKeyCredential, odata } = require("@azure/search-documents");
```

Ezután meg kell követelni a `dotenv` csomagnak a **. env** fájl paramétereinek olvasását a következőképpen:

```javascript
// Load the .env file if it exists
require("dotenv").config();

// Getting endpoint and apiKey from .env file
const endpoint = process.env.SEARCH_API_ENDPOINT || "";
const apiKey = process.env.SEARCH_API_KEY || "";
```

A meglévő importálási és környezeti változók esetében készen áll a fő funkció meghatározására.

Az SDK funkcióinak többsége aszinkron módon működik `async` . A következő függvényt is bemutatjuk `main().catch()` az észlelt hibák befogására és naplózására:

```javascript
async function main() {
    console.log(`Running Azure Cognitive Search Javascript quickstart...`);
    if (!endpoint || !apiKey) {
        console.log("Make sure to set valid values for endpoint and apiKey with proper authorization.");
        return;
    }

    // remaining quickstart code will go here
}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});
```

Ezzel a hellyel készen áll egy index létrehozására.

## <a name="1---create-index"></a>1 – index létrehozása 

Hozzon létre egy fájlt **hotels_quickstart_index.jsa** következőn:.  Ez a fájl határozza meg, hogyan működik az Azure Cognitive Search a következő lépésben betöltés alatt álló dokumentumokkal. Az egyes mezők azonosítása a `name` és a megadott értékkel történik `type` . Az egyes mezőkben index attribútumok is megadhatók, amelyek meghatározzák, hogy az Azure Cognitive Search kereshet, szűrhető, rendezhető és kategorizálható a mező alapján. A mezők többsége egyszerű adattípusú, néhány például `AddressType` olyan összetett típus, amely lehetővé teszi, hogy gazdag adatstruktúrákat hozzon létre az indexében.  További információt a [támogatott adattípusokról](/rest/api/searchservice/supported-data-types) és az index [létrehozása (REST)](/rest/api/searchservice/create-index)című témakörben talál. 

Adja hozzá a következőt a **hotels_quickstart_index.jshoz** , vagy [töltse le a fájlt](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels_quickstart_index.json). 

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

Ha az index definíciója van érvényben,  **hotels_quickstart_index.jsa** **index.js** tetején szeretnénk importálni, így a fő függvény elérheti az index definícióját.

```javascript
const indexDefinition = require('./hotels_quickstart_index.json');
```

A fő függvényen belül létrehozunk egy `SearchIndexClient` -t, amely az Azure Cognitive Search indexek létrehozásához és kezeléséhez használatos. 

```javascript
const indexClient = new SearchIndexClient(endpoint, new AzureKeyCredential(apiKey));
```

Ezután törölni szeretnénk az indexet, ha már létezik. Ez egy gyakori eljárás a tesztelési/demó kódokhoz.

Ezt egy olyan egyszerű függvény definiálásával tesszük, amely megpróbálja törölni az indexet.

```javascript
async function deleteIndexIfExists(indexClient, indexName) {
    try {
        await indexClient.deleteIndex(indexName);
        console.log('Deleting index...');
    } catch {
        console.log('Index does not exist yet.');
    }
}
```

A függvény futtatásához ki kell bontani az index nevét az index definícióból, és át `indexName` kell adni a `indexClient` `deleteIndexIfExists()` függvényt a függvénnyel együtt.

```javascript
const indexName = indexDefinition["name"];

console.log('Checking if index exists...');
await deleteIndexIfExists(indexClient, indexName);
```

Ezt követően készen áll az index létrehozására a `createIndex()` metódussal.

```javascript
console.log('Creating index...');
let index = await indexClient.createIndex(indexDefinition);

console.log(`Index named ${index.name} has been created.`);
```

### <a name="run-the-sample"></a>Minta futtatása

Ezen a ponton készen áll a minta futtatására. Futtassa a következő parancsot egy terminál-ablak használatával:

```cmd
node index.js
```

Ha [letöltötte a forráskódot](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) , és még nem telepítette a szükséges csomagokat, először futtassa a parancsot `npm install` .

A program által végrehajtott műveleteket ismertető üzenetnek kell megjelennie. 

Nyissa meg a keresési szolgáltatás **áttekintését** a Azure Portal. Válassza ki az **indexek** lapot. A következőhöz hasonlónak kell megjelennie:

:::image type="content" source="media/search-get-started-javascript/create-index-no-data.png" alt-text="Képernyőkép a Azure Portalről, a keresési szolgáltatás áttekintése, indexek lap" border="false":::

A következő lépésben hozzá kell adnia az adatindexhez. 

## <a name="2---load-documents"></a>2 – dokumentumok betöltése 


Az Azure Cognitive Searchban a dokumentumok olyan adatstruktúrák, amelyek mind a lekérdezések indexeléséhez, mind pedig a kimenetekhez tartoznak. Ilyen típusú adatküldést küldhet az indexbe, vagy használhat [Indexelő](search-indexer-overview.md). Ebben az esetben a dokumentumokat az indexbe programozott módon.

A dokumentumok bemenetei lehetnek egy adatbázisban lévő sorok, a blob Storage-ban található Blobok, vagy a példában szereplő JSON-dokumentumok a lemezen. Letöltheti [hotels.js](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/quickstart/v11/hotels.json) , vagy létrehozhat saját **hotels.js** a következő tartalommal rendelkező fájlon:

```json
{
    "value": [
        {
            "HotelId": "1",
            "HotelName": "Secret Point Motel",
            "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
            "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
            "Category": "Boutique",
            "Tags": ["pool", "air conditioning", "concierge"],
            "ParkingIncluded": false,
            "LastRenovationDate": "1970-01-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "677 5th Ave",
                "City": "New York",
                "StateProvince": "NY",
                "PostalCode": "10022"
            }
        },
        {
            "HotelId": "2",
            "HotelName": "Twin Dome Motel",
            "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Boutique",
            "Tags": ["pool", "free wifi", "concierge"],
            "ParkingIncluded": "false",
            "LastRenovationDate": "1979-02-18T00:00:00Z",
            "Rating": 3.6,
            "Address": {
                "StreetAddress": "140 University Town Center Dr",
                "City": "Sarasota",
                "StateProvince": "FL",
                "PostalCode": "34243"
            }
        },
        {
            "HotelId": "3",
            "HotelName": "Triple Landscape Hotel",
            "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
            "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
            "Category": "Resort and Spa",
            "Tags": ["air conditioning", "bar", "continental breakfast"],
            "ParkingIncluded": "true",
            "LastRenovationDate": "2015-09-20T00:00:00Z",
            "Rating": 4.8,
            "Address": {
                "StreetAddress": "3393 Peachtree Rd",
                "City": "Atlanta",
                "StateProvince": "GA",
                "PostalCode": "30326"
            }
        },
        {
            "HotelId": "4",
            "HotelName": "Sublime Cliff Hotel",
            "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
            "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
            "Category": "Boutique",
            "Tags": ["concierge", "view", "24-hour front desk service"],
            "ParkingIncluded": true,
            "LastRenovationDate": "1960-02-06T00:00:00Z",
            "Rating": 4.6,
            "Address": {
                "StreetAddress": "7400 San Pedro Ave",
                "City": "San Antonio",
                "StateProvince": "TX",
                "PostalCode": "78216"
            }
        }
    ]
}
```

Hasonlóan ahhoz, amit a indexDefinition tettünk, aindex.jstetején is importálni kell, `hotels.json` hogy az  adat a fő függvényben is elérhető legyen.

```javascript
const hotelData = require('./hotels.json');
```


A keresési indexbe történő adatindexeléshez most létre kell hoznia a következőt: `SearchClient` . Az `SearchIndexClient` indexek létrehozásához és kezeléséhez a a a `SearchClient` dokumentumok feltöltésére és az index lekérdezésére szolgál.

Kétféleképpen hozhat létre `SearchClient` . Az első lehetőség a `SearchClient` teljesen új létrehozása:

```javascript
 const searchClient = new SearchClient(endpoint, indexName, new AzureKeyCredential(apiKey));
```

Azt is megteheti, hogy a (z) `getSearchClient()` metódusával `SearchIndexClient` létrehozza a következőt `SearchClient` :

```javascript
const searchClient = indexClient.getSearchClient(indexName);
```

Most, hogy megtörtént az ügyfél definiálása, töltse fel a dokumentumokat a keresési indexbe. Ebben az esetben a `mergeOrUploadDocuments()` metódust használjuk, amely feltölti a dokumentumokat, vagy egyesít egy meglévő dokumentummal, ha már létezik ilyen nevű dokumentum.

```javascript
console.log('Uploading documents...');
let indexDocumentsResult = await searchClient.mergeOrUploadDocuments(hotelData['value']);

console.log(`Index operations succeeded: ${JSON.stringify(indexDocumentsResult.results[0].succeeded)}`);
```

Futtassa újra a programot a alkalmazással `node index.js` . Az 1. lépésben látott, némileg eltérő üzeneteket kell látnia. Ebben az esetben *az index létezik* , és a törlésével kapcsolatos üzenet jelenik meg, mielőtt az alkalmazás létrehozza az új indexet, és beküldi az adatait. 

Mielőtt a következő lépésben futtatná a lekérdezéseket, Definiáljon egy függvényt, hogy a program várjon egy másodpercig. Ezt csak tesztelési/bemutató célokra lehet elvégezni, hogy az indexelés befejeződik, és hogy a dokumentumok elérhetők legyenek az indexben a lekérdezésekhez.

```javascript
function sleep(ms) {
    var d = new Date();
    var d2 = null;
    do {
        d2 = new Date();
    } while (d2 - d < ms);
}
```

Ha szeretné, hogy a program egy másodpercig várjon, hívja meg az `sleep` alábbihoz hasonló függvényt:

```javascript
sleep(1000);
```

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

A létrehozott indexek és a feltöltött dokumentumok már készen állnak arra, hogy lekérdezéseket küldjön az indexbe. Ebben a szakaszban öt különböző lekérdezést küldünk a keresési indexnek, amely az Ön számára elérhető különböző lekérdezési funkciókat mutatja be.

A lekérdezéseket egy függvényben kell megírni, `sendQueries()` amelyet a következő fő függvényben hívunk meg:

```javascript
await sendQueries(searchClient);
```

A lekérdezések küldése a `search()` metódusának használatával történik `searchClient` . Az első paraméter a keresett szöveg, a második paraméter pedig bármely további keresési lehetőség.

Az első lekérdezés megkeresi `*` , ami egyenértékű a kereséssel, és kiválasztja a három mezőt az indexben. Az ajánlott eljárás csak `select` a szükséges mezőkre vonatkozik, mert a szükségtelen adat visszahúzásával késleltetést adhat a lekérdezésekhez.

A ehhez a `searchOptions` lekérdezéshez a `includeTotalCount` értéke is be van állítva `true` , amely a találatok számát adja vissza.

```javascript
async function sendQueries(searchClient) {
    console.log('Query #1 - search everything:');
    let searchOptions = {
        includeTotalCount: true,
        select: ["HotelId", "HotelName", "Rating"]
    };

    let searchResults = await searchClient.search("*", searchOptions);
    for await (const result of searchResults.results) {
        console.log(`${JSON.stringify(result.document)}`);
    }
    console.log(`Result count: ${searchResults.count}`);

    // remaining queries go here
}
```

Az alább vázolt többi lekérdezést is hozzá kell adni a `sendQueries()` függvényhez. Az olvashatóság érdekében ezek el vannak különítve.

A következő lekérdezésben megadjuk a keresési kifejezést, `"wifi"` és egy szűrőt is tartalmaznak, amely csak azokat az eredményeket adja vissza, amelyekben az állapot egyenlő `'FL'` . Az eredményeket a szálloda is megrendezi `Rating` .

```javascript
console.log('Query #2 - Search with filter, orderBy, and select:');
let state = 'FL';
searchOptions = {
    filter: odata`Address/StateProvince eq ${state}`,
    orderBy: ["Rating desc"],
    select: ["HotelId", "HotelName", "Rating"]
};

searchResults = await searchClient.search("wifi", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

Ezt követően a keresés egyetlen kereshető mezőre korlátozódik a `searchFields` paraméter használatával. Ez nagyszerű lehetőség a lekérdezés hatékonyabbá tételére, ha tudja, hogy csak bizonyos mezőkben lévő egyezések érdeklik. 

```javascript
console.log('Query #3 - Limit searchFields:');
searchOptions = {
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("sublime cliff", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
console.log();
```

Egy másik gyakori lehetőség a lekérdezésbe való felvételre `facets` . A dimenziók lehetővé teszik szűrők kiépítését a felhasználói felületen, hogy a felhasználók könnyebben tudják, milyen értékeket tudnak szűrni.

```javascript
console.log('Query #4 - Use facets:');
searchOptions = {
    facets: ["Category"],
    select: ["HotelId", "HotelName", "Rating"],
    searchFields: ["HotelName"]
};

searchResults = await searchClient.search("*", searchOptions);
for await (const result of searchResults.results) {
    console.log(`${JSON.stringify(result.document)}`);
}
```

A végső lekérdezés a `getDocument()` metódusát használja `searchClient` . Ez lehetővé teszi, hogy a kulcs alapján hatékonyan lekérje a dokumentumokat. 

```javascript
console.log('Query #5 - Lookup document:');
let documentResult = await searchClient.getDocument(key='3')
console.log(`HotelId: ${documentResult.HotelId}; HotelName: ${documentResult.HotelName}`)
```

### <a name="run-the-sample"></a>Minta futtatása

Futtassa a programot a alkalmazással `node index.js` . Az előző lépéseken kívül a rendszer elküldi a lekérdezéseket és a konzolra írt eredményeket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, érdemes az egyes projektek végén eldöntenie, hogy szüksége lesz-e még a létrehozott erőforrásokra. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Ebben a JavaScript-útmutatóban számos feladatot dolgozott ki egy index létrehozásához, a dokumentumok betöltéséhez és a lekérdezések futtatásához. 

Ha már rendelkezik valamilyen háttérrel az Azure Cognitive Search-ban, ezt a mintát használhatja kiindulópontként a kipróbálási javaslatok (típus-vagy automatikus kiegészítés), a szűrők és a csiszolt navigáció érdekében. Ha még nem ismeri az Azure Cognitive Searcht, javasoljuk, hogy más oktatóanyagokat próbáljon meg létrehozni, hogy megértsük, mit hozhat létre. További forrásokat a [dokumentációs oldalunkon](https://azure.microsoft.com/documentation/services/search/) talál. 

> [!div class="nextstepaction"]
> [Hozzon létre egy reagáló kezelőfelületet az Azure Cognitive Search](https://github.com/dereklegenzoff/azure-search-react-template)