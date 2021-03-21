---
title: Adatok exportálása az Azure IoT Centralból | Microsoft Docs
description: Az új adatexportálás használata a IoT-adatainak az Azure-ba és az egyéni Felhőbeli célhelyekre való exportálásához.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: 7152012c7c4a342c7491e5f8b835eaede4269c4c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100522614"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>IoT-adatexportálás a Felhőbeli célhelyekre az adatexportálás használatával

> [!Note]
> Ez a cikk a IoT Central adatexportálási funkcióit ismerteti. Az örökölt adatexportálási funkciókkal kapcsolatos információkért lásd: [IoT-adatok exportálása a Felhőbeli célhelyekre adatexportálás (örökölt) használatával](./howto-export-data-legacy.md).

Ez a cikk azt ismerteti, hogyan használható az új adatexportálási funkció az Azure IoT Centralban. Ezzel a szolgáltatással folyamatosan exportálhatja a szűrt és a dúsított IoT-adatait a IoT Central alkalmazásból. Az adatexportálás leküldi a közel valós idejű változásokat a felhőalapú megoldás más részeire a meleg elérésű elemzések, az elemzések és a tárolás érdekében.

Megteheti például a következőt:

- A telemetria-és a tulajdonság-módosítások folyamatos exportálása JSON formátumban, közel valós időben.
- Az adatstreamek szűrésével exportálhatja az egyéni feltételekkel egyező adatforgalmat.
- Az adatstreamek az eszköz egyéni értékeivel és tulajdonságértékek gazdagítása.
- Az Azure Event Hubs, a Azure Service Bus, az Azure Blob Storage és a webhook-végpontok számára küldje el az összes olyan célhelyet.

> [!Tip]
> Amikor bekapcsolja az adatexportálást, a rendszer csak az adott pillanattól kezdve kapja meg az adatait. Jelenleg nem lehet lekérni az adatexportálást olyan időpontra, amikor az adatexportálás ki lett kapcsolva. Több korábbi adat megtartásához kapcsolja be a korai adatexportálást.

## <a name="prerequisites"></a>Előfeltételek

Az adatexportálási funkciók használatához [v3 alkalmazás](howto-get-app-info.md)szükséges, és rendelkeznie kell az [adatexportálási](howto-manage-users-roles.md) engedéllyel.

Ha v2-alkalmazással rendelkezik, tekintse [meg a v2 IoT Central alkalmazás áttelepítését v3 verzióra](howto-migrate.md)című témakört.

## <a name="set-up-export-destination"></a>Exportálás célhelyének beállítása

Az exportálási célhelynek az adatexportálás konfigurálása előtt léteznie kell. Jelenleg a következő típusú célhelyek érhetők el:

- Azure Event Hubs
- Azure Service Bus-üzenetsor
- Azure Service Bus-témakör
- Azure Blob Storage
- Webhook

### <a name="create-an-event-hubs-destination"></a>Event Hubs célhely létrehozása

Ha nem rendelkezik meglévő Event Hubs-névtérrel az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Event Hubs névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.EventHub). További információt az [Azure Event Hubs dokumentációjában](../../event-hubs/event-hubs-create.md)olvashat.

1. Hozzon létre egy Event hubot a Event Hubs névtérben. Nyissa meg a névteret, és a felül található **+ Event hub** elemet választva hozzon létre egy Event hub-példányt.

