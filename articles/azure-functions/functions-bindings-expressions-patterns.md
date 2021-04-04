---
title: Azure Functions kötések és minták
description: Ismerje meg, hogyan hozhat létre különböző Azure Functions kötési kifejezéseket közös minták alapján.
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 7245b0c0fb1e96959ef5dca4992cf52a38accb58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920290"
---
# <a name="azure-functions-binding-expression-patterns"></a>Azure Functions kötési kifejezés mintái

Az [Eseményindítók és kötések](./functions-triggers-bindings.md) egyik leghatékonyabb funkciója a *kötés kifejezése*. A fájl *function.jsján* és a függvény paraméterei és kódjában használhat olyan kifejezéseket, amelyek különböző forrásokból származó értékekre vannak feloldva.

A legtöbb ilyen kifejezés könnyen azonosítható, mivel kapcsos zárójelek között vannak. Egy üzenetsor-kiváltó függvényben például a rendszer `{queueTrigger}` feloldja az üzenetsor-üzenet szövegét. Ha `path` egy blob kimeneti kötés tulajdonsága `container/{queueTrigger}` és a függvény üzenetsor-üzenettel aktiválódik `HelloWorld` , a rendszer létrehoz egy nevű blobot `HelloWorld` .

Kötéskifejezések típusai

