---
title: Lekérdezés az Azure IoT Hub üzenet-útválasztásban | Microsoft Docs
description: Ismerkedjen meg az IoT Hub üzenet-útválasztási lekérdezési nyelvvel, amely segítségével gazdag lekérdezéseket alkalmazhat az üzenetekre, hogy megkapja az Ön számára fontos információkat.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 83c290adea02915db1dc52bd359b4d3165611522
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92547707"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT-központ üzenet-útválasztásának lekérdezési szintaxisa

Az üzenet-útválasztás lehetővé teszi a felhasználók számára a különböző adattípusok, például az eszköz telemetria üzeneteinek, az eszköz életciklusának eseményeinek és az eszközök kettős módosítási eseményeinek különböző végpontokra való továbbítását Ezen az adathalmazon is alkalmazhat Rich lekérdezéseket, mielőtt az útválasztási művelettel megkapja az Ön számára fontos adatforrásokat. Ez a cikk a IoT Hub üzenet-útválasztási lekérdezési nyelvet ismerteti, és néhány gyakori lekérdezési mintát is tartalmaz.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az üzenet-útválasztás lehetővé teszi, hogy lekérdezze az üzenet tulajdonságait és az üzenet törzsét, valamint az eszköz Twin címkéit és az eszköz Twin tulajdonságait. Ha az üzenet törzse nem JSON, az üzenet-útválasztás továbbra is köröztetheti az üzenetet, de a lekérdezések nem alkalmazhatók az üzenet törzsére.  A lekérdezések olyan logikai kifejezésként vannak leírva, amelyben egy logikai igaz érték esetén a lekérdezés sikeres lesz, és az összes bejövő adat átirányítható, és a logikai hamis sikertelen lesz a lekérdezés, és nem történik adat. Ha a kifejezés kiértékelése null vagy nem definiált értékre történik, akkor FALSE (hamis) értékre van állítva, és a rendszer hibát okoz a IoT Hub [útvonalak erőforrás-naplófájljaiban](monitor-iot-hub-reference.md#routes) . A lekérdezés szintaxisának helyesnek kell lennie az útvonal mentéséhez és kiértékeléséhez.  

## <a name="message-routing-query-based-on-message-properties"></a>Üzenet-útválasztási lekérdezés üzenet tulajdonságai alapján 

A IoT Hub a protokollok közötti együttműködés [általános formátumát](iot-hub-devguide-messages-construct.md) határozza meg az összes eszközről a felhőbe irányuló üzenetkezeléshez. IoT Hub üzenet feltételezi az üzenet következő JSON-ábrázolását. A rendszer a minden felhasználó számára hozzáadja a rendszertulajdonságokat, és azonosítja az üzenet tartalmát. A felhasználók szelektív módon adhatnak hozzá alkalmazás-tulajdonságokat az üzenethez. Azt javasoljuk, hogy a IoT Hub eszközről a felhőbe irányuló üzenetkezeléshez használjon egyedi tulajdonságokat, nem pedig a kis-és nagybetűket. Ha például több azonos nevű tulajdonsága van, akkor a IoT Hub csak a tulajdonságok egyikét küldi el.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Rendszertulajdonságok

A rendszer tulajdonságai segítenek az üzenetek tartalmának és forrásának azonosításában. 

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| contentType | sztring | A felhasználó az üzenet tartalmának típusát adja meg. Az üzenet törzse lekérdezésének engedélyezéséhez ennek az értéknek az Application/JSON értéket kell beállítania. |
| contentEncoding | sztring | A felhasználó megadja az üzenet kódolási típusát. Az engedélyezett értékek: UTF-8, UTF-16, UTF-32, ha a contentType alkalmazás/JSON értékre van beállítva. |
| iothub--összekötő-eszköz-azonosító | sztring | Ezt az értéket IoT Hub állítja be, és azonosítja az eszköz AZONOSÍTÓját. A lekérdezéshez használja a következőt: `$connectionDeviceId` . |
| iothub – enqueuedtime | sztring | Ezt az értéket a IoT Hub állítja be, és a tényleges időpontot jelöli, hogy az üzenet enqueuing az UTC-ben. A lekérdezéshez használja a következőt: `enqueuedTime` . |
| DT – DataSchema | sztring |  Ezt az értéket az IoT hub állítja be az eszközről a felhőbe irányuló üzenetekben. Ez tartalmazza az eszköz-kapcsolatban beállított eszköz-modell AZONOSÍTÓját. A lekérdezéshez használja a következőt: `$dt-dataschema` . |
| DT – tárgy | sztring | Annak az összetevőnek a neve, amely az eszközről a felhőbe irányuló üzeneteket küld. A lekérdezéshez használja a következőt: `$dt-subject` . |

A [IoT hub üzeneteiben](iot-hub-devguide-messages-construct.md)leírtak szerint további Rendszertulajdonságok találhatók egy üzenetben. Az előző táblázat fenti tulajdonságai mellett a **connectionModuleId** is lekérdezheti a **connectionDeviceId**.

### <a name="application-properties"></a>Az alkalmazás tulajdonságai

Az alkalmazás tulajdonságai olyan felhasználó által definiált karakterláncok, amelyek hozzáadhatók az üzenethez. Ezeket a mezőket nem kötelező megadni.  

### <a name="query-expressions"></a>Lekérdezési kifejezések

Az üzenetsor-tulajdonságok lekérdezését előtaggal kell ellátni a `$` szimbólummal. Az alkalmazás tulajdonságainak lekérdezései a nevükkel érhetők el, és nem szabad a szimbólummal előtaggal ellátni `$` . Ha egy alkalmazás-tulajdonságnév a `$` következővel kezdődik, akkor IoT hub megkeresi a rendszer tulajdonságai között, és nem található, akkor az alkalmazás tulajdonságai között fog megjelenni. Például: 

Lekérdezés a rendszertulajdonság contentEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Lekérdezés az Application Property processingPath:

```sql
processingPath = 'hot'
```

A lekérdezések kombinálásával logikai kifejezéseket és függvényeket használhat:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

A támogatott operátorok és függvények teljes listája a [kifejezés és a kikötések között](iot-hub-devguide-query-language.md#expressions-and-conditions)látható.

## <a name="message-routing-query-based-on-message-body"></a>Üzenet-útválasztási lekérdezés üzenet törzse alapján

Az üzenettörzs lekérdezésének engedélyezéséhez az üzenetnek egy UTF-8, UTF-16 vagy UTF-32 kódolású JSON-fájlban kell lennie. A `contentType` `application/JSON` `contentEncoding` tulajdonságot a következőre kell beállítani: és értékre kell állítani a System (rendszer) tulajdonság egyik támogatott UTF-kódolásához. Ha ezek a tulajdonságok nincsenek megadva, a IoT Hub nem értékeli ki a lekérdezési kifejezést az üzenet törzsében. 

Az alábbi példa bemutatja, hogyan hozhat létre egy megfelelően formázott és kódolt JSON-törzsű üzenetet: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

> [!NOTE] 
> Ez azt mutatja be, hogyan kezelhető a törzs kódolása a JavaScriptben. Ha a C#-ban szeretné megtekinteni a mintát, töltse le az [Azure IoT c#-mintákat](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Bontsa ki a master.zip fájlt. A Visual Studio Solution *SimulatedDevice* program. cs fájlja azt mutatja be, hogyan lehet üzeneteket kódolni és elküldeni egy IoT hubba. Ez ugyanaz a minta, amely az üzenet-útválasztás tesztelésére szolgál az üzenet- [útválasztási oktatóanyagban](tutorial-routing.md)leírtak szerint. A program. cs alján egy metódust is olvashat az egyik kódolt fájl olvasásához, dekódolja azt, és az ASCII-ként írja vissza, hogy elolvassa. 


### <a name="query-expressions"></a>Lekérdezési kifejezések

Az üzenettörzs lekérdezését előtaggal kell ellátni `$body` . A lekérdezési kifejezésben használhat egy szövegtörzs-hivatkozást, törzsi tömböt vagy több szövegtörzs-hivatkozást is. A lekérdezési kifejezés egy szövegtörzs-hivatkozást is egyesítheti az üzenetrendszer tulajdonságaival és az üzenet alkalmazás tulajdonságainak hivatkozásával. A következők például az összes érvényes lekérdezési kifejezés: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Üzenet-útválasztási lekérdezés a Twin eszköz alapján 

Az üzenet-útválasztás lehetővé teszi, hogy lekérdezze a JSON-objektumokat az [eszköz kettős](iot-hub-devguide-device-twins.md) címkéi és tulajdonságai között. A Twin modul lekérdezése is támogatott. Alább látható az eszköz két címkéje és a tulajdonságok mintája.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Lekérdezési kifejezések

Az üzenet Twin lekérdezését előtaggal kell ellátni `$twin` . A lekérdezés kifejezése egy kettős címkét vagy egy tulajdonság hivatkozását is egyesítheti egy szövegtörzs-hivatkozással, egy üzenetrendszer tulajdonságaival és az üzenet alkalmazás tulajdonságainak hivatkozásával. Azt javasoljuk, hogy a címkék és a tulajdonságok egyedi nevét használják, mivel a lekérdezés nem megkülönbözteti a kis-és nagybetűket. Ez az összes eszközre és az ikrekre is vonatkozik. A, a, a, a, a, a, a `twin` `$twin` és a `body` `$body` tulajdonság neve is. A következők például az összes érvényes lekérdezési kifejezés: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

A szövegtörzsön vagy eszközön lévő, a hasznos adattartalomban vagy a tulajdonság nevében lévő időszakra vonatkozó útválasztási lekérdezés nem támogatott.

## <a name="next-steps"></a>Következő lépések

* Tudnivalók az [üzenetek útválasztásáról](iot-hub-devguide-messages-d2c.md).
* Próbálja ki az [üzenet-útválasztási oktatóanyagot](tutorial-routing.md).
