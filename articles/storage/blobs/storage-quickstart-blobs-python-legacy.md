---
title: 'Gyors útmutató: Azure Blob Storage ügyféloldali kódtár v 2.1 a Pythonhoz'
description: Ebben a rövid útmutatóban egy tárfiókot és egy tárolót hoz létre egy objektumtárban (Blob Storage-fiókban). Ezután a Storage ügyféloldali kódtár 2.0-s verziójának használatával feltölt egy blobot az Azure Storage-ba, letölt egy blobot, és kilistázza a tárolóban lévő blobokat.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 34205caf03d2f2d7255f75ea6203c5572c4c429b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95523304"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>Gyors útmutató: Blobok kezelése a Python v 2.1 SDK-val

Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti a blobokat a Python használatával. A Blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatok tárolására képesek, beleértve a képeket, a dokumentumokat, a médiafolyamokat és az archiválási adatokhoz. Feltöltheti, letöltheti és listázhatja a blobokat, és létrehozhatja és törölheti a tárolókat.

> [!NOTE]
> Ez a rövid útmutató az Azure Blob Storage ügyféloldali függvénytárának örökölt verzióját használja. A legújabb verzió használatának megkezdéséhez lásd: gyors útmutató [: Blobok kezelése a Python V12 SDK-val](storage-quickstart-blobs-python.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy Storage-fiókot](../common/storage-account-create.md).
- [Python](https://www.python.org/downloads/).
- [A Pythonhoz készült Azure Storage SDK](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git) egy egyszerű Python-alkalmazás.  

A következő [git](https://git-scm.com/) -paranccsal töltheti le az alkalmazást a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

A Python-program áttekintéséhez nyissa meg a *example.py* fájlt a tárház gyökerében.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

Az alkalmazásban adja meg a tárfiók nevét és a fiókkulcsot, hogy létrehozhasson egy `BlockBlobService` objektumot.

1. Nyissa meg az *example.py* fájlt az IDE Megoldáskezelőjéből.

1. Cserélje le az `accountname` és az `accountkey` értékeket a Storage-fiók nevére és kulcsára:

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. Mentse és zárja be a fájlt.

## <a name="run-the-sample"></a>Minta futtatása

A minta program egy tesztoldalt hoz létre a *dokumentumok* mappában, feltölti a fájlt a blob Storage-ba, felsorolja a fájlban lévő blobokat, majd letölti a fájlt egy új névvel.

1. A függőségek telepítése:

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. Nyissa meg a minta alkalmazást:

    ```console
    cd storage-blobs-python-quickstart
    ```

1. Minta futtatása:

    ```console
    python example.py
    ```

    A következő kimenethez hasonló üzenetek jelennek meg:
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. A folytatás előtt lépjen a *dokumentumok* mappájába, és keresse meg a két fájlt.

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_ \<universally-unique-identifier\> _DOWNLOADED*

1. Ha megnyitja őket, láthatja, hogy megegyeznek.

    Használhat olyan eszközt is, mint a [Azure Storage Explorer](https://storageexplorer.com). Érdemes megtekinteni a blob Storage-ban található fájlokat. A Azure Storage Explorer egy ingyenes platformfüggetlen eszköz, amely lehetővé teszi a Storage-fiók adatainak elérését. 

1. Miután megtekintette a fájlokat, nyomja le bármelyik billentyűt a minta befejezéséhez és a tesztüzenetek törléséhez.

## <a name="learn-about-the-sample-code"></a>A mintakód megismerése

Most, hogy már tudja, mi a minta, nyissa meg a *example.py* fájlt, és tekintse meg a kódot.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz

Ebben a szakaszban létre fogja hozni az objektumok példányát és egy új tárolót, majd beállítja annak engedélyeit úgy, hogy a blobok nyilvánosak legyenek. Meghívja a tárolót `quickstartblobs` . 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

Először létre kell hozni a referenciákat a Blob Storage eléréséhez és kezeléséhez használt objektumokhoz. Ezek az objektumok egymásra épülnek, és mindegyiket a listában utánuk következő használja.

* Hozza létre a **BlockBlobService** objektum példányát, amely a tárfiók Blob szolgáltatására mutat. 

* Hozza létre a **CloudBlobContainer** objektum példányát. Ez azt a tárolót jelöli, amelyhez Ön hozzáfér. A rendszer tárolók használatával rendezi a blobokat, például a számítógépeken lévő mappák használatával rendszerezheti a fájlokat.

A felhőbeli blobtároló létrehozása után hozza létre a **CloudBlockBlob** objektum példányát, amely pontosan arra a blobra mutat, amelyre kíváncsi. Ezután elvégezheti a feltöltési, letöltési, másolási vagy egyéb műveleteket.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és a Blobok neveivel kapcsolatos további információkért lásd: [tárolók, blobok és metaadatok elnevezése és hivatkozása](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A blokkblobok legfeljebb 4,7 TB méretűek lehetnek, és az Excel-munkafüzetektől kezdve a nagyméretű videofájlokig bármit tartalmazhatnak. Ha egy fájlba szeretne írni, és további információ hozzáadására van szükség, a Blobok hozzáfűzésével is használhatja a naplózást. Az oldal blobokat elsősorban a virtuális merevlemezek (VHD-k) számára használják, amelyek szolgáltatásként működő virtuális gépek (IaaS VM-EK). A leggyakrabban használt elemek a blokkblobok. Ez a rövid útmutató blokk-blobokat használ.

Ha szeretne feltölteni egy fájlt a blobba, szüksége lesz a fájl teljes útvonalára, amelyet a helyi meghajtón található könyvtárnév és fájlnév összefűzésével kap meg. Ezt követően feltöltheti a fájlt a megadott útvonalra a `create_blob_from_path` metódus használatával. 

A mintakód létrehoz egy helyi fájlt, amelyet a rendszer a feltöltéshez és a letöltéshez használ, tárolja a rendszerfeltöltő fájlokat *full_path_to_fileként* és a blob nevét *local_file_nameként*. Ez a példa feltölti a fájlt a tárolóba `quickstartblobs` :

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

A Blob-tárolóval többféle feltöltési módszer használható. Ha például memóriastreamje van, használhatja a `create_blob_from_stream` metódust a `create_blob_from_path` helyett. 

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

Az alábbi kód létrehoz egy `generator` `list_blobs` metódust a metódushoz. A kód a tárolóban lévő Blobok listáján keresztül hurkot jelenít meg, és kiírja a nevét a konzolra.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>A blobok letöltése


Töltse le a blobokat a helyi lemezre a `get_blob_to_path` metódus használatával.
A következő kód letölti a korábban feltöltött blobot. A rendszer hozzáfűzi *_DOWNLOADED* a blob nevéhez, hogy mindkét fájlt látni tudja a helyi lemezen.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban feltöltött blobokra, a teljes tárolót törölheti a `delete_container` metódussal. Az egyes fájlok törléséhez használja a `delete_blob` metódust.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>Blobokkal rendelkező Python-alkalmazások fejlesztéséhez használható forrásanyagok

A Python-fejlesztés és a blob Storage szolgáltatással kapcsolatos további információkért tekintse meg ezeket a további forrásokat:

### <a name="binaries-and-source-code"></a>Bináris fájlok és forráskód

- Az Azure Storage-hoz tartozó [Python ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-python) megtekintheti, letöltheti és telepítheti a GitHubról.

### <a name="client-library-reference-and-samples"></a>Ügyféloldali kódtár – referencia és minták

- További információ a Python-ügyfél könyvtáráról: a [Pythonhoz készült Azure Storage kódtárak](/python/api/overview/azure/storage).
- Tekintse át a Python ügyféloldali kódtár használatával írt [Blob Storage-mintákat](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob).

## <a name="next-steps"></a>Következő lépések
 
Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a Python használatával. 

A Storage Explorer és a Blobokkal kapcsolatos további információkért lásd: az [Azure Blob Storage-erőforrások kezelése Storage Explorerokkal](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).