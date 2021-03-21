---
title: Az Azure Table Storage vagy a Azure Cosmos DB Table API használata a Node.js
description: Strukturált adatok tárolása a felhőben az Azure Table Storage vagy a Node.js Azure Cosmos DB Table API használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-js
ms.openlocfilehash: 2d40b70d49b1934c9dd2d911369245b1b2e4f2ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93079700"
---
# <a name="how-to-use-azure-table-storage-or-the-azure-cosmos-db-table-api-from-nodejs"></a>Az Azure Table Storage és az Azure Cosmos DB Table API használata a Node.js segítségével
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre táblákat, tárolhat adatait, és hogyan végezhet szifilisz-műveleteket az adatokon. Válassza ki az Azure Table service vagy a Azure Cosmos DB Table API. A minták Node.jsban íródnak.

## <a name="create-an-azure-service-account"></a>Azure-szolgáltatásfiók létrehozása

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Azure-tárfiók létrehozása**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB Table API-fiók létrehozása**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage-or-the-azure-cosmos-db-table-api"></a>Az alkalmazás konfigurálása az Azure Storage vagy Azure Cosmos DB Table API használatához

Az Azure Storage vagy Azure Cosmos DB használatához szükség van az Azure Storage SDK for Node.js csomagra, amelyben a Storage REST-szolgáltatásokkal kommunikáló egyszerűsített kódtárak találhatók.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Csomag telepítése a Node Package Manager (NPM) használatával

1. Használjon egy parancssori felületet, amilyen például a **PowerShell** (Windows), a **Terminal** (Mac) vagy a **Bash** (Unix), és keresse meg azt a mappát, amelyben létrehozta az alkalmazást.
2. Írja be az **npm install azure-storage** kifejezést a parancsablakba. A parancs kimenete az alábbihoz fog hasonlítani.

   ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
   ```

3. A **node_modules** mappa létrehozásának ellenőrzéséhez manuálisan is futtathatja az **ls** parancsot. A mappában található az **azure-storage** csomag, amely a tárhely eléréséhez szükséges kódtárakat tartalmazza.

### <a name="import-the-package"></a>A csomag importálása

Adja hozzá a következő kódot a **server.js** fájl elejéhez az alkalmazásban:

```javascript
var azure = require('azure-storage');
```

## <a name="add-your-connection-string"></a>Adja meg a kapcsolatok karakterláncát

Csatlakozhat az Azure Storage-fiókhoz vagy a Azure Cosmos DB Table API-fiókhoz. Szerezze be a kapcsolatok karakterláncát a használt fiók típusától függően.

### <a name="add-an-azure-storage-connection"></a>Azure Storage-kapcsolat hozzáadása

Az Azure-modul az AZURE_STORAGE_ACCOUNT és AZURE_STORAGE_ACCESS_KEY, illetve az AZURE_STORAGE_CONNECTION_STRING környezeti változókat olvassa be az Azure Storage-fiókhoz való csatlakozáshoz szükséges információkért. Ha a környezeti változók nincsenek beállítva, meg kell adnia a fiók adatait a híváskor `TableService` . A következő kód például létrehoz egy `TableService` objektumot:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

### <a name="add-an-azure-cosmos-db-connection"></a>Azure Cosmos DB-kapcsolat hozzáadása

Azure Cosmos DB-kapcsolatok hozzáadásához hozzon létre egy `TableService` objektumot, és adja meg a fiók nevét, az elsődleges kulcsot és a végpontot. Ezeket az értékeket a   >  Cosmos db-fiókjához tartozó Azure Portalban lévő beállítások **közötti kapcsolatok sztringből** másolhatja. Például:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```

## <a name="create-a-table"></a>Tábla létrehozása

A következő kód létrehoz egy `TableService` objektumot, és felhasználja egy új tábla létrehozásához.

```javascript
var tableSvc = azure.createTableService();
```

A hívás `createTableIfNotExists` új táblát hoz létre a megadott névvel, ha még nem létezik. Az alábbi példában létrehozunk egy új, „mytable” nevű táblát, ha még nem létezik ilyen:

