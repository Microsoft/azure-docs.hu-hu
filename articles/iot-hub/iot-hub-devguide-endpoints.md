---
title: A Azure IoT Hub végpontok | Microsoft Docs
description: Fejlesztői útmutató – referenciainformációk az IoT Hub és szolgáltatás felé néző végpontok használatával kapcsolatban.
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
ms.openlocfilehash: d2b9ea2e075ddcf20860ccb9ab1f2eff654993ad
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499376"
---
# <a name="reference---iot-hub-endpoints"></a>Referencia – IoT Hub végpontok

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT Hub nevek

A végpontokat tartalmazó IoT Hub gazdagépnevét a portál áttekintési oldalán **találja.** Alapértelmezés szerint az IoT Hub DNS-neve a következő: `{your iot hub name}.azure-devices.net` .

## <a name="list-of-built-in-iot-hub-endpoints"></a>Beépített végpontok IoT Hub listája

Azure IoT Hub egy több-bérlős szolgáltatás, amely elérhetővé teszi a funkcióit a különböző a szereplők számára. Az alábbi ábrán az elérhetővé IoT Hub végpontok láthatóak.

![IoT Hub-végpontok](./media/iot-hub-devguide-endpoints/endpoints.png)

Az alábbi lista a végpontokat ismerteti:

