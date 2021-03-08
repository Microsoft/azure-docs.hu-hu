---
title: Azure Table Storage – kimeneti kötések Azure Functions
description: Ismerje meg, hogyan használható az Azure Table Storage kimeneti kötései a Azure Functionsban.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: f793f96f55a258b2d7cb11f214984416557618df
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453002"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Azure Table Storage – kimeneti kötések Azure Functions

Egy Azure Table Storage-beli kimeneti kötés használatával entitásokat írhat egy Azure Storage-fiókba tartozó táblába.

> [!NOTE]
> Ez a kimeneti kötés nem támogatja a meglévő entitások frissítését. `TableOperation.Replace`Egy meglévő entitás frissítéséhez használja az [Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity) műveletét.

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egy http-triggert használ egy egyoszlopos sor írásához.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

Az alábbi példa egy tábla kimeneti kötést mutat be egy *function.jsa* fájl-és [C#-parancsfájlhoz](functions-reference-csharp.md) , amely a kötést használja. A függvény több tábla entitást ír.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

# <a name="java"></a>[Java](#tab/java)

Az alábbi példa egy olyan Java-függvényt mutat be, amely egy HTTP-triggert használ egy egyoszlopos sor írásához.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

Az alábbi példa egy olyan Java-függvényt mutat be, amely egy HTTP-triggert használ több táblázat sorok írásához.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi példa egy tábla kimeneti kötést mutat be egy *function.jsa* fájlban, és egy [JavaScript-függvényt](functions-reference-node.md) , amely a kötést használja. A függvény több tábla entitást ír.

A fájl *function.js* :

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }

    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa azt mutatja be, hogyan lehet több entitást írni egy függvényből egy táblába.

function.jskötésének konfigurálása _a_ következőn:

```json
{
  "bindings": [
    {
      "name": "InputData",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "MyStorageConnectionAppSetting",
      "name": "TableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

PowerShell-kód a _run.ps1ban_:

```powershell
param($InputData, $TriggerMetadata)
  
