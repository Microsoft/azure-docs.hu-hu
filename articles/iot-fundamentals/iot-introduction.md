---
title: Bevezetés az Azure eszközök internetes hálózataba (IoT)
description: Bevezetés az Azure IoT és a IoT szolgáltatások alapjaira, beleértve a IoT használatának szemléltetését segítő példákat is.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: d2afd88500801f1e2b1e4da5a1e267bddfa69fc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96453025"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Mi az Azure eszközök internetes hálózata (IoT)?

Az Azure IoT (eszközök internetes hálózata) a Microsoft által felügyelt felhőszolgáltatások gyűjteménye, amelyek az IoT-hez csatlakozó eszközök millióit kötik össze, monitorozzák és irányítják. Egyszerűbb feltételek mellett egy IoT-megoldás egy vagy több olyan IoT-eszközből áll, amely egy vagy több, a felhőben üzemeltetett háttér-szolgáltatással kommunikál. 

## <a name="iot-devices"></a>IoT-eszközök

Az IoT-eszközök jellemzően egy hálózati érzékelővel rendelkező áramköri táblából állnak, amely WiFi használatával csatlakozik az internethez. Például:

* A Nyomásérzékelő egy távoli olajszivattyú.
* Hőmérséklet-és páratartalom-érzékelők egy légkondicionáló egységben.
* Egy gyorsulásmérő egy liftben.
* Jelenléti érzékelők egy helyiségben.

Számos különböző gyártótól származó eszköz áll rendelkezésre a megoldás létrehozásához. Az Azure IoT Hub-vel való együttműködésre tanúsított eszközök listáját az [Azure Certified for IoT-eszközök katalógusában](https://catalog.azureiotsolutions.com/alldevices)tekintheti meg. A prototípusok esetében olyan eszközöket használhat, mint például a [MXChip IoT fejlesztői készlet](https://microsoft.github.io/azure-iot-developer-kit/) vagy a [málna PI](https://www.raspberrypi.org/). A fejlesztői készlet beépített érzékelőkkel rendelkezik a hőmérséklet, a nyomás, a páratartalom, valamint a giroszkóp, a gyorsulásmérő és a magnetometer számára. A málna PI számos különböző típusú érzékelő csatlakoztatását teszi lehetővé. 

A Microsoft nyílt forráskódú [eszköz SDK](../iot-hub/iot-hub-devguide-sdks.md) -kat biztosít, amelyek segítségével az eszközökön futó alkalmazásokat hozhatja létre. Ezek az [SDK-k leegyszerűsítik és felgyorsítják](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) a IoT-megoldások fejlesztését.

## <a name="communication"></a>Kommunikáció

A IoT-eszközök általában a felhőben lévő telemetria küldik el az érzékelőktől a háttér-szolgáltatásokhoz. Más típusú kommunikáció azonban lehetséges, például egy háttér-szolgáltatás, amely parancsokat küld az eszközökre. Az alábbiakban néhány példát talál az eszközről a felhőre és a felhőből az eszközre irányuló kommunikációra:

* A mobil hűtési tehergépkocsi 5 percenként küldi el a hőmérsékletet egy IoT Hub. 

* A háttér-szolgáltatás egy parancsot küld egy eszköznek, hogy megváltoztassa a telemetria küldésének gyakoriságát, hogy segítsen a probléma diagnosztizálásában. 

* Egy eszköz riasztásokat küld az érzékelőktől beolvasott értékek alapján. Ha például egy eszköz egy batch-reaktort figyel egy vegyipari üzemben, riasztást küld, ha a hőmérséklet meghaladja az adott értéket.

* Az eszközök adatokat küldenek az irányítópulton az emberi operátorok általi megjelenítéshez. Például egy olyan vezérlő helyisége, amely az egyes csövek hőmérsékletét, terhelését és áramlási mennyiségét mutatja be, lehetővé teszi, hogy a kezelők a létesítményt felügyeljék. 

A [IoT-eszköz SDK](../iot-hub/iot-hub-devguide-sdks.md) -k és IoT hub támogatják a gyakori [kommunikációs protokollokat](../iot-hub/iot-hub-devguide-protocols.md) , például a http-t, a MQTT és a AMQP-t.

A IoT-eszközök más jellemzőkkel rendelkeznek, mint a böngészők és a Mobile apps. Az eszköz SDK-k segítenek az eszközök biztonságos és megbízható módon történő csatlakoztatásával kapcsolatos problémák megoldásában.  Pontosabban, az IoT-eszközök:

* Általában beágyazott, emberi beavatkozást nem igénylő rendszerek (a telefonokkal ellentétben).
* Távoli helyeken is üzembe helyezhetők, ahol a fizikai hozzáférés drága lenne.
* Előfordulhat, hogy csak a megoldás háttérrendszerén keresztül érhetők el.
* Áramellátásuk és feldolgozási erőforrásaik korlátozottak lehetnek.
* A hálózati kapcsolat időszakos, lassú vagy drága lehet.
* Saját fejlesztésű, egyedi vagy iparág-specifikus alkalmazás-protokollokra lehet szükség.

## <a name="back-end-services"></a>Háttérbeli szolgáltatások 

Egy IoT-megoldásban a háttérrendszer olyan funkciókat biztosít, mint például a következők:

* Telemetria fogadása az eszközökről, valamint az adatok feldolgozásának és tárolásának meghatározása.
* Elemezheti a telemetria, hogy valós időben vagy a tény alapján szolgáltasson betekintést.
* Parancsok küldése a felhőből egy adott eszközre. 
* Eszközök kiépítése és az infrastruktúrához való kapcsolódásuk szabályozása.
* Az eszközök állapotának szabályozása és tevékenységük figyelése.
* Az eszközökön telepített belső vezérlőprogram kezelése.

Például egy olajszivattyú-állomás távoli figyelési megoldásában a Felhőbeli háttér telemetria használ a szivattyúktól a rendellenes viselkedés azonosítása érdekében. Ha a háttérrendszer egy rendellenességet azonosít, automatikusan visszaküldheti a parancsot az eszköznek, hogy kijavítson egy műveletet. A folyamat létrehoz egy automatizált visszajelzési hurkot az eszköz és a felhő között, amely jelentősen növeli a megoldás hatékonyságát.

## <a name="azure-iot-examples"></a>Azure IoT-példák

A szervezeteknek az Azure IoT használatát bemutató valós Példákért lásd: [Microsoft technikai esettanulmányok IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

Az IoT-architektúra részletes ismertetéséhez tekintse át a következő dokumentumot: [Microsoft Azure IoT Reference Architecture](/azure/architecture/reference-architectures/iot) (Microsoft Azure IoT-referenciaarchitektúra).

## <a name="next-steps"></a>Következő lépések

Egyes tényleges üzleti esetekben és a használt architektúrában tekintse meg a [Microsoft Azure IoT műszaki esettanulmányok](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured)című témakört.

Egyes IoT-fejlesztői készlet kipróbálható példákért tekintse meg a [IoT fejlesztői készlet Project Catalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)című részt. 

A különböző szolgáltatások és azok használatának részletes ismertetését lásd: [Azure IoT Services és Technologies](iot-services-and-technologies.md).