---
title: Azure Functions fejlesztése a Media Services v3-vel
description: Ebből a témakörből megtudhatja, hogyan kezdheti el a Azure Functions fejlesztését Media Services v3-val a Azure Portal használatával.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 389ad34bb856675dfabd761507ed07cc722c032a
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961676"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>Azure Functions fejlesztése a Media Services v3-vel

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk bemutatja, hogyan kezdheti meg a Media Servicest használó Azure Functions létrehozását. A cikkben meghatározott Azure-függvény egy **input** nevű Storage-fiók tárolóját figyeli az új MP4-fájlokhoz. Miután a rendszer eldobott egy fájlt a tárolóba, a blob-trigger végrehajtja a függvényt. Azure Functions áttekintéséhez tekintse meg a **Azure functions** szakaszban található [Áttekintés](../../azure-functions/functions-overview.md) és egyéb témaköröket.

Ha a Azure Media Servicest használó meglévő Azure Functionsokat szeretné felderíteni és üzembe helyezni, tekintse meg [Media Services Azure functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration). Ez a tárház olyan példákat tartalmaz, amelyek Media Services használatával jelenítik meg a tartalom betöltéséhez kapcsolódó munkafolyamatokat közvetlenül a blob Storage-ból, a kódolásból és a tartalomnak a blob Storage-ba való írásához

## <a name="prerequisites"></a>Előfeltételek

- Ahhoz, hogy létrehozhassa az első függvényét, egy aktív Azure-fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure-fiókkal, [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/).
- Ha olyan Azure Functions hoz létre, amely műveleteket hajt végre a Azure Media Services-(AMS-) fiókban, vagy a Media Services által eljuttatott eseményeket figyeli, hozzon létre egy AMS-fiókot az [itt](account-create-how-to.md)leírtak szerint.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com), és jelentkezzen be az Azure-fiókjával.
2. Hozzon létre egy Function alkalmazást az [itt](../../azure-functions/functions-create-function-app-portal.md)leírtak szerint.

>[!NOTE]
> A megadott Storage-fióknak ugyanabban a régióban kell lennie, mint az alkalmazásnak.

## <a name="configure-function-app-settings"></a>Function alkalmazás beállításainak konfigurálása

Media Services függvények fejlesztésekor hasznos lehet olyan környezeti változók hozzáadása, amelyeket a rendszer a függvények során fog használni. Az Alkalmazásbeállítások konfigurálásához kattintson a configure app Settings (Alkalmazásbeállítások konfigurálása) hivatkozásra. További információ:  [Az Azure Function app beállításainak konfigurálása](../../azure-functions/functions-how-to-use-azure-function-app-settings.md).

## <a name="create-a-function"></a>Függvény létrehozása

Miután telepítette a Function alkalmazást, a **App Services** Azure functions között találhatja meg.

1. Válassza ki a Function alkalmazást, és kattintson az **új függvény** elemre.
1. Válassza ki a **C#** nyelvét és az **adatfeldolgozási** forgatókönyvet.
1. Válassza a **BlobTrigger** -sablon lehetőséget. Ez a függvény akkor aktiválódik, amikor egy blob fel van töltve a **bemeneti** tárolóba. A **bemeneti** nevet a rendszer az **elérési úton** adja meg a következő lépésben.
1. Miután kiválasztotta a **BlobTrigger**-t, néhány további vezérlő jelenik meg az oldalon.
1. Kattintson a **Létrehozás** lehetőségre.

## <a name="files"></a>Fájlok

Az Azure-függvény az ebben a szakaszban ismertetett kódrészletekhez és egyéb fájlokhoz van társítva. Ha a Azure Portal használatával hoz létre egy függvényt, **function.jsbe** és **futtatni. CSX** jönnek létre. Fel kell vennie vagy fel kell töltenie egy **project.js** fájlt. A szakasz további része röviden ismerteti az egyes fájlokat, és megjeleníti a definíciókat.

### <a name="functionjson"></a>function.json

