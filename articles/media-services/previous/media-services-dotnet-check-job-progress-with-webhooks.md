---
title: Azure webhookok használata Media Services-feladatok értesítéseinek figyeléséhez .NET-tel | Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure webhookokat Media Services feladatok értesítéseinek figyelésére. A kód mintája C# nyelven íródott, és a .NET-hez készült Media Services SDK-t használja.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a61fe157-81b1-45c1-89f2-224b7ef55869
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5ae1bdfeeffdb904ba80ed42919e4f51411871c3
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016151"
---
# <a name="use-azure-webhooks-to-monitor-media-services-job-notifications-with-net"></a>Az Azure webhookok használata Media Services feladatok értesítéseinek figyeléséhez a .NET-tel

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

A feladatok futtatásakor gyakran szükség van a feladat előrehaladásának nyomon követésére. Az Azure webhookok vagy az [Azure üzenetsor-tároló](media-services-dotnet-check-job-progress-with-queues.md)használatával figyelheti Media Services feladatok értesítéseit. Ez a cikk bemutatja, hogyan dolgozhat webhookokkal.

Ez a cikk bemutatja, hogyan

*  Definiáljon egy olyan Azure-függvényt, amely testreszabható a webhookokra való válaszadásra. 
    
    Ebben az esetben a webhookot a Media Services indítja el, amikor a kódolási feladatok állapota megváltozik. A függvény figyeli a webhook hívását Media Services értesítésekről, és a feladat befejeződése után közzéteszi a kimeneti objektumot. 
    
    >[!TIP]
    >A folytatás előtt győződjön meg arról, hogy [Azure FUNCTIONS http-és webhook-kötések](../../azure-functions/functions-bindings-http-webhook.md) működnek.
    >
    
* Vegyen fel egy webhookot a kódolási feladatba, és adja meg a webhook URL-címét és a titkos kulcsot, amelyet a webhook válaszol. Megtalál egy példát, amely a cikk végén felvesz egy webhookot a kódolási feladatba.  

A különböző Media Services .NET-Azure Functions definícióit [itt](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)tekintheti meg (beleértve a cikkben láthatót is).

## <a name="prerequisites"></a>Előfeltételek

Az ismertetett eljárás végrehajtásához a következők szükségesek:

* Egy Azure-fiók. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* Egy Media Services-fiók. A Media Services-fiók létrehozásáról a [Media Services-fiók létrehozása](media-services-portal-create-account.md) című cikk nyújt tájékoztatást.
* A [Azure functions használatának](../../azure-functions/functions-overview.md)ismertetése. Tekintse át a [Azure FUNCTIONS http-és webhook-kötéseket](../../azure-functions/functions-bindings-http-webhook.md)is.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be az Azure-fiókjával.
2. Hozzon létre egy Function alkalmazást az [itt](../../azure-functions/functions-create-function-app-portal.md)leírtak szerint.

## <a name="configure-function-app-settings"></a>Function alkalmazás beállításainak konfigurálása

Media Services függvények fejlesztésekor hasznos lehet olyan környezeti változók hozzáadása, amelyeket a rendszer a függvények során fog használni. Az Alkalmazásbeállítások konfigurálásához kattintson a configure app Settings (Alkalmazásbeállítások konfigurálása) hivatkozásra. 