* **Erőforrás-szolgáltató.** A IoT Hub erőforrás-szolgáltató elérhetővé teszi a [Azure Resource Manager](../azure-resource-manager/management/overview.md) felületet. Ez a felület lehetővé teszi az Azure-előfizetések tulajdonosai számára IoT Hubok létrehozására és törlésére, valamint az IoT Hub tulajdonságainak frissítését. IoT Hub tulajdonságok szabályozzák a [központszintű](iot-hub-devguide-security.md#access-control-and-permissions)biztonsági szabályzatokat, nem pedig az eszközszintű hozzáférés-vezérlést, valamint a felhőből az eszközre és az eszközről a felhőbe való üzenetküldés funkcionális lehetőségeit. A IoT Hub erőforrás-szolgáltató lehetővé teszi az [eszköz identitásának exportálását is.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

* **Eszközidentitás-kezelés.** Minden IoT Hub https REST-végpontok készletét teszi elérhetővé az eszköz identitásának kezeléséhez (létrehozás, lekérés, frissítés és törlés). [Az eszköz identitások](iot-hub-devguide-identity-registry.md) eszközhitelesítéshez és hozzáférés-vezérléshez használhatók.

* **Ikereszköz-kezelés.** Minden IoT Hub elérhetővé teszi a szolgáltatás felé néző HTTPS REST-végpontok készletét az ikereszközök [lekérdezéséhez](iot-hub-devguide-device-twins.md) és frissítéséhez (címkék és tulajdonságok frissítése). 

* **Feladatkezelés.** Minden IoT Hub elérhetővé teszi a szolgáltatások felé néző HTTPS REST-végpontok készletét a feladatok lekérdezéséhez és [kezeléséhez.](iot-hub-devguide-jobs.md)

* **Eszközvégpontokat.** Az identitásjegyzékben minden eszközhöz elérhetővé IoT Hub egy végpontkészletet. A feljegyzett végpontok az [MQTT v3.1.1,](https://mqtt.org/)HTTPS 1.1 és [AMQP 1.0](https://www.amqp.org/) protokollok használatával vannak elérhetővé téve. Az AMQP és az MQTT [WebSocketen keresztül](https://tools.ietf.org/html/rfc6455) is elérhető a 443-as porton.

  * *Eszközről a felhőbe küldött üzenetek.* Egy eszköz ezt a végpontot használja az [eszközről a felhőbe küldött üzenetek küldésére.](iot-hub-devguide-messages-d2c.md)

  * *Felhőből az eszközre küldött üzenetek fogadása.* Az eszközök ezt a végpontot használják a célzott, [felhőből az eszközre küldött üzenetek fogadására.](iot-hub-devguide-messages-c2d.md)

  * *Fájlfeltöltés kezdeményezése.* Egy eszköz ezt a végpontot használja egy Azure Storage SAS URI fogadására a IoT Hub [egy fájl feltöltéséhez.](iot-hub-devguide-file-upload.md)

  * *Az ikereszköz tulajdonságainak lekérése és frissítése.* Egy eszköz ezt a végpontot használja az [ikereszköz tulajdonságainak](iot-hub-devguide-device-twins.md)eléréséhez. A HTTPS nem támogatott.

  * *Közvetlen metódusra vonatkozó kérések fogadása.* Az eszközök ezzel a végponttal figyelik a közvetlen [metódusok](iot-hub-devguide-direct-methods.md)kérését. A HTTPS nem támogatott.

  [!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

* **Szolgáltatásvégpontokat.** Minden IoT Hub elérhetővé teszi a megoldás háttér-végpontjainak egy készletét az eszközökkel való kommunikációhoz. Egy kivétellel ezek a végpontok csak az [AMQP](https://www.amqp.org/) és az AMQP használatával vannak elérhetővé téve WebSockets protokollon keresztül. A közvetlen metódushívási végpont a HTTPS protokollon keresztül érhető el.
  
  * *Az eszközről a felhőbe küldött üzenetek fogadása.* Ez a végpont kompatibilis a [Azure Event Hubs.](https://azure.microsoft.com/documentation/services/event-hubs/) A háttérszolgáltatással olvashatja az [](iot-hub-devguide-messages-d2c.md) eszközök által küldött, az eszközről a felhőbe küldött üzeneteket. A beépített végpont mellett egyéni végpontokat is létrehozhat az IoT Hubon.
  
  * *Felhőből az eszközre küldött üzenetek küldése és kézbesítési nyugták fogadása.* Ezek a végpontok lehetővé teszik, hogy a megoldás háttérszolgáltatása megbízható, felhőből az eszközre küldött üzeneteket [küldjön,](iot-hub-devguide-messages-c2d.md)és megkapja a megfelelő kézbesítési vagy lejárati nyugtákat.
  
  * *Fájlértesítések fogadása.* Ezzel az üzenetkezelési végponttal értesítéseket kaphat arról, hogy az eszközök mikor tölthetnek fel sikeresen fájlokat. 
  
  * *Közvetlen metódushívás.* Ez a végpont lehetővé teszi, hogy [a](iot-hub-devguide-direct-methods.md) háttérszolgáltatás közvetlen metódust hív meg egy eszközön.
  
  * *Műveletek figyelési eseményeinek fogadása.* Ez a végpont lehetővé teszi a műveletek figyelési eseményeinek fogadását, ha az IoT Hub úgy van konfigurálva, hogy kibocsátsa azokat. További információ: IoT Hub [figyelése.](iot-hub-operations-monitoring.md)

Az [Azure IoT SDKs (Azure IoT SDK-k)](iot-hub-devguide-sdks.md) cikk a végpontok elérésének különböző módjait ismerteti.

Minden IoT Hub végpont használja a [TLS](https://tools.ietf.org/html/rfc5246) protokollt, és soha nem lesz végpont felfedve titkosítatlan/nem biztonságos csatornákon.

## <a name="custom-endpoints"></a>Egyéni végpontok

Az Azure-előfizetések meglévő Azure-szolgáltatásait összekapcsolhatja az IoT Hubbal, hogy az üzenetek útválasztásának végpontjaiként szolgálnak. Ezek a végpontok szolgáltatásvégpontként viselkednek, és üzenetútvonalak fogadóiként használatosak. Az eszközök nem írhatnak közvetlenül a további végpontokra. További információ az [üzenet-útválasztásról.](../iot-hub/iot-hub-devguide-messages-d2c.md)

IoT Hub a következő Azure-szolgáltatásokat támogatja további végpontokként:

* Azure Storage-tárolók
* Event Hubs
* Service Bus által kezelt üzenetsorok
* Service Bus-üzenettémák

A felvehető végpontok számának korlátozását lásd: [Kvóták és szabályozás.](iot-hub-devguide-quotas-throttling.md)

## <a name="endpoint-health"></a>Endpoint Health

[!INCLUDE [iot-hub-endpoint-health](../../includes/iot-hub-include-endpoint-health.md)]

## <a name="field-gateways"></a>Helyszíni átjárók

Egy IoT-megoldásban *egy* helyszíni átjáró van az eszközök és a IoT Hub végpontjai között. Ez általában az eszközök közelében található. Az eszközök közvetlenül kommunikálnak a helyszíni átjáróval az eszközök által támogatott protokoll használatával. A helyszíni átjáró egy IoT Hub végponthoz csatlakozik egy, a IoT Hub. A helyszíni átjáró lehet egy dedikált hardvereszköz vagy egy egyéni átjárószoftvert futtató alacsony áramot futtató számítógép.

A helyszíni [Azure IoT Edge](../iot-edge/index.yml) használhatja. IoT Edge olyan funkciókat kínál, mint például a több eszközről ugyanazon a kapcsolaton keresztül IoT Hub kommunikáció.

## <a name="next-steps"></a>Következő lépések

A fejlesztői útmutató további referencia-IoT Hub a következők:

* [IoT Hub ikereszköz, feladat és üzenet-útválasztás lekérdezési nyelvének használata](iot-hub-devguide-query-language.md)
* [Kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub MQTT támogatása](iot-hub-mqtt-support.md)
* [Az IoT Hub IP-címének a kezelése](iot-hub-understand-ip-address.md)
