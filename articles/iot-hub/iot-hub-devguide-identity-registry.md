---
title: Az Azure IoT Hub Identity Registry ismertetése | Microsoft Docs
description: Fejlesztői útmutató – a IoT Hub Identity Registry leírása és az eszközök kezelésének módja. Az eszköz-identitások tömeges importálásával és exportálásával kapcsolatos információkat tartalmaz.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 2d9b0d97fa1823314f5109a1c7fc79054806c148
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146926"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Az IoT hub Identity Registry ismertetése

Minden IoT hub rendelkezik egy azonosítóval, amely az IoT hubhoz való kapcsolódáshoz engedélyezett eszközökről és modulokról tartalmaz információkat. Ahhoz, hogy egy eszköz vagy modul csatlakozni tudjanak egy IoT hubhoz, az IoT hub azonosító-beállításjegyzékében szerepelnie kell egy bejegyzésnek az adott eszközhöz vagy modulhoz. Az eszköznek vagy modulnak a IoT hub-ban is hitelesítenie kell magát az Identity registryben tárolt hitelesítő adatok alapján.

Az Identity registryben tárolt eszköz vagy modul azonosítója megkülönbözteti a kis-és nagybetűket.

Magas szinten az Identity Registry az eszköz-vagy modul-identitás erőforrásainak REST-kompatibilis gyűjteménye. Ha hozzáad egy bejegyzést az identitás-beállításjegyzékben, a IoT Hub eszközönkénti erőforrások, például a felhőből az eszközre irányuló üzeneteket tartalmazó várólista-készletet hoz létre.

Ha a következőkre van szüksége, használja az Identity registryt:

* Az IoT hub-hoz csatlakozó eszközök vagy modulok kiépítése.
* Eszközön vagy modulon keresztüli hozzáférés vezérlése a hub eszközéhez vagy modulhoz kapcsolódó végpontokhoz.

> [!NOTE]
> * Az Identity Registry nem tartalmaz alkalmazásspecifikus metaadatokat.
> * A modul identitása és a Twin modul nyilvános előzetes verzióban érhető el. Az alábbi funkciót a rendszer a modul identitásán fogja támogatni, ha általánosan elérhető.
>

## <a name="identity-registry-operations"></a>Identitásjegyzék műveletei

A IoT Hub Identity Registry a következő műveleteket teszi elérhetővé:

* Eszköz vagy modul identitásának létrehozása
* Eszköz vagy modul identitásának frissítése
* Eszköz vagy modul identitásának beolvasása azonosító alapján
* Eszköz vagy modul identitásának törlése
* Legfeljebb 1000 identitások listázása
* Eszköz identitásának exportálása az Azure Blob Storage-ba
* Eszköz identitások importálása az Azure Blob Storage-ból