Az [Alkalmazásbeállítások](media-services-dotnet-how-to-use-azure-functions.md#configure-function-app-settings) szakasz azokat a paramétereket határozza meg, amelyek a jelen cikkben definiált webhookban használatosak. Adja hozzá a következő paramétereket az alkalmazás beállításaihoz is. 

|Name|Definíció|Példa| 
|---|---|---|
|SigningKey |Egy aláíró kulcs.| j0txf1f8msjytzvpe40nxbpxdcxtqcgxy0nt|
|WebHookEndpoint | Egy webhook végpontjának címe. Ha létrehozta a webhook-függvényt, az URL-címet a **Get függvény URL-címe** hivatkozás használatával másolhatja. | https: \/ /juliakofuncapp.azurewebsites.NET/API/Notification_Webhook_Function?Code=iN2phdrTnCxmvaKExFWOTulfnm4C71mMLIy8tzLr7Zvf6Z22HHIK5g = =.|

## <a name="create-a-function"></a>Függvény létrehozása

Miután telepítette a Function alkalmazást, a **App Services** Azure functions között találhatja meg.

1. Válassza ki a Function alkalmazást, és kattintson az **új függvény** elemre.
2. Válassza a **C#** kód és **API & webhookok** forgatókönyvet. 
3. Válassza az **általános webhook – C#** elemet.
4. Nevezze el a webhookot, és kattintson a **Létrehozás** gombra.

### <a name="files"></a>Fájlok

Az Azure-függvény az ebben a szakaszban ismertetett kódrészletekhez és egyéb fájlokhoz van társítva. Alapértelmezés szerint a függvény a **function.jsés a** **Run. CSX** (C#) fájlokhoz van társítva. Hozzá kell adnia egy **project.jsa** fájlhoz. A szakasz további részében a fájlok definíciói láthatók.

![fájlok](./media/media-services-azure-functions/media-services-azure-functions003.png)

#### <a name="functionjson"></a>function.json

A fájl function.jsa függvények kötéseit és az egyéb konfigurációs beállításokat határozza meg. A futtatókörnyezet ezt a fájlt használja a figyelni kívánt események meghatározásához és az adatoknak a függvény végrehajtásból való visszaküldéséhez és az adatok visszaadásához. 

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

#### <a name="projectjson"></a>project.jsbekapcsolva

A fájl project.jsfüggőségeket tartalmaz. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}
```
    
#### <a name="runcsx"></a>Futtassa a. CSX

Az ebben a szakaszban található kód egy olyan Azure-függvény implementációját mutatja be, amely egy webhook. Ebben a példában a függvény figyeli a webhook hívását Media Services értesítésekről, és a feladat befejeződése után közzéteszi a kimeneti eszközt.

A webhook egy aláíró kulcsot (hitelesítő adatot) vár az értesítési végpont konfigurálásakor megadott értéknek megfelelően. Az aláírási kulcs a 64 bájtos Base64 kódolású érték, amely az Azure Media Services webhookok visszahívásának védelméhez és biztonságossá tételéhez használható. 

A következő webhook-definíciós kódban a **VerifyWebHookRequestSignature** metódus ellenőrzi az értesítési üzenetet. Ennek az ellenőrzésnek a célja, hogy biztosítsa, hogy az üzenetet Azure Media Services küldte el, és nem módosították illetéktelenül. Az aláírás nem kötelező a Azure Functions esetében, mert a **kód** értéke lekérdezési paraméterként szerepel a TRANSPORT Layer Security (TLS) protokollon keresztül. 

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.

```csharp
///////////////////////////////////////////////////
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using Newtonsoft.Json;
using Microsoft.Azure;
using System.Net;
using System.Security.Cryptography;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

internal const string SignatureHeaderKey = "sha256";
internal const string SignatureHeaderValueTemplate = SignatureHeaderKey + "={0}";
static string _webHookEndpoint = Environment.GetEnvironmentVariable("WebHookEndpoint");
static string _signingKey = Environment.GetEnvironmentVariable("SigningKey");

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");

static readonly string _AMSClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _AMSClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static CloudMediaContext _context = null;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
    byte[] requestBody = await taskForRequestBody;

    string jsonContent = await req.Content.ReadAsStringAsync();
    log.Info($"Request Body = {jsonContent}");

    IEnumerable<string> values = null;
    if (req.Headers.TryGetValues("ms-signature", out values))
    {
        byte[] signingKey = Convert.FromBase64String(_signingKey);
        string signatureFromHeader = values.FirstOrDefault();

        if (VerifyWebHookRequestSignature(requestBody, signatureFromHeader, signingKey))
        {
            string requestMessageContents = Encoding.UTF8.GetString(requestBody);

            NotificationMessage msg = JsonConvert.DeserializeObject<NotificationMessage>(requestMessageContents);

            if (VerifyHeaders(req, msg, log))
            { 
                string newJobStateStr = (string)msg.Properties.Where(j => j.Key == "NewState").FirstOrDefault().Value;
                if (newJobStateStr == "Finished")
                {
                    AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                                AzureEnvironments.AzureCloudEnvironment);

                    AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                    _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                    if(_context!=null)   
                    {                        
                        string urlForClientStreaming = PublishAndBuildStreamingURLs(msg.Properties["JobId"]);
                        log.Info($"URL to the manifest for client streaming using HLS protocol: {urlForClientStreaming}");
                    }
                }

                return req.CreateResponse(HttpStatusCode.OK, string.Empty);
            }
            else
            {
                log.Info($"VerifyHeaders failed.");
                return req.CreateResponse(HttpStatusCode.BadRequest, "VerifyHeaders failed.");
            }
        }
        else
        {
            log.Info($"VerifyWebHookRequestSignature failed.");
            return req.CreateResponse(HttpStatusCode.BadRequest, "VerifyWebHookRequestSignature failed.");
        }
    }

    return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
}

