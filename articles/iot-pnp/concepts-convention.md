---
title: IoT Plug and Play konvenciók | Microsoft Docs
description: A IoT konvenciók leírása Plug and Play a telemetria és a tulajdonságok elküldésére, valamint a parancsok és a tulajdonság-frissítések kezelésére szolgáló eszközök használatát várja.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a8dfb3cacf798559273361ec75cab4570a8a5228
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582680"
---
# <a name="iot-plug-and-play-conventions"></a>Az IoT Plug and Playhez kapcsolódó konvenciók

A IoT Plug and Play-eszközöknek olyan konvenciókat kell követniük, amikor üzeneteket cserélnek egy IoT hubhoz. A IoT Plug and Play-eszközök a MQTT protokollt használják a IoT Hub való kommunikációhoz.

Az eszközök tartalmazhatnak [modulokat](../iot-hub/iot-hub-devguide-module-twins.md), illetve a IoT Edge futtatókörnyezet által üzemeltetett [IoT Edge modulban](../iot-edge/about-iot-edge.md) is megvalósítható.

Ismerteti azokat a telemetria, tulajdonságokat és parancsokat, amelyeket egy IoT Plug and Play-eszköz a [digitális Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) _modellel_ implementál. Ebben a cikkben a modell két típusát említi:

- **Nincs összetevő** – olyan modell, amely nem rendelkezik összetevőkkel. A modell az telemetria, a tulajdonságokat és a parancsokat deklarálja legfelső szintű tulajdonságokként a fő felület tartalom szakaszában. Az Azure IoT Explorer eszközében ez a modell egyetlen _alapértelmezett összetevőként_ jelenik meg.
- **Több összetevő** – két vagy több illesztőfelületből álló modell. Egy fő felület, amely az _alapértelmezett összetevőként_ jelenik meg a telemetria, a Properties és a parancsokkal. Egy vagy több, összetevőként deklarált illesztőfelület további telemetria, tulajdonságokkal és parancsokkal.

További információ: [IoT Plug and Play modellezési útmutató](concepts-modeling-guide.md).

## <a name="identify-the-model"></a>A modell azonosítása

Ha be szeretné jelenteni az általa megvalósított modellt, egy IoT Plug and Play eszköz vagy modul tartalmazza a modell AZONOSÍTÓját a MQTT-kapcsolati csomagban a `model-id` mezőhöz való hozzáadásával `USERNAME` .

Az eszköz vagy modul által megvalósított modell azonosításához a szolgáltatás a következő helyről kérheti le a modell AZONOSÍTÓját:

- Az eszköz Twin `modelId` mezője
- A digitális dupla `$metadata.$model` mező.
- Egy digitális kettős változásról szóló értesítés.

## <a name="telemetry"></a>Telemetria

A nem telemetria eljuttatott eszközök nem igényelnek további metaadatokat. A szolgáltatás hozzáadja a `dt-dataschema` tulajdonságot.

A több összetevőből küldött telemetria az `$.sub` üzenet tulajdonságként kell hozzáadni. A System hozzáadja a `dt-subject` és a `dt-dataschema` tulajdonságokat.

## <a name="read-only-properties"></a>Csak olvasható tulajdonságok

### <a name="sample-no-component-read-only-property"></a>Minta nincs írásvédett összetevő tulajdonsága

Egy eszköz vagy modul bármilyen érvényes JSON-t küldhet, amely a DTDL v2 szabályait követi.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Példa jelentett tulajdonság hasznos adatai:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Több összetevő írásvédett tulajdonságának mintája

Az eszköznek vagy modulnak hozzá kell adnia a `{"__t": "c"}` jelölőt, hogy jelezze, hogy az elem egy összetevőre hivatkozik.

Összetevőre hivatkozó DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

Az összetevőt meghatározó DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Példa jelentett tulajdonság hasznos adatai:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Írható tulajdonságok

Az eszköznek vagy a modulnak meg kell győződnie arról, hogy a tulajdonságot egy jelentett tulajdonság elküldésével kapta. A jelentett tulajdonságnak a következőket kell tartalmaznia:

- `value` – a tulajdonság tényleges értéke (általában a fogadott érték, de az eszköz más érték bejelentését is eldöntheti).
- `ac` – egy HTTP-állapotkódot használó nyugtázási kód.
- `av` – a visszaigazolási verzió, amely a `$version` kívánt tulajdonságra hivatkozik. Ezt az értéket a kívánt tulajdonság JSON-adattartalomban találja.
- `ad` – a visszaigazolás nem kötelező leírása.

Ha egy eszköz elindul, az eszköznek külön kell kérnie az eszközt, és ellenőriznie kell az írható tulajdonságok frissítéseit. Ha egy írható tulajdonság verziója az eszköz offline állapotában megnőtt, az eszköznek egy jelentett tulajdonságot kell küldenie, hogy ellenőrizze, hogy a frissítés megérkezett-e.

Ha egy eszköz először indul el, akkor egy jelentett tulajdonság kezdeti értékét küldheti el, ha nem kap kezdeti kívánt tulajdonságot a központból. Ebben az esetben az eszköznek a következőre kell állítania: `av` `1` . Például:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

Egy eszköz a jelentett tulajdonsággal további információkat adhat meg a központnak. Az eszköz például több, folyamatban lévő üzenettel is reagálhat, például:

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

Amikor az eszköz eléri a célként megadott hőmérsékletet, a következő üzenetet küldi el:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

Egy eszköz hibát jelenthet, például:

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>Minta – az összetevő írható tulajdonsága nem

Ha egy eszköz több jelentett tulajdonságot kap egyetlen adattartalomban, akkor a jelentett tulajdonságokat több hasznos adat között is elküldheti.

Egy eszköz vagy modul bármilyen érvényes JSON-t küldhet, amely a DTDL v2 szabályait követi:

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Példa a kívánt tulajdonság-adattartalomra:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

Példa jelentett tulajdonság első hasznos adat:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Példa jelentett tulajdonság második hasznos adat:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>Több összetevő írható tulajdonságának mintája

Az eszköznek vagy modulnak hozzá kell adnia a `{"__t": "c"}` jelölőt, hogy jelezze, hogy az elem egy összetevőre hivatkozik.

A jelölő csak az adott összetevőben definiált tulajdonságok frissítéseire lesz elküldve. Az alapértelmezett összetevőben definiált tulajdonságok frissítései nem tartalmazzák a jelölőt, lásd: az [összetevő nem írható tulajdonságának mintája](#sample-no-component-writable-property)

Ha egy eszköz több jelentett tulajdonságot kap egyetlen adattartalomban, akkor a jelentett tulajdonságokat több hasznos adat között is elküldheti.

Az eszköznek vagy a modulnak meg kell győződnie arról, hogy a jelentett tulajdonságok küldésével megkapta a tulajdonságokat:

Összetevőre hivatkozó DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

Az összetevőt meghatározó DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Példa a kívánt tulajdonság-adattartalomra:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

Példa jelentett tulajdonság első hasznos adat:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Példa jelentett tulajdonság második hasznos adat:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>Parancsok

Az összetevő-illesztőfelületek nem a parancs nevét használják előtag nélkül.

Egy eszközön vagy modulon több összetevő-illesztőfelület is a következő formátumú parancsokat használja: `componentName*commandName` .

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a IoT Plug and Play konvenciókat, néhány további erőforrást is talál:

- [A digitális ikerpéldányok definíciós nyelve (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C eszköz SDK](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](/rest/api/iothub/device)
- [IoT Plug and Play modellezési útmutató](concepts-modeling-guide.md)