1. Hozzon létre egy kulcsot, amelyet akkor kell használni, ha az adatexportálást IoT Centralban állítja be:

    - Válassza ki a létrehozott Event hub-példányt.
    - Válassza a **beállítások > a közös hozzáférési szabályzatok** lehetőséget.
    - Hozzon létre egy új kulcsot, vagy válasszon olyan meglévő kulcsot, amely rendelkezik **küldési** engedéllyel.
    - Másolja az elsődleges vagy a másodlagos kapcsolatok sztringjét. Ezt a kapcsolódási karakterláncot használva új célhelyet állíthat be IoT Centralban.
    - Azt is megteheti, hogy a teljes Event Hubs névtérhez létrehoz egy kapcsolatok karakterláncot:
        1. Nyissa meg a Event Hubs névteret a Azure Portalban.
        2. A **Beállítások** területen válassza a **megosztott elérési szabályzatok** elemet.
        3. Hozzon létre egy új kulcsot, vagy válasszon olyan meglévő kulcsot, amely rendelkezik **küldési** engedéllyel.
        4. Az elsődleges vagy a másodlagos kapcsolatok karakterláncának másolása
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Service Bus üzenetsor vagy témakör célhelyének létrehozása

Ha nem rendelkezik meglévő Service Bus-névtérrel az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Service Bus névteret a Azure Portalban](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). [Azure Service Bus dokumentációban](../../service-bus-messaging/service-bus-create-namespace-portal.md)bővebben is olvashat.

1. Az exportáláshoz használandó üzenetsor vagy témakör létrehozásához nyissa meg a Service Bus névteret, és válassza a **+ üzenetsor** vagy a **+ témakör** lehetőséget.

1. Hozzon létre egy kulcsot, amelyet akkor kell használni, ha az adatexportálást IoT Centralban állítja be:

    - Válassza ki a létrehozott várólistát vagy témakört.
    - Válassza a **Beállítások/közös hozzáférési szabályzatok** lehetőséget.
    - Hozzon létre egy új kulcsot, vagy válasszon olyan meglévő kulcsot, amely rendelkezik **küldési** engedéllyel.
    - Másolja az elsődleges vagy a másodlagos kapcsolatok sztringjét. Ezt a kapcsolódási karakterláncot használva új célhelyet állíthat be IoT Centralban.
    - Azt is megteheti, hogy a teljes Service Bus névtérhez létrehoz egy kapcsolatok karakterláncot:
        1. Nyissa meg a Service Bus névteret a Azure Portalban.
        2. A **Beállítások** területen válassza a **megosztott elérési szabályzatok** elemet.
        3. Hozzon létre egy új kulcsot, vagy válasszon olyan meglévő kulcsot, amely rendelkezik **küldési** engedéllyel.
        4. Az elsődleges vagy a másodlagos kapcsolatok karakterláncának másolása

### <a name="create-an-azure-blob-storage-destination"></a>Azure Blob Storage célhely létrehozása

Ha nem rendelkezik meglévő Azure Storage-fiókkal az exportáláshoz, kövesse az alábbi lépéseket:

1. Hozzon létre egy [új Storage-fiókot a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). További információ: új [Azure Blob Storage-fiókok](../../storage/blobs/storage-quickstart-blobs-portal.md) vagy [Azure Data Lake Storage v2 Storage-fiókok](../../storage/common/storage-account-create.md)létrehozása. Az adatexportálás csak a blokk blobokat támogató Storage-fiókokba tud írni. A következő lista az ismert kompatibilis Storage-fiókok típusait mutatja be:

    |Teljesítményszint|Fiók típusa|
    |-|-|
    |Standard|általános célú v2|
    |Standard|általános célú v1|
    |Standard|Blob Storage|
    |Prémium|BLOB Storage letiltása|

1. A Storage-fiókban lévő tároló létrehozásához nyissa meg a Storage-fiókját. A **blob szolgáltatás** alatt válassza a **Tallózás Blobok** lehetőséget. Egy új tároló létrehozásához kattintson a felül található **+ tároló** elemre.

1. A Storage-fiókhoz tartozó kapcsolati karakterlánc létrehozásához nyissa meg a **beállítások > hozzáférési kulcsok lehetőséget**. Másolja az egyiket a két összekapcsolási karakterláncból.

### <a name="create-a-webhook-endpoint"></a>Webhook-végpont létrehozása