```javascript
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

A `result.created` értéke `true`, amennyiben új táblát hoz létre, és `false`, amennyiben a tábla már létezik. A `response` a kérésre vonatkozó információkat tartalmaz.

### <a name="apply-filters"></a>Szűrők alkalmazása

A használatával végrehajtott műveletekhez nem kötelező szűrést alkalmazhat `TableService` . A szűrési műveletek magukban foglalhatják a naplózást, az automatikus újrapróbálkozásokat stb. A szűrők olyan objektumok, amelyek az aláírással ellátott metódust implementálják:

```javascript
function handle (requestOptions, next)
```

A kérésbeállítások előfeldolgozása után a metódusnak hívnia kell a **next** értéket, és továbbítania kell egy visszahívást a következő aláírással:

```javascript
function (returnObject, finalCallback, next)
```

Ebben a visszahívásban, és a `returnObject` (a kéréstől a kiszolgálónak küldött válasz) feldolgozását követően a visszahívásnak meg kell hívnia, `next` hogy továbbra is feldolgozza-e más szűrőket, vagy egyszerűen hívja meg a szolgáltatás meghívásának `finalCallback` befejezését.

Az újrapróbálkozási logikát megvalósító két szűrő szerepel a Node.js készült Azure SDK-ban `ExponentialRetryPolicyFilter` és a-ben `LinearRetryPolicyFilter` . A következő létrehoz egy `TableService` objektumot, amely a következőket használja `ExponentialRetryPolicyFilter` :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához

Entitás hozzáadásához először hozzon létre egy objektumot, amely meghatározza az entitástulajdonságokat. Az összes entitásnak tartalmaznia kell a **PartitionKey** és **RowKey** egyedi entitásazonosítókat.

* **PartitionKey** – Meghatározza, hogy melyik partíció tárolja az entitást.
* **RowKey** – Egyedileg azonosítja az entitást a partíción belül.

A **PartitionKey** és a **RowKey** értéknek is sztringértéknek kell lennie. További információt a [Table Service adatmodelljét ismertető](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) témakörben talál.

Az alábbi egy entitás meghatározására szolgál példaként. A **dueDate** típusként van definiálva `Edm.DateTime` . A típus meghatározása nem kötelező, és a rendszer kikövetkezteti a nem meghatározott típusokat.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> `Timestamp`Minden rekordhoz van egy mező is, amelyet az Azure állít be, amikor egy entitást beszúr vagy frissít.

A használatával `entityGenerator` entitásokat is létrehozhat. A következő példa ugyanazt a feladatot hozza létre a használatával `entityGenerator` .

```javascript
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Ha hozzá szeretne adni egy entitást a táblához, adja át az entitás objektumot a `insertEntity` metódusnak.

```javascript
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

Ha a művelet sikeres, a `result` tartalmazza a beszúrt rekord [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) elemét, a `response` pedig a művelettel kapcsolatos információkat tartalmaz.

Példaválasz:

```javascript
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Alapértelmezés szerint a `insertEntity` nem küldi vissza a beszúrt entitást az `response` információ részeként. Ha egyéb műveleteket szeretne végrehajtani ezen az entitáson vagy gyorsítótárazni kívánja az információkat, érdemes lehet visszakérni a `result` részeként. Ezt a következőképpen teheti meg `echoContent` :
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Entitás frissítése

Több metódus is rendelkezésre áll a meglévő entitások frissítéséhez:

* `replaceEntity` -Egy meglévő entitás frissítése a helyére.
* `mergeEntity` -Egy meglévő entitás frissítése új tulajdonságértékek meglévő entitásba való egyesítésével.
* `insertOrReplaceEntity` -Egy meglévő entitás frissítése a helyére. Ha még nincsen entitás, beszúr egy újat.
* `insertOrMergeEntity` -Egy meglévő entitás frissítése új tulajdonságértékek meglévőbe való egyesítésével. Ha még nincsen entitás, beszúr egy újat.

Az alábbi példa egy entitás frissítését mutatja be a használatával `replaceEntity` :

```javascript
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> Alapértelmezés szerint az entitások frissítésének folyamata nem ellenőrzi, hogy a frissített adatok módosultak-e egy korábbi folyamat során. Egyidejű frissítések támogatásához:
>
> 1. Kérje le a frissíteni kívánt objektum ETagjét. A rendszer ezt az entitásokhoz kapcsolódó műveletekre adott `response` részeként adja vissza, és a következőn keresztül kérhető le: `response['.metadata'].etag`.
> 2. Amikor frissíti az entitásokat, adja hozzá a korábban lekért ETag-információt az új entitáshoz. Például:
>
>       entity2['.metadata'].etag = currentEtag;
> 3. Hajtsa végre a frissítést. Ha az entitás módosult az ETag értékének lekérése óta (például az alkalmazás egy másik példányáról van szó), egy `error` elem jelenik meg, amely szerint a kérésben meghatározott frissítési feltétel nem teljesült.
>
>

`replaceEntity` `mergeEntity` Ha a és a esetében a frissítendő entitás nem létezik, akkor a frissítési művelet meghiúsul; ezért ha egy entitást attól függetlenül kíván tárolni, hogy az már létezik, használja `insertOrReplaceEntity` vagy `insertOrMergeEntity` .

Sikeres frissítés esetén a frissített entitás `result`ETagje **a** részét képezi.

## <a name="work-with-groups-of-entities"></a>Entitáscsoportok használata

Annak biztosításához, hogy a kiszolgáló elvégezze a kérés elemi feldolgozását, néha érdemes több műveletet egyszerre, egy kötegben elküldeni. Ennek elvégzéséhez használja az `TableBatch` osztályt egy köteg létrehozásához, majd használja a `executeBatch` metódust a `TableService` kötegelt műveletek végrehajtásához.

 Az alábbi példa két entitás egy kötegben való elküldését mutatja be:

```javascript
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

