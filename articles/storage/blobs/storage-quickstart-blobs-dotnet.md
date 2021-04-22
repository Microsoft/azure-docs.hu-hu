---
title: 'Rövid útmutató: Azure Blob Storage kódtár v12 - .NET'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure Blob Storage .NET-hez használható ügyféloldali kódtár 12-es verzióját egy tároló és egy blob létrehozásához a Blob (objektum) tárolóban. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: twooley
ms.author: twooley
ms.date: 03/03/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: f913b33d0bea425a24d2fd336c9d065978606e82
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869256"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>Rövid útmutató: Azure Blob Storage .NET-hez való v12-es ügyféloldali kódtár létrehozása

A .NET-hez Azure Blob Storage ügyféloldali kódtár v12 első lépések. Azure Blob Storage a Microsoft felhőalapú objektumtárolási megoldása. Kövesse a csomag telepítésének lépéseit, és próbálja ki az alapszintű feladatokhoz szükséges példakódot. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

Használja a Azure Blob Storage .NET-hez való 12-es ügyfélszámítógép-kódtárát a következőre:

* Tároló létrehozása
* Blob feltöltése az Azure Storage-ba
* A tárolóban lévő összes blob felsorolása
* A blob letöltése a helyi számítógépre
* Tároló törlése

További források:

* [API-referenciadokumentáció](/dotnet/api/azure.storage.blobs)
* [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Package (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Példák](../common/storage-samples-dotnet.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés [– hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/)
* Azure Storage-fiók [– tárfiók létrehozása](../common/storage-account-create.md)
* Aktuális [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) az operációs rendszer számára. Ne a futásidőt, csak az SDK-t szerezze be.

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti egy projekt előkészítésén a .NET-hez készült Azure Blob Storage ügyféloldali kódtárával való munkához.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *BlobQuickstartV12* nevű .NET Core-alkalmazást.

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) az paranccsal hozzon létre egy új konzolalkalmazást `dotnet new` *BlobQuickstartV12 néven.* Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájllal: *Program.cs*.

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. Váltson az újonnan létrehozott *BlobQuickstartV12 könyvtárra.*

   ```console
   cd BlobQuickstartV12
   ```

1. A *BlobQuickstartV12* könyvtár mellett hozzon létre egy másik könyvtárat *data néven.* Itt lesznek létrehozva és tárolva a blobok adatfájljai.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>A csomag telepítése

Továbbra is az alkalmazás könyvtárában maradva telepítse a Azure Blob Storage .NET-csomag ügyféloldali kódtárát az `dotnet add package` paranccsal.

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projektkönyvtárból:

1. Nyissa meg *a Program.cs* fájlt a szerkesztőben.
1. Távolítsa el a `Console.WriteLine("Hello World!");` utasítást.
1. `using`Direktíva hozzáadása.
1. Frissítse a `Main` metódusdeklarációt az async támogatásához.

    A kód a következő:

    :::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/app_framework.cs":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A Blob Storage háromféle erőforrástípust kínál:

* A tárfiók
* Egy tároló a tárfiókban
* Egy blob a tárolóban

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![A Blob Storage architektúráját szemléltető diagram](./media/storage-blobs-introduction/blob1.png)

Az alábbi .NET-osztályokkal használhatja ezeket az erőforrásokat:

* [BlobServiceClient:](/dotnet/api/azure.storage.blobs.blobserviceclient)A osztály lehetővé teszi az `BlobServiceClient` Azure Storage-erőforrások és -blobtárolók manipulálát.
* [BlobContainerClient:](/dotnet/api/azure.storage.blobs.blobcontainerclient)A osztály lehetővé teszi az Azure Storage-tárolók és azok `BlobContainerClient` blobjainak a beállítását.
* [BlobClient:](/dotnet/api/azure.storage.blobs.blobclient)A `BlobClient` osztály lehetővé teszi az Azure Storage-blobok manipulálát.
* [BlobDownloadInfo:](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo)A osztály a blob letöltésével visszaadott tulajdonságokat `BlobDownloadInfo` és tartalmat jelöli.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek azt mutatják be, hogyan hajthatja végre a következőket a .NET-hez Azure Blob Storage ügyféloldali kódtárával:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [Tárolóban lévő blobok kilistázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód lekéri a tárfiók kapcsolati sztringet a Tárterület kapcsolati sztring konfigurálása szakaszban létrehozott környezeti [változóból.](#configure-your-storage-connection-string)

Adja hozzá ezt a kódot a `Main` metódushoz:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód hozzáfűz egy GUID-értéket a tároló nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Hozza létre a [BlobServiceClient osztály egy példányát.](/dotnet/api/azure.storage.blobs.blobserviceclient) Ezután hívja meg a [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) metódust a tároló tárfiókban való létrehozásához.

Adja hozzá ezt a kódot a metódus `Main` véghez:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

Az alábbi kódrészlet:

1. Létrehoz egy szövegfájlt a helyi *adatkönyvtárban.*
1. Lekért egy [blobclient objektumra](/dotnet/api/azure.storage.blobs.blobclient) vonatkozó hivatkozást a Tároló létrehozása szakasz [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) metódusának a tárolón való [hívásával.](#create-a-container)
1. Feltölti a helyi szövegfájlt a blobba az [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync#Azure_Storage_Blobs_BlobClient_UploadAsync_System_IO_Stream_System_Boolean_System_Threading_CancellationToken_) metódus hívásával. Ez a metódus létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen.

Adja hozzá ezt a kódot a metódus `Main` véghez:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

Listzza ki a tárolóban lévő blobokat a [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) metódus hívásával. Ebben az esetben csak egy blob lett hozzáadva a tárolóhoz, így a listázási művelet csak ezt az egy blobot adja vissza.

Adja hozzá ezt a kódot a metódus `Main` véghez:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [DownloadAsync metódus hívásával.](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync) A példakód hozzáadja a "DOWNLOADED" utótagot a fájlnévhez, így mindkét fájlt láthatja a helyi fájlrendszerben.

Adja hozzá ezt a kódot a metódus `Main` véghez:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Tároló törlése

A következő kód törli az alkalmazás által létrehozott erőforrásokat a teljes tároló törlésével a [DeleteAsync használatával.](/dotnet/api/azure.storage.blobs.blobcontainerclient.deleteasync) Emellett törli az alkalmazás által létrehozott helyi fájlokat is.

Az alkalmazás a hívásával szünetelteti a felhasználói bevitelt, mielőtt töröl egy blobot, egy tárolót és `Console.ReadLine` egy helyi fájlt. Ez jó lehetőség annak ellenőrzésére, hogy az erőforrások valóban megfelelően, a törlésük előtt létrejöttek-e.

Adja hozzá ezt a kódot a metódus `Main` véghez:

:::code language="csharp" source="~/azure-storage-snippets/blobs/quickstarts/dotnet/BlobQuickstartV12/Program.cs" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy tesztfájlt a helyi *adatmappában,* és feltölti azt a Blob Storage-be. A példa ezután listázza a tárolóban lévő blobokat, és letölti a fájlt egy új névvel, hogy összehasonlítsa a régi és az új fájlokat.

Navigáljon az alkalmazás könyvtárához, majd készítse el és futtassa az alkalmazást.

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

A tisztítási folyamat megkezdése előtt ellenőrizze a *két* fájlt az adatmappában. Ha megnyitja őket, láthatja, hogy megegyeznek.

A fájlok ellenőrzése után nyomja le az **Enter** billentyűt a tesztfájlok törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megismerkedett a blobok a .NET használatával való fel- és letöltésével, valamint kilistázásával.

A Blob Storage mintaalkalmazások a következővel continue to:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK v12 .NET-minták](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* Az oktatóanyagokért, mintákért, rövid útmutatókért és egyéb dokumentációért látogasson el az [Azure .NET- és .NET Core-fejlesztőknek](/dotnet/azure/)oldalon.
* A .NET Core keretrendszerrel kapcsolatos további információért lásd [a .NET használatának első lépéseit 10 percben](https://dotnet.microsoft.com/learn/dotnet/hello-world-tutorial/intro) ismertető szakaszt.
