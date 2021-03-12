---
title: Felügyeleti .NET SDK a Azure Stream Analyticshoz
description: Ismerkedés a Stream Analytics Management .NET SDK-val. Ismerje meg, hogyan állíthatja be és futtathatja az elemzési feladatokat. Hozzon létre egy projektet, bemeneteket, kimeneteket és átalakításokat.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 3/12/2021
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9adc4c92e3e637b9d3e18249b5de00782a94baab
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232885"
---
# <a name="management-net-sdk-set-up-and-run-analytics-jobs-using-the-azure-stream-analytics-api-for-net"></a>Felügyelet .NET SDK: elemzési feladatok beállítása és futtatása a .NET-hez készült Azure Stream Analytics API-val
Ismerje meg, hogyan állíthatja be és futtathatja az elemzési feladatokat a .NET Stream Analytics API-val a felügyeleti .NET SDK használatával. Projekt beállítása, bemeneti és kimeneti források, átalakítások, valamint indítási és leállítási feladatok létrehozása. Az elemzési feladatokhoz blob Storage-ból vagy Event hub-ból továbbíthatja az adatait.

Tekintse [meg a .net-hez készült stream Analytics API kezelési útmutatójának dokumentációját](/previous-versions/azure/dn889315(v=azure.100)).

A Azure Stream Analytics egy teljes körűen felügyelt szolgáltatás, amely kis késleltetésű, magasan elérhető, méretezhető, összetett eseményt biztosít a felhőben tárolt adatfolyam-adatfeldolgozáshoz. Stream Analytics lehetővé teszi az ügyfeleknek, hogy az adatfolyamok elemzéséhez beállítsák a folyamatos átviteli feladatokat, és lehetővé teszik a közel valós idejű elemzések elvégzését.  

> [!NOTE]
> Ebben a cikkben a Azure Stream Analytics Management .NET SDK v2. x verziójával frissítettük a mintakód-kódot. A lagecy (1. x) SDK-verziót használó mintakód esetében lásd: [a felügyeleti .net SDK v1. x használata a stream Analytics]().

## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdése előtt a következő követelményeknek kell megfelelnie:

* Telepítse a Visual Studio 2019 vagy a 2015 programot.
* Töltse le és telepítse az [Azure .net SDK](https://azure.microsoft.com/downloads/)-t.
* Hozzon létre egy Azure-erőforráscsoportot az előfizetésében. A következő példa egy minta Azure PowerShell szkript. Azure PowerShell információ: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/);  

   ```powershell
   # Log in to your Azure account
   Add-AzureAccount
   
   # Select the Azure subscription you want to use to create the resource group
   Select-AzureSubscription -SubscriptionName <subscription name>
   
   # If Stream Analytics has not been registered to the subscription, remove the remark    symbol (#) to run the Register-AzProvider cmdlet to register the provider namespace
   #Register-AzProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
   
   # Create an Azure resource group
   New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
   ```

* Állítson be egy bemeneti forrást és egy kimeneti célt ahhoz, hogy a feladathoz kapcsolódjon.

## <a name="set-up-a-project"></a>Projekt beállítása
Elemzési feladatok létrehozásához használja a .NET-hez készült Stream Analytics API-t, majd állítsa be a projektet.

1. Hozzon létre egy Visual Studio C# .NET-konzol alkalmazást.
2. A Package Manager konzolon futtassa a következő parancsokat a NuGet-csomagok telepítéséhez. Az első a Azure Stream Analytics Management .NET SDK. A második az Azure ügyfél-hitelesítés.

   ```powershell   
   Install-Package Microsoft.Azure.Management.StreamAnalytics -Version 2.0.0
   Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.3.1
   ```

3. Adja hozzá a következő **appSettings** szakaszt a App.config fájlhoz:
   
   ```powershell
   <appSettings>
       <add key="ClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
       <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
       <add key="SubscriptionId" value="YOUR SUBSCRIPTION ID" />
       <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```

    Cserélje le az **SubscriptionId** és a **ActiveDirectoryTenantId** értékeit az Azure-előfizetéssel és a bérlői azonosítókkal. Ezeket az értékeket a következő Azure PowerShell parancsmag futtatásával érheti el:

   ```powershell
      Get-AzureAccount
   ```

