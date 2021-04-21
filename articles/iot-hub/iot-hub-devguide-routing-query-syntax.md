---
title: Üzenet-Azure IoT Hub lekérdezése | Microsoft Docs
description: Ismerje meg IoT Hub üzenet-útválasztás lekérdezési nyelvét, amely segítségével gazdag lekérdezéseket alkalmazhat az üzenetekre a fontos adatok fogadásához.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: c4ba48377d868404ff130ec458e50e2b42fae977
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790516"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT-központ üzenet-útválasztásának lekérdezési szintaxisa

Az üzenet-útválasztással a felhasználók különböző adattípusokat, eszköz-telemetriai üzeneteket, eszköz életciklus-eseményeket és ikereszköz-módosítási eseményeket irányíthatnak át különböző végpontokra. Emellett gazdag lekérdezéseket is alkalmazhat ezekre az adatokra, mielőtt azokat az Ön számára fontos adatok fogadásához útválasztásként átveszi. Ez a cikk az üzenetek IoT Hub lekérdezési nyelvét ismerteti, és néhány gyakori lekérdezési mintát ismertet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az üzenetek útválasztása lehetővé teszi az üzenettulajdonságok és az üzenet törzsének, valamint az ikereszköz-címkék és az ikertulajdonságok lekérdezését. Ha az üzenet törzse nem JSON, az üzenetek útválasztása továbbra is irányíthatja az üzenetet, de nem lehet lekérdezéseket alkalmazni az üzenet törzsére.  A lekérdezések olyan logikai kifejezésekként vannak leírva, amelyekben a logikai igaz érték sikeressé teszi a lekérdezést, amely minden bejövő adatot irányít át, a logikai hamis pedig meghiúsul a lekérdezésben, és egyetlen adat sem lesz irányítva. Ha [a](monitor-iot-hub-reference.md#routes) kifejezés kiértékelése null vagy nincs meghatározva, akkor a rendszer false (hamis) értékként kezeli, és hiba esetén hibát generál a IoT Hub az erőforrásnaplók naplóit. A lekérdezési szintaxisnak helyesnek kell lennie a menteni és kiértékelni tervezett útvonalhoz.  

## <a name="message-routing-query-based-on-message-properties"></a>Üzenet-útválasztási lekérdezés üzenettulajdonságok alapján 

A IoT Hub közös [formátumot](iot-hub-devguide-messages-construct.md) határoz meg az összes eszközről a felhőbe való üzenetkezeléshez a protokollok közötti együttműködés érdekében. IoT Hub üzenet az üzenet következő JSON-ábrázolását feltételezi. A rendszer minden felhasználó számára hozzáadja a rendszertulajdonságokat, és azonosítja az üzenet tartalmát. A felhasználók szelektíven adhatnak alkalmazástulajdonságokat az üzenethez. Javasoljuk, hogy egyedi tulajdonságneveket IoT Hub az eszközről a felhőbe való üzenetküldéshez, és ne legyen megkülönböztetve a kis- és nagybetűktől. Ha például több azonos nevű tulajdonsággal rendelkezik, a IoT Hub csak az egyik tulajdonságot fogja küldeni.  

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

A rendszertulajdonságok segítenek azonosítani az üzenetek tartalmát és forrását. 

| Tulajdonság | Típus | Description |
| -------- | ---- | ----------- |
| contentType (tartalomtípus) | sztring | A felhasználó megadja az üzenet tartalomtípusát. Az üzenet törzsére vonatkozó lekérdezések engedélyezése érdekében ezt az értéket alkalmazás/JSON értékre kell állítani. |
| contentEncoding (tartalom kódolása) | sztring | A felhasználó megadja az üzenet kódolási típusát. Megengedett értékek: UTF-8, UTF-16, UTF-32, ha a contentType értéke application/JSON. |
| iothub-connection-device-id | sztring | Ezt az értéket a IoT Hub és azonosítja az eszköz azonosítóját. A lekérdezéshez használja a `$connectionDeviceId` et. |
| iothub-enqueuedtime | sztring | Ezt az értéket a rendszer IoT Hub, és az üzenetbesorolás tényleges idejét jelöli UTC időzónában. A lekérdezéshez használja a `enqueuedTime` et. |
| dt-dataschema | sztring |  Ezt az értéket az IoT Hub adja meg az eszközről a felhőbe küldött üzeneteken. Ez tartalmazza az eszközkapcsolatban beállított eszközmodell-azonosítót. A lekérdezéshez használja a `$dt-dataschema` et. |
| dt-subject | sztring | Az eszközről a felhőbe üzeneteket küldő összetevő neve. A lekérdezéshez használja a `$dt-subject` et. |

Ahogy azt a [IoT Hub Messages](iot-hub-devguide-messages-construct.md)(Üzenetek) IoT Hub is ismerteti, az üzenetek további rendszertulajdonságokat tartalmaznak. Az előző táblázat fenti tulajdonságai mellett lekérdezheti a **connectionDeviceId**, **connectionModuleId adatokat is.**

### <a name="application-properties"></a>Az alkalmazás tulajdonságai

Az alkalmazástulajdonságok felhasználó által definiált sztringek, amelyek hozzáadhatóak az üzenethez. Ezek a mezők nem kötelezőek.  

### <a name="query-expressions"></a>Lekérdezési kifejezések

Az üzenetrendszer tulajdonságaira rákérdező lekérdezést a szimbólummal kell `$` előtagként használni. Az alkalmazástulajdonságok lekérdezései a nevük alapján érhetők el, és nem előtagként kell őket használni a `$` szimbólummal. Ha egy alkalmazástulajdonság neve a kifejezéssel kezdődik, IoT Hub a rendszertulajdonságok között keresi azt, és nem található, akkor az alkalmazás tulajdonságai `$` között fog keresni. Például: 

A contentEncoding rendszertulajdonság lekérdezése 

```sql
$contentEncoding = 'UTF-8'
```

A processingPath alkalmazástulajdonság lekérdezése:

```sql
processingPath = 'hot'
```

A lekérdezések kombinálhatóak logikai kifejezésekkel és függvényekkel:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

A támogatott operátorok és függvények teljes listája a Kifejezés és feltételek [alatt látható.](iot-hub-devguide-query-language.md#expressions-and-conditions)

## <a name="message-routing-query-based-on-message-body"></a>Üzenet-útválasztási lekérdezés az üzenet törzse alapján

Ha engedélyezni szeretné a lekérdezést az üzenet törzsén, az üzenetnek UTF-8, UTF-16 vagy UTF-32 kódolású JSON-fájlban kell lennie. A és a tulajdonságot a rendszertulajdonság által támogatott `contentType` `application/JSON` UTF-kódolások `contentEncoding` egyikére kell állítani. Ha ezek a tulajdonságok nincsenek megadva, IoT Hub nem értékeli ki a lekérdezési kifejezést az üzenet törzsében. 

Az alábbi példa bemutatja, hogyan hozhat létre egy üzenetet egy megfelelően formázott és kódolt JSON-törzstel: 

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
> Ez bemutatja, hogyan kezelhető a törzs kódolása JavaScriptben. Ha C# környezetben szeretne mintát látni, töltse le az [Azure IoT C#-mintákat.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Csomagolja ki a master.zip fájlt. A Visual Studio *SimulatedDevice* Program.cs fájlja bemutatja, hogyan kódolhat és küldhet el üzeneteket egy IoT Hub. Ez ugyanaz a minta, mint az üzenetek útválasztásának tesztelésére, ahogy azt a [Message Routing oktatóanyagban is ismertettuk.](tutorial-routing.md) A Program.cs fájl alján található egy metódus is, amely beolvassa az egyik kódolt fájlt, dekódolja, majd ASCII-ként kiírja, hogy el tudja olvasni. 


### <a name="query-expressions"></a>Lekérdezési kifejezések

Az üzenet törzsére rákérdező lekérdezést a előtaggal kell előtagként `$body` használni. Használhat törzshivatkozást, törzstömbhivatkozást vagy több törzshivatkozást a lekérdezési kifejezésben. A lekérdezési kifejezés egy törzshivatkozást is kombinálhat üzenetrendszer-tulajdonságokkal és üzenetalkalmazás-tulajdonságok hivatkozásával. A következők például mind érvényes lekérdezési kifejezések: 

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

> [!NOTE] 
> Lekérdezéseket és függvényeket csak a törzshivatkozás tulajdonságain futtathat. Nem futtathat lekérdezéseket vagy függvényeket a teljes törzshivatkozáson. A következő lekérdezés például nem *támogatott,* és a következőt adja `undefined` vissza:
> 
> ```sql
> $body[0] = 'Feb'
> ```

## <a name="message-routing-query-based-on-device-twin"></a>Ikereszközön alapuló üzenet-útválasztási lekérdezés 

Az üzenetek útválasztása lehetővé teszi az [ikereszközcímkék](iot-hub-devguide-device-twins.md) és -tulajdonságok, azaz JSON-objektumok lekérdezését. Az ikermodulon való lekérdezés is támogatott. Alább látható az ikereszközcímkék és -tulajdonságok mintája.

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

Az ikerüzenetre rákérdező lekérdezést a előtaggal kell előtagként `$twin` használni. A lekérdezési kifejezés egy ikercímkét vagy tulajdonsághivatkozást is kombinálhat egy törzshivatkozással, az üzenetrendszer tulajdonságaival és az üzenetalkalmazás tulajdonságainak hivatkozásával. Javasoljuk, hogy egyedi neveket használ a címkékben és tulajdonságokban, mivel a lekérdezés nem megkülönbözteti a kis- és nagybetűket. Ez az ikereszközre és a modulikre egyaránt vonatkozik. Emellett ne használja a `twin` `$twin` , , vagy `body` `$body` tulajdonságneveket. A következők például mind érvényes lekérdezési kifejezések: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Nem támogatott az útválasztási lekérdezés a törzsön vagy az ikereszközön, ha a hasznos adat vagy a tulajdonság neve pontból áll.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [üzenetek útválasztását.](iot-hub-devguide-messages-d2c.md)
* Próbálja ki [az üzenet-útválasztási oktatóanyagot.](tutorial-routing.md)
