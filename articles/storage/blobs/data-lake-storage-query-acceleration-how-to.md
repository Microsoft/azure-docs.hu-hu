---
title: Az Adatszűrés Azure Data Lake Storage lekérdezési gyorsítás használatával | Microsoft Docs
description: A lekérdezési gyorsítás használatával kérheti le az adatok egy részhalmazát a Storage-fiókból.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 5d5278c33c24c7f95459b9c121bca9e960615b9c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99260180"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Az Adatszűrés Azure Data Lake Storage lekérdezési gyorsítás használatával

Ebből a cikkből megtudhatja, hogyan használhatja a lekérdezési gyorsítást az adatok egy részhalmazának beolvasásához a Storage-fiókból. 

A lekérdezési gyorsítás lehetővé teszi, hogy az alkalmazások és az elemzési keretrendszerek jelentősen optimalizálják az adatfeldolgozást azáltal, hogy csak az adott művelet végrehajtásához szükséges adatok beolvasását végzik. További információ: [Azure Data Lake Storage lekérdezési gyorsítás](data-lake-storage-query-acceleration.md).

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Storage eléréséhez Azure-előfizetésre lesz szüksége. Ha még nem rendelkezik előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

- **Általános célú v2-** es Storage-fiók. Lásd: [Storage-fiók létrehozása](../common/storage-account-create.md).

