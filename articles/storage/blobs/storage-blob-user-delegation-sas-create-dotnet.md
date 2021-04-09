---
title: Felhasználói delegálási SAS létrehozása egy tárolóhoz, könyvtárhoz vagy blobhoz a .NET használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre Azure Active Directory hitelesítő adatokkal rendelkező felhasználói delegálási SAS-t az Azure Storage-hoz készült .NET ügyféloldali kódtár használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/03/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 13491735f73cb1696f3c36f3434cc781a1e2b739
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526805"
---
# <a name="create-a-user-delegation-sas-for-a-container-directory-or-blob-with-net"></a>Felhasználói delegálási SAS létrehozása tárolóhoz, címtárhoz vagy blobhoz .NET-tel

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan használhatók a Azure Active Directory (Azure AD) hitelesítő adatok egy felhasználói delegálási SAS létrehozásához egy tárolóhoz, könyvtárhoz vagy blobhoz a .NET-es verzióhoz készült Azure Storage ügyféloldali kódtár használatával.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="assign-azure-roles-for-access-to-data"></a>Azure-szerepkörök kiosztása az adathoz való hozzáféréshez

Amikor egy Azure AD rendszerbiztonsági tag megpróbál hozzáférni a blob-adatforráshoz, a rendszerbiztonsági tag engedélyekkel kell rendelkeznie az erőforráshoz. Azt jelzi, hogy a rendszerbiztonsági tag felügyelt identitás-e az Azure-ban vagy egy olyan Azure AD-felhasználói fiókban, amely kódot futtat a fejlesztési környezetben, a rendszerbiztonsági tag számára olyan Azure-szerepkört kell hozzárendelni, amely hozzáférést biztosít az Azure Storage-beli blob Az engedélyek Azure RBAC való hozzárendelésével kapcsolatos információkért tekintse meg az Azure [-blobok és-várólisták hozzáférésének engedélyezése az Azure-beli blobokhoz és-várólistákhoz Azure Active Directory használatával](../common/storage-auth-aad.md#assign-azure-roles-for-access-rights)című szakaszt. 

[!INCLUDE [storage-install-packages-blob-and-identity-include](../../../includes/storage-install-packages-blob-and-identity-include.md)]

Ha többet szeretne megtudni arról, hogyan lehet hitelesíteni az Azure Identity ügyféloldali függvénytárát az Azure Storage-ból, tekintse meg a **hitelesítés az Azure Identity Library** használatával című szakaszt a [blobokhoz és várólistákhoz való hozzáférés engedélyezése a Azure Active Directory és felügyelt identitások Azure-erőforrásokhoz](../common/storage-auth-aad-msi.md?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library)című részben.

## <a name="get-an-authenticated-token-credential"></a>Hitelesített jogkivonat hitelesítő adatainak beolvasása

Ha meg szeretné kapni a jogkivonat hitelesítő adatait, amelyet a kód használhat az Azure Storage-ba irányuló kérések engedélyezéséhez, hozza létre a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) osztály egy példányát.

A következő kódrészlet bemutatja, hogyan kérheti le a hitelesített jogkivonat hitelesítő adatait, és hogyan hozhat létre szolgáltatási ügyfelet a blob Storage-hoz:

```csharp
// Construct the blob endpoint from the account name.
string blobEndpoint = string.Format("https://{0}.blob.core.windows.net", accountName);

// Create a new Blob service client with Azure AD credentials.
BlobServiceClient blobClient = new BlobServiceClient(new Uri(blobEndpoint),
                                                     new DefaultAzureCredential());
```

## <a name="get-the-user-delegation-key"></a>Felhasználói delegálási kulcs beszerzése

Minden SAS egy kulccsal van aláírva. Felhasználói delegálási SAS létrehozásához először egy felhasználói delegálási kulcsot kell igényelnie, amelyet a rendszer az SAS aláírására használ. A felhasználói delegálási kulcs hasonlít a szolgáltatás SAS-vagy fiók-SAS-aláírásához használt fiók kulcsához, azzal a különbséggel, hogy az Azure AD-beli hitelesítő adataira támaszkodik. Amikor egy ügyfél egy OAuth 2,0-token használatával kér felhasználói delegálási kulcsot, az Azure Storage a felhasználó nevében visszaadja a felhasználói delegálási kulcsot.

