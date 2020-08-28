---
title: Nyilvános tárolók és Blobok elérése névtelenül a .NET-tel
titleSuffix: Azure Storage
description: A .NET-hez készült Azure Storage ügyféloldali kódtára a nyilvános tárolók és Blobok névtelen elérésére használható.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-csharp
ms.openlocfilehash: 1ba36c7128f72f5181f3fed53f5615ab464e3325
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "89013537"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>Nyilvános tárolók és Blobok elérése névtelenül a .NET-tel

Az Azure Storage támogatja a tárolók és a Blobok opcionális nyilvános olvasási hozzáférését. Az ügyfelek névtelenül érhetik el a nyilvános tárolókat és a blobokat az Azure Storage ügyféloldali kódtárai használatával, valamint az Azure Storage szolgáltatáshoz való adatelérést támogató egyéb eszközökkel és segédprogramokkal.

Ez a cikk bemutatja, hogyan érheti el egy nyilvános tárolót vagy blobot a .NET-ről. A névtelen olvasási hozzáférés tárolón való konfigurálásával kapcsolatos információkért lásd: [Névtelen nyilvános olvasási hozzáférés beállítása tárolók és Blobok](anonymous-read-access-configure.md)számára. A Storage-fiókokhoz való névtelen hozzáférés megakadályozásával kapcsolatos információkért lásd: a [Névtelen nyilvános olvasási hozzáférés megakadályozása a tárolók és a Blobok](anonymous-read-access-prevent.md)számára.

A tárolókat és blobokat elérő ügyfelek névtelenül használhatják a hitelesítő adatokat nem igénylő konstruktorokat. Az alábbi példák a tárolók és Blobok névtelen módon történő hivatkozásának néhány különböző módját mutatják be.

## <a name="create-an-anonymous-client-object"></a>Névtelen ügyfél-objektum létrehozása

A névtelen hozzáféréshez létrehozhat egy új szolgáltatás-ügyféloldali objektumot, ha megadja a fiókhoz tartozó blob Storage-végpontot. Azonban ismernie kell egy olyan tároló nevét is a fiókban, amely elérhető a névtelen hozzáféréshez.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

---

## <a name="reference-a-container-anonymously"></a>Tároló hivatkozása névtelenül

Ha egy névtelenül elérhető tároló URL-címe van, akkor használhatja a tároló közvetlen hivatkozására.

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

---

## <a name="reference-a-blob-anonymously"></a>BLOB hivatkozása névtelenül

Ha rendelkezik egy névtelen hozzáféréshez elérhető blob URL-címével, akkor közvetlenül a következő URL-cím használatával hivatkozhat a blobra:

# <a name="net-v12-sdk"></a>[\.NET V12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>Következő lépések

- [Névtelen nyilvános olvasási hozzáférés konfigurálása a tárolók és a Blobok számára](anonymous-read-access-configure.md)
- [Tárolók és Blobok névtelen nyilvános olvasási hozzáférésének tiltása](anonymous-read-access-prevent.md)
- [Hozzáférés engedélyezése az Azure Storage-hoz](../common/storage-auth.md)
