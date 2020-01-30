---
title: Az Azure Storage Table service vagy a PHP-Azure Cosmos DB Table API használata
description: Az Azure Table Storage vagy az Azure Cosmos DB Table API használatával strukturált adatok tárolhatók a felhőben.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: php
ms.topic: sample
ms.date: 04/05/2018
ms.openlocfilehash: a19928516685e7496dc3e892d2598b24b5abae19
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771054"
---
# <a name="how-to-use-azure-storage-table-service-or-the-azure-cosmos-db-table-api-from-php"></a>Az Azure Table Storage szolgáltatás és az Azure Cosmos DB Table API használata a PHP segítségével
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

## <a name="overview"></a>Áttekintés
Ez az útmutató bemutatja, hogyan hajthat végre gyakori forgatókönyveket az Azure Storage Table szolgáltatás és az Azure Cosmos DB Table API használatával. A mintákat a PHP-ben írták, és az [Azure Storage Table php ügyféloldali kódtárat][download]használják. Az ismertetett forgatókönyvek a **táblák létrehozásával és törlésével**, valamint a **táblaentitások beszúrásával, törlésével és lekérdezésével** foglalkoznak. Az Azure Table Service szolgáltatással kapcsolatos további információkért lásd a [További lépések](#next-steps) szakaszt.


## <a name="create-an-azure-service-account"></a>Azure-szolgáltatásfiók létrehozása

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Azure Cosmos DB Table API-fiók létrehozása

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-php-application"></a>PHP-alkalmazás létrehozása

A Storage Table szolgáltatáshoz vagy az Azure Cosmos DB Table API-hoz való hozzáférés céljából létrehozott PHP-alkalmazásokra vonatkozó egyetlen követelmény, hogy a kódból a PHP-hoz készült Azure Storage Table SDK-ban szereplő osztályokra hivatkozzon. Az alkalmazás létrehozásához bármilyen fejlesztői eszközt (pl. Jegyzettömböt) használhat.

Ebben az útmutatóban olyan Storage Table szolgáltatási vagy Azure Cosmos DB-funkciókat használunk, amelyek egy PHP-alkalmazásból helyileg hívhatóak meg, vagy egy Azure-beli webes szerepkörben, feldolgozói szerepkörben, vagy weboldalon futó kódban.

## <a name="get-the-client-library"></a>Az ügyfélkönyvtár lekérése

1. Hozzon létre egy composer.json nevű fájlt a projekt gyökérkönyvtárában, és adja hozzá az alábbi kódot:
   ```json
   {
   "require": {
    "microsoft/azure-storage-table": "*"
   }
   }
   ```
2. Töltse le a [composer.phar](https://getcomposer.org/composer.phar) fájlt a gyökérkönyvtárba. 
3. Nyissa meg a parancssort, és hajtsa végre az alábbi parancsot a projekt gyökérkönyvtárában:
   ```
   php composer.phar install
   ```
   Másik megoldásként navigáljon az [Azure Storage Table PHP ügyféloldali kódtárához](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table) a GitHubon, és klónozza a forráskódot.


## <a name="add-required-references"></a>Szükséges referenciák hozzáadása
A Storage Table szolgáltatás vagy az Azure Cosmos DB API-k használatához a következőket kell tennie:

* Hivatkozzon az automatikus betöltő fájlra a [require_once][require_once] utasítás használatával, és
* hivatkozzon bármely felhasznált osztályra.

A következő példa bemutatja, hogyan használható az automatikus betöltő fájl és hogyan lehet a **TableRestProxy** osztályra hivatkozni.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Table\TableRestProxy;
```

Az alábbi példákban a `require_once` utasítás mindig látható, de csak a példa által végrehajtandó osztályokra hivatkozik.

## <a name="add-a-storage-table-service-connection"></a>Storage Table szolgáltatási kapcsolat hozzáadása
Storage Table szolgáltatási ügyfél példányosításához először is rendelkeznie kell egy érvényes kapcsolati sztringgel. A Storage szolgáltatás kapcsolati sztringjének formátuma:

```php
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"
```

## <a name="add-an-azure-cosmos-db-connection"></a>Azure Cosmos DB-kapcsolat hozzáadása
Azure Cosmos DB Table-ügyfél példányosításához először is rendelkeznie kell egy érvényes kapcsolati sztringgel. Az Azure Cosmos DB kapcsolati sztringjének formátuma:

```php
$connectionString = "DefaultEndpointsProtocol=[https];AccountName=[myaccount];AccountKey=[myaccountkey];TableEndpoint=[https://myendpoint/]";
```

## <a name="add-a-storage-emulator-connection"></a>Storage Emulator-kapcsolat hozzáadása
Az emulátor tárolójának elérése:

```php
UseDevelopmentStorage = true
```

Azure Table Service-ügyfél vagy Azure Cosmos DB-ügyfél létrehozásához a **TableRestProxy** osztályt kell használnia. Előnyök:

* A kapcsolati sztringet továbbíthatja közvetlenül, vagy
* használhatja a **CloudConfigurationManager (CCM)** eszközt, ha több külső forrást szeretne ellenőrizni a kapcsolati sztringhez:
  * Alapértelmezés szerint csak egy külső forrást támogat: a környezeti változókat.
  * Új forrásokat a `ConnectionStringSource` osztály kiterjesztésével adhat hozzá.

Az itt ismertetett példák esetében a kapcsolati sztringet közvetlenül továbbítjuk.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;

$tableClient = TableRestProxy::createTableService($connectionString);
```

## <a name="create-a-table"></a>Tábla létrehozása
A **TableRestProxy** objektum lehetővé teszi egy tábla létrehozását a **createTable** metódussal. Tábla létrehozásakor beállíthatja a Table Service időkorlátját. (További információ a Table service időtúllépésről: a [Table Service-műveletek időtúllépésének beállítása][table-service-timeouts].)

```php
require_once 'vendor\autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create Table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Create table.
    $tableClient->createTable("mytable");
}
catch(ServiceException $e){
    $code = $e->getCode();
    $error_message = $e->getMessage();
    // Handle exception based on error codes and messages.
    // Error codes and messages can be found here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
}
```

A táblázatos nevek korlátozásával kapcsolatos információkért lásd: [a Table Service adatmodell ismertetése][table-data-model].

## <a name="add-an-entity-to-a-table"></a>Entitás hozzáadása a táblához
Ha hozzá szeretne adni egy entitást egy táblához, hozzon létre egy új **Entity** objektumot, és továbbítsa a **TableRestProxy->insertEntity** metódushoz. Vegye figyelembe, hogy entitás létrehozásakor meg kell adnia egy `PartitionKey` és egy `RowKey` értéket. Ezek az entitás egyedi azonosítói, és az értékeik sokkal gyorsabban lekérdezhetők, mint a többi entitástulajdonság. A rendszer a `PartitionKey` használatával automatikusan elosztja a tábla entitásait számos tárcsomópont között. Az ugyanazzal a `PartitionKey` tulajdonsággal rendelkező entitások tárolása ugyanazon a csomóponton történik. (Az ugyanazon a csomóponton tárolt több entitás műveletei jobbak, mint a különböző csomópontokon tárolt entitások esetén.) A `RowKey` a partíción belüli entitás egyedi azonosítója.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$entity = new Entity();
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity->addProperty("Location", EdmType::STRING, "Home");

try{
    $tableClient->insertEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
}
```

További információ a táblázat tulajdonságairól és típusairól: [a Table Service adatmodell ismertetése][table-data-model].

A **TableRestProxy** osztály két alternatív metódust kínál az entitások beszúrásához: az **insertOrMergeEntity** és az **insertOrReplaceEntity** metódust. A két metódus használatához hozzon létre egy új **entitást**, és továbbítsa azt valamelyik metódusnak paraméterként. Mindkét metódus beszúrja az entitást, ha az nem létezik. Ha az entitás már létezik, az **insertOrMergeEntity** frissíti a tulajdonságértékeket, ha a tulajdonságok már léteznek, és új tulajdonságokat ad hozzá, ha még nem, míg az **insertOrReplaceEntity** teljesen lecseréli a meglévő entitást. A következő példa az **insertOrMergeEntity** használatát mutatja be. Ha a „taskSeattle” `PartitionKey`-értékkel, és az „1” `RowKey`-értékkel rendelkező entitás még nem létezik, akkor beszúrja. Ha azonban azt korábban már beszúrták (ahogyan a fenti példa mutatja), akkor frissíti a `DueDate` tulajdonságot, a `Status` tulajdonságot pedig hozzáadja. A `Description` és `Location` tulajdonságok is frissülnek, de olyan értékekkel, amelyek révén lényegében nem változnak. Ha a két utóbbi érték a példától eltérő módon nem lenne hozzáadva, de létezne a célentitáson, akkor meglévő értékeik változatlanok maradtak volna.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

//Create new entity.
$entity = new Entity();

// PartitionKey and RowKey are required.
$entity->setPartitionKey("tasksSeattle");
$entity->setRowKey("1");

// If entity exists, existing properties are updated with new values and
// new properties are added. Missing properties are unchanged.
$entity->addProperty("Description", null, "Take out the trash.");
$entity->addProperty("DueDate", EdmType::DATETIME, new DateTime()); // Modified the DueDate field.
$entity->addProperty("Location", EdmType::STRING, "Home");
$entity->addProperty("Status", EdmType::STRING, "Complete"); // Added Status field.

try    {
    // Calling insertOrReplaceEntity, instead of insertOrMergeEntity as shown,
    // would simply replace the entity with PartitionKey "tasksSeattle" and RowKey "1".
    $tableClient->insertOrMergeEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="retrieve-a-single-entity"></a>Egyetlen entitás lekérdezése
A **TableRestProxy->getEntity** metódus lehetővé teszi egyetlen entitás lekérését a `PartitionKey` és a `RowKey` tulajdonsága lekérdezésével. Az alábbi példában a `tasksSeattle` partíciókulcsot és az `1` sorkulcsot továbbítjuk a **getEntity** metódushoz.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    $result = $tableClient->getEntity("mytable", "tasksSeattle", 1);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entity = $result->getEntity();

echo $entity->getPartitionKey().":".$entity->getRowKey();
```

## <a name="retrieve-all-entities-in-a-partition"></a>Egy partíció összes entitásának lekérése
Az entitások lekérdezése szűrők használatával történik (További információ: [táblák és entitások lekérdezése][filters]). Ha egy partíció minden entitását le szeretné kérdezni, használja a "PartitionKey eq *partíció_neve*" szűrőt. Az alábbi példa bemutatja, hogyan kérheti le a `tasksSeattle` partíció minden entitását úgy, hogy egy szűrőt továbbít a **queryEntities** metódusnak.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "PartitionKey eq 'tasksSeattle'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entities-in-a-partition"></a>Entitások egy részének lekérése egy partíción belül
Az előző példában használt minta felhasználható entitások bármilyen részhalmazának lekérésére egy partíción belül. A lekért entitások részhalmazát a használt szűrő határozza meg (További információ: [táblák és entitások lekérdezése][filters]). Az alábbi példa azt mutatja be, hogyan használható egy szűrő az összes olyan entitás lekérésére, amely egy adott `Location` és egy `DueDate` a megadott dátumnál kisebb.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$filter = "Location eq 'Office' and DueDate lt '2012-11-5'";

try    {
    $result = $tableClient->queryEntities("mytable", $filter);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$entities = $result->getEntities();

foreach($entities as $entity){
    echo $entity->getPartitionKey().":".$entity->getRowKey()."<br />";
}
```

## <a name="retrieve-a-subset-of-entity-properties"></a>Az entitástulajdonságok egy részének lekérése
Egy lekérdezéssel lekérhető az entitástulajdonságok egy része. Ez a *leképezésnek* hívott technika csökkenti a sávszélesség felhasználását, és javíthatja a lekérdezési teljesítményt, főleg a nagy entitások esetén. Ha szeretne megadni egy lekérendő tulajdonságot, továbbítsa a tulajdonság nevét a **Query->addSelectField** metódusnak. Ezt a metódust többször is meghívhatja, ha több tulajdonságot szeretne hozzáadni. A **TableRestProxy->queryEntities** metódus végrehajtását követően a visszaadott entitások csak a kiválasztott tulajdonságokkal fognak rendelkezni. (Ha táblaentitások egy részét szeretné visszakapni, használjon a fenti lekérdezésekben szereplőhöz hasonló szűrőt).

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\QueryEntitiesOptions;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$options = new QueryEntitiesOptions();
$options->addSelectField("Description");

try    {
    $result = $tableClient->queryEntities("mytable", $options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

// All entities in the table are returned, regardless of whether
// they have the Description field.
// To limit the results returned, use a filter.
$entities = $result->getEntities();

foreach($entities as $entity){
    $description = $entity->getProperty("Description")->getValue();
    echo $description."<br />";
}
```

## <a name="update-an-entity"></a>Entitás frissítése
Meglévő entitás frissítéséhez használja az **Entity->setProperty** és az **Entity->addProperty** metódusokat az entitáson, majd hívja meg a **TableRestProxy->updateEntity** metódust. A következő példa visszaad egy entitást, módosít egy tulajdonságot, eltávolít egy másik tulajdonságot, és hozzáad egy újat. Ne feledje, hogy egy tulajdonságot úgy is eltávolíthat, ha **null**értéket ad meg hozzá.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

$result = $tableClient->getEntity("mytable", "tasksSeattle", 1);

$entity = $result->getEntity();
$entity->setPropertyValue("DueDate", new DateTime()); //Modified DueDate.
$entity->setPropertyValue("Location", null); //Removed Location.
$entity->addProperty("Status", EdmType::STRING, "In progress"); //Added Status.

try    {
    $tableClient->updateEntity("mytable", $entity);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="delete-an-entity"></a>Entitás törlése
Entitás törléséhez továbbítsa a tábla nevét, valamint az entitás `PartitionKey` és `RowKey` tulajdonságait a **TableRestProxy->deleteEntity** metódusnak.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete entity.
    $tableClient->deleteEntity("mytable", "tasksSeattle", "2");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Egyidejűségi ellenőrzések esetén beállíthatja az entitás ETagjének törlését a **DeleteEntityOptions->setEtag** metódus használatával és a **DeleteEntityOptions** objektum negyedik paraméterként való továbbításával a **deleteEntity** számára.

## <a name="batch-table-operations"></a>Kötegelt táblaműveletek
A **TableRestProxy->batch** metódus lehetővé teszi több művelet végrehajtását egyetlen kéréssel. A mintázat részét képezi műveletek hozzáadása a **BatchRequest** objektumhoz, majd a **BatchRequest** objektum továbbítása a **TableRestProxy->batch** metódusnak. Ha egy műveletet szeretne hozzáadni a **BatchRequest** objektumhoz, az alábbi metódusok bármelyikét többször is meghívhatja:

* **addInsertEntity** (hozzáad egy insertEntity műveletet)
* **addUpdateEntity** (hozzáad egy updateEntity műveletet)
* **addMergeEntity** (hozzáad egy mergeEntity műveletet)
* **addInsertOrReplaceEntity** (hozzáad egy insertOrReplaceEntity műveletet)
* **addInsertOrMergeEntity** (hozzáad egy insertOrMergeEntity műveletet)
* **addDeleteEntity** (hozzáad egy deleteEntity műveletet)

A következő példa az **insertEntity** és a **deleteEntity** művelet egy kérésben történő végrehajtásának módját mutatja be. 

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Table\Models\Entity;
use MicrosoftAzure\Storage\Table\Models\EdmType;
use MicrosoftAzure\Storage\Table\Models\BatchOperations;

// Configure a connection string for Storage Table service.
$connectionString = "DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]"

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

// Create list of batch operation.
$operations = new BatchOperations();

$entity1 = new Entity();
$entity1->setPartitionKey("tasksSeattle");
$entity1->setRowKey("2");
$entity1->addProperty("Description", null, "Clean roof gutters.");
$entity1->addProperty("DueDate",
                        EdmType::DATETIME,
                        new DateTime("2012-11-05T08:15:00-08:00"));
$entity1->addProperty("Location", EdmType::STRING, "Home");

// Add operation to list of batch operations.
$operations->addInsertEntity("mytable", $entity1);

// Add operation to list of batch operations.
$operations->addDeleteEntity("mytable", "tasksSeattle", "1");

try    {
    $tableClient->batch($operations);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

További információ a kötegelt tábla műveleteivel kapcsolatban: [entitás-csoport tranzakcióinak végrehajtása][entity-group-transactions].

## <a name="delete-a-table"></a>Tábla törlése
Végül tábla törléséhez továbbítsa a tábla nevét a **TableRestProxy->deleteTable** metódusnak.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Table\TableRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create table REST proxy.
$tableClient = TableRestProxy::createTableService($connectionString);

try    {
    // Delete table.
    $tableClient->deleteTable("mytable");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Table-Service-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte az Azure Table Service és az Azure Cosmos DB alapjait, az alábbi hivatkozásokra kattintva tudhat meg többet.

* A [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) egy ingyenes, önálló alkalmazás, amelynek segítségével vizuálisan dolgozhat Azure Storage-adatokkal Windows, macOS és Linux rendszereken.

* [PHP fejlesztői központ](https://azure.microsoft.com/develop/php/).

[download]: https://packagist.org/packages/microsoft/azure-storage-table
[require_once]: https://php.net/require_once
[table-service-timeouts]: https://docs.microsoft.com/rest/api/storageservices/setting-timeouts-for-table-service-operations

[table-data-model]: https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model
[filters]: https://docs.microsoft.com/rest/api/storageservices/Querying-Tables-and-Entities
[entity-group-transactions]: https://docs.microsoft.com/rest/api/storageservices/Performing-Entity-Group-Transactions