4. Adja hozzá a következő hivatkozást a. csproj fájlban:

   ```csharp
   <Reference Include="System.Configuration" />
   ```

5. Adja hozzá a következő **using** utasításokat a forrásfájlban (program.cs) a projektben:
   
   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Configuration;
   using System.Threading;
   using System.Threading.Tasks;
   
   using Microsoft.Azure.Management.StreamAnalytics;
   using Microsoft.Azure.Management.StreamAnalytics.Models;
   using Microsoft.Rest.Azure.Authentication;
   using Microsoft.Rest;
   ```

6. Hitelesítési segítő módszer hozzáadása:

   ```csharp
   private static async Task<ServiceClientCredentials> GetCredentials()
   {
       var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(ConfigurationManager.AppSettings["ClientId"], new Uri("urn:ietf:wg:oauth:2.0:oob"));
       ServiceClientCredentials credentials = await UserTokenProvider.LoginWithPromptAsync(ConfigurationManager.AppSettings["ActiveDirectoryTenantId"], activeDirectoryClientSettings);
       
       return credentials;
    }
   ```

## <a name="create-a-stream-analytics-management-client"></a>Stream Analytics Management-ügyfél létrehozása
A **StreamAnalyticsManagementClient** -objektumok lehetővé teszik a feladatok és a feladatok összetevőinek, például a bevitel, a kimenet és az átalakítás kezelését.

Adja hozzá a következő kódot a **Main** metódus elejéhez:

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamingJobName = "<YOUR STREAMING JOB NAME>";
    string inputName = "<YOUR JOB INPUT NAME>";
    string transformationName = "<YOUR JOB TRANSFORMATION NAME>";
    string outputName = "<YOUR JOB OUTPUT NAME>";
    
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    
    // Get credentials
    ServiceClientCredentials credentials = GetCredentials().Result;
    
    // Create Stream Analytics management client
    StreamAnalyticsManagementClient streamAnalyticsManagementClient = new StreamAnalyticsManagementClient(credentials)
    {
        SubscriptionId = ConfigurationManager.AppSettings["SubscriptionId"]
    };
   ```

A **resourceGroupName** változó értékének meg kell egyeznie az előfeltételként létrehozott vagy a kiválasztott erőforráscsoport nevével.

A feladatok létrehozásához szükséges hitelesítő adatok megjelenítésének automatizálásához tekintse meg az [egyszerű szolgáltatás hitelesítése Azure Resource Managerkal](../active-directory/develop/howto-authenticate-service-principal-powershell.md)című témakört.

A cikk hátralévő fejezetei azt feltételezik, hogy ez a kód a **Main** metódus elején található.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása
A következő kód létrehoz egy Stream Analytics feladatot a definiált erőforráscsoport alatt. Később egy bemeneti, kimeneti és átalakítási feladatot fog hozzáadni a feladathoz.

   ```csharp
   // Create a streaming job
   StreamingJob streamingJob = new StreamingJob()
   {
       Tags = new Dictionary<string, string>()
       {
           { "Origin", ".NET SDK" },
           { "ReasonCreated", "Getting started tutorial" }
       },
       Location = "West US",
       EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Drop,
       EventsOutOfOrderMaxDelayInSeconds = 5,
       EventsLateArrivalMaxDelayInSeconds = 16,
       OutputErrorPolicy = OutputErrorPolicy.Drop,
       DataLocale = "en-US",
       CompatibilityLevel = CompatibilityLevel.OneFullStopZero,
       Sku = new Sku()
       {
           Name = SkuName.Standard
       }
   };
   StreamingJob createStreamingJobResult = streamAnalyticsManagementClient.StreamingJobs.CreateOrReplace(streamingJob, resourceGroupName, streamingJobName);
   ```

