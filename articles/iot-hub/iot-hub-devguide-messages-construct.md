---
title: Az Azure IoT Hub üzenet formátumának ismertetése | Microsoft Docs
description: Fejlesztői útmutató – ismerteti IoT Hub üzenetek formátumát és várt tartalmát.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6d6b7122963b51619f26b8d02a8be4ad39261afb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92147687"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub-üzenetek létrehozása és olvasása

A protokollok közötti zökkenőmentes együttműködési képesség támogatása érdekében a IoT Hub az összes eszközre irányuló protokoll közös üzenet-formátumát határozza meg. Ez az üzenet formátuma az [eszközről a felhőbe](iot-hub-devguide-messages-d2c.md) irányuló Útválasztás és a [felhőből az eszközre](iot-hub-devguide-messages-c2d.md) irányuló üzenetek esetében is használatos. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub az eszközről a felhőbe irányuló üzenetküldést adatfolyam-üzenetküldési minta használatával valósítja meg. A IoT Hub eszközről a felhőbe irányuló üzenetek többek között a [Service Bus](../service-bus-messaging/index.yml) *üzenetekben* [Event Hubs](../event-hubs/index.yml) *események* , így nagy mennyiségű esemény halad át a szolgáltatáson keresztül, amely több olvasó által is olvasható.

Egy IoT Hub üzenet a következőkből áll:

* A *Rendszertulajdonságok* előre meghatározott készlete az alábbi listában.

* Az *alkalmazás tulajdonságainak* halmaza. Az alkalmazás által definiálható és elérhető karakterlánc-tulajdonságok szótára anélkül, hogy a deszerializálni kellene az üzenet törzsét. IoT Hub soha nem módosítja ezeket a tulajdonságokat.

* Egy átlátszatlan bináris törzs.

