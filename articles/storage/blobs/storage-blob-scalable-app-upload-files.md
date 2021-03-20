---
title: Nagy mennyiségű véletlenszerű adatok párhuzamos feltöltése az Azure Storage-ba
description: Ismerje meg, hogyan tölthetők fel nagy mennyiségű véletlenszerű adatok párhuzamosan egy Azure Storage-fiókba az Azure Storage ügyféloldali kódtár használatával
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: ed7020a58f3f15403108934bcc3fab644bd1b627
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584465"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Nagy mennyiségű véletlenszerű adat párhuzamos feltöltése az Azure Storage-ba

Ez az oktatóanyag egy sorozat második része. Ez az oktatóanyag egy olyan alkalmazás üzembe helyezését mutatja be, amely nagy mennyiségű véletlenszerű adat feltöltését végzi egy Azure tárfiókba.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Kapcsolati sztring konfigurálása
> * Az alkalmazás létrehozása
> * Az alkalmazás futtatása
> * A kapcsolatok számának ellenőrzése

A Microsoft Azure Blob Storage méretezhető szolgáltatást biztosít az adatai tárolásához. Ahhoz, hogy az alkalmazás a lehető legjobb teljesítménnyel működhessen, ajánlatos megismerkedni a Blob Storage működésével. Az Azure-Blobok korlátainak ismerete fontos, ha többet szeretne megtudni ezekről a korlátokról, látogasson el a [blob Storage méretezhetőségi és teljesítménybeli céljaira](../blobs/scalability-targets.md).

A [partíció elnevezése](../blobs/storage-performance-checklist.md#partitioning) a nagy teljesítményű alkalmazások Blobokkal történő tervezésekor egy másik fontos tényező. A 4 MiB-nél nagyobb vagy azzal egyenlő blokkos méretek esetén a [nagy átviteli sebességű blokk blobokat](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) használja a rendszer, és a partíciók elnevezése nem befolyásolja a teljesítményt. A 4 MiB-nél kisebb méretű blokkok esetében az Azure Storage egy tartományon alapuló particionálási sémát használ a méretezéshez és a terheléselosztáshoz. Ez a konfiguráció azt jelenti, hogy a hasonló elnevezési konvenciókkal vagy előtagokkal rendelkező fájlok ugyanarra a partícióra kerülnek. Ez a logika magában foglalja a tároló nevét, amelybe a fájlokat feltöltik. Ebben az oktatóanyagban olyan fájlokat használ, amelyek nevek helyett GUID-azonosítókkal, valamint véletlenszerűen létrehozott tartalmakkal rendelkeznek. Ezeket azután öt különböző, véletlenszerű nevű tárolóba töltjük fel.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez el kell végeznie az előző tárolási oktatóanyagot: [Virtuális gép és tárfiók létrehozása skálázható alkalmazások számára][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>A virtuális gép távoli vezérlése

Használja az alábbi parancsot a helyi gépén, ha egy távoli asztali kapcsolatot szeretne létrehozni a virtuális géphez. Cserélje le az IP-címet a virtuális gépe nyilvános IP-címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```console
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Kapcsolati sztring konfigurálása

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Másolja ki az elsődleges vagy a másodlagos kulcs **kapcsolati sztringjét**. Jelentkezzen be az előző oktatóanyagban létrehozott virtuális gépbe. Nyissa meg rendszergazdaként a **parancssort** és futtassa a `setx` parancsot a `/m` kapcsolóval. Ez a parancs egy gépbeállításhoz tartozó környezeti változó értékét menti. A környezeti változó nem érhető el, amíg újra be nem tölti a **parancssort**. Cserélje le **\<storageConnectionString\>** a következő mintát:

```console
setx storageconnectionstring "<storageConnectionString>" /m
```

Ha végzett, nyissa meg újból a **parancssort**, lépjen be a `D:\git\storage-dotnet-perf-scale-app` mappába, és gépelje be a következő parancsot az alkalmazás buildeléséhez: `dotnet build`.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Nyissa meg a `D:\git\storage-dotnet-perf-scale-app` címet.

Az alkalmazás futtatásához írja be a következőt: `dotnet run`. A `dotnet` parancs első futtatásakor feltölti a helyi csomaggyorsítótárat, hogy javítsa a visszaállítási sebességet és, hogy offline hozzáférést tegyen lehetővé. A parancs végrehajtása akár egy percet is igénybe vehet, és csak egyszer kell végrehajtani.

```console
dotnet run
```

Az alkalmazás öt, véletlenszerűen elnevezett tárolót hoz létre, és megkezdi az átmeneti könyvtárban lévő fájlok feltöltését a tárfiókba.

A `UploadFilesAsync` metódus a következő példában látható:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Scalable.cs" id="Snippet_UploadFilesAsync":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

A szálak minimális és maximális száma 100-re van állítva, hogy nagy számú egyidejű kapcsolat engedélyezett legyen.

```csharp
private static async Task UploadFilesAsync()
{
    // Create five randomly named containers to store the uploaded files.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();

    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // Path to the directory to upload
    string uploadPath = currentdir + "\\upload";

    // Start a timer to measure how long it takes to upload all the files.
    Stopwatch time = Stopwatch.StartNew();

    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);

        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections
        // are retried with a back off policy. As multiple large files are being uploaded using
        // large block sizes, this can cause an issue if an exponential retry policy is not defined.
        // Additionally, parallel operations are enabled with a thread count of 8.
        // This should be a multiple of the number of processor cores in the machine.
        // Lastly, MD5 hash validation is disabled for this example, improving the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };

        // Create a new instance of the SemaphoreSlim class to 
        // define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set the block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;

            await sem.WaitAsync();

            // Create a task for each file to upload. The tasks are
            // added to a collection and all run asynchronously.
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));

            count++;
        }

        // Run all the tasks asynchronously.
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
A szálkezelési és a kapcsolati korlátozások beállításán felül az [UploadFromStreamAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.uploadfromstreamasync) metódus [BlobRequestOptions](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions) beállítása úgy van konfigurálva, hogy párhuzamosságot használjon és letiltsa az MD5-kivonat érvényesítését. A fájlok feltöltése 100 MB méretű blokkokban történik. Ez a konfiguráció jobb teljesítményt biztosít, a használata azonban gyengén teljesítő hálózaton nem ajánlott, mert hiba esetén a teljes 100 MB-os blokk feltöltését újra kell kezdeni.

