---
title: 'Gyors útmutató: Azure Blob Storage ügyféloldali kódtár – Ruby'
description: Hozzon létre egy Storage-fiókot és egy tárolót az Azure Blob Storageban. A Storage ügyféloldali kódtár használatával hozzon létre egy blobot, töltsön le egy blobot, és sorolja fel a tárolóban lévő blobokat.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ec3fc490466f5fce36b67b2f3744e4ee5cc0ae79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96781096"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>Gyors útmutató: Azure Blob Storage-ügyféloldali kódtár a Rubyhoz

Megtudhatja, hogyan hozhat létre, tölthet le és listázhat blobokat az Microsoft Azure Blob Storage tárolójában a Ruby használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Győződjön meg arról, hogy a következő további előfeltételek vannak telepítve:

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Azure Storage-kódtár a Ruby számára](https://github.com/azure/azure-storage-ruby)a [RubyGem-csomag](https://rubygems.org/gems/azure-storage-blob)használatával:

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) egy egyszerű Ruby-alkalmazás.

A [git](https://git-scm.com/) használatával letöltheti az alkalmazás egy példányát a fejlesztői környezetbe. Ez a parancs a tárházat a helyi gépre klónozott:

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

Navigáljon a *Storage-Blobs-Ruby-Gyorsindítás* mappára, és nyissa meg a *example. RB* fájlt a Kódszerkesztő alkalmazásban.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása

Adja meg a Storage-fiók nevét és a [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) az alkalmazáshoz tartozó új példány létrehozásához.

A *example. RB* fájl következő kódja új [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) objektumot hoz létre. Az *accountname* és az *accountkey* értékeket cserélje le a fiók nevére, illetve kulcsára.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>Minta futtatása

A minta létrehoz egy tárolót a Blob Storageban, létrehoz egy új blobot a tárolóban, listázza a tárolóban lévő blobokat, és letölti a blobot egy helyi fájlba.

Futtassa a mintát. Az alábbi példa az alkalmazás futtatásának kimenetét szemlélteti:

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Amikor lenyomja az ENTER billentyűt a folytatáshoz, a minta program törli a tárolót és a helyi fájlt. A folytatás előtt tekintse meg a letöltött fájl *dokumentumok* mappáját.

A Storage-fiókban található fájlok megtekintéséhez [Azure Storage Explorer](https://storageexplorer.com) is használhatja. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat.

A fájlok ellenőrzése után nyomja le az ENTER billentyűt a tesztoldal törléséhez és a bemutató befejezéséhez. A kód megkereséséhez nyissa meg a *example. RB* fájlt.

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következő lépés a mintakód áttekintése, hogy megtudja, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz

Első lépésként hozza létre a Blob Storage eléréséhez és kezeléséhez használt objektumok példányait. Ezek az objektumok egymásra épülnek. Mindegyiket a listában utána következő használja.

- Hozzon létre egy példányt az Azure Storage [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) objektumából a kapcsolati hitelesítő adatok beállításához.
- Hozza létre a [tároló](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container) objektumot, amely az elérni kívánt tárolót jelöli. A tárolók a blobok csoportosítására használhatók, hasonlóan ahhoz, ahogyan a számítógépen a mappákkal rendszerezi a fájlokat.

Ha rendelkezik a Container objektummal, létrehozhat egy [blokk](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) blob-objektumot, amely arra az adott blobra mutat, amelyben érdekli. Blobok létrehozásához, letöltéséhez és másolásához használja a [Block](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) objektumot.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és a Blobok neveivel kapcsolatos további információkért lásd: [tárolók, blobok és metaadatok elnevezése és hivatkozása](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

A következő mintakód:

- Új tároló létrehozása
- Beállítja a tárolóra vonatkozó engedélyeket, hogy a Blobok nyilvánosak legyenek. A tároló neve *quickstartblobs* , amelyhez egyedi azonosító van hozzáfűzve.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>BLOB létrehozása a tárolóban

Blob Storage támogatja a Blobok letiltását, a Blobok hozzáfűzését és az oldal blobokat. BLOB létrehozásához hívja meg az [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) metódust, amely a blobhoz tartozó adatforgalmat eredményezi.

A következő példa létrehoz egy *QuickStart_* nevű blobot egy egyedi azonosítóval és egy *. txt* kiterjesztéssel a korábban létrehozott tárolóban.

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

A blokkos Blobok akár 4,7 TB-os méretűek is lehetnek, és a számolótáblák és a nagyméretű videofájlok is lehetnek. Az oldal Blobok elsődlegesen a IaaS virtuális gépeket futtató VHD-fájlokhoz használatosak. A hozzáfűző blobokat általában a naplózáshoz használják, például ha egy fájlba szeretne írni, majd további információk hozzáadására van szükség.

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

Szerezze be a tárolóban található fájlok listáját a [list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method) metódus használatával. A következő kód lekéri a Blobok listáját, majd megjeleníti a nevüket.

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Blob letöltése

Töltse le a blobot a helyi lemezre a [get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method) metódus használatával. A következő kód letölti az előző szakaszban létrehozott blobot.

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség blobra, a [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) használatával távolítsa el. Törölje a teljes tárolót a [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method) metódus használatával. A tároló törlése a tárolóban tárolt összes blobot is törli.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Blobokkal rendelkező Ruby-alkalmazások fejlesztéséhez használható forrásanyagok

Tekintse meg ezeket a további forrásokat a Ruby-fejlesztéshez:

- Az Azure Storage-hoz készült [Ruby ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-ruby) a GitHubon tekintheti meg és töltheti le.
- Ismerkedjen meg a Ruby ügyféloldali kódtár használatával írt [Azure-mintákkal](/samples/browse/?products=azure&languages=ruby) .
- [Minta: Első lépések az Azure Storage-ban a Rubyban](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan vihetők át fájlok az Azure Blob Storage és egy helyi lemez között a Ruby használatával. Ha többet szeretne megtudni a Blob Storage használatáról, folytassa a Storage-fiók áttekintésével.

> [!div class="nextstepaction"]
> [Tárfiókok áttekintése](../common/storage-account-overview.md)

A Storage Explorer és a Blobokkal kapcsolatos további információkért lásd: az [Azure Blob Storage-erőforrások kezelése Storage Explorerokkal](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