private static string PublishAndBuildStreamingURLs(String jobID)
{
    IJob job = _context.Jobs.Where(j => j.Id == jobID).FirstOrDefault();
    IAsset asset = job.OutputMediaAssets.FirstOrDefault();

    // Create a 30-day readonly access policy. 
    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
    TimeSpan.FromDays(30),
    AccessPermissions.Read);

    // Create a locator to the streaming content on an origin. 
    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
    policy,
    DateTime.UtcNow.AddMinutes(-5));

    // Get a reference to the streaming manifest file from the  
    // collection of files in the asset. 
    var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
                EndsWith(".ism")).
                FirstOrDefault();

    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest" +  "(format=m3u8-aapl)";
    return urlForClientStreaming;

}

private static bool VerifyWebHookRequestSignature(byte[] data, string actualValue, byte[] verificationKey)
{
    using (var hasher = new HMACSHA256(verificationKey))
    {
        byte[] sha256 = hasher.ComputeHash(data);
        string expectedValue = string.Format(CultureInfo.InvariantCulture, SignatureHeaderValueTemplate, ToHex(sha256));

        return (0 == String.Compare(actualValue, expectedValue, System.StringComparison.Ordinal));
    }
}

private static bool VerifyHeaders(HttpRequestMessage req, NotificationMessage msg, TraceWriter log)
{
    bool headersVerified = false;

    try
    {
        IEnumerable<string> values = null;
        if (req.Headers.TryGetValues("ms-mediaservices-accountid", out values))
        {
            string accountIdHeader = values.FirstOrDefault();
            string accountIdFromMessage = msg.Properties["AccountId"];

            if (0 == string.Compare(accountIdHeader, accountIdFromMessage, StringComparison.OrdinalIgnoreCase))
            {
                headersVerified = true;
            }
            else
            {
                log.Info($"accountIdHeader={accountIdHeader} does not match accountIdFromMessage={accountIdFromMessage}");
            }
        }
        else
        {
            log.Info($"Header ms-mediaservices-accountid not found.");
        }
    }
    catch (Exception e)
    {
        log.Info($"VerifyHeaders hit exception {e}");
        headersVerified = false;
    }

    return headersVerified;
}

