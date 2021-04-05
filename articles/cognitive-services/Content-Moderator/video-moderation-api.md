---
title: Videotartalom elemzése a kifogásolt anyagokhoz a C#-Content Moderator
titleSuffix: Azure Cognitive Services
description: Különböző kifogásolt anyagok videotartalom elemzése a .NET-hez készült Content Moderator SDK-val
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: how-to
ms.date: 05/18/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f98eac4305333ec7225c90da2777b7e02f050a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96853532"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Videotartalom elemzése a kifogásolt anyagokhoz C-ben #

Ez a cikk a .NET-hez készült [Content MODERATOR SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) -val való ismerkedéshez nyújt segítséget és példákat a videó tartalmának felnőtt vagy zamatos tartalomra való vizsgálatához.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/). 

## <a name="prerequisites"></a>Előfeltételek
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.

## <a name="set-up-azure-resources"></a>Az Azure-erőforrások beállítása

A Content Moderator videó-moderálási képessége ingyenes nyilvános előzetes verziójú **adathordozó-feldolgozóként** érhető el Azure Media Servicesban (AMS). A Azure Media Services egy speciális Azure-szolgáltatás, amely a videó tartalmának tárolására és továbbítására szolgál. 

### <a name="create-an-azure-media-services-account"></a>Azure Media Services-fiók létrehozása

Az AMS-re való előfizetéshez és a társított Azure Storage-fiók létrehozásához kövesse az [Azure Media Services fiók létrehozása](../../media-services/previous/media-services-portal-create-account.md) című témakör utasításait. Ebben a Storage-fiókban hozzon létre egy új blob Storage-tárolót.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory-alkalmazás létrehozása

Navigáljon az új AMS-előfizetéshez a Azure Portal, és válassza az **API-hozzáférés** lehetőséget az oldal menüjéből. Válassza **a kapcsolódás Azure Media Services az egyszerű szolgáltatással** lehetőséget. Jegyezze fel a **REST API Endpoint (végpont** ) mezőben lévő értéket. erre később szüksége lesz.

Az **Azure ad alkalmazás** szakaszban válassza az **új létrehozása** elemet, és nevezze el az új Azure ad-alkalmazás regisztrációját (például "VideoModADApp"). Kattintson a **Save (Mentés** ) gombra, és várjon néhány percet, amíg az alkalmazás konfigurálva van. Ezután meg kell jelennie az új alkalmazás regisztrálásának az oldal **Azure ad-alkalmazás** szakaszában.

Válassza ki az alkalmazás regisztrációját, és kattintson az alatta lévő **alkalmazás kezelése** gombra. Jegyezze fel az **alkalmazás azonosító** mezőjében szereplő értéket; erre később szüksége lesz. Válassza a **Beállítások**  >  **kulcsok** lehetőséget, és adja meg az új kulcs leírását (például "VideoModKey"). Kattintson a **Mentés** gombra, és figyelje meg az új kulcs értékét. Másolja ezt a karakterláncot, és mentse biztonságos helyre.

A fenti folyamat alaposabb áttekintése: az [Azure ad-hitelesítés első lépései](../../media-services/previous/media-services-portal-get-started-with-aad.md).

Ezt követően két különböző módon használhatja a videó-moderálási adathordozó-processzort.

## <a name="use-azure-media-services-explorer"></a>A Azure Media Services Explorer használata

