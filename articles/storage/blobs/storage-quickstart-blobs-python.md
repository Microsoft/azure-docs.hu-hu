---
title: 'Rövid útmutató: Azure Blob Storage 12-es kódtár – Python'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Azure Blob Storage Pythonhoz készült ügyféloldali kódtár 12-es verzióját egy tároló és egy blobtárolóban való létrehozásához. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: twooley
ms.author: twooley
ms.date: 01/28/2021
ms.topic: quickstart
ms.service: storage
ms.subservice: blobs
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: 9a6ccbe5482ac8ffe484c3155334cba1b50ba0dc
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535649"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>Rövid útmutató: Blobok kezelése a Python v12 SDK-val

Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti a blobokat a Python használatával. A blobok olyan objektumok, amelyek nagy mennyiségű szöveget vagy bináris adatot, köztük képeket, dokumentumokat, médiastreamelést és archív adatokat is tartalmaznak. Blobokat fog feltölteni, letölteni és listába helyezni, valamint tárolókat fog létrehozni és törölni.

További források:

* [API-referenciadokumentáció](/python/api/azure-storage-blob)
* [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob)
* [Csomag (Python-csomagindex)](https://pypi.org/project/azure-storage-blob/)
* [Példák](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Egy Azure Storage-fiók. [Hozzon létre egy tárfiókot.](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) 2.7 vagy 3.6+.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti egy projekt előkészítésén a Pythonhoz készült Azure Blob Storage 12-es Azure Blob Storage való használatához.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *blob-quickstart-v12 nevű Python-alkalmazást.*

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Váltson az újonnan *létrehozott blob-quickstart-v12 könyvtárra.*

    ```console
    cd blob-quickstart-v12
    ```

1. A *blob-quickstart-v12* könyvtár mellett hozzon létre egy másik könyvtárat *data néven.* Ebben a könyvtárban lesznek létrehozva és tárolva a blobok adatfájljai.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>A csomag telepítése

Az alkalmazáskönyvtárban maradva telepítse a Azure Blob Storage Pythonhoz készült ügyféloldali kódtárat az `pip install` paranccsal.

```console
pip install azure-storage-blob
```

Ez a parancs telepíti a Azure Blob Storage Python-csomaghoz készült ügyféloldali kódtárat és az összes kódtárat, amelyektől függ. Ebben az esetben ez csak a Pythonhoz készült Azure alapkönyvtár.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projektkönyvtárból:

1. Új szövegfájl megnyitása a kódszerkesztőben
1. Utasítások `import` hozzáadása
1. A program struktúrájának létrehozása, beleértve az alapszintű kivételkezelést

    A kód a következő:

    :::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/app_framework.py":::

1. Mentse az új fájlt *blob-quickstart-v12.py* *blob-quickstart-v12 könyvtárba.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem felelnie egy adott adatmodellnek vagy -definíciónak, például szöveges vagy bináris adatok. A Blob Storage háromféle erőforrást kínál:

* A tárfiók
* Egy tároló a tárfiókban
* Egy blob a tárolóban

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![A Blob Storage architektúráját ábrázoló diagram](./media/storage-blobs-introduction/blob1.png)

Az alábbi Python-osztályokkal használhatja ezeket az erőforrásokat:

* [BlobServiceClient:](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient)A osztály lehetővé teszi az `BlobServiceClient` Azure Storage-erőforrások és blobtárolók manipulálása.
* [ContainerClient:](/python/api/azure-storage-blob/azure.storage.blob.containerclient)A osztály lehetővé teszi az Azure Storage-tárolók és azok `ContainerClient` blobok manipulálát.
* [BlobClient:](/python/api/azure-storage-blob/azure.storage.blob.blobclient)A `BlobClient` osztály lehetővé teszi az Azure Storage-blobok manipulálása.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek azt mutatják be, hogyan kell elvégezni a következő feladatokat Azure Blob Storage Pythonhoz készült ügyféloldali kódtára használatával:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [Tárolóban lévő blobok kilistázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód lekéri a tárfiók kapcsolati sztringet a Tárfiók kapcsolati sztring konfigurálása szakaszban létrehozott környezeti [változóból.](#configure-your-storage-connection-string)

Adja hozzá ezt a kódot a `try` blokkhoz:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód egy UUID-értéket fűz a tároló nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Hozza létre a [BlobServiceClient osztály](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) egy példányát a from_connection_string [metódus](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) hívásával. Ezután hívja meg a [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-) metódust, hogy ténylegesen létrehozza a tárolót a tárfiókban.

Adja hozzá ezt a kódot a blokk `try` véghez:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

Az alábbi kódrészlet:

1. Létrehoz egy helyi könyvtárat az adatfájlok számára.
1. Létrehoz egy szövegfájlt a helyi könyvtárban.
1. Lekért egy BlobClient objektumra [](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) való hivatkozást a [blobserviceclient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) get_blob_client metódusának a [Tároló](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) létrehozása szakaszból való [hívásával.](#create-a-container)
1. Feltölti a helyi szövegfájlt a blobba a upload_blob [metódus](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) hívásával.

Adja hozzá ezt a kódot a blokk `try` véghez:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_UploadBlobs":::

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

Lists a tárolóban lévő blobokat a list_blobs [metódus](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) hívásával. Ebben az esetben csak egy blob lett hozzáadva a tárolóhoz, így a listázási művelet csak ezt az egy blobot adja vissza.

Adja hozzá ezt a kódot a blokk `try` véghez:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) metódus hívásával. A példakód egy "DOWNLOAD" (LETÖLTÉS) utótagot ad hozzá a fájlnévhez, így mindkét fájlt láthatja a helyi fájlrendszerben.

Adja hozzá ezt a kódot a blokk `try` véghez:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_DownloadBlobs":::

### <a name="delete-a-container"></a>Tároló törlése

Az alábbi kód az alkalmazás által létrehozott erőforrásokat távolítja el úgy, hogy a teljes tárolót eltávolítja a [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) metódussal. Ha szeretné, törölheti is a helyi fájlokat.

Az alkalmazás a hívásával szünetelteti a felhasználói bevitelt, mielőtt töröl egy blobot, egy tárolót és egy `input()` helyi fájlt. A törlés előtt ellenőrizze, hogy az erőforrások megfelelően létrejöttek-e.

Adja hozzá ezt a kódot a blokk `try` véghez:

:::code language="python" source="~/azure-storage-snippets/blobs/quickstarts/python/V12/blob-quickstart-v12.py" id="Snippet_CleanUp":::

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy tesztfájlt a helyi mappában, és feltölti a Azure Blob Storage. A példa ezután listázza a tárolóban lévő blobokat, és letölti a fájlt egy új névvel. Összehasonlíthatja a régi és az új fájlokat.

Keresse meg a blob-quickstart-v12.py *fájlt* tartalmazó könyvtárat, majd futtassa az alkalmazást a következő `python` paranccsal.

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

A tisztítási folyamat megkezdése előtt ellenőrizze a *két* fájlt az adatmappában. Megnyithatja őket, és megfigyelheti, hogy megegyeznek.

A fájlok ellenőrzése után nyomja le az **Enter** billentyűt a tesztfájlok törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listából blobokat a Python használatával.

Blob Storage-mintaalkalmazások:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK v12 Python-minták](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* További tudnivalókért lásd a [Pythonhoz készült Azure Storage-ügyfélkódtárakat.](/azure/developer/python/sdk/storage/overview)
* Oktatóanyagok, minták, rövid útmutatók és egyéb dokumentáció: [Azure Python-fejlesztőknek.](/azure/python/)
