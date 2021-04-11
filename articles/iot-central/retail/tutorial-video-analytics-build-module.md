---
title: Oktatóanyag – a Azure IoT Edge Live Video Analytics-modulok módosítása
description: Ebből az oktatóanyagból megtudhatja, hogyan módosíthatja és építheti ki az élő videó elemzési átjáró modulokat, amelyeket a video Analytics-Object és a Motion Detection alkalmazás sablonja használ.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: cbff2e774fae1f71624aa3cdbd83bfb21e3a9586
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101721463"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>Oktatóanyag: az élő videó elemzési átjáró moduljainak módosítása és összeállítása

Ez az oktatóanyag bemutatja, hogyan módosíthatja a IoT Edge modul kódját az élő videó-elemzési (LVA) modulokhoz.

Az előző oktatóanyagok a modulok előre elkészített képeit használják.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek elvégzéséhez a következőkre lesz szüksége:


> [!div class="checklist"]
> * [Node.js](https://nodejs.org/en/download/) V10-es vagy újabb verzió
> * A [Visual Studio Code](https://code.visualstudio.com/Download) és a [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) bővítmény telepítve van
> * [Docker](https://www.docker.com/products/docker-desktop) -motor
> * [Azure Container Registry](../../container-registry/index.yml) a modulok verzióinak üzemeltetéséhez.
> * Egy [Azure Media Services](../../media-services/index.yml) -fiók. Ha befejezte az előző oktatóanyagokat, újra felhasználhatja a korábban létrehozottt.

## <a name="clone-the-repository"></a>A tárház klónozása

Ha még nem klónozotta a tárházat, a következő paranccsal klónozást végez a helyi gépen található megfelelő helyre:

```cmd
git clone https://github.com/Azure/live-video-analytics
```

Nyissa meg a local *Live-Video-Analytics* adattár MAPPÁT a vs Code használatával.

## <a name="edit-the-deploymentamd64json-file"></a>A fájl deployment.amd64.jsszerkesztése

1. Ha még nem tette meg, hozzon létre egy *ref-apps/LVA-Edge-IOT-Central-Gateway/Storage* nevű mappát a **LVA-Gateway** adattár helyi példányában. Ezt a mappát a git figyelmen kívül hagyja, hogy megakadályozza a bizalmas információk véletlen ellenőrzését.
1. Másolja a fájlt *deployment.amd64.jsa* *telepítési* mappájából a *Storage* mappába.
1. A VS Code-ban nyissa meg a *Storage/deployment.amd64.js* fájlt.
1. Szerkessze a `registryCredentials` szakaszt Azure Container Registry hitelesítő adatainak hozzáadásához.
1. A `LvaEdgeGatewayModule` modul szakasz szerkesztésével adja meg a rendszerkép nevét és az AMS-fiók nevét a következőben: `env:amsAccountName:value` .
1. Szerkessze a `lvaYolov3` modul szakaszt, és adja hozzá a rendszerkép nevét.
1. Szerkessze a `lvaEdge` modul szakaszt, és adja hozzá a rendszerkép nevét.
1. A konfiguráció végrehajtásával kapcsolatos további információkért tekintse meg a [video Analytics-alkalmazás létrehozása az Azure-ban IoT Central](tutorial-video-analytics-create-app-yolo-v3.md) .

## <a name="build-the-code"></a>A kód létrehozása

1. Mielőtt először megpróbálja felépíteni a kódot, használja a VS Code terminált a parancs futtatásához `npm install` . Ez a parancs telepíti a szükséges csomagokat, és futtatja a telepítési parancsfájlokat.

    > [!TIP]
    > Ha az adattár GitHub-tárházának újabb verzióját kéri le, törölje a *node_modules* mappát, majd futtassa `npm install` újra.

1. Szerkessze a *./setup/imageConfig.js* fájlt a tároló beállításjegyzékének neve alapján a nevű rendszerkép frissítéséhez:

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. A VS Code Terminal használatával futtassa a `docker login [your server].azurecr.io` parancsot. Használja ugyanazokat a hitelesítő adatokat, amelyeket az üzembe helyezési jegyzékben megadott a modulokhoz.

1. A VS Code Terminal használatával futtassa a **NPM Version patch** parancsot. Ez a Build szkript telepíti a lemezképeket a tároló-beállításjegyzékbe. A VS Code Terminal ablak kimenete azt mutatja, hogy a Build sikeres-e.

1. A **LvaEdgeGatewayModule** -rendszerkép verziója a Build befejeződése után minden alkalommal növekszik. Ezt a verziót kell használnia a telepítési jegyzékfájlban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az alkalmazással, a következőképpen távolíthatja el az összes létrehozott erőforrást:

1. A IoT Central alkalmazásban navigáljon az **alkalmazás** lapjára az **Adminisztráció** szakaszban. Ezután válassza a **Törlés** elemet.
1. A Azure Portal törölje a **LVA-RG** erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a video Analytics – Object és a mozgásérzékelő alkalmazás sablonját, valamint a LVA IoT Edge modulokat, a javasolt következő lépés az alábbiak megismerése:

> [!div class="nextstepaction"]
> [Kiskereskedelmi megoldások létrehozása az Azure IoT Centrallal](overview-iot-central-retail.md)