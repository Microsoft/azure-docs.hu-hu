---
title: Adatok szűrése lekérdezésgyorsítási Azure Data Lake Storage használatával | Microsoft Docs
description: Lekérdezésgyorsítással lekérheti az adatok egy részkészletét a tárfiókból.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 01/06/2021
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp
ms.openlocfilehash: 58b8cdef604861342a6489ef4e57ff1d057cd3f4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377734"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration"></a>Adatok szűrése a lekérdezésgyorsítás Azure Data Lake Storage használatával

Ez a cikk bemutatja, hogyan használhatja a lekérdezésgyorsítást az adatok egy részkészletének a tárfiókból való lekéréséhez. 

A lekérdezésgyorsítás lehetővé teszi, hogy az alkalmazások és az elemzési keretrendszerek jelentősen optimalizálják az adatfeldolgozást azáltal, hogy csak az adott művelet végrehajtásához szükséges adatokat kérik le. További információ: Azure Data Lake Storage [lekérdezésgyorsítás.](data-lake-storage-query-acceleration.md)

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Storage eléréséhez Szüksége lesz egy Azure-előfizetésre. Ha még nem rendelkezik előfizetéssel, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) kezdés előtt hozzon létre egy ingyenes fiókot.

- Általános **célú v2-tárfiók.** lásd: [Tárfiók létrehozása.](../common/storage-account-create.md)

