---
title: Fejlesztés Azure IoT SDK nélkül | Microsoft Docs
description: Fejlesztői útmutató – az eszközök és a háttérbeli alkalmazások Azure IoT SDK használata nélküli létrehozásához használható témakörökre mutató hivatkozások.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: ae8b01522a248b8b1dbdd255a9fcd55f16cf2369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461711"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Fejlesztés Azure IoT Hub SDK használata nélkül

Ez a témakör hasznos információkat és hivatkozásokat tartalmaz azoknak a fejlesztőknek, akik az Azure IoT SDK-k használata nélkül szeretnének eszközöket vagy háttérbeli alkalmazásokat fejleszteni.

A Microsoft határozottan javasolja az Azure IoT SDK használatát. Az Azure IoT-eszköz és a szolgáltatás SDK-kat számos népszerű platformon közzétesszük. Az SDK-k olyan kényelmi réteget biztosítanak, amely a mögöttes kommunikációs protokoll összetettségét kezeli, beleértve az eszköz kapcsolatát és újrakapcsolását, valamint az újrapróbálkozási szabályzatot. Az SDK-k rendszeresen frissülnek a IoT Hub és a biztonsági frissítések által elérhető legújabb funkciók biztosításához. Az SDK-k segítségével csökkentheti a kód karbantartására fordított fejlesztési időt és időt. Ha többet szeretne megtudni az Azure IoT SDK-ról, tekintse meg az [Azure IoT-eszközök és-szolgáltatások SDK](iot-hub-devguide-sdks.md)-kat. Az Azure IoT SDK használatának előnyeivel kapcsolatos további információkért tekintse meg az [Azure IoT SDK-k és a buktatók használatának előnyeit, hogy elkerülje, ha nem](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) a blogbejegyzés.

Bár a IoT Hub támogatja a AMQP, a AMQP-t a WebSockets, a HTTPS, a MQTT és a MQTT használatával a websocketeken keresztül az eszközökkel való kommunikációhoz, javasoljuk, hogy a MQTT-et használja, ha az eszköz támogatja

## <a name="development-prerequisites"></a>Fejlesztési előfeltételek

A fejlesztés megkezdése előtt alapos ismeretekkel kell rendelkeznie a IoT Hubről és az eszköz vagy a háttérbeli alkalmazás megvalósításához szükséges funkciókról. Íme egy nagyon rövidített lista a témakörökről, amelyeket érdemes megismernie:

* Győződjön meg arról, hogy megérti a IoT Hub által közzétett végpontokat és az egyes végpontokon támogatott protokollokat. További információ: IoT Hub- [végpontok](iot-hub-devguide-endpoints.md).

* Ha az eszköz alkalmazásaiban egy választott protokoll szerepel, javasoljuk, hogy használja a MQTT-t. A protokoll kiválasztása előtt mindenképpen tekintse meg az egyesek által előírt korlátozásokat. További információt a [kommunikációs protokoll kiválasztása](iot-hub-devguide-protocols.md)című témakörben talál.

* A IoT Hubval való hitelesítés megismeréséhez tekintse meg a [IoT hub hozzáférésének szabályozása](iot-hub-devguide-security.md)című témakört.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>Súgó különböző protokollokhoz

Segítség az Azure IoT SDK-val nem rendelkező következő protokollok használatához:

* Eszköz-vagy háttérbeli alkalmazások a **AMQP**, lásd: [AMQP-támogatás](iot-hub-amqp-support.md).

* A **MQTT** eszköz alkalmazásai: [MQTT-támogatás](iot-hub-mqtt-support.md). Ennek a témakörnek a többsége közvetlenül a MQTT protokoll használatát kezeli. Emellett a [IOT MQTT-minta tárházának](https://github.com/Azure-Samples/IoTMQTTSample)használatával kapcsolatos információkat is tartalmaz. Ez a tárház C mintákat tartalmaz, amelyek az Eclipse Mosquitto könyvtárat használják az üzenetek IoT Hub való küldéséhez.

* Eszköz-vagy háttérbeli alkalmazások **https**-en, tekintse meg az [Azure IoT hub REST API-kat](/rest/api/iothub/). Ügyeljen arra, hogy a [fejlesztés előfeltételei](#development-prerequisites)szerint ne használja az X. 509 HITELESÍTÉSSZOLGÁLTATÓ (CA) hitelesítést a HTTPS protokollal.

Az eszközök esetében erősen ajánlott a MQTT használata, ha az eszköz támogatja azt.

## <a name="next-steps"></a>Következő lépések

* [MQTT-támogatás](iot-hub-mqtt-support.md)