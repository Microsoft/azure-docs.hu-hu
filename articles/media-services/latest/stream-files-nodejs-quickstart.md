---
title: Videofájlok továbbítása Azure Media Services-Node.js
description: Az oktatóanyag lépéseit követve hozzon létre egy új Azure Media Services fiókot, kódoljon egy fájlt, és továbbítsa a Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, stream
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: mvc, devx-track-js
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: 0eb334111a8f5ffc63d0f858966e4e65f99d3b16
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095958"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---nodejs"></a>Oktatóanyag: távoli fájl kódolása URL-cím alapján és stream a videón – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ebből az oktatóanyagból megtudhatja, hogy milyen egyszerűen kódolhatja és indíthatja el a streaming-videókat számos különböző böngészőn és eszközön a Azure Media Services használatával. A bemeneti videofájl HTTPS URL-címek, SAS URL-címek vagy az Azure Blob Storage-ban található fájlok elérési útjai alapján adható meg.

A cikkben szereplő minta egy HTTPS URL-címen keresztül elérhetővé teszi a tartalmat. Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

Az oktatóanyag végén megtudhatja, hogyan tölthet fel, kódolhat és továbbíthat videókat egy HLS vagy DASH-ügyfélszoftver használatával.

![Videó lejátszása](./media/stream-files-nodejs-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Node.js](https://nodejs.org/en/download/) telepítése
- [Hozzon létre egy Media Services fiókot](./create-account-howto.md).<br/>Ügyeljen arra, hogy az erőforráscsoport neveként használt értékeket jegyezze fel, és Media Services a fiók nevét.
- Kövesse a [Azure Media Services API-nak az Azure CLI-vel való elérésének](./access-api-howto.md) lépéseit, és mentse a hitelesítő adatokat. Ezeket az API-k eléréséhez kell használnia.
- Tekintse át a [configure és a Kapcsolódás a Node.js](./configure-connect-nodejs-howto.md) az első lépések a Node.js ügyféloldali SDK használatának megismeréséhez

## <a name="download-and-configure-the-sample"></a>A minta letöltése és konfigurálása

A következő parancs használatával klónozott egy GitHub-tárházat, amely a streaming Node.js mintát tartalmazza a gépre:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
 ```

A minta a [StreamFilesSample](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/master/AMSv3Samples/StreamFilesSample) mappában található.

Nyissa meg az [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) fájlt a letöltött projektben. Frissítse a sample. env fájlt a gyökérkönyvtárban az API-k [eléréséhez](./access-api-howto.md)kapott értékekkel és hitelesítő adatokkal. Nevezze át a sample. env fájlt a. env fájlba. 

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy **átalakítót** (először ellenőrzi, hogy létezik-e a megadott átalakító). 
2. Létrehozza a kódolási **feladatok** **kimenetéhez használt kimeneti objektumot** .
1. Opcionálisan feltölt egy helyi fájlt a Storage blob SDK használatával.
1. Létrehoz egy HTTPS URL-címen vagy feltöltött fájlon alapuló **feladatot**.
1. Elküldi a kódolási **feladatot** a [tartalmat támogató kódolási beállításkészlettel](./content-aware-encoding.md)a korábban létrehozott bemenet és kimenet használatával.
1. Ellenőrzi a feladat állapotát.
1. Letölti a kódolási feladatok kimenetét egy helyi mappába.
1. Létrehoz egy **streaming-lokátort** a lejátszóban való használathoz.
1. Streaming URL-címeket hoz létre a HLS és a DASH-hez.
1. Visszajátssza a tartalmat egy Player-alkalmazásban – Azure Media Player.

## <a name="run-the-sample-app"></a>A mintaalkalmazás futtatása

1. Az alkalmazás letölti a kódolt fájlokat. Hozzon létre egy mappát, ahol a kimeneti fájlok el szeretnének menni, és frissítse a **outputFolder** változó értékét az [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.js#L59) fájlban. Alapértelmezés szerint "Temp" értékre van állítva.
1. Nyisson meg egy **parancssort**, keresse meg a minta címtárát, és hajtsa végre a következő parancsokat.
1. Könyvtár módosítása a AMSv3Samples mappába
    ```bash
    cd AMSv3Samples
    ```

1. Telepítse a packages.jsben használt csomagokat a következőn:
    ```bash
    npm install 
    ```

1. Könyvtár módosítása a StreamFilesSample mappába
    ```bash
    cd StreamFilesSample
    ```

1. Indítsa el a Visual Studio Code-ot a AMSv3Samples mappából. Erre azért van szükség, hogy az a mappában induljon el, ahol a ". vscode" mappa és a tsconfig.jsfájl található.

    ```bash
    cd ..
    code .
    ```

Nyissa meg a StreamFilesSample mappát, és nyissa meg az index. ts fájlt a Visual Studio Code Editorban.
Az index. TS fájlban nyomja le az F5 billentyűt a hibakereső elindításához.


## <a name="test-with-azure-media-player"></a>Tesztelés az Azure Media Player használatával

Ebben a cikkben az Azure Media Playert használjuk a streamelés teszteléséhez. Bármilyen HLS vagy DASH-kompatibilis lejátszót használhat, például a következőt: a (z), HLS.js, Dash.js vagy más.

A minta létrehozásához kattintson a hivatkozásra, és indítsa el az AMP-lejátszót a már betöltött DASH jegyzékfájl használatával.

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy webböngészőt, és navigáljon a következőhöz: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Az **URL** mezőbe illessze be az alkalmazás futtatásakor kapott streamelési URL-értékek egyikét. 
 
     Az URL-címet HLS, Dash vagy Smooth formátumban is beillesztheti, és a Azure Media Player a megfelelő folyamatos átviteli protokollra vált az eszközön való automatikus lejátszás érdekében.
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoport egyik erőforrására sem, beleértve az oktatóanyaghoz létrehozott Media Services-és Storage-fiókokat, törölje az erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>További fejlesztői dokumentáció az Azure-beli Node.js
- [Azure for JavaScript & Node.js fejlesztők számára](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Media Services forráskód a git hub-tárházban @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Az Azure Package dokumentációja Node.js-fejlesztőknek](https://docs.microsoft.com/javascript/api/overview/azure/?view=azure-node-latest)

## <a name="see-also"></a>Lásd még

- [Feladattípus](/rest/api/media/jobs/get#joberrorcode)-hibakódok.
- [NPM telepítése @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure for JavaScript & Node.js fejlesztők számára](https://docs.microsoft.com/azure/developer/javascript/?view=azure-node-latest)
- [Forráskód Media Services a tárházban @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Media Services fogalmak](concepts-overview.md)
