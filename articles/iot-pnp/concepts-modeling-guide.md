---
title: A IoT Plug and Play eszköz modelljeinek ismertetése | Microsoft Docs
description: A IoT Plug and Play-eszközök digitális Twins Definition Language (DTDL) modellezési nyelvének megismerése. A cikk a primitív és az összetett adattípusokat, az összetevőket és az öröklést használó mintákat, valamint a szemantikai típusokat ismerteti. A cikk útmutatást nyújt az eszköz kettős modell-azonosítójának kiválasztásához és a modell-létrehozási eszközök támogatásához.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 85888370106f34c723be4e47738114f7df33dcf4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057473"
---
# <a name="iot-plug-and-play-modeling-guide"></a>IoT Plug and Play modellezési útmutató

A IoT Plug and Play egy olyan eszköz _modellje_ , amely leírja az eszköz képességeit egy IoT Plug and Play-kompatibilis alkalmazás számára. Ez a modell illesztőfelület-készletként van strukturálva, amely az alábbiakat határozza meg:

- Egy eszköz vagy más entitás írásvédett vagy írható állapotát jelképező _Tulajdonságok_ . Előfordulhat például, hogy egy eszköz sorozatszáma csak olvasható tulajdonság, és a termosztát hőmérséklete írható tulajdonság lehet.
- Az eszköz által kibocsátott adatokat meghatározó _telemetria_ -mezők, hogy az adatok a szenzorok normál streamje, egy alkalmi hiba vagy egy tájékoztató üzenet.
- Egy eszközön elvégezhető függvényt vagy műveletet leíró _parancsok_ . Egy parancs például újraindíthatja az átjárót, vagy egy távoli kamera használatával is készíthet képet.

Ha többet szeretne megtudni arról, hogyan használja a IoT Plug and Play az eszközök modelljeit, tekintse meg a [IoT Plug and Play-eszköz fejlesztői útmutatóját](concepts-developer-guide-device.md) és a [IoT Plug and Play Service fejlesztői útmutatót](concepts-developer-guide-service.md).

