---
title: Videofájlok kódolása és továbbítása Node.JS
description: Videofájlok továbbítása Node.JS használatával. Az oktatóanyag lépéseit követve hozzon létre egy új Azure Media Services fiókot, kódoljon egy fájlt, és továbbítsa a Azure Media Player.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: Azure Media Services, stream, Node.JS
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: inhenkel
ms.openlocfilehash: a431060be380952940bdb99c65abc7a9740091de
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105038158"
---
# <a name="how-to-encode-and-stream-video-files-with-nodejs"></a>Videofájlok kódolása és továbbítása Node.JS

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a rövid útmutató bemutatja, hogy milyen könnyen kezdheti meg a videók kódolását és streamelését számos különféle böngészőben és eszközön az Azure Media Services használatával. A bemeneti videofájl HTTPS URL-címek, SAS URL-címek vagy az Azure Blob Storage-ban található fájlok elérési útjai alapján adható meg.

A rövid útmutató végén a következőket fogja tudni:

- Kódolás a Node.JS használatával
- Stream Node.JS
- Fájl feltöltése HTTPS URL-címről Node.JS
- HLS vagy DASH-ügyfélszoftver használata Node.JS

A cikkben szereplő minta egy HTTPS URL-címen keresztül elérhetővé teszi a tartalmat. Vegye figyelembe, hogy az AMS v3 jelenleg nem támogatja a HTTPS URL-címekkel történő darabolásos átviteli kódolást.

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

Nyissa meg az [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/master/AMSv3Samples/StreamFilesSample/index.ts) fájlt a letöltött projektben. Frissítse a *sample. env* fájlt a gyökérkönyvtárban az API-k [eléréséhez](./access-api-howto.md)kapott értékekkel és hitelesítő adatokkal. Nevezze át a *sample. env* fájlt a *. env* fájlba (igen, csak a bővítményre).

A minta a következő műveleteket hajtja végre:

1. Létrehoz egy **átalakítót** egy [Content Aware Encoding-készlettel](./content-aware-encoding.md). Először ellenőrzi, hogy létezik-e a megadott átalakító.
1. Létrehoz egy kimeneti adategységet **, amelyet a** kódolási **feladatokban** használ a kimenet
1. Opcionálisan feltölt egy helyi fájlt a Storage blob SDK használatával.
1. Létrehoz egy HTTPS URL-címen vagy feltöltött fájlon alapuló **feladatot** .
1. Elküldi a kódolási **feladatot** a korábban létrehozott bemenet és kimenet használatával.
1. A feladatok állapotának ellenőrzése
1. Letölti a kódolási feladatok kimenetét egy helyi mappába.
1. Létrehoz egy **streaming-lokátort** a lejátszóban való használathoz
1. Streaming URL-címeket hoz létre a HLS és a DASH számára
1. Visszajátssza a tartalmat egy Player-alkalmazásban – Azure Media Player

## <a name="run-the-sample"></a>Minta futtatása

1. Az alkalmazás letölti a kódolt fájlokat. Hozzon létre egy mappát, ahol szeretné, hogy a kimeneti fájlok meglépjenek, és frissítse a **outputFolder** változó értékét az [index. TS](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/StreamFilesSample/index.ts#L59) fájlban. Alapértelmezés szerint "Temp" értékre van állítva.
1. Nyisson meg egy **parancssort**, és keresse meg a minta címtárát.
1. Módosítsa a könyvtárat a AMSv3Samples mappába.

    ```bash
    cd AMSv3Samples
    ```

1. Telepítse a fájl *packages.js* használt csomagokat.

    ```bash
    npm install 
    ```

1. Módosítsa a könyvtárat a *StreamFilesSample* mappába.

    ```bash
    cd StreamFilesSample
    ```

1. Indítsa el a Visual Studio Code-ot a *AMSv3Samples* mappából. (Ez ahhoz szükséges, hogy az a mappában legyen elindítva, ahol a *. vscode* mappa és a *tsconfig.js* fájl található.)

    ```bash
    cd ..
    code .
    ```

Nyissa meg a *StreamFilesSample* mappát, és nyissa meg az *index. TS* fájlt a Visual Studio Code Editorban.
Az *index. TS* fájlban nyomja le az F5 billentyűt a hibakereső elindításához.

## <a name="test-with-azure-media-player"></a>Tesztelés az Azure Media Player használatával

A stream teszteléséhez használja a Azure Media Player. Bármilyen HLS vagy DASH-kompatibilis lejátszót használhat, például a következőt: a (z), HLS.js, Dash.js vagy más.

Kattintson a mintában létrehozott hivatkozásra, és indítsa el az AMP-lejátszót a már betöltött DASH-jegyzékfájl használatával.

> [!NOTE]
> Ha a lejátszót egy HTTPS-hely futtatja, az URL-t módosítsa a HTTPS-protokoll használatára.

1. Nyisson meg egy webböngészőt, és navigáljon a következőhöz: [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. Az **URL:** mezőben illessze be az alkalmazás futtatásakor kapott streaming URL-címek egyikét. Az URL-címet HLS, Dash vagy Smooth formátumban is beillesztheti, és a Azure Media Player a megfelelő folyamatos átviteli protokollra vált az eszközön való automatikus lejátszás érdekében.
3. Kattintson az **Update Player** (Lejátszó frissítése) elemre.

Az Azure Media Player használható tesztelésre, az éles környezetben való használata azonban nem ajánlott.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoport egyik erőforrására sem, beleértve az oktatóanyaghoz létrehozott Media Services-és Storage-fiókokat, törölje az erőforráscsoportot.

Hajtsa végre a következő CLI-parancsot:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>További fejlesztői dokumentáció az Azure-beli Node.js

- [Azure for JavaScript & Node.js fejlesztők számára](/azure/developer/javascript/)
- [Media Services forráskód a git hub-tárházban @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Az Azure Package dokumentációja Node.js-fejlesztőknek](/javascript/api/overview/azure/)

## <a name="see-also"></a>Lásd még

- [Feladattípus](/rest/api/media/jobs/get#joberrorcode)-hibakódok.
- [NPM telepítése @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Azure for JavaScript & Node.js fejlesztők számára](/azure/developer/javascript/)
- [Forráskód Media Services a tárházban @azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Következő lépések

> [Media Services fogalmak](concepts-overview.md)
