---
title: A Azure IoT Hub identitásjegyzék | Microsoft Docs
description: Fejlesztői útmutató – az IoT Hub identitásjegyzékének leírása, és annak használata az eszközök kezeléséhez. Az eszköz identitásának tömeges importálására és exportálására vonatkozó információkat tartalmaz.
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
ms.openlocfilehash: 42def04db63d81bdb3eff8098daa8c75924bffec
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502079"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Az IoT Hub identitásjegyzékének az ellenőrzése

Minden IoT Hub rendelkezik egy identitásjegyzékvel, amely az IoT Hubhoz való csatlakozáshoz engedélyezett eszközök és modulok adatait tárolja. Ahhoz, hogy egy eszköz vagy modul csatlakozni tudjon egy IoT Hubhoz, meg kell lennie egy bejegyzésnek az eszközhöz vagy modulhoz az IoT Hub identitásjegyzékében. Egy eszköznek vagy modulnak is hitelesítenie kell magát az IoT Hubbal az identitásjegyzékben tárolt hitelesítő adatok alapján.

Az identitásjegyzékben tárolt eszköz- vagy modulazonosító megkülönbözteti a kis- és nagybetűket.

Az identitásjegyzék az eszköz- vagy modulidentitás-erőforrások REST-kompatibilis gyűjteménye. Amikor hozzáad egy bejegyzést az identitásjegyzékhez, a IoT Hub létrehoz egy eszközönkénti erőforráskészletet, például egy üzenetsort, amely felhőből az eszközre úszó üzeneteket tartalmaz.

Használja az identitásjegyzéket, ha a következőre van szüksége:

* Az IoT Hubhoz csatlakozó eszközök vagy modulok kiépítése.
* Az eszközönkénti/modulonkénti hozzáférés vezérlése a hub eszközéhez vagy modulhoz elérhető végpontjaihoz.

> [!NOTE]
> * Az identitásjegyzék nem tartalmaz alkalmazásspecifikus metaadatokat.
> * A modulidentitás és a modulikre nyilvános előzetes verzióban érhető el. Az alábbi funkció támogatott lesz a modulidentitásban, ha az általánosan elérhető.
>

## <a name="identity-registry-operations"></a>Identitásjegyzék műveletei

A IoT Hub identity registry a következő műveleteket teszi elérhetővé:

* Eszköz- vagy modulidentitás létrehozása
* Eszköz- vagy modulidentitás frissítése
* Eszköz- vagy modulidentitás lekérése azonosító alapján
* Eszköz- vagy modulidentitás törlése
* Legfeljebb 1000 identitás listosítása
* Eszköz identitásának exportálása az Azure Blob Storage-ba
* Eszköz identitásának importálása az Azure Blob Storage-ból

