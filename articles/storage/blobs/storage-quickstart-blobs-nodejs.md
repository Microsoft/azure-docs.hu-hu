---
title: 'Gyors útmutató: Azure Blob Storage Library V12 – JavaScript'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure Blob Storage ügyféloldali kódtár 12-es verzióját a JavaScripthez a tárolók és Blobok blob (Object) tárolóban történő létrehozásához. Ezután megtudhatja, hogyan töltheti le a blobot a helyi számítógépére, és hogyan listázhatja ki a tárolóban található összes blobot.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 0883fd1546ac3b55ec4211e5048569556858de29
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179918"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Gyors útmutató: Blobok kezelése a JavaScript V12 SDK-val Node.js

Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti a blobokat a Node.js használatával. A Blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatok tárolására képesek, beleértve a képeket, a dokumentumokat, a médiafolyamokat és az archiválási adatokhoz. Feltöltheti, letöltheti és listázhatja a blobokat, és létrehozhatja és törölheti a tárolókat.

További források:

* [API-referenciadokumentáció](/javascript/api/@azure/storage-blob)
* [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob)
* [Csomag (node Package Manager)](https://www.npmjs.com/package/@azure/storage-blob)
* [Példák](../common/storage-samples-javascript.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy Storage-fiókot](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Beállítás

Ebből a szakaszból megtudhatja, hogyan készít elő egy projektet a JavaScripthez készült Azure Blob Storage ügyféloldali kódtáraval való együttműködésre.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy *blob-Gyorsindítás-V12* nevű JavaScript-alkalmazást.

1. A konzol ablakban (például cmd, PowerShell vagy bash) hozzon létre egy új könyvtárat a projekthez.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Váltson az újonnan létrehozott *blob-Gyorsindítás-V12* könyvtárra.

    ```console
    cd blob-quickstart-v12
    ```

1. Hozzon létre egy *package.js* nevű új szövegfájlt. Ez a fájl határozza meg a Node.js projektet. Mentse ezt a fájlt a *blob-Gyorsindítás-V12* könyvtárba. A fájl tartalma:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```

    Ha szeretné, saját nevet is helyezhet a `author` mezőbe.

### <a name="install-the-package"></a>A csomag telepítése

Miközben továbbra is a *blob-Gyorsindítás-V12* könyvtárban található, telepítse az Azure Blob Storage ügyféloldali kódtárat a JavaScript-csomaghoz a `npm install` parancs használatával. Ez a parancs beolvassa a *package.js* fájlt, és telepíti az Azure Blob Storage ügyféloldali kódtárat a JavaScript-csomaghoz, és az összes olyan könyvtárat, amelytől függ.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Nyisson meg egy másik új szövegfájlt a Kódszerkesztőben
1. `require`Hívások hozzáadása az Azure és Node.js modulok betöltéséhez
1. A program struktúrájának létrehozása, beleértve az alapszintű kivételek kezelését

    A kód a következő:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const { v1: uuidv1} = require('uuid');

    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }

    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Mentse az új fájlt *blob-quickstart-v12.jsként* a *blob-Gyorsindítás-V12* könyvtárba.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva. A strukturálatlan adatok olyan adatok, amelyek nem követnek egy adott adatmodellt vagy definíciót, például szöveges vagy bináris adatok. A blob Storage háromféle típusú erőforrást kínál:

* A Storage-fiók
* Egy tároló a Storage-fiókban
* A tárolóban lévő blob

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![BLOB Storage-architektúra ábrája](./media/storage-blobs-introduction/blob1.png)

Használja az alábbi JavaScript-osztályokat a következő erőforrásokkal való interakcióhoz:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): a `BlobServiceClient` osztály lehetővé teszi az Azure Storage-erőforrások és a blob-tárolók kezelését.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): a `ContainerClient` osztály lehetővé teszi az Azure Storage-tárolók és a Blobok kezelését.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): a `BlobClient` osztály lehetővé teszi az Azure Storage-Blobok kezelését.

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő műveleteket az Azure Blob Storage ügyféloldali kódtára használatával a JavaScripthez:

* [A kapcsolati sztring lekérése](#get-the-connection-string)
* [Tároló létrehozása](#create-a-container)
* [Blobok feltöltése tárolóba](#upload-blobs-to-a-container)
* [Tárolóban lévő blobok kilistázása](#list-the-blobs-in-a-container)
* [Blobok letöltése](#download-blobs)
* [Tároló törlése](#delete-a-container)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alábbi kód a Storage- [kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóból kéri le a Storage-fiókhoz tartozó kapcsolatok karakterláncát.

Adja hozzá ezt a kódot a `main` függvényhez:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Tároló létrehozása

Döntse el az új tároló nevét. Az alábbi kód egy UUID értéket fűz hozzá a tároló nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> A tárolók nevei csak kisbetűket tartalmazhatnak. A tárolók és blobok elnevezésével kapcsolatos további információkért lásd a [tárolók, blobok és metaadatok elnevezését és hivatkozását](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Hozza létre a [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) osztály egy példányát a [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) metódus meghívásával. Ezután hívja meg a [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) metódust egy tárolóra mutató hivatkozás beszerzéséhez. Végül hívja a [create (létrehozás](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) ) lehetőséget, hogy ténylegesen létrehozza a tárolót a Storage-fiókban.

Adja hozzá ezt a kódot a függvény végéhez `main` :

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Blobok feltöltése tárolóba

A következő kódrészlet:

1. Egy blobba feltöltendő szöveges karakterláncot hoz létre.
1. Beolvas egy [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) objektumra mutató hivatkozást úgy, hogy meghívja a [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) metódust a [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) a [tároló létrehozása](#create-a-container) szakaszban.
1. Feltölti a szöveges karakterláncot a blobba a [feltöltési](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) metódus meghívásával.

Adja hozzá ezt a kódot a függvény végéhez `main` :

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

A tárolóban lévő Blobok listázása a [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) metódus meghívásával. Ebben az esetben a tárolóhoz csak egy blob lett hozzáadva, így a listázási művelet csak ezt az egy blobot adja vissza.

Adja hozzá ezt a kódot a függvény végéhez `main` :

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Blobok letöltése

Töltse le a korábban létrehozott blobot a [letöltési](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) módszer meghívásával. A példában szereplő kód egy nevű segítő függvényt tartalmaz `streamToString` , amely egy Node.js olvasható adatfolyam karakterláncba való olvasására szolgál.

Adja hozzá ezt a kódot a függvény végéhez `main` :

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Adja hozzá ezt a segítő függvényt a függvény *után* `main` :

```javascript
// A helper function used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
  return new Promise((resolve, reject) => {
    const chunks = [];
    readableStream.on("data", (data) => {
      chunks.push(data.toString());
    });
    readableStream.on("end", () => {
      resolve(chunks.join(""));
    });
    readableStream.on("error", reject);
  });
}
```

### <a name="delete-a-container"></a>Tároló törlése

A következő kód törli az alkalmazás által létrehozott erőforrásokat, ha eltávolítja a teljes tárolót a [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) metódus használatával. Ha szeretné, törölheti a helyi fájlokat is.

Adja hozzá ezt a kódot a függvény végéhez `main` :

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás létrehoz egy szöveges karakterláncot, és feltölti a blob Storage-ba. A példa ezután felsorolja a tárolóban lévő blobokat, letölti a blobot, és megjeleníti a letöltött adatfájlokat.

A konzol parancssorában navigáljon a *blob-quickstart-v12.js* fájlt tartalmazó könyvtárra, majd hajtsa végre a következő `node` parancsot az alkalmazás futtatásához.

```console
node blob-quickstart-v12.js
```

Az alkalmazás kimenete az alábbi példához hasonló:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Lépjen be a hibakeresőbe, és vizsgálja meg a [Azure Portal](https://portal.azure.com) a folyamat során. Ellenőrizze, hogy a tároló létrehozása folyamatban van-e. A blobot megnyithatja a tárolón belül, és megtekintheti a tartalmat.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan tölthet fel, tölthet le és listázhat blobokat a JavaScript használatával.

Az oktatóanyagok, minták, rövid útmutatók és egyéb dokumentációk a következő címen találhatók:

> [!div class="nextstepaction"]
> [Azure a JavaScript fejlesztői központhoz](/azure/developer/javascript/)

* Az Azure Blob Storage-t használó webalkalmazások üzembe helyezésének megismeréséhez lásd [: oktatóanyag: képadatok feltöltése a felhőbe az Azure Storage](./storage-upload-process-images.md?preserve-view=true&tabs=javascript) használatával
* Ha szeretné megtekinteni a blob Storage-beli példákat, folytassa az [Azure Blob Storage ügyféloldali kódtár V12 JavaScript-mintáit](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples).
* További információért lásd az [Azure Blob Storage ügyféloldali kódtárat a javascripthez](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)című témakört.
