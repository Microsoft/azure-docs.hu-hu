---
title: Az Azure IoT Hub hibáinak elhárítása 401003 IoTHubUnauthorized
description: Ismerje meg, hogyan javíthatja a 401003-es hibát a IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 8fb891d5a47203c9905a7def9d04199d24327f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357249"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Ez a cikk a **401003 IoTHubUnauthorized** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

### <a name="symptom-1"></a>1. tünet

A naplókban megtekintheti az **401003 IoTHubUnauthorized**-vel leválasztott eszközök mintáját, majd a **404104 DeviceConnectionClosedRemotely**, majd a röviddel később történő csatlakozást.

### <a name="symptom-2"></a>2. tünet

A következő hibaüzenetek egyikével sikertelen IoT Hub kérelmek:

* Az engedélyezési fejléc hiányzik
* A (z) " \* " IotHub nem tartalmazza a megadott " \* " eszközt
* A (z) "" engedélyezési szabály \* nem engedélyezi a (z) "" elérését \*
* Az eszköz hitelesítése nem sikerült, a jogkivonat vagy tanúsítvány megújítása és újracsatlakoztatás
* Az ujjlenyomat nem felel meg a konfigurációnak: ujjlenyomat: SHA1Hash = \* , SHA2Hash = \* ; Konfiguráció: PrimaryThumbprint = \* , SecondaryThumbprint =\*

## <a name="cause"></a>Ok

### <a name="cause-1"></a>1\. ok

A MQTT esetében egyes SDK-k az IoT Hub-t használják, hogy kiállítsák a leválasztást, amikor az SAS-token lejár a frissítéshez. Tehát:

1. Az SAS-token lejár
1. IoT Hub észleli a lejáratot, és leválasztja az eszközt a **401003 IoTHubUnauthorized**
1. Az eszköz befejezi a leválasztást a **404104 DeviceConnectionClosedRemotely**
1. A IoT SDK új SAS-jogkivonatot hoz létre
1. Az eszköz újrakapcsolódik IoT Hub sikeresen

### <a name="cause-2"></a>2\. ok

A IoT Hub nem tudta hitelesíteni az Auth fejlécét, szabályát vagy kulcsát. Ezt az okozhatja, hogy a jelenségek egyike a tüneteket idézi elő.

## <a name="solution"></a>Megoldás

### <a name="solution-1"></a>1\. megoldás

Nincs szükség beavatkozásra, ha az IoT SDK-t használja az eszköz kapcsolati karakterláncával való kapcsolathoz. A IoT SDK újragenerálta az új tokent az SAS-jogkivonat lejáratának újrakapcsolódásához.

Az alapértelmezett jogkivonat élettartama 60 perc az SDK-k között. egyes SDK-k esetében azonban a jogkivonat élettartama és a jogkivonat megújítási küszöbértéke konfigurálható. Emellett az eszköz leválasztása és a jogkivonat-megújítások újracsatlakozása során létrehozott hibák eltérnek az egyes SDK-kal. Ha többet szeretne megtudni arról, hogy miként állapítható meg, hogy az eszköz milyen SDK-t használ a naplókban, tekintse meg az [MQTT-eszköz leválasztásának viselkedését az Azure IoT SDK](iot-hub-troubleshoot-connectivity.md#mqtt-device-disconnect-behavior-with-azure-iot-sdks)-k használatával

Ha az eszközök fejlesztői számára a hibák mennyisége aggodalomra ad okot, váltson át a C SDK-ra, amely megújítja az SAS-jogkivonatot a lejárat előtt. A AMQP esetében az SAS-token leválasztás nélkül is frissíthető.

### <a name="solution-2"></a>2\. megoldás

Általánosságban elmondható, hogy a hibaüzenet kijavításának módját a következő üzenet ismerteti:. Ha valamilyen okból nem fér hozzá a hibaüzenet részleteihez, ügyeljen a következőre:

- Az SAS vagy más használt biztonsági jogkivonat nem járt le.
- Az X. 509 Tanúsítványos hitelesítéshez az eszköz tanúsítványa vagy az eszközhöz rendelt HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány nem járt le. Ha meg szeretné tudni, hogyan regisztrálhat X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat IoT Hub használatával, tekintse meg az [x. 509-Biztonság beállítása az Azure IoT hub-ban](iot-hub-security-x509-get-started.md)című témakört.
- Az X. 509 tanúsítvány ujjlenyomatának hitelesítéséhez az eszköz tanúsítványának ujjlenyomata IoT Hub van regisztrálva.
- Az engedélyezési hitelesítő adat jól formázott a használt protokollhoz. További információ: [a IoT hub hozzáférésének szabályozása](iot-hub-devguide-security.md).
- A használt engedélyezési szabály jogosult a kért műveletre.

## <a name="next-steps"></a>Következő lépések

- Ahhoz, hogy könnyebben IoT Hub a hitelesítés, javasoljuk, hogy az [Azure IoT SDK](iot-hub-devguide-sdks.md)-kat használja.
- A IoT Hub használatával történő hitelesítés részletes ismertetését lásd: [a IoT hub hozzáférésének szabályozása](iot-hub-devguide-security.md).
