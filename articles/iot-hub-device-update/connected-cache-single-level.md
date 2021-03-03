---
title: A Microsoft csatlakoztatott gyorsítótár előzetes telepítési forgatókönyvének mintái | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: A Microsoft csatlakoztatott gyorsítótár előzetes verziójának telepítési forgatókönyve minták oktatóanyagok
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ae07926d7d8c768170e945e916367bee41999571
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664774"
---
# <a name="microsoft-connected-cache-preview-deployment-scenario-samples"></a>A Microsoft csatlakoztatott gyorsítótár előzetes telepítési forgatókönyvének mintái

## <a name="single-level-azure-iot-edge-gateway-no-proxy"></a>Egyetlen szintű Azure IoT Edge átjáró nem rendelkezik proxyval

Az alábbi ábra azt a forgatókönyvet ismerteti, amelyben egy Azure IoT Edge átjáró, amely közvetlen hozzáférést biztosít a CDN-erőforrásokhoz, és létezik egy Azure IoT Leaf-eszköz, például egy málna PI, amely a Azure IoT Edge átjáró internetes elszigetelt gyermek eszközei. 

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="A Microsoft csatlakoztatott gyorsítótára megszakadt az eszköz frissítése" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Adja hozzá a Microsoft Connected cache modult a Azure IoT Edge átjáró eszközének üzembe helyezéséhez az Azure IoT Hubban (lásd a `MCC concepts` modul beszerzésének részleteit).
2. Adja hozzá a környezet változóit a központi telepítéshez. Alább látható egy példa a környezeti változókra.

    **Környezeti változók**
    
    | Name                 | Érték                                       |
    | ----------------------------- | --------------------------------------------| 
    | CACHE_NODE_ID                 | Lásd a fenti környezeti változók leírása című témakört. |
    | CUSTOMER_ID                   | Lásd a fenti környezeti változók leírása című témakört. |
    | CUSTOMER_KEY                  | Lásd a fenti környezeti változók leírása című témakört. |
    | STORAGE_ *N* _SIZE_GB           | N = 5                                       |

3. Adja hozzá a tároló létrehozási beállításait a központi telepítéshez. Az alábbi példa a tároló létrehozási lehetőségeit mutatja be.

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

A megfelelően működő Microsoft-gyorsítótár megfelelő működésének ellenőrzéséhez hajtsa végre a következő parancsot a modult futtató IoT Edge eszköz terminálján, vagy a hálózaton lévő összes eszközt.

```bash
    wget "http://<IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```

## <a name="single-level-azure-iot-edge-gateway-with-outbound-unauthenticated-proxy"></a>Egyetlen szintű Azure IoT Edge átjáró kimenő, nem hitelesített proxyval

Ebben a forgatókönyvben van egy Azure IoT Edge átjáró, amely egy kimenő, nem hitelesített proxyn keresztül fér hozzá a CDN-erőforrásokhoz. A Microsoft csatlakoztatott gyorsítótára úgy van konfigurálva, hogy egy egyéni tárházból gyorsítótárazza a tartalmakat, és az összegző jelentést mindenki számára láthatóvá tette a hálózaton. Alább látható egy példa arra, hogy milyen ÜGYFÉLKÖZPONT-környezeti változók legyenek beállítva.

  :::image type="content" source="media/connected-cache-overview/single-level-proxy.png" alt-text="A Microsoft csatlakoztatott gyorsítótárának egyetlen szintű proxyja" lightbox="media/connected-cache-overview/single-level-proxy.png":::

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
```

A megfelelően működő Microsoft-gyorsítótár megfelelő működésének ellenőrzéséhez hajtsa végre a következő parancsot a modult futtató Azure IoT Edge eszköz terminálján, vagy a hálózaton lévő összes eszközt.

```bash
    wget "http://<Azure IOT Edge Gateway IP>/mscomtest/wuidt.gif?cacheHostOrigin=au.download.windowsupdate.com
```