Sikeres kötegműveletek esetén a `result` a kötegben lévő minden egyes műveletről tartalmaz információkat.

### <a name="work-with-batched-operations"></a>Kötegelt műveletek használata

Az `operations` tulajdonság áttekintésével megvizsgálhatja a köteghez adott műveleteket. A következő metódusokat is használhatja, ha a műveletekkel szeretne dolgozni:

* **clear** – Törli egy köteg összes műveletét.
* **getOperations** – Lehív egy műveletet a kötegből.
* **hasOperations** – Igaz értéket ad vissza, ha a köteg műveleteket tartalmaz.
* **removeOperations** – Eltávolít egy műveletet.
* **size** – Visszaadja a kötegben lévő műveletek számát.

## <a name="retrieve-an-entity-by-key"></a>Entitás lekérése kulcs alapján

Ha egy adott entitást kíván visszaadni a **PartitionKey** és a **RowKey** alapján, használja a **retrieveEntity** metódust.

```javascript
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

A művelet végrehajtása után a `result` tartalmazza az entitást.

## <a name="query-a-set-of-entities"></a>Több entitás lekérdezése

Tábla lekérdezéséhez használja a **TableQuery** objektumot, amellyel összeállíthat egy lekérdezési kifejezést az alábbi záradékokkal:

* **select** – A lekérdezésben visszaadandó mezők.
* **where** – A where záradék.

  * **and** – Egy `and` where feltétel.
  * **or** – Egy `or` where feltétel.
* **top** – A beolvasni kívánt elemek száma.

A következő példa összeállít egy lekérdezést, amely az öt első „hometasks” PartitionKey-értékkel rendelkező elemet adja vissza.

```javascript
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

Mivel a **select** nincs használatban, a rendszer minden mezőt visszaad. Ha egy táblán szeretne végrehajtani lekérdezést, a **queryEntities** lekérdezést használja. Az alábbi példa ezt használja a „mytable” tábla entitásainak visszaadásához.

```javascript
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

Ha a lekérdezés sikeres, a `result.entries` a lekérdezési feltételekkel egyező entitások tömbjét tartalmazza. Ha a lekérdezés nem tudta visszaadni az összes entitást, a `result.continuationToken` nem *null* értékű lesz, és felhasználható a **queryEntities** harmadik paramétereként további eredmények lekéréséhez. A kezdeti lekérdezéshez használja a *null* értéket harmadik paraméterként.

### <a name="query-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérdezése

Egy táblalekérdezéssel egy entitásnak csak bizonyos mezőit is lekérdezheti.
Ez csökkenti a sávszélesség felhasználását, és javíthatja a lekérdezési teljesítményt, főleg a nagy entitások esetében. Használja a **select** záradékot, és adja át a visszaadni kívánt mezők nevét. A következő lekérdezés például csak a **description** és **dueDate** mezőket adja vissza.

```javascript
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Entitás törlése

