---
title: 'Gyors útmutató: Azure Blob Storage a JavaScript v10-hez a böngészőben'
description: A HTML-lapokon a JavaScript v10 SDK használatával megtudhatja, hogyan tölthet fel, listázhat és törölhet blobokat.
services: storage
author: mhopkins-msft
ms.custom: mvc, devx-track-js
ms.service: storage
ms.author: mhopkins
ms.date: 07/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: d212029936bcd257ef5a78eeedc98c2d6e1df514
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012782"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Gyors útmutató: Blobok kezelése a JavaScript v10 SDK-val a böngészőben

Ebből a rövid útmutatóból megtudhatja, hogyan kezelheti a blobokat a teljes böngészőben futtatott JavaScript-kód használatával. A Blobok olyan objektumok, amelyek nagy mennyiségű szöveges vagy bináris adatok tárolására képesek, beleértve a képeket, a dokumentumokat, a médiafolyamokat és az archiválási adatokhoz. A szükséges biztonsági intézkedéseket fogja használni a blob Storage-fiókhoz való védett hozzáférés biztosításához.

> [!NOTE]
> Ez a rövid útmutató az Azure Blob Storage ügyféloldali függvénytárának örökölt verzióját használja. A legújabb verzió használatának megkezdéséhez lásd: gyors útmutató [: Blobok kezelése a JavaScript V12 SDK-val egy böngészőben](quickstart-blobs-javascript-browser.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy Azure Storage-fiók. [Hozzon létre egy Storage-fiókot](../common/storage-account-create.md).
- Helyi webkiszolgáló. Ez a cikk a [Node.js](https://nodejs.org) használatával nyit meg egy alapszintű kiszolgálót.
- [Visual Studio Code](https://code.visualstudio.com).
- Egy VS Code-bővítmény a böngésző hibakereséséhez, például a [Microsoft Edge](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-edge)-hez készült [Chrome](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome) vagy Debugger hibakeresője.

## <a name="setting-up-storage-account-cors-rules"></a>Tárfiók CORS-szabályainak beállítása

Ahhoz, hogy a webalkalmazás hozzáférhessen a blob Storage-hoz az ügyféltől, konfigurálnia kell a fiókját, hogy engedélyezze a [több eredetű erőforrás-megosztást](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)vagy a CORS.

Lépjen vissza az Azure Portalra, és válassza ki a tárfiókot. Új CORS-szabály definiálásához navigáljon a **Beállítások** szakaszra, és kattintson a **CORS** hivatkozásra. Ezután kattintson a **Hozzáadás** gombra a **CORS-szabály hozzáadása** ablak megnyitásához. Ebben a rövid útmutatóban egy nyitott CORS-szabályt hozunk létre:

![Azure Blob Storage-fiók CORS-beállításai](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

A következő tábla az egyes CORS-beállításokat írja le, és ismerteti a szabály meghatározásához használt értékeket.

|Beállítás  |Érték  | Leírás |
|---------|---------|---------|
| Engedélyezett eredetek | * | Elfogadható eredetekként beállított tartományok vesszővel tagolt listáját fogadja el. A `*` érték beállításakor minden tartomány hozzáfér a tárfiókhoz. |
| Engedélyezett metódusok     | törlés, lekérés, fej, egyesítés, közzététel, beállítások és áthelyezés | A tárfiókon futtatható HTTP-műveleteket listázza. Ebben a rövid útmutatóban válassza ki az összes elérhető beállítást. |
| Engedélyezett fejlécek | * | A tárfiókban engedélyezett kérelemfejlécek listáját határozza meg (beleértve az előtaggal ellátott fejléceket). A `*` érték beállítása minden fejléc hozzáférését engedélyezi. |
| Közzétett fejlécek | * | A fiók által engedélyezett válaszfejléceket listázza. A `*` érték beállítása esetén a fiók bármilyen fejlécet küldhet.  |
| Maximális életkor (másodperc) | 86400 | A maximális idő, ameddig a böngésző gyorsítótárazza az előzetes OPTIONS kérést. A *86400* érték lehetővé teszi, hogy a gyorsítótár egy teljes napig megmaradjon. |

> [!IMPORTANT]
> Győződjön meg arról, hogy az éles környezetben használt összes beállítás elérhetővé teszi a Storage-fiók számára a biztonságos hozzáférés fenntartásához szükséges minimális hozzáférést. Az itt leírt CORS-beállítások megfelelőek ehhez a rövid útmutatóhoz, mert laza biztonsági szabályzatot határoznak meg. Nem ajánlottak azonban valós környezetekben.

Ezután az Azure Cloud Shell-lel létrehoz egy biztonsági jogkivonatot.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Közös hozzáférési aláírás létrehozása

A böngészőben futó kód a közös hozzáférésű jogosultságkóddal (SAS) engedélyezteti a Blob Storage-ba érkező kéréseket. A SAS használatával az ügyfél a fiók hozzáférési kulcsa vagy kapcsolati sztringje nélkül is elvégezheti a tárolási erőforrásokhoz való hozzáférés engedélyeztetését. Az SAS-sel kapcsolatos további információkat a [közös hozzáférésű jogosultságkód (SAS) használatát ismertető](../common/storage-sas-overview.md) cikkben olvashat.

Létrehozhat egy SAS-t az Azure CLI használatával az Azure Cloud shellben vagy a Azure Portal vagy Azure Storage Explorer. A következő táblázat azokat a paramétereket ismerteti, amelyekkel a rendszernek értékeket kell megadnia a parancssori felülettel való SAS létrehozásához.

| Paraméter      |Leírás  | Helyőrző |
|----------------|-------------|-------------|
| *lejárta*       | A hozzáférési jogkivonat lejárati dátuma ÉÉÉÉ-HH-NN formátumban. Írja be a holnapi dátumot ehhez a rövid útmutatóhoz. | *FUTURE_DATE* |
| *fiók neve* | A tárfiók neve. Használja a korábbi lépésben félretett nevet. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | A tárfiókkulcs. Használja a korábbi lépésben félretett kulcsot. | *YOUR_STORAGE_ACCOUNT_KEY* |

Használja az alábbi CLI-parancsot az egyes helyőrzők tényleges értékeivel, hogy létrehozzon egy SAS-t, amelyet használhat a JavaScript-kódban.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

A paraméterek után szereplő értékek nehezen érthetőek lehetnek. Ezek a paraméterértékek a megfelelő engedélyek első betűiből vannak összeállítva. A következő táblázat az értékek eredetét írja le:

| Paraméter        | Érték   | Leírás  |
|------------------|---------|---------|
| *engedélyek*    | racwdl  | Ez az SAS *olvasási*, *hozzáfűzési*, *létrehozási*, *írási*, *törlési* és *listázási* képességeket engedélyez. |
| *resource-types* | sco     | Az SAS a *szolgáltatás*, a *tároló* és az *objektum* erőforrásokra van hatással. |
| *Services*       | b       | Az SAS a *Blob* szolgáltatásra van hatással. |

Most, hogy létrehozta az SAS-t, másolja át a visszaadott értéket, és mentse valahova egy későbbi lépésben való használatra. Ha az SAS-t az Azure CLI-től eltérő módszerrel hozta létre, akkor a kezdeti állapotot el kell távolítania `?` . Ez a karakter egy URL-elválasztó, amely már szerepel az URL-sablonban a témakör későbbi részében, ahol a SAS használatban van.

> [!IMPORTANT]
> Éles környezetben mindig a TLS protokollt használó SAS-tokeneket adja át. Ezenkívül az SAS-jogkivonatokat a kiszolgálón kell létrehozni, és a HTML-oldalra kell küldeni, hogy a rendszer visszaadja azokat az Azure Blob Storage-ba. Érdemes például kiszolgáló nélküli függvényt használni SAS-jogkivonatok létrehozásához. Az Azure Portal olyan függvénysablonokat tartalmaz, amelyekkel JavaScript-függvény használatával hozható létre SAS.

## <a name="implement-the-html-page"></a>A HTML-oldal implementálása

Ebben a szakaszban létre fog hozni egy alapszintű weblapot, és a VS Code-t kell konfigurálnia az oldal elindításához és hibakereséséhez. Az indítás előtt azonban Node.jst kell használnia egy helyi webkiszolgáló elindításához és az oldal kiszolgálásához, amikor a böngésző kéri. Ezután adja hozzá a JavaScript-kódot a különböző blob Storage API-k meghívásához, és jelenítse meg az eredményeket az oldalon. A hívások eredményeit a [Azure Portal](https://portal.azure.com), a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer)és az [Azure Storage bővítményben](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) is láthatja a vs Code-ban.

### <a name="set-up-the-web-application"></a>A webalkalmazás beállítása

Először hozzon létre egy *Azure-Blobs-JavaScript* nevű új mappát, és nyissa meg a vs Code-ban. Ezután hozzon létre egy új fájlt a VS Code-ban, adja hozzá a következő HTML-kódot, és mentse *index.html* néven az *Azure-Blobs-JavaScript* mappában.

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>A hibakereső konfigurálása

A Debugger bővítmény a VS Code-ban való beállításához válassza a **hibakeresés > konfiguráció hozzáadása...** lehetőséget, majd a **Chrome** vagy a **Edge** lehetőséget, attól függően, hogy az előfeltételek szakaszban milyen bővítményt telepített. Ez a művelet létrehoz egy *launch.jsa* fájlon, és megnyitja a szerkesztőben.

Ezután módosítsa a fájl *launch.jsét* , hogy az érték a következőt `url` tartalmazza `/index.html` :

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

Ez a konfiguráció közli a VS Code-val, hogy melyik böngészőt kell elindítani, és melyik URL-címet kell betölteni.

### <a name="launch-the-web-server"></a>A webkiszolgáló elindítása

A helyi Node.js webkiszolgáló indításához válassza a **> Terminal megtekintése** lehetőséget, hogy a vs Code-ban nyissa meg a konzol ablakát, majd írja be a következő parancsot.

```console
npx http-server
```

Ez a parancs telepíti a *http-Server* csomagot, és elindítja a kiszolgálót, így az aktuális mappa az alapértelmezett URL-címeken keresztül érhető el, beleértve az előző lépésben jelzetteket is.

### <a name="start-debugging"></a>Hibakeresés indítása

Ha *index.html* -t a BÖNGÉSZŐBEN a vs Code Debuggerrel együtt szeretné elindítani, válassza a **hibakeresés > a hibakeresés indítása** vagy az F5 billentyű lenyomása a vs Code-ban lehetőséget.

A megjelenített felhasználói felület még nem csinál semmit, de a következő szakaszban a JavaScript-kódot fogja hozzáadni az egyes függvények megvalósításához. Ezután megadhatja a töréspontokat, és használhatja a hibakeresőt, ha szüneteltetve van a kódban.

Ha módosítja *index.html*-t, a böngészőben a módosítások megtekintéséhez töltse be újra a lapot. A VS Code-ban a hibakeresés **> a hibakeresés újraindítása** vagy a CTRL + SHIFT + F5 billentyűkombinációt is használhatja.

### <a name="add-the-blob-storage-client-library"></a>A blob Storage ügyféloldali kódtár hozzáadása

A blob Storage API meghívásának engedélyezéséhez először [töltse le a JavaScript-blob ügyféloldali kódtár Azure Storage SDK-](https://aka.ms/downloadazurestoragejsblob)ját, bontsa ki a zip tartalmát, és helyezze el a *azure-storage-blob.js* fájlt az *Azure-Blobs-JavaScript* mappába.

Ezután illessze be a következő HTML-t a *index.html* -be a `</body>` záró címke után, és cserélje le a helyőrző megjegyzését.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Ez a kód egy hivatkozást ad hozzá a parancsfájlhoz, és helyet biztosít a saját JavaScript-kódjához. Ebben a rövid útmutatóban a *azure-storage-blob.js* parancsfájlt használjuk, hogy a vs Code-ban nyissa meg a fájlt, olvassa el a tartalmát, és állítsa be a töréspontokat. Éles környezetben a zip-fájlban is szereplő, Compact *azure-storage.blob.min.js* fájlt kell használnia.

Az egyes blob Storage-függvényekről a [dokumentációban](/javascript/api/%40azure/storage-blob/index)talál további információt. Vegye figyelembe, hogy az SDK egyes funkciói csak Node.js vagy csak a böngészőben érhetők el.

A kód *azure-storage-blob.js* exportál egy nevű globális változót `azblob` , amelyet a JavaScript-kódban fog használni a blob Storage API-k eléréséhez.

### <a name="add-the-initial-javascript-code"></a>A kezdeti JavaScript-kód hozzáadása

Ezután illessze be a következő kódot az `<script>` előző kódrészletben látható elembe, és cserélje le a helyőrző megjegyzését.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

Ez a kód mezőket hoz létre minden olyan HTML-elemhez, amelyet a következő kód fog használni, és végrehajt egy `reportStatus` függvényt a kimenet megjelenítéséhez.

A következő részekben adja hozzá a JavaScript-kód minden új blokkját az előző blokk után.

### <a name="add-your-storage-account-info"></a>A Storage-fiók adatainak hozzáadása

Ezután vegyen fel kódot a Storage-fiók eléréséhez, és cserélje le a helyőrzőket a fiók nevére és az előző lépésben létrehozott SAS-ra.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Ez a kód a fiókadatok és az SAS használatával hozza létre a [ContainerURL](/javascript/api/@azure/storage-blob/ContainerURL) -példányt, ami hasznos lehet a tárolók létrehozásához és kezeléséhez.

### <a name="create-and-delete-a-storage-container"></a>Tároló létrehozása és törlése

Ezután adja hozzá a Storage-tároló létrehozásához és törléséhez szükséges kódot a megfelelő gomb megnyomásakor.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

Ez a kód meghívja a ContainerURL [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) és [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) függvényt a [megszakítási](/javascript/api/@azure/storage-blob/aborter) példányok használata nélkül. Ha a rövid útmutatóhoz egyszerűen szeretné megtartani a dolgokat, ez a kód feltételezi, hogy a Storage-fiók létrejött, és engedélyezve van. Az éles kódban a megszakító példány használatával adja hozzá az időtúllépési funkciót.

### <a name="list-blobs"></a>Blobok listázása

Ezután adjon hozzá egy kódot a tároló tartalmának listázásához a **fájlok listázása** gomb megnyomásakor.

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

Ez a kód meghívja a [ContainerURL. listBlobFlatSegment](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) függvényt egy hurokban, így biztosítva az összes szegmens lekérését. Minden szegmensnél hurkot vesz fel a benne található blob-elemek listáján, és frissíti a **fájlok** listáját.

### <a name="upload-blobs"></a>Blobok feltöltése

Ezután adja hozzá a fájlokat a Storage-tárolóba a **fájlok kiválasztása és feltöltése** gomb megnyomásakor.

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

Ez a kód csatlakoztatja a **fájlok kiválasztása és feltöltése** gombot a rejtett `file-input` elemhez. Így a Button `click` esemény elindítja a fájl bemeneti `click` eseményét, és megjeleníti a fájl-választót. Miután kiválasztotta a fájlokat, és bezárta a párbeszédpanelt, az `input` esemény bekövetkezik, és `uploadFiles` meghívja a függvényt. Ez a függvény meghívja a csak böngészőalapú [uploadBrowserDataToBlockBlob](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) függvényt minden kiválasztott fájlhoz. Minden hívás egy ígéretet ad vissza, amely egy listához kerül, így egyszerre csak egyszer lehet várni, így a fájlok párhuzamosan tölthetők fel.

### <a name="delete-blobs"></a>Blobok törlése

Ezután adjon hozzá kódot a fájlok törléséhez a tárolóból a **kijelölt fájlok törlése** gomb megnyomásakor.

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

Ez a kód meghívja a [BlobURL. Delete](/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) függvényt, hogy eltávolítsa a listában kijelölt összes fájlt. Ezután meghívja a `listFiles` korábban bemutatott függvényt a **fájlok** listájának tartalmának frissítéséhez.

### <a name="run-and-test-the-web-application"></a>A webalkalmazás futtatása és tesztelése

Ezen a ponton elindíthatja a lapot, és kísérletet tehet a blob Storage működésének megismerésére. Ha bármilyen hiba történik (például ha a tároló létrehozása előtt próbál meg fájlokat listázni), az **állapot** ablaktáblán megjelenik a kapott hibaüzenet. A JavaScript-kódban töréspontokat is beállíthat a tárolási API-k által visszaadott értékek vizsgálatához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató során létrehozott erőforrások törléséhez nyissa meg a [Azure Portal](https://portal.azure.com) , és törölje az előfeltételek szakaszban létrehozott erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy egyszerű webhelyet, amely hozzáfér a blob Storage-hoz a böngésző alapú JavaScriptből. Ha szeretné megtudni, hogyan üzemeltetheti a webhelyeket a blob Storage szolgáltatásban, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Statikus webhely üzemeltetése Blob Storage](./storage-blob-static-website-host.md)