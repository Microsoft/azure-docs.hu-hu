---
title: Microsoft csatlakoztatott gyorsítótár – két szintű beágyazott Azure IoT Edge átjáró kimenő, nem hitelesített proxyval | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Microsoft csatlakoztatott gyorsítótár – kétszintű beágyazott Azure IoT Edge átjáró kimenő, nem hitelesített proxyval – oktatóanyag
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 7facb74cd407c576b2a7b119f19427dcd185f04e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568817"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-sample-two-level-nested-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>A Microsoft csatlakoztatott gyorsítótár előzetes telepítési forgatókönyvének mintája: kétszintű beágyazott Azure IoT Edge átjáró kimenő, nem hitelesített proxyval

Az alábbi ábrának megfelelően ebben a forgatókönyvben van egy Azure IoT Edge átjáró és egy alsóbb rétegbeli Azure IoT Edge-eszköz, egy Azure IoT Edge-átjáró egy másik Azure IoT Edge átjáróhoz, és egy proxykiszolgáló az informatikai DMZ-ben. Alább látható egy példa arra, hogy a Microsoft csatlakoztatott gyorsítótár-környezeti változói hogyan lesznek beállítva a Azure Portal UX-ben a Azure IoT Edge-átjárók számára telepített MCC-modulok esetében. A példában látható példa Azure IoT Edge átjárók kétszintű konfigurációját mutatja be, de nincs korlátozva a Microsoft által csatlakoztatott gyorsítótár által támogatott felsőbb szintű gazdagépek mélysége. Az előző példákban nincs különbség az MCC-tároló létrehozási beállításai között.

A Azure IoT Edge-átjárók rétegzett központi telepítésének konfigurálásával kapcsolatos további részletekért tekintse meg a következő dokumentációt: az alsóbb rétegbeli [IoT Edge eszközök csatlakoztatása Azure IoT Edge](../iot-edge/how-to-connect-downstream-iot-edge-device.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11) . Emellett vegye figyelembe, hogy a Azure IoT Edge, a Microsoft csatlakoztatott gyorsítótára és az egyéni modulok telepítésekor minden modulnak ugyanabban a tároló-beállításjegyzékben kell lennie.

Az alábbi ábra azt a forgatókönyvet ismerteti, amelyben az egyik Azure IoT Edge átjáró a CDN-erőforrásokhoz való közvetlen hozzáférésként viselkedik, mint egy másik Azure IoT Edge átjáró, amely szülőként működik egy Azure IoT Leaf-eszköz, például egy málna PI számára. Csak az Azure IoT Edge átjáró szülője rendelkezik internetkapcsolattal a CDN-erőforrásokhoz, és mind a Azure IoT Edge gyermek, mind az Azure IoT-eszköz internetes elkülönítésben van. 

  :::image type="content" source="media/connected-cache-overview/nested-level-proxy.png" alt-text="A Microsoft csatlakoztatott gyorsítótára egymásba ágyazva" lightbox="media/connected-cache-overview/nested-level-proxy.png":::

## <a name="parent-gateway-configuration"></a>Szülő átjáró konfigurációja

1. Adja hozzá a Microsoft Connected cache modult az Azure IoT Hub Azure IoT Edge átjáró eszközének üzembe helyezéséhez.
2. Adja hozzá a környezet változóit a központi telepítéshez. Alább látható egy példa a környezeti változókra.

    **Környezeti változók**

    | Name                 | Érték                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Lásd a fenti környezeti változók leírása című témakört. |
    | CUSTOMER_ID                   | Lásd a fenti környezeti változók leírása című témakört. |
    | CUSTOMER_KEY                  | Lásd a fenti környezeti változók leírása című témakört. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                          |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                             |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                        |
    | UPSTREAM_PROXY                | Proxykiszolgáló IP-címe vagy teljes tartományneve                     |

3. Adja hozzá a tároló létrehozási beállításait a központi telepítéshez. Az MCC-tároló létrehozási beállításai nem különböznek az előző példától. Az alábbi példa a tároló létrehozási lehetőségeit mutatja be.

### <a name="container-create-options"></a>Tároló-létrehozási beállítások

```markdown
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
```

## <a name="child-gateway-configuration"></a>Alárendelt átjáró konfigurációja

>[!Note]
>Ha a saját privát beállításjegyzékében a konfigurációban használt replikált tárolók vannak, akkor módosítani kell a config. toml beállításait és a futásidejű beállításokat a modul üzembe helyezésében. További információkért tekintse meg az [oktatóanyag – IoT Edge eszközök hierarchiájának létrehozása – Azure IoT Edge](../iot-edge/tutorial-nested-iot-edge.md?preserve-view=true&tabs=azure-portal&view=iotedge-2020-11#deploy-modules-to-the-lower-layer-device) további részleteket.

1. Módosítsa a peremhálózati ügynök rendszerképének elérési útját az alábbi példában bemutatott módon:

```markdown
[agent]
name = "edgeAgent"
type = "docker"
env = {}
[agent.config]
image = "<parent_device_fqdn_or_ip>:8000/iotedge/azureiotedge-agent:1.2.0-rc2"
auth = {}
```
2. Módosítsa az Edge hub és az Edge Agent futtatókörnyezet beállításait a Azure IoT Edge üzemelő példányban, ahogy az a következő példában látható:
    
    * Az Edge hub terület rendszerkép mezőjébe írja be a ```$upstream:8000/iotedge/azureiotedge-hub:1.2.0-rc2```
    * Az Edge-ügynök területen a rendszerkép mezőbe írja be a ```$upstream:8000/iotedge/azureiotedge-agent:1.2.0-rc2```

3. Adja hozzá a Microsoft Connected cache modult az Azure IoT Hub Azure IoT Edge átjáró eszközének üzembe helyezéséhez.

   * Válassza ki a modul nevét: ```ConnectedCache```
   * Módosítsa a rendszerkép URI-JÁT: ```$upstream:8000/mcc/linux/iot/mcc-ubuntu-iot-amd64:latest```

4. Adja hozzá ugyanazt a környezeti változót és a tároló létrehozási beállításait a fölérendelt központi telepítésben.

A megfelelően működő Microsoft-gyorsítótár megfelelő működésének ellenőrzéséhez hajtsa végre a következő parancsot a modult futtató IoT Edge eszköz terminálján, vagy a hálózaton lévő összes eszközt.

```bash
    wget "http://<CHILD Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```