---
title: Nagy mennyiségű véletlenszerű adatok párhuzamos feltöltése az Azure Storage-ba
description: Ismerje meg, hogyan tölthetők fel nagy mennyiségű véletlenszerű adatok párhuzamosan egy Azure Storage-fiókba az Azure Storage ügyféloldali kódtár használatával
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: dd87e1a9bcff55813dff420976df58351386fb34
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75371938"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Nagy mennyiségű véletlenszerű adat párhuzamos feltöltése az Azure Storage-ba

Ez az oktatóanyag egy sorozat második része. Ez az oktatóanyag egy olyan alkalmazás üzembe helyezését mutatja be, amely nagy mennyiségű véletlenszerű adat feltöltését végzi egy Azure tárfiókba.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Kapcsolati sztring konfigurálása
> * Az alkalmazás létrehozása
> * Az alkalmazás futtatása
> * A kapcsolatok számának ellenőrzése

Az Azure Blob Storage skálázható szolgáltatást biztosít adatainak tárolásához. Ahhoz, hogy az alkalmazás a lehető legjobb teljesítménnyel működhessen, ajánlatos megismerkedni a Blob Storage működésével. Az Azure-Blobok korlátainak ismerete fontos, ha többet szeretne megtudni ezekről a korlátokról, látogasson el a [blob Storage méretezhetőségi és teljesítménybeli céljaira](../blobs/scalability-targets.md).

