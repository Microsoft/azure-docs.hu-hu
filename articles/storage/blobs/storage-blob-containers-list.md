---
title: BLOB-tárolók listázása a .NET-Azure Storage használatával
description: Megtudhatja, hogyan listázhatja a blob-tárolókat az Azure Storage-fiókban a .NET ügyféloldali kódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: a12fc991734fe74e450aa14a477f3a4500ba659c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96937253"
---
# <a name="list-blob-containers-with-net"></a>BLOB-tárolók listázása a .NET-tel

Amikor egy Azure Storage-fiókban lévő tárolókat listáz a kódból, megadhat számos lehetőséget, amelyekkel kezelheti az Azure Storage-beli eredmények eredményét. Ez a cikk bemutatja, hogyan listázhat tárolókat a [.net-hez készült Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage)használatával.  

## <a name="understand-container-listing-options"></a>A tárolók listázási beállításainak ismertetése

A Storage-fiókban lévő tárolók listázásához hívja a következő módszerek egyikét:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

- [GetBlobContainers](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainers)
- [GetBlobContainersAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainersasync)

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

- [ListContainersSegmented](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmented)
- [ListContainersSegmentedAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.listcontainerssegmentedasync)

---

Ezeknek a módszereknek a túlterhelései további lehetőségeket biztosítanak a tárolók a listázási művelettel történő visszaküldéséhez. Ezeket a beállításokat a következő szakaszokban ismertetjük.

### <a name="manage-how-many-results-are-returned"></a>A visszaadott eredmények számának kezelése

Alapértelmezés szerint a listázási művelet egyszerre legfeljebb 5000 eredményt ad vissza. Ha kisebb eredményeket szeretne visszaadni, adjon meg nullától eltérő értéket a visszaadni kívánt eredmények oldalának méretéhez.

Ha a Storage-fiók több mint 5000 tárolót tartalmaz, vagy ha olyan oldalméretet adott meg, amely úgy van megadva, hogy a listázási művelet a Storage-fiókban lévő tárolók egy részhalmazát adja vissza, akkor az Azure Storage egy *folytatási tokent* ad vissza a tárolók listájával. A folytatási token egy átlátszatlan érték, amelyet az Azure Storage következő eredményeinek lekérésére használhat.

A kódban ellenőrizze a folytatási token értékét annak megállapításához, hogy üres-e (.NET V12 esetén) vagy Null (.NET v11 és korábbi verziók esetén). Ha a folytatási jogkivonat null értékű, akkor az eredmények halmaza befejeződött. Ha a folytatási jogkivonat nem null értékű, akkor ismét hívja meg a listázási módszert, és a folytatási tokenben adja meg a következő eredmények beolvasását, amíg a folytatási jogkivonat null nem lesz.

### <a name="filter-results-with-a-prefix"></a>Eredmények szűrése előtaggal

A tárolók listájának szűréséhez a paraméterhez meg kell adni egy karakterláncot `prefix` . Az előtag-karakterlánc tartalmazhat egy vagy több karaktert. Az Azure Storage ezt követően csak azokat a tárolókat adja vissza, amelyeknek a neve az adott előtaggal kezdődik.

### <a name="return-metadata"></a>Metaadatok visszaküldése

Ha vissza szeretné állítani a tároló metaadatait az eredményekkel, akkor a [BlobContainerTraits](/dotnet/api/azure.storage.blobs.models.blobcontainertraits) enumerálás (.net V12 esetében) vagy a [ContainerListingDetails](/dotnet/api/microsoft.azure.storage.blob.containerlistingdetails) Enum (a .net-v11 és a korábbi verziók esetében) **metaadat** értékét kell megadnia. Az Azure Storage a visszaadott tárolók metaadatait tartalmazza, így nem kell beolvasnia a tároló metaadatait is.

## <a name="example-list-containers"></a>Példa: tárolók listázása

A következő példa aszinkron módon Listázza azokat a tárolókat, amelyek egy megadott előtaggal kezdődnek. A példa felsorolja a megadott előtaggal kezdődő tárolókat, és a megadott számú eredményt adja vissza a listázási művelet hívása során. Ezután a folytatási tokent használja az eredmények következő szegmensének lekéréséhez. A példa a tároló metaadatait is visszaadja az eredményekkel együtt.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="Snippet_ListContainers":::

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
private static async Task ListContainersWithPrefixAsync(CloudBlobClient blobClient,
                                                        string prefix,
                                                        int? segmentSize)
{
    Console.WriteLine("List containers beginning with prefix {0}, plus container metadata:", prefix);

    BlobContinuationToken continuationToken = null;
    ContainerResultSegment resultSegment;

    try
    {
        do
        {
            // List containers beginning with the specified prefix,
            // returning segments of 5 results each.
            // Passing in null for the maxResults parameter returns the maximum number of results (up to 5000).
            // Requesting the container's metadata as part of the listing operation populates the metadata,
            // so it's not necessary to call FetchAttributes() to read the metadata.
            resultSegment = await blobClient.ListContainersSegmentedAsync(
                prefix, ContainerListingDetails.Metadata, segmentSize, continuationToken, null, null);

            // Enumerate the containers returned.
            foreach (var container in resultSegment.Results)
            {
                Console.WriteLine("\tContainer:" + container.Name);

                // Write the container's metadata keys and values.
                foreach (var metadataItem in container.Metadata)
                {
                    Console.WriteLine("\t\tMetadata key: " + metadataItem.Key);
                    Console.WriteLine("\t\tMetadata value: " + metadataItem.Value);
                }
            }

            // Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;

        } while (continuationToken != null);

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Lásd még

- [Tárolók listázása](/rest/api/storageservices/list-containers2)
- [BLOB-erőforrások enumerálása](/rest/api/storageservices/enumerating-blob-resources)