* [Alkalmazásbeállítások](#binding-expressions---app-settings)
* [Eseményindító fájl neve](#trigger-file-name)
* [Eseményindító metaadatok](#trigger-metadata)
* [JSON-adattartalmak](#json-payloads)
* [Új GUID](#create-guids)
* [Aktuális dátum és idő](#current-time)

## <a name="binding-expressions---app-settings"></a>Kötési kifejezések – Alkalmazásbeállítások

Ajánlott eljárásként a titkokat és a kapcsolatok karakterláncait az Alkalmazásbeállítások használatával kell felügyelni, nem pedig a konfigurációs fájlokat. Ez korlátozza a hozzáférést ezekhez a titkokhoz, és biztonságos módon tárolja a fájlokat, például *afunction.jst a* nyilvános forrású vezérlő adattárakban.

Az Alkalmazásbeállítások akkor is hasznosak, ha módosítani szeretné a környezet alapján a konfigurációt. Tesztelési környezetben például érdemes lehet figyelni egy másik üzenetsor-vagy blob Storage-tárolót.

Az Alkalmazásbeállítások kötési kifejezései más kötési kifejezésektől eltérően vannak azonosítva: a rendszer a kapcsos zárójelek helyett százalékos jelekbe csomagolja őket. Ha például a blob kimeneti kötési útvonala, `%Environment%/newblob.txt` és az Alkalmazásbeállítások `Environment` értéke, a rendszer `Development` létrehoz egy blobot a `Development` tárolóban.

Ha egy függvény helyileg fut, az Alkalmazásbeállítások értéke a fájl *local.settings.js* származik.

> [!NOTE]
> Az `connection` Eseményindítók és kötések tulajdonsága egy speciális eset, amely automatikusan feloldja az értékeket az Alkalmazásbeállítások nélkül, százalékban megadva. 

Az alábbi példa egy Azure Queue Storage-trigger, amely egy Alkalmazásbeállítások használatával `%input_queue_name%` határozza meg, hogy a várólista aktiválva legyen.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input_queue_name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

Ugyanezt a megközelítést használhatja az osztályok könyvtáraiban:

```csharp
[FunctionName("QueueTrigger")]
public static void Run(
    [QueueTrigger("%input_queue_name%")]string myQueueItem, 
    ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
}
```

## <a name="trigger-file-name"></a>Eseményindító fájl neve

A `path` blob-triggerek esetében olyan minta lehet, amely lehetővé teszi, hogy az aktiváló blob nevét más kötésekben és a függvény kódjában tekintse át. A minta tartalmazhat olyan szűrési feltételeket is, amelyek meghatározzák, hogy mely Blobok indíthatnak el egy függvényt.

A blob-trigger következő kötésében például a minta a (z `path` ) `sample-images/{filename}` , amely létrehoz egy nevű kötési kifejezést `filename` :

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    ...
```

A kifejezés `filename` Ezután használható kimeneti kötésben a létrehozandó blob nevének megadásához:

```json
    ...
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

A függvény kódja paraméter neveként a következővel érheti el ugyanezt az értéket `filename` :

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, ILogger log)  
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->

A kötési kifejezések és minták használata is ugyanaz, mint az osztályok könyvtáraiban található attribútumok esetében. A következő példában az attribútumok konstruktorának paraméterei ugyanazokat az értékeket használják, `path` mint az előző *function.js* a példákban: 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger log)
{
    log.LogInformation($"Blob trigger processing: {filename}");
    // ...
}

```

A fájlnevek részeihez is létrehozhat kifejezéseket. A következő példában a függvény csak olyan fájlneveken aktiválódik, amelyek megfelelnek a mintának: `anyname-anyfile.csv`

```json
{
    "name": "myBlob",
    "type": "blobTrigger",
    "direction": "in",
    "path": "testContainerName/{date}-{filetype}.csv",
    "connection": "OrderStorageConnection"
}
```

A kifejezéseknek és mintáknak a blob Path karakterláncban történő használatáról további információt a [Storage blob kötési referenciájában](functions-bindings-storage-blob.md)talál.

## <a name="trigger-metadata"></a>Eseményindító metaadatok

Az eseményindító által biztosított adattartalom (például a függvényt kiváltó üzenetsor-üzenet tartalma) mellett számos eseményindító további metaadat-értékeket is biztosít. Ezek az értékek a C# és F # bemeneti paraméterként, illetve a JavaScriptben lévő objektum tulajdonságaiban is használhatók `context.bindings` . 

Az Azure üzenetsor-tárolói trigger például a következő tulajdonságokat támogatja:

* QueueTrigger – az üzenet tartalmának elindítása, ha érvényes karakterlánc
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

Ezek a metaadat-értékek a fájl tulajdonságainál *function.js* érhetők el. Tegyük fel például, hogy üzenetsor-triggert használ, és az üzenetsor-üzenet tartalmazza az olvasni kívánt blob nevét. A fájl *function.js* a `queueTrigger` blob tulajdonságban használhatja a metadata tulajdonságot a `path` következő példában látható módon:

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
}
```

Az egyes triggerek metaadat-tulajdonságainak részletes ismertetését a megfelelő hivatkozási cikk ismerteti. Példa: [üzenetsor-trigger metaadatainak](functions-bindings-storage-queue-trigger.md#message-metadata). A dokumentáció a portál Integration ( **integrálás** ) lapján, a kötési konfiguráció terület alatti **dokumentáció** szakaszban is elérhető.  

## <a name="json-payloads"></a>JSON-adattartalmak

Ha egy trigger adattartalma JSON, akkor a más kötésekhez tartozó tulajdonságokat a konfigurációban tekintheti meg ugyanabban a függvényben és a függvény kódjában.

A következő példa egy webhook-függvény fájljának *function.jsét* mutatja be, amely egy blob nevét kapja a JSON-ban: `{"BlobName":"HelloWorld.txt"}` . A blob bemeneti kötése beolvassa a blobot, és a HTTP-kimeneti kötés visszaadja a blob tartalmát a HTTP-válaszban. Figyelje meg, hogy a blob bemeneti kötése lekéri a blob nevét úgy, hogy közvetlenül a `BlobName` tulajdonságra hivatkozik ( `"path": "strings/{BlobName}"` )

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson"
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Ahhoz, hogy ez a C# és az F # használatával működjön, szüksége van egy osztályra, amely meghatározza a deszerializálni kívánt mezőket, ahogy az alábbi példában is látható:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents, ILogger log)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    log.LogInformation($"Processing: {info.BlobName}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

A JavaScriptben a JSON-deszerializálás automatikusan történik.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

### <a name="dot-notation"></a>Pont jelölése

Ha a JSON-adattartalom egyes tulajdonságai a tulajdonságokkal rendelkező objektumok, akkor ezeket közvetlenül a dot jelölés használatával tekintheti meg. Tegyük fel például, hogy a JSON így néz ki:

```json
{
  "BlobName": {
    "FileName":"HelloWorld",
    "Extension":"txt"
  }
}
```

Közvetlenül is hivatkozhat a következőre: `FileName` `BlobName.FileName` . Ezzel a JSON-formátummal az `path` előző példában szereplő tulajdonság a következőképpen fog kinézni:

```json
"path": "strings/{BlobName.FileName}.{BlobName.Extension}",
```

A C# nyelvben két osztályra van szükség:

```csharp
public class BlobInfo
{
    public BlobName BlobName { get; set; }
}
public class BlobName
{
    public string FileName { get; set; }
    public string Extension { get; set; }
}
```

## <a name="create-guids"></a>GUID-azonosítók létrehozása

A `{rand-guid}` kötési kifejezés létrehoz egy GUID azonosítót. Egy fájl következő blob-elérési útja `function.json` létrehoz egy blobot egy olyan névvel, mint a *50710cb5-84b9-4d87-9d83-a03d6976a682.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}.txt"
}
```

## <a name="current-time"></a>Aktuális idő

A kötési kifejezés a következőhöz lesz `DateTime` feloldva: `DateTime.UtcNow` . Egy fájl következő blob-elérési útja `function.json` létrehoz egy blobot egy olyan névvel, mint a *2018-02-16T17-59-55Z.txt*.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}.txt"
}
```
## <a name="binding-at-runtime"></a>Kötés futásidőben

C#-és egyéb .NET-nyelveken a kötelező kötési mintát használhatja, szemben a *function.json* és az attribútumokban található deklaratív kötésekkel. A kényszerített kötés akkor hasznos, ha a kötési paramétereket nem a tervezési idő, hanem futásidőben kell kiszámítani. További információért lásd a [C# fejlesztői referenciáját](functions-dotnet-class-library.md#binding-at-runtime) vagy a [c# parancsfájl fejlesztői referenciáját](functions-reference-csharp.md#binding-at-runtime).

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Az Azure Function Return értékének használata](./functions-bindings-return-value.md)
