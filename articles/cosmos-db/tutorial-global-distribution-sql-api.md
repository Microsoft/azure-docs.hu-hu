---
title: Az Azure Cosmos DB globális terjesztési oktatóanyaga az SQL API-hoz
description: Ismerje meg, hogyan állíthatja be az Azure Cosmos DB globális terjesztését az SQL API használatával.
author: rimman
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 07/15/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: a566094f88ba9ffd25eadd046ae7254e26b9c2cf
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234601"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Az Azure Cosmos DB globális terjesztésének beállítása az SQL API használatával

Ebben a cikkben bemutatjuk, hogyan állíthatja be az Azure Cosmos DB globális terjesztését az Azure Portallal, és hogyan csatlakozhat az SQL API használatával.

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása az [SQL API-k](sql-api-introduction.md) használatával

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>Csatlakozás egy kívánt régióhoz az SQL API használatával

A [globális terjesztés](distribute-data-globally.md) kihasználása érdekében az ügyfélalkalmazások megadhatják a preferált régiók sorrendbe rendezett listáját a dokumentumokkal kapcsolatos műveletek elvégzéséhez. Ezt a kapcsolódási szabályzat beállításával lehet megtenni. Az SQL SDK az Azure Cosmos DB-fiók konfigurációja, az aktuális régiónkénti rendelkezésre állás és a megadott preferencialista alapján fogja kiválasztani az optimális végpontot az írási és olvasási műveletek végrehajtásához.

A preferencialista meghatározására akkor kerül sor, amikor kapcsolatot inicializál az SQL SDK-k használatával. Az SDK-k egy „PreferredLocations” nevű választható paramétert is elfogadnak, amely az Azure-régiók rendezett listája.

Az SDK automatikusan elküldi az írásokat az aktuális írási régióba.

Az olvasásokat a PreferredLocations lista első elérhető régiójába küldi. Ha a kérelem meghiúsul, az ügyfél továbbadja a listát a következő régiónak, és így tovább.

Az SDK-k csak a PreferredLocations listában szereplő régiókból próbálnak meg olvasni. Így ha például az adatbázisfiók négy régióban érhető el, de az ügyfél csak két olvasási (nem írási) régiót ad meg a PreferredLocations listában, akkor a PreferredLocations listán nem szereplő olvasási régiók nem szolgálnak ki olvasásokat. Ha a PreferredLocations listán szereplő olvasási régiók nem érhetők el, az olvasásokat is az írási régió szolgálja ki.

Az alkalmazás az SDK 1.8-as vagy újabb verzióiban elérhető WriteEndpoint és a ReadEndpoint tulajdonságok megtekintésével ellenőrizni tudja az SDK által kiválasztott aktuális írási és olvasási végpontot.

Ha a PreferredLocations tulajdonság nincs beállítva, a kérelmek az aktuális írási régióból lesznek teljesítve.

## <a name="net-sdk"></a>.NET SDK
Az SDK a kód módosítása nélkül használható. Ebben az esetben az SDK automatikusan átirányítja az olvasásokat és az írásokat az aktuális írási régióba.

A .NET SDK 1.8-as és újabb verzióiban a DocumentClient konstruktor ConnectionPolicy paramétere rendelkezik egy Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations nevű tulajdonsággal. Ez a tulajdonság egy `<string>` típusú gyűjtemény, amelynek egy listát kell tartalmaznia a régiónevekkel. A karakterlánc-értékek az oszlop szerint formázott a [Azure-régiók][regions] lapon, szóközök nélküli szövegláncként előtt vagy után az első és utolsó karakter.

Az aktuális írási és olvasási végpontokat a DocumentClient.WriteEndpoint és a DocumentClient.ReadEndpoint tulajdonság tartalmazza.

> [!NOTE]
> A végpontok URL-címei nem tekinthetők hosszú élettartamú állandóknak. A szolgáltatás bármikor frissítheti őket. Az SDK automatikusan kezeli ezt a módosítást.
>
>

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

