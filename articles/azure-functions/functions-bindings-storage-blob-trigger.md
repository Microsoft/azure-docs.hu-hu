---
title: Azure Blob Storage-trigger Azure Functions
description: Ismerje meg, hogyan futtathat Azure-függvényeket Azure Blob Storage-beli adatváltozásokkal.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 1d83a828829d27d85749b3fa7b283cad9683bffc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455915"
---
# <a name="azure-blob-storage-trigger-for-azure-functions"></a>Azure Blob Storage-trigger Azure Functions

A blob Storage-trigger új vagy frissített blob észlelésekor egy függvényt indít el. A blob tartalma [bemenetként van megadva a függvénynek](./functions-bindings-storage-blob-input.md).

Az Azure Blob Storage-triggerhez általános célú Storage-fiók szükséges. A [hierarchikus névtereket](../storage/blobs/data-lake-storage-namespace.md) tartalmazó Storage v2-fiókok szintén támogatottak. Ha csak blobos fiókot szeretne használni, vagy ha az alkalmazás speciális igényekkel rendelkezik, tekintse át a trigger használatára vonatkozó alternatívákat.

További információ a telepítésről és a konfigurációról: [Áttekintés](./functions-bindings-storage-blob.md).

## <a name="polling"></a>Ciklikus lekérdezés

A lekérdezés hibridként működik a naplók vizsgálata és az időszakos tároló-ellenőrzések futtatása között. A Blobok a 10 000-es csoportokba vannak beolvasva, és az intervallumok közötti folytatási tokent használják.

> [!WARNING]
> Emellett a [tárolási naplók a "legjobb megoldás" alapon jönnek létre](/rest/api/storageservices/About-Storage-Analytics-Logging) . Nem garantálható, hogy minden esemény rögzítve legyen. Bizonyos körülmények között előfordulhat, hogy a naplók kimaradnak.
> 
> Ha gyorsabb vagy megbízhatóbb blob-feldolgozásra van szüksége, érdemes lehet üzenetsor- [üzenetet](../storage/queues/storage-dotnet-how-to-use-queues.md) létrehozni a blob létrehozásakor. Ezután használjon egy [üzenetsor-triggert](functions-bindings-storage-queue.md) a blob-trigger helyett a blob feldolgozásához. Egy másik lehetőség a Event Grid használata; Tekintse meg az oktatóanyagot, amely [automatizálja a feltöltött képek átméretezését Event Grid használatával](../event-grid/resize-images-on-storage-blob-upload-event.md).
>

## <a name="alternatives"></a>Alternatív megoldások

### <a name="event-grid-trigger"></a>Event Grid trigger

A [Event Grid trigger](functions-bindings-event-grid.md) a [blob eseményeinek](../storage/blobs/storage-blob-event-overview.md)beépített támogatásával is rendelkezik. A blob Storage-trigger helyett használja Event Grid a következő helyzetekben:

