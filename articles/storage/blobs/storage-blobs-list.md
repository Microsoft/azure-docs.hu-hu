---
title: Blobok listázása az Azure Storage API-kkal
description: Megtudhatja, hogyan listázhat blobokat a Storage-fiókban az Azure Storage ügyféloldali kódtárainak használatával. A példák a Blobok listázására, illetve a Blobok hierarchikus listázására szolgálnak, mintha könyvtárakba vagy mappákba vannak rendezve.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/24/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ff20b8bd0aab94cadadddbb7a4b7b32b1db1ee85
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046942"
---
# <a name="list-blobs-with-azure-storage-client-libraries"></a>Blobok listázása az Azure Storage ügyféloldali kódtárakkal

Ha a kódból listázza a blobokat, megadhatja az eredmények Azure Storage-ból való visszaadásának módját. Megadhatja az egyes eredményekben visszaadott eredmények számát, majd beolvashatja a következő készleteket. Megadhat egy előtagot olyan Blobok visszaadásához, amelyek neve megegyezik a karakterrel vagy karakterlánccal. Emellett a blobokat egy egyszerű felsorolási struktúrában vagy hierarchikusan is listázhatja. A hierarchikus lista a blobokat úgy adja vissza, mintha mappákba vannak rendezve.

## <a name="understand-blob-listing-options"></a>A Blobok listázási beállításainak ismertetése

A Storage-fiókban lévő Blobok listázásához hívja a következő módszerek egyikét:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [BlobContainerClient.GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs)
- [BlobContainerClient.GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync)
- [BlobContainerClient.GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)
- [BlobContainerClient.GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync)

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

- [CloudBlobClient. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobs)
- [CloudBlobClient. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmented)
- [CloudBlobClient. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listblobssegmentedasync)

A tárolóban lévő Blobok listázásához hívja a következő módszerek egyikét:

- [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs)
- [CloudBlobContainer. ListBlobsSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmented)
- [CloudBlobContainer. ListBlobsSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobssegmentedasync)

# <a name="python-v12"></a>[Python V12](#tab/python)

- [ContainerClient.list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-)

---

### <a name="manage-how-many-results-are-returned"></a>A visszaadott eredmények számának kezelése

Alapértelmezés szerint a listázási művelet egyszerre legfeljebb 5000 eredményt ad vissza, de megadhatja, hogy az egyes listázási műveletek hány eredményt adjanak vissza. A cikkben bemutatott példák bemutatják, hogyan adhatók vissza az eredmények a lapokon.

### <a name="filter-results-with-a-prefix"></a>Eredmények szűrése előtaggal

A Blobok listájának szűréséhez a paraméterhez meg kell adni egy karakterláncot `prefix` . Az előtag-karakterlánc tartalmazhat egy vagy több karaktert. Az Azure Storage ezt követően csak azokat a blobokat adja vissza, amelyeknek a neve az adott előtaggal kezdődik.

### <a name="return-metadata"></a>Metaadatok visszaküldése

A blob metaadatait az eredményekkel adhatja vissza.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

A [BlobTraits](/dotnet/api/azure.storage.blobs.models.blobtraits) enumerálás **metaadat** -értékének megadása.

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

A [BlobListingDetails](/dotnet/api/microsoft.azure.storage.blob.bloblistingdetails) enumerálás **metaadat** -értékének megadása. Az Azure Storage tartalmazza az összes visszaadott blob metaadatait, így nem kell meghívnia a kontextus egyik **FetchAttributes** metódusát a blob metaadatainak lekéréséhez.

# <a name="python-v12"></a>[Python V12](#tab/python)

`metadata` `include=` [List_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-)hívásakor a paraméter megadása.

---

### <a name="list-blob-versions-or-snapshots"></a>BLOB-verziók vagy-Pillanatképek listázása

