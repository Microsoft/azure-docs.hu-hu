---
title: Az Azure Blob Storage kimeneti kötése Azure Functions
description: Ismerje meg, hogyan biztosíthat Azure-beli blob Storage-információkat egy Azure-függvénynek.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: tracking-python
ms.openlocfilehash: 56c11c2ae867769eb5eab00a2a6a3ecb616449b1
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84560021"
---
# <a name="azure-blob-storage-output-binding-for-azure-functions"></a>Az Azure Blob Storage kimeneti kötése Azure Functions

A kimeneti kötés lehetővé teszi blob Storage-adat módosítását és törlését egy Azure-függvényben.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-storage-blob.md).

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

A következő példa egy olyan [C#-függvény](functions-dotnet-class-library.md) , amely blob-triggert és két kimeneti blob-kötést használ. A függvényt egy képblob létrehozásával indítják el a *minta-rendszerkép* tárolóban. Létrehozza a képblob kis és közepes méretű másolatait.

```csharp
using System.Collections.Generic;
using System.IO;
using Microsoft.Azure.WebJobs;
using SixLabors.ImageSharp;
using SixLabors.ImageSharp.Formats;
using SixLabors.ImageSharp.PixelFormats;
using SixLabors.ImageSharp.Processing;

public class ResizeImages
{
    [FunctionName("ResizeImage")]
    public static void Run([BlobTrigger("sample-images/{name}")] Stream image,
        [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall,
        [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
    {
        IImageFormat format;

        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageSmall, ImageSize.Small, format);
        }

        image.Position = 0;
        using (Image<Rgba32> input = Image.Load<Rgba32>(image, out format))
        {
            ResizeImage(input, imageMedium, ImageSize.Medium, format);
        }
    }

    public static void ResizeImage(Image<Rgba32> input, Stream output, ImageSize size, IImageFormat format)
    {
        var dimensions = imageDimensionsTable[size];

        input.Mutate(x => x.Resize(dimensions.Item1, dimensions.Item2));
        input.Save(output, format);
    }

    public enum ImageSize { ExtraSmall, Small, Medium }

    private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
        { ImageSize.ExtraSmall, (320, 200) },
        { ImageSize.Small,      (640, 400) },
        { ImageSize.Medium,     (800, 600) }
    };

}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

<!--Same example for input and output. -->

A következő példa a blob bemeneti és kimeneti kötéseit ábrázolja egy *function. JSON* fájlban és [C# parancsfájlban (. CSX)](functions-reference-csharp.md) , amely a kötéseket használja. A függvény egy szöveges blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metaadatok tulajdonság a következő tulajdonságok alapján adható meg `path` :

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

<!--Same example for input and output. -->

Az alábbi példa a blob bemeneti és kimeneti kötéseit mutatja be egy *function. JSON* fájlban és [JavaScript-kódban](functions-reference-node.md) , amely a kötéseket használja. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metaadatok tulajdonság a következő tulajdonságok alapján adható meg `path` :

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

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

A következő példa egy *function. JSON* fájlban és a kötéseket használó [Python-kódban](functions-reference-python.md) mutatja be a blob bemeneti és kimeneti kötéseit. A függvény egy blob másolatát készíti el. A függvényt egy üzenetsor-üzenet indítja el, amely a másolandó blob nevét tartalmazza. Az új blob neve *{originalblobname} – Copy*.

A *function. JSON* fájlban a `queueTrigger` metaadatok tulajdonság a következő tulajdonságok alapján adható meg `path` :

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
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "outputblob",
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

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream,
         outputblob: func.Out[func.InputStream]):
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    outputblob.set(inputblob)
```

# <a name="java"></a>[Java](#tab/java)

Ez a szakasz a következő példákat tartalmazza:

* [HTTP-trigger a OutputBinding használatával](#http-trigger-using-outputbinding-java)
* [Üzenetsor-trigger, függvény visszatérési értékének használata](#queue-trigger-using-function-return-value-java)

#### <a name="http-trigger-using-outputbinding-java"></a>HTTP-trigger a OutputBinding (Java) használatával

 A következő példa egy Java-függvényt mutat be, amely a `HttpTrigger` jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó paraméter fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és továbbítja a tartalmát a függvénynek `byte[]` . A jegyzet a következőhöz `BlobOutput` kötődik `OutputBinding outputItem` :, amelyet a függvény használ a bemeneti blob tartalmának a konfigurált tárolóba való írásához.

```java
  @FunctionName("copyBlobHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage copyBlobHttp(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    @BlobOutput(
      name = "target", 
      path = "myblob/{Query.file}-CopyViaHttp")
    OutputBinding<String> outputItem,
    final ExecutionContext context) {
      // Save blob to outputItem
      outputItem.setValue(new String(content, StandardCharsets.UTF_8));

      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-using-function-return-value-java"></a>Üzenetsor-trigger, függvény visszatérési értéke (Java) használatával

 A következő példa egy Java-függvényt mutat be, amely a `QueueTrigger` jegyzetet használja a blob Storage-tárolóban található fájl nevét tartalmazó üzenet fogadására. A `BlobInput` jegyzet ezután beolvassa a fájlt, és továbbítja a tartalmát a függvénynek `byte[]` . A `BlobOutput` jegyzet a függvény visszatérési értékéhez kötődik, amelyet ezután a futtatókörnyezet használ a bemeneti blob tartalmának a konfigurált tárolóba való írásához.

```java
  @FunctionName("copyBlobQueueTrigger")
  @StorageAccount("Storage_Account_Connection_String")
  @BlobOutput(
    name = "target", 
    path = "myblob/{queueTrigger}-Copy")
  public String copyBlobQueue(
    @QueueTrigger(
      name = "filename", 
      dataType = "string",
      queueName = "myqueue-items") 
    String filename,
    @BlobInput(
      name = "file", 
      path = "samples-workitems/{queueTrigger}") 
    String content,
    final ExecutionContext context) {
      context.getLogger().info("The content of \"" + filename + "\" is: " + content);
      return content;
  }
```

 A [Java functions runtime library](/java/api/overview/azure/functions/runtime)-ben használja a `@BlobOutput` Megjegyzések a függvények azon paramétereit, amelyek értékét a blob Storage-ban lévő objektumba kívánja írni.  A paraméternek a következőnek kell lennie: `OutputBinding<T>` , ahol a T bármely natív Java-típus vagy POJO.

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs).

Az attribútum konstruktora a blob elérési útját és az `FileAccess` olvasási vagy írási paramétereket jelölő paramétert a következő példában látható módon veszi át:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
{
    ...
}
```

A tulajdonság beállításával `Connection` megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image,
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
{
    ...
}
```

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

Az `@BlobOutput` attribútum hozzáférést biztosít a függvényt kiváltó blobhoz. Ha az attribútummal rendelkező byte tömböt használ, állítsa a következőre: `dataType` `binary` . A részletekért tekintse meg a [kimeneti példát](#example) .

---

Teljes példa: [kimeneti példa](#example).

Az `StorageAccount` attribútummal megadhatja a Storage-fiókot az osztály, a metódus vagy a paraméter szintjén. További információ: [trigger-attributes](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations).

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a *function. JSON* fájlban és az attribútumban beállított kötési konfigurációs tulajdonságokat ismerteti `Blob` .

|function. JSON-tulajdonság | Attribútum tulajdonsága |Leírás|
|---------|---------|----------------------|
|**típusa** | n.a. | Értékre kell állítani `blob` . |
|**direction** | n.a. | `out`Kimeneti kötéshez be kell állítani. A kivételek a [használat](#usage) szakaszban vannak feltüntetve. |
|**név** | n.a. | A blobot jelölő változó neve a függvény kódjában.  Állítsa a értékre `$return` a függvény visszatérési értékének hivatkozásához.|
|**elérési útja** |**BlobPath** | A blob-tároló elérési útja. |
|**kapcsolat** |**Kapcsolat**| Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" értékre van állítva `connection` , a functions futtatókörnyezet egy "AzureWebJobsMyStorage" nevű alkalmazás-beállítást keres. Ha `connection` üresen hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű alkalmazás-beállításban `AzureWebJobsStorage` .<br><br>A kapcsolatok karakterláncának általános célú Storage-fiókhoz kell tartoznia, nem [csak blob Storage-fiókhoz](../storage/common/storage-account-overview.md#types-of-storage-accounts).|
|n.a. | **Hozzáférés** | Azt jelzi, hogy olvasás vagy írás történik-e. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-output-usage.md](../../includes/functions-bindings-blob-storage-output-usage.md)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScriptben a használatával férhet hozzá a blob-adataihoz `context.bindings.<name from function.json>` .

# <a name="python"></a>[Python](#tab/python)

A függvények paramétereinek deklarálása a blob Storage-ba való kiíráshoz a következő típusok szerint végezhető el:

* Karakterláncok mint`func.Out(str)`
* Stream as`func.Out(func.InputStream)`

A részletekért tekintse meg a [kimeneti példát](#example) .

# <a name="java"></a>[Java](#tab/java)

Az `@BlobOutput` attribútum hozzáférést biztosít a függvényt kiváltó blobhoz. Ha az attribútummal rendelkező byte tömböt használ, állítsa a következőre: `dataType` `binary` . A részletekért tekintse meg a [kimeneti példát](#example) .

---

## <a name="exceptions-and-return-codes"></a>Kivételek és visszatérési kódok

| Kötés |  Referencia |
|---|---|
| Blob | [BLOB-hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-error-codes) |
| BLOB, tábla, üzenetsor |  [Tárolási hibakódok](https://docs.microsoft.com/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| BLOB, tábla, üzenetsor |  [Hibaelhárítás](https://docs.microsoft.com/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Következő lépések

- [Függvény futtatása a blob Storage-beli adatváltozások esetén](./functions-bindings-storage-blob-trigger.md)
- [BLOB Storage-adat olvasása függvény futtatásakor](./functions-bindings-storage-blob-input.md)