- Válasszon egy lapot az SDK-ra vonatkozó előfeltételek megtekintéséhez.

  ### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

  Nem alkalmazható

  ### <a name="net"></a>[.NET](#tab/dotnet)

  A [.NET SDK](https://dotnet.microsoft.com/download) 

  ### <a name="java"></a>[Java](#tab/java)

  - [Java fejlesztői készlet (JDK)](/java/azure/jdk/) 8-as vagy újabb verziója

  - [Apache Maven](https://maven.apache.org/download.cgi) 

    > [!NOTE] 
    > Ez a cikk feltételezi, hogy létrehozott egy Java-projektet az Apache Maven használatával. A projektek Apache Maven használatával való létrehozására vonatkozó példáért lásd: [Setting up (Beállítás).](storage-quickstart-blobs-java.md#setting-up)
  
  ### <a name="python"></a>[Python](#tab/python)

  [Python](https://www.python.org/downloads/) 3.8 vagy nagyobb.

  ### <a name="nodejs"></a>[Node.js](#tab/nodejs)

  A Node.js SDK használatának nincs további előfeltétele.

---

## <a name="enable-query-acceleration"></a>Lekérdezésgyorsítás engedélyezése

A lekérdezésgyorsítás használatához regisztrálnia kell a lekérdezésgyorsítási funkciót az előfizetésében. Miután ellenőrizte, hogy a funkció regisztrálva van-e, regisztrálnia kell az Azure Storage erőforrás-szolgáltatót. 

### <a name="step-1-register-the-query-acceleration-feature"></a>1. lépés: A lekérdezésgyorsítási funkció regisztrálása

A lekérdezésgyorsítás használatához először regisztrálnia kell a lekérdezésgyorsítási funkciót az előfizetésében. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Nyisson meg Windows PowerShell parancsablakot.

1. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

2. Ha az identitása több előfizetéshez is társítva van, állítsa be az aktív előfizetést.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Cserélje le `<subscription-id>` a helyőrző értékét az előfizetése azonosítójára.

3. Regisztrálja a lekérdezésgyorsítási funkciót a [Register-AzProviderFeature paranccsal.](/powershell/module/az.resources/register-azproviderfeature)

   ```powershell
   Register-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
   ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nyissa meg [a Azure Cloud Shell,](../../cloud-shell/overview.md)vagy ha [](/cli/azure/install-azure-cli) helyileg telepítette az Azure CLI-t, nyisson meg egy parancskonzol-alkalmazást, például a Windows PowerShell.

2. Ha az identitása több előfizetéshez is társítva van, állítsa be az aktív előfizetést a tárfiók előfizetéséhez.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Cserélje le `<subscription-id>` a helyőrző értékét az előfizetése azonosítójára.

3. Regisztrálja a lekérdezésgyorsítási funkciót az [az feature register paranccsal.](/cli/azure/feature#az-feature-register)

   ```azurecli
   az feature register --namespace Microsoft.Storage --name BlobQuery
   ```

---

### <a name="step-2-verify-that-the-feature-is-registered"></a>2. lépés: A funkció regisztrációja ellenőrzése

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

A regisztráció befejezésének ellenőrzéséhez használja a [Get-AzProviderFeature parancsot.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName BlobQuery
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A regisztráció befejezésének ellenőrzéséhez használja [az az feature](/cli/azure/feature#az-feature-show) parancsot.

```azurecli
az feature show --namespace Microsoft.Storage --name BlobQuery
```

---

### <a name="step-3-register-the-azure-storage-resource-provider"></a>3. lépés: Az Azure Storage erőforrás-szolgáltató regisztrálása

A regisztráció jóváhagyása után újra regisztrálnia kell az Azure Storage erőforrás-szolgáltatót. 

#### <a name="powershell"></a>[PowerShell](#tab/powershell)

Az erőforrás-szolgáltató regisztráláshoz használja a [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) parancsot.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az erőforrás-szolgáltató regisztráláshoz használja [az az provider register](/cli/azure/provider#az-provider-register) parancsot.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

---

## <a name="set-up-your-environment"></a>A környezet kialakítása

### <a name="step-1-install-packages"></a>1. lépés: Csomagok telepítése 

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Telepítse az Az modul 4.6.0-s vagy újabb verzióját.

```powershell
Install-Module -Name Az -Repository PSGallery -Force
```

Az Az egy régebbi verziójáról való frissítéshez futtassa a következő parancsot:

```powershell
Update-Module -Name Az
```

#### <a name="net"></a>[.NET](#tab/dotnet)

1. Nyisson meg egy parancssort, és módosítsa a könyvtárat ( `cd` ) a projektmappába, például:

   ```console
   cd myProject
   ```

2. Telepítse az Azure Blob Storage .NET-csomaghoz való ügyféloldali kódtárának vagy újabb verzióját `12.5.0-preview.6` az `dotnet add package` paranccsal. 

   ```console
   dotnet add package Azure.Storage.Blobs -v 12.8.0
   ```

3. Az ebben a cikkben található példák egy CSV-fájlt elemeznek a [CsvHelper kódtár](https://www.nuget.org/packages/CsvHelper/) használatával. A kódtár a következő paranccsal használható.

   ```console
   dotnet add package CsvHelper
   ```

#### <a name="java"></a>[Java](#tab/java)

1. Nyissa meg *pom.xml* projekt fájlját egy szövegszerkesztőben. Adja hozzá a következő függőségi elemeket a függőségek csoportjához. 

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

Telepítse a Azure Data Lake Storage Pythonhoz készült ügyféloldali kódtárat a [pip használatával.](https://pypi.org/project/pip/)

```
pip install azure-storage-blob==12.4.0
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Telepítse a JavaScripthez használt Data Lake ügyféloldali kódtárat egy terminálablak megnyitásával, majd írja be a következő parancsot.

```javascript
    npm install @azure/storage-blob
    npm install @fast-csv/parse
```

---

### <a name="step-2-add-statements"></a>2. lépés: Utasítások hozzáadása

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Nem alkalmazható

#### <a name="net"></a>[.NET](#tab/dotnet)

Adja hozzá `using` ezeket az utasításokat a kódfájl tetejéhez.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
```

A lekérdezésgyorsítás CSV- és Json-formátumú adatokat ad vissza. Ezért mindenképpen adjon hozzá using utasításokat a használni választott CSV- vagy Json-elemző kódtárakhoz. A cikkben megjelenő példák egy CSV-fájlt elemeznek a NuGeten elérhető [CsvHelper](https://www.nuget.org/packages/CsvHelper/) kódtár használatával. Ezért ezeket az utasításokat a kódfájl tetejéhez `using` adjuk.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

A cikkben bemutatott példák fordítására ezeket az utasításokat is hozzá `using` kell adni.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
```

#### <a name="java"></a>[Java](#tab/java)

Adja hozzá `import` ezeket az utasításokat a kódfájl tetejéhez.

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

Adja hozzá ezeket az importálási utasításokat a kódfájl tetejéhez.

```python
import sys, csv
from azure.storage.blob import BlobServiceClient, ContainerClient, BlobClient, DelimitedTextDialect, BlobQueryError
```

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

A modult úgy foglalhatja bele, hogy elhelyezi ezt az utasítást `storage-blob` a kódfájl tetején. 

```javascript
const { BlobServiceClient } = require("@azure/storage-blob");
```

A lekérdezésgyorsítás CSV- és Json-formátumú adatokat ad vissza. Ezért ügyeljen arra, hogy utasításokat adjon hozzá a használni választott CSV- vagy Json-elemző modulokhoz. A cikkben megjelenő példák egy CSV-fájlt elemeznek a [fast-csv modullal.](https://www.npmjs.com/package/fast-csv) Ezért ezt az utasítást a kódfájl tetejéhez adjuk.

```javascript
const csv = require('@fast-csv/parse');
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Adatok lekérése szűrővel

Az SQL használatával megadhatja a sorszűrő predikátumokat és oszloplevetítéseket egy lekérdezésgyorsítási kérelemben. A következő kód lekérdez egy CSV-fájlt a tárolóban, és visszaadja az összes olyan adatsort, ahol a harmadik oszlop megegyezik a `Hemingway, Ernest` értékkel. 

- Az SQL-lekérdezésben a kulcsszó jelöli a lekérdezett `BlobStorage` fájlt.

- Az oszlophivatkozások a következőként vannak `_N` megadva: , ahol az első oszlop a `_1` . Ha a forrásfájl fejlécsort tartalmaz, akkor a fejléc sorában megadott név alapján hivatkozhat az oszlopokra. 

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

Az aszinkron metódus elküldi a lekérdezést a lekérdezésgyorsítási API-nak, majd streameli az eredményeket az alkalmazásnak `BlobQuickQueryClient.QueryAsync` [Stream-objektumként.](/dotnet/api/system.io.stream)

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

A metódus elküldi a lekérdezést a lekérdezésgyorsítási API-nak, majd az eredményeket egy objektumként továbbítja az alkalmazásnak, amely úgy olvasható, mint bármely `BlobQuickQueryClient.openInputStream()` `InputStream` más InputStream-objektum.

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

Ez a példa elküldi a lekérdezést a lekérdezésgyorsítási API-nak, majd streameli az eredményeket. A `blob` segítő függvénynek átadott `queryHemingway` objektum típusa [BlockBlobClient.](/javascript/api/@azure/storage-blob/blockblobclient) A [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) objektumok lekért használatával kapcsolatos további információkért lásd: Rövid útmutató: Blobok kezelése a [JavaScript v12 SDK-val ](storage-quickstart-blobs-nodejs.md)a Node.js.

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

## <a name="retrieve-specific-columns"></a>Adott oszlopok lekérése

Az eredményeket az oszlopok egy részkészletének hatóköreként is megszűrheti. Így csak az adott számítás végrehajtásához szükséges oszlopokat kell lekérni. Ez javítja az alkalmazások teljesítményét és csökkenti a költségeket, mivel kevesebb adatot továbbít a rendszer a hálózaton. 

Ez a kód csak az adatkészlet összes könyvének oszlopát `BibNum` olvassa be. A forrásfájl fejlécsorának információit is felhasználja a lekérdezés oszlopaira való hivatkozáshoz.

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

Az alábbi kód a sorszűrés és az oszloplevetítések egy lekérdezésben való kombinálása. 

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

- [Azure Data Lake Storage gyorsítása](data-lake-storage-query-acceleration.md)
- [Lekérdezésgyorsítás SQL nyelvi referenciája](query-acceleration-sql-reference.md)
