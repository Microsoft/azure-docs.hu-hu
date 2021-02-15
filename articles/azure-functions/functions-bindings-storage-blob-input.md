---
title: Az Azure Blob Storage bemeneti kötése Azure Functions
description: Ismerje meg, hogyan biztosíthatja az Azure Blob Storage-beli bemeneti kötési adatokat egy Azure-függvénynek.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 678b6f02245e463870fc5b2c4bfc8b5ffa50de60
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381689"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Az Azure Blob Storage bemeneti kötése Azure Functions

A bemeneti kötés lehetővé teszi a blob Storage-adatok bemenetként való beolvasását egy Azure-függvénybe.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-storage-blob.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

A következő példa egy [C#-függvény](functions-dotnet-class-library.md) , amely egy üzenetsor-triggert és egy bemeneti blob-kötést használ. Az üzenetsor-üzenet tartalmazza a blob nevét, és a függvény naplózza a blob méretét.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

<!--Same example for input and output. -->

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy olyan *function.jsa* fájl-és [C#-parancsfájl (. CSX)](functions-reference-csharp.md) kódjában, amely a kötéseket használja. A függvény egy szöveges blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A fájl *function.js* a `queueTrigger` metaadatok tulajdonság használatával adja meg a blob nevét a `path` Tulajdonságok között:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

A C# szkript kódja:

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="java"></a>[Java](#tab/java)

Ez a szakasz a következő példákat tartalmazza:

* [HTTP-trigger, keresse meg a blob nevét a lekérdezési karakterláncból](#http-trigger-look-up-blob-name-from-query-string)
* [Üzenetsor-trigger, a blob nevének fogadása az üzenetsor-üzenetből](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP-trigger, keresse meg a blob nevét a lekérdezési karakterláncból

 A következő példa egy Java-függvényt mutat be, amely a `HttpTrigger` jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó paraméter fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és továbbítja a tartalmát a függvénynek `byte[]` .

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>Üzenetsor-trigger, a blob nevének fogadása az üzenetsor-üzenetből

 A következő példa egy Java-függvényt mutat be, amely a `QueueTrigger` jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó üzenet fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és továbbítja a tartalmát a függvénynek `byte[]` .

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@BlobInput` jegyzeteket azon paramétereknél, amelyek értéke egy blobból származik.  Ez a jegyzet natív Java-típusokkal, Szerializálói vagy NULL értékű értékekkel használható a használatával `Optional<T>` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy *function.jsa* fájl-és [JavaScript-kódban](functions-reference-node.md) , amely a kötéseket használja. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A fájl *function.js* a `queueTrigger` metaadatok tulajdonság használatával adja meg a blob nevét a `path` Tulajdonságok között:

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
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

Itt látható a JavaScript-kód:

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa egy blob bemeneti kötést mutat be, amely a _function.js_ fájlon van meghatározva, amely a bejövő blob-adatokat elérhetővé teszi a [PowerShell](functions-reference-powershell.md) -függvény számára.

Itt látható a JSON-konfiguráció:

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

A függvény kódja:

```powershell
# Input bindings are passed in via param block.
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy *function.jsa* fájlban és a [Python-kódban](functions-reference-python.md) , amely a kötéseket használja. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A fájl *function.js* a `queueTrigger` metaadatok tulajdonság használatával adja meg a blob nevét a `path` Tulajdonságok között:

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "dataType": "binary",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

A [konfigurációs](#configuration) szakasz ezeket a tulajdonságokat ismerteti.

A `dataType` tulajdonság határozza meg, hogy melyik kötést használja a rendszer. A következő értékek érhetők el különböző kötési stratégiák támogatásához:

| Kötési érték | Alapértelmezett | Leírás | Példa |
| --- | --- | --- | --- |
| `undefined` | Y | Gazdag kötést használ | `def main(input: func.InputStream)` |
| `string` | N | Általános kötést használ, és a bemeneti típust `string` | `def main(input: str)` |
| `binary` | N | Általános kötést használ, és a bemeneti blobot `bytes` Python-objektumként adja át | `def main(input: bytes)` |

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Az attribútum konstruktora a blob elérési útját és az `FileAccess` olvasási vagy írási paramétereket jelölő paramétert a következő példában látható módon veszi át:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

A tulajdonság beállításával `Connection` megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

Az `StorageAccount` attribútummal megadhatja a Storage-fiókot az osztály, a metódus vagy a paraméter szintjén. További információ: [trigger-attributes and jegyzetek](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

Az `@BlobInput` attribútum hozzáférést biztosít a függvényt kiváltó blobhoz. Ha az attribútummal rendelkező byte tömböt használ, állítsa a következőre: `dataType` `binary` . A részletekért tekintse meg a [bemeneti példát](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `Blob` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**típusa** | n.a. | Értékre kell állítani `blob` . |
|**irányba** | n.a. | Értékre kell állítani `in` . A kivételek a [használat](#usage) szakaszban vannak feltüntetve. |
|**név** | n.a. | A blobot jelölő változó neve a függvény kódjában.|
|**elérési útja** |**BlobPath** | A blob elérési útja. |
|**kapcsolat** |**Kapcsolat**| Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó [tárolási kapcsolati karakterláncot](../storage/common/storage-configure-connection-string.md) tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" értékre van állítva `connection` , a functions futtatókörnyezet egy "AzureWebJobsMyStorage" nevű alkalmazás-beállítást keres. Ha `connection` üresen hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű alkalmazás-beállításban `AzureWebJobsStorage` .<br><br>A kapcsolatok karakterláncának általános célú Storage-fiókhoz kell tartoznia, nem [csak blob Storage-fiókhoz](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Ha [a bővítmény 5. x vagy újabb verzióját](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)használja, a kapcsolati karakterlánc helyett megadhat egy olyan konfigurációs szakaszra mutató hivatkozást, amely meghatározza a kapcsolódást. Lásd: [kapcsolatok](./functions-reference.md#connections).|
|**dataType**| n.a. | A dinamikusan beírt nyelvek esetében az alapul szolgáló adattípus adható meg. A lehetséges értékek: `string` , `binary` , vagy `stream` . További részletekért tekintse meg az [Eseményindítók és a kötések fogalmait](functions-triggers-bindings.md?tabs=python#trigger-and-binding-definitions). |
|n.a. | **Hozzáférés** | Azt jelzi, hogy olvasás vagy írás történik-e. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="java"></a>[Java](#tab/java)

Az `@BlobInput` attribútum hozzáférést biztosít a függvényt kiváltó blobhoz. Ha az attribútummal rendelkező byte tömböt használ, állítsa a következőre: `dataType` `binary` . A részletekért tekintse meg a [bemeneti példát](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A Blobok adataihoz való hozzáférés `context.bindings.<NAME>` `<NAME>` a *function.js* által megadott értéknek felel meg.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A blob-adatelérést olyan paraméterrel érheti el, amely megegyezik a kötés Name paraméterében megadott névvel a fájl _function.js_ .

# <a name="python"></a>[Python](#tab/python)

A blob-adatelérést a [InputStream](/python/api/azure-functions/azure.functions.inputstream?view=azure-python&preserve-view=true)típussal megadott paraméterrel érheti el. A részletekért tekintse meg a [bemeneti példát](#example) .

---

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása a blob Storage-beli adatváltozások esetén](./functions-bindings-storage-blob-trigger.md)
- [BLOB Storage-adatok írása függvényből](./functions-bindings-storage-blob-output.md)