Entitásokat a partíció- és a sorkulcsokkal törölhet. Ebben a példában a **task1** objektum tartalmazza a törölni kívánt entitás **RowKey** és **PartitionKey** értékeit. Ezután a rendszer a **deleteEntity** metódusba továbbítja az objektumot.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> Elemek törlésekor érdemes használni az ETageket, mert így meg tud győződni róla, hogy az elemet nem módosították korábbi folyamatok. Az ETagek használatával kapcsolatos további információkért lásd az [entitások frissítésével](#update-an-entity) foglalkozó szakaszt.
>
>

## <a name="delete-a-table"></a>Tábla törlése

Az alábbi kóddal törölhető egy tábla egy tárfiókból.

```javascript
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Ha nem biztos benne, hogy a tábla létezik, használja a **deleteTableIfExists** metódust.

## <a name="use-continuation-tokens"></a>Folytatási tokenek használata

Ha nagy mennyiségű találatot eredményez egy tábla lekérdezése, fontolja meg a folytatási tokenek használatát. Előfordulhat, hogy nagy mennyiségű lekérdezhető adat áll rendelkezésre, amelyet nem feltétlenül fog észrevenni, ha az összeállítás nem teszi lehetővé a folytatási token jelenlétének a felismerését.

Ha jelen van ilyen token, az entitások lekérdezésekor visszaadott **results** objektum beállít egy `continuationToken` tulajdonságot. Ezt lekérdezések végrehajtásakor használhatja, hogy továbbléphessen a partíciók és táblaentitások között.

Lekérdezés végrehajtásakor megadhat egy `continuationToken` paramétert a lekérdezésiobjektum-példány és a visszahívási függvény között:

```javascript
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Ha megvizsgálja a `continuationToken` objektumot, olyan tulajdonságokat fog találni, mint például a `nextPartitionKey`, `nextRowKey` és `targetLocation`, amelyeket az eredmények közti iterációhoz használhat.

A és a együtt is használható `top` `continuationToken` az oldalméret beállításához.

## <a name="work-with-shared-access-signatures"></a>Közös hozzáférésű jogosultságkódok használata

A közös hozzáférésű jogosultságkódokkal (Shared access signatures, SAS) biztonságos és részletes hozzáférést biztosíthat a táblákhoz anélkül, hogy megadná Storage-fiókjának nevét vagy kulcsait. Az SAS-t gyakran használják az adatokhoz való korlátozott hozzáférés biztosítására, például arra, hogy a mobilalkalmazások hozzáférhessenek a lekérdezésrekordokhoz.

A megbízható alkalmazások, például a felhőalapú szolgáltatások SAS-eket hoznak létre a **TableService****generateSharedAccessSignature** metódusával, és átadják azokat nem megbízható vagy részben megbízható alkalmazásoknak, például mobilalkalmazásoknak. A rendszer egy szabályzat segítségével hozza létre az SAS-eket, amely meghatározza az SAS érvényességének kezdő és befejező dátumát, valamint az SAS tulajdonosának biztosított hozzáférési szintet.

Az alábbi példa egy új megosztott elérési házirendet hoz létre, amellyel az SAS tulajdonosa lekérdezheti ('r’) a táblát, és amely a létrehozástól számított 100 perc elteltével lejár.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

Vegye figyelembe, hogy a gazdagép adatait is meg kell adnia, mivel szükség van rájuk, ha az SAS tulajdonosa megkísérel hozzáférni a táblához.

Ezután az ügyfélalkalmazás az SAS-t használja a **TableServiceWithSAS** metódussal, hogy műveleteket hajtson végre a táblán. A következő példa csatlakozik a táblához, és végrehajt egy lekérdezést. Lásd: [korlátozott hozzáférés engedélyezése az Azure Storage-erőforrásokhoz megosztott hozzáférési aláírások (SAS) használatával](../storage/common/storage-sas-overview.md) a tableSAS formátumához.

```javascript
// Note in the following command, host is in the format: `https://<your_storage_account_name>.table.core.windows.net` and the tableSAS is in the format: `sv=2018-03-28&si=saspolicy&tn=mytable&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D`;

var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

Mivel az SAS csak lekérdezési hozzáféréssel lett létrehozva, a rendszert hibát jelez, amennyiben megkísérel entitásokat beszúrni, frissíteni vagy törölni.

### <a name="access-control-lists"></a>Hozzáférés-vezérlési listák

Hozzáférés-vezérlési listát (Access Control List, ACL) is használhat az SAS hozzáférési szabályzatának beállítására. Ez akkor hasznos, ha több ügyfélnek is lehetővé kívánja tenni a tábla elérését úgy, hogy az egyes ügyfelekre más-más hozzáférési szabályzat vonatkozik.

A rendszer ACL-eket implementál egy hozzáférésiszabályzat-tömbbel, és minden szabályzathoz azonosító van társítva. Az alábbi példa két szabályzatot határoz meg, egyet „user1”, egy másikat pedig „user2” részére:

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Az alábbi példa lekéri a **hometasks** tábla aktuális ACL-jét, majd hozzáadja az új szabályzatokat a **setTableAcl** használatával. Ez a megközelítés lehetővé teszi, hogy:

```javascript
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Miután beállította az ACL-t, létrehozzon egy SAS-t egy szabályzat azonosítója alapján. Az alábbi példa egy új SAS-t hoz létre „user2” számára:

```javascript
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>Következő lépések

További információkért lásd a következő forrásanyagokat.

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.
* [Azure Storage SDK for Node.js](https://github.com/Azure/azure-storage-node) adattár a GitHubon.
* [Azure for Node.js-fejlesztők](/azure/developer/javascript/)
* [Node.js-webalkalmazás létrehozása az Azure-ban](../app-service/quickstart-nodejs.md)
* [Node.js-alkalmazás létrehozása és telepítése egy Azure-felhőszolgáltatásban](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (a Windows PowerShell használatával)