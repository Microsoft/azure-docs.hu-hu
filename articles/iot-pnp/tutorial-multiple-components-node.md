---
title: IoT csatlakoztatása Plug and Play minta Node.js összetevő-eszköz kódja a IoT Hubhoz | Microsoft Docs
description: Hozzon létre és futtasson IoT Plug and Play minta Node.js eszköz kódját, amely több összetevőt használ, és csatlakozik egy IoT hubhoz. Az Azure IoT Explorer eszköz használatával megtekintheti az eszköz által a hubhoz továbbított adatokat.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 2f0bf3b4b6e7091222b98e1743f0fb4f431fdd12
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736055"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-multiple-component-device-application-to-iot-hub-nodejs"></a>Oktatóanyag: minta IoT csatlakoztatása Plug and Play több összetevőt tartalmazó eszköz alkalmazása IoT Hub (Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre IoT Plug and Play-eszköz-alkalmazást összetevőkkel, hogyan csatlakoztathatja az IoT hubhoz, és az Azure IoT Explorer eszköz használatával megtekintheti a központnak küldött adatokat. A minta alkalmazás a Node.jshoz van írva, és a Node.js Azure IoT Hub Device SDK része. A megoldás-szerkesztő az Azure IoT Explorer eszköz használatával képes értelmezni egy IoT Plug and Play eszköz képességeit anélkül, hogy meg kellene tekintenie az eszköz kódját.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Az oktatóanyag elvégzéséhez Node.jsra van szükség a fejlesztői gépen. A [NodeJS.org](https://nodejs.org)több platformon is letöltheti a legújabb ajánlott verziót.

A Node.js aktuális verzióját a következő paranccsal ellenőrizheti a fejlesztői gépen:

```cmd/sh
node --version
```

## <a name="download-the-code"></a>A kód letöltése

Ha befejezte a gyors üzembe helyezést [: csatlakoztasson egy IoT Plug and Play Windows rendszerű eszközt IoT hub (node)](quickstart-connect-device-node.md), már klónozotta a tárházat.

Nyisson meg egy parancssort az Ön által választott könyvtárban. Futtassa a következő parancsot a Node.jsGitHub-adattárhoz [tartozó Microsoft Azure IOT SDK ](https://github.com/Azure/azure-iot-sdk-node) ezen a helyen történő klónozásához:

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

## <a name="install-required-libraries"></a>A szükséges kódtárak telepítése

Az eszköz SDK-val felépítheti a mellékelt mintakód-kódot. Az Ön által létrehozott alkalmazás szimulál egy Plug and Play eszközt több, a IoT hubhoz csatlakozó összetevővel. Az alkalmazás telemetria és tulajdonságokat küld, és parancsokat fogad.

1. A helyi terminál ablakban lépjen a klónozott adattár mappájába, és keresse meg a */Azure-IOT-SDK-Node/Device/Samples/PnP* mappát. Ezután futtassa a következő parancsot a szükséges kódtárak telepítéséhez:

```cmd/sh
npm install
```

Ezzel telepíti a megfelelő NPM-fájlokat, amelyek a mintáknak a mappában való futtatásához szükségesek.

## <a name="review-the-code"></a>A kód áttekintése

Navigáljon a *Azure-IOT-SDK-node\device\samples\pnp* mappára.

A *Azure-IOT-SDK-node\device\samples\pnp* mappa tartalmazza a IOT Plug and Play hőmérséklet-vezérlő eszköz mintájának kódját.

A *pnpTemperatureController.js* fájlban található kód egy IoT Plug and Play hőmérséklet-vezérlő eszközt valósít meg. A minta által megvalósított modell [több összetevőt](concepts-components.md)használ. A [hőmérséklet-eszköz digitális Twins Definition Language (DTDL) modellje](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) az eszköz által megvalósított telemetria, tulajdonságokat és parancsokat határozza meg.

Nyissa meg a *pnpTemperatureController.js* fájlt egy tetszőleges szerkesztőprogramban. A mintakód a következőket mutatja be:

- Határozza meg, `modelId` hogy melyik a megvalósított eszköz DTMI. Ez a DTMI felhasználó által definiált, és meg kell egyeznie a [hőmérséklet-vezérlő DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)DTMI.

- Implementálja a hőmérséklet-vezérlő DTDL-modellben meghatározott összetevőket. A valós hőmérséklet-vezérlő összetevőinek meg kell valósítaniuk ezt a két felületet. Ez a két csatoló már közzé van téve egy központi tárházban. Ebben a példában a két csatoló a következő:

  - Okostelefonok
  - Az Azure által fejlesztett eszközök adatai

- Adja meg az összetevők nevét. Ez a minta két termosztáttal és egy eszköz-információs összetevővel rendelkezik.

- Adja meg a parancs nevét. Ezek azok a parancsok, amelyekre az eszköz válaszol.

- Adja meg az `serialNumber` állandót. A `serialNumber` rögzített egy adott eszközt.

- Adja meg a parancs-kezelőket.

- Adja meg a függvényeket a parancs válaszainak elküldéséhez.

- Segítő függvények definiálása a parancsok naplózásához.

- Adjon meg egy segítő függvényt a tulajdonságok létrehozásához.

- Adjon meg egy figyelőt a tulajdonságok frissítéseihez.

- Definiáljon egy függvényt, amely telemetria küld az eszközről. Mindkét termosztát és az alapértelmezett összetevő telemetria küld. Ez a függvény paraméterként fogadja az összetevő nevét.

- Definiáljon egy `main` függvényt, amely:

  - Az eszközoldali SDK használatával hozza létre az eszköz ügyfelet, és csatlakozik az IoT hubhoz. Az eszköz biztosítja, `modelId` hogy a IoT hub azonosítani tudja az eszközt IoT Plug and Play eszközként.

  - A függvény használatával elindítja a parancsokra vonatkozó kérelmek figyelését `onDeviceMethod` . A függvény egy figyelőt állít be a szolgáltatástól érkező parancsokhoz:

    - Az eszköz DTDL határozza meg a `reboot` és a `getMaxMinReport` parancsokat.
    - A `commandHandler` függvény határozza meg, hogy az eszköz hogyan válaszol a parancsra.

  - Elindítja a telemetria küldését a és a használatával `setInterval` `sendTelemetry` .

  - A `helperCreateReportedPropertiesPatch` függvény használatával hozza létre a tulajdonságokat, és `updateComponentReportedProperties` frissíti a tulajdonságokat.

  - A használatával `desiredPropertyPatchListener` figyeli a tulajdonságok frissítéseit.

  - Letiltja az összes figyelőt és feladatot, és a **q** vagy a **q** gomb megnyomásakor kilép a hurokból.

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

A minta-konfigurációval kapcsolatos további tudnivalókért tekintse meg a [minta](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/pnp/readme.md)információit.

Most, hogy már látta a kódot, a következő paranccsal futtathatja a mintát:

```cmd\sh
node pnpTemperatureController.js
```

A következő kimenet jelenik meg, amely azt jelzi, hogy az eszköz megkezdte a telemetria adatok küldését a központba, és most már készen áll a parancsok és a tulajdonságok frissítéseinek fogadására.

![Eszköz megerősítő üzenetei](media/tutorial-multiple-components-node/multiple-component.png)

A következő lépések elvégzése közben tartsa a mintát.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>A kód érvényesítése az Azure IoT Explorer használatával

Az ügyfél mintájának elindítása után az Azure IoT Explorer eszköz használatával ellenőrizze, hogy működik-e.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan csatlakoztathat egy IoT Plug and Play-eszközt az összetevőkkel egy IoT hubhoz. Ha többet szeretne megtudni a IoT Plug and Play eszköz modelljeiről, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [IoT Plug and Play modellezési fejlesztői útmutató](concepts-developer-guide-device-csharp.md)
