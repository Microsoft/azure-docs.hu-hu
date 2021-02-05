---
title: Nagy mennyiségű véletlenszerű adatok letöltése az Azure Storage-ból | Microsoft Docs
description: Megismerkedhet nagy mennyiségű véletlenszerű adat letöltésével egy Azure Storage-fiókból az Azure SDK használatával
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 0c029abd87e1b819cc4d96e906be8824c019f433
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584974"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Nagy mennyiségű véletlenszerű adat letöltése az Azure Storage-ból

Ez az oktatóanyag egy sorozat harmadik része. Ez az oktatóanyag azt mutatja be, hogyan tölthető le nagy mennyiségű adat az Azure Storage-ból.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás frissítése
> * Az alkalmazás futtatása
> * A kapcsolatok számának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez el kell végeznie az előző Storage-oktatóanyagot, amelynek címe: [Nagy mennyiségű véletlenszerű adat párhuzamos feltöltése az Azure Storage-ba][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>A virtuális gép távoli vezérlése

 Használja az alábbi parancsot a helyi gépén, ha egy távoli asztali munkamenetet szeretne létrehozni a virtuális géppel. Cserélje le az IP-címet a virtuális gépe nyilvános IP-címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```console
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Az alkalmazás frissítése

Az előző oktatóanyagban csak a tárfiókba töltött fel fájlokat. Nyissa meg a következőt egy szövegszerkesztőben: `D:\git\storage-dotnet-perf-scale-app\Program.cs`. Cserélje le a `Main` metódust az alábbi mintára. Ez a példa megjegyzésként szerepelteti a feltöltési feladatot, a letöltési feladatot és a művelet befejeződésekor a tárfiók tartalmának törlési feladatát azonban nem.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to 
    // ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage 
    // client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        // await UploadFilesAsync();

        // Uncomment the following line to enable downloading of files from the storage account.
        // This is commented out initially to support the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        await DownloadFilesAsync();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.
        // This is commented out initially as the tutorial at 
        // https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files
        // has you upload only for one tutorial and download for the other.
        if (!exception)
        {
            // await DeleteExistingContainersAsync();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Az alkalmazás frissítését követően újra létre kell hoznia az alkalmazást. Nyisson meg egy `Command Prompt` elemet, és keresse meg a következőt: `D:\git\storage-dotnet-perf-scale-app`. Az alkalmazás újbóli létrehozása a `dotnet build` futtatásával történik, az alábbi példában látható módon:

```console
dotnet build
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Most, hogy az alkalmazás újra létre lett hozva, futtathatja az alkalmazást a frissített kóddal. Ha még nem tette meg, nyisson meg egy `Command Prompt` elemet, és keresse meg a következőt: `D:\git\storage-dotnet-perf-scale-app`.

Az alkalmazás futtatásához írja be a következőt: `dotnet run`.

```console
dotnet run
```

A következő példában az `DownloadFilesAsync` feladat látható:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Az alkalmazás a **storageconnectionstring** karakterlánccal megadott tárfiókban lévő tárolókat olvassa. A [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) metódussal megismétli a blobokat, és letölti azokat a helyi gépre a [DownloadToAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) metódus használatával.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_DownloadFilesAsync":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Az alkalmazás a **storageconnectionstring** karakterlánccal megadott tárfiókban lévő tárolókat olvassa. A Blobok 10-én a [ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync) metódus használatával, a tárolókban, és a [DownloadToFileAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadtofileasync) metódus használatával töltik le őket a helyi gépre.

A következő táblázat az egyes Blobok letöltéséhez megadott [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) mutatja be.

|Tulajdonság|Érték|Leírás|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Ez a tulajdonság letiltja a feltöltött tartalom MD5-kivonat ellenőrzését. A gyorsabb átvitel érdekében tiltsa le az MD5-ellenőrzést. Így azonban nem biztosított a folyamatban lévő átvitelben érintett fájlok érvényessége vagy integritása. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| hamis| Ez a tulajdonság határozza meg, hogy az MD5 kivonatoló kiszámítása és tárolása megtörtént-e.   |

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 
    // hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.
    // Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container, 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

---

### <a name="validate-the-connections"></a>A kapcsolatok ellenőrzése

A fájlok letöltése közben ellenőrizheti a tárfiók egyidejű kapcsolatainak számát. Nyisson meg egy konzol ablakot és írja be a következőt: `netstat -a | find /c "blob:https"` . Ez a parancs a jelenleg megnyitott kapcsolatok számát jeleníti meg. Ahogy az alábbi példában látható, több mint 280 kapcsolat is megnyitotta a fájlok a Storage-fiókból való letöltésekor.

```console
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Következő lépések

A sorozat harmadik részében megtanulta, hogyan lehet nagy mennyiségű adatforrást letölteni egy Storage-fiókból, beleértve a következőket:

> [!div class="checklist"]
> * Az alkalmazás futtatása
> * A kapcsolatok számának ellenőrzése

A portálon az átviteli sebesség és a késés mérőszámának ellenőrzéséhez nyissa meg a sorozat negyedik részét.

> [!div class="nextstepaction"]
> [Átviteli sebességgel és késéssel kapcsolatos mérőszámok ellenőrzése a portálon](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
