---
title: Adatok exportálása az Azure IoT Centralból (örökölt) | Microsoft Docs
description: Adatok exportálása az Azure IoT Central alkalmazásból az Azure-ba Event Hubs, Azure Service Bus és az Azure Blob Storage-ba
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: d4c099d29a843b4c354ffb218887dc7ffab51771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98065439"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>IoT-adatexportálás a Felhőbeli célhelyekre az adatexportálás használatával (örökölt)

> [!Note]
> Ez a cikk a IoT Central korábbi adatexportálási funkcióit ismerteti.
>
> - További információ a legújabb adatexportálási funkciókról: [IoT-adatok exportálása a Felhőbeli célhelyekre adatexportálás használatával](./howto-export-data.md).
> - Az előzetes verziójú adatexportálás és az örökölt adatexportálási funkciók közötti különbségekről az [összehasonlítási táblázatban](./howto-export-data.md#comparison-of-legacy-data-export-and-data-export)talál további információt.

Ez a cikk az Azure IoT Central adatexportálási funkciójának használatát ismerteti. Ezzel a szolgáltatással folyamatosan exportálhatja az adatait az **azure Event Hubsba**, **Azure Service Busba** vagy **Azure Blob Storage** -példányba. Az adatexportálás JSON-formátumot használ, és tartalmazhatja a telemetria, az eszköz adatait és az eszköz sablonjának adatait. Az exportált adatértékek használata:

- Meleg elérésű elemzések és elemzések. Ez a beállítás magában foglalja az egyéni szabályok beindítását a Azure Stream Analyticsban, az egyéni munkafolyamatok aktiválását a Azure Logic Appsban, vagy átadja a Azure Functions át.
- A Microsoft Power BI-ban a ritka elérésű elemzések, például a Azure Machine Learning vagy a hosszú távú trendek elemzése során betanítási modellek.

> [!Note]
> Amikor bekapcsolja az adatexportálást, a rendszer csak az adott pillanattól kezdve kapja meg az adatait. Jelenleg nem lehet lekérni az adatexportálást olyan időpontra, amikor az adatexportálás ki lett kapcsolva. Több korábbi adat megtartásához kapcsolja be a korai adatexportálást.

## <a name="prerequisites"></a>Előfeltételek

A IoT Central alkalmazásban rendszergazdának kell lennie, vagy az adatexportálási engedélyekkel kell rendelkeznie.

## <a name="set-up-export-destination"></a>Exportálás célhelyének beállítása

Az exportálási célhelynek az adatexportálás konfigurálása előtt léteznie kell.

### <a name="create-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Ha nem rendelkezik meglévő Event Hubs-névtérrel az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Event Hubs névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.EventHub). További információt az [Azure Event Hubs dokumentációjában](../../event-hubs/event-hubs-create.md)olvashat.

2. Válasszon előfizetést. Az olyan egyéb előfizetésekben is exportálhat adatexportálást, amelyek nem ugyanabban az előfizetésben vannak, mint a IoT Central alkalmazás. Ebben az esetben kapcsolati sztringet használ.

3. Hozzon létre egy Event hubot a Event Hubs névtérben. Nyissa meg a névteret, és a felül található **+ Event hub** elemet választva hozzon létre egy Event hub-példányt.

### <a name="create-service-bus-namespace"></a>Service Bus névtér létrehozása

Ha nem rendelkezik meglévő Service Bus-névtérrel az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Service Bus névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). [Azure Service Bus dokumentációban](../../service-bus-messaging/service-bus-create-namespace-portal.md)bővebben is olvashat.
2. Válasszon előfizetést. Az olyan egyéb előfizetésekben is exportálhat adatexportálást, amelyek nem ugyanabban az előfizetésben vannak, mint a IoT Central alkalmazás. Ebben az esetben kapcsolati sztringet használ.

3. Az exportáláshoz használandó üzenetsor vagy témakör létrehozásához nyissa meg a Service Bus névteret, és válassza a **+ üzenetsor** vagy a **+ témakör** lehetőséget.

Ha a Service Bus exportálás célhelyként választja, akkor a várólisták és a témakörök nem rendelkezhetnek a munkamenetek és az ismétlődő észlelések engedélyezésével. Ha ezek bármelyike engedélyezve van, néhány üzenet nem érkezik meg a várólistán vagy a témakörben.

### <a name="create-storage-account"></a>Storage-fiók létrehozása

