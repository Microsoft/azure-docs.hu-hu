---
title: 'Rövid útmutató: a Defender-IoT-Micro-Agent konfigurálása és engedélyezése az Azure RTOS'
description: Ismerje meg, hogyan hozhatja be és engedélyezheti a Defender-IoT-Micro-Agent Azure RTOS szolgáltatást az Azure-IoT Hub.
services: defender-for-iot
ms.topic: quickstart
ms.date: 01/24/2021
ms.openlocfilehash: db0fc0be3cd10bc57a46cb5a056ca5e057ae255a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781160"
---
# <a name="quickstart-defender-iot-micro-agent-for-azure-rtos-preview"></a>Gyors útmutató: Defender-IoT-Micro-Agent Azure RTOS (előzetes verzió)

Ez a cikk az első lépések előtt ismerteti az előfeltételeket, és bemutatja, hogyan engedélyezheti a Defender-IoT-Micro-Agent for Azure RTOS szolgáltatást egy IoT Hub. Ha jelenleg nem rendelkezik IoT Hubval, a kezdéshez tekintse meg [a IoT hub létrehozása a Azure Portal használatával](../iot-hub/iot-hub-create-through-portal.md) című témakört.

## <a name="prerequisites"></a>Előfeltételek 

### <a name="supported-devices"></a>Támogatott eszközök

- ST STM32F746G-felderítési készlet
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Az [Azure RTOS GitHub-erőforráshoz tartozó Defender-IoT-Micro-Agent](https://github.com/azure-rtos/azure-iot-preview/releases)használatával letöltheti, lefordíthatja és futtathatja az adott tábla és eszköz (IAR, fél ide vagy számítógép) egyik. zip fájlját.

### <a name="azure-resources"></a>Azure-erőforrások

Az első lépések előkészítésének következő lépése az Azure-erőforrások előkészítése. Szüksége lesz egy IoT Hubra, és javaslatot teszünk egy Log Analytics-munkaterületre. IoT Hub esetén az eszközhöz való kapcsolódáshoz IoT Hub kapcsolati karakterláncra lesz szüksége. 
  
### <a name="iot-hub-connection"></a>IoT Hub-kapcsolatok

A kezdéshez IoT Hub-kapcsolatok szükségesek. 

1. Nyissa meg a **IoT Hubt** Azure Portal.

1. Navigáljon a **IoT-eszközökhöz**.

1. Válassza a **Létrehozás** lehetőséget.

1. Másolja a IoT-kapcsolódási karakterláncot a [konfigurációs fájlba](how-to-azure-rtos-security-module.md).

A kapcsolatok hitelesítő adatai a felhasználói alkalmazás konfigurációjának **HOST_NAME**, **DEVICE_ID** és **DEVICE_SYMMETRIC_KEY**.

Az Azure RTOS készült Defender-IoT-Micro-Agent Azure IoT middleware-kapcsolatokat használ a **MQTT** protokoll alapján.

## <a name="next-steps"></a>Következő lépések

Folytassa a következő cikkel a megoldás konfigurálásának és testreszabásának befejezéséhez.

> [!div class="nextstepaction"]
> [Defender-IoT-Micro-Agent konfigurálása és testreszabása az Azure RTOS (előzetes verzió)](how-to-azure-rtos-security-module.md)