- **Csak blob Storage-fiókok**: a blobos bemeneti és kimeneti kötések, de a blob-eseményindítók esetében nem használhatók [blob-alapú tárolási fiókok](../storage/common/storage-account-overview.md#types-of-storage-accounts) .

- **Nagy léptékű**: a nagy léptékű tárolók lazán definiálhatók olyan tárolók, amelyek több mint 100 000 blobtal rendelkeznek, vagy amelyek másodpercenként több mint 100 blob-frissítéssel rendelkeznek.

- **Minimális késés**: Ha a Function alkalmazás a használati terven van, az új Blobok feldolgozására akár 10 percet is igénybe vehet, ha egy Function alkalmazás üresjáratban van. Ha el szeretné kerülni ezt a késést, átválthat egy App Service-csomagra az Always On enabled lehetőséggel. Egy [Event Grid triggert](functions-bindings-event-grid.md) is használhat a blob Storage-fiókkal. Példaként tekintse meg a [Event Grid oktatóanyagot](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json).

Tekintse meg a [képek átméretezését Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md) Event Grid példával kapcsolatos oktatóanyagot.

### <a name="queue-storage-trigger"></a>Queue Storage-eseményindító

A Blobok feldolgozásának másik módja a létrehozott vagy módosított bloboknak megfelelő üzenetsor-üzenetek írása, majd az [üzenetsor-tárolási trigger](./functions-bindings-storage-queue.md) használata a feldolgozás megkezdéséhez.

## <a name="example"></a>Példa

# <a name="c"></a>[C#](#tab/csharp)

Az alábbi példa egy [C#-függvényt](functions-dotnet-class-library.md) mutat be, amely egy naplót ír be, amikor a `samples-workitems` tárolóban blobot adnak hozzá vagy frissítenek.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

A `{name}` blob trigger elérési útjának karakterlánca `samples-workitems/{name}` egy [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#blob-name-patterns) a cikk későbbi részében.

További információ az `BlobTrigger` attribútumról: [attribútumok és jegyzetek](#attributes-and-annotations).

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A következő példa egy blob trigger kötést mutat be egy *function.jsa* fájlban és a kódban, amely a kötést használja. A függvény naplót ír, amikor egy blobot vesznek fel vagy frissülnek a `samples-workitems` [tárolóban](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

A *function.js* fájlban található kötési adatfájlok:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A `{name}` blob trigger elérési útjának karakterlánca `samples-workitems/{name}` egy [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#blob-name-patterns) a cikk későbbi részében.

További információ a fájltulajdonságok *function.jsáról* : [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Az alábbi C#-szkript a következőhöz kötődik `Stream` :

```cs
public static void Run(Stream myBlob, string name, ILogger log)
{
   log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

Az alábbi C#-szkript a következőhöz kötődik `CloudBlockBlob` :

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, ILogger log)
{
    log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

# <a name="java"></a>[Java](#tab/java)

Ez a függvény naplót ír a tárolóban lévő Blobok hozzáadásakor vagy frissítésekor `myblob` .

```java
@FunctionName("blobprocessor")
public void run(
  @BlobTrigger(name = "file",
               dataType = "binary",
               path = "myblob/{name}",
               connection = "MyStorageAccountAppSetting") byte[] content,
  @BindingName("name") String filename,
  final ExecutionContext context
) {
  context.getLogger().info("Name: " + filename + " Size: " + content.length + " bytes");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A következő példa egy blob trigger kötést mutat be egy *function.jsa* fájlban és a [JavaScript-kódban](functions-reference-node.md) , amely a kötést használja. A függvény naplót ír, amikor egy blobot vesznek fel vagy frissülnek a `samples-workitems` tárolóban.

A fájl *function.js* :

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A `{name}` blob trigger elérési útjának karakterlánca `samples-workitems/{name}` egy [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#blob-name-patterns) a cikk későbbi részében.

További információ a fájltulajdonságok *function.jsáról* : [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt látható a JavaScript-kód:

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi példa bemutatja, hogyan hozhat létre olyan függvényt, amely akkor fut le, amikor egy fájlt hozzáadnak a `source` blob Storage-tárolóhoz.

A függvény konfigurációs fájlja (_function.json_) tartalmaz egy kötést a (z) és a (z) értékkel `type` `blobTrigger` `direction` `in` .

```json
{
  "bindings": [
    {
      "name": "InputBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "source/{name}",
      "connection": "MyStorageAccountConnectionString"
    }
  ]
}
```

Itt látható a _run.ps1_ fájlhoz tartozó kód.

```powershell
param([byte[]] $InputBlob, $TriggerMetadata)

Write-Host "PowerShell Blob trigger: Name: $($TriggerMetadata.Name) Size: $($InputBlob.Length) bytes"
```

# <a name="python"></a>[Python](#tab/python)

A következő példa egy blob trigger kötést mutat be egy *function.jsa* fájlban és a [Python-kódban](functions-reference-python.md) , amely a kötést használja. A függvény naplót ír, amikor egy blobot vesznek fel vagy frissülnek a `samples-workitems` [tárolóban](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources).

A fájl *function.js* :

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,
    "bindings": [
        {
            "name": "myblob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems/{name}",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

A `{name}` blob trigger elérési útjának karakterlánca `samples-workitems/{name}` egy [kötési kifejezést](./functions-bindings-expressions-patterns.md) hoz létre, amelyet a függvény kódjában használhat az indító blob fájlnevének eléréséhez. További információ: [blob Name Patterns](#blob-name-patterns) a cikk későbbi részében.

További információ a fájltulajdonságok *function.jsáról* : [konfigurációs](#configuration) szakasz ismerteti ezeket a tulajdonságokat.

Itt látható a Python-kód:

```python
import logging
import azure.functions as func


def main(myblob: func.InputStream):
    logging.info('Python Blob trigger function processed %s', myblob.name)
```

---

## <a name="attributes-and-annotations"></a>Attribútumok és jegyzetek

# <a name="c"></a>[C#](#tab/csharp)

A [C# osztályok könyvtáraiban](functions-dotnet-class-library.md)használja a következő attribútumokat a blob-triggerek konfigurálásához:

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobTriggerAttribute.cs)

  Az attribútum konstruktora egy elérésiút-karakterláncot vesz fel, amely jelzi a tárolót, és opcionálisan egy [blob-nevet](#blob-name-patterns). Bemutatunk egy példát:

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
  ```

  A tulajdonság beállításával `Connection` megadhatja a használni kívánt Storage-fiókot, ahogy az az alábbi példában is látható:

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image,
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  {
      ....
  }
   ```

  A teljes példa: [trigger példa](#example).

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)

  Egy másik módszert biztosít a használni kívánt Storage-fiók megadására. A konstruktor egy olyan Alkalmazásbeállítás nevét veszi fel, amely egy tárolási kapcsolatot tartalmazó karakterláncot tartalmaz. Az attribútumot a paraméter, a metódus vagy az osztály szintjén lehet alkalmazni. Az alábbi példa az osztály szintjét és a metódus szintjét mutatja be:

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  {
      ....
  }
  ```

A használandó Storage-fiók a következő sorrendben van meghatározva:

* Az `BlobTrigger` attribútum `Connection` tulajdonsága.
* A `StorageAccount` attribútummal megegyező paraméterre alkalmazott attribútum `BlobTrigger` .
* A `StorageAccount` függvényre alkalmazott attribútum.
* Az `StorageAccount` osztályra alkalmazott attribútum.
* A Function alkalmazás alapértelmezett Storage-fiókja ("AzureWebJobsStorage").

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

A C# parancsfájl nem támogatja az attribútumokat.

# <a name="java"></a>[Java](#tab/java)

Az `@BlobTrigger` attribútummal hozzáférést biztosíthat a függvényt kiváltó blobhoz. A részletekért tekintse meg az [trigger példáját](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A JavaScript nem támogatja az attribútumokat.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A PowerShell nem támogatja az attribútumokat.

# <a name="python"></a>[Python](#tab/python)

A Python nem támogatja az attribútumokat.

---

## <a name="configuration"></a>Konfiguráció

Az alábbi táblázat a fájl és attribútum *function.jsjában* beállított kötési konfigurációs tulajdonságokat ismerteti `BlobTrigger` .

|function.jsa tulajdonságon | Attribútum tulajdonsága |Description|
|---------|---------|----------------------|
|**típusa** | n.a. | Értékre kell állítani `blobTrigger` . Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban.|
|**irányba** | n.a. | Értékre kell állítani `in` . Ez a tulajdonság automatikusan be van állítva, amikor létrehozza az triggert a Azure Portalban. A kivételek a [használat](#usage) szakaszban vannak feltüntetve. |
|**név** | n.a. | A blobot jelölő változó neve a függvény kódjában. |
|**elérési útja** | **BlobPath** |A figyelni kívánt [tároló](../storage/blobs/storage-blobs-introduction.md#blob-storage-resources) .  A [blob neve minta](#blob-name-patterns)lehet. |
|**kapcsolat** | **Kapcsolat** | Egy olyan Alkalmazásbeállítás neve, amely a kötéshez használandó tárolási kapcsolati karakterláncot tartalmazza. Ha az Alkalmazásbeállítások neve "AzureWebJobs" előtaggal kezdődik, akkor itt csak a nevet adja meg. Ha például a "MyStorage" értékre van állítva `connection` , a functions futtatókörnyezet egy "AzureWebJobsMyStorage" nevű alkalmazás-beállítást keres. Ha `connection` üresen hagyja, a functions futtatókörnyezet az alapértelmezett tárolási kapcsolatok karakterláncát használja a nevű alkalmazás-beállításban `AzureWebJobsStorage` .<br><br>A kapcsolatok karakterláncának általános célú Storage-fiókhoz kell tartoznia, nem [blob Storage-fiókhoz](../storage/common/storage-account-overview.md#types-of-storage-accounts).<br><br>Ha [a bővítmény 5. x vagy újabb verzióját](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)használja, a kapcsolati karakterlánc helyett megadhat egy olyan konfigurációs szakaszra mutató hivatkozást, amely meghatározza a kapcsolódást. Lásd: [kapcsolatok](./functions-reference.md#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Használat

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-trigger.md)]

# <a name="java"></a>[Java](#tab/java)

Az `@BlobTrigger` attribútummal hozzáférést biztosíthat a függvényt kiváltó blobhoz. A részletekért tekintse meg az [trigger példáját](#example) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A Blobok adataihoz való hozzáférés `context.bindings.<NAME>` `<NAME>` a *function.js* által megadott értéknek felel meg.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A blob-adatelérést olyan paraméterrel érheti el, amely megegyezik a kötés Name paraméterében megadott névvel a fájl _function.js_ .

# <a name="python"></a>[Python](#tab/python)

A blob-adatelérést a [InputStream](/python/api/azure-functions/azure.functions.inputstream)típussal megadott paraméterrel érheti el. A részletekért tekintse meg az [trigger példáját](#example) .

---

## <a name="blob-name-patterns"></a>BLOB nevének mintázata

A blob neve mintát megadhatja a `path` tulajdonságban *function.json* vagy az `BlobTrigger` Attribute konstruktorban. A név minta lehet [szűrő vagy kötési kifejezés](./functions-bindings-expressions-patterns.md)is. A következő szakasz példákat tartalmaz.

> [!TIP]
> A tároló neve nem tartalmazhat feloldót a név mintában.

### <a name="get-file-name-and-extension"></a>Fájl nevének és kiterjesztésének beolvasása

Az alábbi példa bemutatja, hogyan köthető a blob-fájl nevéhez és kiterjesztéséhez:

```json
"path": "input/{blobname}.{blobextension}",
```

Ha a blob neve *original-Blob1.txt*, a (z) és a (z `blobname` `blobextension` ) függvény kódjában szereplő változók értékei az *eredeti-Blob1* és a *txt*.

### <a name="filter-on-blob-name"></a>Szűrés a blob nevén

Az alábbi példa csak a tárolóban lévő blobokat indítja `input` el, amelyek az "Original-" karakterlánccal kezdődnek:

```json
"path": "input/original-{name}",
```

Ha a blob neve *original-Blob1.txt*, a `name` függvény kódjában található változó értéke `Blob1.txt` .

### <a name="filter-on-file-type"></a>Szűrés fájltípus alapján

A következő példa csak a *. png* fájlok esetében indít el:

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>Kapcsos zárójelek szűrése fájlnevekben

Ha kapcsos zárójeleket szeretne keresni a fájlnevekben, a kapcsos zárójeleket két zárójel használatával kell kipróbálni. A következő példa azokat a blobokat szűri, amelyek kapcsos zárójelekkel rendelkeznek a névben:

```json
"path": "images/{{20140101}}-{name}",
```

Ha a blob neve *{20140101}-soundfile.mp3*, a `name` függvény kódjában található változó értéke *soundfile.mp3*.

## <a name="metadata"></a>Metaadatok

# <a name="c"></a>[C#](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="c-script"></a>[C#-parancsfájl](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-trigger](../../includes/functions-bindings-blob-storage-metadata.md)]

# <a name="java"></a>[Java](#tab/java)

A metaadatok nem érhetők el a javában.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
module.exports = function (context, myBlob) {
    context.log("Full blob path:", context.bindingData.blobTrigger);
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A metaadatok a `$TriggerMetadata` paraméterrel érhetők el.

# <a name="python"></a>[Python](#tab/python)

A metaadatok nem érhetők el a Pythonban.

---

## <a name="blob-receipts"></a>BLOB-visszaigazolások

A Azure Functions futtatókörnyezet biztosítja, hogy a blob trigger függvény többször is meghívja ugyanazt az új vagy frissített blobot. Annak megállapításához, hogy egy adott blob-verzió feldolgozása megtörtént-e, a *blob-visszaigazolásokat* tart fenn.

Azure Functions a blob-visszaigazolásokat egy *Azure-webjobs* nevű tárolóban tárolja – az Azure Storage-fiókban lévő gazdagépeket a Function alkalmazáshoz (amelyet az alkalmazás-beállítás határoz meg `AzureWebJobsStorage` ). A blob-visszaigazolás a következő információkat tartalmazhatja:

* Az aktivált függvény ( `<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>` például: `MyFunctionApp.Functions.CopyBlob` )
* A tároló neve
* A blob típusa ( `BlockBlob` vagy `PageBlob` )
* A blob neve
* A ETag (a blob verziószáma, például: `0x8D1DC6E70A277EF` )

Egy blob újrafeldolgozásának kényszerítéséhez törölje az adott blobhoz tartozó blob-elismervényt az *Azure-webjobs-hosts* tárolóból manuálisan. Előfordulhat, hogy az újrafeldolgozás nem azonnal, hanem egy későbbi időpontban történik. Az azonnali újrafeldolgozáshoz az *Azure-webjobs-hosts/blobscaninfo* *scaninfo* -blob is frissíthető. Minden olyan blob, amelynek utolsó módosítási időbélyege a tulajdonság ismételt vizsgálatát követően történik `LatestScan` .

## <a name="poison-blobs"></a>Megmérgezés Blobok

Ha egy blob trigger-függvény meghibásodik egy adott blobnál, Azure Functions az újrapróbálkozások alapértelmezés szerint összesen 5 alkalommal működnek.

Ha mind az 5 próbálkozás sikertelen, Azure Functions hozzáadja az üzenetet egy *webjobs-blobtrigger-méreg* nevű Storage-várólistához. Az újrapróbálkozások maximális száma konfigurálható. Ugyanez a MaxDequeueCount-beállítás a blob-kezelő és a méreg üzenetsor-üzenetek kezelésére szolgál. A méreg-Blobok üzenetsor-üzenete egy JSON-objektum, amely a következő tulajdonságokat tartalmazza:

* FunctionId (formátum `<FUNCTION_APP_NAME>.Functions.<FUNCTION_NAME>` )
* BlobType ( `BlockBlob` vagy `PageBlob` )
* ContainerName
* BlobName
* ETag (blob-verzió azonosítója, például: `0x8D1DC6E70A277EF` )

## <a name="concurrency-and-memory-usage"></a>Egyidejűség és memóriahasználat

A blob-trigger belsőleg használ egy várólistát, így az egyidejű függvények maximális számát a [host.js-ben lévő várólisták konfigurációja](functions-host-json.md#queues)vezérli. Az alapértelmezett beállítások a párhuzamosságot 24 hívásra korlátozzák. Ez a korlát külön vonatkozik a blob-triggert használó összes függvényre.

> [!NOTE]
> A [5.0.0 vagy újabb verzióját](functions-bindings-storage-blob.md#storage-extension-5x-and-higher)használó alkalmazások esetén a várólisták konfigurálása host.json csak üzenetsor-eseményindítók esetén érvényes. A blob-trigger egyidejűségét Ehelyett a Blobok konfigurációja vezérli a [host.json](functions-host-json.md#blobs).

[A használati terv az](event-driven-scaling.md) egyik virtuális GÉPEN (VM) lévő function alkalmazást 1,5 GB memóriára korlátozza. A memóriát a párhuzamosan futó és a functions Runtime is használja. Ha egy blob által aktivált függvény a teljes blobot betölti a memóriába, az adott függvény által a Blobok által használt maximális memória 24 * a blob maximális mérete. Például a három blob által aktivált függvényt használó Function alkalmazás, valamint az alapértelmezett beállítások esetében a 3 * 24 = 72 függvény meghívása esetén a virtuális gépek maximális száma.

A JavaScript és a Java függvények a teljes blobot a memóriába töltik be, a C#-függvények pedig a következőhöz kötődnek: `string` vagy `Byte[]` .

## <a name="hostjson-properties"></a>Tulajdonságok host.js

A fájl [host.js](functions-host-json.md#blobs) a blob-trigger viselkedését vezérlő beállításokat tartalmaz. A rendelkezésre álló beállításokkal kapcsolatos részletekért tekintse [ meg ahost.json Settings](functions-bindings-storage-blob.md#hostjson-settings) szakaszt.

## <a name="next-steps"></a>Következő lépések

- [BLOB Storage-adat olvasása függvény futtatásakor](./functions-bindings-storage-blob-input.md)
- [BLOB Storage-adatok írása függvényből](./functions-bindings-storage-blob-output.md)
