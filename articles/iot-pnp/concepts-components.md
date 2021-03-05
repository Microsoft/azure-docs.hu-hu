---
title: Az összetevők ismertetése a IoT Plug and Play-modellekben | Microsoft Docs
description: Ismerje meg a IoT Plug and Play DTDL modellek közötti különbséget, amelyek olyan összetevőket és modelleket használnak, amelyek nem használnak összetevőket.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: eef8179567d83e3727c3ab949eef2706ce2a9b16
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175804"
---
# <a name="iot-plug-and-play-components-in-models"></a>Modellekben lévő IoT Plug and Play-összetevők

Az IoT Plug and Playhez kapcsolódó konvenciók szerint az eszköz egy IoT Plug and Play-eszköz, ha bemutatja a DTDL- (Digital Twins Definition Language-) modellazonosítóját, amikor IoT hubhoz csatlakozik.

Az alábbi kódrészlet a modellazonosítók néhány példáját mutatják be:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Nincsenek összetevők

Egy egyszerű modell nem használ beágyazott vagy lépcsőzetes összetevőket. A fejléc információi és a tartalom szakasza tartalmazza a telemetria, a tulajdonságokat és a parancsokat.

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

Bár a modell nem definiál explicit módon egy összetevőt, úgy viselkedik, mintha egyetlen, _alapértelmezett összetevő_ van, az összes telemetria, tulajdonsággal és parancs-definícióval.

Az alábbi képernyőképen látható, hogyan jelenik meg a modell az Azure IoT Explorer eszközben:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Alapértelmezett összetevő az Azure IoT Explorerben":::

A modell AZONOSÍTÓját a Device Twin tulajdonság tárolja, amely a következő képernyőképet mutatja:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="Modell azonosítója a digitális Twin tulajdonságban":::

Az összetevők nélküli DTDL modellek hasznos egyszerűsítést biztosítanak egy eszközhöz vagy IoT Edge modulhoz, amely egyetlen telemetria, tulajdonsággal és paranccsal rendelkezik. Az összetevőket nem használó modellek egyszerűen áttelepíthetik egy meglévő eszközt vagy modult IoT Plug and Play eszközre vagy modulra – létrehoz egy DTDL modellt, amely leírja a tényleges eszközt vagy modult anélkül, hogy meg kellene határoznia az összetevőket.

> [!TIP]
> A modul lehet egy eszköz [modul](../iot-hub/iot-hub-devguide-module-twins.md) vagy egy [IoT Edge modul](../iot-edge/about-iot-edge.md).

## <a name="multiple-components"></a>Több összetevő

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
...
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
...
```

Ebben a modellben három összetevő van definiálva a tartalom szakaszban – két `Thermostat` összetevő és egy `DeviceInformation` összetevő. Létezik egy alapértelmezett összetevő is.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte a modell-összetevőket, néhány további erőforrást is talál:

- [A DTDL authoring Tools telepítése és használata](howto-use-dtdl-authoring-tools.md)
- [Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Modell adattárai](./concepts-model-repository.md)
