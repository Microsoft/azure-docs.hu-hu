---
title: Az Azure IoT Hub üzenetkezelés ismertetése | Microsoft Docs
description: Fejlesztői útmutató – eszközről a felhőbe és a felhőből az eszközre irányuló üzenetküldés IoT Hub használatával. Az üzenetek formátumával és a támogatott kommunikációs protokollokkal kapcsolatos információkat tartalmazza.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: e25b9d6cbe8446fb2f10150f17f7e2b0ccf85d3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147623"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Eszközről a felhőbe és a felhőből az eszközre irányuló üzenetek küldése IoT Hub

IoT Hub lehetővé teszi a kétirányú kommunikációt az eszközökkel. IoT Hub üzenetkezeléssel kommunikálhat az eszközökkel, ha üzeneteket küld az eszközökről a megoldási háttérbe, és parancsokat küld a IoT-megoldásairól a saját eszközeire. További információ a [IoT hub üzenet formátumáról](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Eszközről a felhőbe irányuló üzenetek küldése IoT Hub

IoT Hub rendelkezik egy beépített szolgáltatás-végponttal, amelyet a háttér-szolgáltatások használhatnak a telemetria üzenetek olvasásához az eszközökről. Ez a végpont kompatibilis a [Event Hubskel](../event-hubs/index.yml) , és a szabványos IoT hub SDK-k használatával a [beépített végpontból is elolvasható](iot-hub-devguide-messages-read-builtin.md).

A IoT Hub támogatja az [Egyéni végpontokat](iot-hub-devguide-endpoints.md#custom-endpoints) is, amelyeket a felhasználók az eszközök telemetria és eseményeinek az Azure-szolgáltatásokhoz való küldésére használhatnak az [üzenet-útválasztás](iot-hub-devguide-messages-d2c.md)használatával.

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>A felhőből az eszközre irányuló üzenetek küldése IoT Hub

A megoldás háttérbeli üzeneteiről az eszközökre küldheti a [felhőből az eszközre](iot-hub-devguide-messages-c2d.md) irányuló üzeneteket.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A IoT Hub üzenetkezelési funkcióinak alapvető tulajdonságai az üzenetek megbízhatósága és tartóssága. Ezek a tulajdonságok lehetővé teszik a rugalmasságot az eszköz oldalának időnkénti kapcsolatára, valamint a Felhőbeli események feldolgozására vonatkozó tüskék betöltését. IoT Hub *legalább egyszer* implementálja az eszközről a felhőbe és a felhőből az eszközre irányuló üzenetküldéshez szükséges garanciákat.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>A megfelelő típusú IoT Hub üzenetkezelés kiválasztása

Az eszközről a felhőbe irányuló üzenetek használatával idősorozat-telemetria és-riasztásokat küldhet az eszköz alkalmazásaiból, valamint a felhőből az eszközre irányuló üzeneteket az eszköz alkalmazására vonatkozó egyirányú értesítésekhez.

* Tekintse meg az [eszközről a felhőbe irányuló kommunikációs útmutatót](./iot-hub-devguide-d2c-guidance.md) az eszközről a felhőbe irányuló üzenetek, a jelentett tulajdonságok vagy a fájlfeltöltés közötti választáshoz.

* Tekintse át a [felhőből az eszközre irányuló kommunikációs útmutatót](./iot-hub-devguide-c2d-guidance.md) a felhőből az eszközre irányuló üzenetek, a kívánt tulajdonságok vagy a közvetlen metódusok közötti választáshoz.

## <a name="next-steps"></a>Következő lépések

* Ismerkedjen meg IoT Hub [üzenet-útválasztással](iot-hub-devguide-messages-d2c.md).

* További tudnivalók a [felhőből az eszközökre IoT hub üzenetkezelésről](iot-hub-devguide-messages-c2d.md).