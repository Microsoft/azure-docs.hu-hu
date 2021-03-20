---
title: Tárolt hozzáférési szabályzat létrehozása a .NET-tel
titleSuffix: Azure Storage
description: Az Azure Storage és a .NET használatával hozzon létre egy tárolt hozzáférési szabályzatot. A kiszolgálón a szolgáltatás szintű közös hozzáférésű aláírások további szintjének szabályozása.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cd6147fbe38710bcefd580e71be1d6f5d446a21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89010749"
---
# <a name="create-a-stored-access-policy-with-net"></a>Tárolt hozzáférési szabályzat létrehozása a .NET-tel

A tárolt hozzáférési szabályzatok a kiszolgálóoldali megosztott hozzáférési aláírások (SAS) további szintjét biztosítják. A tárolt hozzáférési szabályzatok meghatározása a közös hozzáférési aláírások csoportosítására szolgál, és további korlátozásokat biztosít a szabályzat által kötött közös hozzáférési aláírásokhoz. A tárolt hozzáférési szabályzatok segítségével megváltoztathatja az SAS kezdési idejét, lejárati idejét vagy engedélyeit, illetve visszavonhatja azt a kiállítása után.
  
A következő Azure Storage-erőforrások támogatják a tárolt hozzáférési szabályzatokat:  
  
- Blobtárolók  
- Fájlmegosztások  
- Üzenetsorok  
- Táblák  
  
> [!NOTE]
> Egy tároló tárolt hozzáférési szabályzata társítható egy közös hozzáférési aláírással, amely engedélyeket biztosít magának a tárolónak vagy a benne található bloboknak. Hasonlóképpen, egy fájlmegosztás tárolt hozzáférési szabályzata társítható egy közös hozzáférési aláírással, amely jogosultságokat biztosít a megosztáshoz vagy a benne található fájlokhoz.  
>
> A tárolt hozzáférési szabályzatok csak a Service SAS esetében támogatottak. A tárolt hozzáférési szabályzatok nem támogatottak a fiók SAS vagy a felhasználói delegálás SAS esetében.  

További információ a tárolt hozzáférési házirendekről: [tárolt hozzáférési szabályzat definiálása](/rest/api/storageservices/define-stored-access-policy).

## <a name="create-a-stored-access-policy"></a>Tárolt hozzáférési szabályzat létrehozása

A tárolt hozzáférési szabályzat létrehozásához használt mögöttes REST művelet a [tároló ACL-t állítja be](/rest/api/storageservices/set-container-acl). A kapcsolati karakterláncban a fiók hozzáférési kulcsainak használatával engedélyeznie kell a műveletet, hogy a megosztott kulcson keresztül hozzon létre egy tárolt hozzáférési szabályzatot. A **Container ACL** -művelet Azure ad-beli hitelesítő adatokkal való megadása nem támogatott. További információ: [a blob-és üzenetsor-adatműveletek meghívására vonatkozó engedélyek](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

A következő kódrészletek például egy tárolt hozzáférési szabályzatot hoznak létre egy tárolón. A hozzáférési házirend segítségével megadhatja a tárolón vagy annak blobján lévő szolgáltatás SAS-korlátozásait.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Ha tárolt hozzáférési szabályzatot szeretne létrehozni az Azure Storage-hoz készült .NET ügyféloldali kódtár 12-es verziójával rendelkező tárolón, hívja a következő módszerek egyikét:

- [BlobContainerClient.SetAccessPolicy](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicy)
- [BlobContainerClient.SetAccessPolicyAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setaccesspolicyasync)

A következő példa egy olyan tárolt hozzáférési szabályzatot hoz létre, amely egy napra érvényes, és amely olvasási/írási jogosultságot biztosít:

```csharp
async static Task CreateStoredAccessPolicyAsync(string containerName)
{
    string connectionString = "";

    // Use the connection string to authorize the operation to create the access policy.
    // Azure AD does not support the Set Container ACL operation that creates the policy.
    BlobContainerClient containerClient = new BlobContainerClient(connectionString, containerName);

    try
    {
        await containerClient.CreateIfNotExistsAsync();

        // Create one or more stored access policies.
        List<BlobSignedIdentifier> signedIdentifiers = new List<BlobSignedIdentifier>
        {
            new BlobSignedIdentifier
            {
                Id = "mysignedidentifier",
                AccessPolicy = new BlobAccessPolicy
                {
                    StartsOn = DateTimeOffset.UtcNow.AddHours(-1),
                    ExpiresOn = DateTimeOffset.UtcNow.AddDays(1),
                    Permissions = "rw"
                }
            }
        };
        // Set the container's access policy.
        await containerClient.SetAccessPolicyAsync(permissions: signedIdentifiers);
    }
    catch (RequestFailedException e)
    {
        Console.WriteLine(e.ErrorCode);
        Console.WriteLine(e.Message);
    }
    finally
    {
        await containerClient.DeleteAsync();
    }
}
```

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

Ha tárolt hozzáférési szabályzatot szeretne létrehozni az Azure Storage-hoz készült .NET ügyféloldali kódtár 12-es verziójával rendelkező tárolón, hívja a következő módszerek egyikét:

- [CloudBlobContainer. SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [CloudBlobContainer. SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

A következő példa egy olyan tárolt hozzáférési szabályzatot hoz létre, amely egy napra érvényes, és amely olvasási, írási és listázási engedélyeket biztosít:

```csharp
private static async Task CreateStoredAccessPolicyAsync(CloudBlobContainer container, string policyName)
{
    // Create a new stored access policy and define its constraints.
    // The access policy provides create, write, read, list, and delete permissions.
    SharedAccessBlobPolicy sharedPolicy = new SharedAccessBlobPolicy()
    {
        // When the start time for the SAS is omitted, the start time is assumed to be the time when Azure Storage receives the request.
        SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
        Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List |
            SharedAccessBlobPermissions.Write
    };

    // Get the container's existing permissions.
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();

    // Add the new policy to the container's permissions, and set the container's permissions.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    await container.SetPermissionsAsync(permissions);
}
```

---

## <a name="see-also"></a>Lásd még

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](storage-sas-overview.md)
- [Tárolt hozzáférési szabályzat meghatározása](/rest/api/storageservices/define-stored-access-policy)
- [Configure Azure Storage connection strings (Az Azure Storage kapcsolati sztringek konfigurálása)](storage-configure-connection-string.md)
