---
title: Azure Table Storage – bemeneti kötések Azure Functions
description: Ismerje meg, hogyan használhatók az Azure Table Storage-beli beviteli kötések Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 4fc2426189384856d2d2e95887cdabd2f9e9ebea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98033778"
---
# <a name="azure-table-storage-input-bindings-for-azure-functions"></a>Azure Table Storage – bemeneti kötések Azure Functions

Egy Azure Storage-fiókban lévő tábla beolvasásához használja az Azure Table Storage bemeneti kötését.

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

### <a name="one-entity"></a>Egy entitás

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egy egyoszlopos sort olvas. A rendszer a várólistára küldött összes üzenet esetében elindítja a függvényt.

A (z) "{queueTrigger}" sorcsoport-érték azt jelzi, hogy a sor kulcsa az üzenetsor-üzenet sztringből származik.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition", "{queueTrigger}")] MyPoco poco, 
        ILogger log)
    {
        log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
    }
}
```

### <a name="cloudtable"></a>CloudTable

`CloudTable` csak a [functions v2 és a nagyobb futtatókörnyezetekben](functions-versions.md)támogatott.

A `CloudTable` tábla az Azure Storage SDK használatával történő beolvasásához használja a Method paramétert. Az alábbi példa egy olyan függvényt mutat be, amely egy Azure Functions naplózási táblázatot kérdez le:

```csharp
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos.Table;
using System;
using System.Threading.Tasks;

namespace FunctionAppCloudTable2
{
    public class LogEntity : TableEntity
    {
        public string OriginalName { get; set; }
    }
    public static class CloudTableDemo
    {
        [FunctionName("CloudTableDemo")]
        public static async Task Run(
            [TimerTrigger("0 */1 * * * *")] TimerInfo myTimer, 
            [Table("AzureWebJobsHostLogscommon")] CloudTable cloudTable,
            ILogger log)
        {
            log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

            TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
                TableQuery.CombineFilters(
                    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
                        "FD2"),
                    TableOperators.And,
                    TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
                        "t")));

            // Execute the query and loop through the results
            foreach (LogEntity entity in 
                await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
            {
                log.LogInformation(
                    $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
            }
        }
    }
}
```

További információ a CloudTable használatáról: Ismerkedés [Az Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md)szolgáltatással.

Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

### <a name="iqueryable"></a>IQueryable

`IQueryable` csak a [functions v1 futtatókörnyezetben](functions-versions.md)támogatott.

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely több olyan tábla sort olvas be, amelyből a `MyPoco` osztály származik `TableEntity` .

```csharp
public class TableStorage
{
    public class MyPoco : TableEntity
    {
        public string Text { get; set; }
    }