private static readonly char[] HexLookup = new char[] { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F' };

/// <summary>
/// Converts a <see cref="T:byte[]"/> to a hex-encoded string.
/// </summary>
private static string ToHex(byte[] data)
{
    if (data == null)
    {
        return string.Empty;
    }

    char[] content = new char[data.Length * 2];
    int output = 0;
    byte d;

    for (int input = 0; input < data.Length; input++)
    {
        d = data[input];
        content[output++] = HexLookup[d / 0x10];
        content[output++] = HexLookup[d % 0x10];
    }

    return new string(content);
}

internal enum NotificationEventType
{
    None = 0,
    JobStateChange = 1,
    NotificationEndPointRegistration = 2,
    NotificationEndPointUnregistration = 3,
    TaskStateChange = 4,
    TaskProgress = 5
}

internal sealed class NotificationMessage
{
    public string MessageVersion { get; set; }
    public string ETag { get; set; }
    public NotificationEventType EventType { get; set; }
    public DateTime TimeStamp { get; set; }
    public IDictionary<string, string> Properties { get; set; }
}
```

Mentse és futtassa a függvényt.

### <a name="function-output"></a>Függvény kimenete

A webhook elindítása után a fenti példa a következő kimenetet eredményezi, az értékek eltérőek lesznek.

```output
C# HTTP trigger function processed a request. RequestUri=https://juliako001-functions.azurewebsites.net/api/otification_Webhook_Function?code=9376d69kygoy49oft81nel8frty5cme8hb9xsjslxjhalwhfrqd79awz8ic4ieku74dvkdfgvi
Request Body = 
{
  "MessageVersion": "1.1",
  "ETag": "b8977308f48858a8f224708bc963e1a09ff917ce730316b4e7ae9137f78f3b20",
  "EventType": 4,
  "TimeStamp": "2017-02-16T03:59:53.3041122Z",
  "Properties": {
    "JobId": "nb:jid:UUID:badd996c-8d7c-4ae0-9bc1-bd7f1902dbdd",
    "TaskId": "nb:tid:UUID:80e26fb9-ee04-4739-abd8-2555dc24639f",
    "NewState": "Finished",
    "OldState": "Processing",
    "AccountName": "mediapkeewmg5c3peq",
    "AccountId": "301912b0-659e-47e0-9bc4-6973f2be3424",
    "NotificationEndPointId": "nb:nepid:UUID:cb5d707b-4db8-45fe-a558-19f8d3306093"
  }
}

URL to the manifest for client streaming using HLS protocol: http://mediapkeewmg5c3peq.streaming.mediaservices.windows.net/0ac98077-2b58-4db7-a8da-789a13ac6167/BigBuckBunny.ism/manifest(format=m3u8-aapl)
```

## <a name="add-a-webhook-to-your-encoding-task"></a>Webhook hozzáadása a kódolási feladathoz

Ebben a szakaszban a webhook-értesítés feladathoz való hozzáadását ismertető kód jelenik meg. Felvehet egy feladat szintű értesítést is, amely hasznos lehet a láncolt feladatokkal rendelkező feladatok esetében.  

1. A Visual Studióban hozzon létre egy új Visual C#-konzolalkalmazást. Adja meg a nevét, a helyét és a megoldás nevét, majd kattintson az OK gombra.
2. Azure Media Services telepítéséhez használja a [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices) .
3. App.config fájl frissítése a megfelelő értékekkel: 
    
   * Azure Media Services a kapcsolatok adatait, 
   * webhook URL-címe, amely az értesítések beszerzésére vár 
   * a webhook által várt kulccsal egyező aláíró kulcs. Az aláírási kulcs a 64 bájtos Base64 kódolású érték, amely az Azure Media Services webhookok visszahívásának védelméhez és biztonságossá tételéhez használható. 

     ```xml
           <appSettings>
               <add key="AMSAADTenantDomain" value="domain" />
               <add key="AMSRESTAPIEndpoint" value="endpoint" />

               <add key="AMSClientId" value="clinet id" />
               <add key="AMSClientSecret" value="client secret" />

               <add key="WebhookURL" value="https://yourapp.azurewebsites.net/api/functionname?code=ApiKey" />
               <add key="WebhookSigningKey" value="j0txf1f8msjytzvpe40nxbpxdcxtqcgxy0nt" />
           </appSettings>
     ```

4. Frissítse a Program.cs-fájlt a következő kóddal:

    ```csharp
            using System;
            using System.Configuration;
            using System.Linq;
            using Microsoft.WindowsAzure.MediaServices.Client;

            namespace NotificationWebHook
            {
                class Program
                {
                // Read values from the App.config file.
                private static readonly string _AMSAADTenantDomain =
                    ConfigurationManager.AppSettings["AMSAADTenantDomain"];
                private static readonly string _AMSRESTAPIEndpoint =
                    ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];

                private static readonly string _AMSClientId =
                    ConfigurationManager.AppSettings["AMSClientId"];
                private static readonly string _AMSClientSecret =
                    ConfigurationManager.AppSettings["AMSClientSecret"];

                private static readonly string _webHookEndpoint =
                    ConfigurationManager.AppSettings["WebhookURL"];
                private static readonly string _signingKey =
                    ConfigurationManager.AppSettings["WebhookSigningKey"];

                // Field for service context.
                private static CloudMediaContext _context = null;

                static void Main(string[] args)
                {
                    AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AMSAADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                    AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                    _context = new CloudMediaContext(new Uri(_AMSRESTAPIEndpoint), tokenProvider);

                    byte[] keyBytes = Convert.FromBase64String(_signingKey);

                    IAsset newAsset = _context.Assets.FirstOrDefault();

                    // Check for existing Notification Endpoint with the name "FunctionWebHook"

                    var existingEndpoint = _context.NotificationEndPoints.Where(e => e.Name == "FunctionWebHook").FirstOrDefault();
                    INotificationEndPoint endpoint = null;

                    if (existingEndpoint != null)
                    {
                    Console.WriteLine("webhook endpoint already exists");
                    endpoint = (INotificationEndPoint)existingEndpoint;
                    }
                    else
                    {
                    endpoint = _context.NotificationEndPoints.Create("FunctionWebHook",
                        NotificationEndPointType.WebHook, _webHookEndpoint, keyBytes);
                    Console.WriteLine("Notification Endpoint Created with Key : {0}", keyBytes.ToString());
                    }

                    // Declare a new encoding job with the Standard encoder
                    IJob job = _context.Jobs.Create("MES Job");

                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    ITask task = job.Tasks.AddNew("My encoding task",
                    processor,
                    "Adaptive Streaming",
                    TaskOptions.None);

                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(newAsset);

                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew(newAsset.Name, AssetCreationOptions.None);

                    // Add the WebHook notification to this Task and request all notification state changes.
                    // Note that you can also add a job level notification
                    // which would be more useful for a job with chained tasks.  
                    if (endpoint != null)
                    {
                    task.TaskNotificationSubscriptions.AddNew(NotificationJobState.All, endpoint, true);
                    Console.WriteLine("Created Notification Subscription for endpoint: {0}", _webHookEndpoint);
                    }
                    else
                    {
                    Console.WriteLine("No Notification Endpoint is being used");
                    }

                    job.Submit();

                    Console.WriteLine("Expect WebHook to be triggered for the Job ID: {0}", job.Id);
                    Console.WriteLine("Expect WebHook to be triggered for the Task ID: {0}", task.Id);

                    Console.WriteLine("Job Submitted");

                }
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                    if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                    return processor;
                }
                }
            }
    ```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
