---
title: Képadatok feltöltése a felhőbe az Azure Storage segítségével | Microsoft Docs
description: Az Azure Blob Storage és a Web Apps használatával tárolhatja az alkalmazásadatok egy Storage-fiókba. Ez az oktatóanyag egy webalkalmazást hoz létre, amely az Azure Storage-ban tárolja és megjeleníti a lemezképeket.
author: mhopkins-msft
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/24/2020
ms.author: mhopkins
ms.reviewer: dineshm
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: b50aadfa16ed95bacb5247187c15489a1b017d39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676574"
---
# <a name="tutorial-upload-image-data-in-the-cloud-with-azure-storage"></a>Oktatóanyag: képadatok feltöltése a felhőbe az Azure Storage szolgáltatással

Ez az oktatóanyag egy sorozat első része. Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy webalkalmazást. A webalkalmazás az Azure Blob Storage ügyféloldali függvénytárát használja a lemezképek Storage-fiókba való feltöltéséhez. Ha elkészült, egy olyan webalkalmazás fog rendelkezni, amely az Azure Storage-ban tárolja és megjeleníti a lemezképeket.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

![Rendszerkép-alkalmazás a .NET-ben](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

![Rendszerkép-Resizer alkalmazás JavaScriptben](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]

> * Tárfiók létrehozása
> * Tároló létrehozása és az engedélyek beállítása
> * Hozzáférési kulcs lekérése
> * Webalkalmazás üzembe helyezése az Azure-ban
> * Alkalmazásbeállítások konfigurálása
> * Interakció a webalkalmazással

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure-előfizetésre. Mielőtt elkezdené, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

A parancssori felület helyi telepítéséhez és használatához futtassa az Azure CLI 2.0.4 vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.  

A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.

```azurecli
az group create --name myResourceGroup --location southeastasia
```

```powershell
az group create --name myResourceGroup --location southeastasia
```

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A minta feltölti a képeket egy Azure Storage-fiókban található blob-tárolóba. A tárfiók egy egyedi névteret biztosít az Azure Storage-adatobjektumok tárolásához és hozzáféréséhez. Az [az storage account create](/cli/azure/storage/account) paranccsal hozzon létre egy tárfiókot a létrehozott erőforráscsoportban.

> [!IMPORTANT]
> Az oktatóanyag 2. részében a blob Storage-Azure Event Gridt használja. Győződjön meg arról, hogy a Storage-fiókot egy olyan Azure-régióban hozza létre, amely támogatja a Event Grid. A támogatott régiók listáját lásd: Azure- [termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all).

A következő parancsban cserélje le a saját globálisan egyedi nevét arra a blob Storage-fiókra, amelyben a `<blob_storage_account>` helyőrző látható.

```azurecli
blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia \
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

```powershell
$blobStorageAccount="<blob_storage_account>"

az storage account create --name $blobStorageAccount --location southeastasia `
  --resource-group myResourceGroup --sku Standard_LRS --kind StorageV2 --access-tier hot
```

## <a name="create-blob-storage-containers"></a>BLOB Storage-tárolók létrehozása

Az alkalmazás két tárolót használ a Blob Storage-fiókban. A tárolók hasonlók a mappákhoz, és tárolják a blobokat. A *képek* tároló az a hely, ahová az alkalmazás feltölti a teljes felbontású képeket. A sorozat egy későbbi részében egy Azure-függvényalkalmazás tölt fel átméretezett képminiatűröket a *thumbnails* tárolóba.

Szerezze be a tárfiókkulcsot az [az storage account keys list](/cli/azure/storage/account/keys) parancs segítségével. Ezután ezt a kulcsot használva hozzon létre két tárolót az az [Storage Container Create](/cli/azure/storage/container) paranccsal.

A *rendszerkép* -tároló nyilvános hozzáférése a következőre van beállítva: `off` . A *miniatűrök* tároló nyilvános hozzáférése a következőre van beállítva: `container` . A `container` nyilvános hozzáférés beállítás lehetővé teszi a felhasználóknak, hogy meglátogassák a weblapot a miniatűrök megtekintéséhez.

```bash
blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails \
  --account-name $blobStorageAccount \
  --account-key $blobStorageAccountKey --public-access container
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
  -n $blobStorageAccount --query "[0].value" --output tsv)

az storage container create --name images `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey

az storage container create --name thumbnails `
  --account-name $blobStorageAccount `
  --account-key $blobStorageAccountKey --public-access container
```

Jegyezze fel a blob Storage-fiók nevét és kulcsát. A minta alkalmazás ezeket a beállításokat használva csatlakozik a Storage-fiókhoz a lemezképek feltöltéséhez. 

## <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Az alkalmazást tároló webkiszolgálófarm helyét, méretét és funkcióit egy [App Service-csomag](../../app-service/overview-hosting-plans.md) határozza meg.

Hozzon létre egy App Service-csomagot az [az appservice plan create](/cli/azure/appservice/plan) paranccsal.

Az alábbi példa egy `myAppServicePlan` nevű App Service-csomag létrehozását mutatja be az **INGYENES** tarifacsomagban:

```azurecli
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

```powershell
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku Free
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A webalkalmazás üzemeltetési területet biztosít a GitHub-minta tárházban üzembe helyezett minta alkalmazás kódjához. Az [ az webapp create](/cli/azure/webapp) paranccsal hozzon létre egy [webalkalmazást](../../app-service/overview.md) a `myAppServicePlan` App Service-csomagban.  

A következő parancsban cserélje le a parancsot `<web_app>` egyedi névre. Érvényes karakterek: `a-z`, `0-9` és `-`. Ha `<web_app>` nem egyedi, a következő hibaüzenet jelenik meg: a *megadott nevű webhely `<web_app>` már létezik.* A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.  

```azurecli
webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

```powershell
$webapp="<web_app>"

az webapp create --name $webapp --resource-group myResourceGroup --plan myAppServicePlan
```

## <a name="deploy-the-sample-app-from-the-github-repository"></a>Mintaalkalmazás üzembe helyezése a GitHub-adattárból

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/storage-blob-upload-from-webapp) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.

