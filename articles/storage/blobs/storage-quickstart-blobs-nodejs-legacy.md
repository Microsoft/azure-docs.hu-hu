---
title: 'Gyors útmutató: Azure Blob Storage ügyféloldali kódtár v10 a JavaScripthez'
description: Blobok és tárolók létrehozása, feltöltése és törlése a Node.jsban az Azure Storage ügyféloldali kódtár v10 a JavaScripthez
author: twooley
ms.author: twooley
ms.date: 01/19/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: ddcce3e624de7e8bed62783bdc22c5c4211e19e2
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280105"
---
# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-nodejs"></a>Gyors útmutató: Blobok kezelése a JavaScript v10 SDK-val Node.js

Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti a blobokat a Node.js használatával. A Blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatok tárolására képesek, beleértve a képeket, a dokumentumokat, a médiafolyamokat és az archiválási adatokhoz. Feltöltheti, letöltheti, listázhatja és törölheti a blobokat, és kezelheti a tárolókat.

> [!NOTE]
> Ez a rövid útmutató az Azure Blob Storage ügyféloldali függvénytárának örökölt verzióját használja. A legújabb verzió használatának megkezdéséhez lásd: gyors útmutató [: Blobok kezelése a JavaScript V12 SDK-val Node.js](storage-quickstart-blobs-nodejs.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy Storage-fiókot](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

## <a name="download-the-sample-application"></a>A mintaalkalmazás letöltése

A rövid útmutatóban használt [mintaalkalmazás](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git) egy egyszerű Node.js-konzolalkalmazás. Első lépésként klónozza az adattárat a gépre a következő paranccsal:

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

Ezután módosítsa az alkalmazás mappáit:

```bash
cd azure-storage-js-v10-quickstart
```

Most nyissa meg a mappát kedvenc kódszerkesztő környezetében.

## <a name="configure-your-storage-credentials"></a>A tároló hitelesítő adatainak konfigurálása

Az alkalmazás futtatása előtt meg kell adnia a tárfiókhoz tartozó hitelesítő adatokat. A mintatadattár tartalmaz egy *.env.example* nevű fájlt. Nevezze át a fájlt a *.example* bővítmény eltávolításával, amelynek eredményeként egy *.env* nevű fájl jön létre. A *.env* fájlban adja meg a fióknév és a hozzáférési kulcs értékét az *AZURE_STORAGE_ACCOUNT_NAME* és az *AZURE_STORAGE_ACCOUNT_ACCESS_KEY* kulcs után.

## <a name="install-required-packages"></a>Szükséges csomagok telepítése

Az alkalmazás könyvtárában futtassa az *npm install* parancsot az alkalmazáshoz szükséges csomagok telepítéséhez.

```bash
npm install
```

## <a name="run-the-sample"></a>Minta futtatása

A függőségek telepítését követően a következő paranccsal futtathatja a mintát:

```bash
npm start
```

Az alkalmazás kimenete az alábbi példához hasonló lesz:

```bash
Container "demo" is created
Containers:
 - container-one
 - container-two
 - demo
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Ha ehhez a rövid útmutatóhoz új Storage-fiókot használ, akkor előfordulhat, hogy csak a "*tárolók:*" felirat alatt látható *bemutató* tárolót fogja látni.

## <a name="understanding-the-code"></a>A kód értelmezése

A minta első része osztályokat és függvényeket importál az Azure Blob Storage-névtérből. Az egyes importált elemek a mintában való használatukkor részletesen is be lesznek mutatva.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

A hitelesítő adatokat a rendszer környezeti változókból olvassa be a vonatkozó környezetnek megfelelően.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

A *dotenv* modul tölti be a környezeti változókat, amikor az alkalmazást hibakeresési céllal helyileg futtatja. Az értékek egy *.env* fájlban vannak meghatározva. A modul innen tölti be őket az aktuális végrehajtási környezetbe. Éles környezetben a kiszolgáló konfigurációja biztosítja ezeket az értékeket, ezért ez a kód csak akkor fut, amikor a szkript *nem* „éles” környezetben fut.

A következő néhány blokkot a fájlrendszerhez való jobb illeszkedés érdekében importáljuk.

```javascript
const fs = require('fs');
const path = require('path');
```

A modulok rendeltetése a következő: 

- Az *fs* a Node.js natív modulja a fájlrendszer használatához

- A *path* a fájl abszolút elérési útjának meghatározásához szükséges, amelyet a fájl a Blob Storage-be való feltöltése során használatos

Ezután a környezeti változók értékeinek beolvasása és konstans értékekben való tárolása történik.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
A következő konstansok segítenek feltárni a fájlméretszámítás célját a feltöltési műveletek során.

```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```

Az API által kezdeményezett kérelmek egy adott intervallum után időkorlátra állíthatók be. Az [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-legacy&preserve-view=true) osztály feladata a kérelmek időtúllépésének kezelése, és a mintában az időtúllépések meghatározása a következő állandó alkalmazásával történik.

```javascript
const ONE_MINUTE = 60 * 1000;
```

### <a name="calling-code"></a>Hívó kód

A JavaScript *async/await* szintaxisának támogatása érdekében a teljes hívókód egy *execute* nevű függvénybe van csomagolva. Tehát a rendszer az *execute* függvényt fogja hívni, amelyet egy ígéretként kezel.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```

A teljes alábbi kód az execute függvényen belül fut, amelybe a `// commands...` megjegyzés kerül.

Első lépésként a releváns változókat deklaráljuk a nevek és mintatartalmak hozzárendeléséhez, valamint a Blob Storage-be feltölteni kívánt helyi fájl kijelöléséhez.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

A fiók hitelesítő adataival egy folyamatot hozunk létre, amelynek a feladata lesz azt kezelni, hogy a kérések hogyan lesznek a REST API-ra küldve. A folyamatok szálbiztosak, és az újrapróbálkozási szabályzatok, a naplózás, a HTTP-válaszdeszerializálási szabályok és egyebek logikáját határozzák meg.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```

Ebben a kódblokkban a következő osztályokat használjuk:

- A [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-legacy&preserve-view=true) osztály a tárfiókok hitelesítő adatainak beburkolását végzi a kérelemfolyamatoknak való átadás érdekében.

- A [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-legacy&preserve-view=true) osztály az új folyamatok létrehozására szolgál.

- A [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-legacy&preserve-view=true) osztály egy, a REST API-ban használt URL modellezését végzi. Az osztály példányaival olyan műveletek hajthatók végre, mint például a tárolók listázása és a környezeti információk megadása a tárolók URL-címeinek létrehozásához.

A *ServiceURL*-példányt a [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-legacy&preserve-view=true)- és [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-legacy&preserve-view=true)-példányokkal együtt használva kezelhetők a tárfiókban lévő tárolók és blobok.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```

A *containerURL* és a *blockBlobURL* változók végig használatban maradnak a mintában a tárfiókon végrehajtott műveletekben. 

Ezen a ponton a tároló nem létezik a tárfiókban. A *ContainerURL*-példány jelöli azt az URL-címet, amelyet használhat. A példány használatával hozhatja létre és törölheti a tárolót. A tároló helye megfelel egy, a következőhöz hasonló helynek felel meg:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

A *blockBlobURL* az egyes blobok kezelésére szolgál, és a blobok tartalmának feltöltéséhez, letöltéséhez és törléséhez használható. Az itt bemutatott URL a következő helyhez hasonló:

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```

Ahogy a tároló, úgy még a blokkblob sem létezik. A *blockBlobURL* változót később használjuk majd a blob tartalmak feltöltésével való létrehozásához.

### <a name="using-the-aborter-class"></a>Az Aborter osztály használata

Az API által kezdeményezett kérelmek egy adott intervallum után időkorlátra állíthatók be. A *megszakítási* osztály feladata a kérelmek időkorlátjának kezelése. A következő kód egy olyan környezetet hoz létre, amelyben a kérelmek halmaza 30 percet vesz igénybe.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```

Az Aborterek (megszakítók) lehetővé teszik a kérelmek szabályzását, mivel a segítségükkel:

- kijelölhető egy adott kérelemköteg számára rendelkezésre álló idő,
- kijelölhető, hogy egy adott egyéni kérelem milyen hosszan futhat a kötegben,
- visszavonhatók a kérelmek,
- az *Aborter.none* statikus tag használatával megakadályozható, hogy a kérelmek egyáltalán időtúllépésbe kerüljenek

### <a name="create-a-container"></a>Tároló létrehozása

A tárolók létrehozásához a *ContainerURL* osztály *create* metódusa használható.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```

Mivel a tároló neve a *ContainerURL.fromServiceURL(serviceURL, containerName)* meghívásakor definiálva lesz, a tároló létrehozásához elegendő a *create* metódust meghívni.

### <a name="show-container-names"></a>Tárolónevek megjelenítése

A fiókok rengeteg tárolót tartalmazhatnak. A következő kód bemutatja, hogyan lehet a tárolókat szegmentált módon listázni, aminek segítségével nagy számú tárolót tekinthet át. A *showContainerNames* függvénynek *ServiceURL*- és *Aborter*-példányokat adunk át.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```

A *showContainerNames* függvény a *listContainersSegment* metódus használatával tárolónevek kötegeit kéri le a tárfiókról.

```javascript
async function showContainerNames(aborter, serviceURL) {
    let marker = undefined;

    do {
        const listContainersResponse = await serviceURL.listContainersSegment(aborter, marker);
        marker = listContainersResponse.nextMarker;
        for(let container of listContainersResponse.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```

A válasz visszaérkezésekor a *containerItems* többszöri végrehajtásával adható át a név a konzolnak. 

### <a name="upload-text"></a>Szöveg feltöltése

A szövegek feltöltéséhez a blobba használja az *upload* metódust.

```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```

Itt a szöveget és annak hosszát adjuk át a metódusnak.

### <a name="upload-a-local-file"></a>Helyi fájl feltöltése

A helyi fájlok a tárolóba való feltöltéséhez a tároló URL-címére és a fájl elérési útjára van szükség.

```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```

Az *uploadLocalFile* függvény meghívja az *uploadFileToBlockBlob* függvényt, amely a fájl elérési útját és a blokkblob céljának egy példányát veszi fel argumentumként.

```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```

### <a name="upload-a-stream"></a>Stream feltöltése

A streamek feltöltése is támogatott. A minta egy helyi fájlt streamként megnyit és átad az upload metódusnak.

```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```

Az *uploadStream* függvény az *uploadStreamToBlockBlob* meghívásával tölti fel a streamet a tárolóba.

```javascript
async function uploadStream(aborter, containerURL, filePath) {
    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```

A feltöltés során az *uploadStreamToBlockBlob* pufferek lefoglalásával biztosítja a stream adatainak gyorsítótárazását, amennyiben újra kellene próbálkozni. A *maxBuffers* érték jelzi, hogy legfeljebb hány puffer használható, mivel mindegyik puffer külön fájlfeltöltési kérelmet hoz létre. Ideális esetben a több puffer nagyobb sebességet eredményez, azonban így nő a memóriahasználat. A pufferek kellően magas száma esetén a feltöltési sebesség elér egy korlátot, és már a hálózat vagy a meghajtó jelenti a szűk keresztmetszetet az ügyfél helyett.

### <a name="show-blob-names"></a>Blobnevek megjelenítése

Ahogy az egyes fiókok több tárolót is tartalmazhatnak, az egyes tárolók maguk is potenciálisan hatalmas mennyiségű blobot tartalmazhatnak. A tárolóban lévő egyes blobok a *ContainerURL* osztály egy példányán keresztül érhetők el.

```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```

A *showBlobNames* függvény a *listBlobFlatSegment* meghívásával kéri le a blobkötegeket a tárolókból.

```javascript
async function showBlobNames(aborter, containerURL) {
    let marker = undefined;

    do {
        const listBlobsResponse = await containerURL.listBlobFlatSegment(Aborter.none, marker);
        marker = listBlobsResponse.nextMarker;
        for (const blob of listBlobsResponse.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```

### <a name="download-a-blob"></a>Blob letöltése

A létrehozását követően a blob tartalma a *download* metódussal tölthető le.

```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = await streamToString(downloadResponse.readableStreamBody);
console.log(`Downloaded blob content: "${downloadedContent}"`);
```

A rendszer a választ streamként adja vissza. Ebben a példában az adatfolyamot a rendszer a következő *streamToString* segítő függvény használatával konvertálja karakterlánccá.

```javascript
// A helper method used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", data => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
}
```

### <a name="delete-a-blob"></a>Blob törlése

A blobok a *BlockBlobURL*-példányok *delete* metódusával törölhetők a tárolóból.

```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```

### <a name="delete-a-container"></a>Tároló törlése

A tárolók a *ContainerURL*-példányok *delete* metódusával törölhetők a tárfiókból.

```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kódminta végén a tárfiókba írt összes adat automatikusan törölve lesz. 

## <a name="next-steps"></a>Következő lépések

Ez a rövid útmutató azt mutatja be, hogyan kezelhetők a blobok és tárolók az Azure Blob Storage-tárfiókokban a Node.js használatával. Az SDK használatával kapcsolatos bővebb információkért lásd a GitHub-adattárat.

> [!div class="nextstepaction"]
> [Azure Storage v10 SDK a JavaScript-tárházhoz](https://github.com/Azure/azure-storage-js) 
>  Az [Azure Storage JavaScript API-referenciája](/javascript/api/overview/azure/storage-overview)
