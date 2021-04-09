---
title: Tulajdonságok használata Azure IoT Central-megoldásokban
description: Ismerje meg, hogyan használható a csak olvasható és írható tulajdonságok egy Azure IoT Central-megoldásban.
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 36329987e510372ff286a10584a115ea259afc60
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98119084"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Tulajdonságok használata Azure IoT Central-megoldásokban

Ez a útmutató azt mutatja be, hogyan használható az eszköz fejlesztője az Azure IoT Central-alkalmazásban található eszköz-sablonban definiált eszközök tulajdonságainak használatára.

A tulajdonságok a pont – idő értékeket jelölik. Egy eszköz használhat például egy tulajdonságot a elérni kívánt cél hőmérséklet jelentésére. Alapértelmezés szerint az eszköz tulajdonságai írásvédettek a IoT Centralban. Az írható tulajdonságok lehetővé teszik az állapot szinkronizálását az eszköz és az Azure IoT Central alkalmazása között.

A felhő tulajdonságait egy Azure IoT Central alkalmazásban is megadhatja. A Felhőbeli tulajdonságok értékeit a rendszer soha nem cseréli ki az eszközön, és a jelen cikk hatókörén kívül esik.

## <a name="define-your-properties"></a>A tulajdonságok megadása

A tulajdonságok olyan adatmezők, amelyek az eszköz állapotát jelölik. Tulajdonságok használata az eszköz tartós állapotának, például az eszköz be-és kikapcsolási állapotának megjelenítéséhez. A tulajdonságok az alapszintű eszköz tulajdonságait is jelezhetik, például az eszköz szoftveres verzióját. A tulajdonságokat csak olvashatóként vagy írhatóként deklarálhatja.

Az alábbi képernyőfelvétel egy Azure IoT Central alkalmazásban található tulajdonság-definíciót mutat be.

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="Képernyőfelvétel egy Azure IoT Central-alkalmazásban található tulajdonság-definícióról.":::

A következő táblázat a tulajdonságok funkciójának konfigurációs beállításait mutatja be.

| Mező           | Leírás                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Megjelenített név    | Az irányítópultokon és űrlapokon használt tulajdonságérték megjelenítendő neve.                                                                                                                                                              |
| Name            | A tulajdonság neve. Az Azure IoT Central a megjelenítendő név alapján létrehoz egy értéket a mezőhöz, de szükség esetén kiválaszthatja a saját értékét is. A mezőnek alfanumerikusnak kell lennie.  Az eszköz kódja ezt a **nevet** használja.           |
| Képesség típusa | Tulajdonság.                                                                                                                                                                                                                          |
| Szemantikai típus   | A tulajdonság szemantikai típusa, például hőmérséklet, állapot vagy esemény. A szemantikai típus megválasztása határozza meg, hogy a következő mezők közül melyek érhetők el.                                                                       |
| Séma          | A tulajdonság adattípusa, például Double, string vagy Vector. Az elérhető beállításokat a szemantikai típus határozza meg. A séma nem érhető el az esemény és az állapot szemantikai típusaihoz.                                               |
| Írható       | Ha a tulajdonság nem írható, az eszköz jelentést tud készíteni az Azure IoT Central. Ha a tulajdonság írható, az eszköz képes jelenteni a tulajdonságértékek az Azure IoT Central számára. Ezután az Azure IoT Central el tudja küldeni a tulajdonságok frissítéseit az eszközre. |
| Súlyosság        | Csak az esemény szemantikai típusához érhető el. A megszakítások a következők: **hiba**, **információ** vagy **Figyelmeztetés**.                                                                                                                         |
| Állapot értékei    | Csak az állapot szemantikai típusához érhető el. Definiálja a lehetséges állapotinformációkat, amelyek mindegyike megjelenített névvel, névvel, számbavételi típussal és értékkel rendelkezik.                                                                                   |
| Unit (Egység)            | A tulajdonság értékének egysége, például: **mph**, **%** , vagy **&deg; C**.                                                                                                                                                              |
| Megjelenítési egység    | Irányítópultokon és űrlapokon használható megjelenítési egység.                                                                                                                                                                                    |
| Megjegyzés         | A tulajdonság képességével kapcsolatos megjegyzések.                                                                                                                                                                                        |
| Leírás     | A tulajdonság funkciójának leírása.                                                                                                                                                                                          |

A tulajdonságok az eszköz sablonjának felületén is meghatározhatók, ahogy az itt látható:

``` json
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "targetTemperature",
  "schema": "double",
  "displayName": "Target Temperature",
  "description": "Allows to remotely specify the desired target temperature.",
  "unit" : "degreeCelsius",
  "writable": true
},
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "unit" : "degreeCelsius",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

Ez a példa két tulajdonságot mutat be. Ezek a tulajdonságok a felhasználói felületen található tulajdonság-definícióhoz kapcsolódnak:

* `@type` a képesség típusát adja meg: `Property` . Az előző példa a szemantikai típust is megjeleníti `Temperature` mindkét tulajdonsághoz.
* `name` a tulajdonsághoz.
* `schema` Megadja a tulajdonság adattípusát. Ez az érték egyszerű típus lehet, például dupla, Integer, Boolean vagy string. Az összetett objektumtípusok és leképezések is támogatottak.
* `writable` Alapértelmezés szerint a tulajdonságok csak olvashatók. A tulajdonságot írhatóként jelölheti meg a mező használatával.

Az opcionális mezők, például a megjelenítendő név és a Leírás lehetővé teszik, hogy további részleteket adjon hozzá az interfészhez és a képességekhez.

Amikor létrehoz egy tulajdonságot, megadhat összetett sémákat, például az **objektumokat** és az **enumerálást**.

![Képernyőkép, amely bemutatja, hogyan adhat hozzá egy képességet.](./media/howto-use-properties/property.png)

Ha kijelöli az összetett **sémát**, például az **objektumot**, az objektumot is meg kell határoznia.

:::image type="content" source="media/howto-use-properties/object.png" alt-text="Az objektum definiálásának módját bemutató képernyőkép":::

Az alábbi kód egy objektum típusú tulajdonság definícióját mutatja be. Ennek az objektumnak két típusa van: string és Integer.

``` json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Csak olvasható tulajdonságok implementálása

