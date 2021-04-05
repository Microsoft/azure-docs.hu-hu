---
title: BLOB-pillanatkép létrehozása és kezelése a .NET-ben
titleSuffix: Azure Storage
description: Ebből a cikkből megtudhatja, hogyan használhatja a .NET ügyféloldali függvénytárat egy blob írásvédett pillanatképének létrehozásához egy adott pillanatban a blob-adatok biztonsági mentéséhez.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/27/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: ae1d72c30fb7c45c92f24ec5132859a45c6ae953
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543374"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>BLOB-pillanatkép létrehozása és kezelése a .NET-ben

A pillanatképek egy adott időpontban végrehajtott blob írásvédett verziója, amely egy adott időpontban történik. Ez a cikk bemutatja, hogyan hozhat létre és kezelhet blob-pillanatképeket a [.net-hez készült Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage)használatával.

További információ az Azure Storage-beli blob-pillanatképekről: [blob-Pillanatképek](snapshots-overview.md).

## <a name="create-a-snapshot"></a>Pillanatkép létrehozása

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Ha a .NET-hez készült Azure Storage ügyféloldali kódtára 12. x verziójával szeretne pillanatképet készíteni a blokk blobról, használja a következő módszerek egyikét:

- [CreateSnapshot](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.createsnapshotasync)

A következő mintakód bemutatja, hogyan hozhat létre a 12. x verziójú pillanatképet. Vegyen fel egy hivatkozást az [Azure. Identity](https://www.nuget.org/packages/azure.identity) könyvtárra, hogy az Azure ad hitelesítő adatait használja a szolgáltatáshoz intézett kérések engedélyezéséhez.

```csharp
private static async Task CreateBlockBlobSnapshot(string accountName, string containerName, string blobName, Stream data)
{
    const string blobServiceEndpointSuffix = ".blob.core.windows.net";
    Uri containerUri = new Uri("https://" + accountName + blobServiceEndpointSuffix + "/" + containerName);

    // Get a container client object and create the container.
    BlobContainerClient containerClient = new BlobContainerClient(containerUri,
        new DefaultAzureCredential());
    await containerClient.CreateIfNotExistsAsync();

    // Get a blob client object.
    BlobClient blobClient = containerClient.GetBlobClient(blobName);

    try
    {
        // Upload text to create a block blob.
        await blobClient.UploadAsync(data);

        // Add blob metadata.
        IDictionary<string, string> metadata = new Dictionary<string, string>
        {
            { "ApproxBlobCreatedDate", DateTime.UtcNow.ToString() },
            { "FileType", "text" }
        };
        await blobClient.SetMetadataAsync(metadata);

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        await blobClient.CreateSnapshotAsync();
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Ha a .NET-hez készült Azure Storage ügyféloldali kódtára 11. x verziójával szeretné létrehozni a blokk blob pillanatképét, használja az alábbi módszerek egyikét:

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

A következő mintakód bemutatja, hogyan hozhat létre egy pillanatképet a 11. x verziójával. Ez a példa további metaadatokat határoz meg a pillanatkép létrehozásakor.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
        Console.WriteLine(snapshot.SnapshotQualifiedStorageUri.PrimaryUri);
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

## <a name="delete-snapshots"></a>Pillanatképek törlése

BLOB törléséhez először törölnie kell a blob összes pillanatképét. A pillanatképeket egyenként is törölheti, vagy megadhatja, hogy a rendszer az összes pillanatképet törli a forrás blob törlésekor. Ha olyan blobot próbál törölni, amely még tartalmaz pillanatképeket, akkor a hiba eredménye.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Ha törölni szeretne egy blobot és annak pillanatképeit az Azure Storage .NET-hez készült ügyféloldali kódtár 12. x verziójával, használja az alábbi módszerek egyikét, és foglalja bele a [DeleteSnapshotsOption](/dotnet/api/azure.storage.blobs.models.deletesnapshotsoption) enumerálást:

- [Törlés](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteasync)
- [Deleteifexists paranccsal](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.deleteifexistsasync)

A következő mintakód bemutatja, hogyan törölhet egy blobot és annak pillanatképeit a .NET-ben, ahol a egy `blobClient` [BlobClient](/dotnet/api/azure.storage.blobs.blobclient)típusú objektum.

```csharp
await blobClient.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, default);
```

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Ha törölni szeretne egy blobot és annak pillanatképeit az Azure Storage .NET-hez készült ügyféloldali kódtár 11. x verziójával, használja a következő blob-törlési módszerek egyikét, és foglalja bele a [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) enumerálást:

- [Törlés](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [Deleteifexists paranccsal](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

A következő mintakód bemutatja, hogyan törölhet egy blobot és annak pillanatképeit a .NET-ben, ahol a (z `blockBlob` ) [CloudBlockBlob] [dotnet_CloudBlockBlob] típusú objektum:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

---

## <a name="next-steps"></a>Következő lépések

- [BLOB-Pillanatképek](snapshots-overview.md)
- [BLOB-verziók](versioning-overview.md)
- [Blobok helyreállítható törlése](./soft-delete-blob-overview.md)