A minta projekt egy [ASP.net MVC](https://www.asp.net/mvc) -alkalmazást tartalmaz. Az alkalmazás elfogadja a lemezképet, menti azt egy Storage-fiókba, és megjeleníti a képeket egy miniatűr tárolóból. A webalkalmazás az [Azure. Storage](/dotnet/api/azure.storage), az [Azure. Storage. Blobs](/dotnet/api/azure.storage.blobs)és az [Azure. Storage. Blobs. models](/dotnet/api/azure.storage.blobs.models) névtereket használja az Azure Storage szolgáltatással való kommunikációhoz.

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Az App Service több módszert is támogat tartalmak webalkalmazásba való üzembe helyezésére. Ebben az oktatóanyagban a webalkalmazást egy [nyilvános GitHub-mintaadattárból](https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs) telepítheti. Konfigurálja a GitHubról való telepítést a webalkalmazásba az [az webapp deployment source config](/cli/azure/webapp/deployment/source) parancs segítségével.

```azurecli
az webapp deployment source config --name $webapp --resource-group myResourceGroup \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

```powershell
az webapp deployment source config --name $webapp --resource-group myResourceGroup `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/azure-sdk-for-js-storage-blob-stream-nodejs
```

---

## <a name="configure-web-app-settings"></a>A webalkalmazás beállításainak konfigurálása

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

A minta webalkalmazás a [.net-hez készült Azure Storage API-kat](/dotnet/api/overview/azure/storage) használja a Képek feltöltéséhez. A Storage-fiók hitelesítő adatai a webalkalmazás alkalmazás-beállításainál vannak megadva. Adja hozzá az alkalmazás beállításait az üzembe helyezett alkalmazáshoz az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings) paranccsal.

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AzureStorageConfig__AccountName=$blobStorageAccount \
    AzureStorageConfig__ImageContainer=images \
    AzureStorageConfig__ThumbnailContainer=thumbnails \
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AzureStorageConfig__AccountName=$blobStorageAccount `
    AzureStorageConfig__ImageContainer=images `
    AzureStorageConfig__ThumbnailContainer=thumbnails `
    AzureStorageConfig__AccountKey=$blobStorageAccountKey
```

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

A minta webalkalmazás az [Azure Storage ügyféloldali kódtárat használja a javascripthez](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage) a lemezképek feltöltéséhez. A Storage-fiók hitelesítő adatai a webalkalmazás alkalmazás-beállításainál vannak megadva. Adja hozzá az alkalmazás beállításait az üzembe helyezett alkalmazáshoz az az [WebApp config appSettings set](/cli/azure/webapp/config/appsettings) paranccsal.

```azurecli
az webapp config appsettings set --name $webapp --resource-group myResourceGroup \
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
    AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

```powershell
az webapp config appsettings set --name $webapp --resource-group myResourceGroup `
  --settings AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey
```

---

A webalkalmazás üzembe helyezése és konfigurálása után tesztelheti a rendszerkép feltöltési funkcióját az alkalmazásban.

## <a name="upload-an-image"></a>Rendszerkép feltöltése

