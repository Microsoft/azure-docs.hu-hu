---
title: Az Azure IoT Hub-végpontok ismertetése | Microsoft Docs
description: Fejlesztői útmutató – IoT Hub eszközre irányuló és a szolgáltatáshoz kapcsolódó végpontokkal kapcsolatos információk.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: a58e141c6232db08b125b265e3d4ad74c784ba24
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92152185"
---
# <a name="reference---iot-hub-endpoints"></a>Hivatkozás – IoT Hub végpontok

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub nevek

Megkeresheti a végpontokat üzemeltető IoT hub állomásnevét a központ  **Áttekintés** oldalán található portálon. Alapértelmezés szerint az IoT hub DNS-neve a következőképpen néz ki: `{your iot hub name}.azure-devices.net` .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Beépített IoT Hub végpontok listája

Az Azure IoT Hub egy több-bérlős szolgáltatás, amely számos különböző résztvevő számára teszi elérhetővé a funkcionalitását. Az alábbi ábrán a IoT Hub elérhető különböző végpontok láthatók.

![IoT Hub-végpontok](./media/iot-hub-devguide-endpoints/endpoints.png)

A következő lista a végpontokat ismerteti:

* **Erőforrás-szolgáltató**. A IoT Hub erőforrás-szolgáltató egy [Azure Resource Manager](../azure-resource-manager/management/overview.md) felületet tesz elérhetővé. Ez az interfész lehetővé teszi, hogy az Azure-előfizetések tulajdonosai IoT-hubokat hozzanak létre és töröljenek, valamint az IoT hub tulajdonságait IoT Hub tulajdonságok szabályozzák a [hub szintű biztonsági házirendeket](iot-hub-devguide-security.md#access-control-and-permissions), az eszköz szintű hozzáférés-vezérlést, valamint a felhőből az eszközre és az eszközről a felhőbe irányuló üzenetkezelés funkcionális lehetőségeit. A IoT Hub erőforrás-szolgáltató lehetővé teszi az [eszközök identitásának exportálását](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)is.

* **Eszköz-identitás kezelése**. Mindegyik IoT hub HTTPS REST-végpontokat tesz elérhetővé az eszközök identitásának kezeléséhez (létrehozás, lekérés, frissítés és törlés). Az eszköz- [identitások](iot-hub-devguide-identity-registry.md) az eszközök hitelesítéséhez és a hozzáférés-vezérléshez használatosak.

* **Eszközök kettős kezelése**. Mindegyik IoT hub szolgáltatáshoz kapcsolódó HTTPS REST-végpontot tesz elérhetővé az [eszközökhöz tartozó ikrek](iot-hub-devguide-device-twins.md) lekérdezéséhez és frissítéséhez (címkék és tulajdonságok frissítése). 

* **Feladatok kezelése**. Mindegyik IoT hub egy szolgáltatáshoz kapcsolódó HTTPS REST-végpontot tesz elérhetővé a [feladatok](iot-hub-devguide-jobs.md)lekérdezéséhez és kezeléséhez.

* **Eszköz-végpontok**. Az Identity Registry minden eszközén IoT Hub a végpontok készletét teszi elérhetővé. Ha ez nem történik meg, a végpontok a [MQTT v 3.1.1](https://mqtt.org/), a https 1,1 és a [AMQP 1,0](https://www.amqp.org/) protokoll használatával tehetők elérhetővé. A AMQP és a MQTT a 443-es porton keresztül is elérhetők [WebSockets](https://tools.ietf.org/html/rfc6455) -en keresztül.

  * *Eszközről a felhőbe irányuló üzenetek küldése*. Az eszköz ezt a végpontot használja az [eszközről a felhőbe irányuló üzenetek küldéséhez](iot-hub-devguide-messages-d2c.md).

  * *A felhőből az eszközre irányuló üzenetek fogadása*. Az eszköz ezt a végpontot használja a [felhőből az eszközre](iot-hub-devguide-messages-c2d.md)irányuló üzenetek fogadására.

  * A fájlfeltöltés *kezdeményezése*. Egy eszköz ezt a végpontot használja az Azure Storage SAS URI-azonosítójának IoT Hub [egy fájl feltöltéséhez](iot-hub-devguide-file-upload.md).

  * *Eszköz Twin tulajdonságainak beolvasása és frissítése*. Az eszköz ezt a végpontot használja az [eszköz Twin](iot-hub-devguide-device-twins.md)tulajdonságainak eléréséhez. A HTTPS nem támogatott.

  * *Közvetlen metódusokra vonatkozó kérelmek fogadása*. Egy eszköz ezt a végpontot használja a [közvetlen metódus](iot-hub-devguide-direct-methods.md)kérelmének figyelésére. A HTTPS nem támogatott.

  [!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

* **Szolgáltatási végpontok**. Minden IoT hub a megoldás hátterében lévő végpontokat tesz elérhetővé az eszközökkel való kommunikációhoz. Egyetlen kivétel esetén ezek a végpontok csak a [AMQP](https://www.amqp.org/) és a AMQP használatával lesznek elérhetők a WebSockets protokollokon keresztül. A közvetlen metódus Meghívási végpontja elérhető a HTTPS protokollon keresztül.
  
  * *Eszközről a felhőbe irányuló üzenetek fogadása*. Ez a végpont kompatibilis az [Azure Event Hubsával](https://azure.microsoft.com/documentation/services/event-hubs/). A háttér-szolgáltatás segítségével elolvashatja az eszközökről a felhőbe küldött, az [eszközről a felhőbe](iot-hub-devguide-messages-d2c.md) irányuló üzeneteket. A beépített végponton kívül egyéni végpontokat is létrehozhat az IoT hub-ban.
  
  * *A felhőből az eszközre irányuló üzenetek küldése és a kézbesítési visszaigazolások fogadása*. Ezek a végpontok lehetővé teszik a megoldás hátterében a megbízható [felhőből az eszközre](iot-hub-devguide-messages-c2d.md)irányuló üzenetek küldését, valamint a megfelelő kézbesítési vagy lejárati igazolások fogadását.
  
  * *Fogadott fájlok értesítései*. Ez az üzenetkezelési végpont lehetővé teszi, hogy értesítést kapjon, amikor az eszközök sikeresen feltölthetnek egy fájlt. 
  
  * *Közvetlen metódus meghívása*. Ez a végpont lehetővé teszi, hogy egy háttér-szolgáltatás [közvetlen metódust](iot-hub-devguide-direct-methods.md) indítson az eszközön.
  
  * *Fogadási műveletek figyelési eseményei*. Ez a végpont lehetővé teszi az Operations monitoring-események fogadását, ha az IoT hub úgy lett konfigurálva, hogy kiállítsa őket. További információ: [IoT hub Operations monitoring](iot-hub-operations-monitoring.md).

Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k cikke ismerteti a végpontok elérésének különféle módjait.

Az összes IoT Hub végpont a [TLS](https://tools.ietf.org/html/rfc5246) protokollt használja, és a nem titkosított/nem biztonságos csatornákon egyetlen végpont sincs kitéve.

## <a name="custom-endpoints"></a>Egyéni végpontok

Az előfizetéshez tartozó meglévő Azure-szolgáltatásokat összekapcsolhatja az IoT hubhoz, hogy az üzenet-útválasztás végpontként működjön. Ezek a végpontok szolgáltatás-végpontként működnek, és az üzenetek elérési útjaiként használatosak. Az eszközök nem tudnak közvetlenül a további végpontokra írni. További információ az [üzenetek útválasztásáról](../iot-hub/iot-hub-devguide-messages-d2c.md).

IoT Hub jelenleg a következő Azure-szolgáltatásokat támogatja további végpontként:

* Azure Storage-tárolók
* Event Hubs
* Service Bus által kezelt üzenetsorok
* Service Bus-üzenettémák

A felvehető végpontok számának korlátozásait lásd: [kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md).

## <a name="endpoint-health"></a>Végpont állapota

[!INCLUDE [iot-hub-endpoint-health](../../includes/iot-hub-include-endpoint-health.md)]

## <a name="field-gateways"></a>Mező-átjárók

Egy IoT-megoldásban a *Field Gateway* az eszközök és a IoT hub végpontok között helyezkedik el. Általában az eszközök közelében található. Az eszközök közvetlenül kommunikálnak a helyszíni átjáróval az eszközök által támogatott protokoll használatával. A Field Gateway egy IoT Hub által támogatott protokoll használatával csatlakozik egy IoT Hub végponthoz. A helyszíni átjáró lehet egy dedikált hardvereszköz vagy egy kis teljesítményű számítógép, amely egyéni átjáró szoftvert futtat.

A [Azure IoT Edge](../iot-edge/index.yml) használható a Field Gateway megvalósításához. IoT Edge olyan funkciókat kínál, mint például a több eszközről származó többszörös kommunikáció ugyanarra a IoT Hub kapcsolatra.

## <a name="next-steps"></a>Következő lépések

A IoT Hub Fejlesztői útmutatóban szereplő további témakörök a következők:

* [Az ikrek, a feladatok és az üzenet-útválasztás IoT Hub lekérdezési nyelve](iot-hub-devguide-query-language.md)
* [Kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md)
* [Az IoT hub IP-címének megismerése](iot-hub-understand-ip-address.md)