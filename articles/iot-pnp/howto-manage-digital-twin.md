---
title: Az IoT-Plug and Play kezelése
description: IoT-Plug and Play kezelése digitális iker API-k használatával
author: prashmo
ms.author: prashmo
ms.date: 12/17/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: e68003878dc0e9275461100a59e0f45486c2978f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739871"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>IoT-Plug and Play kezelése

Az IoT Plug and Play **támogatja** a Digitális iker lekért és **a Digitális ikerművelet** frissítése műveleteket a digitális ikerműveletek kezeléséhez. Használhatja a [REST API-kat](/rest/api/iothub/service/digitaltwin) vagy valamelyik szolgáltatási [API-t.](libraries-sdks.md)

A cikk írásakor a Digital Twin API verziója `2020-09-30` a következő: .

## <a name="update-a-digital-twin"></a>Digitális iker frissítése

Az IoT Plug and Play eszköz megvalósít egy modellt, amelyet a [Digital Twins Definition Language v2 (DTDL) ismertet.](https://github.com/Azure/opendigitaltwins-dtdl) A megoldásfejlesztők a **Digital Twins API frissítésével** frissítheti az összetevő állapotát és a digitális iker tulajdonságait.

A cikkben példaként Plug and Play IoT-eszköz a Temperature [Controller](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) modellt implementálja [Termosztát-összetevőkkel.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)

Az alábbi kódrészlet egy JSON-objektumként formázott **Digitális ikereszköz** lekérése kérelemre adott választ mutatja be. További információ a digitális ikerformátumról: [Az IoT-alkalmazások Plug and Play:](./concepts-digital-twin.md#digital-twin-example)

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

A digitális ikerekkel egy teljes összetevőt vagy tulajdonságot frissítheti [egy JSON-javítással.](http://jsonpatch.com/)

Például a következőképpen `targetTemperature` frissítheti a tulajdonságot:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

Az előző frissítés beállítja egy tulajdonság kívánt értékét a megfelelő összetevőszinten, az alábbi `$metadata` kódrészletben látható módon. IoT Hub frissíti a tulajdonság kívánt verzióját:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Összetevő hozzáadása, cseréje vagy eltávolítása

Az összetevőszintű műveletekhez üres objektumjelölőre van `$metadata` szükség az értéken belül.

Egy összetevő hozzáadása vagy cseréje művelet beállítja az összes megadott tulajdonság kívánt értékeit. Emellett törli a kívánt értékeket minden olyan írható tulajdonságnál, amely nem található meg a frissítésben.

Egy összetevő eltávolítása törli az összes írható tulajdonság kívánt értékeit. Az eszközök végül szinkronizálják ezt az eltávolítást, és nem jelentik az egyes tulajdonságokat. Ezután eltávolítja az összetevőt a digitális ikereszközből.

A következő JSON-javítási minta bemutatja, hogyan adhat hozzá, cserélheti le vagy távolíthat el egy összetevőt:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Tulajdonság hozzáadása, cseréje vagy eltávolítása

Egy hozzáadási vagy csere művelet beállítja egy tulajdonság kívánt értékét. Az eszköz szinkronizálhatja az állapotot, és jelentést készít az érték frissítéséről egy kóddal, verzióval `ack` és leírással együtt.

Egy tulajdonság eltávolítása törli a tulajdonság kívánt értékét, ha be van állítva. Az eszköz ezután leállíthatja a tulajdonság jelentését, és el lesz távolítva az összetevőből. Ha ez a tulajdonság az összetevő utolsó eleme, akkor az összetevő is el lesz távolítva.

A következő JSON-javítási minta bemutatja, hogyan adhat hozzá, cserélheti le vagy távolíthat el egy tulajdonságot egy összetevőn belül:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Digitális ikertulajdonság kívánt értékének beállítására vonatkozó szabályok

**Név**

Az összetevő vagy tulajdonság nevének érvényes DTDL v2 névnek kell lennie.

Megengedett karakterek: a-z, A-Z, 0-9 (nem az első karakter) és aláhúzásjel (nem az első vagy az utolsó karakter).

A név 1–64 karakter hosszú lehet.

**Tulajdonságérték**

Az értéknek érvényes [DTDL v2 tulajdonságnak kell lennie.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)

Minden primitív típus támogatott. Az összetett típusokon belül az enumok, térképek és objektumok támogatottak. További információ: [DTDL v2-sémák.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas)

A tulajdonságok nem támogatják a tömböt vagy a tömböt tartalmazó összetett sémákat.

Összetett objektumok esetén legfeljebb öt szint mélysége támogatott.

A komplex objektumon belül minden mezőnévnek érvényes DTDL v2-névnek kell lennie.

Minden térképkulcsnak érvényes DTDL v2-névnek kell lennie.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>A Digital Twin API frissítésével kapcsolatos hibák elhárítása

A digital twin API a következő általános hibaüzenetet jelenik meg:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Ha ezt a hibát látja, győződjön meg arról, hogy a frissítési javítás követi a digitális ikertulajdonság kívánt értékének [beállítására vonatkozó szabályokat](#rules-for-setting-the-desired-value-of-a-digital-twin-property)

Összetevő frissítésekor győződjön meg arról, hogy az üres objektum [$metadata jelölő be](#add-replace-or-remove-a-component) van állítva.

A frissítések meghiúsulnak, ha egy eszköz jelentett értékei nem felelnek meg az [IoT plug and play konvencióinak.](./concepts-convention.md#writable-properties)

## <a name="next-steps"></a>Következő lépések

Most, hogy már megtanulta a digitális ikereket, íme néhány további forrás:

- [Kommunikáció egy eszközzel a megoldásból](quickstart-service.md)
- [IoT Digital Twin REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
