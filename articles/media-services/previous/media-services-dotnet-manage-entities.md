---
title: Eszközök és kapcsolódó entitások kezelése Media Services .NET SDK-val
description: Ismerje meg, hogyan kezelheti az eszközöket és a kapcsolódó entitásokat a .NET-hez készült Media Services SDK-val.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1bd8fd42-7306-463d-bfe5-f642802f1906
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b859ff49edb495be15cdf76719c3aaaf1ac72a6e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013771"
---
# <a name="managing-assets-and-related-entities-with-media-services-net-sdk"></a>Eszközök és kapcsolódó entitások kezelése Media Services .NET SDK-val

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-manage-entities.md)
> * [REST](media-services-rest-manage-entities.md)
> 
> 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Ez a témakör bemutatja, hogyan kezelheti Azure Media Services entitásokat a .NET-tel.

2017. április 1-től kezdődően a fiókokban a 90 napnál régebbi Feladat rekordok automatikusan törölve lesznek, a kapcsolódó Művelet rekordokkal egyetemben, még ha a rekordok összesített száma nem is éri el a maximális kvótát. Például 2017. április 1-jén a fiókban lévő minden olyan feladatra vonatkozó rekordot, amely a 2016 december 31-én régebbi, automatikusan törlődik. Ha archiválni szeretné a feladat/feladat adatait, használhatja a jelen témakörben ismertetett kódot.

## <a name="prerequisites"></a>Előfeltételek

Állítsa be a fejlesztési környezetet, és töltse fel a app.config fájlt a következő témakörben leírtak szerint: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel. 

## <a name="get-an-asset-reference"></a>Tárgyieszköz-hivatkozás beszerzése
Gyakori feladat, hogy a Media Services egy meglévő eszközre mutató hivatkozást kapjon. A következő mintakód bemutatja, hogyan kérhető le egy eszköz-hivatkozás a kiszolgálói környezeti objektum assets gyűjteményéből az eszköz azonosítója alapján. A következő kódrészlet egy LINQ-lekérdezést használ egy meglévő IAsset-objektumra mutató hivatkozás beszerzéséhez.

```csharp
    static IAsset GetAsset(string assetId)
    {
        // Use a LINQ Select query to get an asset.
        var assetInstance =
            from a in _context.Assets
            where a.Id == assetId
            select a;
        // Reference the asset as an IAsset.
        IAsset asset = assetInstance.FirstOrDefault();

        return asset;
    }
```

## <a name="list-all-assets"></a>Az összes eszköz listázása
Mivel a tárhelyen lévő eszközök száma növekszik, hasznos lehet az eszközök listázása. A következő mintakód bemutatja, hogyan lehet megismételni az eszközök gyűjteményét a kiszolgáló környezeti objektumán. Az egyes adategységek esetében a kód példa a tulajdonságok egyes értékeit is beírja a-konzolra. Az egyes eszközök például több médiafájlt is tartalmazhatnak. A kód például az egyes eszközökhöz társított összes fájlt kiírja.

```csharp
    static void ListAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IAsset asset in _context.Assets)
        {
            // Display the collection of assets.
            builder.AppendLine("");
            builder.AppendLine("******ASSET******");
            builder.AppendLine("Asset ID: " + asset.Id);
            builder.AppendLine("Name: " + asset.Name);
            builder.AppendLine("==============");
            builder.AppendLine("******ASSET FILES******");

            // Display the files associated with each asset. 
            foreach (IAssetFile fileItem in asset.AssetFiles)
            {
                builder.AppendLine("Name: " + fileItem.Name);
                builder.AppendLine("Size: " + fileItem.ContentFileSize);
                builder.AppendLine("==============");
            }
        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="get-a-job-reference"></a>Feladatok hivatkozásának beolvasása

Ha Media Services kódban dolgozik a feldolgozási feladatokkal, gyakran egy azonosító alapján kell megszereznie egy meglévő feladatra mutató hivatkozást. A következő mintakód bemutatja, hogyan kérhető le egy IJob objektumra mutató hivatkozás a feladatok gyűjteményből.

Előfordulhat, hogy a hosszú ideig futó kódolási feladatok elindításához meg kell adnia egy feladatot, és egy szálon ellenőriznie kell a feladatok állapotát. Ilyen esetekben, ha a metódus egy szálból tér vissza, a feladatokra vonatkozó frissített hivatkozást kell lekérnie.

```csharp
    static IJob GetJob(string jobId)
    {
        // Use a Linq select query to get an updated 
        // reference by Id. 
        var jobInstance =
            from j in _context.Jobs
            where j.Id == jobId
            select j;
        // Return the job reference as an Ijob. 
        IJob job = jobInstance.FirstOrDefault();

        return job;
    }