Ezek a műveletek optimista párhuzamosságot használhatnak a [RFC7232](https://tools.ietf.org/html/rfc7232)-ben megadott módon.

> [!IMPORTANT]
> Az IoT hub identitás-beállításjegyzékének egyetlen módja az [exportálási](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) funkció használata.

Egy IoT Hub Identity Registry:

* Nem tartalmaz alkalmazás-metaadatokat.
* A (z) a **deviceId** vagy a **moduleId** kulcsként való használatával érhető el a szótárhoz.
* A nem támogatja az kifejező lekérdezéseket.

Az IoT-megoldás általában egy külön, alkalmazásspecifikus metaadatokat tartalmazó megoldás-specifikus tárolóval rendelkezik. Például egy intelligens építési megoldás megoldás-specifikus tárolója rögzíti azt a termet, amelyben a hőmérséklet-érzékelő üzembe lett helyezve.

> [!IMPORTANT]
> Csak az Identitáskezelés és a kiépítési műveletek esetében használja az Identity registryt. A nagy átviteli sebességű műveletek futási időben való végrehajtása nem függhet az Identity registryben végrehajtott műveletektől. Például egy eszköz kapcsolódási állapotának ellenőrzése a parancsok elküldése előtt nem támogatott minta. Ügyeljen arra, hogy ellenőrizze az azonosító-beállításjegyzék [szabályozási sebességét](iot-hub-devguide-quotas-throttling.md) , valamint az [eszköz szívverési](iot-hub-devguide-identity-registry.md#device-heartbeat) mintáját.

## <a name="disable-devices"></a>Eszközök letiltása

Az eszközöket letilthatja az identitás beállításjegyzékében lévő identitás **állapot** tulajdonságának frissítésével. Ezt a tulajdonságot általában két forgatókönyvben kell használni:

* Üzembe helyezési folyamat során. További információ: [Device kiépítés](iot-hub-devguide-identity-registry.md#device-provisioning).

* Ha bármilyen okból kifolyólag, azt gondolja, hogy az eszköz biztonsága sérül vagy jogosulatlanul lett kitéve.

Ez a funkció modulok esetén nem érhető el.

## <a name="import-and-export-device-identities"></a>Eszköz identitásának importálása és exportálása

Az [IoT hub erőforrás-szolgáltató végpontján](iot-hub-devguide-endpoints.md) aszinkron műveletekkel exportálhatja az eszköz-identitásokat egy IoT hub azonosító-beállításjegyzékének tömeges használatával. Az Exportálás olyan hosszan futó feladatok, amelyek ügyfél által megadott blob-tárolóval mentik az eszköz azonosító adatait az identitás-beállításjegyzékből.

Használjon aszinkron műveleteket a [IoT hub erőforrás-szolgáltató végponton](iot-hub-devguide-endpoints.md) az eszköz-identitások tömeges importálásához egy IoT hub azonosítójának beállításjegyzékében. Az Importálások olyan hosszan futó feladatok, amelyek az ügyfél által megadott blob-tárolóban tárolt adatokkal írják le az eszköz azonosító adatait az Identity registrybe.

További információ az importálási és exportálási API-król: [IoT hub erőforrás-szolgáltató REST API](/rest/api/iothub/iothubresource)-k. Ha többet szeretne megtudni az importálási és exportálási feladatok futtatásáról, tekintse meg [a IoT hub-eszköz identitások tömeges kezelése](iot-hub-bulk-identity-mgmt.md)című témakört.

Az eszközök identitásai a Service API-n keresztül is exportálhatók és importálhatók IoT Hub a [REST API](/rest/api/iothub/service/jobs/createimportexportjob) vagy a IoT hub [Service SDK](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)-k egyikével.

## <a name="device-provisioning"></a>Eszköz kiépítés

Az adott IoT-megoldás által tárolt adatok a megoldás konkrét követelményeitől függenek. Azonban a megoldásnak legalább az eszköz-identitásokat és a hitelesítési kulcsokat kell tárolnia. Az Azure IoT Hub tartalmaz egy azonosító beállításjegyzéket, amely az egyes eszközök, például azonosítók, hitelesítési kulcsok és állapotkódok értékeit képes tárolni. Egy megoldás más Azure-szolgáltatásokat is használhat, például a Table Storage-t, a blob Storage-t vagy a Cosmos DB a további eszközbeállítások tárolására.

Az *eszköz kiépítés* során a rendszer a kezdeti eszköz adatait hozzáadja a megoldásban lévő üzletekhez. Ahhoz, hogy egy új eszköz csatlakozhasson a központhoz, hozzá kell adnia egy eszköz AZONOSÍTÓját és kulcsait a IoT Hub Identity registryhez. A kiépítési folyamat részeként előfordulhat, hogy más megoldás-tárolókban is inicializálnia kell az eszközre vonatkozó adatmennyiséget. Az Azure IoT Hub Device Provisioning Service használatával az emberi beavatkozás nélkül is engedélyezheti a nulla érintéses, igény szerinti üzembe helyezést egy vagy több IoT-hubhoz. További információt a [kiépítési szolgáltatás dokumentációjában](https://azure.microsoft.com/documentation/services/iot-dps)talál.

## <a name="device-heartbeat"></a>Eszköz szívverése

A IoT Hub Identity Registry tartalmaz egy **connectionState** nevű mezőt. A fejlesztés és a hibakeresés során csak a **connectionState** mezőt használja. A IoT-megoldásoknak futási időben nem kell lekérdezni a mezőt. Ne kérdezje le például, hogy az eszköz csatlakoztatva van-e, mielőtt egy felhőből eszközre irányuló üzenetet vagy SMS-t küld a **connectionState** . Azt javasoljuk, hogy a riasztások lekérése és az eszköz kapcsolati állapotának figyelése érdekében a [ **leválasztott eszközre**](iot-hub-event-grid.md#event-types) való feliratkozást Event Grid. Ebből az [oktatóanyagból](iot-hub-how-to-order-connection-state-events.md) megtudhatja, hogyan integrálhatja a csatlakoztatott eszközök és az eszközök kapcsolatait a IoT-megoldás IoT hub.

Ha a IoT-megoldásnak tudnia kell, hogy az eszköz csatlakoztatva van-e, megadhatja a *szívverési mintát*.
A szívverési mintában az eszköz legalább egyszer elküldi az eszközről a felhőbe irányuló üzeneteket (például legalább óránként egyszer). Ezért még akkor is, ha az eszközön nincs olyan adat, amelyet el szeretne küldeni, továbbra is egy üres, az eszközről a felhőbe irányuló üzenetet küld (általában egy olyan tulajdonsággal, amely szívverésként azonosítja azt). A szolgáltatás oldalán a megoldás egy térképet tart fenn az egyes eszközökön fogadott utolsó szívveréssel. Ha a megoldás nem kap szívverési üzenetet az eszköztől várt időn belül, feltételezi, hogy probléma merült fel az eszközön.

Az összetettebb implementációk magukban foglalhatják a [Azure monitor](../azure-monitor/index.yml) és a [Azure Resource Health](../service-health/resource-health-overview.md) adatait, hogy azonosítsák a csatlakozni próbáló vagy a sikertelenül kommunikáló eszközöket. További információért lásd: [IoT hub figyelése](monitor-iot-hub.md) és a [IoT hub erőforrás állapotának ellenőrzéséhez](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health). A szívverési minta megvalósításakor ellenőrizze, hogy [IoT hub kvóták és szabályozások](iot-hub-devguide-quotas-throttling.md)szerepelnek-e.

> [!NOTE]
> Ha egy IoT-megoldás csak a kapcsolódási állapotot használja a felhőből az eszközre irányuló üzenetek küldéséhez, és az üzenetek nem küldhetők el nagy mennyiségű eszközre, érdemes lehet az egyszerűbb *rövid lejárati időt* használni. Ez a minta ugyanazt az eredményt éri el, mint az eszköz-kapcsolódási állapot beállításjegyzékének megtartása a szívverési minta használatával, miközben hatékonyabb. Ha üzenet-visszaigazolást kér, IoT Hub értesítést kaphat arról, hogy mely eszközök fogadhatnak üzeneteket, és melyek nem.

## <a name="device-and-module-lifecycle-notifications"></a>Eszközök és modulok életciklusára vonatkozó értesítések

A IoT Hub értesítéseket küldhet a IoT-megoldásról, amikor egy identitást hoz létre vagy töröl az életciklus-értesítések elküldésével. Ehhez a IoT-megoldásnak létre kell hoznia egy útvonalat, és az adatforrást a *DeviceLifecycleEvents* vagy a *ModuleLifecycleEvents* értékkel kell beállítania. Alapértelmezés szerint a rendszer nem küld életciklus-értesítéseket, azaz nem léteznek ilyen útvonalak. Az értesítési üzenet tartalmazza a tulajdonságokat és a szövegtörzset.

Tulajdonságok: az üzenetrendszer tulajdonságai előtaggal vannak ellátva a `$` szimbólummal.

Értesítési üzenet az eszközhöz:

| Name | Érték |
| --- | --- |
|$content típusa | application/json |
|$iothub – enqueuedtime |  Az értesítés elküldésének ideje |
|$iothub – üzenet – forrás | deviceLifecycleEvents |
|$content – kódolás | UTF-8 |
|opType | **createDeviceIdentity** vagy **deleteDeviceIdentity** |
|hubName | IoT Hub neve |
|deviceId | Az eszköz azonosítója |
|operationTimestamp | A művelet ISO8601 időbélyege |
|iothub – üzenet – séma | deviceLifecycleNotification |

Törzs: Ez a szakasz JSON formátumú, és a létrehozott eszköz-identitás ikerét jelöli. Példa:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Értesítési üzenet a modulhoz:

| Name | Érték |
| --- | --- |
$content típusa | application/json |
$iothub – enqueuedtime |  Az értesítés elküldésének ideje |
$iothub – üzenet – forrás | moduleLifecycleEvents |
$content – kódolás | UTF-8 |
opType | **createModuleIdentity** vagy **deleteModuleIdentity** |
hubName | IoT Hub neve |
moduleId | A modul azonosítója |
operationTimestamp | A művelet ISO8601 időbélyege |
iothub – üzenet – séma | moduleLifecycleNotification |

Törzs: Ez a szakasz JSON formátumú, és a létrehozott modul-identitás ikerét jelöli. Példa:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Eszköz identitásának tulajdonságai

Az eszközök identitásai JSON-dokumentumokként jelennek meg a következő tulajdonságokkal:

| Tulajdonság | Beállítások | Leírás |
| --- | --- | --- |
| deviceId |kötelező, csak olvasható a frissítésekben |Kis-és nagybetűket megkülönböztető karakterlánc (legfeljebb 128 karakter) ASCII 7 bites alfanumerikus karakterekből és bizonyos speciális karakterekből: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |kötelező, csak olvasható |Egy IoT hub által generált, kis-és nagybetűket megkülönböztető karakterlánc legfeljebb 128 karakter hosszú lehet. Ez az érték az azonos **deviceId**-vel rendelkező eszközök megkülönböztetésére szolgál, ha azokat törölték és újra létrehozták. |
| ETAG |kötelező, csak olvasható |Egy olyan karakterlánc, amely az eszköz identitásának gyenge ETag jelöli, mint [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |választható |A hitelesítési adatokat és biztonsági anyagokat tartalmazó összetett objektum. |
| Auth. symkey |választható |Base64 formátumban tárolt elsődleges és másodlagos kulcsot tartalmazó összetett objektum. |
| status |kötelező |Egy hozzáférési mutató. **Engedélyezhető** vagy **letiltható**. Ha **engedélyezve** van, az eszköz csatlakozhat. Ha **le van tiltva**, az eszköz nem fér hozzá az eszközre irányuló végpontokhoz. |
| statusReason |választható |Egy 128 karakter hosszú karakterlánc, amely az eszköz identitási állapotának okát tárolja. Minden UTF-8 karakter engedélyezett. |
| statusUpdateTime |csak olvasható |Egy időbeli jelző, amely a legutóbbi állapot frissítésének dátumát és időpontját mutatja. |
| connectionState |csak olvasható |A kapcsolat állapotát jelző mező: **csatlakoztatva** vagy **leválasztva**. Ez a mező az eszköz-kapcsolatok állapotának IoT Hub nézetét jelöli. **Fontos**: Ez a mező csak fejlesztési/hibakeresési célokra használható. A MQTT vagy AMQP használó eszközök esetén a rendszer csak a kapcsolatok állapotát frissíti. Emellett a protokoll szintű pingeléseken (MQTT pingelések vagy AMQP pingek) alapul, és legfeljebb 5 percet vehet igénybe. Ezen okok miatt hamis pozitívak lehetnek, például a csatlakoztatottként jelentett, de leválasztott eszközök. |
| connectionStateUpdatedTime |csak olvasható |Egy időbeli jelző, amely a dátumot és a kapcsolatok állapotának legutóbbi frissítését mutatja. |
| lastActivityTime |csak olvasható |Egy időbeli jelző, amely azt mutatja, hogy az eszköz Mikor kapcsolódott, illetve mikor érkezett, illetve mikor küldött üzenetet. Ez a tulajdonság végül konzisztens, de akár 5 – 10 percet is késleltethető. Ezért nem használható éles környezetben. |

> [!NOTE]
> A kapcsolási állapot csak a kapcsolatok állapotának IoT Hub nézetét jelenítheti meg. Az állapot frissítései a hálózati feltételektől és konfigurációktól függően késleltetve lehetnek.

> [!NOTE]
> Az eszköz-SDK-k jelenleg nem támogatják a `+` és a `#` karaktereket a **deviceId**-ben.

## <a name="module-identity-properties"></a>Modul identitásának tulajdonságai

A modul identitásai JSON-dokumentumokként jelennek meg a következő tulajdonságokkal:

| Tulajdonság | Beállítások | Leírás |
| --- | --- | --- |
| deviceId |kötelező, csak olvasható a frissítésekben |Kis-és nagybetűket megkülönböztető karakterlánc (legfeljebb 128 karakter) ASCII 7 bites alfanumerikus karakterekből és bizonyos speciális karakterekből: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| moduleId |kötelező, csak olvasható a frissítésekben |Kis-és nagybetűket megkülönböztető karakterlánc (legfeljebb 128 karakter) ASCII 7 bites alfanumerikus karakterekből és bizonyos speciális karakterekből: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |kötelező, csak olvasható |Egy IoT hub által generált, kis-és nagybetűket megkülönböztető karakterlánc legfeljebb 128 karakter hosszú lehet. Ez az érték az azonos **deviceId**-vel rendelkező eszközök megkülönböztetésére szolgál, ha azokat törölték és újra létrehozták. |
| ETAG |kötelező, csak olvasható |Egy olyan karakterlánc, amely az eszköz identitásának gyenge ETag jelöli, mint [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |választható |A hitelesítési adatokat és biztonsági anyagokat tartalmazó összetett objektum. |
| Auth. symkey |választható |Base64 formátumban tárolt elsődleges és másodlagos kulcsot tartalmazó összetett objektum. |
| status |kötelező |Egy hozzáférési mutató. **Engedélyezhető** vagy **letiltható**. Ha **engedélyezve** van, az eszköz csatlakozhat. Ha **le van tiltva**, az eszköz nem fér hozzá az eszközre irányuló végpontokhoz. |
| statusReason |választható |Egy 128 karakter hosszú karakterlánc, amely az eszköz identitási állapotának okát tárolja. Minden UTF-8 karakter engedélyezett. |
| statusUpdateTime |csak olvasható |Egy időbeli jelző, amely a legutóbbi állapot frissítésének dátumát és időpontját mutatja. |
| connectionState |csak olvasható |A kapcsolat állapotát jelző mező: **csatlakoztatva** vagy **leválasztva**. Ez a mező az eszköz-kapcsolatok állapotának IoT Hub nézetét jelöli. **Fontos**: Ez a mező csak fejlesztési/hibakeresési célokra használható. A MQTT vagy AMQP használó eszközök esetén a rendszer csak a kapcsolatok állapotát frissíti. Emellett a protokoll szintű pingeléseken (MQTT pingelések vagy AMQP pingek) alapul, és legfeljebb 5 percet vehet igénybe. Ezen okok miatt hamis pozitívak lehetnek, például a csatlakoztatottként jelentett, de leválasztott eszközök. |
| connectionStateUpdatedTime |csak olvasható |Egy időbeli jelző, amely a dátumot és a kapcsolatok állapotának legutóbbi frissítését mutatja. |
| lastActivityTime |csak olvasható |Egy időbeli jelző, amely azt mutatja, hogy az eszköz Mikor kapcsolódott, illetve mikor érkezett, illetve mikor küldött üzenetet. |

> [!NOTE]
> Az eszköz-SDK-k jelenleg nem támogatják a `+` és a `#` karaktereket a **DeviceID** és a **moduleId** használatával.

## <a name="additional-reference-material"></a>További referenciaanyagok

A IoT Hub Fejlesztői útmutatóban található további témakörök a következők:

* [IoT hub végpontok](iot-hub-devguide-endpoints.md) ismertetik a különböző végpontokat, amelyeket az egyes IoT hub a futásidejű és a felügyeleti műveletek számára tesz elérhetővé.

* A [szabályozás és a kvóták](iot-hub-devguide-quotas-throttling.md) a IoT hub szolgáltatásra vonatkozó kvótákat és szabályozási viselkedéseket ismertetik.

* Az [Azure IoT-eszközök és-szolgáltatások SDK](iot-hub-devguide-sdks.md) -k felsorolja azokat a különböző nyelvi SDK-kat, amelyek a IoT hub használatával kommunikáló eszköz-és szolgáltatás-alkalmazások fejlesztéséhez használhatók.

* [IoT hub lekérdezési nyelv](iot-hub-devguide-query-language.md) leírja a lekérdezési nyelvet, amellyel információkat kérhet le az eszközökről, és feladatairól IoT hub.

* [IOT hub MQTT-támogatás](iot-hub-mqtt-support.md) további információkat nyújt a MQTT protokoll IoT hub támogatásáról.

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan használhatja a IoT Hub Identity registryt, az alábbi IoT Hub fejlesztői útmutató témaköreiben találhat további információt:

* [IoT Hub-hozzáférés szabályozása](iot-hub-devguide-security.md)

* [Az állapot és a konfigurációk szinkronizálása az eszközök ikrek használatával](iot-hub-devguide-device-twins.md)

* [Közvetlen metódus meghívása egy eszközön](iot-hub-devguide-direct-methods.md)

* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

A cikkben ismertetett fogalmak némelyikének kipróbálásához tekintse meg a következő IoT Hub oktatóanyagot:

* [Ismerkedés az Azure IoT Hub szolgáltatással](quickstart-send-telemetry-dotnet.md)

Ha szeretné megtekinteni a IoT Hub Device Provisioning Service használatát a nulla érintéses, igény szerinti kiépítés engedélyezéséhez, olvassa el a következő témakört: 

* [Azure IoT Hub Device Provisioning Service](https://azure.microsoft.com/documentation/services/iot-dps)