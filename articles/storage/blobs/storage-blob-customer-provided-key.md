---
title: Ügyfél által megadott kulcs meghatározása a blob Storage-hoz a .NET-Azure Storage szolgáltatással
description: Megtudhatja, hogyan határozhat meg egy ügyfél által megadott kulcsot a blob Storage-hoz a .NET használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: c3096da8b3c83dbfe8cfdd6a5fa4d177241334de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97693507"
---
# <a name="specify-a-customer-provided-key-on-a-request-to-blob-storage-with-net"></a>Ügyfél által megadott kulcs meghatározása a blob Storage-hoz a .NET-tel

Az Azure Blob Storage-ra irányuló kérelmeket használó ügyfelek számára lehetőség van arra, hogy egy egyéni kérelemben biztosítson AES-256 titkosítási kulcsot. A kérelemben szereplő titkosítási kulcs részletesen szabályozható a blob Storage-műveletek titkosítási beállításai között. A felhasználó által megadott kulcsok Azure Key Vault vagy egy másik kulcstárolóban tárolhatók.

Ez a cikk bemutatja, hogyan határozhat meg egy ügyfél által megadott kulcsot a .NET-kérelemben.

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Ha többet szeretne megtudni az Azure Identity ügyféloldali kódtár hitelesítéséről, tekintse meg az [Azure Identity ügyféloldali kódtára a .net-hez](/dotnet/api/overview/azure/identity-readme)című témakört.

## <a name="use-a-customer-provided-key-to-write-to-a-blob"></a>Egy felhasználó által megadott kulcs használata egy blobba való íráshoz

Az alábbi példa egy AES-256 kulcsot biztosít, amikor feltölt egy blobot a V12-es ügyféloldali kódtár blob Storage-hoz. A példa a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) objektum használatával engedélyezi az írási kérést az Azure ad-ben, de a kérést megosztott kulcsú hitelesítő adatokkal is engedélyezheti.

```csharp
async static Task UploadBlobWithClientKey(Uri blobUri,
                                          Stream data,
                                          byte[] key,
                                          string keySha256)
{
    // Create a new customer-provided key.
    // Key must be AES-256.
    var cpk = new CustomerProvidedKey(key);

    // Check the key's encryption hash.
    if (cpk.EncryptionKeyHash != keySha256)
    {
        throw new InvalidOperationException("The encryption key is corrupted.");
    }

    // Specify the customer-provided key on the options for the client.
    BlobClientOptions options = new BlobClientOptions()
    {
        CustomerProvidedKey = cpk
    };

    // Create the client object with options specified.
    BlobClient blobClient = new BlobClient(
        blobUri,
        new DefaultAzureCredential(),
        options);

    // If the container may not exist yet,
    // create a client object for the container.
    // The container client retains the credential and client options.
    BlobContainerClient containerClient =
        blobClient.GetParentBlobContainerClient();

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload the data using the customer-provided key.
        await blobClient.UploadAsync(data);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="next-steps"></a>Következő lépések

- [Titkosítási kulcs megadása a blob Storage-kérelemben](encryption-customer-provided-keys.md)
- [Inaktív adatok Azure Storage-titkosítása](../common/storage-service-encryption.md)