A [partíció elnevezése](../blobs/storage-performance-checklist.md#partitioning) a nagy teljesítményű alkalmazások Blobokkal történő tervezésekor egy másik fontos tényező. A 4 MiB-nél nagyobb vagy azzal egyenlő blokkos méretek esetén a [nagy átviteli sebességű blokk blobokat](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) használja a rendszer, és a partíciók elnevezése nem befolyásolja a teljesítményt. A 4 MiB-nél kisebb méretű blokkok esetében az Azure Storage egy tartományon alapuló particionálási sémát használ a méretezéshez és a terheléselosztáshoz. Ez a konfiguráció azt jelenti, hogy a hasonló elnevezési konvenciókkal vagy előtagokkal rendelkező fájlok ugyanarra a partícióra kerülnek. Ez a logika magában foglalja a tároló nevét, amelybe a fájlokat feltöltik. Ebben az oktatóanyagban olyan fájlokat használ, amelyek nevek helyett GUID-azonosítókkal, valamint véletlenszerűen létrehozott tartalmakkal rendelkeznek. Ezeket azután öt különböző, véletlenszerű nevű tárolóba töltjük fel.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez el kell végeznie az előző tárolási oktatóanyagot: [Virtuális gép és tárfiók létrehozása skálázható alkalmazások számára][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>A virtuális gép távoli vezérlése

Használja az alábbi parancsot a helyi gépén, ha egy távoli asztali kapcsolatot szeretne létrehozni a virtuális géphez. Cserélje le az IP-címet a virtuális gépe nyilvános IP-címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Kapcsolati sztring konfigurálása

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Másolja ki az elsődleges vagy a másodlagos kulcs **kapcsolati sztringjét**. Jelentkezzen be az előző oktatóanyagban létrehozott virtuális gépbe. Nyissa meg rendszergazdaként a **parancssort** és futtassa a `setx` parancsot a `/m` kapcsolóval. Ez a parancs egy gépbeállításhoz tartozó környezeti változó értékét menti. A környezeti változó nem érhető el, amíg újra be nem tölti a **parancssort**. Cserélje ** \<le\> a storageConnectionString** a következő mintában:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

Ha végzett, nyissa meg újból a **parancssort**, lépjen be a `D:\git\storage-dotnet-perf-scale-app` mappába, és gépelje be a következő parancsot az alkalmazás buildeléséhez: `dotnet build`.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Nyissa meg a `D:\git\storage-dotnet-perf-scale-app` címet.

Az alkalmazás futtatásához írja be a következőt: `dotnet run`. A `dotnet` parancs első futtatásakor feltölti a helyi csomaggyorsítótárat, hogy javítsa a visszaállítási sebességet és, hogy offline hozzáférést tegyen lehetővé. A parancs végrehajtása akár egy percet is igénybe vehet, és csak egyszer kell végrehajtani.

```
dotnet run
```

Az alkalmazás öt, véletlenszerűen elnevezett tárolót hoz létre, és megkezdi az átmeneti könyvtárban lévő fájlok feltöltését a tárfiókba. Az alkalmazás a szálak minimális számát 100-ra állítja, a [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) változó értékét pedig 100-ra annak érdekében, hogy az alkalmazás futtatása közben nagy számú párhuzamosan futó kapcsolat legyen futtatható.

A szálkezelési és a kapcsolati korlátozások beállításán felül az [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) metódus [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) beállítása úgy van konfigurálva, hogy párhuzamosságot használjon és letiltsa az MD5-kivonat érvényesítését. A fájlok feltöltése 100 MB méretű blokkokban történik. Ez a konfiguráció jobb teljesítményt biztosít, a használata azonban gyengén teljesítő hálózaton nem ajánlott, mert hiba esetén a teljes 100 MB-os blokk feltöltését újra kell kezdeni.

|Tulajdonság|Érték|Leírás|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| A beállítás feltöltéskor blokkokra töri a blobot. A legnagyobb teljesítmény érdekében ennek az értéknek a magok számának nyolcszor kell lennie. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| igaz| Ez a tulajdonság letiltja a feltöltött tartalom MD5-kivonat ellenőrzését. A gyorsabb átvitel érdekében tiltsa le az MD5-ellenőrzést. Így azonban nem biztosított a folyamatban lévő átvitelben érintett fájlok érvényessége vagy integritása.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| hamis| Ez a tulajdonság határozza meg, hogy az MD5 kivonatoló kiszámítása és fájlban való tárolása megtörtént-e.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| 2-second backoff with 10 max retry |A kérések újrapróbálkozási szabályzatát határozza meg. Kapcsolódási hiba esetén a rendszer újra próbálkozik, ebben a példában az [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) szabályzat 2 másodperces leállításra és legfeljebb 10 újrapróbálkozásra van konfigurálva. Ez a beállítás akkor fontos, ha az alkalmazás közelebb kerül a blob Storage skálázhatósági céljainak eléréséhez. További információkért lásd [a blob Storage skálázhatósági és teljesítménybeli céljait](../blobs/scalability-targets.md)ismertető témakört.  |

A következő példában az `UploadFilesAsync` feladat látható:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

Az alábbi példa a Windowson futtatott alkalmazás kimenetének egy része.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>A kapcsolatok ellenőrzése

A fájlok feltöltése közben ellenőrizheti a tárfiók egyidejű kapcsolatainak számát. Nyissa meg a **parancssort**, és írja be a `netstat -a | find /c "blob:https"` parancsot. Ez a parancs a `netstat` használatával mutatja a jelenleg megnyitott kapcsolatok számát. A következő példa egy ahhoz hasonló kimenetet mutat be, mint amit akkor láthat, ha saját maga futtatja az oktatóanyagot. Ahogy a példában látható, 800 kapcsolat volt megnyitva a fájlok tárfiókba történő véletlenszerű feltöltése közben. Ez az érték a feltöltés futtatása során folyamatosan változik. Blokkrészek párhuzamos feltöltésével a tartalmak áthelyezéséhez szükséges idő jelentősen csökken.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>További lépések

A sorozat második részében megismerkedett a nagy mennyiségű véletlenszerű adat tárfiókba történő párhuzamos feltöltésével, többek között a következőkkel:

> [!div class="checklist"]
> * Kapcsolati sztring konfigurálása
> * Az alkalmazás létrehozása
> * Az alkalmazás futtatása
> * A kapcsolatok számának ellenőrzése

A sorozat harmadik részében nagy mennyiségű adatot fog letölteni egy tárfiókból.

> [!div class="nextstepaction"]
> [Nagy mennyiségű véletlenszerű adat letöltése az Azure Storage-ból](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