Modell definiálásához használja a digitális Twins Definition Language (DTDL) nyelvet. A DTDL [JSON-ld](https://json-ld.org/)nevű JSON-változatot használ. Az alábbi kódrészlet egy olyan termosztát-eszköz modelljét mutatja be, amely:

- Egyedi modell-AZONOSÍTÓval rendelkezik: `dtmi:com:example:Thermostat;1` .
- Hőmérséklet-telemetria küld.
- Egy írható tulajdonsága a célként megadott hőmérséklet beállítására.
- A csak olvasható tulajdonsággal jelentheti a maximális hőmérsékletet az utolsó újraindítás óta.
- Egy olyan parancsra válaszol, amely egy adott időszakban maximális, minimális és átlagos hőmérsékletet kér.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
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
        "name": "tempReport",
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
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

A termosztát modell egyetlen illesztőfelülettel rendelkezik. A jelen cikk későbbi példái az összetevőket és öröklést használó összetettebb modelleket mutatják be.

Ez a cikk bemutatja, hogyan tervezhet és hozhat létre saját modelleket, és hogyan fedi le az olyan témákat, mint például az adattípusok, a modell szerkezete és az eszközök.

További tudnivalókat a [digitális Twins Definition Language v2](https://github.com/Azure/opendigitaltwins-dtdl) specifikációja című témakörben talál.

## <a name="model-structure"></a>Modellstruktúra

A tulajdonságok, a telemetria és a parancsok felületekbe vannak csoportosítva. Ez a szakasz azt ismerteti, hogyan használhatók a kezelőfelületek egyszerű és összetett modellek leírásához összetevők és öröklés használatával.

### <a name="model-ids"></a>Modell-azonosítók

Minden felület egyedi digitális kettős modell-azonosítóval (DTMI) rendelkezik. Az összetett modellek a DTMIs használják az összetevők azonosítására. Az alkalmazások használhatják azt a DTMIs, amellyel az eszközök elküldik a modell definícióinak megkeresését egy adattárban.

A DTMIs követnie kell az [IoT Plug and Play Model adattárához](https://github.com/Azure/iot-plugandplay-models)szükséges elnevezési konvenciót:

- A DTMI előtag: `dtmi:` .
- Az DTMI utótag a modell verziószáma, például: `;2` .
- A DTMI törzse arra a mappára és fájlra mutat, amelyben a modellt tároló adattár található. A verziószám a fájlnév részét képezi.

A DTMI által azonosított modellt például a `dtmi:com:Example:Thermostat;2` *DTMI/com/example/thermostat-2.js* fájl tárolja.

Az alábbi kódrészlet egy illesztőfelület-definíció vázlatát mutatja be egyedi DTMI:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Nincsenek összetevők

Egy egyszerű modell, például a korábban bemutatott termosztát nem használ beágyazott vagy lépcsőzetes összetevőket. A telemetria, a tulajdonságok és a parancsok a `contents` csatoló csomópontjában vannak meghatározva.

Az alábbi példa egy olyan egyszerű modell részét mutatja be, amely nem használ összetevőket:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Az Azure IoT Explorer és a IoT Central eszköz sablonjának tervezője egy különálló felületet, például a termosztátot _alapértelmezett összetevőként_ címkézi.

Az alábbi képernyőképen látható, hogyan jelenik meg a modell az Azure IoT Explorer eszközben:

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Alapértelmezett összetevő az Azure IoT Explorerben":::

Az alábbi képernyőfelvételen látható, hogyan jelenik meg a modell alapértelmezett összetevőként a IoT Central eszköz sablon-tervezőben. Válassza az **identitás megtekintése** lehetőséget a modell DTMI megtekintéséhez:

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="A IoT Central Designerben a termosztát modelljét ábrázoló képernyőfelvétel":::

A modell AZONOSÍTÓját a Device Twin tulajdonság tárolja, amely a következő képernyőképet mutatja:

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="Modell azonosítója a digitális Twin tulajdonságban":::

Az összetevők nélküli DTDL modellek hasznos egyszerűsítést biztosítanak egy eszközhöz vagy egy IoT Edge modulhoz, amely egyetlen telemetria, tulajdonsággal és paranccsal rendelkezik. Az összetevőket nem használó modellek egyszerűen áttelepíthetik egy meglévő eszközt vagy modult IoT Plug and Play eszközre vagy modulra – létrehoz egy DTDL modellt, amely leírja a tényleges eszközt vagy modult anélkül, hogy meg kellene határoznia az összetevőket.

> [!TIP]
> A modul lehet egy eszköz [modul](../iot-hub/iot-hub-devguide-module-twins.md) vagy egy [IoT Edge modul](../iot-edge/about-iot-edge.md).

### <a name="reuse"></a>Újrafelhasználás

Az illesztőfelület-definíciók újbóli használatának két módja van. Használjon több összetevőt a modellben más illesztőfelület-definíciók hivatkozásához. Használja az öröklést a meglévő illesztőfelület-definíciók kiterjesztéséhez.

### <a name="multiple-components"></a>Több összetevő

Az összetevők lehetővé teszik a modell felületének összeállítását más felületek szerelvényként.

A [termosztát](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) interfész például modellként van definiálva. Ezt a felületet a [hőmérséklet-vezérlő modell](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)meghatározásakor egy vagy több összetevőbe is beépítheti. A következő példában a rendszer ezeket az összetevőket hívja meg `thermostat1` és `thermostat2` .

Több összetevővel rendelkező DTDL-modell esetén két vagy több összetevőből álló szakaszt tartalmaz. Mindegyik szakasz a (z) értékre van `@type` állítva, `Component` és explicit módon hivatkozik egy sémára, ahogy az a következő kódrészletben látható:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Ebben a modellben három összetevő van definiálva a tartalom szakaszban – két `Thermostat` összetevő és egy `DeviceInformation` összetevő. A tartalom szakaszban a tulajdonságok, a telemetria és a parancssori definíciók is szerepelnek.

A következő képernyőképek azt mutatják be, hogyan jelennek meg a modell a IoT Centralban. A hőmérséklet-vezérlőben a tulajdonság, a telemetria és a parancs definíciói a legfelső szintű **alapértelmezett összetevőben** jelennek meg. Az egyes termosztátok tulajdonság-, telemetria-és parancssori definíciói az összetevők definíciójában jelennek meg:

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="Képernyőfelvétel: IoT Centralban található hőmérséklet-vezérlő eszköz sablonja.":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="Képernyőfelvétel: a termosztát összetevői a IoT Central hőmérséklet-vezérlési eszköz sablonjában.":::

Az összetevőkkel kommunikáló eszköz kódjának megírásával kapcsolatban lásd: [IoT Plug and Play eszköz fejlesztői útmutatója](concepts-developer-guide-device.md).

A következő témakörben megtudhatja, hogyan írhat olyan programkódot, amely az eszközökön található összetevőkkel rendelkezik: [IoT Plug and Play Service fejlesztői útmutató](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Öröklődés

Az öröklés lehetővé teszi, hogy egy alapcsatoló képességeit újra felhasználva kiterjessze az illesztőfelület képességeit. Például számos eszköz-modell képes megosztani a gyakori képességeket, például a sorozatszámot:

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Példa egy eszköz-modellben lévő öröklésre, amely egy olyan termosztátot és egy adatáramlási vezérlőt mutat be, amely egy alapszintű csatolón belül" border="false":::

Az alábbi kódrészlet egy DTML modellt mutat be, amely a `extends` kulcsszó használatával határozza meg az előző ábrán látható öröklési kapcsolatot:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

A következő képernyőkép a modellt mutatja be a IoT Central eszköz sablonjának környezetében:

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="A felület öröklődését bemutató képernyőkép IoT Central":::

Az eszköz vagy a szolgáltatás-oldali kód írásakor a kódnak nem kell semmi különlegeset tennie az örökölt felületek kezeléséhez. Az ebben a szakaszban bemutatott példában az eszköz kódja úgy jeleníti meg a sorozatszámot, mintha az a termosztát felületének része legyen.

### <a name="tips"></a>Tippek

A modellek létrehozásakor kombinálhatja az összetevőket és az öröklést is. Az alábbi ábrán egy `thermostat` felületről örökölt modell látható `baseDevice` . Az `baseDevice` illesztőfelület egy összetevővel rendelkezik, amely maga örökli egy másik csatolótól:

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Az összetevőket és az öröklést egyaránt használó modellt bemutató diagram." border="false":::

Az alábbi kódrészlet egy DTML modellt mutat be, amely a `extends` és a `component` kulcsszavakat használja az öröklési kapcsolat és az előző ábrán látható összetevők használatának definiálásához:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Adattípusok

Az adattípusok segítségével definiálhatja a telemetria, a tulajdonságokat és a parancs paramétereit. Az adattípusok lehetnek primitívek vagy bonyolultak. Az összetett adattípusok primitíveket vagy más összetett típusokat használnak. Az összetett típusok maximális mélysége öt szint.

### <a name="primitive-types"></a>Primitív típusok

A következő táblázat a használható primitív típusok készletét tartalmazza:

| Primitív típus | Leírás |
| --- | --- |
| `boolean` | Logikai érték |
| `date` | Az [RFC 3339 5,6-es szakaszában](https://tools.ietf.org/html/rfc3339#section-5.6) meghatározott teljes dátum |
| `dateTime` | Az [RFC 3339](https://tools.ietf.org/html/rfc3339) -ben meghatározott dátum-idő |
| `double` | IEEE 8 bájtos lebegőpontos pont |
| `duration` | ISO 8601 formátumú időtartam |
| `float` | IEEE 4 bájtos lebegőpontos |
| `integer` | Aláírt 4 bájtos egész szám |
| `long` | Aláírt 8 bájtos egész szám |
| `string` | Egy UTF8-karakterlánc |
| `time` | Az [RFC 3339 5,6-es szakaszában](https://tools.ietf.org/html/rfc3339#section-5.6) meghatározott teljes idő |

Az alábbi kódrészlet egy példa telemetria-definíciót mutat be, amely a következő `double` `schema` mezőt használja:

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Összetett adattípusok

Az összetett adattípusok *tömb*, *enumerálás*, *Térkép*, *objektum* vagy a földrajzi típusok egyike.

#### <a name="arrays"></a>Tömbök

A tömb egy indexelhető adattípus, amelyben az összes elem azonos típusú. Az elem típusa lehet primitív vagy összetett típus.

Az alábbi kódrészlet egy példa telemetria-definíciót mutat be, amely a `Array` mezőben szereplő típust használja `schema` . A tömb elemei logikai értékek:

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Enumerálások

Az enumerálás egy olyan típust ír le, amely az értékeket képező névvel ellátott címkék halmazát tartalmazza. Az értékek lehetnek egész számok vagy karakterláncok, de a feliratok mindig karakterláncok.

Az alábbi kódrészlet egy példa telemetria-definíciót mutat be, amely a `Enum` mezőben szereplő típust használja `schema` . Az enumerálásban szereplő értékek egész számok:

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Maps

A Térkép olyan kulcs-érték párokkal rendelkező típus, ahol az összes érték azonos típusú. A térképben szereplő kulcsnak karakterláncnak kell lennie. A Térkép értékei bármilyen típusúak lehetnek, beleértve egy másik összetett típust is.

Az alábbi kódrészlet egy példa tulajdonság-definíciót mutat be, amely a `Map` mezőben szereplő típust használja `schema` . A Térkép értékei sztringek:

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Objektumokat

Az Objektumtípusok elnevezett mezőkből állnak. Az objektum-hozzárendelés mezőinek típusa primitív vagy összetett típus lehet.

Az alábbi kódrészlet egy példa telemetria-definíciót mutat be, amely a `Object` mezőben szereplő típust használja `schema` . Az objektum mezői a következők `dateTime` :, `duration` és `string` típusok:

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Térinformatikai típusok

A DTDL térinformatikai típusokat biztosít a [GeoJSON](https://geojson.org/)alapján a következő modellezési földrajzi adatstruktúrákhoz:,,,, `point` `multiPoint` `lineString` `multiLineString` `polygon` és `multiPolygon` . Ezek a típusok a tömbök, objektumok és enumerálások előre beágyazott szerkezetét alkotják.

Az alábbi kódrészlet egy példa telemetria-definíciót mutat be, amely a következő `point` `schema` mezőt használja:

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Mivel a térinformatikai típusok Array-alapúak, jelenleg nem használhatók a tulajdonságok definíciójában.

## <a name="semantic-types"></a>Szemantikai típusok

Egy tulajdonság-vagy telemetria-definíció adattípusa határozza meg az eszköz által szolgáltatással való kommunikációhoz használt adatformátumot. A szemantikai típus információt nyújt azokról a telemetria és tulajdonságokról, amelyeket az alkalmazás használhat az értékek feldolgozásának vagy megjelenítésének meghatározásához. Minden szemantikai típushoz egy vagy több társított egység tartozik. Például a Celsius és a Fahrenheit a hőmérséklet szemantikai típusának egysége. IoT Central az irányítópultok és az elemzések a szemantikai típussal kapcsolatos információk segítségével határozzák meg a telemetria és a tulajdonságértékek és a megjelenítési egységek ábrázolásának módját. Ha szeretné megtudni, hogyan használhatja a modell-elemzőt a szemantikai típusok olvasásához, olvassa el [a digitális Twins modell-elemző ismertetése](concepts-model-parser.md)című témakört.

Az alábbi kódrészlet egy példa telemetria-definíciót mutat be, amely szemantikai típusú adatokat tartalmaz. A rendszer hozzáadja a szemantikai típust `Temperature` a `@type` tömbhöz, és az `unit` érték az `degreeCelsius` egyik érvényes egység a szemantikai típushoz:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Honosítás

Az alkalmazások, például a IoT Central, a modellben található információk használatával dinamikusan hozhatnak létre felhasználói felületet a IoT Plug and Play eszközzel cserélt adatok köré. Az irányítópult csempéi például megjeleníthetik a telemetria, a tulajdonságok és a parancsok nevét és leírását.

A `description` modellben lévő opcionális és `displayName` mezők a felhasználói felületen való használatra szánt karakterláncokat tartanak. Ezek a mezők olyan honosított karakterláncokat tárolhatnak, amelyeket az alkalmazás a honosított felhasználói felület megjelenítésére használhat.

Az alábbi kódrészlet egy példaként szolgáló telemetria-definíciót mutat be, amely honosított karakterláncokat tartalmaz:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

Honosított karakterláncok hozzáadása nem kötelező. A következő példa csak egyetlen, alapértelmezett nyelvet tartalmaz:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>Életciklus és eszközök

Az eszköz modellének négy életciklusi szakasza a szerzői, a közzététel, a használat és a verziószámozás:

### <a name="author"></a>Szerző

A DTML a szövegszerkesztőben létrehozható JSON-dokumentumok. IoT Central azonban a DTML-modell létrehozásához használhatja az eszköz sablonjának GUI-környezetét. IoT Central a következőket teheti:

- Tulajdonságokat, telemetria és parancsokat definiáló illesztőfelületek létrehozása.
- Az összetevők együttes használatával több felületet is összeállíthat.
- Öröklődési kapcsolatok definiálása a felületek között.
- DTML-modell fájljainak importálása és exportálása.

További információért lásd: [új IoT-eszköz típusának meghatározása az Azure IoT Central alkalmazásban](../iot-central/core/howto-set-up-template.md).

A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) -hoz készült DTDL-szerkesztő lehetővé teszi egy szöveges szerkesztési környezet szintaxisának érvényesítését és automatikus kiegészítését a modell-létrehozási élmény pontosabb szabályozása érdekében.

### <a name="publish"></a>Közzététel

Ahhoz, hogy a DTML-modelljei megoszthatók és felderíthetők legyenek, egy eszköz-modell tárházban teheti közzé őket.

Mielőtt közzéteszi a modellt a nyilvános tárházban, használhatja az eszközöket a `dmr-client` modell érvényesítéséhez.

További információért lásd: az [eszköz modelljeinek tárháza](concepts-model-repository.md).

### <a name="use"></a>Használat

Az alkalmazások, például a IoT Central, az eszközök modelljeit használják. A IoT Centralban a modell a sablon azon része, amely leírja az eszköz képességeit. A IoT Central az eszköz sablonjának használatával dinamikusan felépíti az eszköz felhasználói felületét, beleértve az irányítópultokat és az elemzéseket.

Egy egyéni megoldás használhatja a [digitális Twins modell-elemzőt](concepts-model-parser.md) a modellt megvalósító eszközök képességeinek megismerésére. További információ: [IoT Plug and Play modellek használata egy IoT-megoldásban](concepts-model-discovery.md).

### <a name="version"></a>Verzió

Annak biztosítása érdekében, hogy a modelleket használó eszközök és kiszolgálóoldali megoldások továbbra is működőképesek legyenek, a közzétett modellek nem változtathatók meg.

A DTMI tartalmaz egy verziószámot, amely segítségével a modell több verzióját is létrehozhatja. Az eszközök és a kiszolgálóoldali megoldások használhatják a használni kívánt adott verziót.

IoT Central implementálja az eszközök modelljeinek további verziószámozási szabályait. Ha az eszköz sablonját és modelljét IoT Central-ben telepíti, a korábbi verziókról újabb verzióra telepíthet át eszközöket. Az áttelepített eszközök azonban nem tudnak új képességeket használni a belső vezérlőprogram frissítése nélkül. További információért lásd: [új eszköz sablon verziójának létrehozása](../iot-central/core/howto-version-device-template.md).

## <a name="limits-and-constraints"></a>Korlátok és megkötések

Az alábbi lista néhány fontos korlátozást és a modellekre vonatkozó korlátozásokat foglalja össze:

- Jelenleg a tömbök, a térképek és az objektumok maximális mélysége öt mélységi szint.
- Nem használhat tömböket a tulajdonságok definíciójában.
- A felületek kiterjeszthetők 10 szint mélységére.
- Egy felület legfeljebb két másik felületen bővíthető.
- Egy összetevő nem tartalmazhat másik összetevőt.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az eszközök modellezését, íme néhány további erőforrás:

- [A DTDL authoring Tools telepítése és használata](howto-use-dtdl-authoring-tools.md)
- [Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Modell adattárai](./concepts-model-repository.md)
