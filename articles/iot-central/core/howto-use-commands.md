---
title: Az eszközök parancsainak használata Azure IoT Central-megoldásokban
description: Az eszközök parancsainak használata az Azure IoT Central megoldásban. Ebből az oktatóanyagból megtudhatja, hogyan, mint az eszköz fejlesztője, hogyan használhat eszköz-parancsokat az ügyfélalkalmazás Azure IoT Central-alkalmazásához.
author: dominicbetts
ms.author: dobett
ms.date: 01/07/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: e53bf377a7ef8f2293debd288ba25ef8f04ff4fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98610998"
---
# <a name="how-to-use-commands-in-an-azure-iot-central-solution"></a>Parancsok használata Azure IoT Central-megoldásokban

Ez a útmutató azt mutatja be, hogyan lehet az eszköz fejlesztője az eszköz sablonjában definiált parancsok használatát.

Az operátorok a IoT Central felhasználói felület használatával hívhatnak meg egy parancsot az eszközön. A parancsok vezérlik az eszköz viselkedését. Előfordulhat például, hogy egy operátor meghív egy parancsot egy eszköz újraindítására vagy diagnosztikai adatok gyűjtésére.

Az eszköz A következőket teheti:

* Azonnal válaszoljon a parancsra.
* Válaszoljon IoT Centralra, amikor megkapja a parancsot, majd később értesíti IoT Central a *hosszan futó parancs* befejeződéséről.

Alapértelmezés szerint a parancsok elvárják, hogy az eszköz csatlakoztatva legyen, és sikertelen lesz, ha az eszköz nem érhető el. Ha az eszköz sablonjának felhasználói felületén a **kapcsolat nélküli** beállítás van kiválasztva, a parancs várólistára helyezhető, amíg az eszköz online állapotba nem kerül. Ezek az *Offline parancsok* a cikk későbbi részében, egy külön szakaszban olvashatók.

## <a name="define-your-commands"></a>A parancsok megadása

A rendszer elküldi a szabványos parancsokat egy eszköznek, hogy utasítsa az eszközt a művelet elvégzésére. A parancsok tartalmazhatnak további információkat tartalmazó paramétereket. Egy eszközön lévő szelep megnyitására szolgáló parancs például rendelkezhet egy paraméterrel, amely meghatározza, hogy mennyit kell megnyitni a szelepet. A parancsok visszatérési értéket is kaphatnak, amikor az eszköz befejezi a parancsot. Például egy olyan parancs, amely arra kéri az eszközt, hogy egy bizonyos diagnosztikát futtasson, visszaadott értékként diagnosztikai jelentést kaphat.

A parancsok egy eszköz sablonjának részeként vannak meghatározva. A következő képernyőfelvételen látható a **Beolvasás Max-Min jelentés** -utasítás a **termosztát** -eszköz sablonjában. A parancshoz a kérelem és a válasz paraméter is tartozik: 

:::image type="content" source="media/howto-use-commands/command-definition.png" alt-text="Képernyőfelvétel: a maximális min. jelentés beolvasása parancs a termosztát-eszköz sablonjában":::

A következő táblázat a parancs funkciójának konfigurációs beállításait mutatja be:

| Mező             |Leírás|
|-------------------|-----------|
|Megjelenítendő név       |Az irányítópultokon és űrlapokon használt parancs értéke.|
| Name            | A parancs neve. IoT Central a megjelenített név alapján létrehoz egy értéket a mezőhöz, de szükség esetén kiválaszthatja a saját értékét is. A mezőnek alfanumerikusnak kell lennie. Az eszköz kódja ezt a **nevet** használja.|
| Képesség típusa | Parancs.|
| Üzenetsor offline | Azt határozza meg, hogy a parancs elérhető-e *Offline* parancsként. |
| Leírás     | A parancs funkciójának leírása.|
| Megjegyzés     | A parancs képességével kapcsolatos megjegyzések.|
| Kérés     | Az eszköz parancsának adattartalma.|
| Reagálás     | Az eszköz parancsra adott válasz hasznos adatai.|

A következő kódrészlet a parancs JSON-ábrázolását mutatja az eszköz modelljében. Ebben a példában a válasz értéke egy összetett **objektumtípus** több mezővel:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

> [!TIP]
> Az eszköz modelljét az eszköz sablonja lapon exportálhatja.

A parancs definícióját a felhasználói felület képernyőképére a következő mezők használatával kapcsolhatja össze:

* `@type` a képesség típusának megadása: `Command`
* `name` a parancs értékeként.

Az opcionális mezők, például a megjelenítendő név és a Leírás lehetővé teszik, hogy további részleteket adjon hozzá az interfészhez és a képességekhez.

## <a name="standard-commands"></a>Szabványos parancsok

Ez a szakasz azt mutatja be, hogyan küld egy eszköz a válasz értékét, amint megkapja a parancsot.

A következő kódrészlet azt mutatja be, hogy egy eszköz hogyan tud válaszolni egy parancsra a sikeres sikerességi kód elküldése után:

> [!NOTE]
> Ez a cikk az egyszerűség kedvéért Node.js használ. Más nyelvi példákat a következő témakörben talál: [ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central alkalmazással](tutorial-connect-device.md) – oktatóanyag.

