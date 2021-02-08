---
title: 'Gyors útmutató: az Azure RTOS biztonsági moduljának konfigurálása és engedélyezése'
description: Ismerje meg, hogyan hozhatja be és engedélyezheti az Azure RTOS szolgáltatás biztonsági modulját az Azure-IoT Hub.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: d7e901e440a6df5ae1d11a150e09a36b565904d9
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99809065"
---
# <a name="quickstart-security-module-for-azure-rtos-preview"></a>Gyors útmutató: biztonsági modul az Azure RTOS (előzetes verzió)

Ez a cikk az első lépések előtt ismerteti az előfeltételeket, és bemutatja, hogyan engedélyezheti az Azure RTOS Service biztonsági modulját egy IoT Hub. Ha jelenleg nem rendelkezik IoT Hubval, a kezdéshez tekintse meg [a IoT hub létrehozása a Azure Portal használatával](../iot-hub/iot-hub-create-through-portal.md) című témakört.

## <a name="prerequisites"></a>Előfeltételek 

### <a name="supported-devices"></a>Támogatott eszközök

- ST STM32F746G-felderítési készlet
- NXP i.MX RT1060 EVK
- Microchip SAM E54 Xplained Pro EVK

Az [Azure RTOS GitHub-erőforrás biztonsági moduljának](https://github.com/azure-rtos/azure-iot-preview/releases)használatával letöltheti, lefordíthatja és futtathatja az adott tábla és eszköz (IAR, fél ide vagy számítógép) egyik. zip fájlját.

### <a name="azure-resources"></a>Azure-erőforrások

Az első lépések előkészítésének következő lépése az Azure-erőforrások előkészítése. Szüksége lesz egy IoT Hubra, és javaslatot teszünk egy Log Analytics-munkaterületre. IoT Hub esetén az eszközhöz való kapcsolódáshoz IoT Hub kapcsolati karakterláncra lesz szüksége. 
  
### <a name="iot-hub-connection"></a>IoT Hub-kapcsolatok

A kezdéshez IoT Hub-kapcsolatok szükségesek. 

1. Nyissa meg a **IoT Hubt** Azure Portal.
1. Navigáljon a **IoT-eszközökhöz**.
1. Válassza a **Létrehozás** lehetőséget.
1. Másolja a IoT-kapcsolódási karakterláncot a [konfigurációs fájlba](how-to-azure-rtos-security-module.md).

A kapcsolatok hitelesítő adatai a felhasználói alkalmazás konfigurációjának **HOST_NAME**, **DEVICE_ID** és **DEVICE_SYMMETRIC_KEY**.

Az Azure RTOS biztonsági modulja az Azure IoT middleware-kapcsolatokat használja a **MQTT** protokoll alapján.

## <a name="next-steps"></a>Következő lépések

Folytassa a következő cikkel a megoldás konfigurálásának és testreszabásának befejezéséhez.

> [!div class="nextstepaction"]
> [Az Azure RTOS biztonsági moduljának konfigurálása](how-to-azure-rtos-security-module.md)