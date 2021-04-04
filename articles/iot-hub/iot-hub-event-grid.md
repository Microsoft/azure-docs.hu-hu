---
title: Azure IoT Hub és Event Grid | Microsoft Docs
description: A Azure Event Grid használatával a folyamatokat a IoT Hubban végrehajtott műveletek alapján aktiválhatja.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: 0e0ca8a787145fb40087a2d99be85607404eebfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152140"
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions"></a>IoT Hub eseményekre való reagálás Event Grid használatával a műveletek elindításához

Az Azure IoT Hub és az Azure Event Grid integrációja révén eseményekkel kapcsolatos értesítéseket küldhet más szolgáltatásokba, és alsóbb rétegbeli folyamatokat aktiválhat. Üzleti alkalmazásait úgy konfigurálhatja, hogy figyeljék az IoT Hub-eseményeket, így megbízható, skálázható és biztonságos módon reagálhat a kritikus eseményekre. Készíthet például olyan alkalmazást, amely frissít egy adatbázist, létrehoz egy munkajegyet és e-mail-értesítést küld minden alkalommal, amikor új IoT-eszközt regisztrálnak az IoT-központba.

A [Azure Event Grid](../event-grid/overview.md) egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egy közzétételi és előfizetési modellt használ. Event Grid beépített támogatást biztosít az Azure-szolgáltatásokhoz, például a [Azure Functionshoz](../azure-functions/functions-overview.md) és a [Azure Logic Appshoz](../logic-apps/logic-apps-overview.md), és az események riasztásait a nem Azure-szolgáltatásokhoz webhookok használatával lehet kézbesíteni. A Event Grid által támogatott eseménykezelők teljes listájáért tekintse [meg a Azure Event Grid bemutatása](../event-grid/overview.md)című témakört.

