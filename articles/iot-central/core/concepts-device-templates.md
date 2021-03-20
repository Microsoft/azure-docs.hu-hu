---
title: Mik az Azure IoT Centralban található eszközök sablonjai | Microsoft Docs
description: Az Azure IoT Central-sablonjai lehetővé teszik az alkalmazáshoz csatlakoztatott eszközök viselkedésének megadását. Az telemetria az eszköz által megvalósított eszközöket, tulajdonságokat és parancsokat határozza meg. Az eszköz sablonja az eszköz felhasználói felületét is meghatározza IoT Central például az operátor által használt űrlapokat és irányítópultokat.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 04c2330ffee396f5fc30b85640e992df77c08263
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97795428"
---
# <a name="what-are-device-templates"></a>Mik azok az eszközsablonok?

_Ez a cikk az eszközök fejlesztőire és megoldás-építői vonatkozik._

Az Azure IoT Central egy olyan tervrajz, amely az alkalmazáshoz csatlakozó eszközök jellemzőit és viselkedését határozza meg. Az eszköz sablonja például meghatározza azt a telemetria, amelyet az eszköz küld, így a IoT Central a megfelelő egységeket és adattípusokat használó vizualizációkat hozhat létre.

A megoldás-szerkesztő egy IoT Central alkalmazáshoz adja hozzá az eszközök sablonjait. Az eszközök fejlesztője az eszköz kódját írja le, amely megvalósítja az eszköz sablonjában definiált viselkedéseket.

Az eszköz sablonjai a következő szakaszt tartalmazzák:

- _Egy eszköz modellje_. Az eszköz ezen része határozza meg, hogy az eszköz hogyan kommunikál az alkalmazással. Az eszközök fejlesztői implementálják a modellben definiált viselkedéseket.
    - _Alapértelmezett összetevő_. Minden eszköz-modellhez tartozik egy alapértelmezett összetevő. Az alapértelmezett összetevő felülete az eszköz modelljére jellemző képességeket ismerteti.
    - _Összetevők_. Az eszköz modellje tartalmazhatja az alapértelmezett összetevő mellett az eszközök képességeinek leírására szolgáló összetevőket is. Minden összetevőhöz tartozik egy felület, amely leírja az összetevő képességeit. Az összetevő-felületek más eszközökön is felhasználhatók. Például több telefonos eszköz modell is használhatja ugyanazt a kamera-felületet.
    - _Örökölt felületek_. Az eszköz modellje egy vagy több olyan felületet tartalmaz, amelyek kibővítik az alapértelmezett összetevő képességeit.
- _Felhő tulajdonságai_ A sablon ezen része lehetővé teszi, hogy a megoldás fejlesztője a tárolni kívánt eszköz-metaadatokat határozza meg. A felhő tulajdonságai soha nem szinkronizálhatók az eszközökkel, és csak az alkalmazásban léteznek. A felhő tulajdonságai nem érintik azt a kódot, amelyet az eszköz fejlesztője az eszköz modellének megvalósítására ír.
- _Testreszabások_. Az eszközbeállítások ezen része lehetővé teszi, hogy a megoldás fejlesztője felülbírálja az eszköz modellje definícióit. A testreszabások akkor hasznosak, ha a megoldás fejlesztője szeretné megszabni, hogy az alkalmazás hogyan kezelje az értéket, például egy tulajdonság megjelenített nevének vagy a telemetria értékének megjelenítéséhez használt színnek a módosítását. A testreszabások nem érintik azt a kódot, amelyet az eszköz fejlesztője az eszköz modellének megvalósítására ír.
- _Nézetek_. Az eszközbeállítások ezen része lehetővé teszi, hogy a megoldás fejlesztője a vizualizációkat definiálja az eszköz adatainak megtekintéséhez, valamint az eszközök felügyeletére és vezérlésére szolgáló űrlapokat. A nézetek az eszköz modelljét, a felhő tulajdonságait és a testreszabásokat használják. A nézetek nem érintik azt a kódot, amelyet az eszköz fejlesztője az eszköz modellének megvalósítására ír.

## <a name="device-models"></a>Eszközmodellek