Ha nem rendelkezik meglévő Azure Storage-fiókkal az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Storage-fiókot a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). További információ: új [Azure Blob Storage-fiókok](../../storage/blobs/storage-quickstart-blobs-portal.md) vagy [Azure Data Lake Storage v2 Storage-fiókok](../../storage/common/storage-account-create.md)létrehozása. Az adatexportálás csak a blokk blobokat támogató Storage-fiókokba tud írni. A következő lista az ismert kompatibilis Storage-fiókok típusait mutatja be:

    |Teljesítményszint|Fiók típusa|
    |-|-|
    |Standard|általános célú v2|
    |Standard|általános célú v1|
    |Standard|Blob Storage|
    |Prémium|BLOB Storage letiltása|

2. Hozzon létre egy tárolót a Storage-fiókban. Nyissa meg a Storage-fiókját. A **blob szolgáltatás** alatt válassza a **Tallózás Blobok** lehetőséget. Egy új tároló létrehozásához kattintson a felül található **+ tároló** elemre.

## <a name="set-up-data-export"></a>Az adatexportálás beállítása

Most, hogy van egy célhelye az adatexportáláshoz, az alábbi lépéseket követve állíthatja be az adatexportálást.

1. Jelentkezzen be IoT Central alkalmazásba.

2. A bal oldali panelen válassza az **adatexportálás** elemet.

    > [!Tip]
    > Ha nem látja az **adatexportálást** a bal oldali ablaktáblán, akkor nincs engedélye az adatexportálás konfigurálására az alkalmazásban. Az adatexportálás beállításához forduljon a rendszergazdához.

3. Kattintson az **+ új** gombra. Válassza ki az **azure blob Storage**, az **azure Event Hubs**, **Azure Service Bus üzenetsor** vagy a **Azure Service Bus témakör** egyikét az Exportálás céljának megfelelően. Az alkalmazáson keresztüli exportálások maximális száma öt.

4. Adja meg az Exportálás nevét. A legördülő listában válassza ki a **névteret**, vagy **adjon meg egy kapcsolatok karakterláncot**.

    - A IoT Central alkalmazással megegyező előfizetésben csak a Storage-fiókok, a Event Hubs névterek és a Service Bus névterek láthatók. Ha az előfizetésen kívüli célhelyre szeretne exportálni, válassza **az adja meg a kapcsolati karakterláncot** , és tekintse meg a 6. lépést.
    - Az ingyenes díjszabási csomag használatával létrehozott alkalmazások esetében az adatexportálás konfigurálása egyetlen módon történik a kapcsolódási karakterláncon keresztül. Az ingyenes díjszabási csomag alkalmazásai nem rendelkeznek társított Azure-előfizetéssel.

    ![Új Event hub létrehozása](media/howto-export-data-legacy/export-event-hub.png)

5. Válassza ki az Event hub, a várólista, a témakör vagy a tároló elemet a legördülő listából.

6. Választható Ha a **kapcsolódási karakterlánc megadása** lehetőséget választotta, a rendszer egy új mezőt jelenít meg a kapcsolódási karakterlánc beillesztéséhez. A következőhöz tartozó kapcsolódási karakterlánc lekérése:

    - Event Hubs vagy Service Bus keresse meg a névteret a Azure Portalban:
        - A teljes névtérhez tartozó kapcsolódási karakterlánc használata:
            1. A **Beállítások** területen válassza a **megosztott elérési szabályzatok** elemet.
            2. Hozzon létre egy új kulcsot, vagy válasszon olyan meglévő kulcsot, amely rendelkezik **küldési** engedéllyel.
            3. Az elsődleges vagy a másodlagos kapcsolatok karakterláncának másolása
        - Ha a kapcsolati karakterláncot egy adott Event hub-példányhoz vagy Service Bus üzenetsor vagy témakörhöz szeretné használni, lépjen az **entitások > Event Hubs** vagy **entitások > várólisták** vagy **entitások > témakörök** elemre. Válasszon egy adott példányt, és kövesse a fenti lépéseket a kapcsolódási karakterlánc beszerzéséhez.
    - Storage-fiók, nyissa meg a Azure Portal Storage-fiókját:
        - Csak a teljes Storage-fiókhoz tartozó kapcsolatok karakterláncai támogatottak. Az egyetlen tárolóra kiterjedő kapcsolódási karakterláncok nem támogatottak.
          1. A **Beállítások** területen válassza a **hozzáférési kulcsok** elemet.
          2. Másolja a key1-vagy a key2-kapcsolatok karakterláncát

    Illessze be a kapcsolatok karakterláncát. Írja be a példányt vagy a kis-és nagybetűket megkülönböztető **tároló nevét**.

7. Az **exportálni** kívánt adat területen válassza ki az exportálandó adattípusokat **, ha a** típust be értékre állítja.

8. Az adatexportálás bekapcsolásához ellenőrizze, **hogy be van**-e kapcsolva az **engedélyezve** váltógomb. Kattintson a **Mentés** gombra.

