---
title: BLOB-tároló létrehozása vagy törlése a .NET-Azure Storage használatával
description: Megtudhatja, hogyan hozhat létre vagy törölhet blob-tárolókat az Azure Storage-fiókban a .NET ügyféloldali kódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/04/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: dac50d8e35080a083e42891732512e012fae8fbd
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576447"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>Tároló létrehozása vagy törlése az Azure Storage-ban .NET-tel

Az Azure Storage-beli Blobok tárolóba vannak rendezve. A Blobok feltöltéséhez először létre kell hoznia egy tárolót. Ez a cikk bemutatja, hogyan hozhat létre és törölhet tárolókat a [.net-hez készült Azure Storage ügyféloldali kódtár](/dotnet/api/overview/azure/storage)használatával.

## <a name="name-a-container"></a>Tároló neve

A tároló nevének érvényes DNS-névnek kell lennie, mivel az a tároló vagy a Blobok kezeléséhez használt egyedi URI részét képezi. A tárolók elnevezése esetén kövesse az alábbi szabályokat:

- A tároló neve 3 – 63 karakter hosszúságú lehet.
- A tároló nevének betűvel vagy számmal kell kezdődnie, és csak kisbetűket, számokat és kötőjel (-) karaktert tartalmazhat.
- A tárolók neveiben két vagy több egymást követő kötőjel karakter nem engedélyezett.

A tároló URI-ja ebben a formátumban van:

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>Tároló létrehozása

Tároló létrehozásához hívja a következő módszerek egyikét:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

- [CreateBlobContainer](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainer)
- [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync)

Ezek a metódusok kivételt képeznek, ha már létezik ilyen nevű tároló.

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

- [Létrehozás](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [Createifnotexists metódust](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

A **create** és a **CreateAsync** metódus kivételt jelez, ha már létezik ilyen nevű tároló.

A **createifnotexists metódust** és a **CreateIfNotExistsAsync** metódus egy logikai értéket ad vissza, amely azt jelzi, hogy létrejött-e a tároló. Ha már létezik ilyen nevű tároló, ezek a metódusok **Hamis értéket** adnak vissza, jelezve, hogy egy új tároló nem lett létrehozva.

---

A tárolók közvetlenül a Storage-fiók alatt jönnek létre. Egy tárolót nem lehet beágyazni egy másik alá.

A következő példa aszinkron módon hoz létre egy tárolót:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateSampleContainerAsync":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```
---

## <a name="create-the-root-container"></a>A gyökér tároló létrehozása

A legfelső szintű tároló a Storage-fiók alapértelmezett tárolója. Mindegyik Storage-fiók rendelkezhet egy legfelső szintű tárolóval, amelynek neve *$root* kell, hogy legyen. A legfelső szintű tárolót explicit módon kell létrehozni vagy törölni.

A gyökér tárolóban tárolt blobokra hivatkozhat a gyökér-tároló nevének megadása nélkül is. A gyökérszintű tároló lehetővé teszi, hogy a Storage-fiók hierarchiájának legfelső szintjén lévő blobra hivatkozzon. A következő módon hivatkozhat például a gyökér tárolóban található blobokra:

`https://myaccount.blob.core.windows.net/default.html`

A következő példa a gyökérszintű tárolót szinkron módon hozza létre:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="CreateRootContainer":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```
---

## <a name="delete-a-container"></a>Tároló törlése

A tárolók .NET-ben való törléséhez használja a következő módszerek egyikét:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

- [Törlés](/dotnet/api/azure.storage.blobs.blobcontainerclient.delete)
- [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync)
- [Deleteifexists paranccsal](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteifexistsasync)

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

- [Törlés](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [Deleteifexists paranccsal](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)
---

A **delete** és a **DeleteAsync** metódus kivételt jelez, ha a tároló nem létezik.

A **deleteifexists paranccsal** és a **DeleteIfExistsAsync** metódus egy logikai értéket ad vissza, amely azt jelzi, hogy a tároló törölve lett-e. Ha a megadott tároló nem létezik, akkor ezek a metódusok **Hamis értéket** adnak vissza, jelezve, hogy a tárolót nem törölték.

A tárolók törlése után nem hozhat létre azonos nevű tárolót *legalább* 30 másodpercig. Az azonos nevű tároló létrehozására tett kísérlet sikertelen lesz a 409-as HTTP-hibakódnál (ütközés). A tárolón vagy a benne található blobokon végrehajtott egyéb műveletek meghiúsulnak a 404-as HTTP-hibakód esetén (nem található).

A következő példa törli a megadott tárolót, és kezeli a kivételt, ha a tároló nem létezik:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteSampleContainerAsync":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```
---

Az alábbi példa bemutatja, hogyan törölheti az összes olyan tárolót, amely a megadott előtaggal kezdődik.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Containers.cs" id="DeleteContainersWithPrefixAsync":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            await container.DeleteAsync();
        }

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

- [Tároló létrehozása művelet](/rest/api/storageservices/create-container)
- [Tároló törlése művelet](/rest/api/storageservices/delete-container)