    [FunctionName("TableInput")]
    public static void TableInput(
        [QueueTrigger("table-items")] string input, 
        [Table("MyTable", "MyPartition")] IQueryable<MyPoco> pocos, 
        ILogger log)
    {
        foreach (MyPoco poco in pocos)
        {
            log.LogInformation($"PK={poco.PartitionKey}, RK={poco.RowKey}, Text={poco.Text}");
        }
    }
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

### <a name="one-entity"></a>Egy entitás

Az alábbi példa egy táblázatos beviteli kötést mutat be egy *function.jsa* fájl-és [C#-parancsfájlhoz](functions-reference-csharp.md) , amely a kötést használja. A függvény üzenetsor-triggert használ egy egytáblázatos sor olvasására. 

A fájl *function.js* az a és a paramétert adja meg `partitionKey` `rowKey` . A `rowKey` (z) {queueTrigger} érték azt jelzi, hogy a sor kulcsa az üzenetsor-üzenet sztringből származik.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```csharp
public static void Run(string myQueueItem, Person personEntity, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    log.LogInformation($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

### <a name="cloudtable"></a>CloudTable

`IQueryable` nem támogatott a [2. x vagy újabb verziók](functions-versions.md)functions futtatókörnyezetében. Egy másik megoldás, ha `CloudTable` metódus-paramétert használ a tábla olvasásához az Azure Storage SDK használatával. Az alábbi példa egy olyan függvényt mutat be, amely egy Azure Functions naplózási táblázatot kérdez le:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 */1 * * * *"
    },
    {
      "name": "cloudTable",
      "type": "table",
      "connection": "AzureWebJobsStorage",
      "tableName": "AzureWebJobsHostLogscommon",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using System;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, CloudTable cloudTable, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");

    TableQuery<LogEntity> rangeQuery = new TableQuery<LogEntity>().Where(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, 
            "FD2"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, 
            "a")));

    // Execute the query and loop through the results
    foreach (LogEntity entity in 
    await cloudTable.ExecuteQuerySegmentedAsync(rangeQuery, null))
    {
        log.LogInformation(
            $"{entity.PartitionKey}\t{entity.RowKey}\t{entity.Timestamp}\t{entity.OriginalName}");
    }
}

public class LogEntity : TableEntity
{
    public string OriginalName { get; set; }
}
```

További információ a CloudTable használatáról: Ismerkedés [Az Azure Table Storage](../cosmos-db/tutorial-develop-table-dotnet.md)szolgáltatással.

Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

### <a name="iqueryable"></a>IQueryable

Az alábbi példa egy táblázatos beviteli kötést mutat be egy *function.jsa* fájl-és [C#-parancsfájlhoz](functions-reference-csharp.md) , amely a kötést használja. A függvény beolvassa az entitásokat egy üzenetsor-üzenetben megadott partíciós kulcshoz.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnectionAppSetting",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C#-szkript kód az Azure Storage SDK-ra mutató hivatkozást tartalmaz, hogy az entitás típusa a következőből származtatható `TableEntity` :

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Extensions.Logging;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.LogInformation($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy HTTP által aktivált függvényt mutat be, amely egy, a Table Storage-ban megadott partícióban található, személyre szabott objektumok listáját adja vissza. A példában a partíciós kulcsot a rendszer kinyeri a http-útvonalból, és a táblanév és a kapcsolatok a függvény beállításaiból származnak. 

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() { return this.PartitionKey; }
    public void setPartitionKey(String key) { this.PartitionKey = key; }
    public String getRowKey() { return this.RowKey; }
    public void setRowKey(String key) { this.RowKey = key; }
    public String getName() { return this.Name; }
    public void setName(String name) { this.Name = name; }
}

@FunctionName("getPersonsByPartitionKey")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}") HttpRequestMessage<Optional<String>> request,
        @BindingName("partitionKey") String partitionKey,
        @TableInput(name="persons", partitionKey="{partitionKey}", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with partition key: " + partitionKey);

    return persons;
}
```

A TableInput jegyzet a kérelem JSON-törzsében is kinyerheti a kötéseket, például az alábbi példában látható módon.

```java
@FunctionName("GetPersonsByKeysFromRequest")
public HttpResponseMessage get(
        @HttpTrigger(name = "getPerson", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="query") HttpRequestMessage<Optional<String>> request,
        @TableInput(name="persons", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") Person person,
        final ExecutionContext context) {

    if (person == null) {
        return request.createResponseBuilder(HttpStatus.NOT_FOUND)
                    .body("Person not found.")
                    .build();
    }

    return request.createResponseBuilder(HttpStatus.OK)
                    .header("Content-Type", "application/json")
                    .body(person)
                    .build();
}
```

Az alábbi példa a szűrő használatával kérdez le egy Azure-táblázatban megadott névvel rendelkező személyeket, és korlátozza a lehetséges egyezések számát 10 találatra.

```java
@FunctionName("getPersonsByName")
public Person[] get(
        @HttpTrigger(name = "getPersons", methods = {HttpMethod.GET}, authLevel = AuthorizationLevel.FUNCTION, route="filter/{name}") HttpRequestMessage<Optional<String>> request,
        @BindingName("name") String name,
        @TableInput(name="persons", filter="Name eq '{name}'", take = "10", tableName="%MyTableName%", connection="MyConnectionString") Person[] persons,
        final ExecutionContext context) {

    context.getLogger().info("Got query for person related to persons with name: " + name);

    return persons;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy tábla beviteli kötést mutat be egy *function.jsa* fájlban és a [JavaScript-kódban](functions-reference-node.md) , amely a kötést használja. A függvény üzenetsor-triggert használ egy egytáblázatos sor olvasására. 

A fájl *function.js* az a és a paramétert adja meg `partitionKey` `rowKey` . A `rowKey` (z) {queueTrigger} érték azt jelzi, hogy a sor kulcsa az üzenetsor-üzenet sztringből származik.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A következő függvény egy üzenetsor-triggert használ egy függvény bemenetének beolvasásához.

Ebben a példában a kötési konfiguráció egy explicit értéket ad meg a tábla számára, `partitionKey` és egy kifejezést használ a értékre való továbbításhoz `rowKey` . A `rowKey` kifejezés `{queueTrigger}` azt jelzi, hogy a sor kulcsa az üzenetsor-üzenet sztringből származik.

function.jskötésének konfigurálása _a_ következőn:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "MyQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "PersonEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

PowerShell-kód a _run.ps1ban_:

```powershell
param($MyQueueItem, $PersonEntity, $TriggerMetadata)
Write-Host "PowerShell queue trigger function processed work item: $MyQueueItem"
Write-Host "Person entity name: $($PersonEntity.Name)"
```

# <a name="python"></a>[Python](#tab/python)

A következő függvény egy üzenetsor-triggert használ egy függvény bemenetének beolvasásához.

Ebben a példában a kötési konfiguráció egy explicit értéket ad meg a tábla számára, `partitionKey` és egy kifejezést használ a értékre való továbbításhoz `rowKey` . A `rowKey` kifejezés `{id}` azt jelzi, hogy a sor kulcsa az üzenetsor-üzenet sztringből származik.

Kötési konfiguráció a fájl _function.jsjában_ :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "messageJSON",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "rowKey": "{id}",
      "connection": "AzureWebJobsStorage",
      "direction": "in"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ],
      "route": "messages/{id}"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ],
  "disabled": false
}
```

Python-kód az *\_ \_ init \_ \_ .* rajzfájl fájlban:

```python
import json

import azure.functions as func

def main(req: func.HttpRequest, messageJSON) -> func.HttpResponse:

    message = json.loads(messageJSON)
    return func.HttpResponse(f"Table row: {messageJSON}")
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

 A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat egy tábla bemeneti kötésének konfigurálásához:

* [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)

  Az attribútum konstruktora a tábla nevét, a partíció kulcsát és a sor kulcsát veszi figyelembe. Az attribútum az `out` alábbi példában látható módon vagy a függvény visszatérési értékén is használható:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  A tulajdonság beállításával `Connection` megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

  ```csharp
  [FunctionName("TableInput")]
  public static void Run(
      [QueueTrigger("table-items")] string input, 
      [Table("MyTable", "Http", "{queueTrigger}", Connection = "StorageConnectionAppSetting")] MyPoco poco, 
      ILogger log)
  {
      ...
  }
  ```

  Teljes példaként tekintse meg a C# példát.

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módszert biztosít a használni kívánt Storage-fiók megadására. A konstruktor egy olyan Alkalmazásbeállítás nevét veszi fel, amely egy tárolási kapcsolatot tartalmazó karakterláncot tartalmaz. Az attribútumot a paraméter, a metódus vagy az osztály szintjén lehet alkalmazni. Az alábbi példa az osztály szintjét és a metódus szintjét mutatja be:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("TableInput")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ...
  }
  ```

A használandó Storage-fiók a következő sorrendben van meghatározva:

* Az `Table` attribútum `Connection` tulajdonsága.
* A `StorageAccount` attribútummal megegyező paraméterre alkalmazott attribútum `Table` .
* A `StorageAccount` függvényre alkalmazott attribútum.
* Az `StorageAccount` osztályra alkalmazott attribútum.
* A Function alkalmazás alapértelmezett Storage-fiókja ("AzureWebJobsStorage").

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@TableInput` jegyzeteket azon paramétereknél, amelyek értéke a Table Storage-ból származik.  Ez a jegyzet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel használható a használatával `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `Table` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n.a. | Értékre kell állítani `table` . A rendszer automatikusan beállítja ezt a tulajdonságot, amikor létrehozza a kötést a Azure Portalban.|
|**irányba** | n.a. | Értékre kell állítani `in` . A rendszer automatikusan beállítja ezt a tulajdonságot, amikor létrehozza a kötést a Azure Portalban. |
|**név** | n.a. | Annak a változónak a neve, amely a függvény kódjában szereplő táblát vagy entitást jelképezi. | 
|**Táblanév** | **TableName** | A tábla neve.| 
|**partitionKey** | **PartitionKey** |Választható. Az olvasni kívánt tábla entitás partíciós kulcsa. Tekintse meg a [használat](#usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**rowKey** |**RowKey** | Választható. Az olvasni kívánt tábla entitáshoz tartozó sor kulcsa. Tekintse meg a [használat](#usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**eltarthat** |**Eltarthat** | Választható. A JavaScriptben olvasható entitások maximális száma. Tekintse meg a [használat](#usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**szűrő** |**Szűrő** | Választható. OData-szűrési kifejezés a JavaScriptben a tábla beviteléhez. Tekintse meg a [használat](#usage) című szakaszt, amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**kapcsolat** |**Kapcsolat** | Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. A beállítás lehet egy "AzureWebJobs" előre rögzített alkalmazás vagy a kapcsolatok karakterláncának neve. Ha például a beállítás neve "AzureWebJobsMyStorage", itt adhatja meg a "MyStorage" értéket. A functions futtatókörnyezet automatikusan megkeresi a "AzureWebJobsMyStorage" nevű alkalmazás-beállítást. Ha `connection` üresen hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű alkalmazás-beállításban `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

