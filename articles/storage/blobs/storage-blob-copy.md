---
title: BLOB másolása az Azure Storage API-kkal
description: Megtudhatja, hogyan másolhat egy blobot az Azure Storage ügyféloldali kódtárai használatával.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/08/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 28b23555f97e58dde038c5e3e12a4b741b81776d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98059041"
---
# <a name="copy-a-blob-with-azure-storage-client-libraries"></a>BLOB másolása az Azure Storage ügyféloldali kódtárakkal

Ez a cikk bemutatja, hogyan másolhat egy blobot egy Azure Storage-fiókba. Azt is bemutatja, hogyan lehet megszakítani egy aszinkron másolási műveletet. A példában szereplő kód az Azure Storage ügyféloldali kódtárait használja.

## <a name="about-copying-blobs"></a>Tudnivalók a Blobok másolásáról

Ha ugyanazon a Storage-fiókon belül másol egy blobot, akkor ez egy szinkron művelet. Ha átmásolja a fiókokat, ez egy aszinkron művelet.

A másolási műveletekhez tartozó forrás blob lehet egy blokkos blob, egy hozzáfűző blob, egy oldal blobja vagy egy pillanatkép. Ha a cél blob már létezik, a forrás-blobtal megegyező típusú blobnak kell lennie. A rendszer felülírja a meglévő cél blobot.

A cél blob nem módosítható, amíg folyamatban van egy másolási művelet. A célhely blobjának csak egy függőben lévő másolási művelete lehet. Más szóval a blob nem lehet a több függőben lévő másolási művelet célja.

A teljes forrás blobot vagy fájlt mindig másolja a rendszer. A bájtok vagy blokkok egy tartományának másolása nem támogatott.

A Blobok másolásakor a rendszer a cél blobba másolja a rendszertulajdonságokat ugyanazokkal az értékekkel.

A másolási művelet a következő formák bármelyikét elvégezheti:

- Másolja a forrás blobot egy másik névvel ellátott cél blobba. A cél blob lehet ugyanazon blob típusú (blokk, Hozzáfűzés vagy lap) meglévő blobja, vagy a másolási művelet által létrehozott új blob is lehet.
- Másolja a forrás blobot egy olyan célhelyre, amely ugyanazzal a névvel van ellátva, és gyakorlatilag cserélje le a cél blobot. A másolási művelet eltávolítja a nem véglegesített blokkokat, és felülírja a cél blob metaadatait.
- Másolja a forrásfájlt az Azure file Service-be egy cél blobba. A cél blob lehet egy meglévő blokk-blob, vagy lehet egy új, a másolási művelet által létrehozott blokk-blob. A fájlok másolása az oldal blobokra vagy a hozzáfűzési Blobok nem támogatottak.
- Pillanatkép másolása az alap blobon keresztül. Egy pillanatképnek az alap blob pozícióba való előléptetésével a blob egy korábbi verzióját állíthatja vissza.
- Pillanatkép másolása egy másik nevű célhelyre. Az eredményül kapott cél blob egy írható blob, nem pillanatkép.

## <a name="copy-a-blob"></a>BLOB másolása

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

BLOB másolásához hívja a következő módszerek egyikét:

- [StartCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuri)
- [StartCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.startcopyfromuriasync)

A `StartCopyFromUri` és `StartCopyFromUriAsync` metódusok egy [CopyFromUriOperation](/dotnet/api/azure.storage.blobs.models.copyfromurioperation) objektumot adnak vissza, amely a másolási műveletre vonatkozó információkat tartalmaz.

A következő mintakód egy korábban létrehozott blobot jelképező [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) kap, és egy új blobba másolja azt ugyanabban a tárolóban:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_CopyBlob":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

BLOB másolásához hívja a következő módszerek egyikét:

- [StartCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopy)
- [StartCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.startcopyasync)

A `StartCopy` és `StartCopyAsync` metódusok visszaadják az állapot ellenőrzését vagy a másolási művelet megszakításához használt másolati azonosító értékét.

A következő mintakód egy korábban létrehozott blobra mutató hivatkozást kap, és egy új blobba másolja azt ugyanabban a tárolóban:

```csharp
private static async Task CopyBlockBlobAsync(CloudBlobContainer container)
{
    CloudBlockBlob sourceBlob = null;
    CloudBlockBlob destBlob = null;
    string leaseId = null;

    try
    {
        // Get a block blob from the container to use as the source.
        sourceBlob = container.ListBlobs().OfType<CloudBlockBlob>().FirstOrDefault();

        // Lease the source blob for the copy operation 
        // to prevent another client from modifying it.
        // Specifying null for the lease interval creates an infinite lease.
        leaseId = await sourceBlob.AcquireLeaseAsync(null);

        // Get a reference to a destination blob (in this case, a new blob).
        destBlob = container.GetBlockBlobReference("copy of " + sourceBlob.Name);

        // Ensure that the source blob exists.
        if (await sourceBlob.ExistsAsync())
        {
            // Get the ID of the copy operation.
            string copyId = await destBlob.StartCopyAsync(sourceBlob);

            // Fetch the destination blob's properties before checking the copy state.
            await destBlob.FetchAttributesAsync();

            Console.WriteLine("Status of copy operation: {0}", destBlob.CopyState.Status);
            Console.WriteLine("Completion time: {0}", destBlob.CopyState.CompletionTime);
            Console.WriteLine("Bytes copied: {0}", destBlob.CopyState.BytesCopied.ToString());
            Console.WriteLine("Total bytes: {0}", destBlob.CopyState.TotalBytes.ToString());
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
    finally
    {
        // Break the lease on the source blob.
        if (sourceBlob != null)
        {
            await sourceBlob.FetchAttributesAsync();

            if (sourceBlob.Properties.LeaseState != LeaseState.Available)
            {
                await sourceBlob.BreakLeaseAsync(new TimeSpan(0));
            }
        }
    }
}
```