9. Néhány perc elteltével az adatai megjelennek a kiválasztott célhelyen.

## <a name="export-contents-and-format"></a>Tartalom és formátum exportálása

Az exportált telemetria-adatok teljes egészében tartalmazzák az eszköz által IoT Central küldött üzenetet, nem csak a telemetria értékeket. Az exportált eszközökhöz tartozó adatok az összes eszköz tulajdonságainak és metaadatainak változásait tartalmazzák, az exportált eszközök pedig az összes eszközosztály változásait tartalmazzák.

Event Hubs és Service Bus esetében az adatexportálás közel valós idejű. Az adatobjektum a `body` tulajdonságban van, és JSON formátumú. Példákat alább talál.

A blob Storage esetében percenként egyszer exportálja az adatmennyiséget, és minden olyan fájlt, amely a legutóbbi exportált fájl óta módosul. Az exportált adatfájlok JSON formátumú három mappába kerülnek. A Storage-fiók alapértelmezett elérési útjai a következők:

- Telemetria: _{Container}/{app-ID}/telemetry/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Eszközök: _{Container}/{app-ID}/Devices/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Eszközök sablonjai: _{Container}/{app-ID}/deviceTemplates/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_

Az exportált fájlok tallózásához a Azure Portalban navigáljon a fájlhoz, és válassza a **blob szerkesztése** lapot.

## <a name="telemetry"></a>Telemetria

Event Hubs és Service Bus esetén a IoT Central gyorsan exportál egy új üzenetet, miután megkapta az üzenetet az eszközről. Minden exportált üzenet tartalmazza az eszköz JSON formátumban küldött teljes üzenetét.

A blob Storage esetében az üzenetek kötegbe kerülnek, és percenként egyszer lesznek exportálva. Az exportált fájlok ugyanazt a formátumot használják, mint a blob Storage-ba [IoT hub üzenet-útválasztás](../../iot-hub/tutorial-routing.md) által exportált üzenet-fájlok.

> [!NOTE]
> BLOB Storage esetén győződjön meg arról, hogy az eszközök olyan üzeneteket küldenek, amelyek rendelkeznek `contentType: application/JSON` és `contentEncoding:utf-8` (vagy `utf-16` `utf-32` ). Példaként tekintse meg a [IoT hub dokumentációját](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) .

A telemetria küldő eszközt az eszköz azonosítója jelöli (lásd a következő részeket). Az eszközök nevének beszerzéséhez exportálja az eszközöket, és korrelálja az egyes üzeneteket az **connectionDeviceId** , amely megfelel az eszköz **deviceId** -beli értékének.

Az alábbi példa egy Event hub vagy Service Bus üzenetsor vagy témakör által fogadott üzenetet jelenít meg:

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

Ez az üzenet nem tartalmazza a küldő eszköz AZONOSÍTÓját.

Ha Azure Stream Analytics lekérdezésben lévő üzenet adatait szeretné lekérni, használja a [GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue) függvényt. Példaként tekintse meg a lekérdezést az [Azure IoT Central kiterjesztése egyéni szabályokkal stream Analytics, Azure functions és SendGrid használatával](./howto-create-custom-rules.md).

Az eszköz AZONOSÍTÓjának lekéréséhez Azure Databricks vagy Apache Spark munkaterületen használja a [systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md). Példaként tekintse meg a Databricks munkaterületet az [Azure IoT Central kiterjesztése egyéni elemzéssel az Azure Databricks használatával](./howto-create-custom-analytics.md).

Az alábbi példa egy blob Storage-ba exportált rekordot mutat be:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Eszközök

A pillanatképben szereplő minden üzenet vagy rekord az eszköz és az eszköz és a felhő tulajdonságai egy vagy több módosítását jelöli az utolsó exportált üzenet óta. Az üzenet tartalmazza a következőket:

- `id` az eszköz IoT Central
- `displayName` az eszköz
- Eszköz sablonjának azonosítója a `instanceOf`
- `simulated` jelző, igaz, ha az eszköz szimulált eszköz
- `provisioned` jelző, igaz, ha az eszköz ki lett építve
- `approved` jelző, igaz, ha az eszköz jóvá lett hagyva az adatküldéshez
- Tulajdonságértékek
- `properties` az eszköz és a felhő tulajdonságainak értékeit is beleértve

A törölt eszközök nincsenek exportálva. Jelenleg nincsenek mutatók a törölt eszközök exportált üzeneteiben.

Event Hubs és Service Bus esetében IoT Central az eszköz adatait tartalmazó üzeneteket küld az Event hub-nak vagy a Service Bus üzenetsor vagy a témakör közelében, közel valós időben.