```

## <a name="list-jobs-and-assets"></a>Feladatok és eszközök listázása
Fontos szempont, hogy az eszközöket a társított feladattal rendelkező Media Services sorolja fel. A következő mintakód bemutatja, hogyan listázhatja az egyes IJob objektumokat, majd minden egyes feladathoz megjelenítheti a feladattal kapcsolatos tulajdonságokat, az összes kapcsolódó feladatot, az összes bemeneti eszközt és a kimeneti eszközöket. Az ebben a példában szereplő kód számos más feladathoz is hasznos lehet. Ha például egy vagy több korábban futtatott kódolási feladatból szeretné kilistázni a kimeneti eszközöket, akkor ez a kód azt mutatja be, hogyan férhet hozzá a kimeneti eszközökhöz. Ha egy kimeneti objektumra mutató hivatkozással rendelkezik, a tartalmat letöltheti más felhasználók vagy alkalmazások számára, vagy URL-címeket is biztosíthat. 

További információ az eszközök kézbesítési lehetőségeiről: [eszközök kézbesítése a .net-hez készült Media Services SDK-val](media-services-deliver-streaming-content.md).

```csharp
    // List all jobs on the server, and for each job, also list 
    // all tasks, all input assets, all output assets.

    static void ListJobsAndAssets()
    {
        string waitMessage = "Building the list. This may take a few "
            + "seconds to a few minutes depending on how many assets "
            + "you have."
            + Environment.NewLine + Environment.NewLine
            + "Please wait..."
            + Environment.NewLine;
        Console.Write(waitMessage);

        // Create a Stringbuilder to store the list that we build. 
        StringBuilder builder = new StringBuilder();

        foreach (IJob job in _context.Jobs)
        {
            // Display the collection of jobs on the server.
            builder.AppendLine("");
            builder.AppendLine("******JOB*******");
            builder.AppendLine("Job ID: " + job.Id);
            builder.AppendLine("Name: " + job.Name);
            builder.AppendLine("State: " + job.State);
            builder.AppendLine("Order: " + job.Priority);
            builder.AppendLine("==============");


            // For each job, display the associated tasks (a job  
            // has one or more tasks). 
            builder.AppendLine("******TASKS*******");
            foreach (ITask task in job.Tasks)
            {
                builder.AppendLine("Task Id: " + task.Id);
                builder.AppendLine("Name: " + task.Name);
                builder.AppendLine("Progress: " + task.Progress);
                builder.AppendLine("Configuration: " + task.Configuration);
                if (task.ErrorDetails != null)
                {
                    builder.AppendLine("Error: " + task.ErrorDetails);
                }
                builder.AppendLine("==============");
            }

            // For each job, display the list of input media assets.
            builder.AppendLine("******JOB INPUT MEDIA ASSETS*******");
            foreach (IAsset inputAsset in job.InputMediaAssets)
            {

                if (inputAsset != null)
                {
                    builder.AppendLine("Input Asset Id: " + inputAsset.Id);
                    builder.AppendLine("Name: " + inputAsset.Name);
                    builder.AppendLine("==============");
                }
            }

            // For each job, display the list of output media assets.
            builder.AppendLine("******JOB OUTPUT MEDIA ASSETS*******");
            foreach (IAsset theAsset in job.OutputMediaAssets)
            {
                if (theAsset != null)
                {
                    builder.AppendLine("Output Asset Id: " + theAsset.Id);
                    builder.AppendLine("Name: " + theAsset.Name);
                    builder.AppendLine("==============");
                }
            }

        }

        // Display output in console.
        Console.Write(builder.ToString());
    }
```

## <a name="list-all-access-policies"></a>Az összes hozzáférési szabályzat listázása
Media Services a hozzáférési házirendet megadhatja egy eszközön vagy annak fájljain. A hozzáférési házirend határozza meg egy fájl vagy eszköz engedélyeit (a hozzáférés típusát és az időtartamot). A Media Services-kódban általában egy IAccessPolicy objektum létrehozásával definiál egy hozzáférési szabályzatot, majd egy meglévő eszközzel társítja azt. Ezután létrehoz egy ILocator objektumot, amely lehetővé teszi, hogy közvetlen hozzáférést biztosítson a Media Services lévő eszközökhöz. A dokumentációs sorozathoz tartozó Visual Studio-projekt több olyan kódrészletet tartalmaz, amelyek bemutatják, hogyan hozhat létre és rendelhet hozzá hozzáférési házirendeket és lokátorokat az eszközökhöz.

A következő mintakód bemutatja, hogyan listázhatja az összes hozzáférési házirendet a kiszolgálón, és megjeleníti az egyesekhez társított engedélyek típusát. A hozzáférési szabályzatok megtekintésének egy másik hasznos módja a kiszolgálón lévő összes ILocator listázása, majd az egyes lokátorok esetében a társított hozzáférési szabályzatot a saját AccessPolicy tulajdonságával is listázhatja.

```csharp
    static void ListAllPolicies()
    {
        foreach (IAccessPolicy policy in _context.AccessPolicies)
        {
            Console.WriteLine("");
            Console.WriteLine("Name:  " + policy.Name);
            Console.WriteLine("ID:  " + policy.Id);
            Console.WriteLine("Permissions: " + policy.Permissions);
            Console.WriteLine("==============");

        }
    }