foreach ($i in 1..10) {
    Push-OutputBinding -Name TableBinding -Value @{
        PartitionKey = 'Test'
        RowKey = "$i"
        Name = "Name $i"
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi példa bemutatja, hogyan használható a Table Storage kimeneti kötése. A `table` kötés úgy van konfigurálva a *function.jsban* , hogy értékeket rendel hozzá,, `name` `tableName` `partitionKey` és `connection` :

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

A következő függvény egyedi UUI hoz létre az `rowKey` értékhez, és megőrzi az üzenetet a Table Storage szolgáltatásban.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs).

Az attribútum konstruktora a tábla nevét adja meg. Az attribútum az `out` alábbi példában látható módon vagy a függvény visszatérési értékén is használható:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

A tulajdonság beállításával `Connection` megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

Teljes példaként tekintse meg a [C# példát](#example).

Az `StorageAccount` attribútummal megadhatja a Storage-fiókot az osztály, a metódus vagy a paraméter szintjén. További információ: [input-attributes](./functions-bindings-storage-table-input.md#attributes-and-annotations).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a paraméterek [TableOutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) jegyzetét, hogy értékeket írjon a Table Storage-ba.

[További részletekért](#example)tekintse meg a példát.

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
|**irányba** | n.a. | Értékre kell állítani `out` . A rendszer automatikusan beállítja ezt a tulajdonságot, amikor létrehozza a kötést a Azure Portalban. |
|**név** | n.a. | A táblát vagy entitást jelölő függvény kódjában használt változó neve. Állítsa a értékre `$return` a függvény visszatérési értékének hivatkozásához.| 
|**Táblanév** |**TableName** | A tábla neve.| 
|**partitionKey** |**PartitionKey** | Az írni kívánt tábla entitás partíciós kulcsa. Tekintse meg a [használat című szakaszt](#usage) , amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**rowKey** |**RowKey** | Az írni kívánt tábla entitáshoz tartozó sor kulcsa. Tekintse meg a [használat című szakaszt](#usage) , amely útmutatást nyújt ennek a tulajdonságnak a használatáról.| 
|**kapcsolat** |**Kapcsolat** | Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" értékre van állítva `connection` , a functions futtatókörnyezet egy "MyStorage" nevű alkalmazás-beállítást keres. Ha `connection` üresen hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű alkalmazás-beállításban `AzureWebJobsStorage` .|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

A kimeneti tábla entitásának elérése metódus-paraméterrel `ICollector<T> paramName` , vagy a (z `IAsyncCollector<T> paramName` `T` ) és a (z `PartitionKey` `RowKey` ) tulajdonságot tartalmazza. Ezeket a tulajdonságokat gyakran a megvalósítás vagy az `ITableEntity` öröklés kíséri `TableEntity` .

Azt is megteheti, `CloudTable` hogy az Azure Storage SDK használatával metódus paraméterrel ír a táblába. Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A kimeneti tábla entitásának elérése metódus-paraméterrel `ICollector<T> paramName` , vagy a (z `IAsyncCollector<T> paramName` `T` ) és a (z `PartitionKey` `RowKey` ) tulajdonságot tartalmazza. Ezeket a tulajdonságokat gyakran a megvalósítás vagy az `ITableEntity` öröklés kíséri `TableEntity` . Az `paramName` érték a `name` *function.js* tulajdonságában van megadva.

Azt is megteheti, `CloudTable` hogy az Azure Storage SDK használatával metódus paraméterrel ír a táblába. Ha egy hibaüzenetet próbál meg kötni `CloudTable` , és hibaüzenetet kap, ellenőrizze, hogy rendelkezik-e [a megfelelő Storage SDK-verzióra](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)mutató hivatkozással.

# <a name="java"></a>[Java](#tab/java)

Két lehetőség áll rendelkezésre a Table Storage-sorok függvényből történő kiosztására a [TableStorageOutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput) -jegyzet használatával:

- Visszaadott **érték**: a jegyzetnek a függvényhez való alkalmazásával a függvény visszatérési értéke Table Storage-sorként marad.

- **Fontos**: Ha explicit módon be szeretné állítani az üzenet értékét, alkalmazza a jegyzetet egy adott típusú paraméterre [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) , ahol `T` a tartalmazza a `PartitionKey` és a `RowKey` tulajdonságokat. Ezeket a tulajdonságokat gyakran a megvalósítás vagy az `ITableEntity` öröklés kíséri `TableEntity` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A kimeneti esemény eléréséhez használja `context.bindings.<name>` a `<name>` `name` *function.js* tulajdonságában megadott értéket.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ha táblázatos adatként szeretne írni, használja a `Push-OutputBinding` parancsmagot, `-Name TableBinding` és állítsa a paramétert és a `-Value` paramétert a sor értékével megegyező értékre. További részletekért tekintse meg a [PowerShell-példát](#example) .

# <a name="python"></a>[Python](#tab/python)

Két lehetőség áll rendelkezésre a táblázatos tárolási sor üzenetének egy függvényből való kiosztására:

- Visszaadott **érték**: állítsa be `name` *function.js* tulajdonságát a értékre `$return` . Ezzel a konfigurációval a függvény visszatérési értéke táblázatos tárolási sorként marad.

- **Elengedhetetlen**: adjon meg egy értéket a [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) metódusnak, amely [kimenő](/python/api/azure-functions/azure.functions.out) típusként van deklarálva. Az átadott érték az `set` Event hub-üzenetként is megmarad.

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés | Referencia |
|---|---|
| Tábla | [Tábla Hibakódai](/rest/api/storageservices/fileservices/table-service-error-codes) |
| BLOB, tábla, üzenetsor | [Tárolási hibakódok](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tábla, üzenetsor | [Hibaelhárítás](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)
