---
title: 'Gyors útmutató: Azure Blob Storage Library v12-.NET'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használható a .NET-hez készült Azure Blob Storage Client Library 12-es verziója a tárolók és Blobok blob (Object) tárolóban történő létrehozásához. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/03/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 82dcfa0942f21f01424c4744aa0b23f4672a135f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105106717"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Gyors útmutató: Azure Blob Storage Client Library V12 a .NET-hez

Ismerkedés az Azure Blob Storage a .NET-hez készült ügyféloldali kódtáraval. Az Azure Blob Storage a Microsoft objektum-tárolási megoldás a felhőhöz. Kövesse a csomag telepítésének lépéseit, és próbálja ki például az alapszintű feladatokhoz tartozó kódot. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

Az Azure Blob Storage a .NET-hez készült ügyféloldali kódtár a következőhöz használható:

* Tároló létrehozása
* Blob feltöltése az Azure Storage-ba
* Egy tároló összes blobjának listázása
* A blob letöltése a helyi számítógépre
* Tároló törlése

További források:

* [API-referenciadokumentáció](/dotnet/api/azure.storage.blobs)
* [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Példák](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* Azure Storage-fiók – [Storage-fiók létrehozása](../common/storage-account-create.md)
* Az operációs rendszer jelenlegi [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core) . Győződjön meg arról, hogy az SDK-t és nem a futtatókörnyezetet kapja meg.

## <a name="setting-up"></a>Beállítás

Ez a szakasz végigvezeti a projekt előkészítésének folyamatán az Azure Blob Storage .NET-hez készült ügyféloldali kódtár használatával.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *BlobQuickstartV12* nevű .net Core-alkalmazást.

1. A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, *BlobQuickstartV12* nevű Console-alkalmazást. Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl: *program. cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Váltson az újonnan létrehozott *BlobQuickstartV12* -könyvtárra.

   ```console
   cd BlobQuickstartV12
   ```

1. A *BlobQuickstartV12* könyvtárban hozzon létre egy másik könyvtárat, amely az *adat* nevű. A blob-adatfájlokat a rendszer létrehozza és tárolja.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>A csomag telepítése

Noha még mindig az alkalmazás könyvtárában van, telepítse az Azure Blob Storage .NET-csomaghoz készült ügyféloldali kódtárat a `dotnet add package` paranccsal.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Nyissa meg a *program. cs* fájlt a szerkesztőben.
1. Távolítsa el az `Console.WriteLine("Hello World!");` utasítást.
1. `using`Irányelvek hozzáadása.
1. Frissítse a `Main` metódus deklarációját az aszinkron támogatásához.

    A kód a következő:

    :::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/app_framework.cs":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A blob Storage háromféle típusú erőforrást kínál:

* A Storage-fiók
* Egy tároló a Storage-fiókban
* A tárolóban lévő blob

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![BLOB Storage-architektúra ábrája](./media/storage-blobs-introduction/blob1.png)

Használja az alábbi .NET-osztályokat a következő erőforrásokkal való interakcióhoz:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): a `BlobServiceClient` osztály lehetővé teszi az Azure Storage-erőforrások és a blob-tárolók kezelését.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): a `BlobContainerClient` osztály lehetővé teszi az Azure Storage-tárolók és a Blobok kezelését.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): a `BlobClient` osztály lehetővé teszi az Azure Storage-Blobok kezelését.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): az `BlobDownloadInfo` osztály a blob letöltésekor visszaadott tulajdonságokat és tartalmat jelöli.

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan hajthatja végre a következőket az Azure Blob Storage .NET-hez készült ügyféloldali kódtár használatával:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [Tárolóban lévő blobok kilistázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód a Storage- [kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból kéri le a Storage-fiókhoz tartozó kapcsolatok karakterláncát.

Adja hozzá ezt a kódot a `Main` metódushoz:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód egy GUID értéket fűz hozzá a tároló nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Hozza létre a [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) osztály egy példányát. Ezután hívja meg a [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) metódust a tároló létrehozásához a Storage-fiókban.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet:

1. Létrehoz egy szövegfájlt *a helyi* adatkönyvtárban.
1. Beolvas egy [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) objektumra mutató hivatkozást úgy, hogy meghívja a [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) metódust a tárolóban a [tároló létrehozása](#create-a-container) szakaszban.
1. A [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) metódus meghívásával feltölti a helyi szövegfájlt a blobba. Ez a metódus létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

A tárolóban lévő Blobok listázása a [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) metódus meghívásával. Ebben az esetben a tárolóhoz csak egy blob lett hozzáadva, így a listázási művelet csak ezt az egy blobot adja vissza.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) metódus meghívásával. A példában szereplő kód a "letöltött" utótagot adja hozzá a fájl nevéhez, hogy mindkét fájl látható legyen a helyi fájlrendszerben.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Tároló törlése

A következő kód megtisztítja az alkalmazás által létrehozott erőforrásokat a teljes tároló törlésével a [DeleteAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync)használatával. Emellett törli az alkalmazás által létrehozott helyi fájlokat is.

Az alkalmazás a felhasználói bevitel szüneteltetését hívja `Console.ReadLine` meg, mielőtt törli a blobot, a tárolót és a helyi fájlokat. Ez jó eséllyel ellenőrizhető, hogy az erőforrások valóban helyesen lettek-e létrehozva a Törlésük előtt.

Adja hozzá ezt a kódot a metódus végéhez `Main` :

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy tesztoldalt *a helyi* Adatmappában, és feltölti azt a blob Storage-ba. A példa ezután felsorolja a tárolóban lévő blobokat, és letölti a fájlt egy új névvel, hogy össze lehessen hasonlítani a régi és az új fájlokat.

Navigáljon az alkalmazás könyvtárába, majd hozza létre és futtassa az alkalmazást.

```console
dotnet build
```

```console
dotnet run
```

Az alkalmazás kimenete az alábbi példához hasonló:

```output
Azure Blob Storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

A tisztítási folyamat megkezdése előtt tekintse *meg a két fájl adatmappáját* . Ha megnyitja őket, láthatja, hogy megegyeznek.

A fájlok ellenőrzése után nyomja le az **ENTER** billentyűt a tesztoldal törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megismerkedett a blobok a .NET használatával való fel- és letöltésével, valamint kilistázásával.

A blob Storage-beli minták alkalmazásainak megtekintéséhez folytassa a következőket:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK v12 .NET-minták](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Az oktatóanyagok, minták, gyors üzembe helyezés és egyéb dokumentációk az [Azure-ban .net-és .net Core-fejlesztőknek](/dotnet/azure/)készültek.
* A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://www.microsoft.com/net/learn/get-started/) ismertető szakaszt.