* **Egy sor olvasása a következőben:**

  Beállítás `partitionKey` és `rowKey` . A tábla adataihoz a metódus paraméterének használatával férhet hozzá `T <paramName>` . A C#-szkriptben a `paramName` `name` *function.js* tulajdonságában megadott érték van megadva. `T` általában olyan típus, amely `ITableEntity` a-ból implementál vagy származtat `TableEntity` . A `filter` és a `take` Tulajdonságok nem használatosak ebben a forgatókönyvben.

* **Egy vagy több sor olvasása**

  A tábla adataihoz a metódus paraméterének használatával férhet hozzá `IQueryable<T> <paramName>` . A C#-szkriptben a `paramName` `name` *function.js* tulajdonságában megadott érték van megadva. `T` olyan típusnak kell lennie, amely `ITableEntity` a-ből implementál vagy származtat `TableEntity` . `IQueryable`A módszerek használatával bármilyen szűrést végezhet. A `partitionKey` , `rowKey` , `filter` és `take` tulajdonságokat nem használja ebben a forgatókönyvben.  

  > [!NOTE]
  > `IQueryable` nem támogatott a [functions v2 futtatókörnyezetben](functions-versions.md). Egy másik lehetőség egy [CloudTable paramName metódus paraméter használata](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) a tábla olvasásához az Azure Storage SDK használatával. Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

* **Egy sor olvasása a következőben:**

  Beállítás `partitionKey` és `rowKey` . A tábla adataihoz a metódus paraméterének használatával férhet hozzá `T <paramName>` . A C#-szkriptben a `paramName` `name` *function.js* tulajdonságában megadott érték van megadva. `T` általában olyan típus, amely `ITableEntity` a-ból implementál vagy származtat `TableEntity` . A `filter` és a `take` Tulajdonságok nem használatosak ebben a forgatókönyvben.

* **Egy vagy több sor olvasása**

  A tábla adataihoz a metódus paraméterének használatával férhet hozzá `IQueryable<T> <paramName>` . A C#-szkriptben a `paramName` `name` *function.js* tulajdonságában megadott érték van megadva. `T` olyan típusnak kell lennie, amely `ITableEntity` a-ből implementál vagy származtat `TableEntity` . `IQueryable`A módszerek használatával bármilyen szűrést végezhet. A `partitionKey` , `rowKey` , `filter` és `take` tulajdonságokat nem használja ebben a forgatókönyvben.  

  > [!NOTE]
  > `IQueryable` nem támogatott a [functions v2 futtatókörnyezetben](functions-versions.md). Egy másik lehetőség egy [CloudTable paramName metódus paraméter használata](https://stackoverflow.com/questions/48922485/binding-to-table-storage-in-v2-azure-functions-using-cloudtable) a tábla olvasásához az Azure Storage SDK használatával. Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="java"></a>[Java](#tab/java)

A [TableInput](/java/api/com.microsoft.azure.functions.annotation.tableinput) attribútum hozzáférést biztosít a függvényt kiváltó tábla sorához.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Adja meg a `filter` és a `take` tulajdonságokat. Nincs beállítva `partitionKey` vagy `rowKey` . A bemeneti tábla entitás (vagy entitások) elérése a használatával `context.bindings.<BINDING_NAME>` . A deszerializált objektumok rendelkeznek `RowKey` és `PartitionKey` tulajdonságokkal rendelkeznek.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az adatokat a rendszer a `name` fájl *function.js* kulcsában megadott bemeneti paraméternek adja át. A és a `partitionKey` `rowKey` lehetővé teszi a szűrést adott rekordokra. További részletekért tekintse meg a [PowerShell-példát](#example) .

# <a name="python"></a>[Python](#tab/python)

A tábla adatát a rendszer JSON-karakterláncként adja át a függvénynek. Az üzenet deszerializálása a `json.loads` bemeneti [példában](#example)látható módon.

---

## <a name="next-steps"></a>Következő lépések

* [Táblázatos tárolási adatok írása függvényből](./functions-bindings-storage-table-output.md)
