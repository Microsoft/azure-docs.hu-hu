---
title: Oktatóanyag – video Analytics-objektumok és mozgásészlelési alkalmazások létrehozása az Azure IoT Central (OpenVINO)
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre egy video Analytics-alkalmazást a IoT Centralban. Létrehozhatja, testreszabhatja és kapcsolódhat más Azure-szolgáltatásokhoz. Ez az oktatóanyag az Intel OpenVINO &trade; eszközkészletet használja a valós idejű objektumok észleléséhez.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
author: KishorIoT
ms.author: nandab
ms.date: 10/06/2020
ms.openlocfilehash: a201a0300cb4ae0fba1a41b5f64838c17904fa83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99832096"
---
# <a name="tutorial-create-a-video-analytics---object-and-motion-detection-application-in-azure-iot-central-openvinotrade"></a>Oktatóanyag: video Analytics-objektumok és mozgásészlelési alkalmazások létrehozása az Azure IoT Centralban (OpenVINO &trade; )

Megoldás-szerkesztőként megtudhatja, hogyan hozhat létre video Analytics-alkalmazást a IoT Central *video Analytics-Object és Motion Detection* alkalmazás sablonnal, Azure IoT Edge eszközökkel, Azure Media Servicesekkel és az Intel hardveres optimalizált OpenVINO &trade; az objektumok és a mozgásészlelés számára. A megoldás egy kiskereskedelmi áruházban mutatja be, hogyan teljesíti a közös üzleti igényeket a biztonsági kamerák figyelésére. A megoldás automatikus objektum-észlelést használ egy videó-hírcsatornában érdekes események gyors azonosításához és megkereséséhez.

> [!TIP]
> Ha a YOLO v3-et szeretné használni a OpenVINO helyett az &trade; objektumhoz, tekintse meg a következőt [: oktatóanyag: video Analytics-objektum és mozgásészlelési alkalmazás létrehozása az Azure IOT Central (YOLO v3)](tutorial-video-analytics-create-app-yolo-v3.md).

[!INCLUDE [iot-central-video-analytics-part1](../../../includes/iot-central-video-analytics-part1.md)]

- [Scratchpad.txt](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/Scratchpad.txt) – ez a fájl segítséget nyújt a szükséges különböző konfigurációs beállítások rögzítésében az oktatóanyagokon végzett munka során.
- [deployment.openvino.amd64.jsbekapcsolva](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/deployment.openvino.amd64.json)
- [LvaEdgeGatewayDcm.jsbekapcsolva](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/LvaEdgeGatewayDcm.json)
- [state.js](https://raw.githubusercontent.com/Azure/live-video-analytics/master/ref-apps/lva-edge-iot-central-gateway/setup/state.json) – csak akkor kell letöltenie ezt a fájlt, ha azt tervezi, hogy az Intel NUC eszközt használja a második oktatóanyagban.

[!INCLUDE [iot-central-video-analytics-part2](../../../includes/iot-central-video-analytics-part2.md)]

## <a name="edit-the-deployment-manifest"></a>Az üzembe helyezési jegyzék szerkesztése

Üzembe helyez egy IoT Edge modult egy üzembe helyezési jegyzékfájl használatával. IoT Central importálhatja a jegyzékfájlt sablonként, majd engedélyezheti IoT Central a modul központi telepítésének kezelését.

Az üzembe helyezési jegyzék előkészítése:

1. Nyissa meg a *deployment.openvino.amd64.js* fájlt, amelyet a *LVA-konfigurációs* mappában mentett a szövegszerkesztő használatával.

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

Ez az oktatóanyag konfigurálja a megoldást arra, hogy a OpenVINO &trade; modult használja az objektumok és a mozgásérzékelők észleléséhez. A következő kódrészlet a modul konfigurációját mutatja be:

```json
"OpenVINOModelServerEdgeAIExtensionModule": {
    "settings": {
        "image": "marketplace.azurecr.io/intel_corporation/open_vino",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"4000/tcp\":[{\"HostPort\":\"4000\"}]}},\"Cmd\":[\"/ams_wrapper/start_ams.py\",\"--ams_port=4000\",\"--ovms_port=9000\"]}"
    },
    "type": "docker",
    "version": "1.0",
    "status": "running",
    "restartPolicy": "always"
}
```

[!INCLUDE [iot-central-video-analytics-part3](../../../includes/iot-central-video-analytics-part3.md)]

### <a name="replace-the-manifest"></a>A jegyzékfájl cseréje

Az **LVA Edge Gateway v2** lapon válassza a **+ jegyzékfájl cseréje** lehetőséget.

:::image type="content" source="./media/tutorial-video-analytics-create-app-openvino/replace-manifest.png" alt-text="Jegyzékfájl cseréje":::

Navigáljon a *LVA* mappára, és válassza ki a korábban szerkesztett *deployment.openvino.amd64.jsa* jegyzékfájlban. Válassza a **Feltöltés** lehetőséget. Az ellenőrzés befejezésekor válassza a **replace (csere**) lehetőséget.

[!INCLUDE [iot-central-video-analytics-part4](../../../includes/iot-central-video-analytics-part4.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az alkalmazással, a következőképpen távolíthatja el az összes létrehozott erőforrást:

1. A IoT Central alkalmazásban navigáljon az **alkalmazás** lapjára az **Adminisztráció** szakaszban. Ezután válassza a **Törlés** elemet.
1. A Azure Portal törölje a **LVA-RG** erőforráscsoportot.
1. A helyi gépen állítsa le az **amp-Viewer** Docker-tárolót.

## <a name="next-steps"></a>Következő lépések

Ezzel létrehozott egy IoT Central alkalmazást a **video Analytics-Object és a mozgásérzékelő** alkalmazás sablonnal, létrehozott egy eszköz sablont az átjáró eszközhöz, és hozzáadta az átjáró-eszközt az alkalmazáshoz.

Ha olyan IoT Edge-modulokkal szeretné kipróbálni a video Analytics – Object és Motion Detection alkalmazást, amelyeken szimulált videó streamekkel rendelkező felhőalapú virtuális gépek futnak:

> [!div class="nextstepaction"]
> [IoT Edge-példány létrehozása a video analyticshez (Linux rendszerű virtuális gép)](tutorial-video-analytics-iot-edge-vm.md)

Ha olyan IoT Edge-modulokkal szeretné kipróbálni a video Analytics – Object és Motion Detection alkalmazást, amelyek valódi **ONVIF** kamera használatával futtatnak valós eszközt:

> [!div class="nextstepaction"]
> [IoT Edge-példány létrehozása a video analyticshez (Intel NUC)](tutorial-video-analytics-iot-edge-nuc.md)