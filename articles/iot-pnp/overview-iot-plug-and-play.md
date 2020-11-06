---
title: A IoT Plug and Play bemutatása | Microsoft Docs
description: A IoT Plug and Play megismerése. A IoT Plug and Play egy nyílt modellezési nyelven alapul, amely lehetővé teszi, hogy az intelligens IoT-eszközök bejelentsenek képességeiket. A IoT-eszközök a deklarációt, az eszköz modelljét használják, amikor felhőalapú megoldásokhoz csatlakoznak. A felhőalapú megoldás ezután automatikusan megérti az eszközt, és megkezdheti a velük való interakciót, anélkül, hogy kódot kellene írnia.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 4fd7a24edffbfb63adc830ddb83b45997743ad42
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421634"
---
# <a name="what-is-iot-plug-and-play"></a>Mi az IoT Plug and Play?

A IoT Plug and Play lehetővé teszi a megoldás-építők számára, hogy manuális konfiguráció nélkül integrálják az intelligens eszközöket a megoldásaikkal. A IoT-Plug and Play középpontjában a egy eszköz _modellje_ , amelyet az eszköz használ a képességeinek egy IoT Plug and Play-kompatibilis alkalmazásba való reklámozásához. Ez a modell olyan elemek halmaza, amelyek a következőket határozzák meg:

- Egy eszköz vagy más entitás írásvédett vagy írható állapotát jelképező _Tulajdonságok_ . Előfordulhat például, hogy egy eszköz sorozatszáma csak olvasható tulajdonság, és a termosztát hőmérséklete írható tulajdonság lehet.
- _Telemetria_ az eszköz által kibocsátott adatok, függetlenül attól, hogy az adatok az érzékelők normál streamje, egy alkalmi hiba vagy egy tájékoztató üzenet.
- Egy eszközön elvégezhető függvényt vagy műveletet leíró _parancsok_ . Egy parancs például újraindíthatja az átjárót, vagy egy távoli kamera használatával is készíthet képet.

Ezeket az elemeket az interfészek között csoportosítva újra felhasználhatja a modelleket, így egyszerűbbé és gyorsabbá teheti a fejlesztést.

Ahhoz, hogy a IoT-Plug and Play működjön az [Azure Digital Twins](../digital-twins/overview.md)szolgáltatással, a modelleket és a felületeket a [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)használatával határozhatja meg. A IoT Plug and Play és a DTDL nyitva vannak a Közösség számára, és a Microsoft üdvözli az ügyfelekkel, partnerekkel és az iparággal való együttműködést. Mindkettő olyan nyílt W3C-szabványokon alapul, mint például a JSON-LD és az RDF, amely megkönnyíti a szolgáltatások és az eszközök használatát.

A IoT Plug and Play és a DTDL használata nem igényel további költségeket. Az [azure IoT hub](../iot-hub/about-iot-hub.md) és más Azure-szolgáltatások standard díjszabása változatlan marad.

Ez a cikk a következőket ismerteti:

- A IoT Plug and Play használó projekthez társított tipikus szerepkörök.
- A IoT Plug and Play-eszközök használata az alkalmazásban.
- A IoT Plug and Play támogató IoT-alkalmazás fejlesztése.

## <a name="user-roles"></a>Felhasználói szerepkörök

A IoT Plug and Play két típusú fejlesztő számára hasznos:

- A _megoldás-szerkesztő_ feladata, hogy IoT-megoldást fejlesszen az Azure IoT hub és más Azure-erőforrások használatával, valamint a IoT-eszközök integrálásához.
- Egy _eszköz-szerkesztő_ létrehozza a megoldáshoz csatlakoztatott eszközön futó kódot.

## <a name="use-iot-plug-and-play-devices"></a>IoT Plug and Play-eszközök használata

Megoldás-szerkesztőként olyan felhőalapú IoT-megoldást fejleszthet, amely IoT Plug and Play eszközöket használ. A IoT-alkalmazás és az eszközök közötti biztonságos, kétirányú kommunikációhoz a [IoT hub](../iot-hub/about-iot-hub.md) által felügyelt felhőalapú szolgáltatást használhatja.

Amikor egy IoT Plug and Play eszközt csatlakoztat egy IoT hubhoz, az [Azure IoT Explorer](./howto-use-iot-explorer.md) eszköz használatával megtekintheti a modellt alkotó felületeken definiált telemetria, tulajdonságokat és parancsokat.

Ha rendelkezik egy Windows-vagy Linux-átjáróhoz csatlakoztatott érzékelőkkel, akkor a [IoT Plug and Play Bridge](./concepts-iot-pnp-bridge.md)használatával csatlakoztathatja ezeket az érzékelőket, és létrehozhat IoT Plug and Play eszközöket anélkül, hogy az eszköz szoftverét vagy belső vezérlőprogramot kellene írnia ( [támogatott protokollok](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors) esetén).

## <a name="develop-an-iot-device-application"></a>IoT-eszköz alkalmazásának fejlesztése

Eszköz-szerkesztőként olyan IoT-terméket fejleszthet, amely támogatja a IoT Plug and Play. A folyamat három fő lépést tartalmaz:

1. Adja meg az eszköz modelljét. A [DTDL](https://github.com/Azure/opendigitaltwins-dtdl)használatával olyan JSON-fájlokat hozhat létre, amelyek meghatározzák az eszköz képességeit. A modell egy teljes entitást, például egy fizikai terméket ír le, és meghatározza az adott entitás által megvalósított felületek készletét. A felületek olyan megosztott szerződések, amelyek egyedileg azonosítják az eszköz által támogatott telemetria, tulajdonságokat és parancsokat. A felületek újra felhasználhatók különböző modellekben.

1. Az eszköz szoftverét vagy a belső vezérlőprogramot úgy hozhatja létre, hogy a telemetria, tulajdonságaik és parancsaik kövessék a IoT Plug and Play konvenciókat. Ha Windows vagy Linux rendszerű átjáróhoz csatlakoztatott meglévő érzékelőket csatlakoztat, a [IoT Plug and Play-híd](./concepts-iot-pnp-bridge.md) leegyszerűsítheti ezt a lépést.

1. Az eszköz bejelenti a modell AZONOSÍTÓját a MQTT-kapcsolat részeként. Az Azure IoT SDK új szerkezeteket tartalmaz, amelyek a modell AZONOSÍTÓjának megadására szolgálnak a kapcsolódás időpontjában.

> [!Important]
> A IoT Plug and Play-eszközöknek MQTT vagy MQTT-t kell használniuk a WebSockets-en keresztül. Más protokollok, például a AMQP vagy a HTTP nem érvényesek a IoT Plug and Play-eszközök megvalósítására.

## <a name="device-certification"></a>Eszköztanúsítás

A [IoT Plug and Play eszköz minősítési program](howto-certify-device.md) ellenőrzi, hogy az eszköz megfelel-e a IoT Plug and Play minősítési követelményeinek. Hozzáadhat egy hitelesített eszközt az [Azure IoT-eszközök nyilvános minősítésére](https://aka.ms/devicecatalog).

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a IoT Plug and Play, a javasolt következő lépés az egyik rövid útmutató kipróbálása:

- [Eszköz csatlakoztatása IoT Hubhoz (C)](./quickstart-connect-device-c.md)
- [Kommunikáció egy eszközzel a megoldásból (Node.js)](./quickstart-service-node.md)