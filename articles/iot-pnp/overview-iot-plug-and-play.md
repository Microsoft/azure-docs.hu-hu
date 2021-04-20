---
title: Bevezetés az IoT-Plug and Play | Microsoft Docs
description: Az IoT-Plug and Play. Az IoT Plug and Play egy nyílt modellezési nyelvre épül, amely lehetővé teszi az intelligens IoT-eszközök számára, hogy deklarálják a képességeiket. Az IoT-eszközök ezt a deklarációt eszközmodellnek nevezik, amikor felhőalapú megoldásokhoz csatlakoznak. A felhőalapú megoldás ezután automatikusan megértheti az eszközt, és kódot írás nélkül elkezdhet vele interakcióba lépni.
author: rido-min
ms.author: rmpablos
ms.date: 03/21/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 91a09db16524ebc7e4c04069b69b1c42c67538c6
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739709"
---
# <a name="what-is-iot-plug-and-play"></a>Mi az IoT Plug and Play?

Az IoT Plug and Play használatával a megoldáskészítők manuális konfigurálás nélkül integrálhatnak intelligens eszközöket a megoldásaikba. Az IoT Plug and Play egy olyan eszközmodell,  amely segítségével az eszköz meghirdeti képességeit egy IoT Plug and Play-kompatibilis alkalmazás számára. Ez a modell elemek halmazaként van strukturálva, amelyek a következőt határozzák meg:

- _Egy_ eszköz vagy más entitás írásható vagy írható állapotát képviselő tulajdonságok. Az eszköz sorozatszáma lehet például csak olvasható tulajdonság, a termosztát célhőmérséklete pedig írható tulajdonság.
- _Telemetria,_ amely az eszköz által kibocsátott adatok, legyen szó az érzékelők által küldött adatok rendszeres streamjéről, alkalmankénti hibáról vagy tájékoztató üzenetről.
- _Az eszközön_ egy függvényt vagy műveletet leíró parancsok. Egy parancs például újraindíthat egy átjárót, vagy egy távoli kamerával képet is létrehozhat.

Ezeket az elemeket felületekbe csoportosíthatja, hogy a modelleket újra felhasználva megkönnyítse az együttműködést és felgyorsítsa a fejlesztést.

