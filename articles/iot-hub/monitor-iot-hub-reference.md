---
title: Az Azure IoT Hub adatreferenciájának monitorozása
description: Az Azure-IoT Hub figyeléséhez szükséges fontos referenciaanyagok
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/22/2020
ms.openlocfilehash: 6c7fd0a310663b664d33027951ade885b83d458a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570955"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Az Azure IoT Hub adatreferenciájának monitorozása

Az Azure IoT Hub figyelési adatainak gyűjtésével és elemzésével kapcsolatban lásd: az [azure IoT hub figyelése](monitor-iot-hub.md) .

## <a name="metrics"></a>Mérőszámok

Ez a szakasz felsorolja az Azure IoT Hub automatikusan összegyűjtött platform-metrikáit. IoT Hub metrikák erőforrás-szolgáltatói névtere a **Microsoft. Devices** és a Type névtér **IoTHubs**.

A következő alszakaszok kiosztják a IoT Hub platform metrikáit az általános kategória szerint, és azokat a megjelenítendő név alapján listázzák, amelyet a Azure Portal tartalmaz. Az egyes alszakaszokban megjelenő metrikákkal kapcsolatos információkat is meg kell adni.

Az Azure Monitor dokumentációjában a [Microsoft. Devices/IotHubs](../azure-monitor/essentials/metrics-supported.md#microsoftdevicesiothubs) alatt található összes IoT hub platform metrikájának listáját is megtalálhatja. Vegye figyelembe, hogy ez a táblázat nem tartalmaz néhány információt, például a jelen cikkben elérhető egyes mérőszámok [támogatott összesítéseit](#supported-aggregations) .

A más Azure-szolgáltatások által támogatott mérőszámokkal kapcsolatos további információkért lásd: [támogatott metrikák Azure monitor](../azure-monitor/essentials/metrics-supported.md)használatával.

**A szakasz témakörei**

- [Az Azure IoT Hub adatreferenciájának monitorozása](#monitoring-azure-iot-hub-data-reference)
  - [Metrikák](#metrics)
    - [Támogatott összesítések](#supported-aggregations)
    - [Felhőből az eszközre irányuló parancsok mérőszámai](#cloud-to-device-command-metrics)
    - [Felhőből az eszközre irányuló közvetlen módszerek mérőszámai](#cloud-to-device-direct-methods-metrics)
    - [Felhőből az eszközre kettős műveleti mérőszámok](#cloud-to-device-twin-operations-metrics)
    - [Konfigurációk metrikái](#configurations-metrics)
    - [Napi kvóta mérőszámai](#daily-quota-metrics)
    - [Eszköz metrikái](#device-metrics)
    - [Eszköz telemetria metrikái](#device-telemetry-metrics)
    - [Eszközről a felhőbe irányuló Twin Operations metrikák](#device-to-cloud-twin-operations-metrics)
    - [Event Grid-metrikák](#event-grid-metrics)
    - [Feladatok metrikái](#jobs-metrics)
    - [Útválasztási metrikák](#routing-metrics)
    - [Dupla lekérdezési metrika](#twin-query-metrics)
  - [Metrikus méretek](#metric-dimensions)
  - [Erőforrásnaplók](#resource-logs)
    - [Kapcsolatok](#connections)
    - [Eszköz telemetria](#device-telemetry)
    - [Felhő–eszköz irányú parancsok](#cloud-to-device-commands)
    - [Eszköz-identitási műveletek](#device-identity-operations)
    - [Fájlfeltöltési műveletek](#file-upload-operations)
    - [Útvonalak](#routes)
    - [Az eszközről a felhőbe irányuló kettős művelet](#device-to-cloud-twin-operations)
    - [A felhőből az eszközre irányuló kettős műveletek](#cloud-to-device-twin-operations)
    - [Dupla lekérdezés](#twin-queries)
    - [Feladatműveletek](#jobs-operations)
    - [Közvetlen metódusok](#direct-methods)
    - [Elosztott nyomkövetés (előzetes verzió)](#distributed-tracing-preview)
      - [IoT Hub D2C (eszközről a felhőbe irányuló) naplók](#iot-hub-d2c-device-to-cloud-logs)
      - [Bejövő IoT Hubi naplók](#iot-hub-ingress-logs)
      - [IoT Hub kimenő naplók](#iot-hub-egress-logs)
    - [Konfigurációk](#configurations)
    - [Eszköz streamek (előzetes verzió)](#device-streams-preview)
  - [Azure Monitor naplók táblái](#azure-monitor-logs-tables)
  - [Lásd még:](#see-also)

### <a name="supported-aggregations"></a>Támogatott összesítések

Az egyes táblák **összesítési típus** oszlopa az alapértelmezett összesítésnek felel meg, amelyet a rendszer akkor használ, amikor a metrika ki van választva egy diagramhoz vagy riasztáshoz.

   ![A metrikák összesítését bemutató képernyőkép](./media/monitor-iot-hub-reference/aggregation-type.png)

A legtöbb mérőszám esetében az összes összesítési típus érvényes; a Count típusú metrikák esetében **azonban a** **darabszám** oszlop értékének értéke csak bizonyos összesítések érvényesek. A metrikák száma a következő két típus egyike lehet:

* Az **egypontos** darabszámú mérőszámok esetében IoT hub egyetlen adatpontot regisztrál – lényegében egy 1 – a mért művelet minden alkalommal. A Azure Monitor ezután összesíti ezeket az adatpontokat a megadott részletességgel. Az **egypontos** mérőszámok például a *telemetria küldött üzenetek* , illetve a C2D-kézbesítések *befejezése*. Ezekben a metrikákban az egyetlen kapcsolódó összesítési típus a Total (Sum). A portál lehetővé teszi a minimális, a maximális és az átlagos érték kiválasztását; Ezek az értékek azonban mindig 1 lesznek.

* A **Pillanatképek** számának mérőszámai esetében IoT hub a mért művelet bekövetkeztekor teljes darabszámot regisztrál. Jelenleg három **Pillanatkép** -metrikát bocsát ki IoT hub: *a felhasznált üzenetek teljes száma, az* *összes eszköz (előzetes verzió)* és a *csatlakoztatott eszközök (előzetes verzió)*. Mivel ezek a metrikák minden kibocsátása után "összesen" mennyiséget mutatnak be, a megadott részletességi értékkel összesítve nincs értelme. Azure Monitor korlátozza a metrikák összesítési típusának átlagos, minimális és maximális értékének kiválasztását.

### <a name="cloud-to-device-command-metrics"></a>Felhőből az eszközre irányuló parancsok mérőszámai

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|C2D-üzenetek lejárt (előzetes verzió)|C2DMessagesExpired|Darabszám|Összesen|A felhőből az eszközre irányuló lejárt üzenetek száma|Nincs|
|C2D-üzenetek kézbesítésének befejezése|C2D. commands. kimenő. Complete. success|Darabszám|Összesen|Az eszköz által sikeresen befejeződött a felhőből az eszközre küldött üzenetek száma|Nincs|
|C2D üzenetek elhagyva|C2D. commands. kimenő. elhagyása. sikeres|Darabszám|Összesen|Az eszköz által elhagyott felhőből az eszközre irányuló üzenetek száma|Nincs|
|C2D-üzenetek elutasítva|C2D. commands. kimenő. elutasítás. sikeres|Darabszám|Összesen|Az eszköz által visszautasított felhőből eszközre irányuló üzenetek száma|Nincs|

A **darabszám** **értékkel** rendelkező metrikák esetében érvényes a Total (Sum) összesítés. A minimális, maximális és átlagos összesítések mindig 1 értéket adnak vissza. További információ: [támogatott összesítések](#supported-aggregations).

### <a name="cloud-to-device-direct-methods-metrics"></a>Felhőből az eszközre irányuló közvetlen módszerek mérőszámai

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|Sikertelen közvetlen metódusok meghívása|C2D. Methods. failure|Darabszám|Összesen|A sikertelen közvetlen metódusok összes hívásának száma.|Nincs|
|A közvetlen metódus meghívásának mérete|C2D. Methods. requestSize|Bájt|Átlag|A közvetlen metódusok összes sikeres kérelmének száma.|Nincs|
|A közvetlen metódus-meghívások válaszának mérete|C2D. Methods. responseSize|Bájt|Átlag|Az összes sikeres közvetlen metódus válaszának száma.|Nincs|
|Közvetlen metódusok sikeres meghívása|C2D. Methods. success|Darabszám|Összesen|Az összes sikeres közvetlen metódus hívásának száma.|Nincs|

Az **egységnyi** értékkel rendelkező mérőszámok esetében **a csak összeg** (Sum) összesítés érvényes. A minimális, maximális és átlagos összesítések mindig 1 értéket adnak vissza. További információ: [támogatott összesítések](#supported-aggregations).

### <a name="cloud-to-device-twin-operations-metrics"></a>Felhőből az eszközre kettős műveleti mérőszámok

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|Sikertelen dupla olvasások a háttérből|C2D. Twin. Read. failure|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla olvasás száma.|Nincs|
|Sikertelen dupla frissítések a háttérből|C2D. Twin. Update. failure|Darabszám|Összesen|Az összes sikertelen háttér által kezdeményezett dupla frissítés száma.|Nincs|
|Dupla olvasások válaszának mérete a háttérből|C2D. Twin. Read. size|Bájt|Átlag|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincs|
|Dupla frissítések mérete a háttérből|C2D. Twin. Update. size|Bájt|Átlag|Az összes sikeres háttér által kezdeményezett dupla frissítés teljes mérete.|Nincs|
|Sikeres dupla olvasások a háttérből|C2D. Twin. Read. success|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla olvasás száma.|Nincs|
|Sikeres dupla frissítések a háttérből|C2D. Twin. Update. success|Darabszám|Összesen|Az összes sikeres háttér által kezdeményezett dupla frissítés száma.|Nincs|

A **darabszám** **értékkel** rendelkező metrikák esetében érvényes a Total (Sum) összesítés. A minimális, maximális és átlagos összesítések mindig 1 értéket adnak vissza. További információ: [támogatott összesítések](#supported-aggregations).

### <a name="configurations-metrics"></a>Konfigurációk metrikái

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|Konfigurációs mérőszámok|konfigurációk|Darabszám|Összesen|Az eszközök konfigurálásához és IoT Edge üzembe helyezéséhez végrehajtott összes szifilisz-művelet száma a céleszköz készletén. Ebbe beletartozik azoknak a műveleteknek a száma is, amelyek az eszköz Twin vagy modulját módosítják ezen konfigurációk miatt.|Nincs|

A **darabszám** **értékkel** rendelkező metrikák esetében érvényes a Total (Sum) összesítés. A minimális, maximális és átlagos összesítések mindig 1 értéket adnak vissza. További információ: [támogatott összesítések](#supported-aggregations).

### <a name="daily-quota-metrics"></a>Napi kvóta mérőszámai

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|Az eszköz összes adatfelhasználása|deviceDataUsage|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincs|
|Az eszköz összes adatfelhasználása (előzetes verzió)|deviceDataUsageV2|Bájt|Összesen|A IotHub-hez csatlakoztatott eszközökről és azokról továbbított bájtok|Nincs|
|A felhasznált üzenetek teljes száma|dailyMessageQuotaUsed|Darabszám|Átlag|A jelenleg használt üzenetek teljes száma. Ez egy kumulatív érték, amely minden nap 00:00-kor, a nulla időpontra visszaáll.|Nincs|

A *felhasznált üzenetek teljes száma* csak a minimális, a maximális és az átlagos összesítések támogatottak. További információ: [támogatott összesítések](#supported-aggregations).

### <a name="device-metrics"></a>Eszköz metrikái

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|Összes eszköz (elavult)|Devices. totalDevices|Darabszám|Összesen|Az IoT hub-ban regisztrált eszközök száma|Nincs|
|Csatlakoztatott eszközök (elavult) |Devices. connectedDevices. allProtocol|Darabszám|Összesen|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincs|
|Összes eszköz (előzetes verzió)|totalDeviceCount|Darabszám|Átlag|Az IoT hub-ban regisztrált eszközök száma|Nincs|
|Csatlakoztatott eszközök (előzetes verzió)|connectedDeviceCount|Darabszám|Átlag|Az IoT hub-hoz csatlakoztatott eszközök száma|Nincs|

Az *összes eszköz (elavult* ) és a *csatlakoztatott eszközök (elavult)* esetében csak a Total (Sum) összesítés érvényes. A minimális, maximális és átlagos összesítések mindig 1 értéket adnak vissza. További információ: [támogatott összesítések](#supported-aggregations).

Az *összes eszköz (előzetes verzió)* és a *csatlakoztatott eszközök (előzetes verzió)* esetében csak a minimális, a maximális és az átlagos összesítés érvényes. További információ: [támogatott összesítések](#supported-aggregations).

A *csatlakoztatott eszközök (előzetes verzió)* és az *összes eszköz (előzetes verzió)* diagnosztikai beállításokon keresztül nem exportálhatók.

### <a name="device-telemetry-metrics"></a>Eszköz telemetria metrikái

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|Szabályozási hibák száma|D2C. telemetria. beáramló. sendThrottle|Darabszám|Összesen|Az eszköz átviteli sebessége miatti szabályozási hibák száma|Nincs|
|Telemetria üzenetek küldése|D2C. telemetria. beáramló. allProtocol|Darabszám|Összesen|Az IoT-hubhoz küldendő eszközről a felhőbe irányuló telemetria-üzenetek száma|Nincs|
|Telemetria üzenetek elküldése|D2C. telemetria. beáramló. sikeres|Darabszám|Összesen|Az IoT hub sikeresen elküldött eszközről a felhőbe irányuló telemetria-üzeneteinek száma|Nincs|

A **darabszám** **értékkel** rendelkező metrikák esetében érvényes a Total (Sum) összesítés. A minimális, maximális és átlagos összesítések mindig 1 értéket adnak vissza. További információ: [támogatott összesítések](#supported-aggregations).

### <a name="device-to-cloud-twin-operations-metrics"></a>Eszközről a felhőbe irányuló Twin Operations metrikák

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|Sikertelen dupla olvasások az eszközökről|D2C. Twin. Read. failure|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett kettős olvasás száma.|Nincs|
|Sikertelen dupla frissítések az eszközökről|D2C. Twin. Update. failure|Darabszám|Összesen|Az összes sikertelen eszköz által kezdeményezett dupla frissítés száma.|Nincs|
|Az eszközökről érkező kettős olvasások válaszának mérete|D2C. Twin. Read. size|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincs|
|Az eszközökből származó kettős frissítések mérete|D2C. Twin. Update. size|Bájt|Átlag|Az összes sikeres eszköz által kezdeményezett dupla frissítés teljes mérete.|Nincs|
|Sikeres dupla olvasások az eszközökről|D2C. Twin. Read. success|Darabszám|Összesen|Az összes sikeres eszköz által kezdeményezett dupla olvasás száma.|Nincs|
|Sikeres dupla frissítések az eszközökről|D2C. Twin. Update. success|Darabszám|Összesen|Az eszköz által kezdeményezett két frissítés sikeres száma.|Nincs|

A **darabszám** **értékkel** rendelkező metrikák esetében érvényes a Total (Sum) összesítés. A minimális, maximális és átlagos összesítések mindig 1 értéket adnak vissza. További információ: [támogatott összesítések](#supported-aggregations).

### <a name="event-grid-metrics"></a>Event Grid-metrikák

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|Event Grid kézbesítések (előzetes verzió)|EventGridDeliveries|Darabszám|Összesen|A Event Grid közzétett IoT Hub események száma. Használja az eredmény dimenziót a sikeres és sikertelen kérelmek számának megkereséséhez. A EventType-dimenzió megjeleníti az esemény típusát ( https://aka.ms/ioteventgrid) .|Eredmény,<br/>EventType<br>*További információ: [metrikus dimenziók](#metric-dimensions)*.|
|Event Grid késés (előzetes verzió)|EventGridLatency|Ezredmásodpercben|Átlag|Az átlagos késés (ezredmásodpercben), amikor az IOT hub-eseményt generálták, amikor az eseményt közzétették Event Grid. Ez a szám az összes eseménytípus közötti átlag. Az EventType dimenzió használatával megtekintheti egy adott típusú esemény késését.|EventType<br>*További információ: [metrikus dimenziók](#metric-dimensions)*.|

A **darabszám** **értékkel** rendelkező metrikák esetében érvényes a Total (Sum) összesítés. A minimális, maximális és átlagos összesítések mindig 1 értéket adnak vissza. További információ: [támogatott összesítések](#supported-aggregations).

### <a name="jobs-metrics"></a>Feladatok metrikái

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|Befejezett feladatok|feladatok. kész|Darabszám|Összesen|Az összes befejezett feladat száma.|Nincs|
|Sikertelen hívások a feladatok listázásához|feladatok. listJobs. hiba|Darabszám|Összesen|A feladatokat listázó sikertelen hívások száma.|Nincs|
|Nem sikerült létrehozni a metódus Meghívási feladatait|feladatok. createDirectMethodJob. hiba|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikertelen létrehozásának száma.|Nincs|
|A kettős frissítési feladatok sikertelen létrehozása|feladatok. createTwinUpdateJob. hiba|Darabszám|Összesen|A kettős frissítési feladatok sikertelen létrehozásának száma.|Nincs|
|Sikertelen feladatok megszakítása|feladatok. cancelJob. hiba|Darabszám|Összesen|A feladat megszakítására irányuló sikertelen hívások száma.|Nincs|
|Sikertelen feladatok lekérdezése|feladatok. queryJobs. hiba|Darabszám|Összesen|A lekérdező feladatok összes sikertelen hívásának száma.|Nincs|
|Sikertelen feladatok|feladatok. sikertelen|Darabszám|Összesen|Az összes sikertelen feladat száma.|Nincs|
|Sikeres hívások a feladatok listázásához|feladatok. listJobs. sikeres|Darabszám|Összesen|A feladatok listázására irányuló sikeres hívások száma.|Nincs|
|Metódus-Meghívási feladatok sikeres létrehozása|feladatok. createDirectMethodJob. sikeres|Darabszám|Összesen|A közvetlen metódus meghívásával kapcsolatos feladatok sikeres létrehozásának száma.|Nincs|
|A kettős frissítési feladatok sikeres létrehozása|Jobs. createTwinUpdateJob.<br>sikeres|Darabszám|Összesen|A kettős frissítési feladatok sikeres létrehozásának száma.|Nincs|
|Sikeres feladatok törlése|feladatok. cancelJob. sikeres|Darabszám|Összesen|A feladat megszakítására irányuló sikeres hívások száma.|Nincs|
|Sikeres feladatok lekérdezése|feladatok. queryJobs. sikeres|Darabszám|Összesen|A lekérdezési feladatok összes sikeres hívásának száma.|Nincs|

A **darabszám** **értékkel** rendelkező metrikák esetében érvényes a Total (Sum) összesítés. A minimális, maximális és átlagos összesítések mindig 1 értéket adnak vissza. További információ: [támogatott összesítések](#supported-aggregations).

### <a name="routing-metrics"></a>Útválasztási metrikák

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
| Útválasztási kézbesítési kísérletek (előzetes verzió) |RoutingDeliveries | Darabszám | Összesen |Ez az útválasztási kézbesítési metrika. A méretek segítségével azonosíthatja egy adott végpont vagy egy adott útválasztási forrás kézbesítési állapotát.| Eredmény,<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>Végpontneve<br>*További információ: [metrikus dimenziók](#metric-dimensions)*. |
| A kézbesítési adatmennyiség mérete bájtban (előzetes verzió)|RoutingDataSizeInBytesDelivered| Bájt | Összesen |A IoT Hub által az egyéni végponthoz és a beépített végponthoz irányított bájtok teljes száma. A méretek használatával azonosíthatja az adott végponthoz vagy egy adott útválasztási forráshoz irányított adatméretet.| RoutingSource,<br>EndpointType<br>Végpontneve<br>*További információ: [metrikus dimenziók](#metric-dimensions)*.|
| Útválasztási késés (előzetes verzió) |RoutingDeliveryLatency| Ezredmásodpercben | Átlag |Ez az útválasztási kézbesítés késésének mérőszáma. A méretek segítségével azonosíthatja egy adott végpont késését, vagy egy adott útválasztási forrás esetében.| RoutingSource,<br>EndpointType,<br>Végpontneve<br>*További információ: [metrikus dimenziók](#metric-dimensions)*.|
|Útválasztás: tárolóba szállított Blobok|D2C. endpoints. kimenő. Storage. Blobok|Darabszám|Összesen|A Blobok tárolási végpontokra IoT Hub útvonalának száma.|Nincs|
|Útválasztás: a tárolóba szállított adatmennyiség|D2C. endpoints. kimenő. Storage. Bytes|Bájt|Összesen|A tárolási végpontoknak továbbított adatmennyiség (bájt) IoT Hub útválasztás.|Nincs|
|Útválasztás: az Event hub üzenetének késése|D2C. endpoints. késleltetés. eventHubs|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) az Event hub típusú egyéni végpontokra. Ez nem tartalmazza a beépített végponthoz (eseményekhez) tartozó üzenetek útvonalait.|Nincs|
|Útválasztás: Service Bus üzenetsor üzenet-késése|D2C. endpoints. késleltetés. serviceBusQueues|Ezredmásodpercben|Átlag|A Service Bus üzenetsor-végpontba beérkező üzenetek IoT Hub és az üzenetbe való belépések közötti átlagos késés (ezredmásodpercben).|Nincs|
|Útválasztás: Service Bus témakör üzenetének késése|D2C. endpoints. késleltetés. serviceBusTopics|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben) Service Bus témakör-végpontba.|Nincs|
|Útválasztás: üzenetek/események üzenetének késése|D2C. endpoints. késleltetés. beépített. események|Ezredmásodpercben|Átlag|A bejövő végpont (üzenetek/események) és a tartalék útvonal között a IoT Hub és az üzenetek bejövő üzenetbe való belépésének átlagos késése (ezredmásodpercben).|Nincs|
|Útválasztás: üzenetek késése a tároláshoz|D2C. endpoints. látencia. Storage|Ezredmásodpercben|Átlag|A IoT Hub és az üzenetek bejövő elérésének átlagos késése (ezredmásodpercben) a tárolási végpontba.|Nincs|
|Útválasztás: az Event hub számára továbbított üzenetek|D2C. endpoints. kimenő. eventHubs|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket az Event hub típusú egyéni végpontokra. Ez nem tartalmazza a beépített végponthoz (eseményekhez) tartozó üzenetek útvonalait.|Nincs|
|Útválasztás: Service Bus üzenetsor számára továbbított üzenetek|D2C. endpoints. kimenő. serviceBusQueues|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus üzenetsor-végpontoknak.|Nincs|
|Útválasztás: Service Bus témakörbe küldött üzenetek|D2C. endpoints. kimenő. serviceBusTopics|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket Service Bus témakör-végpontoknak.|Nincs|
|Útválasztás: tartalékként továbbított üzenetek|D2C. telemetria. kimenő. tartalék|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás a tartalék útvonalhoz társított végpontnak küldött üzeneteket.|Nincs|
|Útválasztás: üzenetek/események küldésére küldött üzenetek|D2C. endpoints. kimenő. beépített. események|Darabszám|Összesen|Azon esetek száma, IoT Hub az Útválasztás sikeresen leküldte az üzeneteket a beépített végpontba (üzenetek/események) és a tartalék útvonalba.|Nincs|
|Útválasztás: a tárolóba küldött üzenetek|D2C. endpoints. kimenő. Storage|Darabszám|Összesen|Azon időpontok száma, IoT Hub az Útválasztás sikeresen küldött üzeneteket a tárolási végpontoknak.|Nincs|
|Útválasztás: telemetria üzenetek kézbesítése|D2C. telemetria. kimenő. sikeres|Darabszám|Összesen|A IoT Hub útválasztást használó összes végponthoz tartozó üzenetek sikeres kézbesítésének száma. Ha egy üzenet több végponthoz van irányítva, ez az érték eggyel nő minden sikeres kézbesítésnél. Ha egy üzenet többször is ugyanarra a végpontra érkezik, az érték minden sikeres kézbesítés esetén eggyel nő.|Nincs|
|Útválasztás: telemetria üzenetek elvetve |D2C. telemetria. kimenő. eldobott|Darabszám|Összesen|A letiltott végpontok miatt IoT Hub útválasztás által eldobott üzenetek száma. Ez az érték nem számítja ki a tartalék útvonalra küldött üzeneteket, mert az eldobott üzenetek nem kerülnek oda.|Nincs|
|Útválasztás: Inkompatibilis telemetria-üzenetek|D2C. telemetria. kimenő. érvénytelen|Darabszám|Összesen|Azon időpontok száma IoT Hub az Útválasztás nem tudott kézbesíteni üzeneteket, mert nem kompatibilis a végponttal. Egy üzenet nem kompatibilis egy végponttal, amikor az IOT hub megkísérli az üzenetet egy végpontnak kézbesíteni, és nem átmeneti hibával meghiúsul. Érvénytelen üzenetek nem lesznek újrapróbálkozva. Ez az érték nem tartalmazza az újrapróbálkozásokat.|Nincs|
|Útválasztás: árva telemetria üzenetek |D2C. telemetria. kimenő. árva|Darabszám|Összesen|Az üzenetek számának IoT Hub az útválasztása, mert nem feleltek meg egyetlen útválasztási lekérdezésnek sem, ha a tartalék útvonal le van tiltva.|Nincs|

A **darabszám** **értékkel** rendelkező metrikák esetében érvényes a Total (Sum) összesítés. A minimális, maximális és átlagos összesítések mindig 1 értéket adnak vissza. További információ: [támogatott összesítések](#supported-aggregations).

### <a name="twin-query-metrics"></a>Dupla lekérdezési metrika

|Metrika megjelenítendő neve|Metric|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|
|Sikertelen dupla lekérdezések|twinQueries. hiba|Darabszám|Összesen|Az összes sikertelen dupla lekérdezés száma.|Nincs|
|Sikeres Twin-lekérdezések|twinQueries. success|Darabszám|Összesen|Az összes sikeres dupla lekérdezés száma.|Nincs|
|Dupla lekérdezések eredményének mérete|twinQueries.resultSize|Bájt|Átlag|Az összes sikeres dupla lekérdezés eredményének teljes mérete.|Nincs|

A **darabszám** **értékkel** rendelkező metrikák esetében érvényes a Total (Sum) összesítés. A minimális, maximális és átlagos összesítések mindig 1 értéket adnak vissza. További információ: [támogatott összesítések](#supported-aggregations).

## <a name="metric-dimensions"></a>Metrikus méretek

Az Azure IoT Hub a következő, az útválasztási és az Event Grid-metrikákkal társított dimenziókkal rendelkezik.

|Dimenzió neve | Description|
|---|---|
||
|**Végpontneve**| A végpont neve.|
|**EndpointType**|A következők egyike: **eventHubs**, **serviceBusQueues**, **cosmosDB**, **serviceBusTopics**. **beépített** vagy **blobStorage**.|
|**EventType**| A következő Event Grid eseménytípus egyike: **Microsoft. Devices. DeviceCreated**. **Microsoft. Devices. DeviceDeleted**, **Microsoft. Devices. DeviceConnected**, **Microsoft. Devices. DeviceDisconnected**, vagy **Microsoft. Devices. DeviceTelemetry**. További információ: [eseménytípus](iot-hub-event-grid.md#event-types).|
|**FailureReasonCategory**| A következők egyike: **érvénytelen**, **eldobott**, **árva** vagy **Null**.|
|**Eredmény**| **Sikeres** vagy **sikertelen**.|
|**RoutingSource**| Eszköz üzenetei<br>Kettős változási események<br>Eszközök életciklusával kapcsolatos események|

További információ a metrikus dimenziókkal kapcsolatban: [többdimenziós mérőszámok](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

## <a name="resource-logs"></a>Erőforrásnaplók

Ez a szakasz felsorolja az Azure IoT Hubhoz gyűjtött összes erőforrás-napló kategóriáját és sémáját. Az összes IoT Hub napló erőforrás-szolgáltatója és típusa [Microsoft. Devices/IotHubs](../azure-monitor/essentials/resource-logs-categories.md#microsoftdevicesiothubs).

**A szakasz témakörei**

- [Az Azure IoT Hub adatreferenciájának monitorozása](#monitoring-azure-iot-hub-data-reference)
  - [Metrikák](#metrics)
    - [Támogatott összesítések](#supported-aggregations)
    - [Felhőből az eszközre irányuló parancsok mérőszámai](#cloud-to-device-command-metrics)
    - [Felhőből az eszközre irányuló közvetlen módszerek mérőszámai](#cloud-to-device-direct-methods-metrics)
    - [Felhőből az eszközre kettős műveleti mérőszámok](#cloud-to-device-twin-operations-metrics)
    - [Konfigurációk metrikái](#configurations-metrics)
    - [Napi kvóta mérőszámai](#daily-quota-metrics)
    - [Eszköz metrikái](#device-metrics)
    - [Eszköz telemetria metrikái](#device-telemetry-metrics)
    - [Eszközről a felhőbe irányuló Twin Operations metrikák](#device-to-cloud-twin-operations-metrics)
    - [Event Grid-metrikák](#event-grid-metrics)
    - [Feladatok metrikái](#jobs-metrics)
    - [Útválasztási metrikák](#routing-metrics)
    - [Dupla lekérdezési metrika](#twin-query-metrics)
  - [Metrikus méretek](#metric-dimensions)
  - [Erőforrásnaplók](#resource-logs)
    - [Kapcsolatok](#connections)
    - [Eszköz telemetria](#device-telemetry)
    - [Felhő–eszköz irányú parancsok](#cloud-to-device-commands)
    - [Eszköz-identitási műveletek](#device-identity-operations)
    - [Fájlfeltöltési műveletek](#file-upload-operations)
    - [Útvonalak](#routes)
    - [Az eszközről a felhőbe irányuló kettős művelet](#device-to-cloud-twin-operations)
    - [A felhőből az eszközre irányuló kettős műveletek](#cloud-to-device-twin-operations)
    - [Dupla lekérdezés](#twin-queries)
    - [Feladatműveletek](#jobs-operations)
    - [Közvetlen metódusok](#direct-methods)
    - [Elosztott nyomkövetés (előzetes verzió)](#distributed-tracing-preview)
      - [IoT Hub D2C (eszközről a felhőbe irányuló) naplók](#iot-hub-d2c-device-to-cloud-logs)
      - [Bejövő IoT Hubi naplók](#iot-hub-ingress-logs)
      - [IoT Hub kimenő naplók](#iot-hub-egress-logs)
    - [Konfigurációk](#configurations)
    - [Eszköz streamek (előzetes verzió)](#device-streams-preview)
  - [Azure Monitor naplók táblái](#azure-monitor-logs-tables)
  - [Lásd még:](#see-also)

### <a name="connections"></a>Kapcsolatok

A kapcsolatok kategória nyomon követi az eszköz csatlakoztatását, és leválasztja az eseményeket egy IoT-hubhoz, valamint a hibákat. Ez a kategória hasznos lehet a jogosulatlan kapcsolódási kísérletek azonosításához, illetve az eszközökhöz való csatlakozás elvesztésével kapcsolatos riasztásokhoz.

> [!NOTE]
> Az eszközök megbízható kapcsolatok állapotának ellenõrzéséhez az [eszköz szívverését](iot-hub-devguide-identity-registry.md#device-heartbeat)kell megadnia.

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>Eszköz telemetria

Az eszköz telemetria kategóriája nyomon követi az IoT hub-ban előforduló hibákat, és a telemetria-folyamathoz kapcsolódik. Ez a kategória olyan hibákat tartalmaz, amelyek a telemetria-események (például a szabályozás) és a telemetria-események (például a jogosulatlan olvasók) küldésekor fordulnak elő. Ez a kategória nem képes az eszközön futó kód által okozott hibák megfogására.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>Felhő–eszköz irányú parancsok

A felhőből az eszközre irányuló parancsok kategóriája nyomon követi az IoT-központban előforduló hibákat, és a felhőből az eszközre irányuló üzenet folyamatához kapcsolódik. Ez a kategória olyan hibákat tartalmaz, amelyek a következő esetekben jelentkeznek:

* A felhőből az eszközre irányuló üzenetek küldése (például jogosulatlan feladói hibák),
* A felhőből az eszközre irányuló üzenetek fogadása (például a kézbesítések száma túllépte a hibákat) és
* A felhőből az eszközre irányuló üzenetek visszajelzésének fogadása (például a visszajelzések lejárt hibái).

Ez a kategória nem fog hibákat észlelni, ha a felhőből az eszközre irányuló üzenet sikeresen kézbesítésre kerül, de az eszköz nem megfelelően kezeli őket.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>Eszköz-identitási műveletek

Az Eszközállapot-üzemeltetési kategória azokat a hibákat követi, amelyek akkor jelentkeznek, amikor megpróbál létrehozni, frissíteni vagy törölni egy bejegyzést az IoT hub Identity registryben. A kategória követése hasznos lehet a kiépítési forgatókönyvek esetében.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>Fájlfeltöltési műveletek

A fájlfeltöltés kategóriája nyomon követi az IoT-központban előforduló hibákat, és a fájlfeltöltés funkcióhoz kapcsolódik. Ez a kategória a következőket tartalmazza:

* A SAS URI-val kapcsolatos hibák, például amikor lejárnak, mielőtt egy eszköz értesíti a befejezett feltöltési központ központi telepítéséről.

* Az eszköz által jelentett feltöltések sikertelenek.

* Hibák, amelyek akkor jelentkeznek, ha egy fájl nem található a tárolóban IoT Hub értesítési üzenet létrehozásakor.

Ez a kategória nem tudja felfogni azokat a hibákat, amelyek közvetlenül bekövetkeznek, miközben az eszköz feltölt egy fájlt a tárolóba.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>Útvonalak

Az [üzenet-útválasztási](./iot-hub-devguide-messages-d2c.md) kategória nyomon követi az üzenetek útvonalának kiértékelése és a végpont állapota során az IoT hub által észlelt hibákat. Ez a kategória olyan eseményeket tartalmaz, mint például:

* Egy szabály "nem definiált" értékre értékeli ki a következőt:
* IoT Hub a végpontot Holtként jelöli meg, vagy
* A végponttól érkezett hibák.

Ez a kategória nem tartalmaz konkrét hibákat az üzenetekről (például az eszköz-szabályozási hibákról), amelyek az "eszköz telemetria" kategóriában vannak bejelentve.

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

További részletek az útválasztási erőforrás-naplókról:

* [Útválasztási erőforrás naplójának hibakódok listája](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Útválasztási erőforrás-naplók operationNames listája](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>Az eszközről a felhőbe irányuló kettős művelet

Az eszközről a felhőbe irányuló kettős műveletek kategóriája az eszköz által kezdeményezett eseményeket követi nyomon az eszközökön. Ezek a műveletek magukban foglalhatják a Get Twin, a frissített jelentett tulajdonságokat, és előfizethetnek a kívánt tulajdonságokra.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>A felhőből az eszközre irányuló kettős műveletek

A felhő – eszköz kettős műveletek kategóriája nyomon követi a szolgáltatás által kezdeményezett eseményeket az eszköz Twins-on. Ezek a műveletek magukban foglalhatják a Get Twin, a Update vagy a Replace címkéket, valamint a kívánt tulajdonságok frissítését vagy cseréjét.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>Dupla lekérdezés

A Twin querys kategória jelentést készít a felhőben kezdeményezett Device Twins-lekérdezési kérelmekről.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>Feladatműveletek

A feladatok műveleti kategóriánként jelentéseket készítenek az eszköz-ikrek frissítéséhez vagy közvetlen metódusok meghívásához több eszközön. Ezeket a kérelmeket a felhőben kezdeményezték.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>Közvetlen metódusok

A közvetlen metódusok kategória az egyes eszközökre küldött kérelem-válasz interakciókat követi nyomon. Ezeket a kérelmeket a felhőben kezdeményezték.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>Elosztott nyomkövetés (előzetes verzió)

Az elosztott nyomkövetési kategória a nyomkövetési környezet fejlécét tartalmazó üzenetek korrelációs azonosítóit követi nyomon. A naplók teljes körű engedélyezéséhez az ügyféloldali kódot a következő elemzéssel kell frissíteni, [és diagnosztizálni kell a IoT-alkalmazásokat végpontok közötti IoT hub elosztott nyomkövetéssel (előzetes verzió)](iot-hub-distributed-tracing.md).

Vegye figyelembe, hogy `correlationId` a W3C- [nyomkövetési kontextusra](https://github.com/w3c/trace-context) vonatkozó javaslatnak megfelel, ahol a tartalmaz egy `trace-id` és egy `span-id` .

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C (eszközről a felhőbe irányuló) naplók

IoT Hub rögzíti ezt a naplót, ha egy érvényes nyomkövetési tulajdonságokat tartalmazó üzenet érkezik a IoT Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Itt `durationMs` nem számítja ki a számítást, mert előfordulhat, hogy a IoT hub órája nem szinkronizálható az eszköz órájával, így az időtartam kiszámítása félrevezető lehet. Az `properties` eszközről a felhőbe irányuló késések rögzítése érdekében a szakasz időbélyegei alapján javasolt a logikát írni.

| Tulajdonság | Típus | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Egész szám | Az eszközről a felhőbe irányuló üzenet mérete bájtban |
| **deviceId** | ASCII 7 bites alfanumerikus karakterek karakterlánca | Az eszköz identitása |
| **callerLocalTimeUtc** | UTC timestamp | Az üzenet létrehozásának ideje, amelyet az eszköz helyi órája jelentett. |
| **calleeLocalTimeUtc** | UTC timestamp | A IoT Hub átjárójának a IoT Hub szolgáltatás által jelzett óra által jelentett időpontja |

#### <a name="iot-hub-ingress-logs"></a>Bejövő IoT Hubi naplók

IoT Hub rögzíti ezt a naplót, ha az érvényes nyomkövetési tulajdonságokat tartalmazó üzenet belső vagy beépített Event hub-ba ír.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

A `properties` szakaszban ez a napló további információkat tartalmaz az üzenetek beérkezéséről.

| Tulajdonság | Típus | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Sztring | Igaz vagy hamis érték esetén azt jelzi, hogy engedélyezve van-e az üzenet-útválasztás a IoT Hub |
| **parentSpanId** | Sztring | A fölérendelt üzenet [span-azonosítója](https://w3c.github.io/trace-context/#parent-id) , amely ebben az esetben a D2C-üzenet nyomkövetése lenne |

#### <a name="iot-hub-egress-logs"></a>IoT Hub kimenő naplók

IoT Hub rögzíti ezt a naplót, ha az [Útválasztás](iot-hub-devguide-messages-d2c.md) engedélyezve van, és az üzenet egy [végpontba](iot-hub-devguide-endpoints.md)íródik. Ha az Útválasztás nincs engedélyezve, IoT Hub nem rögzíti ezt a naplót.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

A `properties` szakaszban ez a napló további információkat tartalmaz az üzenetek beérkezéséről.

| Tulajdonság | Típus | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **Végpontneve** | Sztring | Az útválasztási végpont neve |
| **endpointType** | Sztring | Az útválasztási végpont típusa |
| **parentSpanId** | Sztring | A fölérendelt üzenet [span-azonosítója](https://w3c.github.io/trace-context/#parent-id) , amely a IoT hub bejövő üzenet nyomkövetése lenne ebben az esetben |

### <a name="configurations"></a>Konfigurációk

IoT Hub konfigurációs naplók nyomon követik az automatikus Eszközkezelő szolgáltatáshoz tartozó eseményeket és hibákat.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Eszköz streamek (előzetes verzió)

Az eszköz Streams kategória nyomon követi az egyes eszközökre küldött kérelem-válasz interakciókat.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Azure Monitor naplók táblái
<!-- REQUIRED. Please keep heading in this order -->

Ez a szakasz az összes olyan Azure Monitor naplóra vonatkozik, amely az Azure-IoT Hub releváns Kusto, és Log Analytics számára elérhető. A táblák listáját és a IoT Hub erőforrástípus további információit a következő témakörben találhatja meg: [IoT hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) a Azure monitor naplók táblázatának dokumentációjában.

Az összes Azure Monitor naplóra/Log Analytics táblázatra vonatkozó hivatkozásért tekintse meg a [Azure monitor log Table-referenciát](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="see-also"></a>Lásd még:

* Lásd: az Azure- [IoT hub](monitor-iot-hub.md) figyelése az azure-IoT hub figyelésének leírásához.
* Az Azure-erőforrások figyelésével kapcsolatos részletekért lásd: az [Azure-erőforrások figyelése Azure monitorokkal](../azure-monitor/essentials/monitor-azure-resource.md) .