Alapértelmezés szerint a tulajdonságok csak olvashatók. A csak olvasható tulajdonságok lehetővé teszik, hogy az eszköz jelentési tulajdonsága az Azure IoT Central alkalmazásra frissítsen. Az Azure IoT Central-alkalmazás nem állíthatja be a csak olvasható tulajdonság értékét.

Az Azure IoT Central eszköz-ikrek használatával szinkronizálja az eszköz és az Azure IoT Central alkalmazás közötti tulajdonságértékek értékét. Az eszköz tulajdonságainak értékei az eszköz Twin jelentett tulajdonságait használják. További információ: [eszközök ikrek](../../iot-hub/tutorial-device-twins.md).

Az eszköz modelljének következő kódrészlete egy írásvédett tulajdonság definícióját mutatja be:

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Az eszközök a tulajdonságok frissítését JSON-adattartalomként küldik el. További információ: [hasznos](./concepts-telemetry-properties-commands.md)adatok.

Az Azure IoT-eszköz SDK-val az Azure IoT Central-alkalmazáshoz tartozó tulajdonságok frissítését is elküldheti.

Az eszköz két tulajdonsága az Azure IoT Central-alkalmazásba a következő függvény használatával küldhetők:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

Ez a cikk az egyszerűség kedvéért Node.js használ. Más nyelvi példákat a következő témakörben talál: [ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central alkalmazással](tutorial-connect-device.md) – oktatóanyag.

Az Azure IoT Central alkalmazás következő nézetében láthatók a látható tulajdonságok. A nézet **automatikusan a** _csak olvasható eszköz tulajdonságot_ teszi elérhetővé.

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="A csak olvasható tulajdonságok nézetét megjelenítő képernyőkép":::

## <a name="implement-writable-properties"></a>Írható tulajdonságok implementálása

Az írható tulajdonságokat egy operátor állítja be az Azure IoT Central alkalmazásban az űrlapon. Az Azure IoT Central elküldi a tulajdonságot az eszköznek. Az Azure IoT Central egy nyugtát vár az eszközről.

Az eszköz modelljében az alábbi kódrészlet egy írható tulajdonság definícióját mutatja be:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Az eszköz által válaszolt írható tulajdonságok definiálásához és kezeléséhez a következő kódot használhatja:

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

A válaszüzenetnek tartalmaznia kell a `ac` és a `av` mezőket. A `ad` mező kitöltése nem kötelező. Tekintse meg a következő kódrészleteket példákkal:

* `ac` egy numerikus mező, amely az alábbi táblázatban szereplő értékeket használja.
* `av` az eszközre eljuttatott verziószám.
* `ad` egy paraméter-karakterlánc leírása.

| Érték | Címke | Leírás |
| ----- | ----- | ----------- |
| `'ac': 200` | Befejeződött | A tulajdonság-módosítási művelet sikeresen befejeződött. |
| `'ac': 202` vagy `'ac': 201` | Függőben | A tulajdonság-módosítási művelet függőben vagy folyamatban van. |
| `'ac': 4xx` | Hiba | A kért tulajdonság módosítása nem volt érvényes, vagy hiba történt. |
| `'ac': 5xx` | Hiba | Az eszköz váratlan hibát észlelt a kért módosítás feldolgozása során. |

További információ az eszköz-ikrekről: [eszközök konfigurálása háttér-szolgáltatásból](../../iot-hub/tutorial-device-twins.md).

Ha az operátor egy írható tulajdonságot állít be az Azure IoT Central alkalmazásban, az alkalmazás egy, az eszközre vonatkozó, a kívánt tulajdonságot használva küldi el az értéket az eszköznek. Az eszköz ezután válaszol a Device Twin jelentett tulajdonság használatával. Amikor az Azure IoT Central megkapja a jelentett tulajdonság értékét, az a tulajdonság nézetét **fogadja el**, amelynek állapota elfogadva.

A következő nézetben az írható tulajdonságok láthatók. Ha megadja az értéket, és a **Mentés** lehetőséget választja, a kezdeti állapot **függőben** van. Amikor az eszköz elfogadja a változást, az állapot **elfogadva** értékre változik.

![A függő állapotot megjelenítő képernyőkép.](./media/howto-use-properties/status-pending.png)

![Az elfogadott tulajdonságot megjelenítő képernyőkép.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja a tulajdonságokat az Azure IoT Central alkalmazásban, lásd:

* [Is található adattartalom](concepts-telemetry-properties-commands.md)
* [Ügyfélalkalmazás létrehozása és összekötése az Azure IoT Central-alkalmazással](tutorial-connect-device.md)