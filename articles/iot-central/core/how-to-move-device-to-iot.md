---
title: Eszköz áthelyezése a Azure IoT Central IoT Hub
description: Eszköz áthelyezése a Azure IoT Central IoT Hub
author: philmea
ms.author: philmea
ms.date: 02/20/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 79aead5b374714e7856897a9b85349198341cb3d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872730"
---
# <a name="how-to-transfer-a-device-to-azure-iot-central-from-iot-hub"></a>Eszköz átvitele a Azure IoT Central IoT Hub

*Ez a cikk a kezelőkre és az eszközfejlesztőkre vonatkozik.*  

Ez a cikk azt ismerteti, hogyan továbbítható egy eszköz Azure IoT Central egy IoT Hub. 

Az eszközök először egy DPS-végponthoz csatlakoznak, hogy lekérik az alkalmazáshoz való csatlakozáshoz szükséges információkat. Belsőleg a IoT Central egy IoT Hubot használ az eszközkapcsolat kezeléséhez.  

Az eszközök közvetlenül, kapcsolati sztring használatával vagy DPS használatával is csatlakoztathatóak az IoT Hubhoz. [Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) a szolgáltatás IoT Central.

## <a name="to-move-the-device-to-azure-iot-central"></a>Az eszköz áthelyezése Azure IoT Central

Ha csatlakoztatni szeretne egy eszközt IoT Central a IoT Hub az eszközt a következővel kell frissíteni:

* A [IoT Central](../../iot-dps/concepts-service.md) alkalmazás hatókör-azonosítója.
* A csoportos [SAS-kulcsból](concepts-get-connected.md) vagy az [X.509-tanúsítványból származtatott kulcs](../../iot-hub/iot-hub-x509ca-overview.md)

A IoT Central kell lennie egy eszközsablonnak, amely az eszköz által implementálja a tulajdonságokat/telemetriai adatokat/parancsokat. További információ: [Csatlakozás a](concepts-get-connected.md) IoT Central és [Mik azok az eszközsablonok?](concepts-device-templates.md)

## <a name="next-steps"></a>Következő lépések

Ha Ön eszközfejlesztő, néhány javasolt következő lépés a következő:

- Tekintsen át néhány mintakódot, amely bemutatja, hogyan használhatók az SAS-jogkivonatok az Ügyfélalkalmazás létrehozása és csatlakoztatása a saját Azure IoT Central [oktatóanyagban](tutorial-connect-device.md)
- Ismerje meg, [hogyan csatlakoztathat X.509-tanúsítványokkal](how-to-connect-devices-x509.md) eszközöket az Node.js Sdk for IoT Central Application használatával
- Útmutató az [eszközkapcsolatok figyelése az Azure CLI használatával](./howto-monitor-devices-azure-cli.md)
- Megtudhatja, [hogyan definiálhat új IoT-eszköztípust a Azure IoT Central alkalmazásban](./howto-set-up-template.md)
- További információ [Azure IoT Edge eszközökről és Azure IoT Central](./concepts-iot-edge.md)