```
    
## <a name="limit-access-policies"></a>Hozzáférési szabályzatok korlátozása 

>[!NOTE]
> A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja (például olyan keresők szabályzatait, amelyek hosszú ideig érvényben maradnak, vagyis nem feltöltött szabályzatokat), a szabályzatazonosítónak is ugyanannak kell lennie. 

Létrehozhat például egy általános szabályzatot a következő kóddal, amely csak egyszer fut az alkalmazásban. Az azonosítókat naplófájlba is naplózhatja későbbi használat céljából:

```csharp
    double year = 365.25;
    double week = 7;
    IAccessPolicy policyYear = _context.AccessPolicies.Create("One Year", TimeSpan.FromDays(year), AccessPermissions.Read);
    IAccessPolicy policy100Year = _context.AccessPolicies.Create("Hundred Years", TimeSpan.FromDays(year * 100), AccessPermissions.Read);
    IAccessPolicy policyWeek = _context.AccessPolicies.Create("One Week", TimeSpan.FromDays(week), AccessPermissions.Read);

    Console.WriteLine("One year policy ID is: " + policyYear.Id);
    Console.WriteLine("100 year policy ID is: " + policy100Year.Id);
    Console.WriteLine("One week policy ID is: " + policyWeek.Id);
```

Ezután használhatja a kód meglévő azonosítóit a következőhöz hasonló módon:

```csharp
    const string policy1YearId = "nb:pid:UUID:2a4f0104-51a9-4078-ae26-c730f88d35cf";


    // Get the standard policy for 1 year read only
    var tempPolicyId = from b in _context.AccessPolicies
                       where b.Id == policy1YearId
                       select b;
    IAccessPolicy policy1Year = tempPolicyId.FirstOrDefault();

    // Get the existing asset
    var tempAsset = from a in _context.Assets
                where a.Id == assetID
                select a;
    IAsset asset = tempAsset.SingleOrDefault();

    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
        policy1Year,
        DateTime.UtcNow.AddMinutes(-5));
    Console.WriteLine("The locator base path is " + originLocator.BaseUri.ToString());
```

## <a name="list-all-locators"></a>Az összes lokátor listázása
A lokátor egy URL-cím, amely közvetlen elérési utat biztosít az eszköz eléréséhez, valamint az eszközhöz a lokátorhoz tartozó hozzáférési házirend által meghatározott engedélyekkel. Minden eszközhöz hozzá lehet rendelni egy ILocator-objektumot a lokátorok tulajdonságában. A kiszolgáló környezetében a lokátorok gyűjteménye is megtalálható, amely tartalmazza az összes lokátort.

A következő kódrészlet a kiszolgálón található összes lokátort listázza. Minden lokátornál megjelenik a kapcsolódó objektum és hozzáférési szabályzat azonosítója. Emellett megjeleníti az engedélyek típusát, a lejárati dátumot, valamint az eszköz teljes elérési útját.

Vegye figyelembe, hogy egy adott eszközhöz tartozó lokátor elérési útja csak az objektum alap URL-címe. Ha egy felhasználó vagy alkalmazás által megkereshető egyes fájlokhoz közvetlen elérési utat szeretne létrehozni, a kódnak hozzá kell adnia a megadott elérési utat a lokátor elérési útjához. Ennek módjáról további információt a következő témakörben talál: [eszközök kézbesítése a .net-hez készült Media Services SDK-val](media-services-deliver-streaming-content.md).

```csharp
    static void ListAllLocators()
    {
        foreach (ILocator locator in _context.Locators)
        {
            Console.WriteLine("***********");
            Console.WriteLine("Locator Id: " + locator.Id);
            Console.WriteLine("Locator asset Id: " + locator.AssetId);
            Console.WriteLine("Locator access policy Id: " + locator.AccessPolicyId);
            Console.WriteLine("Access policy permissions: " + locator.AccessPolicy.Permissions);
            Console.WriteLine("Locator expiration: " + locator.ExpirationDateTime);
            // The locator path is the base or parent path (with included permissions) to access  
            // the media content of an asset. To create a full URL to a specific media file, take 
            // the locator path and then append a file name and info as needed.  
            Console.WriteLine("Locator base path: " + locator.Path);
            Console.WriteLine("");
        }
    }