```javascript
client.onDeviceMethod('getMaxMinReport', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'getMaxMinReport': {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

A `onDeviceMethod` metódust beállító hívás `commandHandler` . Ez a parancs kezelője:

1. Ellenőrzi a parancs nevét.
1. A `getMaxMinReport` parancshoz a `getMaxMinReportObject` visszatérési objektumba felvenni kívánt értékek beolvasása szükséges.
1. `sendCommandResponse`A válasz visszaküldésének meghívása IoT Centralra. Ez a válasz tartalmazza a `200` sikerességet jelző választ.

Az alábbi képernyőképen látható, hogyan jelenik meg a sikeres parancs válasza a IoT Central felhasználói felületen:

:::image type="content" source="media/howto-use-commands/simple-command-ui.png" alt-text="A standard parancsra vonatkozó parancs hasznos adatainak megtekintését bemutató képernyőkép":::

## <a name="long-running-commands"></a>Hosszú ideig futó parancsok

Ebből a szakaszból megtudhatja, hogyan késleltetheti egy eszköz a parancs versengő megerősítését.

A következő kódrészlet azt mutatja be, hogy egy eszköz hogyan implementálhat egy hosszan futó parancsot:

> [!NOTE]
> Ez a cikk az egyszerűség kedvéért Node.js használ. Más nyelvi példákat a következő témakörben talál: [ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central alkalmazással](tutorial-connect-device.md) – oktatóanyag.

```javascript
client.onDeviceMethod('rundiagnostics', commandHandler);

// ...

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case 'rundiagnostics': {
    console.log('Starting long-running diagnostics run ' + request.payload);
    await sendCommandResponse(request, response, 202, 'Diagnostics run started');

    // Long-running operation here
    // ...

    const patch = {
      rundiagnostics: {
        value: 'Diagnostics run complete at ' + new Date().toLocaleString()
      }
    };

    deviceTwin.properties.reported.update(patch, function (err) {
      if (err) throw err;
      console.log('Properties have been reported for component');
    });
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};
```

A `onDeviceMethod` metódust beállító hívás `commandHandler` . Ez a parancs kezelője:

1. Ellenőrzi a parancs nevét.
1. `sendCommandResponse`A válasz visszaküldésének meghívása IoT Centralra. Ez a válasz tartalmazza a `202` Válasz kódját, hogy jelezze a függőben lévő eredményeket.
1. Befejezi a hosszan futó műveletet.
1. Egy jelentett tulajdonságot használ ugyanazzal a névvel, mint a paranccsal, hogy IoT Central, hogy a parancs befejeződött.

A következő képernyőfelvétel azt mutatja be, hogy a parancs válasza hogyan jelenjen meg a IoT Central felhasználói felületén, amikor a 202-es válasz kódját kapja:

:::image type="content" source="media/howto-use-commands/long-running-start.png" alt-text="Képernyőkép, amely az eszköz azonnali válaszát mutatja":::

Az alábbi képernyőfelvételen a IoT Central felhasználói felület jelenik meg, amikor megkapja a parancsot tartalmazó frissítést:

:::image type="content" source="media/howto-use-commands/long-running-finish.png" alt-text="A hosszan futó parancs befejeződését bemutató képernyőkép":::

## <a name="offline-commands"></a>Offline parancsok

Ez a szakasz azt mutatja be, hogy az eszköz hogyan kezel egy offline parancsot. Ha egy eszköz online állapotban van, az offline parancsot a fogadása után képes kezelni. Ha egy eszköz offline állapotban van, az az offline parancsot kezeli, amikor a következőhöz csatlakozik IoT Centralhoz. Az eszközök nem küldhetnek visszatérési értéket egy offline parancsra adott válaszként.

> [!NOTE]
> Ez a cikk az egyszerűség kedvéért Node.js használ.

Az alábbi képernyőfelvételen egy **GenerateDiagnostics** nevű offline parancs látható. A Request paraméter olyan objektum, amelynek dátum és idő tulajdonsága egy " **Bank** **" nevű egész** szám enumerálási tulajdonság.

:::image type="content" source="media/howto-use-commands/offline-command.png" alt-text="A kapcsolat nélküli parancs felhasználói felületét megjelenítő képernyőkép":::

A következő kódrészlet azt mutatja be, hogyan figyelheti meg az ügyfél az offline parancsokat, és megjeleníti az üzenet tartalmát:

```javascript
client.on('message', function (msg) {
  console.log('Body: ' + msg.data);
  console.log('Properties: ' + JSON.stringify(msg.properties));
  client.complete(msg, function (err) {
    if (err) {
      console.error('complete error: ' + err.toString());
    } else {
      console.log('complete sent');
    }
  });
});
```

Az előző kódrészlet kimenete az **időpontot és a** **banki** értékeket tartalmazó adattartalmat mutatja. A tulajdonságok listája tartalmazza a parancs nevét a **metódus-név** listaelemben:

```output
Body: {"StartTime":"2021-01-06T06:00:00.000Z","Bank":2}
Properties: {"propertyList":[{"key":"iothub-ack","value":"none"},{"key":"method-name","value":"GenerateDiagnostics"}]}
```

> [!NOTE]
> Az offline parancsok alapértelmezett élettartama 24 óra, amely után lejár az üzenet.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja a parancsokat az Azure IoT Central alkalmazásban, a [hasznos](concepts-telemetry-properties-commands.md) adatokkal kapcsolatos további információkért tekintse meg a parancsok paramétereit, és [hozzon létre és csatlakoztasson egy ügyfélalkalmazás-alkalmazást az Azure IoT Central-alkalmazáshoz](tutorial-connect-device.md) , és tekintse meg a különböző nyelvű kódok teljes