A tulajdonságnév és az értékek csak ASCII alfanumerikus karaktereket tartalmazhatnak, és ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` Ha az eszközről a felhőbe irányuló üzeneteket a HTTPS protokoll használatával küldi el, vagy a felhőből az eszközre irányuló üzeneteket küld.

Az eszközről a felhőbe irányuló üzenetküldés IoT Hub a következő jellemzőkkel rendelkezik:

* Az eszközről a felhőbe irányuló üzenetek tartósak, és az IoT hub alapértelmezett **üzenetek/események** végpontján belül akár hét napig is megmaradnak.

* Az eszközről a felhőbe irányuló üzenetek legfeljebb 256 KB méretűek lehetnek, és kötegekben csoportosíthatók a Küldés optimalizálása érdekében. A kötegek legfeljebb 256 KB méretűek lehetnek.

* A IoT Hub nem teszi lehetővé az tetszőleges particionálást. Az eszközről a felhőbe irányuló üzenetek particionálása a származó **deviceId** alapján történik.

* Ahogy azt a [IoT hub hozzáférésének vezérlése](iot-hub-devguide-security.md)című részben leírtak szerint, IoT hub engedélyezi az eszközönkénti hitelesítést és a hozzáférés-vezérlést.

* Az üzenetek az alkalmazás tulajdonságaiba bekerülő adatokkal is lebélyegzettek. További információ: [üzenet-gazdagítás](iot-hub-message-enrichments-overview.md).

További információ a különböző protokollok használatával elküldött üzenetek kódolásáról és dekódolásáról: [Azure IoT SDK](iot-hub-devguide-sdks.md)-k.

## <a name="system-properties-of-d2c-iot-hub-messages"></a>**D2C** IoT hub üzenetek rendszertulajdonságai

| Tulajdonság | Leírás  |A felhasználó beállítható?|Kulcsszó a következőhöz: </br>útválasztási lekérdezés|
| --- | --- | --- | --- |
| üzenet-azonosító |A kérelem-válasz mintákhoz használt üzenet felhasználó által beállítható azonosítója. Format: kis-és nagybetűket megkülönböztető karakterlánc (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karakterek + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` .  | Yes | messageId |
| iothub – enqueuedtime |Az [eszközről a felhőbe](iot-hub-devguide-d2c-guidance.md) irányuló üzenet IoT hub általi fogadásának dátuma és időpontja. | No | enqueuedTime |
| felhasználói azonosító |Az üzenetek eredetének meghatározására szolgáló azonosító. Ha IoT Hub hozza létre az üzeneteket, az értéke a következő lesz: `{iot hub name}` . | Yes | userId |
| iothub--összekötő-eszköz-azonosító |Az eszközről a felhőbe irányuló üzenetekben IoT Hub által beállított azonosító. Tartalmazza az üzenetet küldő eszköz **deviceId** -számát. | No | connectionDeviceId |
| iothub – összekapcsolási modul azonosítója |Az eszközről a felhőbe irányuló üzenetekben IoT Hub által beállított azonosító. Az üzenetet küldő eszköz **moduleId** tartalmazza. | No | connectionModuleId |
| iothub – kapcsolatok – hitelesítés – generálási azonosító |Az eszközről a felhőbe irányuló üzenetekben IoT Hub által beállított azonosító. Az üzenetet küldő eszköz **connectionDeviceGenerationId** (az [eszköz identitásának tulajdonságai](iot-hub-devguide-identity-registry.md#device-identity-properties)alapján) tartalmazza. | No |connectionDeviceGenerationId |
| iothub – kapcsolatok – hitelesítés – módszer |Az eszközről a felhőbe irányuló üzenetekben IoT Hub által beállított hitelesítési módszer. Ez a tulajdonság az üzenetet küldő eszköz hitelesítéséhez használt hitelesítési módszerről tartalmaz információkat.| No | connectionAuthMethod |
| DT – DataSchema | Ezt az értéket az IoT hub állítja be az eszközről a felhőbe irányuló üzenetekben. Ez tartalmazza az eszköz-kapcsolatban beállított eszköz-modell AZONOSÍTÓját. | No | N/A |
| DT – tárgy | Annak az összetevőnek a neve, amely az eszközről a felhőbe irányuló üzeneteket küld. | Yes | N/A |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>**C2D** IoT hub üzenetek rendszertulajdonságai

| Tulajdonság | Leírás  |A felhasználó beállítható?|
| --- | --- | --- |
| üzenet-azonosító |A kérelem-válasz mintákhoz használt üzenet felhasználó által beállítható azonosítója. Format: kis-és nagybetűket megkülönböztető karakterlánc (legfeljebb 128 karakter hosszú) ASCII 7 bites alfanumerikus karakterek + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` .  |Yes|
| sorozatszám |Az egyes felhőből az eszközre irányuló üzenetek IoT Hub által hozzárendelt szám (egyedi eszközönként). |No|
| felhasználóként a(z) |A [felhőből az eszközre irányuló](iot-hub-devguide-c2d-guidance.md) üzenetekben megadott célhely. |No|
| abszolút – lejárat – idő |Az üzenetek lejáratának dátuma és időpontja. |No|   |
| korrelációs azonosító |Egy válaszüzenet karakterlánc-tulajdonsága, amely általában tartalmazza a kérelem MessageId a kérelem-válasz mintákban. |Yes|
| felhasználói azonosító |Az üzenetek eredetének meghatározására szolgáló azonosító. Ha IoT Hub hozza létre az üzeneteket, az értéke a következő lesz: `{iot hub name}` . |Yes|
| iothub-ACK |Visszajelzési üzenet generátora. Ez a tulajdonság a felhőből az eszközre irányuló üzenetekben használatos, hogy az üzenetnek az eszköz általi felhasználásának eredményeképpen a visszajelzési üzenetek létrehozásához IoT Hub kérjenek. Lehetséges értékek: **nincs (alapértelmezett): nem** jön létre visszajelzési üzenet **, pozitív**: visszajelzési üzenet küldése, ha az üzenet elkészült, **negatív**: visszajelzési üzenet küldése, ha az üzenet lejárt (vagy elérte a maximális kézbesítési értéket) az eszköz befejezése nélkül, vagy **teljes**: pozitív és negatív. |Yes|

### <a name="system-property-names"></a>Rendszertulajdonságok nevei

A Rendszertulajdonságok nevei attól függően változnak, hogy az üzenetek milyen végponton vannak átirányítva. Ezekről a nevekről az alábbi táblázatban talál további információt.

|Rendszertulajdonság neve|Event Hubs|Azure Storage|Service Bus|Event Grid|
|--------------------|----------|-------------|-----------|----------|
|Üzenet azonosítója|üzenet-azonosító|messageId|MessageId|üzenet-azonosító|
|A felhasználó azonosítója|felhasználói azonosító|userId|UserId (Felhasználóazonosító)|felhasználói azonosító|
|Összekötő eszköz azonosítója|iothub--összekötő-eszköz-azonosító| connectionDeviceId|iothub--összekötő-eszköz-azonosító|iothub--összekötő-eszköz-azonosító|
|Kapcsolatazonosító|iothub – összekapcsolási modul azonosítója|connectionModuleId|iothub – összekapcsolási modul azonosítója|iothub – összekapcsolási modul azonosítója|
|A kapcsolatok hitelesítésének generálása azonosító|iothub – kapcsolatok – hitelesítés – generálási azonosító|connectionDeviceGenerationId| iothub – kapcsolatok – hitelesítés – generálási azonosító|iothub – kapcsolatok – hitelesítés – generálási azonosító|
|Kapcsolatok hitelesítési módszere|iothub – kapcsolatok – hitelesítés – módszer|connectionAuthMethod|iothub – kapcsolatok – hitelesítés – módszer|iothub – kapcsolatok – hitelesítés – módszer|
|contentType|Content-Type|contentType|ContentType|iothub – tartalomtípus|
|contentEncoding|Content-Encoding|contentEncoding|ContentEncoding|iothub – tartalom kódolása|
|iothub – enqueuedtime|iothub – enqueuedtime|enqueuedTime| N/A |iothub – enqueuedtime|
|CorrelationId|korrelációs azonosító|correlationId|CorrelationId|korrelációs azonosító|
|DT – DataSchema|DT – DataSchema|DT – DataSchema|DT – DataSchema|DT – DataSchema|
|DT – tárgy|DT – tárgy|DT – tárgy|DT – tárgy|DT – tárgy|

## <a name="message-size"></a>Üzenet mérete

A IoT Hub az üzenetek méretét a protokolltól független módon méri, és csak a tényleges adattartalmat veszi figyelembe. A bájtban megadott méret kiszámítása a következő értékek összegeként történik:

* A törzs mérete bájtban megadva.
* Az üzenetrendszer-tulajdonságok összes értékének bájtban kifejezett mérete.
* A felhasználói tulajdonságok neveinek és értékeinek mérete bájtban megadva.

A tulajdonságok nevei és értékei csak ASCII-karakterekre korlátozódnak, így a karakterláncok hossza bájtban kifejezve.

## <a name="anti-spoofing-properties"></a>Hamisítás elleni tulajdonságok

Az eszközről a felhőbe irányuló üzenetek hamisításának elkerülése érdekében IoT Hub Stamp összes üzenetet a következő tulajdonságokkal:

* **iothub--összekötő-eszköz-azonosító**
* **iothub – kapcsolatok – hitelesítés – generálási azonosító**
* **iothub – kapcsolatok – hitelesítés – módszer**

Az első kettő tartalmazza a kezdeményező eszköz **deviceId** -és **GenerationId** , az [eszköz azonosító tulajdonságai](iot-hub-devguide-identity-registry.md#device-identity-properties)alapján.

A **iothub-kapcsolat-Auth-Method** tulajdonság JSON szerializált objektumot tartalmaz, a következő tulajdonságokkal:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Következő lépések

* További információ a IoT Hub üzenet méretének korlátairól: [IoT hub kvóták és szabályozás](iot-hub-devguide-quotas-throttling.md).

* Ha meg szeretné tudni, hogyan hozhat létre és olvashat IoT Hub üzeneteket különböző programozási nyelveken, tekintse [meg a gyors](quickstart-send-telemetry-node.md)útmutatókat.