## <a name="create-a-stream-analytics-input-source"></a>Stream Analytics bemeneti forrás létrehozása
A következő kód egy Stream Analytics bemeneti forrást hoz létre a blob bemeneti forrás típusával és a CSV-szerializálással. Az Event hub bemeneti forrásának létrehozásához használja a **EventHubStreamInputDataSource** -t a **BlobStreamInputDataSource** helyett. Hasonlóképpen testreszabhatja a bemeneti forrás szerializálási típusát is.

   ```csharp
   // Create an input
   StorageAccount storageAccount = new StorageAccount()
   {
       AccountName = "<YOUR STORAGE ACCOUNT NAME>",
       AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
   };
   Input input = new Input()
   {
       Properties = new StreamInputProperties()
       {
           Serialization = new CsvSerialization()
           {
               FieldDelimiter = ",",
               Encoding = Encoding.UTF8
           },
           Datasource = new BlobStreamInputDataSource()
           {
               StorageAccounts = new[] { storageAccount },
               Container = "<YOUR STORAGE BLOB CONTAINER>",
               PathPattern = "{date}/{time}",
               DateFormat = "yyyy/MM/dd",
               TimeFormat = "HH",
               SourcePartitionCount = 16
           }
       }
   };
   Input createInputResult = streamAnalyticsManagementClient.Inputs.CreateOrReplace(input, resourceGroupName, streamingJobName, inputName);
   ```

A bemeneti források, legyenek a blob Storage vagy az Event hub, egy adott feladathoz vannak kötve. Ha ugyanazt a bemeneti forrást szeretné használni a különböző feladatokhoz, újra kell hívnia a metódust, és meg kell adnia egy másik feladatnév nevet.

## <a name="test-a-stream-analytics-input-source"></a>Stream Analytics bemeneti forrás tesztelése
A **TestConnection** metódus ellenőrzi, hogy a stream Analytics-feladathoz tud-e csatlakozni a bemeneti forráshoz, valamint a bemeneti forrás típusára vonatkozó egyéb szempontokat. Egy korábbi lépésben létrehozott blob bemeneti forrásban például a metódus azt fogja ellenőriznie, hogy a Storage-fiók neve és a kulcspár használható-e a Storage-fiókhoz való kapcsolódáshoz, valamint annak ellenőrzéséhez, hogy a megadott tároló létezik-e.

   ```csharp
   // Test the connection to the input
   ResourceTestStatus testInputResult = streamAnalyticsManagementClient.Inputs.Test(resourceGroupName, streamingJobName, inputName);
   ```
A TestConnection hívás eredménye egy *ResourceTestResult* objektum, amely két tulajdonságot tartalmaz:

- *állapot*: a következő karakterláncok egyike lehet: ["TestNotAttempted", "TestSucceeded", "TestFailed"]
- *hiba*: a következő tulajdonságokat tartalmazó ErrorResponse típusú:
   - *Code*: String típusú kötelező tulajdonság. Az érték a standard System .net. HttpStatusCode, amely a tesztelés során érkezett.
   - *üzenet*: a hibát jelölő karakterlánc típusú kötelező tulajdonság. 

## <a name="create-a-stream-analytics-output-target"></a>Stream Analytics kimeneti cél létrehozása
A kimeneti cél létrehozása hasonló egy Stream Analytics bemeneti forrás létrehozásához. A bemeneti forrásokhoz hasonlóan a kimeneti célok egy adott feladathoz vannak kötve. Ha ugyanazt a kimeneti célt szeretné használni a különböző feladatokhoz, újra kell hívnia a metódust, és meg kell adnia egy másik feladat nevét.

A következő kód létrehoz egy kimeneti célt (Azure SQL Database). Testreszabhatja a kimeneti cél adattípusát és/vagy szerializálási típusát.

   ```csharp
   // Create an output
   Output output = new Output()
   {
       Datasource = new AzureSqlDatabaseOutputDataSource()
       {
           Server = "<YOUR DATABASE SERVER NAME>",
           Database = "<YOUR DATABASE NAME>",
           User = "<YOUR DATABASE LOGIN>",
           Password = "<YOUR DATABASE LOGIN PASSWORD>",
           Table = "<YOUR DATABASE TABLE NAME>"
       }
   };
   Output createOutputResult = streamAnalyticsManagementClient.Outputs.CreateOrReplace(output, resourceGroupName, streamingJobName, outputName);
   ```