A felhasználó delegálási kulcsának megadásával a kulcs használatával tetszőleges számú felhasználói delegálási közös hozzáférési aláírás hozható létre. A felhasználói delegálási kulcs független a beszerzéséhez használt OAuth 2,0 tokentől, így a tokent nem kell megújítani, amíg a kulcs még érvényes. Azt is megadhatja, hogy a kulcs legfeljebb 7 napig érvényes legyen.

A következő módszerek egyikével kérheti le a felhasználói delegálási kulcsot:

- [GetUserDelegationKey](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkey)
- [GetUserDelegationKeyAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobclient.getuserdelegationkeyasync)

A következő kódrészlet lekéri a felhasználói delegálási kulcsot, és kiírja a tulajdonságait:

```csharp
// Get a user delegation key for the Blob service that's valid for seven days.
// You can use the key to generate any number of shared access signatures over the lifetime of the key.
UserDelegationKey key = await blobClient.GetUserDelegationKeyAsync(DateTimeOffset.UtcNow,
                                                                   DateTimeOffset.UtcNow.AddDays(7));

// Read the key's properties.
Console.WriteLine("User delegation key properties:");
Console.WriteLine("Key signed start: {0}", key.SignedStartsOn);
Console.WriteLine("Key signed expiry: {0}", key.SignedExpiresOn);
Console.WriteLine("Key signed object ID: {0}", key.SignedObjectId);
Console.WriteLine("Key signed tenant ID: {0}", key.SignedTenantId);
Console.WriteLine("Key signed service: {0}", key.SignedService);
Console.WriteLine("Key signed version: {0}", key.SignedVersion);
```

## <a name="get-a-user-delegation-sas-for-a-blob"></a>Felhasználói delegálási SAS beszerzése egy blobhoz

A következő kódrészlet a rendszerbiztonsági tag hitelesítésének teljes kódját és a felhasználói delegálási SAS létrehozását mutatja be egy blobhoz:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasBlob":::

A következő példa egy szimulált ügyfélalkalmazás által az előző példában létrehozott felhasználói delegálási SAS tesztelését teszteli. Ha az SAS érvényes, az ügyfélalkalmazás el tudja olvasni a blob tartalmát. Ha az SAS érvénytelen, például ha lejárt, az Azure Storage a 403-as hibakódot (tiltott) adja vissza.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ReadBlobWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-container"></a>Felhasználói delegálási SAS beszerzése egy tárolóhoz

A következő mintakód bemutatja, hogyan lehet egy tárolóhoz felhasználói delegálási SAS-t előállítani:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasContainer":::

A következő példa egy szimulált ügyfélalkalmazás által az előző példában létrehozott felhasználói delegálási SAS tesztelését teszteli. Ha az SAS érvényes, az ügyfélalkalmazás el tudja olvasni a blob tartalmát. Ha az SAS érvénytelen, például ha lejárt, az Azure Storage a 403-as hibakódot (tiltott) adja vissza.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListBlobsWithSasAsync":::

## <a name="get-a-user-delegation-sas-for-a-directory"></a>Felhasználói delegálási SAS beszerzése egy címtárhoz

A következő mintakód bemutatja, hogyan hozhatja ki a címtárhoz egy felhasználói delegálási SAS-t, ha a Storage-fiókhoz engedélyezve van egy hierarchikus névtér:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_GetUserDelegationSasDirectory":::

A következő példa egy szimulált ügyfélalkalmazás által az előző példában létrehozott felhasználói delegálási SAS tesztelését teszteli. Ha az SAS érvényes, az ügyfélalkalmazás listázhatja a könyvtár elérési útját. Ha az SAS érvénytelen, például ha lejárt, az Azure Storage a 403-as hibakódot (tiltott) adja vissza.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Sas.cs" id="Snippet_ListFilePathsWithDirectorySasAsync":::

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>Lásd még

- [Korlátozott hozzáférés biztosítása az Azure Storage-erőforrásokhoz közös hozzáférésű aláírások (SAS) használatával](../common/storage-sas-overview.md)
- [Felhasználói delegálási kulcs műveletének beolvasása](/rest/api/storageservices/get-user-delegation-key)
- [Felhasználói delegálási SAS létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas)