A webalkalmazás teszteléséhez nyissa meg a közzétett alkalmazás URL-címét. A webalkalmazás alapértelmezett URL-címe `https://<web_app>.azurewebsites.net`.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Válassza a **fényképek feltöltése** régiót egy fájl megadásához és feltöltéséhez, vagy húzzon egy fájlt a régióra. Sikeres feltöltés esetén a kép eltűnik. A **generált miniatűrök** szakasz üres marad, amíg az oktatóanyag későbbi szakaszában nem teszteljük.

![Fényképek feltöltése a .NET-ben](media/storage-upload-process-images/figure1.png)

A mintakód a `UploadFileToStorage` *Storagehelper. cs* fájl feladatát használja a lemezképek tárolóban lévő *lemezképek* tárolójába való feltöltéséhez a [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) metódus használatával. A következő mintakód tartalmazza a `UploadFileToStorage` műveletet.

```csharp
public static async Task<bool> UploadFileToStorage(Stream fileStream, string fileName,
                                                    AzureStorageConfig _storageConfig)
{
    // Create a URI to the blob
    Uri blobUri = new Uri("https://" +
                          _storageConfig.AccountName +
                          ".blob.core.windows.net/" +
                          _storageConfig.ImageContainer +
                          "/" + fileName);

    // Create StorageSharedKeyCredentials object by reading
    // the values from the configuration (appsettings.json)
    StorageSharedKeyCredential storageCredentials =
        new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

    // Create the blob client.
    BlobClient blobClient = new BlobClient(blobUri, storageCredentials);

    // Upload the file
    await blobClient.UploadAsync(fileStream);

    return await Task.FromResult(true);
}
```

Az előző feladatban használt osztályok és módszerek a következők:

| Osztály | Módszer |
|-------|--------|
| [URI](/dotnet/api/system.uri) | [URI-konstruktor](/dotnet/api/system.uri.-ctor) |
| [StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) | [StorageSharedKeyCredential (karakterlánc, karakterlánc) konstruktor](/dotnet/api/azure.storage.storagesharedkeycredential.-ctor) |
| [BlobClient](/dotnet/api/azure.storage.blobs.blobclient) | [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) |

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

Válassza a **fájl** kiválasztása lehetőséget egy fájl kiválasztásához, majd kattintson a **rendszerkép feltöltése** elemre. A **generált miniatűrök** szakasz üres marad, amíg az oktatóanyag későbbi szakaszában nem teszteljük.

![Fényképek feltöltése Node.js](media/storage-upload-process-images/upload-app-nodejs.png)

A mintakódban a `post` útvonal felelős a képek blobtárolóba való feltöltéséért. Az útvonal modulok használatával segíti a feltöltés feldolgozását:

- a [multer](https://github.com/expressjs/multer) megvalósítja a feltöltési stratégiát az útválasztási kezelőhöz.
- a [into-stream](https://github.com/sindresorhus/into-stream) a [uploadStream](/javascript/api/%40azure/storage-blob/blockblobclient#uploadstream-readable--number--number--blockblobuploadstreamoptions-)által előírt módon átalakítja a puffert egy adatfolyamba.

Ahogy a fájl el lesz küldve az útvonalra, a fájl tartalma a memóriában marad mindaddig, amíg a fájl fel nem töltődik a blob-tárolóba.

> [!IMPORTANT]
> A nagyméretű fájlok memóriába való betöltése negatív hatással lehet a webalkalmazás teljesítményére. Ha azt szeretné, hogy a felhasználók nagy fájlokat tegyenek közzé, érdemes megfontolnia a webkiszolgáló fájlrendszerének átmeneti fájljait, majd a feltöltéseket a blob Storage-ba ütemezve. Miután a fájlok a blob Storage-ban találhatók, eltávolíthatja őket a kiszolgálói fájlrendszerből.

```javascript
if (process.env.NODE_ENV !== 'production') {
  require('dotenv').config();
}

const {
  BlobServiceClient,
  StorageSharedKeyCredential,
  newPipeline
} = require('@azure/storage-blob');

const express = require('express');
const router = express.Router();
const containerName1 = 'thumbnails';
const multer = require('multer');
const inMemoryStorage = multer.memoryStorage();
const uploadStrategy = multer({ storage: inMemoryStorage }).single('image');
const getStream = require('into-stream');
const containerName2 = 'images';
const ONE_MEGABYTE = 1024 * 1024;
const uploadOptions = { bufferSize: 4 * ONE_MEGABYTE, maxBuffers: 20 };

const sharedKeyCredential = new StorageSharedKeyCredential(
  process.env.AZURE_STORAGE_ACCOUNT_NAME,
  process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY);
const pipeline = newPipeline(sharedKeyCredential);

const blobServiceClient = new BlobServiceClient(
  `https://${process.env.AZURE_STORAGE_ACCOUNT_NAME}.blob.core.windows.net`,
  pipeline
);

const getBlobName = originalName => {
  // Use a random number to generate a unique file name, 
  // removing "0." from the start of the string.
  const identifier = Math.random().toString().replace(/0\./, '');
  return `${identifier}-${originalName}`;
};

router.get('/', async (req, res, next) => {

  let viewData;

  try {
    const containerClient = blobServiceClient.getContainerClient(containerName1);
    const listBlobsResponse = await containerClient.listBlobFlatSegment();

    for await (const blob of listBlobsResponse.segment.blobItems) {
      console.log(`Blob: ${blob.name}`);
    }

    viewData = {
      title: 'Home',
      viewName: 'index',
      accountName: process.env.AZURE_STORAGE_ACCOUNT_NAME,
      containerName: containerName1
    };

    if (listBlobsResponse.segment.blobItems.length) {
      viewData.thumbnails = listBlobsResponse.segment.blobItems;
    }
  } catch (err) {
    viewData = {
      title: 'Error',
      viewName: 'error',
      message: 'There was an error contacting the blob storage container.',
      error: err
    };
    res.status(500);
  } finally {
    res.render(viewData.viewName, viewData);
  }
});

router.post('/', uploadStrategy, async (req, res) => {
  const blobName = getBlobName(req.file.originalname);
  const stream = getStream(req.file.buffer);
  const containerClient = blobServiceClient.getContainerClient(containerName2);;
  const blockBlobClient = containerClient.getBlockBlobClient(blobName);

  try {
    await blockBlobClient.uploadStream(stream,
      uploadOptions.bufferSize, uploadOptions.maxBuffers,
      { blobHTTPHeaders: { blobContentType: "image/jpeg" } });
    res.render('success', { message: 'File uploaded to Azure Blob storage.' });
  } catch (err) {
    res.render('error', { message: err.message });
  }
});

module.exports = router;
```

---

## <a name="verify-the-image-is-shown-in-the-storage-account"></a>Ellenőrizze, hogy a kép megjelenik-e a tárfiókban

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. Válassza a **tárolók**, majd a **lemezképek** tároló elemet.

Ellenőrizze, hogy a kép megjelenik-e a tárolóban.

![Képek tárolójának Azure Portal listája](media/storage-upload-process-images/figure13.png)

## <a name="test-thumbnail-viewing"></a>Miniatűr megtekintésének tesztelése

A miniatűr megtekintésének teszteléséhez fel kell töltenie egy rendszerképet a **miniatűrök** tárolóba annak ellenőrzéséhez, hogy az alkalmazás képes-e olvasni a **miniatűrök** tárolót.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). A bal oldali menüben válassza a **Tárfiókok** lehetőséget, majd válassza ki saját tárfiókja nevét. Válassza a **tárolók** lehetőséget, majd válassza ki a **miniatűrök** tárolót. Válassza a **Feltöltés** lehetőséget a **Blob feltöltése** panel megnyitásához.

Válasszon ki egy fájlt a fájl választóval, és válassza a **feltöltés** lehetőséget.

Lépjen vissza az alkalmazásba és ellenőrizze, hogy a **thumbnails** tárolóba feltöltött kép látható-e.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

![.NET-rendszerkép Resizer-alkalmazás új képpel megjelenítve](media/storage-upload-process-images/figure2.png)

# <a name="javascript-v12"></a>[JavaScript V12](#tab/javascript)

![Node.js képfájl-alkalmazás új képpel megjelenítve](media/storage-upload-process-images/upload-app-nodejs-thumb.png)

---

A sorozat második részében automatizálja a miniatűr képek létrehozását, így nincs szüksége erre a képre. A **miniatűrök** tárolóban válassza ki a feltöltött képet, majd a rendszerkép eltávolításához kattintson a **Törlés** elemre.

Engedélyezheti Content Delivery Network (CDN) számára a tartalom gyorsítótárazását az Azure Storage-fiókból. További információ: [Azure Storage-fiók integrálása Azure CDNokkal](../../cdn/cdn-create-a-storage-account-with-cdn.md).

## <a name="next-steps"></a>Következő lépések

A sorozat első részében megtanulta, hogyan konfigurálhat egy webalkalmazást a tárterülettel való interakcióhoz.

A sorozat második részében megismerheti, hogyan lehet a Event Grid használatával elindítani egy Azure-függvényt egy rendszerkép átméretezéséhez.

> [!div class="nextstepaction"]
> [Egy Azure-függvény Event Grid használatával történő aktiválása egy feltöltött kép átméretezéséhez](../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
