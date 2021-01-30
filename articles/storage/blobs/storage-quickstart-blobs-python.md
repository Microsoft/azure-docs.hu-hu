---
title: 'Gyors útmutató: Azure Blob Storage Library V12 – Python'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használható a Pythonhoz készült Azure Blob Storage Client Library 12-es verziója, és hogyan hozható létre tároló és blob (Object) tároló. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/28/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e315f0f4f7bfff03a659de430e6fe182037f1b8a
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99096406"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Gyors útmutató: Blobok kezelése a Python V12 SDK-val

Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti a blobokat a Python használatával. A Blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatok tárolására képesek, beleértve a képeket, a dokumentumokat, a médiafolyamokat és az archiválási adatokhoz. Feltöltheti, letöltheti és listázhatja a blobokat, és létrehozhatja és törölheti a tárolókat.

További források:

* [API-referenciadokumentáció](/python/api/azure-storage-blob)
* [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-storage-blob/)
* [Példák](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy Storage-fiókot](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 vagy újabb.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Beállítás

Ez a szakasz végigvezeti a projekt előkészítésének folyamatán a Pythonhoz készült Azure Blob Storage ügyféloldali kódtár V12-es verziójának használatával.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *blob-Gyorsindítás-V12* nevű Python-alkalmazást.

1. A konzol ablakban (például cmd, PowerShell vagy bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Váltson az újonnan létrehozott *blob-Gyorsindítás-V12* könyvtárra.

    ```console
    cd blob-quickstart-v12
    ```

1. A *blob-Gyorsindítás-V12* könyvtárban hozzon létre egy másik könyvtárat, amely az *adat* nevű. Ebben a könyvtárban lesz létrehozva és tárolva a blob-adatfájlok.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>A csomag telepítése

Miközben továbbra is az alkalmazás könyvtára, telepítse az Azure Blob Storage a Python-csomaghoz készült ügyféloldali kódtárat a `pip install` paranccsal.

```console
pip install azure-storage-blob
```

Ez a parancs telepíti az Azure Blob Storage ügyféloldali kódtárat a Python-csomaghoz, és az összes olyan kódtárat, amelytől függ. Ebben az esetben ez csak a Pythonhoz készült Azure Core Library.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Új szövegfájl megnyitása a kódszerkesztő programban
1. `import`Utasítások hozzáadása
1. A program struktúrájának létrehozása, beleértve az alapszintű kivételek kezelését

    A kód a következő:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Mentse az új fájlt *blob-Quickstart-v12.py* néven a *blob-Gyorsindítás-V12* könyvtárba.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem felelnek meg egy adott adatmodellnek vagy definíciónak, például szöveges vagy bináris adatoknak. A blob Storage háromféle típusú erőforrást kínál:

* A Storage-fiók
* Egy tároló a Storage-fiókban
* A tárolóban lévő blob

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![BLOB Storage-architektúra ábrája](./media/storage-blobs-introduction/blob1.png)

A következő Python-osztályok használata az alábbi erőforrásokkal való interakcióhoz:

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): a `BlobServiceClient` osztály lehetővé teszi az Azure Storage-erőforrások és a blob-tárolók kezelését.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): a `ContainerClient` osztály lehetővé teszi az Azure Storage-tárolók és a Blobok kezelését.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): a `BlobClient` osztály lehetővé teszi az Azure Storage-Blobok kezelését.

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Pythonhoz készült Azure Blob Storage ügyféloldali kódtár használatával:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [Tárolóban lévő blobok kilistázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód a Storage- [kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból kéri le a Storage-fiókhoz tartozó kapcsolatok karakterláncát.

Adja hozzá ezt a kódot a `try` blokkon belül:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód egy UUID értéket fűz hozzá a tároló nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Hozza létre a [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) osztály egy példányát a [from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) metódus meghívásával. Ezután hívja meg a [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) metódust, hogy ténylegesen létrehozza a tárolót a Storage-fiókban.

Adja hozzá ezt a kódot a blokk végéhez `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet:

1. Létrehoz egy helyi könyvtárat az adatfájlok tárolásához.
1. Létrehoz egy szövegfájlt a helyi könyvtárban.
1. Beolvas egy [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) objektumra mutató hivatkozást úgy, hogy meghívja a [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) metódust a [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) a [tároló létrehozása](#create-a-container) szakaszban.
1. A [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) metódus meghívásával feltölti a helyi szövegfájlt a blobba.

Adja hozzá ezt a kódot a blokk végéhez `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

A tárolóban lévő Blobok listázása a [list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) metódus meghívásával. Ebben az esetben a tárolóhoz csak egy blob lett hozzáadva, így a listázási művelet csak ezt az egy blobot adja vissza.

Adja hozzá ezt a kódot a blokk végéhez `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) metódus meghívásával. A példában szereplő kód a "Letöltés" utótagot adja hozzá a fájl nevéhez, hogy mindkét fájl látható legyen a helyi fájlrendszerben.

Adja hozzá ezt a kódot a blokk végéhez `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Tároló törlése

A következő kód megtisztítja az alkalmazás által létrehozott erőforrásokat, ha eltávolítja a teljes tárolót a [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) metódussal. Ha szeretné, törölheti a helyi fájlokat is.

Az alkalmazás a felhasználói bevitel szüneteltetését hívja `input()` meg, mielőtt törli a blobot, a tárolót és a helyi fájlokat. A törlés előtt ellenőrizze, hogy az erőforrások megfelelően lettek-e létrehozva.

Adja hozzá ezt a kódot a blokk végéhez `try` :

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy tesztoldalt a helyi mappában, és feltölti az Azure Blob Storageba. A példa ezután felsorolja a tárolóban lévő blobokat, és új névvel tölti le a fájlt. Összehasonlíthatja a régi és az új fájlokat.

Keresse meg a *blob-Quickstart-v12.py* fájlt tartalmazó könyvtárat, majd futtassa a következő `python` parancsot az alkalmazás futtatásához.

```console
python blob-quickstart-v12.py
```

Az alkalmazás kimenete az alábbi példához hasonló:

```output
Azure Blob Storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

A karbantartási folyamat megkezdése előtt tekintse *meg a két fájl adatmappáját* . Megnyithatja őket, és megfigyelheti, hogy azonosak-e.

A fájlok ellenőrzése után nyomja le az **ENTER** billentyűt a tesztoldal törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listázhat blobokat a Python használatával.

A blob Storage-beli minták alkalmazásainak megtekintéséhez folytassa a következőket:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 Python-minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* További információ: az [Azure Storage ügyféloldali kódtárai a Pythonhoz](/azure/developer/python/sdk/storage/overview).
* Az oktatóanyagok, minták, rövid útmutatók és egyéb dokumentációk az Azure-ban, [Python-fejlesztőknek](/azure/python/)című oldalon találhatók meg.
