---
title: Azure Data Lake Analytics kezelése az Azure .NET SDK-val
description: Ez a cikk azt ismerteti, hogyan használható az Azure .NET SDK Data Lake Analytics feladatokat, adatforrásokat és & felhasználókat kezelő alkalmazások írásához.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 06/18/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: a9ece3fe54ca8596f610439f8a0dd228ae1ef907
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220907"
---
# <a name="manage-azure-data-lake-analytics-a-net-app"></a>Az Azure Data Lake Analytics kezelése – .NET-alkalmazás

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ez a cikk bemutatja, hogyan kezelheti Azure Data Lake Analytics fiókokat, adatforrásokat, felhasználókat és feladatokat az Azure .NET SDK használatával írt alkalmazásokkal. 

## <a name="prerequisites"></a>Előfeltételek

* **Visual Studio 2015, Visual Studio 2013 4. frissítéssel vagy Visual Studio 2012 és telepített Visual C++**.
* **Microsoft Azure SDK for .NET 2.5-ös vagy újabb verzió**.  Telepítse a [Webplatform-telepítővel](https://www.microsoft.com/web/downloads/platform.aspx).
* **Szükséges NuGet-csomagok**

### <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

|Csomag|Verzió|
|-------|-------|
|[Microsoft. Rest. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)| 2.3.1|
|[Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)|3.0.0|
|[Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)|2.2.0|
|[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|1.6.0 – előzetes verzió|
|[Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|3.4.0 – előzetes verzió|

Ezeket a csomagokat a NuGet parancssorból is telepítheti az alábbi parancsokkal:

```powershell
Install-Package -Id Microsoft.Rest.ClientRuntime.Azure.Authentication  -Version 2.3.1
Install-Package -Id Microsoft.Azure.Management.DataLake.Analytics  -Version 3.0.0
Install-Package -Id Microsoft.Azure.Management.DataLake.Store  -Version 2.2.0
Install-Package -Id Microsoft.Azure.Management.ResourceManager  -Version 1.6.0-preview
Install-Package -Id Microsoft.Azure.Graph.RBAC -Version 3.4.0-preview
```

## <a name="common-variables"></a>Gyakori változók

```csharp
string subid = "<Subscription ID>"; // Subscription ID (a GUID)
string tenantid = "<Tenant ID>"; // AAD tenant ID or domain. For example, "contoso.onmicrosoft.com"
string rg == "<value>"; // Resource  group name
string clientid = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID (this will work, but you should pick your own)
```

## <a name="authentication"></a>Hitelesítés

Több lehetőség is van a Azure Data Lake Analyticsba való bejelentkezésre. Az alábbi kódrészlet egy olyan példát mutat be, amely interaktív felhasználói hitelesítéssel rendelkezik előugró ablakos hitelesítéssel.

``` csharp
using System;
using System.IO;
using System.Threading;
using System.Security.Cryptography.X509Certificates;

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Analytics;
using Microsoft.Azure.Management.DataLake.Analytics.Models;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Graph.RBAC;

public static Program
{
   public static string TENANT = "microsoft.onmicrosoft.com";
   public static string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
   public static System.Uri ARM_TOKEN_AUDIENCE = new System.Uri( @"https://management.core.windows.net/");
   public static System.Uri ADL_TOKEN_AUDIENCE = new System.Uri( @"https://datalake.azure.net/" );
   public static System.Uri GRAPH_TOKEN_AUDIENCE = new System.Uri( @"https://graph.windows.net/" );

   static void Main(string[] args)
   {
      string MY_DOCUMENTS= System.Environment.GetFolderPath( System.Environment.SpecialFolder.MyDocuments);
      string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");

      var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
      var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var graphCreds = GetCreds_User_Popup(TENANT, GRAPH_TOKEN_AUDIENCE, CLIENTID, tokenCache);
   }
}
```

A **GetCreds_User_Popup** forráskódját, valamint a hitelesítés egyéb beállításaihoz tartozó kódot a .net- [hitelesítési beállítások Data Lake Analytics](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options)


## <a name="create-the-client-management-objects"></a>Az ügyfél-felügyeleti objektumok létrehozása

``` csharp
var resourceManagementClient = new ResourceManagementClient(armCreds) { SubscriptionId = subid };

var adlaAccountClient = new DataLakeAnalyticsAccountManagementClient(armCreds);
adlaAccountClient.SubscriptionId = subid;

var adlsAccountClient = new DataLakeStoreAccountManagementClient(armCreds);
adlsAccountClient.SubscriptionId = subid;

var adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(adlCreds);
var adlaJobClient = new DataLakeAnalyticsJobManagementClient(adlCreds);

var adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

var  graphClient = new GraphRbacManagementClient(graphCreds);
graphClient.TenantID = domain;

```

## <a name="manage-accounts"></a>Fiókok kezelése

### <a name="create-an-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

Ha még nem hozott létre egyet, rendelkeznie kell egy Azure-erőforráscsoporthoz a Data Lake Analytics-összetevők létrehozásához. Szüksége lesz a hitelesítő adataira, az előfizetés AZONOSÍTÓJÁRA és egy helyre. Az alábbi kód bemutatja, hogyan hozhat létre erőforráscsoportot:

``` csharp
var resourceGroup = new ResourceGroup { Location = location };
resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rg);
```

További információ: Azure-erőforráscsoportok és Data Lake Analytics.

### <a name="create-a-data-lake-store-account"></a>Data Lake Store-fiók létrehozása

Az Ever ADLA-fiókhoz ADLS-fiók szükséges. Ha még nem rendelkezik ilyennel, a következő kóddal hozhat létre egyet:

``` csharp
var new_adls_params = new DataLakeStoreAccount(location: _location);
adlsAccountClient.Account.Create(rg, adls, new_adls_params);
```

### <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

A következő kód létrehoz egy ADLS-fiókot

``` csharp
var new_adla_params = new DataLakeAnalyticsAccount()
{
   DefaultDataLakeStoreAccount = adls,
   Location = location
};

adlaClient.Account.Create(rg, adla, new_adla_params);
```

### <a name="list-data-lake-store-accounts"></a>Data Lake Store fiókok listázása

``` csharp
var adlsAccounts = adlsAccountClient.Account.List().ToList();
foreach (var adls in adlsAccounts)
{
   Console.WriteLine($"ADLS: {0}", adls.Name);
}
```

### <a name="list-data-lake-analytics-accounts"></a>Data Lake Analytics fiókok listázása

``` csharp
var adlaAccounts = adlaClient.Account.List().ToList();

for (var adla in AdlaAccounts)
{
   Console.WriteLine($"ADLA: {0}, adla.Name");
}
```

### <a name="checking-if-an-account-exists"></a>Annak ellenőrzése, hogy létezik-e fiók

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
```

### <a name="get-information-about-an-account"></a>Fiók adatainak beolvasása

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
if (exists)
{
   var adla_accnt = adlaClient.Account.Get(rg, adla);
}
```

### <a name="delete-an-account"></a>Fiók eltávolítása

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
   adlaClient.Account.Delete(rg, adla);
}
```

### <a name="get-the-default-data-lake-store-account"></a>Az alapértelmezett Data Lake Store fiók beolvasása

Minden Data Lake Analytics fiókhoz alapértelmezett Data Lake Store fiók szükséges. Ezzel a kóddal határozható meg az Analytics-fiók alapértelmezett tárolási fiókja.

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
  var adla_accnt = adlaClient.Account.Get(rg, adla);
  string def_adls_account = adla_accnt.DefaultDataLakeStoreAccount;
}
```

## <a name="manage-data-sources"></a>Adatforrások kezelése

A Data Lake Analytics jelenleg a következő adatforrásokat támogatja:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage-fiók](../storage/common/storage-introduction.md)

### <a name="link-to-an-azure-storage-account"></a>Hivatkozás Azure Storage-fiókra

Az Azure Storage-fiókokra mutató hivatkozásokat is létrehozhat.

``` csharp
string storage_key = "xxxxxxxxxxxxxxxxxxxx";
string storage_account = "mystorageaccount";
var addParams = new AddStorageAccountParameters(storage_key);            
adlaClient.StorageAccounts.Add(rg, adla, storage_account, addParams);
```

### <a name="list-azure-storage-data-sources"></a>Az Azure Storage-adatforrások listázása

``` csharp
var stg_accounts = adlaAccountClient.StorageAccounts.ListByAccount(rg, adla);

if (stg_accounts != null)
{
  foreach (var stg_account in stg_accounts)
  {
      Console.WriteLine($"Storage account: {0}", stg_account.Name);
  }
}
```

### <a name="list-data-lake-store-data-sources"></a>Adatforrások listázása Data Lake Store

``` csharp
var adls_accounts = adlsClient.Account.List();

if (adls_accounts != null)
{
  foreach (var adls_accnt in adls_accounts)
  {
      Console.WriteLine($"ADLS account: {0}", adls_accnt.Name);
  }
}
```

### <a name="upload-and-download-folders-and-files"></a>Mappák és fájlok feltöltése és letöltése

Az alábbi módszerekkel tölthet fel és tölthet le különálló fájlokat vagy mappákat az Azure-ból a helyi számítógépére a Data Lake Store fájlrendszerbeli ügyfél-felügyeleti objektum használatával:

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

A metódusok első paramétere a Data Lake Store fiók neve, amelyet a forrás elérési útja és a cél elérési útja paraméterek követnek.

Az alábbi példa bemutatja, hogyan tölthető le egy mappa a Data Lake Store.

``` csharp
adlsFileSystemClient.FileSystem.DownloadFolder(adls, sourcePath, destinationPath);
```

### <a name="create-a-file-in-a-data-lake-store-account"></a>Fájl létrehozása Data Lake Store fiókban

``` csharp
using (var memstream = new MemoryStream())
{
   using (var sw = new StreamWriter(memstream, UTF8Encoding.UTF8))
   {
      sw.WriteLine("Hello World");
      sw.Flush();
      
      memstream.Position = 0;

      adlsFileSystemClient.FileSystem.Create(adls, "/Samples/Output/randombytes.csv", memstream);
   }
}
```

### <a name="verify-azure-storage-account-paths"></a>Azure Storage-fiók elérési útjának ellenőrzése

A következő kód ellenőrzi, hogy létezik-e egy Azure Storage-fiók (storageAccntName) egy Data Lake Analytics-fiókban (analyticsAccountName), és hogy van-e tároló (containerName) az Azure Storage-fiókban.

``` csharp
string storage_account = "mystorageaccount";
string storage_container = "mycontainer";
bool accountExists = adlaClient.Account.StorageAccountExists(rg, adla, storage_account));
bool containerExists = adlaClient.Account.StorageContainerExists(rg, adla, storage_account, storage_container));
```

## <a name="manage-catalog-and-jobs"></a>A katalógus és a feladatok kezelése

A DataLakeAnalyticsCatalogManagementClient objektum az egyes Azure Data Lake Analytics fiókokhoz megadott SQL Database-adatbázis kezeléséhez biztosít metódusokat. A DataLakeAnalyticsJobManagementClient metódusokat biztosít a feladatok küldéséhez és kezeléséhez az adatbázisban az U-SQL-parancsfájlok használatával.

### <a name="list-databases-and-schemas"></a>Adatbázisok és sémák listázása

Többek között a leggyakrabban használt adatbázisok és sémáik közül választhat. A következő kód adatbázis-gyűjteményt szerez be, majd az egyes adatbázisok sémáit enumerálja.

``` csharp
var databases = adlaCatalogClient.Catalog.ListDatabases(adla);
foreach (var db in databases)
{
  Console.WriteLine($"Database: {db.Name}");
  Console.WriteLine(" - Schemas:");
  var schemas = adlaCatalogClient.Catalog.ListSchemas(adla, db.Name);
  foreach (var schm in schemas)
  {
      Console.WriteLine($"\t{schm.Name}");
  }
}
```

### <a name="list-table-columns"></a>Táblázat oszlopainak listázása

A következő kód bemutatja, hogyan érheti el az adatbázist egy Data Lake Analytics Catalog felügyeleti ügyféllel egy adott tábla oszlopainak listázásához.

```csharp
var tbl = adlaCatalogClient.Catalog.GetTable(adla, "master", "dbo", "MyTableName");
IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

foreach (USqlTableColumn utc in columns)
{
  Console.WriteLine($"\t{utc.Name}");
}
```

### <a name="submit-a-u-sql-job"></a>U-SQL-feladat elküldése

A következő kód bemutatja, hogyan küldhet el egy Data Lake Analytics Feladatkezelő-ügyfelet a feladatok elküldéséhez.

``` csharp
string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
string scriptTxt = string.Empty;
using (StreamReader sr = new StreamReader(scriptStrm))
{
    scriptTxt = sr.ReadToEnd();
}

var jobName = "SR_Wikipedia";
var jobId = Guid.NewGuid();
var properties = new USqlJobProperties(scriptTxt);
var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
var jobInfo = adlaJobClient.Job.Create(adla, jobId, parameters);
Console.WriteLine($"Job {jobName} submitted.");
```

### <a name="list-failed-jobs"></a>Sikertelen feladatok listázása

A következő kód felsorolja a sikertelen feladatokkal kapcsolatos információkat.

```csharp
var odq = new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" };
var jobs = adlaJobClient.Job.List(adla, odq);
foreach (var j in jobs)
{
   Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
}
```

### <a name="list-pipelines"></a>Folyamatok listázása

A következő kód felsorolja a fiókba küldött feladatok egyes folyamatait.

``` csharp
var pipelines = adlaJobClient.Pipeline.List(adla);
foreach (var p in pipelines)
{
   Console.WriteLine($"Pipeline: {p.Name}\t{p.PipelineId}\t{p.LastSubmitTime}");
}
```

### <a name="list-recurrences"></a>Ismétlődések listázása

A következő kód felsorolja a fiókba küldött feladatok minden egyes ismétlődésével kapcsolatos információkat.

``` csharp
var recurrences = adlaJobClient.Recurrence.List(adla);
foreach (var r in recurrences)
{
   Console.WriteLine($"Recurrence: {r.Name}\t{r.RecurrenceId}\t{r.LastSubmitTime}");
}
```

## <a name="common-graph-scenarios"></a>Gyakori gráf-forgatókönyvek

### <a name="look-up-user-in-the-aad-directory"></a>Felhasználó megkeresése a HRE könyvtárban

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
```

### <a name="get-the-objectid-of-a-user-in-the-aad-directory"></a>Felhasználó ObjectId beolvasása a HRE könyvtárban

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
Console.WriteLine( userinfo.ObjectId )
```

## <a name="manage-compute-policies"></a>Számítási házirendek kezelése

A DataLakeAnalyticsAccountManagementClient objektum olyan metódusokat biztosít, amelyekkel felügyelhető a Data Lake Analytics fiók számítási házirendjeinek kezelése.

### <a name="list-compute-policies"></a>Számítási szabályzatok listázása

A következő kód lekéri egy Data Lake Analytics fiók számítási házirendjeinek listáját.

``` csharp
var policies = adlaAccountClient.ComputePolicies.ListByAccount(rg, adla);
foreach (var p in policies)
{
   Console.WriteLine($"Name: {p.Name}\tType: {p.ObjectType}\tMax AUs / job: {p.MaxDegreeOfParallelismPerJob}\tMin priority / job: {p.MinPriorityPerJob}");
}
```

### <a name="create-a-new-compute-policy"></a>Új számítási szabályzat létrehozása

A következő kód egy új számítási szabályzatot hoz létre egy Data Lake Analytics-fiókhoz, amely a megadott felhasználó számára elérhető maximális értéket adja meg 50-re, a feladat minimális prioritását pedig 250-re állítja.

``` csharp
var userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde";
var newPolicyParams = new ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250);
adlaAccountClient.ComputePolicies.CreateOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams);
```

## <a name="next-steps"></a>Következő lépések

* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [Azure Data Lake Analytics kezelése Azure Portal használatával](data-lake-analytics-manage-use-portal.md)
* [Az Azure Data Lake Analytics-feladatok figyelése és hibaelhárítása az Azure Portal használatával](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)