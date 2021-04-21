---
title: Fejlesztés Azure Files Python-| Microsoft Docs
description: Megtudhatja, hogyan fejleszthet olyan Python-alkalmazásokat és -szolgáltatásokat, amelyek Azure Files a fájlok adatainak tárolására.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: 8739bfaf1a41758ef3267c71cba883ef2445c39d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817791"
---
# <a name="develop-for-azure-files-with-python"></a>Fejlesztés az Azure Files szolgáltatáshoz Pythonnal

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Ismerje meg a Python használatának alapjait olyan alkalmazások vagy szolgáltatások fejlesztéséhez, amelyek Azure Files a fájlok adatainak tárolására. Hozzon létre egy egyszerű konzolalkalmazást, és ismerje meg, hogyan végezhet el alapszintű műveleteket a Python és a Azure Files:

- Azure-fájlmegosztások létrehozása
- Könyvtárak létrehozása
- Azure-fájlmegosztás fájljainak és könyvtárának számbavétele
- Fájl feltöltése, letöltése és törlése
- Fájlmegosztások biztonsági mentésének létrehozása pillanatképek használatával

> [!NOTE]
> Mivel Azure Files SMB-kapcsolaton keresztül is elérhetők, egyszerű alkalmazások írhatóak, amelyek a szabványos Python I/O-osztályok és -függvények használatával férnek hozzá az Azure-fájlmegosztáshoz. Ez a cikk bemutatja, hogyan írhat olyan alkalmazásokat, amelyek [](/rest/api/storageservices/file-service-rest-api) a Azure Files Storage Python SDK-t használják, amely a Azure Files REST API segítségével a Azure Files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>A Pythonhoz készült Azure Storage SDK letöltése és telepítése

> [!NOTE]
> Ha a Pythonhoz készült Azure Storage SDK 0.36-os vagy korábbi verziójáról frissít, a legújabb csomag telepítése előtt távolítsa el a régebbi `pip uninstall azure-storage` SDK-t a használatával.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Az Azure File Storage Pythonhoz készült [12.x-es](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) ügyféloldali kódtárhoz Python 2.7-es vagy 3.6-os vagy további szükséges.

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

