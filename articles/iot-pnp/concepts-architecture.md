---
title: IoT Plug and Play architektúra | Microsoft Docs
description: Megoldás-szerkesztőként megismerheti a IoT Plug and Play legfontosabb építészeti elemeit.
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 2cccb1fdfe775250f80da6cc2ecdcc4ddaa3d88e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95484083"
---
# <a name="iot-plug-and-play-architecture"></a>IoT Plug and Play-architektúra

Az IoT Plug and Play használatával a megoldáskészítők manuális konfigurálás nélkül integrálhatnak intelligens eszközöket a megoldásaikba. A IoT Plug and Play egy olyan eszköz _modellje_ , amely leírja az eszköz képességeit egy IoT Plug and Play-kompatibilis alkalmazás számára. Ez a modell illesztőfelület-készletként van strukturálva, amely az alábbiakat határozza meg:

- Egy eszköz vagy más entitás írásvédett vagy írható állapotát jelképező _Tulajdonságok_ . Előfordulhat például, hogy egy eszköz sorozatszáma csak olvasható tulajdonság, és a termosztát hőmérséklete írható tulajdonság lehet.
- _Telemetria_ az eszköz által kibocsátott adatok, függetlenül attól, hogy az adatok az érzékelők normál streamje, egy alkalmi hiba vagy egy tájékoztató üzenet.
- Egy eszközön elvégezhető függvényt vagy műveletet leíró _parancsok_ . Egy parancs például újraindíthatja az átjárót, vagy egy távoli kamera használatával is készíthet képet.

Minden modell és felület egyedi AZONOSÍTÓval rendelkezik.

Az alábbi ábrán egy IoT Plug and Play megoldás fő elemei láthatók:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT Plug and Play-architektúra":::

## <a name="model-repository"></a>Modelladattár

A [Model adattár](./concepts-model-repository.md) a modell-és illesztőfelület-definíciók tárolója. A modelleket és a csatolókat a [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)használatával határozhatja meg.

A webes felhasználói felület lehetővé teszi a modellek és felületek kezelését.

A modell tárház beépített szerepköralapú hozzáférés-vezérléssel rendelkezik, amely lehetővé teszi az illesztőfelület-definíciók elérésének korlátozását.

## <a name="devices"></a>Eszközök

Egy IoT intelligens eszközön a kód egy [Azure IoT-eszköz SDK](./libraries-sdks.md)-k használatával történő futtatását valósítja meg. Az eszköz SDK-k segítik az Eszközkezelőt a következőknek:

- Biztonságos kapcsolódás egy IoT hubhoz.
- Regisztrálja az eszközt az IoT hub-ban, és jelentse be az eszköz által megvalósított DTDL-felületek gyűjteményét azonosító modell AZONOSÍTÓját.
- Szinkronizálja a DTDL-illesztőfelületekben definiált tulajdonságokat az eszköz és az IoT hub között.
- Adja hozzá a DTDL felületeken definiált parancsokhoz tartozó parancssori kezelőket.
- Telemetria küldése az IoT hub-nak.

## <a name="iot-edge-gateway"></a>Átjáró IoT Edge

Egy IoT Edge átjáró közvetítőként viselkedik a IoT Plug and Play olyan eszközök csatlakoztatásához, amelyek nem tudnak közvetlenül csatlakozni az IoT hubhoz. További információt a IoT Edge- [eszköz átjáróként való használatáról](../iot-edge/iot-edge-as-gateway.md)szóló témakörben talál.

## <a name="iot-edge-modules"></a>IoT Edge-modulok

Az _IoT Edge modul_ lehetővé teszi az üzleti logikák üzembe helyezését és kezelését a peremhálózat szélén. A Azure IoT Edge modulok a IoT Edge által kezelt számítási egységek legkisebb egységei, és tartalmazhatnak Azure-szolgáltatásokat (például Azure Stream Analytics) vagy a saját megoldásra vonatkozó kódokat.

Az _IoT Edge hub_ az Azure IoT Edge futtatókörnyezetet alkotó modulok egyike. A IoT Hub helyi proxyként működik, ha a protokoll-végpontokat IoT Hubként teszi elérhetővé. Ez a konzisztencia azt jelenti, hogy az ügyfelek (függetlenül attól, hogy az eszközök vagy modulok) képesek-e csatlakozni a IoT Edge futtatókörnyezethez ugyanúgy, ahogy IoT Hub.

Az eszköz SDK-k segítik a modul-szerkesztőt a következőhöz:

- Az IoT Edge hub használatával biztonságosan csatlakozhat az IoT hubhoz.
- Regisztrálja a modult az IoT hub-ban, és jelentse be a modell AZONOSÍTÓját, amely azonosítja az eszköz által megvalósított DTDL-felületek gyűjteményét.
- Szinkronizálja a DTDL-illesztőfelületekben definiált tulajdonságokat az eszköz és az IoT hub között.
- Adja hozzá a DTDL felületeken definiált parancsokhoz tartozó parancssori kezelőket.
- Telemetria küldése az IoT hub-nak.

## <a name="iot-hub"></a>IoT Hub

[IoT hub](../iot-hub/about-iot-hub.md) egy felhőalapú szolgáltatás, amely központi üzenetsorként működik a IoT-megoldás és az általa felügyelt eszközök közötti kétirányú kommunikációhoz.

Egy IoT hub:

- Egy eszköz által megvalósított modell AZONOSÍTÓját a háttérrendszer számára elérhetővé teszi.
- Karbantartja a központhoz csatlakoztatott Plug and Play-eszközökhöz társított digitális IKeret.
- Továbbítja a telemetria streameket más szolgáltatásoknak feldolgozásra vagy tárolásra.
- Átirányítja a digitális kettős változási eseményeket más szolgáltatásoknak az eszközök figyelésének engedélyezéséhez.

## <a name="backend-solution"></a>Háttérrendszer-megoldás

A háttérrendszer-megoldás a csatlakoztatott eszközöket figyeli és szabályozza a IoT hub digitális ikrekkel való interakciója révén. Használja az egyik Service SDK-t a háttérbeli megoldás megvalósításához. A csatlakoztatott eszközök képességeinek megismeréséhez a megoldás háttere:

1. Az IoT hub-ban regisztrált eszköz AZONOSÍTÓjának beolvasása.
1. A modell AZONOSÍTÓját használja az illesztőfelület-definíciók beolvasásához bármely modell adattárból.
1. A Model Parser használatával kinyeri az adatokat az illesztőfelület-definíciók közül.

A háttérrendszer-megoldás az illesztőfelület-definíciók adatait a következőre használhatja:

- Az eszközök által jelentett tulajdonságértékek olvasása.
- Eszközön lévő írható tulajdonságok frissítése.
- Egy eszköz által megvalósított parancsok hívása.
- Az eszköz által eljuttatott telemetria formátumának megismerése.

## <a name="next-steps"></a>Következő lépések

Most, hogy áttekinti a IoT Plug and Play megoldás architektúráját, a következő lépésekkel többet is megtudhat a következőkről:

- [A modell tárháza](./concepts-model-repository.md)
- [Digitális kettős modell integrációja](./concepts-model-discovery.md)
- [Fejlesztés a IoT Plug and Play](./concepts-developer-guide-device.md)