- Válassza ki a fület az SDK-specifikus előfeltételek megtekintéséhez.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Nem alkalmazható

  ### <a name="net"></a>[.NET](#tab/dotnet)

  A [.net SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - A [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true) 8-as vagy újabb verziója

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > Ez a cikk azt feltételezi, hogy az Apache Maven használatával létrehozott egy Java-projektet. Az Apache Maven használatával történő projekt létrehozásával kapcsolatos példát itt talál: [beállítás](storage-quickstart-blobs-java.md#setting-up).
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3,8 vagy újabb.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  Az Node.js SDK használatához nincs szükség további előfeltételekre.

---

## <a name="enable-query-acceleration"></a>Lekérdezés gyorsításának engedélyezése

A lekérdezési gyorsítás használatához regisztrálnia kell a lekérdezés gyorsítására szolgáló funkciót az előfizetésében. Miután meggyőződött arról, hogy a szolgáltatás regisztrálva van, regisztrálnia kell az Azure Storage erőforrás-szolgáltatót. 

### <a name="step-1-register-the-query-acceleration-feature"></a>1. lépés: a lekérdezési gyorsítási funkció regisztrálása

A lekérdezési gyorsítás használatához először regisztrálnia kell a lekérdezés gyorsítására szolgáló funkciót az előfizetésében. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Nyisson meg egy Windows PowerShell-parancssori ablakot.

1. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

2. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetését.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

3. Regisztrálja a lekérdezési gyorsítási funkciót a [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) parancs használatával.

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nyissa meg a [Azure Cloud Shell](../../cloud-shell/overview.md), vagy ha helyileg [telepítette](/cli/azure/install-azure-cli) az Azure CLI-t, nyisson meg egy parancssori alkalmazást, például a Windows PowerShellt.

2. Ha az identitása egynél több előfizetéshez van társítva, akkor a Storage-fiók előfizetéséhez állítsa be az aktív előfizetést.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

3. Regisztrálja a lekérdezési gyorsítási funkciót az az [Feature Register](/cli/azure/feature#az-feature-register) paranccsal.

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>2. lépés: annak ellenőrzése, hogy a szolgáltatás regisztrálva van-e

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

A regisztráció befejezésének ellenőrzéséhez használja a [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) parancsot.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A regisztráció befejezésének ellenőrzéséhez használja az az [Feature](/cli/azure/feature#az-feature-show) parancsot.

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>3. lépés: az Azure Storage erőforrás-szolgáltató regisztrálása

A regisztráció jóváhagyása után újra regisztrálnia kell az Azure Storage erőforrás-szolgáltatót. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Az erőforrás-szolgáltató regisztrálásához használja a [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) parancsot.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az erőforrás-szolgáltató regisztrálásához használja az az [Provider Register](/cli/azure/provider#az-provider-register) parancsot.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>A környezet kialakítása

### <a name="step-1-install-packages"></a>1. lépés: csomagok telepítése 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Telepítse az az modul Version 4.6.0 vagy újabb verzióját.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Az az alkalmazás régebbi verziójáról történő frissítéshez futtassa a következő parancsot:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Nyisson meg egy parancssort, és módosítsa `cd` a könyvtárat () a projekt mappájába, például:

   ```console
   cd myProject
   ```

2. Telepítse az `12.5.0-preview.6` Azure Blob Storage ügyféloldali kódtára vagy újabb verzióját a .net-csomaghoz a parancs használatával `dotnet add package` . 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. A cikkben megjelenő példák egy CSV-fájlt elemeznek a [CsvHelper](https://www.nuget.org/packages/CsvHelper/) könyvtár használatával. A könyvtár használatához használja a következő parancsot.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Nyissa meg a projekt *pom.xml* fájlját egy szövegszerkesztőben. Adja hozzá az alábbi függőségi elemeket a függőségek csoportjához. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.6.0</version>
   </dependency>
    <dependency>
        <groupId>org.apache.commons</groupId>
        <artifactId>commons-csv</artifactId>
        <version>1.8</version>
    </dependency>    
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-storage-blob</artifactId>
      <version>12.8.0-beta.1</version>
    </dependency>
   ```

#### <a name="python"></a>[Python](#tab/python)

Telepítse a Pythonhoz készült Azure Data Lake Storage ügyféloldali kódtárat a [pip](https://pypi.org/project/pip/)használatával.

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Telepítse Data Lake ügyféloldali kódtárat a JavaScripthez egy terminál ablak megnyitásával, majd írja be a következő parancsot.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>2. lépés: utasítások hozzáadása

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nem alkalmazható

#### <a name="net"></a>[.NET](#tab/dotnet)

Adja hozzá ezeket `using` az utasításokat a fájl elejéhez.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

A lekérdezési gyorsítás lekéri a CSV-és a JSON-formátumú adatait. Ezért ügyeljen arra, hogy a használni kívánt CSV-vagy JSON-elemzési kódtárak használatával adjon hozzá utasításokat. A cikkben megjelenő példák egy CSV-fájlt elemeznek a NuGet-on elérhető [CsvHelper](https://www.nuget.org/packages/CsvHelper/) könyvtár használatával. Ezért ezeket az `using` utasításokat a fájl elejéhez adja.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

A cikkben bemutatott példák fordításához is hozzá kell adnia ezeket az `using` utasításokat is.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Adja hozzá ezeket `import` az utasításokat a fájl elejéhez.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.options.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import java.io.*;
import java.util.function.Consumer;
import org.apache.commons.csv.*;
```

#### <a name="python"></a>[Python](#tab/python)

Adja hozzá ezeket az importálási utasításokat a fájl elejéhez.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Vegye `storage-blob` fel a modult úgy, hogy az utasítást a programkód elejére helyezi. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

A lekérdezési gyorsítás lekéri a CSV-és a JSON-formátumú adatait. Ezért ügyeljen arra, hogy a használni kívánt CSV-vagy JSON-elemzési modulok utasításait adja hozzá. A cikkben megjelenő példák egy CSV-fájlt elemeznek a [gyors CSV-](https://www.npmjs.com/package/fast-csv) modul használatával. Ezért ezt az utasítást a fájl elejéhez adja.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Adatlekérdezés szűrő használatával

Az SQL segítségével megadhatja a sorcsoport-predikátumokat és az oszlopok kivetítéseit egy lekérdezési gyorsítási kérelemben. Az alábbi kód egy CSV-fájlt kérdez le a Storage szolgáltatásban, és visszaadja az összes olyan adatsort, amelyben a harmadik oszlop megfelel az értéknek `Hemingway, Ernest` . 

- Az SQL-lekérdezésben a kulcsszó a `BlobStorage` lekérdezni kívánt fájl jelölésére szolgál.

- Az oszlopok hivatkozásai az `_N` első oszlop helyeként vannak megadva `_1` . Ha a forrásfájl tartalmaz egy fejlécet, akkor az oszlopokat a fejlécsorban megadott név alapján lehet megtekinteni. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library.csv"
Get-QueryCsv $ctx $container $blob "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'" $false

```

### <a name="net"></a>[.NET](#tab/dotnet)

Az aszinkron metódus `BlobQuickQueryClient.QueryAsync` elküldi a lekérdezést a lekérdezési gyorsítási API-nak, majd [stream](/dotnet/api/system.io.stream) -objektumként továbbítja az eredményeket az alkalmazásnak.

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        var options = new BlobQueryOptions() {
            InputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = headers },
            OutputTextConfiguration = new BlobQueryCsvTextOptions() { HasHeaders = true },
            ProgressHandler = new Progress<long>((finishedBytes) => Console.Error.WriteLine($"Data read: {finishedBytes}"))
        };
        options.ErrorHandler += (BlobQueryError err) => {
            Console.ForegroundColor = ConsoleColor.Red;
            Console.Error.WriteLine($"Error: {err.Position}:{err.Name}:{err.Description}");
            Console.ResetColor();
        };
        // BlobDownloadInfo exposes a Stream that will make results available when received rather than blocking for the entire response.
        using (var reader = new StreamReader((await blob.QueryAsync(
                query,
                options)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture, hasHeaderRecord: true) { HasHeaderRecord = true }))
            {
                while (await parser.ReadAsync())
                {
                    Console.Out.WriteLine(String.Join(" ", parser.Parser.Record));
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

A metódus `BlobQuickQueryClient.openInputStream()` elküldi a lekérdezést a lekérdezési gyorsítási API-nak, majd az eredményeket visszaküldi az alkalmazásnak olyan `InputStream` objektumként, amely más InputStream-objektumhoz hasonlóan olvasható.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    DumpQueryCsv(blobClient, expression, true);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
        BlobQuerySerialization input = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');
        BlobQuerySerialization output = new BlobQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(true)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
        Consumer<BlobQueryError> errorConsumer = System.out::println;
        Consumer<BlobQueryProgress> progressConsumer = progress -> System.out.println("total bytes read: " + progress.getBytesScanned());
        BlobQueryOptions queryOptions = new BlobQueryOptions(query)
            .setInputSerialization(input)
            .setOutputSerialization(output)
            .setErrorConsumer(errorConsumer)
            .setProgressConsumer(progressConsumer);            

        /* Open the query input stream. */
        InputStream stream = blobClient.openQueryInputStream(queryOptions).getValue();
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL.withHeader())) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
        e.printStackTrace(System.err);
    }
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_hemingway(blob: BlobClient):
    query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'"
    dump_query_csv(blob, query, False)

def dump_query_csv(blob: BlobClient, query: str, headers: bool):
    qa_reader = blob.query_blob(query, blob_format=DelimitedTextDialect(has_header=headers), on_error=report_error, encoding='utf-8')
    # records() returns a generator that will stream results as received. It will not block pending all results.
    csv_reader = csv.reader(qa_reader.records())
    for row in csv_reader:
        print("*".join(row))
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ez a példa elküldi a lekérdezést a lekérdezési gyorsítási API-nak, majd visszaküldi az eredményeket vissza. A `blob` `queryHemingway` Helper függvénynek átadott objektum [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient)típusú. Ha többet szeretne megtudni a [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) -objektumok beszerzéséről, olvassa el a következőt: gyors útmutató [: Blobok kezelése a JavaScript V12 SDK-val Node.js](storage-quickstart-blobs-nodejs.md).

```javascript
async function queryHemingway(blob)
{
    const query = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest, 1899-1961'";
    await dumpQueryCsv(blob, query, false);
}

async function dumpQueryCsv(blob, query, headers)
{
    var response = await blob.query(query, {
        inputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: headers
        },
        outputTextConfiguration: {
            kind: "csv",
            recordSeparator: '\n',
            hasHeaders: true
        },
        onProgress: (progress) => console.log(`Data read: ${progress.loadedBytes}`),
        onError: (err) => console.error(`Error: ${err.position}:${err.name}:${err.description}`)});
    return new Promise(
        function (resolve, reject) {
            csv.parseStream(response.readableStreamBody)
                .on('data', row => console.log(row))
                .on('error', error => {
                    console.error(error);
                    reject(error);
                })
                .on('end', rowCount => resolve());
    });
}
```

---

## <a name="retrieve-specific-columns"></a>Adott oszlopok beolvasása

Az eredményeket az oszlopok egy részhalmazára is szűkítheti. Így csak az adott számítás végrehajtásához szükséges oszlopokat kéri le. Ez javítja az alkalmazások teljesítményét, és csökkenti a költségeket, mivel kevesebb adat kerül át a hálózaton keresztül. 

Ez a kód csak az `BibNum` adatkészletben lévő összes könyv oszlopát kérdezi le. Emellett a forrásfájl fejléc sorában található információkat is használja a lekérdezésben lévő oszlopokra.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$blob = "csv/csv-general/seattle-library-with-headers.csv"
Get-QueryCsv $ctx $container $blob "SELECT BibNum FROM BlobStorage" $true

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryBibNum(BlockBlobClient blob)
{
    string query = @"SELECT BibNum FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryBibNum(BlobClient blobClient)
{
    String expression = "SELECT BibNum FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_bibnum(blob: BlobClient):
    query = "SELECT BibNum FROM BlobStorage"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryBibNum(blob)
{
    const query = "SELECT BibNum FROM BlobStorage";
    await dumpQueryCsv(blob, query, true);
}
```

---

A következő kód kombinálja a sorok szűrését és az oszlopok kivetítését ugyanabba a lekérdezésbe. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
Get-QueryCsv $ctx $container $blob $query $true

Function Get-QueryCsv($ctx, $container, $blob, $query, $hasheaders) {
    $tempfile = New-TemporaryFile
    $informat = New-AzStorageBlobQueryConfig -AsCsv -HasHeader:$hasheaders
    Get-AzStorageBlobQueryResult -Context $ctx -Container $container -Blob $blob -InputTextConfiguration $informat -OutputTextConfiguration (New-AzStorageBlobQueryConfig -AsCsv -HasHeader) -ResultFile $tempfile.FullName -QueryString $query -Force
    Get-Content $tempfile.FullName
}

$container = "data"
$query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
            FROM BlobStorage 
            WHERE ItemType IN 
                ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"

```

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryDvds(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher, ItemType 
        FROM BlobStorage 
        WHERE ItemType IN 
            ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryDvds(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                        "FROM BlobStorage " +
                        "WHERE ItemType IN " +
                        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    DumpQueryCsv(blobClient, expression, true);
}
```

### <a name="python"></a>[Python](#tab/python)

```python
def query_dvds(blob: BlobClient):
    query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType "\
        "FROM BlobStorage "\
        "WHERE ItemType IN "\
        "   ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')"
    dump_query_csv(blob, query, True)
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
async function queryDvds(blob)
{
    const query = "SELECT BibNum, Title, Author, ISBN, Publisher, ItemType " +
                  "FROM BlobStorage " +
                  "WHERE ItemType IN " + 
                  " ('acdvd', 'cadvd', 'cadvdnf', 'calndvd', 'ccdvd', 'ccdvdnf', 'jcdvd', 'nadvd', 'nadvdnf', 'nalndvd', 'ncdvd', 'ncdvdnf')";
    await dumpQueryCsv(blob, query, true);
}
```

---

## <a name="next-steps"></a>Következő lépések

- [Azure Data Lake Storage lekérdezés gyorsulása](data-lake-storage-query-acceleration.md)
- [A lekérdezés gyorsításának SQL nyelvi referenciája](query-acceleration-sql-reference.md)