A [Pythonhoz készült Azure Storage SDK-hoz a](https://github.com/azure/azure-storage-python) Python 2.7-es vagy 3.6-os vagy további verziója szükséges.

---

## <a name="install-via-pypi"></a>Telepítés PyPI-n keresztül

A Python-csomagindex (PyPI) használatával történő telepítéshez írja be a következőt:

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>A mintaalkalmazás megtekintése

A Python és a Azure Files használatát bemutató mintaalkalmazás megtekintéséhez és futtatásához lásd: Azure Storage: Ismerkedés a Azure Files [a Pythonban.](https://github.com/Azure-Samples/storage-file-python-getting-started)

A mintaalkalmazás futtatásához győződjön meg arról, hogy telepítette a és a `azure-storage-file` `azure-storage-common` csomagot is.

---

## <a name="set-up-your-application-to-use-azure-files"></a>Az alkalmazás beállítása a Azure Files

Adja hozzá a következőt egy Python-forrásfájl tetejéhez a cikkben található kódrészletek használatához.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Kapcsolat beállítása a Azure Files

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

[A ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) lehetővé teszi a megosztások, könyvtárak és fájlok használatával való munkát. Az alábbi kód egy objektumot hoz létre a `ShareServiceClient` tárfiók kapcsolati sztring használatával.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

A [FileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) objektum lehetővé teszi a megosztások, könyvtárak és fájlok használatával való munkát. Az alábbi kód egy objektumot hoz létre a `FileService` tárfiók nevének és fiókkulcsának használatával. A `<myaccount>` és a `<mykey>` értéket cserélje le a fiók nevére és kulcsára.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Az alábbi példakód egy [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) objektumot használ a megosztás létrehozásához, ha az még nem létezik.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Az alábbi példakód [fileService](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) objektumot használ a megosztás létrehozásához, ha az még nem létezik.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Könyvtár létrehozása

A tárterületet úgy rendszerezheti, hogy a fájlokat alkönyvtárakba rendezi ahelyett, hogy mindegyik a gyökérkönyvtárban található volna.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

A következő metódus létrehoz egy könyvtárat a megadott fájlmegosztás gyökerében egy [ShareDirectoryClient objektum](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) használatával.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Az alábbi kód létrehoz egy *sampledir* nevű alkönyvtárat a gyökérkönyvtárban.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Fájl feltöltése

Ebben a szakaszban megtudhatja, hogyan tölthet fel egy fájlt a helyi tárolóból az Azure File Storage.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Az alábbi metódus feltölti a megadott fájl tartalmát a megadott Azure-fájlmegosztás megadott könyvtárába.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Az Azure-fájlmegosztások legalább egy gyökérkönyvtárat tartalmaznak, ahol a fájlok találhatók. Fájl létrehozásához és adatok feltöltéséhez használja a create_file_from_path [,](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-) [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)vagy [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) metódusokat. Ezek magas szintű metódusok, amelyek elvégzik a szükséges adattömbök feldarabolását, ha az adatok mérete meghaladja a 64 MB-ot.

`create_file_from_path` feltölti egy fájl tartalmát a megadott elérési útról, és feltölti a tartalmat egy már megnyitott `create_file_from_stream` fájlból/streamből. `create_file_from_bytes` feltölt egy bájttömböt, és feltölti a megadott szöveges értéket a megadott kódolással (alapértelmezés szerint `create_file_from_text` UTF-8).

Az alábbi példa feltölti asunset.png *tartalmát* a **myfile fájlba.**

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None,  # We want to create this file in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

---

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure-fájlmegosztás fájljainak és könyvtárának számbavétele

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Egy alkönyvtár fájljainak és könyvtárának listához használja a [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) metódust. Ez a metódus egy automatikusan lapozó iterable értéket ad vissza. Az alábbi kód  a megadott könyvtárban található fájlok és alkönyvtárak nevét adja ki a konzolon.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

A megosztásban lévő fájlok és könyvtárak listához használja a [list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) metódust. A metódus egy generátort ad vissza. A következő kód  a konzolon található megosztásban található egyes fájlok és könyvtárak nevét tartalmazza.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Fájl letöltése

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Adatok fájlból való letöltéséhez használja a [következőt: download_file.](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-)

Az alábbi példa bemutatja a parancsot a megadott fájl tartalmának lekért és helyileg való tárolására, a fájlnév előtt a `download_file` **DOWNLOADED** előtaggal.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Adatok fájlból való letöltéséhez használja a [get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)vagy [get_file_to_text.](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-) Ezek magas szintű metódusok, amelyek elvégzik a szükséges adattömbök feldarabolását, ha az adatok mérete meghaladja a 64 MB-ot.

A következő példa bemutatja, hogyan tölthető le a myfile fájl tartalma, és tárolható a fájlban `get_file_to_path` *out-sunset.png*  fájlban.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Megosztás-pillanatkép létrehozása

A teljes fájlmegosztásból létrehozhat egy időponthoz időben létrehozott másolatot.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**Megosztási pillanatkép létrehozása metaadatokkal**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

---

## <a name="list-shares-and-snapshots"></a>Megosztások és pillanatképek listája

Felsorolhatja egy adott megosztás összes pillanatképét.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Megosztási pillanatkép tallózása

Az egyes megosztási pillanatképek között tallózva lekérheti az adott időpontban lévő fájlokat és könyvtárakat.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Fájl lekérte a megosztási pillanatképből

A fájlokat letöltheti egy megosztási pillanatképből. Ez lehetővé teszi egy fájl korábbi verziójának visszaállítását.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Egyetlen megosztási pillanatkép törlése
Egyetlen megosztási pillanatképet törölhet.

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Fájl törlése

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Fájl törléséhez hívja meg a [következőt: delete_file.](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

Fájl törléséhez hívja meg a [következőt: delete_file.](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-)

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Megosztás törlése, ha vannak megosztási pillanatképek

# <a name="azure-python-sdk-v12"></a>[Azure Python SDK v12](#tab/python)

Pillanatképeket tartalmazó megosztás törléséhez hívja meg a delete_share [a](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) `delete_snapshots=True` következővel: .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="azure-python-sdk-v2"></a>[Azure Python SDK v2](#tab/python2)

A pillanatképeket tartalmazó megosztások csak akkor törölhetők, ha először az összes pillanatképet törlik.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan módosíthatja a Azure Files a Pythonnal, az alábbi hivatkozásokon további információt olvashat.

- [Python fejlesztői központ](/azure/developer/python/)
- [Az Azure Storage-szolgáltatások REST API-ja](/rest/api/azure/)
- [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