Az adatexportálás nyilvánosan elérhető HTTP webhook-végpontba is elvégezhető. A [RequestBin](https://requestbin.net/)használatával létrehozhat egy teszt webhook-végpontot. RequestBin-szabályozás a kérelem korlátjának elérésekor:

1. Nyissa meg a [RequestBin](https://requestbin.net/).
2. Hozzon létre egy új RequestBin, és másolja a **bin URL-címét**. Ezt az URL-címet használja az adatexportálás tesztelésekor.

## <a name="set-up-data-export"></a>Az adatexportálás beállítása

Most, hogy van egy célhelye az adatai exportálásához, állítsa be az adatexportálást a IoT Central alkalmazásban:

1. Jelentkezzen be IoT Central alkalmazásba.

1. A bal oldali panelen válassza az **adatexportálás** elemet.

    > [!Tip]
    > Ha nem látja az **adatexportálást** a bal oldali ablaktáblán, akkor nincs engedélye az adatexportálás konfigurálására az alkalmazásban. Az adatexportálás beállításához forduljon a rendszergazdához.

1. Válassza az **+ új Exportálás** lehetőséget.

1. Adja meg az új Exportálás megjelenítendő nevét, és győződjön meg arról, hogy az adatexportálás **engedélyezve** van.

1. Válassza ki az exportálandó adattípust. A következő táblázat a támogatott adatexportálási típusokat sorolja fel:

    | Adattípus | Leírás | Adatformátum |
    | :------------- | :---------- | :----------- |
    |  Telemetria | Telemetria-üzenetek exportálása az eszközökről közel valós időben. Minden exportált üzenet tartalmazza az eredeti üzenet teljes tartalmát, normalizálva.   |  [Telemetria-üzenet formátuma](#telemetry-format)   |
    | Tulajdonságok módosítása | A módosításokat az eszköz és a felhő tulajdonságai között közel valós időben exportálhatja. A csak olvasható eszköz tulajdonságainál a jelentett értékek módosításai lesznek exportálva. Az írási és olvasási tulajdonságok esetében a jelentett és a kívánt értékeket is exportálja a rendszer. | [Tulajdonság-módosítási üzenet formátuma](#property-changes-format) |

<a name="DataExportFilters"></a>
1. Szükség esetén szűrők hozzáadásával csökkentheti az exportált adatmennyiséget. Az egyes adatexportálási típusokhoz különböző típusú szűrők érhetők el:

    A telemetria szűréséhez a következőket teheti:

    - **Az exportált** adatfolyam csak olyan eszközökön telemetria, amelyek megfelelnek az eszköz nevének, az eszköz azonosítójának és az eszköz sablonjának szűrési feltételének.
    - **Szűrés** a képességek felett: Ha egy telemetria elemet választ a **név** legördülő menüben, az exportált adatfolyam csak a szűrési feltételnek megfelelő telemetria tartalmaz. Ha a **név** legördülő menüben kiválasztja az eszköz vagy a felhő tulajdonság elemét, az exportált adatfolyam csak a telemetria megfelelő tulajdonságokkal rendelkező eszközökről származó eszközöket tartalmaz.
    - **Üzenet tulajdonságai szűrő**: az eszköz SDK-kat használó eszközök az egyes telemetria üzenetekben küldhetnek *üzenet-tulajdonságokat* vagy *alkalmazás-tulajdonságokat* . A tulajdonságok olyan kulcs-érték párok táska, amelyek egyéni azonosítókkal címkézik az üzenetet. Az üzenet tulajdonságai szűrő létrehozásához adja meg a keresett üzenet tulajdonság kulcsát, és adjon meg egy feltételt. Csak a megadott szűrési feltételnek megfelelő tulajdonságokkal rendelkező telemetria exportálja a rendszer. A következő karakterlánc-összehasonlító operátorok támogatottak: egyenlő, nem egyenlő, nem tartalmaz, nem tartalmaz, létezik, nem létezik. [További információ az alkalmazás tulajdonságairól IoT hub docs-ból](../../iot-hub/iot-hub-devguide-messages-construct.md).

    A tulajdonságok változásainak szűréséhez használjon egy **képesség szűrőt**. Válasszon egy tulajdonságot a legördülő menüben. Az exportált adatfolyam csak a szűrési feltételnek megfelelő kijelölt tulajdonság módosításait tartalmazza.

<a name="DataExportEnrichmnents"></a>
1. Igény szerint az exportált üzeneteket további kulcs-érték párokkal gazdagíthatja. A következő dúsítások érhetők el a telemetria és a tulajdonsághoz az adatexportálási típusok módosításához:

    - **Egyéni karakterlánc**: egyéni statikus karakterláncot hoz létre minden üzenethez. Írjon be egy tetszőleges kulcsot, és adjon meg egy karakterlánc-értéket.
    - **Tulajdonság**: hozzáadja az aktuális eszköz jelentett tulajdonságát vagy a Felhőbeli tulajdonság értékét az egyes üzenetekhez. Adjon meg egy kulcsot, és válasszon ki egy eszközt vagy egy Felhőbeli tulajdonságot. Ha az exportált üzenet olyan eszközről származik, amely nem rendelkezik a megadott tulajdonsággal, az exportált üzenet nem kapja meg a dúsítást.

1. Adjon hozzá egy új célhelyet, vagy adjon hozzá egy már létrehozott célhelyet. Válassza az **új létrehozása** hivatkozást, és adja hozzá a következő információkat:

    - **Cél neve**: a célhely megjelenítendő neve IoT Centralban.
    - **Cél típusa**: válassza ki a célhely típusát. Ha még nem állította be a célhelyet, olvassa el az [Exportálás célhelyének beállítása](#set-up-export-destination)című témakört.
    - Az Azure Event Hubs, Azure Service Bus üzenetsor vagy témakör esetében illessze be az erőforráshoz tartozó kapcsolati karakterláncot, és szükség esetén adja meg a kis-és nagybetűket megkülönböztető esemény hub, üzenetsor vagy témakör nevét.
    - Az Azure Blob Storage esetében illessze be az erőforráshoz tartozó kapcsolatok karakterláncát, és ha szükséges, adja meg a kis-és nagybetűket megkülönböztető tároló nevét.
    - Webhook esetén illessze be a webhook-végpont visszahívási URL-címét. Igény szerint konfigurálhatja a webhook-engedélyezést (OAuth 2,0 és engedélyezési token), és hozzáadhat egyéni fejléceket. 
        - A OAuth 2,0 esetében csak az ügyfél-hitelesítő adatok folyamata támogatott. A célhely mentésekor a IoT Central kommunikálni fog a OAuth szolgáltatójával egy engedélyezési jogkivonat lekéréséhez. Ez a jogkivonat a célhelyre küldött összes üzenet "Authorization" fejlécéhez lesz csatolva.
        - Az engedélyezési jogkivonat esetében megadhat egy jogkivonat-értéket, amely közvetlenül az adott célhelyre küldött összes üzenet "engedélyezés" fejlécéhez lesz csatolva.
    - Válassza a **Létrehozás** lehetőséget.

1. Válassza a **+ cél** lehetőséget, és válasszon egy célhelyet a legördülő listából. Legfeljebb öt célhelyet adhat hozzá egyetlen exportáláshoz.

1. Ha végzett az Exportálás beállításával, válassza a **Mentés** lehetőséget. Néhány perc elteltével az adatai megjelennek a célhelyen.

## <a name="monitor-your-export"></a>Az Exportálás figyelése

A IoT Centralban lévő exportálások állapotának megtekintése mellett a [Azure monitor](../../azure-monitor/overview.md) használatával megtekintheti, hogy mennyi adattal exportálja, és milyen exportálási hibákba ütközik. Az exportálási és az Eszközállapot-metrikákat a Azure Portalban lévő diagramokon REST API, a PowerShellben vagy az Azure CLI-ben lévő lekérdezésekkel érheti el. Jelenleg a következő adatexportálási metrikákat figyelheti Azure Monitorban:

- Az exportáláshoz bejövő üzenetek száma a szűrők alkalmazása előtt.
- A szűrőkkel továbbított üzenetek száma.
- A célhelyekre sikeresen exportált üzenetek száma.
- Az észlelt hibák száma.

További információ: [IoT Central alkalmazások általános állapotának figyelése](howto-monitor-application-health.md).

## <a name="destinations"></a>Célhelyek

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage célhely

Az adatexportálás percenként egyszer történik, és minden olyan fájl, amely az előző exportálás óta változást tartalmaz. Az exportált adatfájlok JSON formátumban lesznek mentve. A Storage-fiókban található exportált adatelérési utak a következők:

- Telemetria: _{Container}/{app-id}/{partition_id}/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Tulajdonságok módosítása: _{Container}/{app-id}/{partition_id}/{yyyy}/{MM}/{DD}/{hh}/{mm}/{filename}_

Az exportált fájlok tallózásához a Azure Portal navigáljon a fájlhoz, és válassza a **blob szerkesztése** lehetőséget.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs és Azure Service Bus célhelyek

Az adatexportálás közel valós időben történik. Az adattartalom az üzenet törzsében van, és JSON formátumú, UTF-8 kódolású.

Az üzenet jegyzetek vagy Rendszertulajdonságok táska tartalmazza a,, `iotcentral-device-id` `iotcentral-application-id` `iotcentral-message-source` és `iotcentral-message-type` mezőket, amelyek az üzenettörzs megfelelő mezőivel megegyező értékekkel rendelkeznek.

### <a name="webhook-destination"></a>Webhook célhelye

Webhookok célhelye esetén a rendszer a közel valós időben is exportálja az adategységeket. Az üzenet törzsében lévő adatformátum megegyezik a Event Hubs és Service Bus.

## <a name="telemetry-format"></a>Telemetria formátuma

Minden exportált üzenet a teljes üzenet normalizált formáját tartalmazza, amelyet az eszköz küld az üzenet törzsében. Az üzenet JSON formátumú, és UTF-8-ként van kódolva. Az egyes üzenetekben található információk a következők:

- `applicationId`: A IoT Central alkalmazás azonosítója.
- `messageSource`: Az üzenet forrása – `telemetry` .
- `deviceId`: Annak az eszköznek az azonosítója, amely elküldte a telemetria üzenetet.
- `schema`: A hasznos adatok sémájának neve és verziószáma.
- `templateId`: Az eszközhöz társított eszköz sablonjának azonosítója.
- `enrichments`: Az exportáláskor beállított alkoholtartalom-NÖVELÉSEK.
- `messageProperties`: Az eszköz által az üzenettel küldött további tulajdonságok. Ezeket a tulajdonságokat más néven az *alkalmazás tulajdonságai* is nevezik. [További információ: IoT hub docs](../../iot-hub/iot-hub-devguide-messages-construct.md).

Event Hubs és Service Bus esetén a IoT Central gyorsan exportál egy új üzenetet, miután megkapta az üzenetet az eszközről. A felhasználó tulajdonságaiban (más néven az alkalmazás tulajdonságai) minden üzenet, a `iotcentral-device-id` , `iotcentral-application-id` és `iotcentral-message-source` automatikusan szerepel.

A blob Storage esetében az üzenetek kötegbe kerülnek, és percenként egyszer lesznek exportálva.

Az alábbi példa egy exportált telemetria üzenetet mutat be:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
### <a name="message-properties"></a>Üzenet tulajdonságai

A telemetria-üzenetek a telemetria-adattartalom mellett metaadatokat is tartalmazhatnak. Az előző kódrészlet példákat mutat be a rendszerüzenetekre, például a és a szolgáltatásra `deviceId` `enqueuedTime` . Ha többet szeretne megtudni a rendszerüzenetek tulajdonságairól, tekintse meg a [D2C IoT hub üzeneteinek rendszertulajdonságait](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages).

Ha egyéni metaadatokat kell hozzáadnia a telemetria-üzenetekhez, hozzáadhat tulajdonságokat a telemetria-üzenetekhez. Például ha az eszköz létrehozza az üzenetet, hozzá kell adnia egy időbélyeget.

A következő kódrészletből megtudhatja, hogyan adhatja hozzá a `iothub-creation-time-utc` tulajdonságot az üzenethez, amikor létrehozza azt az eszközön:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

A következő kódrészlet ezt a tulajdonságot mutatja be a blob Storage-ba exportált üzenetben:

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>Tulajdonság módosításainak formátuma

Minden üzenet vagy rekord egy eszköz vagy egy Felhőbeli tulajdonság egyik módosítását jelöli. Az eszköz tulajdonságainál csak a jelentett érték változásai lesznek exportálva külön üzenetként. Az exportált üzenetben szereplő információk a következők:

- `applicationId`: A IoT Central alkalmazás azonosítója.
- `messageSource`: Az üzenet forrása – `properties` .
- `messageType`: Vagy `cloudPropertyChange` , `devicePropertyDesiredChange` , vagy `devicePropertyReportedChange` .
- `deviceId`: Annak az eszköznek az azonosítója, amely elküldte a telemetria üzenetet.
- `schema`: A hasznos adatok sémájának neve és verziószáma.
- `templateId`: Az eszközhöz társított eszköz sablonjának azonosítója.
- `enrichments`: Az exportáláskor beállított alkoholtartalom-NÖVELÉSEK.

Event Hubs és Service Bus esetén IoT Central az új üzenetek adatait az Event hub-ba vagy a Service Bus üzenetsor vagy témakörbe exportálja közel valós időben. Az egyes üzenetek felhasználói tulajdonságaiban (más néven az alkalmazás tulajdonságai) a, a, `iotcentral-device-id` a `iotcentral-application-id` `iotcentral-message-source` és a `iotcentral-message-type` automatikusan szerepel.

A blob Storage esetében az üzenetek kötegbe kerülnek, és percenként egyszer lesznek exportálva.

Az alábbi példa egy exportált tulajdonság-módosítási üzenetet mutat be az Azure Blob Storageban.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>A régi adatexportálás és az adatexportálás összehasonlítása

Az alábbi táblázat az [örökölt adatexportálás](howto-export-data-legacy.md) és az új adatexportálási funkciók közötti különbségeket mutatja be:

| Képességek  | Örökölt adatexportálás | Új adatexportálás |
| :------------- | :---------- | :----------- |
| Elérhető adattípusok | Telemetria, eszközök, eszközök sablonjai | Telemetria, tulajdonságok módosításai |
| Szűrés | Nincsenek | Az exportált adattípustól függ. Telemetria, szűrés telemetria, üzenet tulajdonságai, tulajdonságértékek alapján |
| Modellbővítések | Nincsenek | Gazdagítsa az eszköz egyéni sztringjét vagy tulajdonságának értékét |
| Célhelyek | Azure Event Hubs, Azure Service Bus várólisták és témakörök, Azure Blob Storage | Ugyanaz, mint a korábbi adatexportáláshoz és webhookokhoz|
| Támogatott alkalmazások verziói | V2, V3 | Csak v3 |
| Jelentős korlátok | 5 export/alkalmazás, 1 cél/exportálás | 10 exportálás – cél kapcsolatok száma alkalmazás szerint |

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan használhatja az új adatexportálást, egy javasolt következő lépés az [elemzések használatának](./howto-create-analytics.md) megismerése IoT Central