Az eszköz modellje határozza meg, hogy egy eszköz hogyan kommunikál a IoT Central alkalmazással. Az eszköz fejlesztőinek meg kell győződnie arról, hogy az eszköz megvalósítja az eszköz modelljében definiált viselkedéseket, hogy IoT Central tudja figyelni és kezelni az eszközt. Az eszköz modellje egy vagy több _interfészből_ áll, és mindegyik csatoló meghatározhatja a _telemetria_ -típusok, az _eszköz tulajdonságai_ és a _parancsok_ gyűjteményét. A megoldás fejlesztője importálhat egy olyan JSON-fájlt, amely az eszköz modelljét definiálja egy eszköz sablonjában, vagy a IoT Central webes felhasználói felületén keresztül létrehozhatja vagy szerkesztheti az eszköz modelljét. A webes felhasználói felülettel végrehajtott modell módosítása megköveteli az [eszköz verziószámozását](./howto-version-device-template.md).

A megoldás fejlesztője is exportálhat egy JSON-fájlt, amely tartalmazza az eszköz modelljét. Egy eszköz fejlesztője ezt a JSON-dokumentumot arra használhatja, hogy megtudja, hogyan kommunikál az eszköz a IoT Central alkalmazással.

Az eszköz modelljét definiáló JSON-fájl a [Digital Twin Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)protokollt használja. IoT Central azt várja, hogy a JSON-fájl tartalmazza az eszköz modelljét a beágyazott illesztőfelületekkel, nem pedig külön fájlokban.

Egy tipikus IoT-eszköz a következőket alkotja:

- Egyéni részek, amelyek az eszköz egyedivé tételét teszik elérhetővé.
- Szabványos részek, amelyek az összes eszközre jellemző dolgok.

Ezeket a részeket _illesztőfelületeknek_ nevezzük az eszköz modelljében. A felületek határozzák meg az eszköz által megvalósított egyes részek részleteit. Az illesztőfelületek az eszközök modelljei között újrafelhasználhatók. A DTDL egy összetevő egy különálló DTDL-fájlban definiált illesztőfelületre hivatkozik.

Az alábbi példa egy hőmérséklet-vezérlő eszköz eszköz-modelljének körvonalát mutatja be. Az alapértelmezett összetevő a, a és a definícióit tartalmazza `workingSet` `serialNumber` `reboot` . Az eszköz modellje a és a `thermostat` `deviceInformation` felületekre is kiterjed:

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
        "Telemetry", "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit" : "kibibyte"
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
      "name": "thermostat",
      "displayName": "Thermostat",
      "description": "Thermostat One."
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

Az illesztőfelületnek van néhány kötelező mezője:

- `@id`: egyedi azonosító egyszerű egységes erőforrás-név formájában.
- `@type`: kijelenti, hogy ez az objektum illesztőfelület.
- `@context`: az illesztőfelülethez használt DTDL-verziót adja meg.
- `contents`: az eszközt alkotó tulajdonságok, telemetria és parancsok felsorolása. A képességek több felületen is meghatározhatók.

Vannak olyan választható mezők, amelyek segítségével további részleteket adhat hozzá a képesség modelljéhez, például a megjelenítendő név és a Leírás lehetőséghez.

A megvalósítások szakaszban található felületek listájának minden bejegyzése a következőket tartalmazza:

- `name`: az interfész programozási neve.
- `schema`: az a felület, amelyet a képesség modell implementál.

## <a name="interfaces"></a>Interfészek

A DTDL lehetővé teszi az eszköz képességeinek leírását. A kapcsolódó képességek felületekbe vannak csoportosítva. A felületek leírják, hogy a tulajdonságok, a telemetria és a parancsok hogyan implementálják az eszköz részét:

- `Properties`. A tulajdonságok olyan adatmezők, amelyek az eszköz állapotát jelölik. A tulajdonságok használatával az eszköz tartós állapotát, például egy Hűtőfolyadék-szivattyú on-off állapotát jelölheti ki. A tulajdonságok az alapszintű eszköz tulajdonságait is jelezhetik, például az eszköz belső vezérlőprogram-verzióját. A tulajdonságokat csak olvasható vagy írható módon deklarálhatja. Csak olvasható tulajdonság értékének frissítése csak az eszközökön végezhető el. Az operátor beállíthatja egy írható tulajdonság értékét az eszközre történő küldéshez.
- `Telemetry`. A telemetria mezők az érzékelők méréseit jelölik. Minden alkalommal, amikor az eszköz érzékelőt használ, egy telemetria eseményt kell küldenie, amely az érzékelő adatait tartalmazza.
- `Commands`. A parancsok olyan metódusokat jelölnek, amelyeket az eszköz felhasználói futtathatnak az eszközön. Például egy alaphelyzetbe állítási parancs vagy egy, a ventilátor be-és kikapcsolására szolgáló parancs.

A következő példa a termosztát interfész definícióját mutatja be:

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
      "displayName" : "Temperature",
      "description" : "Temperature in degrees Celsius.",
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
  ]
}
```

Ez a példa két tulajdonságot (egy írásvédett és egy írható), egy telemetria-típust és egy parancsot mutat be. A mező minimális leírása a következőket tartalmazhatja:

- `@type` a képesség típusának megadása: `Telemetry` , `Property` , vagy `Command` .  Bizonyos esetekben a típus egy szemantikai típust tartalmaz, amely lehetővé teszi, hogy IoT Central az érték kezelésével kapcsolatos feltételezések elvégzéséhez.
- `name` a telemetria értékhez.
- `schema` a telemetria vagy a tulajdonság adattípusának megadása. Ez az érték egyszerű típus lehet, például dupla, Integer, Boolean vagy string. Az összetett objektumtípusok és leképezések is támogatottak.

Az opcionális mezők, például a megjelenítendő név és a Leírás lehetővé teszik, hogy további részleteket adjon hozzá az interfészhez és a képességekhez.

## <a name="properties"></a>Tulajdonságok

Alapértelmezés szerint a tulajdonságok csak olvashatók. A csak olvasható tulajdonságok érték azt jelenti, hogy az eszköz a IoT Central alkalmazásra frissíti a tulajdonság értékét. A IoT Central alkalmazás nem állíthatja be a csak olvasható tulajdonság értékét.

Egy tulajdonságot egy felületen írhatóként is megadhat. Az eszközök frissíthetik a IoT Central alkalmazásból származó írható tulajdonságot, valamint az alkalmazásra vonatkozó jelentéskészítési tulajdonságértékek frissítését is.

Az eszközöknek nem kell csatlakozniuk a tulajdonságértékek beállításához. A frissített értékek akkor lesznek átadva, amikor az eszköz a következőhöz csatlakozik az alkalmazáshoz. Ez a viselkedés a csak olvasható és írható tulajdonságokra is vonatkozik.

Ne használjon tulajdonságokat a telemetria az eszközről való küldéséhez. Például egy írásvédett tulajdonság például `temperatureSetting=80` azt jelenti, hogy az eszköz hőmérséklete 80 értékre van állítva, és az eszköz megpróbál bejutni vagy maradni, ez a hőmérséklet.

Az írható tulajdonságok esetében az eszköz a kívánt állapot kódját, verzióját és leírását adja vissza annak jelzésére, hogy a kapott és alkalmazta-e a tulajdonság értékét.

## <a name="telemetry"></a>Telemetria

IoT Central segítségével megtekintheti az irányítópultokon és diagramokon lévő telemetria, és a szabályok segítségével elindíthatja a műveleteket, ha elérik a küszöbértékeket. A IoT Central az eszköz modelljében szereplő adatokat, például adattípusokat, egységeket és megjelenítendő neveket használja a telemetria-értékek megjelenítésének meghatározásához.

A IoT Central adatexportálási szolgáltatással továbbíthatja a telemetria más célhelyekre, például a Storage-ba vagy a Event Hubsba.

## <a name="commands"></a>Parancsok

A parancsnak alapértelmezés szerint 30 másodpercen belül kell futnia, és az eszköznek csatlakoztatva kell lennie a parancs megérkezése után. Ha az eszköz időben válaszol, vagy az eszköz nincs csatlakoztatva, akkor a parancs sikertelen lesz.

A parancsok lekérdezési paramétereket tartalmazhatnak, és választ adhatnak vissza.

### <a name="offline-commands"></a>Offline parancsok

Ha egy eszköz jelenleg offline állapotban van, a várólista-utasítások közül választhat, ha az eszközön **Offline** lehetőség van egy parancsra az eszköz sablonjában.

Az offline parancsok egyirányú értesítések az eszközre a megoldástól. Az offline parancsok lekérdezési paramétereket tartalmazhatnak, de nem adnak vissza választ.

> [!NOTE]
> Ez a beállítás csak a IoT Central webes felhasználói felületén érhető el. Ez a beállítás nem áll rendelkezésre, ha az eszköz sablonból exportál egy modellt vagy felületet.

## <a name="cloud-properties"></a>Felhőtulajdonságok

A felhő tulajdonságai az eszköz sablon részét képezik, de nem tartoznak az eszköz modelljébe. A felhő tulajdonságai lehetővé teszik a megoldás fejlesztője számára a IoT Central alkalmazásban tárolni kívánt eszközök metaadatainak megadását. A felhő tulajdonságai nem érintik azt a kódot, amelyet az eszköz fejlesztője az eszköz modellének megvalósítására ír.

A megoldás fejlesztője hozzáadhat Felhőbeli tulajdonságokat az irányítópultokhoz és nézetekhez az eszköz tulajdonságai mellett, hogy az operátor felügyelje az alkalmazáshoz kapcsolódó eszközöket. A megoldás fejlesztője a szabályok definíciójának részeként is használhat Felhőbeli tulajdonságokat, hogy az operátor egy küszöbértéket módosítson.

## <a name="customizations"></a>Testreszabások

A testreszabások az eszköz sablon részét képezik, de nem tartoznak az eszköz modelljébe. A testreszabások lehetővé teszik, hogy a megoldás fejlesztője fejlesszen vagy felülbíráljon néhány definíciót az eszköz modelljében. A megoldás fejlesztője például módosíthatja a telemetria-típus vagy-tulajdonság megjelenítendő nevét. A megoldás fejlesztője testreszabásokat is használhat a karakterlánc-eszköz tulajdonságának minimális vagy maximális hosszának hozzáadásához.

A testreszabások hatással lehetnek arra a kódra, amelyet az eszköz fejlesztője az eszköz modellének megvalósítására ír. Egy Testreszabás például megadhatja a minimális és a maximális karakterlánc hosszát, illetve a telemetria minimális és maximális numerikus értékeit.

## <a name="views"></a>Megjelenítések

A megoldás fejlesztői olyan nézeteket hoznak létre, amelyek lehetővé teszik a kezelők számára a csatlakoztatott eszközök figyelését és kezelését. A nézetek az eszköz sablonjának részét képezik, így a nézet egy adott típusú eszközhöz van társítva. A nézetek A következők lehetnek:

- A telemetria ábrázoló diagramok.
- A csak olvasható eszközök tulajdonságainak megjelenítésére szolgáló csempék.
- Csempék, amelyek lehetővé teszik az operátor számára az írható eszköz tulajdonságainak szerkesztését.
- Csempék, amelyek lehetővé teszik az operátornak a felhő tulajdonságainak szerkesztését.
- Csempék, amelyek lehetővé teszik az operátor hívási parancsait, beleértve a hasznos adatokat tartalmazó parancsokat is.
- Feliratok, képek vagy Markdown szövegének megjelenítéséhez szükséges csempék.

A nézetekhez hozzáadható telemetria, tulajdonságokat és parancsokat az eszköz modellje, a felhő tulajdonságai és a testreszabások határozzák meg az eszköz sablonjában.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az eszközök sablonjait, a következő lépés a [telemetria, a Property és a Command hasznos](./concepts-telemetry-properties-commands.md) adatok beolvasása, amelyekkel részletesebben tájékozódhat az eszközök IoT Centralsal való cseréjéről.

A megoldás fejlesztője javasolt következő lépés egy [új IoT-eszköz típusának beolvasása az Azure IoT Central-alkalmazásban](./howto-set-up-template.md) az eszköz sablonjának létrehozásával kapcsolatos további információért.
