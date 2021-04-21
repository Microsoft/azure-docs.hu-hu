---
title: A Microsoft Csatlakoztatott gyorsítótár szintű beágyazott átjárót Azure IoT Edge kimenő, nem hitelesített proxyval | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft Csatlakoztatott gyorsítótár szintű beágyazott átjáró Azure IoT Edge kimenő, nem hitelesített proxyval oktatóanyag
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 623ce808423f76ae1be079e0424fe3ddf27d1d58
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811885"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Microsoft Csatlakoztatott gyorsítótár előzetes verzió üzembe helyezési forgatókönyvének mintája: Kétszintű beágyazott átjáró Azure IoT Edge kimenő, nem hitelesített proxyval

Az alábbi ábra azt a forgatókönyvet mutatja be, amikor egy Azure IoT Edge-átjáró közvetlen hozzáféréssel rendelkezik a CDN-erőforrásokhoz, és egy másik átjáró szülőjeként Azure IoT Edge működik. A gyermek-IoT Edge átjáró egy Azure IoT levéleszköz, például egy Raspberry Pi szülőjeként működik. A gyermek Azure IoT Edge és az Azure IoT-eszköz is el van különítve az internettől. Az alábbi példa bemutatja a Azure IoT Edge-átjárók kétszintes konfigurációját, de nincs korlátozva a Microsoft által támogatott felsőbb rétegbeli gazdagépek Csatlakoztatott gyorsítótár mélysége. A Microsoft és a Csatlakoztatott gyorsítótár között nincs különbség az előző példákból származó tároló-létrehozási lehetőségek között.

Az átjárók réteges üzembe helyezésének konfigurálásával kapcsolatos további részletekért tekintse meg az IoT Edge [Azure IoT Edge-IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) eszközök csatlakoztatása – Azure IoT Edge dokumentációt. Azt is vegye figyelembe, hogy a Azure IoT Edge, a Microsoft Csatlakoztatott gyorsítótár és az egyéni modulok üzembe helyezésekor minden modulnak ugyanabban a tároló-beállításjegyzékben kell lennie.

>[!Note]
>A virtuális Azure IoT Edge, a Microsoft Csatlakoztatott gyorsítótár és az egyéni modulok üzembe helyezésekor minden modulnak ugyanabban a tárolójegyzékben kell lennie.

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="Microsoft Csatlakoztatott gyorsítótár beágyazott" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Szülőátjáró konfigurálása
1. Adja hozzá a Microsoft Csatlakoztatott gyorsítótár modult az Azure IoT Edge-átjáróeszköz üzembe helyezéséhez [](connected-cache-disconnected-device-update.md) a Azure IoT Hub-ban (a modul lekért verziójának részleteiért lásd: Leválasztott eszközök támogatása).
2. Adja hozzá az üzembe helyezés környezeti változóit. Az alábbiakban egy példa látható a környezeti változókra.

    **Környezeti változók**

    | Name                          | Érték                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Környezeti [változók leírásának](connected-cache-configure.md) lásd: |
    | CUSTOMER_ID                   | Lásd [a környezeti változók](connected-cache-configure.md) leírását |
    | CUSTOMER_KEY                  | Lásd [a környezeti változók](connected-cache-configure.md) leírását |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |

3. Adja hozzá a tároló létrehozási beállításait az üzembe helyezéshez. Az MCC-tároló létrehozási beállításai között nincs különbség az előző példában. Az alábbiakban egy példa látható a tároló létrehozására szolgáló beállításokra.

### <a name="container-create-options"></a>Tároló-létrehozási lehetőségek

```json
{
    "HostConfig": {
        "Binds": [
            "/MicrosoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="child-gateway-configuration"></a>Gyermekátjáró konfigurálása

>[!Note]
>Ha a konfigurációjában replikált tárolókat a saját privát beállításjegyzékében, akkor módosítania kell a config.toml és a futásidejű beállításokat a modul üzembe helyezése során. További információ: Lefelé irányuló IoT Edge csatlakoztatása – [Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-lower-layer-devices) további információ.


1. Módosítsa az Edge-ügynök képútvonalát az alábbi példában látható módon:

    ```markdown
    [agent]
    name = "edgeAgent"
    type = "docker"
    env = {}
    [agent.config]
    image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
    auth = {}
    ```
2. Módosítsa az Edge Hub és az Edge-ügynök futásidejű beállításait a Azure IoT Edge telepítésben az alábbi példában szemléltetettek szerint:
    
    * Az Edge Hub területen a kép mezőbe írja be a következőt: ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * Az Edge Agent területen a kép mezőbe írja be a következőt: ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Adja hozzá a Microsoft Csatlakoztatott gyorsítótár modult a Azure IoT Edge-átjáróeszköz üzembe helyezéséhez a Azure IoT Hub.

   * Válasszon nevet a modulnak: ```ConnectedCache```
   * Módosítsa a rendszerkép URI-ét: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Adja hozzá a szülőtelepítésben használt környezeti változókat és tároló-létrehozási beállításokat.
>[!Note]
>A CACHE_NODE_ID egyedinek kell lennie.  A CUSTOMER_ID és CUSTOMER_KEY azonosak lesznek a szülővel. [(lásd: A Microsoft Csatlakoztatott gyorsítótár](connected-cache-configure.md)

A Microsoft Csatlakoztatott gyorsítótár megfelelő működésének ellenőrzéséhez hajtsa végre a következő parancsot a modult vagy a hálózaton található bármely eszközt üzemeltető IoT Edge-eszköz terminálán. Cserélje le a helyére a saját \<Azure IoT Edge Gateway IP\> átjárója IP-címét IoT Edge állomásnevét. (A jelentés láthatóságával kapcsolatos információkért lásd a környezeti változó részleteit).

```bash
    wget http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```