---
title: Fejlesztés a Azure Files Pythonban | Microsoft Docs
description: Megtudhatja, hogyan fejleszthet olyan Python-alkalmazásokat és-szolgáltatásokat, amelyek a fájlok tárolásához Azure Files használnak.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/08/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-python
ms.openlocfilehash: d45ce3a782d7ee145f769283b82e34647c78f26e
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799870"
---
# <a name="develop-for-azure-files-with-python"></a>Fejlesztés az Azure Files szolgáltatáshoz Pythonnal

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

A Python használatának alapjai a fájlok tárolására Azure Files használó alkalmazások és szolgáltatások fejlesztéséhez. Hozzon létre egy egyszerű konzolos alkalmazást, és Ismerje meg, hogyan hajthat végre alapszintű műveleteket a Python és a Azure Files használatával:

- Azure-fájlmegosztás létrehozása
- Könyvtárak létrehozása
- Fájlok és könyvtárak enumerálása Azure-fájlmegosztás esetén
- Fájl feltöltése, letöltése és törlése
- Fájlmegosztás biztonsági másolatának létrehozása Pillanatképek használatával

> [!NOTE]
> Mivel Azure Files az SMB-kapcsolaton keresztül érhető el, az Azure-fájlmegosztás szabványos Python I/O-osztályaival és-funkcióival való használatával egyszerű alkalmazásokat is írhat. Ez a cikk leírja, hogyan írhat olyan alkalmazásokat, amelyek a Azure Files Storage Python SDK-t használják, amely a [Azure Files REST API](/rest/api/storageservices/file-service-rest-api) használatával kommunikál Azure files.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Töltse le és telepítse a Pythonhoz készült Azure Storage SDK-t

> [!NOTE]
> Ha a Python 0,36-es vagy korábbi verziójú Azure Storage SDK-ból frissít, távolítsa el a régebbi SDK-t a `pip uninstall azure-storage` legújabb csomag telepítése előtt.

# <a name="python-v12"></a>[Python V12](#tab/python)

Az [Azure file Storage a Python v12. x-es verziójának](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share) Python 2,7 vagy 3.6 + rendszerre van szüksége.

# <a name="python-v2"></a>[Python v2](#tab/python2)