# <a name="python-v12"></a>[Python V12](#tab/python)

BLOB másolásához hívja meg a [start_copy_from_url](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#start-copy-from-url-source-url--metadata-none--incremental-copy-false----kwargs-) metódust. A `start_copy_from_url` metódus egy olyan szótárt ad vissza, amely a másolási műveletre vonatkozó információkat tartalmazza.

A következő mintakód egy korábban létrehozott blobot jelképező [BlobClient](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient) kap, és egy új blobba másolja azt ugyanabban a tárolóban:

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_BlobCopy":::

---

## <a name="abort-a-copy-operation"></a>Másolási művelet megszakítása

A másolási művelet megszakítása nulla hosszúságú cél blobot eredményez. A cél blob metaadatai azonban az új értékeket a forrás blobból másolják, vagy explicit módon megadhatják a másolási művelet során. Ha meg szeretné őrizni az eredeti metaadatokat a másolás előtt, készítsen pillanatképet a cél blobról a másolási módszerek egyikének meghívása előtt.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

A másolási művelet állapotának lekéréséhez tekintse meg a [BlobProperties. CopyStatus](/dotnet/api/azure.storage.blobs.models.blobproperties.copystatus) tulajdonságot a cél blobon. A másolás befejezésekor a rendszer véglegesíti a végső blobot.

Amikor megszakít egy másolási műveletet, a célként megadott blob másolási állapota [CopyStatus. megszakítva](/dotnet/api/microsoft.azure.storage.blob.copystatus).

A [AbortCopyFromUri](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuri) és a [AbortCopyFromUriAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.abortcopyfromuriasync) metódus megszakít egy folyamatban lévő másolási műveletet.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CopyBlob.cs" id="Snippet_StopBlobCopy":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

A másolási művelet állapotának lekéréséhez tekintse meg a cél blob [CopyState. status](/dotnet/api/microsoft.azure.storage.blob.copystate.status) tulajdonságát. A másolás befejezésekor a rendszer véglegesíti a végső blobot.

Amikor megszakít egy másolási műveletet, a célként megadott blob másolási állapota [CopyStatus. megszakítva](/dotnet/api/microsoft.azure.storage.blob.copystatus).

A [AbortCopy](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopy) és a [AbortCopyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.abortcopyasync) metódus megszakít egy folyamatban lévő másolási műveletet.

```csharp
// Fetch the destination blob's properties before checking the copy state.
await destBlob.FetchAttributesAsync();

// Check the copy status. If it is still pending, abort the copy operation.
if (destBlob.CopyState.Status == CopyStatus.Pending)
{
    await destBlob.AbortCopyAsync(copyId);
    Console.WriteLine("Copy operation {0} has been aborted.", copyId);
}
```

# <a name="python-v12"></a>[Python V12](#tab/python)

A másolási művelet állapotának lekéréséhez tekintse meg a [get_blob_properties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#get-blob-properties---kwargs-) metódus által visszaadott "status" bejegyzést a [CopyProperties](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) -szótárban. A másolás befejezésekor a rendszer véglegesíti a végső blobot.

Egy másolási művelet megszakításakor az [állapot](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.copyproperties) "megszakított" értékre van állítva.

A [abort_copy](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.blobclient#abort-copy-copy-id----kwargs-) metódus megszakítja a folyamatban lévő másolási műveletet.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/copy_blob.py" id="Snippet_StopBlobCopy":::

---

## <a name="azure-sdks"></a>Azure SDK-k

További információ az Azure SDK-k használatáról:

 - [Azure SDK for .NET](https://github.com/azure/azure-sdk-for-net)
 - [Javához készült Azure SDK](https://github.com/azure/azure-sdk-for-java)
 - [Azure SDK for Python](https://github.com/azure/azure-sdk-for-python)
 - [Azure SDK for JavaScript](https://github.com/azure/azure-sdk-for-js)

## <a name="next-steps"></a>Következő lépések

A következő témakörök a Blobok másolásával és a folyamatban lévő másolási műveletek megszakításával kapcsolatos információkat tartalmazzák az Azure REST API-k használatával.

- [Copy Blob](/rest/api/storageservices/copy-blob)
- [BLOB másolásának megszakítása](/rest/api/storageservices/abort-copy-blob)