A Azure Media Services Explorer egy felhasználóbarát előtér az AMS-hez. Használatával böngészheti az AMS-fiókját, feltöltheti a videókat, és ellenőrizheti a tartalmat a Content Moderator adathordozó-feldolgozóval. Töltse le és telepítse a [githubról](https://github.com/Azure/Azure-Media-Services-Explorer/releases), vagy további információkért tekintse meg a [Azure Media Services Explorer blogbejegyzését](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) .

![Azure Media Services Explorer Content Moderator](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. A Visual Studióban hozzon létre egy új **Console app (.NET-keretrendszer)** projektet, és nevezze el **VideoModeration**. 
1. Ha más projektek is vannak a megoldásban, válassza ki ezt a projektet az egyedüli kezdőprojektként.
1. Szerezze be a szükséges NuGet-csomagokat. A Solution Explorer (Megoldáskezelő) felületén kattintson a jobb gombbal a projektre és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget, majd keresse meg és telepítse a következő csomagokat:
    - windowsazure. Mediaservices
    - windowsazure. Mediaservices. Extensions

## <a name="add-video-moderation-code"></a>Videó moderálási kódjának hozzáadása

Következő lépésként másolja és illessze be az ebben az útmutatóban található kódot a projektjébe egy alapszintű tartalommoderálási forgatókönyv megvalósításához.

### <a name="update-the-programs-using-statements"></a>A program „using” utasításainak frissítése

Adja hozzá az alábbi `using` utasításokat a _Program.cs_ fájl elejéhez.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>Erőforrás-hivatkozások beállítása

Adja hozzá a következő statikus mezőket a **program osztályhoz a** _program. cs_. Ezek a mezők az AMS-előfizetéshez való csatlakozáshoz szükséges információkat rendelkeznek. Töltse ki azokat a következő lépésekben kapott értékekkel. Vegye figyelembe `CLIENT_ID` , hogy az Azure ad-alkalmazás **azonosító** értéke, és az `CLIENT_SECRET` adott alkalmazáshoz létrehozott "VideoModKey" értéke.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Ha helyi videofájl (legegyszerűbb eset) használatát kívánja használni, adja hozzá a projekthez, és adja meg az elérési útját `INPUT_FILE` (relatív elérési utak a végrehajtási könyvtárhoz viszonyítva).

Emellett létre kell hoznia a _preset.js_ az aktuális könyvtárban található fájlon, és a verziószámot kell használnia. Például:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>A bemeneti videó (k) betöltése

A **program** osztály **fő** metódusa létrehoz egy Azure Media-környezetet, majd egy Azure Storage-környezetet (ha a videók blob Storage-tárolóban vannak). A fennmaradó kód egy Azure Storage-tárolóban lévő helyi mappából, blobból vagy több blobból keres videót. Az összes beállítást kipróbálhatja a kód többi sorában is.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Azure Media-környezet létrehozása

Adja hozzá a következő metódust a **program** osztályhoz. Ez az AMS hitelesítő adatait használja az AMS-sel való kommunikáció engedélyezéséhez.

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>A kód hozzáadása Azure Storage-környezet létrehozásához

Adja hozzá a következő metódust a **program** osztályhoz. A tároló hitelesítő adatai alapján létrehozott tárolási környezetet a blob Storage eléréséhez használja.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Adja hozzá a kódot az Azure Media-eszközök helyi fájlból és blobból való létrehozásához

A Content Moderator adathordozó-feldolgozó a Azure Media Services platformon belüli **eszközökön** futtat feladatokat.
Ezek a módszerek létrehozzák az eszközöket egy helyi fájlból vagy egy társított blobból.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>A kód hozzáadása a videók (Blobok) gyűjteményének vizsgálatához egy tárolón belül

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Adja hozzá a metódust a Content Moderator-feladatok futtatásához

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>Segítő függvények hozzáadása

Ezek a módszerek letöltik a Content Moderator kimeneti fájlt (JSON) a Azure Media Services eszközről, és segítenek a moderálási feladatok állapotának nyomon követésében, hogy a program képes legyen naplózni a futó állapotot a konzolon.

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>A program futtatása és a kimenet áttekintése

A Content moderációs feladatok befejezése után elemezze a JSON-választ. Ezek az elemek a következők:

- Videó információinak összegzése
- **Felvételek** "**töredékek**"
-  "**Események**" **reviewRecommended "(= TRUE vagy FALSE)"** jelzővel a **felnőtt** és a **zamatos** pontszámok alapján
- a **Start**, az **időtartam**, a **totalDuration** és az **időbélyeg** "ketyeg". Osztás az **időskála** alapján a szám másodpercben való lekéréséhez.
 
> [!NOTE]
> - `adultScore` a tartalom lehetséges jelenléti és előrejelzési pontszámát jelöli, amely bizonyos helyzetekben szexuálisan explicit vagy felnőttnek tekinthető.
> - `racyScore` a tartalom lehetséges jelenlétét és előrejelzési pontszámát jelöli, amely bizonyos helyzetekben szexuálisan szuggesztív vagy érett lehet.
> - `adultScore` és `racyScore` 0 és 1 között van. Minél magasabb a pontszám, annál nagyobb a modell, ami azt jelzi, hogy a kategória is alkalmazható. Ez az előzetes verzió egy statisztikai modellre támaszkodik, nem pedig manuálisan kódolt eredményeket. Javasoljuk, hogy tesztelje a saját tartalmait annak meghatározásához, hogy az egyes kategóriák hogyan illeszkednek a követelményekhez.
> - `reviewRecommended` értéke igaz vagy hamis, a belső pontszám küszöbértékének függvényében. Az ügyfeleknek fel kell mérniük, hogy ezt az értéket használják-e, vagy egyéni küszöbértékeket határoznak meg a tartalmi szabályzatok alapján.

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan hozhatja ki a moderálási kimenetből származó [video-felülvizsgálatokat](video-reviews-quickstart-dotnet.md) .

[Töltse le a Visual Studio-megoldást](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ehhez és más Content moderator a .net-hez.