---
title: 'Gyors útmutató: Azure Blob Storage Library V12 – C++'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használható az Azure Blob Storage ügyféloldali kódtár 12-es verziója a C++ rendszerhez tároló és blob (Object) tárolóban való létrehozásához. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: twooley
ms.author: twooley
ms.date: 10/21/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 7a0d2bdb1879e4f68b582ee56367f0238285a401
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278082"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>Gyors útmutató: Azure Blob Storage ügyféloldali kódtár V12 a C++ nyelvhez

Ismerkedjen meg a C++ Azure Blob Storage ügyféloldali kódtár V12-es verziójának használatába. Az Azure Blob Storage a Microsoft objektum-tárolási megoldás a felhőhöz. Kövesse a csomag telepítésének lépéseit, és próbálja ki például az alapszintű feladatokhoz tartozó kódot. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva.

A C++-hoz készült Azure Blob Storage ügyféloldali kódtár a következőhöz használható:

- Tároló létrehozása
- Blob feltöltése az Azure Storage-ba
- Egy tároló összes blobjának listázása
- A blob letöltése a helyi számítógépre
- Tároló törlése

Erőforrások:

- [API-referenciadokumentáció](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [Példák](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Azure-előfizetés](https://azure.microsoft.com/free/)
- [Azure Storage-fiók](../common/storage-account-create.md)
- [C++ fordító](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg-C és C++ csomag kezelője](https://github.com/microsoft/vcpkg/blob/master/docs/index.md)
- [LibCurl](https://curl.haxx.se/libcurl/)
- [LibXML2](http://www.xmlsoft.org/)

## <a name="setting-up"></a>Beállítás

Ebből a szakaszból megtudhatja, hogyan készít elő egy projektet a C++ Azure Blob Storage ügyféloldali kódtár V12-es verziójának működéséhez.

### <a name="install-the-packages"></a>A csomagok telepítése

Ha még nem tette meg, telepítse a LibCurl-és LibXML2-csomagokat a `vcpkg install` paranccsal.

```console
vcpkg.exe install libxml2:x64-windows curl:x64-windows
```

Kövesse a GitHubon megjelenő utasításokat a [C++ nyelvhez készült Azure SDK](https://github.com/Azure/azure-sdk-for-cpp/)beolvasásához és létrehozásához.

### <a name="create-the-project"></a>A projekt létrehozása

A Visual Studióban hozzon létre egy új C++ konzol alkalmazást a Windows nevű *BlobQuickstartV12*.

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="Visual Studio párbeszédpanel új C++ Windows-konzol alkalmazás konfigurálásához":::

Adja hozzá a következő kódtárakat a projekthez:

- libcurl. lib
- libxml2. lib
- bcrypt. lib
- Crypt32. lib
- WS2_32. lib
- Azure-Core. lib
- Azure-Storage-Common. lib
- Azure-Storage-Blobs. lib

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem felelnek meg egy adott adatmodellnek vagy definíciónak, például szöveges vagy bináris adatoknak. A Blob Storage háromféle típusú erőforrást kínál:

- A Storage-fiók
- Egy tároló a Storage-fiókban
- A tárolóban lévő blob

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Blob Storage architektúra ábrája](./media/storage-blobs-introduction/blob1.png)

Használja az alábbi C++ osztályokat a következő erőforrásokkal való interakcióhoz:

- [BlobServiceClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html): a `BlobServiceClient` osztály lehetővé teszi az Azure Storage-erőforrások és a blob-tárolók kezelését.
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html): a `BlobContainerClient` osztály lehetővé teszi az Azure Storage-tárolók és a Blobok kezelését.
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html): a `BlobClient` osztály lehetővé teszi az Azure Storage-Blobok kezelését. Ez a speciális blob-osztályok alaposztálya.
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html): a `BlockBlobClient` osztály lehetővé teszi az Azure Storage blokk-Blobok kezelését.

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a C++ Azure Blob Storage ügyféloldali kódtár használatával:

- [Belefoglalási fájlok hozzáadása](#add-include-files)
- [A kapcsolati sztring lekérése](#get-the-connection-string)
- [Tároló létrehozása](#create-a-container)
- [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
- [Tárolóban lévő blobok kilistázása](#list-the-blobs-in-a-container)
- [Blobok letöltése](#download-blobs)
- [Tároló törlése](#delete-a-container)

### <a name="add-include-files"></a>Belefoglalási fájlok hozzáadása

A projekt könyvtárából:

1. A *BlobQuickstartV12. SLN* megoldás fájljának megnyitása a Visual Studióban
1. A Visual Studióban nyissa meg a *BlobQuickstartV12. cpp* forrásfájlt
1. Az `main` automatikusan létrehozott kódok eltávolítása
1. `#include`Utasítások hozzáadása

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_Includes":::

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód a Storage- [kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string)című részében létrehozott környezeti változóból kéri le a Storage-fiókhoz tartozó kapcsolatok karakterláncot.

A kód hozzáadása a következőn belül `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Tároló létrehozása

Hozza létre a [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html) osztály egy példányát a [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ad103d1e3a7ce7c53a82da887d12ce6fe) függvény meghívásával. Ezután hívja a [create (létrehozás](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a22a1e001068a4ec52bb1b6bd8b52c047) ) lehetőséget, hogy létrehozza a tényleges tárolót a Storage-fiókban.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Adja hozzá ezt a kódot a következő végéhez `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet:

1. A "Hello Azure!" kifejezést tartalmazó karakterlánc deklarálása.
1. Egy [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html) objektumra mutató hivatkozás beolvasása a tároló [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f) meghívásával a tároló [létrehozása](#create-a-container) szakaszból.
1. A [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d) függvény meghívásával feltölti a karakterláncot a blobba. Ez a függvény létrehozza a blobot, ha az még nem létezik, vagy ha igen, frissíti.

Adja hozzá ezt a kódot a következő végéhez `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

A tárolóban lévő Blobok listázása a [ListBlobsFlatSegment](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ac7712bc46909dc061d6bf554b496550c) függvény meghívásával. Csak egy blob lett hozzáadva a tárolóhoz, így a művelet csak ezt a blobot adja vissza.

Adja hozzá ezt a kódot a következő végéhez `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Blobok letöltése

A feltöltött blob tulajdonságainak beolvasása. Ezután deklaráljon és méretezze át egy új `std::string` objektumot a feltöltött blob tulajdonságainak használatával. Töltse le a korábban létrehozott blobot az új `std::string` objektumba úgy, hogy meghívja a [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa844f37a8c216f3cb0f27912b114c4d2) függvényt a [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html) alaposztályában. Végül jelenítse meg a letöltött blob-fájlokat.

Adja hozzá ezt a kódot a következő végéhez `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>BLOB törlése

A következő kód törli a blobot az Azure Blob Storage-tárolóból a [BlobClient. Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#a621eabcc8d23893ca1eb106494198615) függvény meghívásával.

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>Tároló törlése

A következő kód megtisztítja az alkalmazás által létrehozott erőforrásokat a teljes tároló törlésével a [BlobContainerClient. Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#aa6b1db52697ae92e9a1227e2e02a5178)használatával.

Adja hozzá ezt a kódot a következő végéhez `main()` :

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" id="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy tárolót, és feltölt egy szövegfájlt az Azure Blob Storageba. A példa ezután felsorolja a tárolóban lévő blobokat, letölti a fájlt, és megjeleníti a fájl tartalmát. Végül az alkalmazás törli a blobot és a tárolót.

Az alkalmazás kimenete az alábbi példához hasonló:

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listázhat blobokat a C++ használatával. Azt is megtanulta, hogyan hozhat létre és törölhet egy Azure Blob Storage-tárolót.

A C++ Blob Storage minta megjelenítéséhez folytassa a következővel:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 C++ minta](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)