Ahhoz, hogy az IoT Plug and Play működjön a [Azure Digital Twins,](../digital-twins/overview.md)modelleket és felületeket definiálhat a [Digital Twins Definition Language (DTDL) használatával.](https://github.com/Azure/opendigitaltwins-dtdl) Az IoT Plug and Play és a DTDL a közösség számára elérhető, és a Microsoft örömmel fogadja az ügyfelekkel, partnerekkel és az iparággal való együttműködést. Mindkettő nyílt W3C-szabványokon alapul, mint például a JSON-LD és az RDF, ami megkönnyíti a szolgáltatások és eszközök közötti elfogadást.

Az IoT-eszközök és a DTDL Plug and Play használata nem jár többletköltséggel. A szolgáltatások [és Azure IoT Hub](../iot-hub/about-iot-hub.md) Azure-szolgáltatások standard díjszabása változatlan marad.

Ez a cikk a következőt ismerteti:

- Az IoT-t használó projekthez tartozó tipikus szerepkörök Plug and Play.
- Az IoT Plug and Play eszközök használata az alkalmazásban.
- IoT-t támogató IoT-eszközalkalmazás Plug and Play.

## <a name="user-roles"></a>Felhasználói szerepkörök

Az IoT Plug and Play két típusú fejlesztő számára hasznos:

- A _megoldáskészítők_ feladata egy IoT-megoldás fejlesztése Azure IoT Hub és más Azure-erőforrások használatával, valamint az integrálni szükséges IoT-eszközök azonosítása.
- Az _eszközszerkesztő_ létrehozza a kódot, amely a megoldáshoz csatlakoztatott eszközön fut.

## <a name="use-iot-plug-and-play-devices"></a>IoT-Plug and Play használata

Megoldáskészítőként a IoT Central [](../iot-central/core/overview-iot-central.md) vagy IoT Hub [](../iot-hub/about-iot-hub.md) ioT-eszközöket használó, felhőben üzemeltetett IoT-megoldás Plug and Play használhatja.

A webes felhasználói felület IoT Central eszközfeltételek figyelése, szabályok létrehozása és eszközök millióinak és adatainak kezelése az életciklusuk során. Az IoT Plug and Play eszközök közvetlenül csatlakoznak egy IoT Central-alkalmazáshoz, ahol testre szabható irányítópultokkal figyelheti és vezérelheti az eszközeit. A DTDL-modellek létrehozásához és szerkesztéséhez IoT Central webes felhasználói felületen eszközsablonokat is használhat.

IoT Hub felügyelt felhőszolgáltatás üzenetközpontként működik az IoT-alkalmazás és az eszközök közötti biztonságos, kétirányú kommunikációhoz. Amikor IoT Plug and Play-eszközt csatlakoztat egy IoT Hubhoz, az [Azure IoT Explorer](./howto-use-iot-explorer.md) eszközzel megtekintheti a DTDL-modellben definiált telemetriai adatokat, tulajdonságokat és parancsokat.

Ha meglévő érzékelők vannak csatlakoztatva egy Windows- vagy Linux-átjáróhoz, az [IoT Plug and Play-híd](./concepts-iot-pnp-bridge.md)használatával csatlakoztathatja ezeket az érzékelőket, és IoT Plug and Play-eszközöket hozhat létre anélkül, hogy eszközszoftvert/belső vezérlőprogramot kellene írnia (támogatott protokollok [esetén).](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors)

## <a name="develop-an-iot-device-application"></a>IoT-eszközalkalmazás fejlesztése

Eszközszerkesztőként olyan IoT-hardverterméket fejleszthet, amely támogatja az IoT-Plug and Play. A folyamat három fő lépésből áll:

1. Definiálja az eszközmodellt. A [DTDL](https://github.com/Azure/opendigitaltwins-dtdl)használatával olyan JSON-fájlokat fog meghozni, amelyek meghatározzák az eszköz képességeit. A modell egy teljes entitást ír le, például egy fizikai terméket, és meghatározza az entitás által megvalósított interfészeket. Az interfészek olyan megosztott szerződések, amelyek egyedileg azonosítják az eszközök által támogatott telemetriai adatokat, tulajdonságokat és parancsokat. A felületek újra felhasználhatók a különböző modellekben.

1. Az eszközszoftvert vagy belső vezérlőprogramot úgy kell megalkosni, hogy a telemetriai adatok, a tulajdonságok és a parancsok kövessék az IoT Plug and Play konvencióit. Ha Windows- vagy Linux-átjáróhoz csatlakoztatott meglévő érzékelőket csatlakoztat, az [IoT Plug and Play-híd](./concepts-iot-pnp-bridge.md) leegyszerűsítheti ezt a lépést.

1. Az eszköz az MQTT-kapcsolat részeként jelenti be a modellazonosítót. Az Azure IoT SDK új szerkezeteket tartalmaz, amelyek a kapcsolatkor biztosítják a modellazonosítót.

> [!Important]
> Az IoT Plug and Play eszközöknek WebSocketen keresztül kell MQTT-t vagy MQTT-t használniuk. Más protokollok, például az AMQP vagy a HTTP nem használhatók IoT-Plug and Play megvalósításához.

## <a name="device-certification"></a>Eszköztanúsítás

Az [IoT Plug and Play](../certification/program-requirements-pnp.md) eszközminősítő program ellenőrzi, hogy az eszköz megfelel-e az IoT Plug and Play követelményeinek. A minősített eszközt hozzáadhatja a nyilvános [Certified for Azure IoT eszközkatalógushoz.](https://aka.ms/devicecatalog)

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekinti az IoT-Plug and Play, a következő javasolt lépés az egyik gyorsútmutató kipróbálja:

- [Eszköz csatlakoztatása az IoT Hubhoz](./quickstart-connect-device.md)
- [Kommunikáció egy eszközzel a megoldásból](./quickstart-service.md)