A blob Storage esetében az utolsó írás óta minden változást tartalmazó új pillanatkép percenként lesz exportálva.

Az alábbi példa egy Event hub-vagy Service Bus-üzenetsor vagy-témakör eszközeit és tulajdonságait mutatja be:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Ez a pillanatkép egy példaként szolgáló üzenet, amely megjeleníti az eszközöket és a tulajdonságokat a blob Storage-ban. Az exportált fájlok rekordokban egyetlen sort tartalmaznak.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Eszközsablonok

Minden üzenet-vagy pillanatkép-rekord egy közzétett sablon egy vagy több módosítását jelöli a legutóbbi exportált üzenet óta. Az egyes üzenetekben vagy rekordokban küldött információk a következők:

- `id` azon eszköz sablonja, amely megfelel a `instanceOf` fenti eszközök streamnek
- `displayName` az eszköz sablonja
- Az eszköz `capabilityModel` , beleértve a saját `interfaces` , a telemetria, a tulajdonságok és a parancsok definícióját
- `cloudProperties` definíciók
- Felülbírálások és kezdeti értékek, beágyazott `capabilityModel`

A törölt eszközöket a rendszer nem exportálja. Jelenleg nincsenek mutatók a törölt eszközök sablonjaihoz tartozó exportált üzenetekben.

A Event Hubs és a Service Bus esetében IoT Central az eszköz sablonjának adatait tartalmazó üzeneteket küld az Event hub-nak vagy a Service Bus üzenetsor vagy témakör számára közel valós időben.

A blob Storage esetében az utolsó írás óta minden változást tartalmazó új pillanatkép percenként lesz exportálva.

Ez a példa egy üzenetet jelenít meg az Event hub vagy Service Bus üzenetsor vagy témakör eszköz sablonjainak adatait illetően:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Ez a példa egy olyan üzenetet mutat be, amely a blob Storage-ban lévő eszköz-és tulajdonságokat tartalmazza. Az exportált fájlok rekordokban egyetlen sort tartalmaznak.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>Adatformátum-változási Megjegyzés

> [!Note]
> Ez a változás nem érinti a telemetria adatfolyam-adatformátumát. Csak az eszközök és az eszközökön tárolt adatfolyamok vannak hatással.

Ha az előnézeti alkalmazásban már van egy meglévő adatexportálás az *eszközök* és az *eszköz sablonjainak* bekapcsolásával, frissítse az exportálást **2020. június 30-ig**. Ez a követelmény az Azure Blob Storage-ba, az Azure Event Hubs-ra és a Azure Service Bus-re való exportálásra vonatkozik.

A 2020. február 3-án kezdődően az eszközökön és az eszközök sablonjain lévő összes új Exportálás a fent ismertetett adatformátummal fog rendelkezni. Az ezen dátum előtt létrehozott összes exportálás a régi adatformátumban, 2020. június 30-ig marad, ekkor a rendszer automatikusan áttelepíti ezeket az exportálásokat az új adatformátumba. Az új adatformátum megegyezik a IoT Central nyilvános API-ban található [eszköz](/rest/api/iotcentral/devices/get), [eszköz tulajdonság](/rest/api/iotcentral/devices/getproperties), [eszköz felhő tulajdonság](/rest/api/iotcentral/devices/getcloudproperties)és [eszköz sablon](/rest/api/iotcentral/devicetemplates/get) objektumokkal.

Az **eszközök** esetében a régi adatformátum és az új adatformátum közötti jelentős különbségek a következők:
- `@id` az eszköz eltávolításakor a `deviceId` rendszer átnevezi `id` 
- `provisioned` a jelző megjelenik az eszköz kiépítési állapotának leírásához.
- `approved` a jelző megjelenik az eszköz jóváhagyási állapotának leírásához.
- `properties` az eszköz és a felhő tulajdonságait is beleértve, a nyilvános API-ban lévő entitásokra illeszkedik

Az **eszközök sablonjai** esetében a régi adatformátum és az új adatformátum közötti jelentős különbségek a következők:

- `@id` az eszköz sablonjának neve: `id`
- `@type` a rendszer átnevezi az eszköz sablonját `types` , és mostantól egy tömb

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Eszközök (a formátum a 2020. február 3. után elavult)

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Eszközök sablonjai (a formátum a 2020. február 3. után elavult)

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Következő lépések

Most, hogy tudja, hogyan exportálhatja adatait az Azure Event Hubsba, Azure Service Busba és az Azure Blob Storage-ba, folytassa a következő lépéssel:

> [!div class="nextstepaction"]
> [Egyéni elemzések futtatása a Databricks](./howto-create-custom-analytics.md)