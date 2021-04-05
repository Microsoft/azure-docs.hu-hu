---
title: Azure rövid útmutató – Blob létrehozása objektumtárban PHP használatával | Microsoft Docs
description: Gyorsan megtudhatja, hogyan vihet át objektumokat az Azure Blob Storage-ba a PHP használatával. Blokk-Blobok feltöltése, letöltése és listázása egy tárolóban az Azure Blob Storage-ban.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: af35927abfa491891dffe2c2397d6daf8c1ccde2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543119"
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>Objektumok továbbítása Azure Blob-tárolókra és -tárolókról a PHP használatával
A rövid útmutató azt ismerteti, hogyan használható a PHP blokkblobok feltöltésére, letöltésére és listázására egy, az Azure Blob Storage-ban található tárolóban. 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Győződjön meg arról, hogy a következő további előfeltételek vannak telepítve:

* [PHP](https://php.net/downloads.php)
* [PHP-hez készült Azure Storage SDK](https://github.com/Azure/azure-storage-php)

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése
A rövid útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git) egy egyszerű PHP-alkalmazás.  

A [git](https://git-scm.com/) használatával letöltheti az alkalmazás egy példányát a fejlesztői környezetbe. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

Ez a parancs a helyi git mappába klónozza az adattárat. A PHP mintaalkalmazás megnyitásához keresse meg a storage-blobs-php-quickstart mappát, és nyissa meg a phpqs.php fájlt.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>A tárolási kapcsolati sztring konfigurálása
Az alkalmazásban meg kell adnia a tárfiók nevét és a fiókkulcsot, hogy létrehozhasson egy **BlobRestProxy** példányt az alkalmazásból. Javasoljuk, hogy ezeket az azonosítókat egy környezeti változóban tárolja az alkalmazást futtató helyi gépen. A környezeti változó létrehozásához kövesse az alábbi példák egyikét az operációs rendszerének megfelelően. A **youraccountname** és a **youraccountkey** értékeket cserélje le a fiók nevére, illetve kulcsára.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windows"></a>[Windows](#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>A környezet konfigurálása
Fogja a mappát a helyi git-mappából, és helyezze egy, a PHP-kiszolgáló által kiszolgált címtárba. Ezután nyisson meg egy parancssort, amelynek a hatóköre ugyanaz a címtár, és írja be a következőt: `php composer.phar install`

## <a name="run-the-sample"></a>Minta futtatása
Ez a minta egy tesztfájlt hoz létre a „.” mappában. A mintaprogram feltölti a tesztfájlt a Blob-tárolóba, listázza a tárolóban található blobokat, majd letölti a fájlt egy új néven. 

Futtassa a mintát. Az alábbi kimenet példa az alkalmazás futtatásakor kapott kimenetre:
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
Amikor lenyomja a megjelenített gombot, a mintaprogram törli a Storage-tárolót és a fájlokat. Mielőtt továbblépne, ellenőrizze a két fájlt a kiszolgálója mappájában. Ha megnyitja őket, láthatja, hogy megegyeznek.

Az [Azure Storage Explorert](https://storageexplorer.com) vagy egy ahhoz hasonló eszközt is használhat, ha szeretné a fájlt megtekinteni a blobtárolóban. Az Azure Storage Explorer egy ingyenes, platformfüggetlen eszköz, amellyel elérheti a tárfiókjával kapcsolatos információkat. 

A fájlok ellenőrzése után nyomja le bármelyik billentyűt a bemutató befejezéséhez és a tesztfájlok törléséhez. Most, hogy tisztában van a minta működésével, nyissa meg az example.rb fájlt, és tekintse meg a kódot. 

## <a name="understand-the-sample-code"></a>A mintakód értelmezése

A következőkben áttekintjük a mintakódot, és értelmezzük, hogyan működik.

### <a name="get-references-to-the-storage-objects"></a>Referenciák beszerzése a tárolóobjektumokhoz
Az első teendő a referenciák létrehozása a Blob-tárolóhoz való hozzáféréshez és kezeléséhez használt objektumokhoz. Ezek az objektumok egymásra épülnek, és mindegyiket a listában utánuk következő használja.

* Hozzon létre egy példányt az Azure Storage **BlobRestProxy** objektumából a kapcsolati hitelesítő adatok beállításához. 
* Hozza létre a **BlobService** objektumot, amely a tárfiók Blob szolgáltatására mutat. 
* Hozza létre a **Container** objektumot. Ez azt a tárolót képviseli, amelyhez Ön hozzáfér. A tárolók a blobok csoportosítására használhatók, hasonlóan ahhoz, ahogyan a számítógépen a mappákkal rendszerezi a fájlokat.

A **blobClient** tárolóobjektum létrehozása után létrehozhatja a **Block** blobobjektumot, amely pontosan arra a blobra mutat, amelyre kíváncsi. Ezután elvégezheti a feltöltési, letöltési, másolási vagy egyéb műveleteket.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos részletekért lásd a [tárolók, blobok és metaadatok elnevezésével és hivatkozásával](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) foglalkozó cikket.

Ebben a szakaszban létre fogja hozni az Azure Storage-kliens és a blobszolgáltatás objektumának egy példányát, valamint egy új tárolót, majd beállítja annak engedélyeit úgy, hogy a blobok nyilvánosak legyenek. A tároló neve **quickstartblobs**. 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>Blobok feltöltése a tárolóba

A Blob Storage támogatja a blokkblobokat, a hozzáfűző blobokat és a lapblobokat. A leggyakrabban használt elemek a blokkblobok, és ez a rövid útmutató is ezeket használja.  

Ha szeretne feltölteni egy fájlt a blobba, szüksége lesz a fájl teljes útvonalára, amelyet a helyi meghajtón található könyvtárnév és fájlnév összefűzésével kap meg. Ezt követően feltöltheti a fájlt a megadott útvonalra a **createblockblob()** metódus használatával. 

A mintakód egy helyi fájlt tölt fel az Azure-ba. A fájlt a rendszer **myfile** néven tárolja, a blob pedig **fileToUpload** néven szerepel a kódban. A következő példa feltölti a fájlt a **quickstartblobs** nevű tárolóba.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

Egy blokkblob tartalmának részleges frissítéséhez használja a **createblocklist()** metódust. A blokkblobok legfeljebb 4,7 TB méretűek lehetnek, és az Excel-munkafüzetektől kezdve a nagyméretű videofájlokig bármit tartalmazhatnak. A lapblobok elsősorban az IaaS virtuális gépek biztonsági mentéséhez szükséges VHD-fájlokhoz használatosak. A hozzáfűző blobok a naplózáshoz használhatók, például amikor egy fájlba szeretne írni, majd folyamatosan újabb információkat szeretne hozzáadni. A hozzáfűző blobokat egyetlen írót tartalmazó modellek esetében érdemes használni. A blobtárolókban tárolt objektumok a legtöbb esetben blokkblobok.

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

A tárolóban található fájlok listáját a **listblobs()** metódus használatával kérheti le. A következő kód lekéri a blobok listáját, majd végighalad rajtuk, és megjeleníti a tárolóban talált blobok nevét.  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>Blobok tartalmának lekérése

A blobok tartalmát a **getBlob()** metódus segítségével kérheti le. A következő kód megjeleníti a korábbi szakaszban feltöltött blob tartalmát.

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs szüksége az ebben a rövid útmutatóban feltöltött blobokra, a teljes tárolót törölheti a **deletecontainer()** metódussal. Ha már nincs szüksége a létrehozott fájlokra, a **deleteblob()** metódus használatával törölheti őket.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>Blobokkal rendelkező PHP-alkalmazások fejlesztéséhez használható forrásanyagok

Blob Storage-tárolókat alkalmazó PHP-alkalmazások fejlesztéséhez tekintse át az alábbi további forrásanyagokat is:

- Az Azure Storage-hoz készült [PHP ügyféloldali kódtár forráskódját](https://github.com/Azure/azure-storage-php) megtekintheti, letöltheti és telepítheti a GitHubról.
- Tekintse át a PHP ügyféloldali kódtár használatával írt [Blob Storage-mintákat](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob).

## <a name="next-steps"></a>Következő lépések
 
Ennek a rövid útmutatónak a segítségével megtanulta, hogyan vihetők át fájlok egy helyi lemez és az Azure Blob Storage között a PHP használatával. Ha bővebb információra van szüksége a PHP használatával kapcsolatban, lépjen tovább a PHP fejlesztői központunkba.

> [!div class="nextstepaction"]
> [PHP fejlesztői központ](https://azure.microsoft.com/develop/php/)


További információk a Storage Explorerről és a blobokról: [Azure Blob Storage-erőforrások kezelése a Storage Explorer használatával](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).