Mindezek a műveletek optimista egyidejűséget használhatnak az [RFC7232](https://tools.ietf.org/html/rfc7232)szabványban megadottak szerint.

> [!IMPORTANT]
> Az IoT Hub identitásjegyzékében az összes identitás lekérésének egyetlen módja az [Exportálás funkció](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) használata.

Egy IoT Hub identitásjegyzék:

* Nem tartalmaz alkalmazás-metaadatokat.
* Szótárként érhető el a **deviceId** vagy **a moduleId** kulcs használatával.
* Nem támogatja a kifejező lekérdezéseket.

Az IoT-megoldások általában külön megoldásspecifikus tárolóval rendelkezik, amely alkalmazásspecifikus metaadatokat tartalmaz. Egy intelligens épület megoldásának megoldásspecifikus tárolója például rögzítheti azt a helyiséget, amelyben egy hőmérséklet-érzékelő üzembe lett helyezni.

> [!IMPORTANT]
> Az identitásjegyzéket csak eszközkezelési és kiépítési műveletekhez használja. A futásidőben futtatott nagy átviteli sebességű műveletek nem függenek az identitásjegyzékben a műveletek elvégzésétől. Egy eszköz kapcsolati állapotának ellenőrzése például a parancs elküldése előtt nem támogatott minta. Ellenőrizze az identitásjegyzék és az eszköz szívverési mintája szabályozási [rátáját.](iot-hub-devguide-identity-registry.md#device-heartbeat) [](iot-hub-devguide-quotas-throttling.md)

## <a name="disable-devices"></a>Eszközök letiltása

Az eszközök letiltásához frissítse egy identitás **status** tulajdonságát az identitásjegyzékben. Ezt a tulajdonságot általában két forgatókönyvben használhatja:

* A kiépítési vezénylési folyamat során. További információ: [Device Provisioning](iot-hub-devguide-identity-registry.md#device-provisioning).

* Ha valamilyen okból úgy gondolja, hogy egy eszközt feltörtek vagy jogosulatlanná váltak.

Ez a funkció modulokhoz nem érhető el.

## <a name="import-and-export-device-identities"></a>Eszköz identitásának importálása és exportálása

Az IoT Hub erőforrás-szolgáltatói végponton aszinkron műveletekkel tömegesen exportálhatja az eszközidentitásokat az IoT Hub identitásjegyzékből. [](iot-hub-devguide-endpoints.md) Az exportálások olyan hosszan futó feladatok, amelyek ügyfél által biztosított blobtárolóval mentik az identitásjegyzékből beolvasott eszközidentitás-adatokat.

Az IoT Hub erőforrás-szolgáltatói végponton aszinkron műveletekkel tömegesen importálhatja az eszközidentitásokat az IoT Hub identitásjegyzékbe. [](iot-hub-devguide-endpoints.md) Az importok hosszú ideig futó feladatok, amelyek az ügyfél által megadott blobtárolóban lévő adatokat használják az eszközidentitás-adatok az identitásjegyzékbe való írásához.

További információ az importálási és exportálási API-król: IoT Hub [REST API-k.](/rest/api/iothub/iothubresource) További információ az importálási és exportálási feladatok futtatásáról: [Eszköz IoT Hub tömeges kezelése.](iot-hub-bulk-identity-mgmt.md)

Az eszköz identitásai exportálhatók és importálhatók IoT Hub szolgáltatás API-n keresztül a [REST API](/rest/api/iothub/service/jobs/createimportexportjob) vagy az egyik IoT Hub [Service SDK-n keresztül.](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)

## <a name="device-provisioning"></a>Eszköz kiépítése

Az adott IoT-megoldás által tartalmazott eszközadatok a megoldás konkrét követelményeitől függnek. A megoldásoknak azonban legalább eszköz identitásokat és hitelesítési kulcsokat kell tárolniuk. Azure IoT Hub tartalmaz egy identitásjegyzéket, amely képes tárolni az egyes eszközök értékeit, például azonosítókat, hitelesítési kulcsokat és állapotkódokat. A megoldások más Azure-szolgáltatásokat is használhatnak, például a Table Storage-et, a blobtárolót vagy Cosmos DB további eszközadatokat tárolnak.

*Az eszköz kiépítése* az a folyamat, amely során hozzáadja a kezdeti eszközadatokat a megoldásban található tárolókhoz. Ahhoz, hogy egy új eszköz csatlakozzon a hubhoz, hozzá kell adni egy eszközazonosítót és kulcsokat a IoT Hub identitásjegyzékéhez. Előfordulhat, hogy a kiépítési folyamat részeként eszközspecifikus adatokat kell inicializálni más megoldástárban. A Azure IoT Hub Device Provisioning Service használatával érintés nélküli, igény szerinti kiépítést is engedélyezhet egy vagy több IoT Hubon anélkül, hogy emberi beavatkozásra lenne szükség. További tudnivalókért tekintse meg a [kiépítési szolgáltatás dokumentációját.](https://azure.microsoft.com/documentation/services/iot-dps)

## <a name="device-heartbeat"></a>Eszköz szívverése

A IoT Hub identitásjegyzéke tartalmaz egy connectionState nevű **mezőt.** Csak a **connectionState mezőt** használja a fejlesztés és a hibakeresés során. Az IoT-megoldásoknak futásidőben nem szabad lekérdezni a mezőt. A **connectionState** mezőben például ne ellenőrizze, hogy az eszköz csatlakoztatva van-e, mielőtt a felhőből az eszközre küld egy üzenetet vagy egy SMS-t. Javasoljuk, hogy a [  ](iot-hub-event-grid.md#event-types) riasztások lekért és az eszköz kapcsolati állapotának figyelése érdekében Event Grid az eszköz leválasztott eseményére a Event Grid feliratkozáskor. Ebből az [oktatóanyagból](iot-hub-how-to-order-connection-state-events.md) megtudhatja, hogyan integrálhatja a csatlakoztatott eszközökről és az eszközökről leválasztott eseményeket IoT Hub ioT-megoldásba.

Ha az IoT-megoldásnak tudnia kell, hogy csatlakoztatva van-e egy eszköz, megvalósíthatja a *szívverési mintát.*
A szívverési mintában az eszköz legalább egyszer küld az eszközről a felhőbe üzeneteket rögzített idő alatt (például óránként legalább egyszer). Ezért még ha egy eszköznek nincs is elküldenie adat, akkor is küld egy üres, az eszközről a felhőbe küldött üzenetet (általában egy olyan tulajdonsággal, amely szívverésként azonosítja). A szolgáltatás oldalán a megoldás egy térképet tart fenn az egyes eszközökre vonatkozó legutóbbi szívveréssel. Ha a megoldás nem kap szívverési üzenetet az eszköztől a várt idő alatt, azt feltételezi, hogy probléma van az eszközzel.

Egy összetettebb megvalósítás magában [](../azure-monitor/index.yml) foglalhatja a [](../service-health/resource-health-overview.md) Azure Monitor és Azure Resource Health, hogy azonosítsa azokat az eszközöket, amelyek csatlakozni vagy kommunikálni próbálnak, de sikertelenek. További információ: [Monitor IoT Hub](monitor-iot-hub.md) és [Check IoT Hub resource health](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health). A szívverési minta megvalósításakor ellenőrizze a [következőt: Kvóták és IoT Hub szabályozások.](iot-hub-devguide-quotas-throttling.md)

> [!NOTE]
> Ha egy IoT-megoldás kizárólag a kapcsolati állapot alapján határozza meg, hogy küld-e a rendszer felhőből  az eszközre üzeneteket, és az üzenetek nem nagy eszközkészletre vannak küldve, fontolja meg az egyszerűbb rövid lejárati idő mintáját. Ez a minta ugyanazt az eredményt éri el, mint az eszköz kapcsolati állapotjegyzékének szívverési minta használatával való fenntartása, miközben hatékonyabb. Ha üzenet nyugtázását kéri, IoT Hub értesítést küldhet arról, hogy mely eszközök fogadhatnak üzeneteket, és melyek nem.

## <a name="device-and-module-lifecycle-notifications"></a>Eszközök és modulok életciklusával kapcsolatos értesítések

IoT Hub az IoT-megoldást, amikor eszközidentitás jön létre vagy törlődik, életciklus-értesítések küldésével. Az IoT-megoldásnak létre kell hoznia egy útvonalat, és az adatforrást a *DeviceLifecycleEvents értékkel kell egyenlőre állítania.* Alapértelmezés szerint a rendszer nem küld életciklus-értesítéseket, azaz nem létezik ilyen útvonal. Ha a *DeviceLifecycleEvents* értékkel egyenlő adatforrással hoz létre útvonalat, a rendszer az eszköz- és a modul-identitásokhoz is el fogja küldeni az életciklus-eseményeket; Az üzenet tartalma azonban attól függően változik, hogy az események modul-identitásokhoz vagy eszköz-identitásokhoz jönnek-e létre.  Fontos megjegyezni, hogy IoT Edge modulok esetében a modulidentitás-létrehozási folyamat eltér a többi modultól, ezért IoT Edge modulok esetében a létrehozási értesítés csak akkor lesz elküldve, ha a frissített IoT Edge modulidentitáshoz tartozó megfelelő IoT Edge-eszköz fut. Minden más modul esetében az életciklus-értesítéseket a rendszer minden alkalommal elküldi, amikor a modulidentitás frissül a IoT Hub oldalán.  Az értesítési üzenet tulajdonságokat és törzset tartalmaz.

Tulajdonságok: Az üzenetrendszer tulajdonságai előtagja a `$` szimbólum.

Értesítési üzenet az eszközhöz:

| Name | Érték |
| --- | --- |
|$content típus | application/json |
|$iothub-enqueuedtime |  Az értesítés elküldének ideje |
|$iothub-message-source | deviceLifecycleEvents |
|$content kódolás | utf-8 |
|opType (opType) | **createDeviceIdentity** vagy **deleteDeviceIdentity** |
|hubName | A IoT Hub |
|deviceId | Az eszköz azonosítója |
|operationTimestamp | ISO8601 művelet időbélyege |
|iothub-message-schema | deviceLifecycleNotification |

Törzs: Ez a szakasz JSON formátumban van, és a létrehozott eszközidentitás ikereszközét jelöli. Példa:

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
$content típus | application/json |
$iothub-enqueuedtime |  Az értesítés elküldének ideje |
$iothub-message-source | moduleLifecycleEvents |
$content kódolása | utf-8 |
opType (opType) | **createModuleIdentity vagy** **deleteModuleIdentity** |
hubName | A IoT Hub |
moduleId (modulazonosító) | A modul azonosítója |
operationTimestamp | ISO8601 művelet időbélyege |
iothub-message-schema | moduleLifecycleNotification |

Törzs: Ez a szakasz JSON formátumban van, és a létrehozott modulidentitás ikereszközét jelöli. Példa:

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

## <a name="device-identity-properties"></a>Eszközidentitás tulajdonságai

Az eszköz identitásait JSON-dokumentumok képviselik az alábbi tulajdonságokkal:

| Tulajdonság | Beállítások | Leírás |
| --- | --- | --- |
| deviceId |kötelező, csak olvasható a frissítésekhez |Egy kis- és nagybetűket (legfeljebb 128 karakter hosszú) tartalmazó sztring, amely 7 bites ASCII alfanumerikus karaktereket és bizonyos speciális karaktereket tartalmaz: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId (generációazonosító) |kötelező, csak olvasható |Egy IoT Hub által létrehozott, kis- és nagybetűket megkülönböztető sztring, amely legfeljebb 128 karakter hosszú lehet. Ez az érték az azonos **eszközazonosítóval**() megadott eszközök megkülönböztetésére használható, ha törölték és újra létrehozták őket. |
| Etag |kötelező, csak olvasható |Az eszközidentitás gyenge ETagje, az [RFC7232 szabványnak megfelelő sztring.](https://tools.ietf.org/html/rfc7232) |
| Auth |választható |Hitelesítési adatokat és biztonsági anyagokat tartalmazó összetett objektum. |
| auth.symkey |választható |Egy base64 formátumban tárolt elsődleges és másodlagos kulcsot tartalmazó összetett objektum. |
| status |kötelező |Egy hozzáférés-jelző. Engedélyezhető vagy **letiltható.**  Ha **engedélyezve van,** az eszköz csatlakozhat. Ha **le van** tiltva, az eszköz nem tud hozzáférni az eszköz felé néző végponthoz. |
| statusReason (állapot állapota) |választható |Egy 128 karakter hosszú sztring, amely az eszközidentitás állapotának okát tárolja. Minden UTF-8 karakter engedélyezett. |
| statusUpdateTime |csak olvasható |Egy historikus jelző, amely a legutóbbi állapotfrissítés dátumát és időpontját mutatja. |
| connectionState (kapcsolati állam) |csak olvasható |A kapcsolat állapotát jelző  mező: Connected (Csatlakoztatva) vagy **Disconnected (Leválasztva).** Ez a mező az IoT Hub állapotának nézetét jelöli. **Fontos:** Ez a mező csak fejlesztési/hibakeresési célokra használható. A kapcsolat állapota csak az MQTT-t vagy AMQP-t használó eszközök esetében frissül. Emellett protokollszintű pingelésen (MQTT-pingek vagy AMQP-pingek) alapul, és legfeljebb 5 perces késéssel lehet. Ezen okokból téves riasztások is lehetnek, például csatlakoztatottként jelentett, de leválasztott eszközök. |
| connectionStateUpdatedTime |csak olvasható |A kapcsolati állapot frissítésének dátumát és utolsó időpontját mutató historikus jelző. |
| lastActivityTime (lastActivityTime) |csak olvasható |Egy historikus jelző, amely az eszköz csatlakozásának, fogadott vagy küldött üzenetének dátumát és utolsó időpontját mutatja. Ez a tulajdonság végül konzisztens lesz, de akár 5–10 percig is késleltetheti. Ezért nem használható éles forgatókönyvekben. |

> [!NOTE]
> A kapcsolat állapota csak a IoT Hub állapotának nézetét tudja képviselni. Az állapot frissítései a hálózati feltételektől és konfigurációktól függően késhetnek.

> [!NOTE]
> Az eszköz-SDK-k jelenleg nem támogatják a és karaktereket a `+` `#` **deviceId-ban.**

## <a name="module-identity-properties"></a>Modulidentitás tulajdonságai

A modul identitásai JSON-dokumentumokként vannak ábrázolva a következő tulajdonságokkal:

| Tulajdonság | Beállítások | Leírás |
| --- | --- | --- |
| deviceId |kötelező, csak olvasható frissítések |7 bites alfanumerikus ASCII-karaktereket és bizonyos speciális karaktereket tartalmazó, kis- és nagybetűket megkülönböztető sztring (legfeljebb 128 karakter `- . + % _ # * ? ! ( ) , : = @ $ '` hosszú). |
| moduleId (modulazonosító) |kötelező, csak olvasható frissítések |7 bites alfanumerikus ASCII-karaktereket és bizonyos speciális karaktereket tartalmazó, kis- és nagybetűket megkülönböztető sztring (legfeljebb 128 karakter `- . + % _ # * ? ! ( ) , : = @ $ '` hosszú): . |
| generationId (generációazonosító) |kötelező, csak olvasható |Egy IoT Hub által létrehozott, kis- és nagybetűket megkülönböztető sztring, amely legfeljebb 128 karakter hosszú lehet. Ez az érték az azonos **eszközazonosítóval**() megadott eszközök megkülönböztetésére használható, ha törölték és újra létrehozták őket. |
| Etag |kötelező, csak olvasható |Az eszközidentitás gyenge ETagje, az [RFC7232 szabványnak megfelelő sztring.](https://tools.ietf.org/html/rfc7232) |
| Auth |választható |Hitelesítési adatokat és biztonsági anyagokat tartalmazó összetett objektum. |
| auth.symkey |választható |Egy base64 formátumban tárolt elsődleges és másodlagos kulcsot tartalmazó összetett objektum. |
| status |kötelező |Egy hozzáférés-jelző. Engedélyezhető vagy **letiltható.**  Ha **engedélyezve van,** az eszköz csatlakozhat. Ha **le van** tiltva, az eszköz nem tud hozzáférni az eszköz felé néző végponthoz. |
| statusReason (állapot állapota) |választható |Egy 128 karakter hosszú sztring, amely az eszközidentitás állapotának okát tárolja. Minden UTF-8 karakter engedélyezett. |
| statusUpdateTime |csak olvasható |Az utolsó állapotfrissítés dátumát és időpontját mutató historikus jelző. |
| connectionState (kapcsolati állam) |csak olvasható |A kapcsolat állapotát jelző  mező: Connected (Csatlakoztatva) vagy **Disconnected (Leválasztva).** Ez a mező az IoT Hub állapotának nézetét jelöli. **Fontos:** Ez a mező csak fejlesztési/hibakeresési célokra használható. A kapcsolat állapota csak az MQTT-t vagy AMQP-t használó eszközök esetében frissül. Emellett protokollszintű pingelésen (MQTT-pingek vagy AMQP-pingek) alapul, és legfeljebb 5 perces késéssel lehet. Ezen okokból téves riasztások is lehetnek, például a csatlakoztatottként jelentett, de leválasztott eszközök. |
| connectionStateUpdatedTime |csak olvasható |Egy historikus jelző, amely a kapcsolat állapotának frissítésének dátumát és utolsó időpontját mutatja. |
| lastActivityTime (lastActivityTime) |csak olvasható |Egy historikus jelző, amely az eszköz csatlakozásának, fogadott vagy küldött üzenetének dátumát és utolsó időpontját mutatja. |

> [!NOTE]
> Az eszköz-SDK-k jelenleg nem támogatják a és karaktereket a deviceId és `+` `#` a **moduleId elemben.** 

## <a name="additional-reference-material"></a>További referenciaanyagok

A fejlesztői útmutató további referencia-IoT Hub a következők:

* [IoT Hub végpontok](iot-hub-devguide-endpoints.md) az egyes IoT Hubok által a futásidőre és felügyeleti műveletekre elérhetővé tevés különböző végpontokat ismertetik.

* [A szabályozás és a](iot-hub-devguide-quotas-throttling.md) kvóták a szolgáltatásra vonatkozó kvótákat és szabályozási viselkedéseket IoT Hub ismerteti.

* Az Azure IoT eszköz- és [szolgáltatás-SDK-k](iot-hub-devguide-sdks.md) felsorolják azokat a különböző nyelvi SDK-kat, amelyek használhatók az eszköz- és szolgáltatásalkalmazások fejlesztéséhez IoT Hub.

* [IoT Hub lekérdezési nyelv](iot-hub-devguide-query-language.md) azt a lekérdezési nyelvet írja le, amely segítségével információkat lehet lekérni IoT Hub ikereszközeiről és -feladatairól.

* [IoT Hub MQTT-támogatás](iot-hub-mqtt-support.md) további információkat tartalmaz az MQTT IoT Hub támogatásához.

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan használhatja az IoT Hub Identity Registryt, érdekelheti az alábbi IoT Hub fejlesztői útmutató témakörei:

* [IoT Hub-hozzáférés szabályozása](iot-hub-devguide-security.md)

* [Ikereszköz használata az állapot és a konfigurációk szinkronizáláshoz](iot-hub-devguide-device-twins.md)

* [Közvetlen metódus meghívása egy eszközön](iot-hub-devguide-direct-methods.md)

* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

A cikkben ismertetett fogalmak kipróbálhoz tekintse meg a következő IoT Hub oktatóanyagot:

* [Ismerkedés az Azure IoT Hub szolgáltatással](quickstart-send-telemetry-dotnet.md)

Az új IoT Hub Device Provisioning Service érintés nélkül, időben való üzembe építés engedélyezéséhez tekintse meg a következőt: 

* [Azure IoT Hub Device Provisioning Service](https://azure.microsoft.com/documentation/services/iot-dps)
