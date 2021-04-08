---
title: Azure Stream Analytics-feladatok programozott figyelése és kezelése
description: Ez a cikk a REST API-k, az Azure SDK vagy a PowerShell használatával létrehozott Stream Analytics feladatok programozott figyelését ismerteti.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: dda1b6b652870440e7228e6640bb555f4c2e4a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015076"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>Stream Analytics-feladatokhoz tartozó figyelő programozott létrehozása

Ez a cikk bemutatja, hogyan engedélyezhető a Stream Analytics feladatok figyelése. A REST API-kkal, az Azure SDK-val vagy a PowerShell-lel létrehozott feladatokhoz alapértelmezés szerint nincs engedélyezve a figyelés. Stream Analytics Manuálisan engedélyezheti azt a Azure Portal a feladatok figyelése lapra, és az Engedélyezés gombra kattintva vagy automatizálhatja ezt a folyamatot a jelen cikkben ismertetett lépéseket követve. A figyelési adatok a Stream Analytics feladathoz tartozó Azure Portal mérőszámok területén jelennek meg.

## <a name="prerequisites"></a>Előfeltételek

A folyamat megkezdése előtt a következő előfeltételeknek kell megfelelnie:

* Visual Studio 2019 vagy 2015
* Az [Azure .net SDK](https://azure.microsoft.com/downloads/) letöltése és telepítése megtörtént
* Meglévő Stream Analytics-feladatot, amelynek figyelését engedélyezni kell

## <a name="create-a-project"></a>Projekt létrehozása

1. Hozzon létre egy Visual Studio C# .NET-konzol alkalmazást.
2. A Package Manager konzolon futtassa a következő parancsokat a NuGet-csomagok telepítéséhez. Az első a Azure Stream Analytics Management .NET SDK. A második az a Azure Monitor SDK, amelyet a rendszer a figyelés engedélyezéséhez fog használni. Az utolsó az a Azure Active Directory-ügyfél, amelyet a rendszer a hitelesítéshez használ.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. Adja hozzá a következő appSettings szakaszt a App.config fájlhoz.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   Cserélje le az *SubscriptionId* és a *ActiveDirectoryTenantId* értékeit az Azure-előfizetéssel és a bérlői azonosítókkal. Ezeket az értékeket a következő PowerShell-parancsmag futtatásával érheti el:
   
   ```powershell
   Get-AzureAccount
   ```
4. Adja hozzá az alábbi using utasításokat a projekt forrásfájljához (Program.cs).
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. Adjon hozzá egy hitelesítési segítő módszert.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>Felügyeleti ügyfelek létrehozása

A következő kód beállítja a szükséges változókat és a felügyeleti ügyfeleket.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>Meglévő Stream Analytics feladatok figyelésének engedélyezése

A következő kód lehetővé teszi egy **meglévő** stream Analytics feladatok figyelését. A kód első része egy GET kérelmet küld a Stream Analytics szolgáltatásnak az adott Stream Analytics feladattal kapcsolatos információk lekéréséhez. Az *azonosító* tulajdonságot (a Get kérelemből lekért) paraméterként használja a kód második felében a Put metódushoz, amely egy Put-kérelmet küld az ininsights szolgáltatásnak a stream Analytics feladatok figyelésének engedélyezéséhez.

> [!WARNING]
> Ha korábban már engedélyezte a figyelést egy másik Stream Analytics feladatokhoz, akár a Azure Portal, akár programozott módon, az alábbi kód segítségével, akkor azt **javasoljuk, hogy ugyanazt a Storage-fióknevet adja meg, amelyet korábban engedélyezett a figyeléshez.**
> 
> A Storage-fiók ahhoz a régióhoz van csatolva, amelyhez a Stream Analytics-feladatot létrehozta, nem kifejezetten a feladatokhoz.
> 
> Az ugyanabban a régióban található összes Stream Analytics feladat (és az összes többi Azure-erőforrás) megosztja ezt a Storage-fiókot a figyelési adatok tárolásához. Ha más Storage-fiókot ad meg, az nem szándékolt mellékhatást eredményezhet más Stream Analytics feladatok vagy más Azure-erőforrások figyelése során.
> 
> A `<YOUR STORAGE ACCOUNT NAME>` következő kódban használt Storage-fióknév olyan Storage-fióknak kell lennie, amely ugyanabban az előfizetésben található, mint a figyelést engedélyező stream Analytics feladatokkal.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>Támogatás kérése

További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Következő lépések

* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)