```

## <a name="enumerating-through-large-collections-of-entities"></a>Az entitások nagy gyűjteményének enumerálása
Az entitások lekérdezése esetén a rendszer egy legfeljebb 1000 entitást ad vissza, mert a nyilvános REST v2 a lekérdezés eredményét 1000 eredményre korlátozza. A kihagyás és az igény használata az entitások nagy gyűjteményén keresztül. 

A következő függvény a megadott Media Services fiókban lévő összes feladaton keresztül hurkokat mutat be. A Media Services a feladatok gyűjteményében a 1000-es feladatokat adja vissza. A függvény kihagyja a kihagyást, és gondoskodik arról, hogy minden feladat enumerálásra kerüljön (ha a fiókjában több mint 1000 feladat van).

```csharp
    static void ProcessJobs()
    {
        try
        {

            int skipSize = 0;
            int batchSize = 1000;
            int currentBatch = 0;

            while (true)
            {
                // Loop through all Jobs (1000 at a time) in the Media Services account
                IQueryable _jobsCollectionQuery = _context.Jobs.Skip(skipSize).Take(batchSize);
                foreach (IJob job in _jobsCollectionQuery)
                {
                    currentBatch++;
                    Console.WriteLine("Processing Job Id:" + job.Id);
                }

                if (currentBatch == batchSize)
                {
                    skipSize += batchSize;
                    currentBatch = 0;
                }
                else
                {
                    break;
                }
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }
```

## <a name="delete-an-asset"></a>Eszköz törlése
A következő példa töröl egy objektumot.

```csharp
    static void DeleteAsset( IAsset asset)
    {
        // delete the asset
        asset.Delete();

        // Verify asset deletion
        if (GetAsset(asset.Id) == null)
            Console.WriteLine("Deleted the Asset");

    }
```

## <a name="delete-a-job"></a>Feladatok törlése
A feladatok törléséhez ellenőriznie kell a feladatok állapotát az állapot tulajdonságban jelzett módon. A befejezett vagy megszakított feladatok törölhetők, míg bizonyos más állapotokban lévő feladatok, például az üzenetsor-kezelés, az ütemezett vagy a feldolgozás, először meg kell szüntetni, majd törölhetik őket.

A következő kódrészlet egy metódust mutat be a feladatok törléséhez a feladatok állapotának ellenőrzésével, majd az állapot befejezésének vagy megszakításának törlésekor. Ez a kód a következő témakör előző szakasza alapján használható a feladatokra való hivatkozás beszerzéséhez: feladatok hivatkozásának beolvasása.

```csharp
    static void DeleteJob(string jobId)
    {
        bool jobDeleted = false;

        while (!jobDeleted)
        {
            // Get an updated job reference.  
            IJob job = GetJob(jobId);

            // Check and handle various possible job states. You can 
            // only delete a job whose state is Finished, Error, or Canceled.   
            // You can cancel jobs that are Queued, Scheduled, or Processing,  
            // and then delete after they are canceled.
            switch (job.State)
            {
                case JobState.Finished:
                case JobState.Canceled:
                case JobState.Error:
                    // Job errors should already be logged by polling or event 
                    // handling methods such as CheckJobProgress or StateChanged.
                    // You can also call job.DeleteAsync to do async deletes.
                    job.Delete();
                    Console.WriteLine("Job has been deleted.");
                    jobDeleted = true;
                    break;
                case JobState.Canceling:
                    Console.WriteLine("Job is cancelling and will be deleted "
                        + "when finished.");
                    Console.WriteLine("Wait while job finishes canceling...");
                    Thread.Sleep(5000);
                    break;
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    job.Cancel();
                    Console.WriteLine("Job is scheduled or processing and will "
                        + "be deleted.");
                    break;
                default:
                    break;
            }

        }
    }
```


## <a name="delete-an-access-policy"></a>Hozzáférési szabályzat törlése
A következő mintakód bemutatja, hogyan kérhető le egy házirend-azonosítón alapuló hozzáférési házirendre mutató hivatkozás, majd törölhető a szabályzat.

```csharp
    static void DeleteAccessPolicy(string existingPolicyId)
    {
        // To delete a specific access policy, get a reference to the policy.  
        // based on the policy Id passed to the method.
        var policyInstance =
                from p in _context.AccessPolicies
                where p.Id == existingPolicyId
                select p;
        IAccessPolicy policy = policyInstance.FirstOrDefault();

        policy.Delete();

    }
```


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
