---
title: A .NET használata a Azure Data Lake Storage Gen2ban tárolt adatkezeléshez
description: A .NET-hez készült Azure Storage ügyféloldali kódtára segítségével kezelheti az olyan Storage-fiókokban lévő címtárakat és fájlokat, amelyeken engedélyezve van a hierarchikus névtér.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 52e993a22a512a94c8b5b8b050205db0c4ce0b1b
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650320"
---
# <a name="use-net-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>A .NET használata könyvtárak és fájlok kezelésére Azure Data Lake Storage Gen2

Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet a .NET használatával olyan könyvtárakat és fájlokat, amelyek hierarchikus névteret tartalmazó Storage-fiókokban vannak.

A címtárak és fájlok hozzáférés-vezérlési listái (ACL) beszerzésével, beállításával és frissítésével kapcsolatos további információkért lásd: a [.net használata az ACL-ek kezeléséhez Azure Data Lake Storage Gen2](data-lake-storage-acl-dotnet.md).

[Csomag (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Példák](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [API-referenciák](/dotnet/api/azure.storage.files.datalake)  |  [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér. Az [alábbi](create-data-lake-storage-account.md) útmutatást követve hozzon létre egyet.

## <a name="set-up-your-project"></a>A projekt beállítása

Első lépésként telepítse az [Azure. Storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet csomagot.

A NuGet-csomagok telepítésével kapcsolatos további információkért lásd: [csomagok telepítése és kezelése a Visual Studióban a NuGet csomagkezelő használatával](/nuget/consume-packages/install-use-packages-visual-studio).

Ezután adja hozzá ezeket az utasításokat a programkód elejéhez.

```csharp
using Azure;
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;

```

## <a name="connect-to-the-account"></a>Kapcsolódás a fiókhoz

A cikkben szereplő kódrészletek használatához létre kell hoznia egy [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -példányt, amely a Storage-fiókot jelképezi. 

### <a name="connect-by-using-an-account-key"></a>Csatlakozási fiók kulcsa alapján

Ez a legegyszerűbb módszer a fiókhoz való kapcsolódásra. 

Ez a példa egy [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -példányt hoz létre a fiók kulcsa alapján.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Kapcsolat Azure Active Directory (Azure AD) használatával

A [.net-hez készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) használatával hitelesítheti alkalmazását az Azure ad-vel.

Ez a példa egy [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -példányt hoz létre egy ügyfél-azonosítóval, egy ügyfél-titkos kulccsal és egy BÉRLŐi azonosítóval.  Az értékek [lekéréséhez lásd: token beszerzése az Azure ad-ből az ügyfélalkalmazások kéréseinek engedélyezéséhez](../common/storage-auth-aad-app.md).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> További példákért tekintse meg a [.net-hez készült Azure Identity ügyféloldali kódtár](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) dokumentációját.

## <a name="create-a-container"></a>Tároló létrehozása

A tároló fájlrendszerként működik a fájlok számára. A [DataLakeServiceClient. CreateFileSystem](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) metódus meghívásával létrehozhat egyet.

Ez a példa egy nevű tárolót hoz létre `my-file-system` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Könyvtár létrehozása

Hozzon létre egy címtár-referenciát a [DataLakeFileSystemClient. CreateDirectoryAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) metódus meghívásával.

Ez a példa egy nevű könyvtárat helyez `my-directory` el egy tárolóhoz, majd hozzáadja a nevű alkönyvtárat `my-subdirectory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Címtár átnevezése vagy áthelyezése

Nevezze át vagy helyezze át a könyvtárat úgy, hogy meghívja a [DataLakeDirectoryClient. RenameAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) metódust. Adja meg a kívánt könyvtár elérési útját (a paramétert). 

Ez a példa átnevez egy alkönyvtárat a névre `my-subdirectory-renamed` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_RenameDirectory":::

Ez a példa egy nevű könyvtárat helyez át egy nevű `my-subdirectory-renamed` könyvtár alkönyvtárába `my-directory-2` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_MoveDirectory":::

## <a name="delete-a-directory"></a>Könyvtár törlése

A [DataLakeDirectoryClient. Delete](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) metódus meghívásával törölhet egy könyvtárat.

Ez a példa törli a nevű könyvtárat `my-directory` .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Fájl feltöltése könyvtárba

Először hozzon létre egy fájlt a célhelyen a [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) osztály egy példányának létrehozásával. Töltsön fel egy fájlt a [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) metódus meghívásával. Ügyeljen arra, hogy a feltöltést a [DataLakeFileClient. FlushAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) metódus meghívásával végezze el.

Ez a példa egy szövegfájlt tölt fel egy nevű könyvtárba `my-directory` . 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFile":::

> [!TIP]
> Ha a fájl mérete nagy, a kódnak több hívást is el kell végeznie a [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Használja helyette a [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) metódust. Így feltöltheti a teljes fájlt egyetlen hívással.
>
> Tekintse meg a következő szakaszt egy példához.

## <a name="upload-a-large-file-to-a-directory"></a>Nagyméretű fájl feltöltése egy könyvtárba

A [DataLakeFileClient. UploadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) metódus használatával nagyméretű fájlokat tölthet fel anélkül, hogy több hívást kellene tennie a [DataLakeFileClient. AppendAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) metódushoz.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Letöltés egy címtárból 

Először hozzon létre egy [DataLakeFileClient](/dotnet/api/azure.storage.files.datalake.datalakefileclient) -példányt, amely a letölteni kívánt fájlt jelöli. Használja a [DataLakeFileClient. ReadAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync)  metódust, és elemezze a visszatérési értéket egy [stream](/dotnet/api/system.io.stream) -objektum beszerzéséhez. A .NET-fájlok feldolgozására szolgáló API-k használatával az adatfolyamból fájlba mentheti a bájtokat. 

Ez a példa egy [BinaryReader](/dotnet/api/system.io.binaryreader) és egy [FileStream](/dotnet/api/system.io.filestream) használ a bájtok fájlba mentéséhez. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_DownloadBinaryFromDirectory":::

## <a name="list-directory-contents"></a>Könyvtár tartalmának listázása

A könyvtár tartalmának listázásához hívja meg a [FileSystemClient. GetPathsAsync](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) metódust, majd az eredmények alapján sorolja fel azokat.

Ez a példa kinyomtatja a nevű könyvtárban található egyes fájlok nevét `my-directory` .

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD_DataLake.cs" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Lásd még

- [API-referenciadokumentáció](/dotnet/api/azure.storage.files.datalake)
- [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Példák](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Gen1 a Gen2-megfeleltetéshez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Ismert problémák](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Visszajelzés küldése](https://github.com/Azure/azure-sdk-for-net/issues)