A fájl function.jsa függvények kötéseit és az egyéb konfigurációs beállításokat határozza meg. A futtatókörnyezet ezt a fájlt használja a figyelni kívánt események meghatározásához és az adatoknak a függvény végrehajtásból való visszaküldéséhez és az adatok visszaadásához. További információ: [Azure Functions – HTTP- és webhookkötések](../../azure-functions/functions-reference.md#function-code).

>[!NOTE]
>Állítsa a **letiltott** tulajdonságot **true (igaz** ) értékre a függvény végrehajtásának megakadályozása érdekében.

Cserélje le a fájl meglévő function.jstartalmát a következő kódra:

```json
{
  "bindings": [
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "input/{filename}.mp4",
      "connection": "ConnectionString"
    }
  ],
  "disabled": false
}
```

### <a name="projectjson"></a>project.jsbekapcsolva

A fájl project.jsfüggőségeket tartalmaz. Íme egy példa arra a **project.js** fájlra, amely tartalmazza a szükséges .net Azure Media Services csomagokat a NuGet-ből. Vegye figyelembe, hogy a verziószámok a csomagok legújabb frissítéseire változnak, ezért a legújabb verziókat kell megerősíteni.

Adja hozzá a következő definíciót a project.jshoz.

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

### <a name="runcsx"></a>Futtassa a. CSX

Ez a függvény C#-kódja.  Az alábbi függvény egy **input** (az elérési úton megadott) nevű Storage-fiók tárolóját figyeli az új MP4-fájlokhoz. Miután a rendszer eldobott egy fájlt a tárolóba, a blob-trigger végrehajtja a függvényt.

Az ebben a szakaszban meghatározott példa a következőket mutatja be:

1. Eszköz betöltése egy Media Services-fiókba (egy blob egy AMS-eszközbe való összevonásával)
2. Media Encoder Standard "adaptív streaming" beállításkészletet használó kódolási feladatok beküldése

Cserélje le a meglévő Run. CSX fájl tartalmát a következő kódra: Ha elkészült, kattintson a **Mentés és Futtatás** gombra.

```csharp
#r "Microsoft.WindowsAzure.Storage"
#r "Newtonsoft.Json"
#r "System.Web"

using System;
using System.Net;
using System.Net.Http;
using Newtonsoft.Json;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Web;
using Microsoft.Azure;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
  
// Read values from the App.config file.

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");
 
static readonly string _mediaservicesClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _mediaservicesClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static readonly string _connectionString = Environment.GetEnvironmentVariable("ConnectionString");  

private static CloudMediaContext _context = null;
private static CloudStorageAccount _destinationStorageAccount = null;

public static void Run(CloudBlockBlob myBlob, string fileName, TraceWriter log)
{
    // NOTE that the variables {fileName} here come from the path setting in function.json
    // and are passed into the  Run method signature above. We can use this to make decisions on what type of file
    // was dropped into the input container for the function. 

    // No need to do any Retry strategy in this function, By default, the SDK calls a function up to 5 times for a 
    // given blob. If the fifth try fails, the SDK adds a message to a queue named webjobs-blobtrigger-poison.

    log.Info($"C# Blob trigger function processed: {fileName}.mp4");
    log.Info($"Media Services REST endpoint : {_RESTAPIEndpoint}");

    try
    {
        AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                            new AzureAdClientSymmetricKey(_mediaservicesClientId, _mediaservicesClientSecret),
                            AzureEnvironments.AzureCloudEnvironment);
 
        AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);
 
        _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

        IAsset newAsset = CreateAssetFromBlob(myBlob, fileName, log).GetAwaiter().GetResult();

        // Step 2: Create an Encoding Job

        // Declare a new encoding job with the Standard encoder
        IJob job = _context.Jobs.Create("Azure Function - MES Job");

        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Create a task with the encoding details, using a custom preset
        ITask task = job.Tasks.AddNew("Encode with Adaptive Streaming",
            processor,
            "Adaptive Streaming",
            TaskOptions.None); 

        // Specify the input asset to be encoded.
        task.InputAssets.Add(newAsset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is not encrypted. 
        task.OutputAssets.AddNew(fileName, AssetCreationOptions.None);

        job.Submit();
        log.Info("Job Submitted");

    }
    catch (Exception ex)
    {
        log.Error("ERROR: failed.");
        log.Info($"StackTrace : {ex.StackTrace}");
        throw ex;
    }
}

private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
{
    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

    if (processor == null)
    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

    return processor;
}

public static async Task<IAsset> CreateAssetFromBlob(CloudBlockBlob blob, string assetName, TraceWriter log){
    IAsset newAsset = null;

    try{
        Task<IAsset> copyAssetTask = CreateAssetFromBlobAsync(blob, assetName, log);
        newAsset = await copyAssetTask;
        log.Info($"Asset Copied : {newAsset.Id}");
    }
    catch(Exception ex){
        log.Info("Copy Failed");
        log.Info($"ERROR : {ex.Message}");
        throw ex;
    }

    return newAsset;
}

/// <summary>
/// Creates a new asset and copies blobs from the specifed storage account.
/// </summary>
/// <param name="blob">The specified blob.</param>
/// <returns>The new asset.</returns>
public static async Task<IAsset> CreateAssetFromBlobAsync(CloudBlockBlob blob, string assetName, TraceWriter log)
{
     //Get a reference to the storage account that is associated with the Media Services account. 
    _destinationStorageAccount = CloudStorageAccount.Parse(_connectionString);

    // Create a new asset. 
    var asset = _context.Assets.Create(blob.Name, AssetCreationOptions.None);
    log.Info($"Created new asset {asset.Name}");

    IAccessPolicy writePolicy = _context.AccessPolicies.Create("writePolicy",
    TimeSpan.FromHours(4), AccessPermissions.Write);
    ILocator destinationLocator = _context.Locators.CreateLocator(LocatorType.Sas, asset, writePolicy);
    CloudBlobClient destBlobStorage = _destinationStorageAccount.CreateCloudBlobClient();

    // Get the destination asset container reference
    string destinationContainerName = (new Uri(destinationLocator.Path)).Segments[1];
    CloudBlobContainer assetContainer = destBlobStorage.GetContainerReference(destinationContainerName);

    try{
    assetContainer.CreateIfNotExists();
    }
    catch (Exception ex)
    {
    log.Error ("ERROR:" + ex.Message);
    }

    log.Info("Created asset.");

    // Get hold of the destination blob
    CloudBlockBlob destinationBlob = assetContainer.GetBlockBlobReference(blob.Name);

    // Copy Blob
    try
    {
    using (var stream = await blob.OpenReadAsync()) 
    {            
        await destinationBlob.UploadFromStreamAsync(stream);          
    }

    log.Info("Copy Complete.");

    var assetFile = asset.AssetFiles.Create(blob.Name);
    assetFile.ContentFileSize = blob.Properties.Length;
    assetFile.IsPrimary = true;
    assetFile.Update();
    asset.Update();
    }
    catch (Exception ex)
    {
    log.Error(ex.Message);
    log.Info (ex.StackTrace);
    log.Info ("Copy Failed.");
    throw;
    }

    destinationLocator.Delete();
    writePolicy.Delete();

    return asset;
}
```

## <a name="test-your-function"></a>A függvény tesztelése

A függvény teszteléséhez fel kell töltenie egy MP4-fájlt a kapcsolódási karakterláncban megadott Storage-fiók **bemeneti** tárolójába.  

1. Válassza ki a megadott Storage-fiókot.
2. Kattintson a **Blobok** elemre.
3. Kattintson a **+ Tároló** lehetőségre. Nevezze el a tároló **bemenetét**.
4. Kattintson a **feltöltés** gombra, és keresse meg a feltölteni kívánt. MP4-fájlt.

>[!NOTE]
> Ha egy használati tervben blob-triggert használ, az új Blobok feldolgozására akár 10 percet is igénybe vehet, miután egy Function alkalmazás üresjáratba került. A Function app futtatása után a Blobok feldolgozása azonnal megtörténik. További információ: [blob Storage-eseményindítók és-kötések](../../azure-functions/functions-bindings-storage-blob.md).

## <a name="next-steps"></a>Következő lépések

Ezen a ponton készen áll egy Media Services alkalmazás fejlesztésének megkezdésére.

További részletekért és a Azure Functions és Logic Apps az Azure Media Services használatával végzett minták/megoldások létrehozásával kapcsolatos további információkért tekintse meg az [Media Services Azure functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration)