## <a name="nodejsjavascript"></a>Node.js/JavaScript

Az SDK a kód módosítása nélkül használható. Ebben az esetben az SDK automatikusan átirányítja az olvasásokat és az írásokat az aktuális írási régióba.

Az SDK-k 1.8-as és újabb verziójában a DocumentClient konstruktor ConnectionPolicy paramétere egy új DocumentClient.ConnectionPolicy.PreferredLocations nevű tulajdonsággal rendelkezik. Ez a paraméter egy sztringtömb, amely a régiónevek listáját használja. A nevek vannak régiónév oszlop található a [Azure-régiók][regions] lap. Az AzureDocuments.Regions objektumban található előre definiált konstansokat is használhatja

Az aktuális írási és olvasási végpontokat a DocumentClient.getWriteEndpoint és a DocumentClient.getReadEndpoint tulajdonság tartalmazza.

> [!NOTE]
> A végpontok URL-címei nem tekinthetők hosszú élettartamú állandóknak. A szolgáltatás bármikor frissítheti őket. Az SDK automatikusan kezeli ezt a módosítást.
>
>

Az alábbi, a példakód Node.js/Javascript számára.

```JavaScript
// Creating a ConnectionPolicy object
var connectionPolicy = new DocumentBase.ConnectionPolicy();

// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);
```

## <a name="python-sdk"></a>Python SDK

A következő kód bemutatja, hogyan állíthatja be az előnyben részesített helyek a Python SDK-val:

```python

connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe']
client = cosmos_client.CosmosClient(ENDPOINT, {'masterKey': MASTER_KEY}, connectionPolicy)

```

## <a name="java-v2-sdk"></a>V2 a Java SDK

A következő kód bemutatja, hogyan állíthatja be az előnyben részesített helyek a Java SDK-val:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Arrays.asList("East US", "West US", "Canada Central"));
AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKeyOrResourceToken(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConnectionPolicy(policy)
                .build();
```

## <a name="rest"></a>REST
Ha az adatbázisfiók több régióban is elérhetővé vált, az ügyfelek lekérdezhetik a rendelkezésre állását egy GET kéréssel, amely a következő URI-ra vonatkozik.

    https://{databaseaccount}.documents.azure.com/

A szolgáltatás visszaadja a régiók listáját és a replikák régiókhoz tartozó Azure Cosmos-DB végpont URI-jait. A válasz az aktuális írási régiót is tartalmazni fogja. Az ügyfél ezután a következő módon választhatja ki a megfelelő végpontot a további REST API-kérésekhez.

Példaválasz

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Az összes PUT, POST és DELETE kérelemnek a megadott írási URI-ra kell érkeznie
* A GET-ek és más csak olvasási kérelmek (például a lekérdezések) az ügyfél által kiválasztott bármelyik végpontra érkezhetnek

A csak olvasási régiókba érkező írási kérelmek a 403-as HTTP-hibakóddal („Tiltott”) hiúsulnak meg.

Ha az írási régió az ügyfél kezdeti felderítési fázisa után módosul, az előző írási régióba érkező későbbi írások a 403-as HTTP-hibakóddal („Tiltott”) hiúsulnak meg. Az ügyfélnek ilyenkor ismét le kell kérnie a régiók listáját a GET metódussal a frissített írási régió beszerzéséhez.

Ezzel el is végezte az oktatóanyagot. Ha meg szeretné ismerni, hogyan kezelheti a globálisan replikált fiók konzisztenciáját, olvassa el a [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md) című cikket. További információ a globális adatbázis-replikáció működéséről az Azure Cosmos DB szolgáltatásban: [Globális adatterjesztés az Azure Cosmos DB-vel](distribute-data-globally.md).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása az SQL API-k használatával

Továbbléphet a következő oktatóanyagra, amelyből megtudhatja, hogyan fejleszthet helyileg az Azure Cosmos DB helyi emulátorával.

> [!div class="nextstepaction"]
> [Helyi fejlesztés az emulátorral](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