A [Pythonhoz készült Azure Storage SDK](https://github.com/azure/azure-storage-python) használatához Python 2,7 vagy 3.6 + szükséges.

---

## <a name="install-via-pypi"></a>Telepítés a PyPI-on keresztül

A Python Package index (PyPI) használatával történő telepítéshez írja be a következőt:

# <a name="python-v12"></a>[Python V12](#tab/python)

```console
pip install azure-storage-file-share
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-file
```

### <a name="view-the-sample-application"></a>A minta alkalmazás megtekintése

A Python és a Azure Files használatának módját bemutató minta alkalmazás megtekintéséhez és futtatásához tekintse meg az [Azure Storage: Első lépések Azure Files a Pythonban](https://github.com/Azure-Samples/storage-file-python-getting-started)című témakört.

A minta alkalmazás futtatásához győződjön meg arról, hogy a és a csomagokat is telepítette `azure-storage-file` `azure-storage-common` .

---

## <a name="set-up-your-application-to-use-azure-files"></a>Az alkalmazás beállítása Azure Files használatára

A cikkben szereplő kódrészletek használatához adja hozzá a következőt a Python-forrásfájl tetejéhez.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_Imports":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
from azure.storage.file import FileService
```

---

## <a name="set-up-a-connection-to-azure-files"></a>Azure Fileshoz való kapcsolódás beállítása

# <a name="python-v12"></a>[Python V12](#tab/python)

A [ShareServiceClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareserviceclient) lehetővé teszi a megosztások, könyvtárak és fájlok működését. A következő kód egy objektumot hoz létre `ShareServiceClient` a Storage-fiókhoz tartozó kapcsolatok karakterláncának használatával.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateShareServiceClient":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

A [Files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) objektum lehetővé teszi a megosztások, könyvtárak és fájlok működését. A következő kód létrehoz egy `FileService` objektumot a Storage-fiók neve és a fiók kulcsa alapján. A `<myaccount>` és a `<mykey>` értéket cserélje le a fiók nevére és kulcsára.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

---

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása

# <a name="python-v12"></a>[Python V12](#tab/python)

A következő mintakód egy [ShareClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient) objektum használatával hozza létre a megosztást, ha az nem létezik.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateFileShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

A következő mintakód egy [Files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true) objektum használatával hozza létre a megosztást, ha az nem létezik.

```python
file_service.create_share('myshare')
```

---

## <a name="create-a-directory"></a>Könyvtár létrehozása

A tárolást úgy rendezheti, hogy az alkönyvtárakban lévő fájlokat helyezi el, ahelyett, hogy mindegyiket a gyökérkönyvtárba helyezné.

# <a name="python-v12"></a>[Python V12](#tab/python)

A következő metódus egy könyvtárat hoz létre a megadott fájlmegosztás gyökerében egy [ShareDirectoryClient](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharedirectoryclient) objektum használatával.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateDirectory":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Az alábbi kód létrehoz egy *sampledir* nevű alkönyvtárat a gyökérkönyvtár alatt.

```python
file_service.create_directory('myshare', 'sampledir')
```

---

## <a name="upload-a-file"></a>Fájl feltöltése

Ebből a szakaszból megtudhatja, hogyan tölthet fel egy fájlt a helyi tárolóból az Azure File Storageba.

# <a name="python-v12"></a>[Python V12](#tab/python)

A következő módszer feltölti a megadott fájl tartalmát a megadott könyvtárba a megadott Azure-fájlmegosztás alapján.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_UploadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Az Azure-fájlmegosztás legalább egy olyan gyökérkönyvtárat tartalmaz, ahol a fájlok találhatók. Fájl létrehozásához és az adatok feltöltéséhez használja a [create_file_from_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-path-share-name--directory-name--file-name--local-file-path--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object---timeout-none-), [create_file_from_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-stream-share-name--directory-name--file-name--stream--count--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--), [create_file_from_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-bytes-share-name--directory-name--file-name--file--index-0--count-none--content-settings-none--metadata-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--)vagy [create_file_from_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#create-file-from-text-share-name--directory-name--file-name--text--encoding--utf-8---content-settings-none--metadata-none--validate-content-false--timeout-none--file-permission-none--smb-properties--azure-storage-file-models-smbproperties-object--) metódust. Magas szintű módszerek, amelyek elvégzik a szükséges adatdarabolást, ha az adatok mérete meghaladja az 64 MB-ot.

`create_file_from_path` feltölti a fájl tartalmát a megadott elérési útról, és `create_file_from_stream` feltölti a tartalmat egy már megnyitott fájlból/streamből. `create_file_from_bytes` feltölt egy bájtos tömböt, és `create_file_from_text` feltölti a megadott szöveges értéket a megadott kódolással (alapértelmezett érték: UTF-8).

A következő példa feltölti a *sunset.png* fájl tartalmát a **sajat** -fájlba.

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

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Fájlok és könyvtárak enumerálása Azure-fájlmegosztás esetén

# <a name="python-v12"></a>[Python V12](#tab/python)

Az alkönyvtárban található fájlok és könyvtárak listázásához használja a [list_directories_and_files](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#list-directories-and-files-directory-name-none--name-starts-with-none--marker-none----kwargs-) metódust. Ez a metódus egy automatikusan megjelenő, előre jelezhető lapozást ad vissza. A következő kód a megadott könyvtárban lévő egyes fájlok és alkönyvtárak **nevét** adja vissza a konzolra.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListFilesAndDirs":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Egy megosztásban található fájlok és könyvtárak listázásához használja a [list_directories_and_files](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#list-directories-and-files-share-name--directory-name-none--num-results-none--marker-none--timeout-none--prefix-none--snapshot-none-) metódust. A metódus egy generátort ad vissza. A következő kód egy megosztásban lévő egyes fájlok és könyvtárak **nevét** adja eredményül a konzolon.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

---

## <a name="download-a-file"></a>Fájl letöltése

# <a name="python-v12"></a>[Python V12](#tab/python)

Az adatok fájlból való letöltéséhez használja a [Download_File](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#download-file-offset-none--length-none----kwargs-).

Az alábbi példa azt mutatja be, hogy a használatával `download_file` beolvassa a megadott fájl tartalmát, és helyileg tárolja a **letöltött** előtagértéke a fájlnévre.

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Az adatok fájlból való letöltéséhez használja [get_file_to_path](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-path-share-name--directory-name--file-name--file-path--open-mode--wb---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_stream](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-stream-share-name--directory-name--file-name--stream--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-), [get_file_to_bytes](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-bytes-share-name--directory-name--file-name--start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-)vagy [get_file_to_text](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#get-file-to-text-share-name--directory-name--file-name--encoding--utf-8---start-range-none--end-range-none--validate-content-false--progress-callback-none--max-connections-2--timeout-none--snapshot-none-). Magas szintű módszerek, amelyek elvégzik a szükséges adatdarabolást, ha az adatok mérete meghaladja az 64 MB-ot.

Az alábbi példa bemutatja, hogyan `get_file_to_path` töltheti le a **sajat** -fájl tartalmát, és hogyan tárolhatja azt a *out-sunset.png* fájlba.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

---

## <a name="create-a-share-snapshot"></a>Megosztás-pillanatkép létrehozása

A teljes fájlmegosztás időpontját is létrehozhatja.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_CreateSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

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

## <a name="list-shares-and-snapshots"></a>Megosztások és Pillanatképek listázása

Egy adott megosztás összes pillanatképét listázhatja.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_ListSharesAndSnapshots":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

---

## <a name="browse-share-snapshot"></a>Megosztási pillanatkép tallózása

Az egyes megosztási Pillanatképek tallózásával lekérheti a fájlokat és a címtárakat az adott időpontban.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_BrowseSnapshotDir":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
directories_and_files = list(
    file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

---

## <a name="get-file-from-share-snapshot"></a>Fájl beolvasása a megosztási pillanatképből

A fájlokat letöltheti egy megosztási pillanatképből. Ez lehetővé teszi egy fájl korábbi verziójának visszaállítását.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DownloadSnapshotFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(
        share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

---

## <a name="delete-a-single-share-snapshot"></a>Egyetlen megosztási pillanatkép törlése
Egyetlen megosztási pillanatképet is törölhet.

# <a name="python-v12"></a>[Python V12](#tab/python)

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteSnapshot":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

---

## <a name="delete-a-file"></a>Fájl törlése

# <a name="python-v12"></a>[Python V12](#tab/python)

Egy fájl törléséhez hívja meg [delete_file](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.sharefileclient#delete-file---kwargs-).

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteFile":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Egy fájl törléséhez hívja meg [delete_file](/python/api/azure-storage-file/azure.storage.file.fileservice.fileservice?view=azure-python-previous&preserve-view=true#delete-file-share-name--directory-name--file-name--timeout-none-).

```python
file_service.delete_file('myshare', None, 'myfile')
```

---

## <a name="delete-share-when-share-snapshots-exist"></a>Megosztás törlése, ha a megosztási Pillanatképek léteznek

# <a name="python-v12"></a>[Python V12](#tab/python)

Pillanatképeket tartalmazó megosztás törléséhez hívja meg a [delete_sharet](/azure/developer/python/sdk/storage/azure-storage-file-share/azure.storage.fileshare.shareclient#delete-share-delete-snapshots-false----kwargs-) a következővel: `delete_snapshots=True` .

:::code language="python" source="~/azure-storage-snippets/files/howto/python/python-v12/file_share_ops.py" id="Snippet_DeleteShare":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Pillanatképeket tartalmazó megosztás csak akkor törölhető, ha az összes pillanatképet először törlik.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

---

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan kezelheti Azure Files a Python segítségével, az alábbi hivatkozásokat követve további információkat tudhat meg.

- [Python fejlesztői központ](/azure/developer/python/)
- [Az Azure Storage-szolgáltatások REST API-ja](/rest/api/azure/)
- [A Pythonhoz készült Microsoft Azure Storage SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage)