## <a name="test-a-stream-analytics-output-target"></a>Stream Analytics kimeneti cél tesztelése
A Stream Analytics kimeneti célpont a kapcsolatok tesztelésére szolgáló **TestConnection** metódussal is rendelkezik.

   ```csharp
   // Test the connection to the output
   ResourceTestStatus testOutputResult = streamAnalyticsManagementClient.Outputs.Test(resourceGroupName, streamingJobName, outputName);
   ```

## <a name="create-a-stream-analytics-transformation"></a>Stream Analytics átalakítás létrehozása
A következő kód egy Stream Analytics átalakítást hoz létre a "Select * in input" lekérdezéssel, és megadja, hogy egy folyamatos átviteli egységet foglaljon le a Stream Analytics feladathoz. A folyamatos átviteli egységek beállításával kapcsolatos további információkért lásd: [Azure stream Analytics feladatok skálázása](stream-analytics-scale-jobs.md).

   ```csharp
   // Create a transformation
   Transformation transformation = new Transformation()
   {
       Query = "Select Id, Name from <your input name>", // '<your input name>' should be replaced with the value you put for the 'inputName' variable above or in a previous step
       StreamingUnits = 1
   };
   Transformation createTransformationResult = streamAnalyticsManagementClient.Transformations.CreateOrReplace(transformation, resourceGroupName, streamingJobName, transformationName);
   ```

A bemenethez és a kimenethez hasonlóan a transzformáció is a-ben létrehozott konkrét Stream Analytics-feladathoz kötődik.

## <a name="start-a-stream-analytics-job"></a>Stream Analytics-feladat elindítása
Miután létrehozta a Stream Analytics feladatot és annak bemenetét, kimenetét és átalakítását, a **Start** metódus meghívásával indíthatja el a feladatot.

Az alábbi mintakód egy Stream Analytics feladatot indít el egy egyéni kimeneti kezdési idővel, amely a 2012, 12:12:12 UTC értékre van beállítva.

   ```csharp
   // Start a streaming job
   StartStreamingJobParameters startStreamingJobParameters = new StartStreamingJobParameters()
   {
       OutputStartMode = OutputStartMode.CustomTime,
       OutputStartTime = new DateTime(2012, 12, 12, 12, 12, 12, DateTimeKind.Utc)
   };
   streamAnalyticsManagementClient.StreamingJobs.Start(resourceGroupName, streamingJobName, startStreamingJobParameters);
   ```

## <a name="stop-a-stream-analytics-job"></a>Stream Analytics feladatok leállítása
A **leállítási** metódus meghívásával leállíthatja a futó stream Analytics feladatot.

   ```csharp
   // Stop a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Stop(resourceGroupName, streamingJobName);
   ```

## <a name="delete-a-stream-analytics-job"></a>Stream Analytics-feladatok törlése
A **delete** metódus törli a feladatot, valamint az alapul szolgáló alerőforrásokat, beleértve a bemeneti (ka) t, a kimenetet és a feladat átalakítását is.

   ```csharp
   // Delete a streaming job
   streamAnalyticsManagementClient.StreamingJobs.Delete(resourceGroupName, streamingJobName);
   ```

## <a name="get-support"></a>Támogatás kérése
További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Következő lépések
Megtanulta, hogyan hozhat létre és futtathat analitikai feladatokat a .NET SDK használatával. További tudnivalókért olvassa el a következő cikket:

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure stream Analytics Management .net SDK](/previous-versions/azure/dn889315(v=azure.100))-t.
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: https://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: ../storage/blobs/storage-quickstart-blobs-dotnet.md

[azure.event.hubs]: https://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: /previous-versions/azure/dn789972(v=azure.100)

[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.forum]: https://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