|Tulajdonság|Érték|Leírás|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount)| 8| A beállítás feltöltéskor blokkokra töri a blobot. A legnagyobb teljesítmény érdekében ennek az értéknek a magok számának nyolcszor kell lennie. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.disablecontentmd5validation)| true| Ez a tulajdonság letiltja a feltöltött tartalom MD5-kivonat ellenőrzését. A gyorsabb átvitel érdekében tiltsa le az MD5-ellenőrzést. Így azonban nem biztosított a folyamatban lévő átvitelben érintett fájlok érvényessége vagy integritása.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.storeblobcontentmd5)| hamis| Ez a tulajdonság határozza meg, hogy az MD5 kivonatoló kiszámítása és fájlban való tárolása megtörtént-e.   |
| [RetryPolicy](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.retrypolicy)| 2-second backoff with 10 max retry |A kérések újrapróbálkozási szabályzatát határozza meg. Kapcsolódási hiba esetén a rendszer újra próbálkozik, ebben a példában az [ExponentialRetry](/dotnet/api/microsoft.azure.batch.common.exponentialretry) szabályzat 2 másodperces leállításra és legfeljebb 10 újrapróbálkozásra van konfigurálva. Ez a beállítás akkor fontos, ha az alkalmazás közelebb kerül a blob Storage skálázhatósági céljainak eléréséhez. További információkért lásd [a blob Storage skálázhatósági és teljesítménybeli céljait](../blobs/scalability-targets.md)ismertető témakört.  |

---

Az alábbi példa a Windowson futtatott alkalmazás kimenetének egy része.

```console
Created container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Created container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Created container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Created container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Created container 92480da9-f695-4a42-abe8-fb35e71eb887
Iterating in directory: C:\git\myapp\upload
Found 5 file(s)
Uploading 1d596d16-f6de-4c4c-8058-50ebd8141e4d.pdf to container 2dbb45f4-099e-49eb-880c-5b02ebac135e
Uploading 242ff392-78be-41fb-b9d4-aee8152a6279.pdf to container 0d784365-3bdf-4ef2-b2b2-c17b6480792b
Uploading 38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.pdf to container 42ac67f2-a316-49c9-8fdb-860fb32845d7
Uploading 45930d63-b0d0-425f-a766-cda27ff00d32.pdf to container f0357772-cb04-45c3-b6ad-ff9b7a5ee467
Uploading 5129b385-5781-43be-8bac-e2fbb7d2bd82.pdf to container 92480da9-f695-4a42-abe8-fb35e71eb887
Uploaded 5 files in 16.9552163 seconds
```

### <a name="validate-the-connections"></a>A kapcsolatok ellenőrzése

A fájlok feltöltése közben ellenőrizheti a tárfiók egyidejű kapcsolatainak számát. Nyisson meg egy konzol ablakot és írja be a következőt: `netstat -a | find /c "blob:https"` . Ez a parancs a jelenleg megnyitott kapcsolatok számát jeleníti meg. Ahogy az alábbi példában is látható, a 800-kapcsolatok megnyílnak a véletlenszerű fájlok Storage-fiókba való feltöltésekor. Ez az érték a feltöltés futtatása során folyamatosan változik. Blokkrészek párhuzamos feltöltésével a tartalmak áthelyezéséhez szükséges idő jelentősen csökken.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Következő lépések

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