- Ha a .NET V12-ügyfél függvénytárával szeretné kilistázni a blob-verziókat vagy pillanatképeket, a [BlobStates](/dotnet/api/azure.storage.blobs.models.blobstates) paramétert a **verzió** vagy a **Pillanatkép** mezővel kell megadni. A verziók és a pillanatképek a legrégebbitől a legújabbig vannak felsorolva. A verziók listázásával kapcsolatos további információkért lásd: [blob-verziók listázása](versioning-enable.md#list-blob-versions).

- A Python V12 ügyféloldali kódtár által készített Pillanatképek számának listázásához `num_snapshots` a `include=` [list_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-)meghívásakor a paramétert kell megadni.

### <a name="flat-listing-versus-hierarchical-listing"></a>A lapos Listázás és a hierarchikus Listázás

A blobokat az Azure Storage-ban egy egyszerű paradigma, nem pedig hierarchikus paradigma (például klasszikus fájlrendszer) szerint rendezi a rendszer. A blobokat azonban a *virtuális könyvtárakba* rendezheti a mappák struktúrájának kiépítéséhez. A virtuális könyvtár a blob nevének részét képezi, amelyet a határoló karakter jelez.

Ha a blobokat virtuális könyvtárakba szeretné szervezni, használjon egy elválasztó karaktert a blob nevében. Az alapértelmezett elválasztó karakter egy perjel (/), de bármilyen karaktert megadhat elválasztóként.

Ha elválasztó karakterrel nevezi el a blobokat, a Blobok hierarchikus listázása is megadható. Hierarchikus listázási művelet esetén az Azure Storage a szülőobjektum alá tartozó összes virtuális könyvtárat és blobot visszaadja. A listázási művelet rekurzív módon hívható át a hierarchiába, hasonlóan ahhoz, ahogy a klasszikus fájlrendszer programozott módon bejárta.

## <a name="use-a-flat-listing"></a>Egyszerű lista használata

Alapértelmezés szerint a listázási művelet egy egyszerű listaelemben lévő blobokat ad vissza. Egy egyszerű felsorolásban a blobokat nem a virtuális könyvtár rendezi.

Az alábbi példa felsorolja a megadott tárolóban lévő blobokat egy egyszerű lista használatával, egy választható szegmens méretének megadása mellett, és a blob nevét a konzol ablakába írja.

Ha engedélyezte a hierarchikus névtér funkciót a fiókjában, a címtárak nem virtuálisak. Ehelyett konkrét, független objektumok. Így a címtárak nulla hosszúságú blobként jelennek meg a listában.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsFlatListing":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Ha a listázási művelet több mint 5000 blobot ad vissza, vagy ha az elérhető Blobok száma meghaladja a megadott számot, akkor az Azure Storage egy *folytatási tokent* ad vissza a Blobok listájával. A folytatási token egy átlátszatlan érték, amelyet az Azure Storage következő eredményeinek lekérésére használhat.

A kódban ellenőrizze a folytatási token értékét annak meghatározásához, hogy null értékű-e. Ha a folytatási jogkivonat null értékű, akkor az eredmények halmaza befejeződött. Ha a folytatási jogkivonat nem null értékű, akkor ismét hívja meg a listázási műveletet, és a folytatási tokenben adja meg a következő eredmények beolvasását, amíg a folytatási jogkivonat null nem lesz.

```csharp
private static async Task ListBlobsFlatListingAsync(CloudBlobContainer container, int? segmentSize)
{
    BlobContinuationToken continuationToken = null;
    CloudBlob blob;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned
        // and execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(string.Empty,
                true, BlobListingDetails.Metadata, segmentSize, continuationToken, null, null);

            foreach (var blobItem in resultSegment.Results)
            {
                blob = (CloudBlob)blobItem;

                // Write out some blob properties.
                Console.WriteLine("Blob name: {0}", blob.Name);
            }

            Console.WriteLine();

           // Get the continuation token and loop until it is null.
           continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/list_blobs.py" id="Snippet_ListBlobs":::

---

A minta kimenete a következőhöz hasonló:

```console
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

## <a name="use-a-hierarchical-listing"></a>Hierarchikus lista használata

Ha hierarchikusan hívja meg a listázási műveletet, az Azure Storage a hierarchia első szintjén adja vissza a virtuális könyvtárakat és blobokat.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

A Blobok hierarchikus listázásához hívja meg a [BlobContainerClient. GetBlobsByHierarchy](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchy)vagy a [BlobContainerClient. GetBlobsByHierarchyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsbyhierarchyasync) metódust.

Az alábbi példa felsorolja a megadott tárolóban lévő blobokat egy hierarchikus lista használatával, egy választható szegmens méretének megadása mellett, és a blob nevét a konzol ablakba írja.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobsHierarchicalListing":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Az egyes virtuális könyvtárak [előtag](/dotnet/api/microsoft.azure.storage.blob.cloudblobdirectory.prefix) tulajdonsága úgy van beállítva, hogy egy rekurzív hívásban továbbítsa az előtagot a következő könyvtár lekéréséhez.

A Blobok hierarchikus listázásához állítsa a `useFlatBlobListing` listázási metódus paraméterét **hamis** értékre.

Az alábbi példa felsorolja a megadott tárolóban lévő blobokat egy egyszerű lista használatával, egy választható szegmens méretének megadása mellett, és a blob nevét a konzol ablakba írja.

```csharp
private static async Task ListBlobsHierarchicalListingAsync(CloudBlobContainer container, string prefix)
{
    CloudBlobDirectory dir;
    CloudBlob blob;
    BlobContinuationToken continuationToken = null;

    try
    {
        // Call the listing operation and enumerate the result segment.
        // When the continuation token is null, the last segment has been returned and
        // execution can exit the loop.
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(prefix,
                false, BlobListingDetails.Metadata, null, continuationToken, null, null);
            foreach (var blobItem in resultSegment.Results)
            {
                // A hierarchical listing may return both virtual directories and blobs.
                if (blobItem is CloudBlobDirectory)
                {
                    dir = (CloudBlobDirectory)blobItem;

                    // Write out the prefix of the virtual directory.
                    Console.WriteLine("Virtual directory prefix: {0}", dir.Prefix);

                    // Call recursively with the prefix to traverse the virtual directory.
                    await ListBlobsHierarchicalListingAsync(container, dir.Prefix);
                }
                else
                {
                    // Write out the name of the blob.
                    blob = (CloudBlob)blobItem;

                    Console.WriteLine("Blob name: {0}", blob.Name);
                }
                Console.WriteLine();
            }

            // Get the continuation token and loop until it is null.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="python-v12"></a>[Python V12](#tab/python)

Ha hierarchikusan szeretné kilistázni a blobokat, hívja meg a [walk_blobs](/azure/developer/python/sdk/storage/azure-storage-blob/azure.storage.blob.containerclient#walk-blobs-name-starts-with-none--include-none--delimiter--------kwargs-) metódust.

Az alábbi példa felsorolja a megadott tárolóban lévő blobokat egy hierarchikus lista használatával, egy választható szegmens méretének megadása mellett, és a blob nevét a konzol ablakba írja.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/list_blobs.py" id="Snippet_WalkHierarchy":::

---

A minta kimenete a következőhöz hasonló:

```console
Virtual directory prefix: FolderA/
Blob name: FolderA/blob1.txt
Blob name: FolderA/blob2.txt
Blob name: FolderA/blob3.txt

Virtual directory prefix: FolderA/FolderB/
Blob name: FolderA/FolderB/blob1.txt
Blob name: FolderA/FolderB/blob2.txt
Blob name: FolderA/FolderB/blob3.txt

Virtual directory prefix: FolderA/FolderB/FolderC/
Blob name: FolderA/FolderB/FolderC/blob1.txt
Blob name: FolderA/FolderB/FolderC/blob2.txt
Blob name: FolderA/FolderB/FolderC/blob3.txt
```

> [!NOTE]
> A blob-Pillanatképek nem szerepelhetnek hierarchikus listázási műveletekben.

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="next-steps"></a>Következő lépések

- [Blobok listázása](/rest/api/storageservices/list-blobs)
- [BLOB-erőforrások enumerálása](/rest/api/storageservices/enumerating-blob-resources)