![Azure Event Grid architektúra](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Regionális elérhetőség

A Event Grid integráció a Event Grid által támogatott régiókban található IoT hubok számára érhető el. A régiók legújabb listáját a [Azure Event Grid bemutatása](../event-grid/overview.md)című témakörben tekintheti meg.

## <a name="event-types"></a>Eseménytípusok

IoT Hub közzéteszi a következő eseménytípus-típusokat:

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Közzétett, ha egy eszköz regisztrálva van egy IoT-hubhoz. |
| Microsoft.Devices.DeviceDeleted | Közzétételre kerül, ha egy eszközt törölnek egy IoT-hubhoz. |
| Microsoft.Devices.DeviceConnected | Akkor jelenik meg, amikor egy eszköz IoT-hubhoz csatlakozik. |
| Microsoft.Devices.DeviceDisconnected | Akkor jelenik meg, ha egy eszköz le van választva egy IoT hubhoz. |
| Microsoft.Devices.DeviceTelemetry | Eszköz-telemetriai üzenet IoT-központba küldésekor van közzétéve |

A Azure Portal vagy az Azure CLI használatával konfigurálhatja, hogy mely eseményeket kell közzétenni az egyes IoT-központokból. Például próbálja ki az oktatóanyagot az [Azure IoT hub eseményekkel kapcsolatos e-mail-értesítések küldéséhez Logic Apps használatával](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="event-schema"></a>Eseményséma

IoT Hub események tartalmazzák az eszköz életciklusában bekövetkező változásokra való reagáláshoz szükséges összes információt. Egy IoT Hub-esemény arról ismerhető fel, hogy az eventType tulajdonság a **Microsoft.Devices** előtaggal kezdődik. További információ az Event Grid esemény tulajdonságainak használatáról: [Event Grid Event Schema](../event-grid/event-schema.md).

### <a name="device-connected-schema"></a>Csatlakoztatott eszköz sémája

Az alábbi példa egy csatlakoztatott eszköz sémáját mutatja be:

```json
[{  
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceConnected",
  "eventTime": "2018-06-02T19:17:44.4383997Z",
  "data": {
      "deviceConnectionStateEventInfo": {
        "sequenceNumber":
          "000000000000000001D4132452F67CE200000002000000000000000000000001"
      },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID",
  }, 
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```



### <a name="device-telemetry-schema"></a>Eszköz telemetria sémája

Az eszköz telemetria érvényes JSON formátumúnak kell lennie, és a contentType az **Application/JSON** és a contentEncoding értékre kell állítani az üzenetrendszer [tulajdonságai](iot-hub-devguide-routing-query-syntax.md#system-properties) **között.** Mindkét tulajdonság nem megkülönbözteti a kis-és nagybetűket. Ha nincs beállítva a tartalom kódolása, akkor a IoT Hub az üzeneteket az alap 64 kódolású formátumban fogja írni.

Az eszközök telemetria az Event Grid közzétételük előtt bővítheti, ha a végpontot Event Gridként kiválasztja. További információ: üzenet- [gazdagítás áttekintése](iot-hub-message-enrichments-overview.md).

Az alábbi példa egy eszköz telemetria-eseményének sémáját mutatja be:

```json
[{  
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {
      "body": {
          "Weather": {
              "Temperature": 900
            },
            "Location": "USA"
        },
        "properties": {
            "Status": "Active"
        },
        "systemProperties": {
          "iothub-content-type": "application/json",
          "iothub-content-encoding": "utf-8",
          "iothub-connection-device-id": "d1",
          "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
          "iothub-connection-auth-generation-id": "123455432199234570",
          "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
          "iothub-message-source": "Telemetry"
        }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="device-created-schema"></a>Eszköz létrehozva séma

Az alábbi példa egy eszköz létrehozott esemény sémáját mutatja be:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag":"null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```


> [!WARNING]
> Az eszköz létrehozási eseményéhez kapcsolódó *Twin adatok* alapértelmezett konfiguráció *, ezért nem szabad az* `authenticationType` újonnan létrehozott eszköz tényleges és egyéb tulajdonságaira támaszkodni. Az `authenticationType` újonnan létrehozott eszközök és egyéb eszközök tulajdonságainál használja az Azure IoT SDK-k által biztosított Register Manager API-t.

Az egyes tulajdonságok részletes ismertetését lásd: [Azure Event Grid Event Schema for IoT hub](../event-grid/event-schema-iot-hub.md).

## <a name="filter-events"></a>Események szűrése

IoT Hub esemény-előfizetések szűrhetik az eseményeket az esemény típusa, az adattartalom és a tárgy alapján, amely az eszköz neve.

Event Grid lehetővé teszi az események, a témák és az adattartalom [szűrését](../event-grid/event-filtering.md) . A Event Grid előfizetés létrehozásakor választhat, hogy előfizet a kiválasztott IoT-eseményekre. A Event Grid-munkafolyamatban található tulajdonosi szűrők a (előtag) és **az** (utótag) egyezések alapján **kezdődnek** . A szűrő egy `AND` operátort használ, így az előtagot és utótagot is megegyező tárgyú események az előfizető számára lesznek továbbítva.

A IoT-események tárgya a formátumot használja:

```json
devices/{deviceId}
```

A Event Grid az egyes események attribútumain is lehetővé teszi a szűrést, beleértve az adattartalmat is. Így kiválaszthatja, hogy milyen események érkeznek a telemetria üzenet alapján. A példák megtekintéséhez tekintse meg a [speciális szűrést](../event-grid/event-filtering.md#advanced-filtering) ismertető témakört. A telemetria **-** üzenet törzsének szűréséhez a ContentType az **Application/JSON** és a contentEncoding értékre kell állítani az üzenetrendszer [tulajdonságai](./iot-hub-devguide-routing-query-syntax.md#system-properties)között. Mindkét tulajdonság nem megkülönbözteti a kis-és nagybetűket.

A nem telemetria események, például a DeviceConnected, a DeviceDisconnected, a DeviceCreated és a DeviceDeleted esetében az előfizetés létrehozásakor az Event Grid szűrés használható. A telemetria-események esetében a Event Grid szűrésén kívül a felhasználók az üzenet-útválasztási lekérdezésen keresztül is szűrhetik az eszközön az ikreket, az üzenet tulajdonságait és a törzsét. 

Ha a Event Grid-on keresztül előfizet a telemetria-eseményekre, IoT Hub létrehoz egy alapértelmezett üzenet-útvonalat, amely az eszköz üzeneteinek küldését Event Grid küldi. További információ az üzenetek útválasztásáról: [IoT hub üzenet-útválasztás](iot-hub-devguide-messages-d2c.md). Ez az útvonal a portálon jelenik meg IoT Hub > üzenet-útválasztás alatt. Az telemetria-eseményekhez létrehozott előfizetések számától függetlenül csak egy Event Grid útvonal jön létre. Ha tehát több előfizetésre van szüksége különböző szűrőkkel, a lekérdezésekben szereplő vagy operátort is használhatja ugyanazon az útvonalon. Az útvonal létrehozását és törlését a telemetria-események Event Gridon keresztüli előfizetése vezérli. IoT Hub üzenet-útválasztás használatával nem hozhat létre vagy törölhet Event Grid útvonalat.

Az üzenetek telemetria az adatküldés előtt frissítheti az [útválasztási lekérdezést](iot-hub-devguide-routing-query-syntax.md). Vegye figyelembe, hogy az útválasztási lekérdezés csak akkor alkalmazható az üzenet törzsére, ha a törzs JSON. Az contentType az **Application/JSON** és a **contentEncoding értékre** kell állítania az üzenetrendszer [tulajdonságai](./iot-hub-devguide-routing-query-syntax.md#system-properties)között.

## <a name="limitations-for-device-connected-and-device-disconnected-events"></a>Csatlakoztatott és nem csatlakoztatott eszközökhöz kapcsolódó események korlátozásai

Az eszközhöz tartozó kapcsolódási állapot eseményeinek fogadásához az eszköznek vagy egy "C2D fogadása" telemetria kell lennie, vagy az IOT hub használatával kell megadnia az "üzenetküldési üzenet" műveletet. Vegye figyelembe azonban, hogy ha egy eszköz AMQP protokollt használ az IOT hub-hoz való kapcsolódáshoz, akkor azt javasoljuk, hogy a kapcsolati állapottal kapcsolatos értesítései néhány perc múlva késleltetve legyenek. Ha az eszköz MQTT protokollt használ, IoT Hub megnyitva marad a C2D hivatkozás. A AMQP a C2D hivatkozást úgy nyithatja meg, hogy meghívja a Receive aszinkron API-t a IoT Hub C# SDK-hoz vagy az AMQP-hez készült [ügyfélhez](iot-hub-amqp-support.md#device-client).

A D2C hivatkozás meg van nyitva, ha telemetria küld. 

Ha az eszköz kapcsolata vibrál, ami azt jelenti, hogy az eszköz gyakran csatlakozik a hálózathoz, és leválasztja a kapcsolatot, a rendszer nem küldi el minden egyes kapcsolati állapotot, de az aktuális kapcsolati állapotot rendszeres pillanatképként fogja közzétenni, amíg a villogás folytatódik. Ha ugyanazt a kapcsolati állapotot vagy eltérő sorszámot vagy eltérő kapcsolati állapotot jelző eseményt fogad, mindkettő azt jelenti, hogy módosult az eszköz kapcsolati állapota.

## <a name="tips-for-consuming-events"></a>Tippek események felhasználásához

Az IoT Hub-eseményeket kezelő alkalmazásokkal érdemes figyelembe venni az alábbi javaslatokat:

* Több feliratkozás is konfigurálható úgy, hogy ugyanahhoz az eseménykezelőhöz irányítsák az eseményeket, ezért nem szabad feltételezni, hogy az események egy adott forrásból származnak. Az üzenet témakörének vizsgálatával mindig ellenőrizni kell, hogy az abból az IoT-központból érkezett, ahonnan várta.

* Nem szabad feltételezni, hogy minden kapott esemény a várt típusú. Az üzenet feldolgozása előtt mindig keresse meg a eventType.

* Megtörténhet, hogy az üzenetek más sorrendben, vagy késve érkeznek. A ETAG mező használatával megtudhatja, hogy az objektumokkal kapcsolatos adatok naprakészek-e az eszköz által létrehozott vagy az eszköz által törölt események esetében.

## <a name="next-steps"></a>Következő lépések

* [Próbálja ki a IoT Hub Events oktatóanyagot](../event-grid/publish-iot-hub-events-to-logic-apps.md)

* [Ismerje meg, hogyan rendezheti az eszközhöz csatlakoztatott és nem csatlakoztatott eseményeket](iot-hub-how-to-order-connection-state-events.md)

* [További információ a Event Grid](../event-grid/overview.md)

* [Az Útválasztás IoT Hub események és üzenetek közötti különbségek összehasonlítása](iot-hub-event-grid-routing-comparison.md)

* [Ismerje meg, hogyan használhatók a IoT telemetria-események a IoT térbeli elemzések megvalósításához a Azure Maps használatával](../azure-maps/tutorial-iot-hub-maps.md)