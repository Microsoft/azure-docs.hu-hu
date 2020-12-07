---
title: Oktatóanyag – video Analytics-objektum-és mozgásészlelési alkalmazás létrehozása az Azure IoT Central (YOLO v3)
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy video Analytics-alkalmazást a IoT Centralban. Létrehozhatja, testreszabhatja és kapcsolódhat más Azure-szolgáltatásokhoz. Ez az oktatóanyag az YOLO v3 valós idejű objektum-felismerő rendszerét használja.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: ecc32908aea2fb474d2ebe5bd94f556527eda814
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763446"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-yolo-v3"></a>Oktatóanyag: video Analytics-objektumok és mozgásészlelési alkalmazások létrehozása az Azure IoT Centralban (YOLO v3)

Megoldás-szerkesztőként megtudhatja, hogyan hozhat létre video Analytics-alkalmazást a IoT Central *video Analytics-Object és Motion Detection* alkalmazás sablonnal, Azure IoT Edge eszközökkel, Azure Media Servicesekkel és a YOLO v3 valós idejű objektum-és mozgásészlelési rendszerrel. A megoldás egy kiskereskedelmi áruházban mutatja be, hogyan teljesíti a közös üzleti igényeket a biztonsági kamerák figyelésére. A megoldás automatikus objektum-észlelést használ egy videó-hírcsatornában érdekes események gyors azonosításához és megkereséséhez.

> [!TIP]
> Ha a YOLO v3 helyett a OpenVINO szeretné használni az &trade; objektumhoz, tekintse meg a következőt [: oktatóanyag: video Analytics-objektum és mozgásészlelési alkalmazás létrehozása az Azure IoT Centralban (OpenVINO &trade; )](tutorial-video-analytics-create-app-openvino.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) – ez a fájl segítséget nyújt a szükséges különböző konfigurációs beállítások rögzítésében az oktatóanyagokon végzett munka során.
- [deployment.amd64.jsbekapcsolva](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.amd64.json)
- [LvaEdgeGatewayDcm.jsbekapcsolva](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json) – csak akkor kell letöltenie ezt a fájlt, ha azt tervezi, hogy az Intel NUC eszközt használja a második oktatóanyagban.

> [!NOTE]
> A GitHub-tárház tartalmazza a **LvaEdgeGatewayModule** és a **lvaYolov3** IoT Edge moduljainak forráskódját is. További információ a forráskód használatáról: a [LVA-átjáró moduljainak összeállítása](tutorial-video-analytics-build-module.md).

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Az üzembe helyezési jegyzék szerkesztése

Üzembe helyez egy IoT Edge modult egy üzembe helyezési jegyzékfájl használatával. IoT Central importálhatja a jegyzékfájlt sablonként, majd engedélyezheti IoT Central a modul központi telepítésének kezelését.

Az üzembe helyezési jegyzék előkészítése:

1. Nyissa meg a *deployment.amd64.js* fájlt, amelyet a *LVA-konfigurációs* mappában mentett a szövegszerkesztő használatával.

1. Keresse meg a `LvaEdgeGatewayModule` beállításokat, és győződjön meg arról, hogy a rendszerkép neve az alábbi kódrészletben látható:

    ```json
    "LvaEdgeGatewayModule": {
        "settings": {
            "image": "mcr.microsoft.com/lva-utilities/lva-edge-iotc-gateway:1.0-amd64",
    ```

1. Adja hozzá a Media Services fiók nevét a `env` szakasz csomópontjában `LvaEdgeGatewayModule` . A *scratchpad.txt* fájlban a Media Services fiók nevét is megjegyezte:

    ```json
    "env": {
        "lvaEdgeModuleId": {
            "value": "lvaEdge"
        },
        "amsAccountName": {
            "value": "<YOUR_AZURE_MEDIA_SERVICES_ACCOUNT_NAME>"
        }
    }
    ```

1. A sablon nem teszi elérhetővé ezeket a tulajdonságokat IoT Centralban, ezért hozzá kell adnia a Media Services konfigurációs értékeit az üzembe helyezési jegyzékhez. Keresse meg a `lvaEdge` modult, és cserélje le a helyőrzőket azokra az értékekre, amelyeket a Media Services-fiók létrehozásakor a *scratchpad.txt* fájlban jegyzett adott meg.

    Az az `azureMediaServicesArmId` **erőforrás-azonosító** , amelyet a Media Services fiók létrehozásakor a *scratchpad.txt* fájlban jegyzett készített.

    A következő táblázat a telepítési jegyzékfájlban használni kívánt *scratchpad.txt* fájlban található **Kapcsolódás Media Services API (JSON)** értékeit mutatja be:

    | Üzembehelyezési jegyzék       | Firkatömb  |
    | ------------------------- | ----------- |
    | aadTenantId               | AadTenantId |
    | aadServicePrincipalAppId  | AadClientId |
    | aadServicePrincipalSecret | AadSecret   |

    > [!CAUTION]
    > Az előző táblázat használatával győződjön meg arról, hogy a megfelelő értékeket adja hozzá a központi telepítési jegyzékfájlhoz, ellenkező esetben az eszköz nem fog működni.

    ```json
    {
        "lvaEdge":{
        "properties.desired": {
            "applicationDataDirectory": "/var/lib/azuremediaservices",
            "azureMediaServicesArmId": "[Resource ID from scratchpad]",
            "aadTenantId": "[AADTenantID from scratchpad]",
            "aadServicePrincipalAppId": "[AadClientId from scratchpad]",
            "aadServicePrincipalSecret": "[AadSecret from scratchpad]",
            "aadEndpoint": "https://login.microsoftonline.com",
            "aadResourceId": "https://management.core.windows.net/",
            "armEndpoint": "https://management.azure.com/",
            "diagnosticsEventsOutputName": "AmsDiagnostics",
            "operationalMetricsOutputName": "AmsOperational",
            "logCategories": "Application,Event",
            "AllowUnsecuredEndpoints": "true",
            "TelemetryOptOut": false
            }
        }
    }
    ```

1. Mentse a módosításokat.

Ez az oktatóanyag konfigurálja a megoldást arra, hogy a YOLO v3 modult használja az objektumok és a mozgásérzékelők észleléséhez. A következő kódrészlet a modul konfigurációját mutatja be:

```json
"lvaYolov3": {
    "settings": {
        "image": "mcr.microsoft.com/lva-utilities/yolov3-onnx:1.0"
    },
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "version": "1.0"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>A jegyzékfájl cseréje

Az **LVA Edge Gateway v2** lapon válassza a **+ jegyzékfájl cseréje** lehetőséget.

:::image type="content" source="./media/tutorial-video-analytics-create-app-yolo-v3/replace-manifest.png" alt-text="Jegyzékfájl cseréje":::

Navigáljon a *LVA* mappára, és válassza ki a korábban szerkesztett *deployment.amd64.jsa* jegyzékfájlban. Válassza a **Feltöltés** lehetőséget. Az ellenőrzés befejezésekor válassza a **replace (csere**) lehetőséget.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]
