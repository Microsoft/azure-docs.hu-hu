---
title: A Microsoft Csatlakoztatott gyorsítótár előzetes verzió üzembe helyezési forgatókönyvének mintái | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Oktatóanyagok Csatlakoztatott gyorsítótár Microsoft Csatlakoztatott gyorsítótár üzembe helyezési forgatókönyvének mintáihoz
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: c116bbf5ea9f5fc6e58962e02c93c630fc747d9e
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811723"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>Példák Csatlakoztatott gyorsítótár Microsoft Csatlakoztatott gyorsítótár üzembe helyezési forgatókönyvére

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Egyszintű Azure IoT Edge átjáró proxy nélkül

Az alábbi ábra azt a forgatókönyvet mutatja be, amelyben egy CDN-erőforrásokhoz közvetlen hozzáféréssel Azure IoT Edge-átjáró található, és van egy Azure IoT levéleszköz, például egy Raspberry PI, amely az Azure IoT Edge-átjáró internetes elkülönített gyermekeszköze. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Microsoft Csatlakoztatott gyorsítótár leválasztott eszköz frissítése" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Adja hozzá a Microsoft Csatlakoztatott gyorsítótár modult az Azure IoT Edge-átjáróeszköz üzembe helyezéséhez [](connected-cache-disconnected-device-update.md) a Azure IoT Hub-ban (a modul lekért verziójának részleteiért lásd: Leválasztott eszközök támogatása).
2. Adja hozzá az üzembe helyezés környezeti változóit. Az alábbiakban egy példa látható a környezeti változókra.

    **Környezeti változók**
    
    | Name                          | Érték                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Környezeti [változók leírásának](connected-cache-configure.md) lásd: |
    | CUSTOMER_ID                   | Környezeti [változók leírásának](connected-cache-configure.md) lásd: |
    | CUSTOMER_KEY                  | Környezeti [változók leírásának](connected-cache-configure.md) lásd: |
    | STORAGE_1_SIZE_GB             | 10                                                                    |

3. Adja hozzá a tároló létrehozási beállításait az üzembe helyezéshez. Az alábbi példa a tárolók létrehozási lehetőségeit mutatja be.

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

A Microsoft Csatlakoztatott gyorsítótár megfelelő működésének ellenőrzéséhez hajtsa végre a következő parancsot IoT Edge modult vagy a hálózaton található bármely eszközt üzemeltető IoT Edge terminálon. Cserélje le a helyére a saját átjárója \<Azure IoT Edge Gateway IP\> IP-címét vagy IoT Edge nevét. (A jelentés láthatóságával kapcsolatos információkért lásd a környezeti változó részleteit).

```bash
    wget http://<IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Egyszintű Azure IoT Edge nem hitelesített proxyval

Ebben a forgatókönyvben egy Azure IoT Edge átjáró, amely egy kimenő, nem hitelesített proxyn keresztül fér hozzá a CDN-erőforrásokhoz. A Microsoft Csatlakoztatott gyorsítótár úgy van konfigurálva, hogy egy egyéni adattárból gyorsítótárazza a tartalmakat, és az összegző jelentés láthatóvá lett tenni a hálózat minden felhasználója számára. Az alábbiakban egy példa látható a beállítható MCC környezeti változókra.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="Microsoft Csatlakoztatott gyorsítótár szintű proxy" lightbox="media/connected-cache-overview/single-level-proxy.png":::

1. Adja hozzá a Microsoft Csatlakoztatott gyorsítótár modult a Azure IoT Edge-átjáróeszköz üzembe helyezéséhez a Azure IoT Hub.
2. Adja hozzá az üzemelő példány környezeti változóit. Az alábbiakban egy példa látható a környezeti változókra.

    **Környezeti változók**

    | Name                          | Érték                                                                 |
    | ----------------------------- | ----------------------------------------------------------------------| 
    | CACHE_NODE_ID                 | Lásd [a környezeti változók](connected-cache-configure.md) leírását |
    | CUSTOMER_ID                   | Lásd [a környezeti változók](connected-cache-configure.md) leírását |
    | CUSTOMER_KEY                  | Lásd [a környezeti változók](connected-cache-configure.md) leírását |
    | STORAGE_1_SIZE_GB             | 10                                                                    |
    | CACHEABLE_CUSTOM_1_HOST       | Packagerepo.com:80                                                    |
    | CACHEABLE_CUSTOM_1_CANONICAL  | Packagerepo.com                                                       |
    | IS_SUMMARY_ACCESS_UNRESTRICTED| true                                                                  |
    | UPSTREAM_PROXY                | A proxykiszolgáló IP-címe vagy teljes tartomány neve                                          |

3. Adja hozzá a tároló létrehozási beállításait az üzembe helyezéshez. Az MCC-tároló létrehozási lehetőségei nem hatnak az előző példára. Az alábbi példa a tárolók létrehozási lehetőségeit mutatja be.

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

A Microsoft Csatlakoztatott gyorsítótár megfelelő működésének ellenőrzéséhez hajtsa végre a következő parancsot a modult üzemeltető Azure IoT Edge-eszköz vagy a hálózaton található bármely eszköz terminálán. Cserélje le a helyére a saját \<Azure IoT Edge Gateway IP\> átjárója IP-címét IoT Edge állomásnevét. (A jelentés láthatóságával kapcsolatos információkért lásd a környezeti változó részleteit).

```bash
    wget http://<Azure IoT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com 
```
