---
title: Az Azure IoT Device kiépítési szolgáltatás MQTT-támogatásának ismertetése | Microsoft Docs
description: Fejlesztői útmutató – az Azure IoT Device kiépítési szolgáltatás (DPS) eszköz felé irányuló végponthoz csatlakozó eszközök támogatása a MQTT protokoll használatával.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0a7ec2f4f8fdf631a6bc5096296275291ec41751
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967125"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Kommunikáció a DPS használatával a MQTT protokollal

A DPS lehetővé teszi, hogy az eszközök a következő paranccsal kommunikáljanak a DPS-eszköz végpontjának használatával:

* [MQTT v 3.1.1](https://mqtt.org/) a 8883-es porton
* [MQTT v 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) a 443-es porton keresztüli WebSocket-en keresztül.

A DPS nem a teljes funkcionalitású MQTT-közvetítő, és nem támogatja az MQTT v 3.1.1 szabványban megadott összes viselkedést. Ez a cikk azt ismerteti, hogyan használhatók az eszközök a támogatott MQTT-viselkedésekkel a DPS-vel való kommunikációhoz.

A DPS-vel való kommunikációt a TLS/SSL protokollal kell biztosítani. Ezért a DPS nem támogatja a nem biztonságos kapcsolatokat a 1883-as porton keresztül.

 > [!NOTE] 
 > A DPS jelenleg nem támogatja a TPM- [igazolási mechanizmust](./concepts-service.md#attestation-mechanism) használó eszközöket az MQTT protokollon keresztül.

## <a name="connecting-to-dps"></a>Csatlakozás a DPS-hez

Az eszközök a MQTT protokoll használatával csatlakozhatnak a DPS-hez az alábbi lehetőségek bármelyikével.

* Az [Azure IoT üzembe](../iot-hub/iot-hub-devguide-sdks.md#microsoft-azure-provisioning-sdks)helyezési SDK-k könyvtárai.
* A MQTT protokoll közvetlenül.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>A MQTT protokoll használata közvetlenül (eszközként)

Ha egy eszköz nem tudja használni az eszköz SDK-kat, akkor továbbra is csatlakozhat a nyilvános eszközök végpontjaihoz a 8883-es porton az MQTT protokoll használatával. A **csatlakoztatási** csomagban az eszköznek a következő értékeket kell használnia:

* A **ClientId** mezőben használja a **regisztrációban**.

* A **Felhasználónév** mezőben használja a nevet `{idScope}/registrations/{registration_id}/api-version=2019-03-31` , ahol a a `{idScope}` DPS [idScope](./concepts-service.md#id-scope) .

* A **Password (jelszó** ) mezőben használjon sas-tokent. Az SAS-token formátuma megegyezik a HTTPS és a AMQP protokollok esetében is:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` A resourceURI formátumúnak kell lennie `{idScope}/registrations/{registration_id}` . A szabályzat nevének a (z) értéknek kell lennie `registration` .

  > [!NOTE]
  > Ha X. 509 tanúsítványalapú hitelesítést használ, az SAS-jogkivonat jelszavai nem szükségesek.

  Az SAS-tokenek létrehozásával kapcsolatos további információkért tekintse [meg a DPS hozzáférés vezérlése](how-to-control-access.md#security-tokens)a biztonsági jogkivonatok című szakaszt.

A következő lista felsorolja a DPS implementáció-specifikus viselkedéseit:

 * A DPS nem támogatja a **CleanSession** jelző funkciójának **0-ra** való beállítását.

 * Ha egy eszköz egy, a **QoS 2** szolgáltatással foglalkozó témakörre van előfizetve, a DPS maximális QoS-szintet biztosít a **SUBACK** -csomagban. Ezután a DPS a QoS 1 használatával továbbítja az üzeneteket az eszköznek.

## <a name="tlsssl-configuration"></a>TLS/SSL-konfiguráció

Ahhoz, hogy az ügyfél közvetlenül használhassa a MQTT protokollt, a TLS 1,2-en keresztül *kell* csatlakoznia. Csatlakozási hibák esetén a lépés kihagyása sikertelen.


## <a name="registering-a-device"></a>Eszköz regisztrálása

Ha a DPS-n keresztül szeretne regisztrálni egy eszközt, az eszköznek `$dps/registrations/res/#` **témakör-szűrőként** kell előfizetnie. A témakör szűrője többszintű helyettesítő karaktert használ, amely `#` lehetővé teszi, hogy az eszköz további tulajdonságokat kapjon a témakör nevében. A DPS nem engedélyezi a `#` vagy `?` helyettesítő karakterek használatát az altémakörök szűréséhez. Mivel a DPS nem általános célú pub-sub üzenetküldési közvetítő, csak a dokumentált témakörök nevét és a témakör szűrőit támogatja.

Az eszköznek közzé kell tennie egy regisztrációs üzenetet a DPS-ben a `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` **témakör neveként**. A hasznos adatoknak JSON formátumban kell tartalmazniuk az [eszköz regisztrációs](/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) objektumát.
Sikeres forgatókönyv esetén az eszköz választ kap a `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` témakör nevére, ahol az x az újrapróbálkozási érték másodpercben. A válasz hasznos tartalma JSON formátumban fogja tartalmazni a [RegistrationOperationStatus](/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) objektumot.

## <a name="polling-for-registration-operation-status"></a>A regisztrációs művelet állapotának lekérdezése

Az eszköznek rendszeresen le kell kérdezni a szolgáltatást, hogy megkapja az eszköz regisztrációs műveletének eredményét. Feltételezve, hogy az eszköz már előfizetett a `$dps/registrations/res/#` témakörre a fentiekben leírtak szerint, közzétehet egy Get operationstatus-üzenetet a `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` témakör nevében. Az üzenetben szereplő műveleti AZONOSÍTÓnak az előző lépésben RegistrationOperationStatus válaszüzenetben kapott értéknek kell lennie. A sikeres esetben a szolgáltatás válaszolni fog a `$dps/registrations/res/200/?$rid={request_id}` témakörre. A válasz hasznos tartalma a RegistrationOperationStatus objektumot fogja tartalmazni. Az eszköznek továbbra is le kell kérdezni a szolgáltatást, ha a válasz kódja 202 az újrapróbálkozási időszakot megegyező késés után. Az eszköz regisztrációs művelete sikeres, ha a szolgáltatás 200 állapotkódot ad vissza.

## <a name="connecting-over-websocket"></a>Csatlakozás WebSocket-kapcsolaton keresztül
WebSocket-kapcsolaton keresztüli kapcsolódáskor adja meg az alprotokollot `mqtt` . Kövesse az [RFC 6455](https://tools.ietf.org/html/rfc6455)-et.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az MQTT protokollról, tekintse meg a [MQTT dokumentációját](https://mqtt.org/).

A DPS képességeinek további megismeréséhez lásd:

* [Tudnivalók a IoT DPS-ről](about-iot-dps.md)