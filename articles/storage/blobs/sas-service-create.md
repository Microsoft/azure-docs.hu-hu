---
title: Szolgáltatás SAS létrehozása tárolóhoz vagy blobhoz
titleSuffix: Azure Storage
description: Ismerje meg, hogyan hozhat létre megosztott hozzáférési aláírást egy tárolóhoz vagy blobhoz az Azure Blob Storage ügyféloldali kódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/23/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 7bbe19de666fb167297de89e85bf302186a9145e
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024880"
---
# <a name="create-a-service-sas-for-a-container-or-blob"></a>Szolgáltatás SAS létrehozása tárolóhoz vagy blobhoz

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan használhatja a Storage-fiók kulcsát egy olyan tárolóhoz vagy blobhoz készült Service SAS létrehozásához, amely a Blob Storage Azure Storage ügyféloldali kódtára.

## <a name="create-a-service-sas-for-a-blob-container"></a>Service SAS létrehozása blob-tárolóhoz

A következő mintakód egy tárolóhoz hoz létre SAS-t. Ha egy meglévő tárolt hozzáférési szabályzat neve van megadva, akkor ez a házirend a SAS-hoz van társítva. Ha nincs megadva tárolt hozzáférési szabályzat, a kód létrehoz egy ad hoc SAS-t a tárolón.

### <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

A Service SAS a fiók hozzáférési kulcsával van aláírva. A [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) osztály használatával hozza létre az SAS aláírásához használt hitelesítő adatokat. Ezután hozzon létre egy új [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) objektumot, és hívja meg a [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) az SAS-jogkivonat karakterláncának beolvasásához.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForContainer":::

### <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

Az [CloudBlobContainer. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getsharedaccesssignature) metódust a tárolóhoz tartozó szolgáltatás sas létrehozásához hívja meg.

```csharp
private static string GetContainerSasUri(CloudBlobContainer container, 
                                         string storedPolicyName = null)
{
    string sasContainerToken;

    // If no stored policy is specified, create a new access policy and define its constraints.
    if (storedPolicyName == null)
    {
        // Note that the SharedAccessBlobPolicy class is used both to define
        // the parameters of an ad hoc SAS, and to construct a shared access policy
        // that is saved to the container's shared access policies.
        SharedAccessBlobPolicy adHocPolicy = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed
            // to be the time when the storage service receives the request. Omitting
            // the start time for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List
        };

        // Generate the shared access signature on the container,
        // setting the constraints directly on the signature.
        sasContainerToken = container.GetSharedAccessSignature(adHocPolicy, null);

        Console.WriteLine("SAS for blob container (ad hoc): {0}", sasContainerToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the container. In this case,
        // all of the constraints for the shared access signature are specified
        // on the stored access policy, which is provided by name. It is also possible
        // to specify some constraints on an ad hoc SAS and others on the stored access policy.
        sasContainerToken = container.GetSharedAccessSignature(null, storedPolicyName);

        Console.WriteLine("SAS for container (stored access policy): {0}", sasContainerToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return container.Uri + sasContainerToken;
}
```

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

A Service SAS a fiók hozzáférési kulcsával van aláírva. A [StorageSharedKeyCredential](/javascript/api/@azure/storage-blob/storagesharedkeycredential) osztály használatával hozza létre az SAS aláírásához használt hitelesítő adatokat. Ezután hívja meg a [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) függvényt, amely megadja a szükséges paramétereket az SAS-jogkivonat karakterláncának beolvasásához.

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_ContainerSAS":::

---

## <a name="create-a-service-sas-for-a-blob"></a>Szolgáltatás SAS létrehozása blobhoz

A következő kódrészlet egy blobon létrehoz egy SAS-t. Ha egy meglévő tárolt hozzáférési szabályzat neve van megadva, akkor ez a házirend a SAS-hoz van társítva. Ha nincs megadva tárolt hozzáférési szabályzat, a kód létrehoz egy ad hoc SAS-t a blobon.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

A Service SAS a fiók hozzáférési kulcsával van aláírva. A [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) osztály használatával hozza létre az SAS aláírásához használt hitelesítő adatokat. Ezután hozzon létre egy új [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) objektumot, és hívja meg a [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) az SAS-jogkivonat karakterláncának beolvasásához.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForBlob":::

# <a name="net-v11"></a>[\.NETTÓ v11](#tab/dotnetv11)

Az [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) metódust a blobhoz tartozó Service sas létrehozásához hívja meg.

```csharp
private static string GetBlobSasUri(CloudBlobContainer container,
                                    string blobName,
                                    string policyName = null)
{
    string sasBlobToken;

    // Get a reference to a blob within the container.
    // Note that the blob may not exist yet, but a SAS can still be created for it.
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    if (policyName == null)
    {
        // Create a new access policy and define its constraints.
        // Note that the SharedAccessBlobPolicy class is used both to define the parameters
        // of an ad hoc SAS, and to construct a shared access policy that is saved to
        // the container's shared access policies.
        SharedAccessBlobPolicy adHocSAS = new SharedAccessBlobPolicy()
        {
            // When the start time for the SAS is omitted, the start time is assumed to be
            // the time when the storage service receives the request. Omitting the start time
            // for a SAS that is effective immediately helps to avoid clock skew.
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessBlobPermissions.Read |
                          SharedAccessBlobPermissions.Write |
                          SharedAccessBlobPermissions.Create
        };

        // Generate the shared access signature on the blob,
        // setting the constraints directly on the signature.
        sasBlobToken = blob.GetSharedAccessSignature(adHocSAS);

        Console.WriteLine("SAS for blob (ad hoc): {0}", sasBlobToken);
        Console.WriteLine();
    }
    else
    {
        // Generate the shared access signature on the blob. In this case, all of the constraints
        // for the SAS are specified on the container's stored access policy.
        sasBlobToken = blob.GetSharedAccessSignature(null, policyName);

        Console.WriteLine("SAS for blob (stored access policy): {0}", sasBlobToken);
        Console.WriteLine();
    }

    // Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Az [CloudBlob. GetSharedAccessSignature](/dotnet/api/microsoft.azure.storage.blob.cloudblob.getsharedaccesssignature) metódust a blobhoz tartozó Service sas létrehozásához hívja meg.

Ha a blobhoz szolgáltatást SAS-t szeretne létrehozni, hívja meg a szükséges paramétereket biztosító [generateBlobSASQueryParameters](/javascript/api/@azure/storage-blob/#generateBlobSASQueryParameters_BlobSASSignatureValues__StorageSharedKeyCredential_) függvényt.

:::code language="javascript" source="~/azure-storage-snippets/blobs/howto/JavaScript/NodeJS-v12/SAS.js" id="Snippet_BlobSAS":::

---

## <a name="create-a-service-sas-for-a-directory"></a>Service SAS létrehozása egy címtárhoz

Egy olyan Storage-fiókban, amelyen engedélyezve van a hierarchikus névtér, létrehozhat egy Service SAS-t a címtárhoz. A szolgáltatás SAS létrehozásához ellenőrizze, hogy telepítette-e az [Azure. Storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) csomag 12.5.0 vagy újabb verzióját.

Az alábbi példa bemutatja, hogyan hozhat létre egy Service SAS-t egy olyan címtárhoz, amely a V12-es .NET-ügyfél kódtára:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetServiceSasUriForDirectory":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

[!INCLUDE [storage-blob-javascript-resources-include](../../../includes/storage-blob-javascript-resources-include.md)]

## <a name="next-steps"></a>Következő lépések

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md)
- [Szolgáltatási SAS létrehozása](/rest/api/storageservices/create-service-sas)
