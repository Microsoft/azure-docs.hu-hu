---
title: Az Azure IoT-eszközök és-alkalmazások fejlesztésének bemutatása
description: Ismerje meg, hogyan használhatja az Azure IoT a beágyazott eszközök fejlesztésére és az eszközre képes felhőalapú alkalmazások készítésére.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: dd4e53eebe6593db457798f009d3d05ddcbd77b8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "100654949"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>Mi az Azure IoT-eszköz és alkalmazásfejlesztés?

Az Azure IoT olyan felügyelt és platform-szolgáltatások gyűjteménye, amelyek a IoT-eszközök csatlakoztatását, figyelését és felügyeletét végzik. Az Azure IoT számos lehetőséget kínál a fejlesztőknek. A lehetőségei közé tartozik az eszköz platformja, a Cloud Services, az SDK-k és az eszközökön futó felhőalapú alkalmazások létrehozásának eszközei.

Ez a cikk az Azure IoT használatának első lépéseivel foglalkozó fejlesztők számára nyújt számos fontos szempontot. Ezek a fogalmak az Azure IoT lehetőségeihez és a kezdéshez szükséges IoT. A cikk a következő fogalmakat tekinti át:
- [Az eszközök fejlesztői szerepköreinek ismertetése](#device-development-roles)
- [A hardver kiválasztása](#choosing-your-hardware)
- [SDK kiválasztása](#choosing-an-sdk)
- [A kapcsolatok beállításainak kiválasztása](#selecting-connection-options)

## <a name="device-development-roles"></a>Eszköz fejlesztési szerepkörei
Ez a cikk két olyan általános szerepkört ismertet, amelyeket az eszközök fejlesztői körében megfigyelheti. Az itt használtak szerint a szerepkör a kapcsolódó fejlesztési feladatok gyűjteménye. Érdemes megismerni, hogy milyen típusú fejlesztési szerepkörrel dolgozik jelenleg. Az Ön szerepköre számos fejlesztési lehetőségre hatással van.

* Az **eszközök alkalmazásának fejlesztése:** A modern fejlesztési gyakorlatokhoz igazodva számos magasabb rendű nyelvet céloz meg, és egy általános célú operációs rendszeren, például Windowson vagy Linuxon hajt végre.

* **Beágyazott eszközök fejlesztése:** Ismerteti az erőforrás által korlátozott eszközök fejlesztésére vonatkozó célzott eszközöket. Az erőforrás által korlátozott eszköz gyakran használatos az egységenkénti költségek, az energiafogyasztás vagy az eszközök méretének csökkentése érdekében. Ezek az eszközök közvetlenül szabályozzák az általuk futtatott hardver platformot.

### <a name="device-application-development"></a>Eszközök alkalmazásának fejlesztése
Az eszköz-alkalmazások fejlesztői a meglévő eszközöket a felhőhöz való kapcsolódáshoz és a IoT-megoldásokhoz való integráláshoz használják. Ezek az eszközök támogatják a magasabb rendű nyelveket, például a C# vagy a Python használatát, és gyakran támogatják a robusztus általános célú operációs rendszert, például a Windowst vagy a Linuxot. A gyakori célpontok közé tartoznak a számítógépek, a tárolók, a málna és a mobileszközök. 

A korlátozott méretű eszközök nagy léptékű fejlesztése helyett ezek a fejlesztők a felhőalapú megoldás által igényelt konkrét IoT-forgatókönyvek engedélyezésére összpontosítanak. Ezen fejlesztők némelyike a felhőalapú megoldásának korlátozott eszközein is működni fog. A korlátozott eszközökkel dolgozó fejlesztők számára lásd az alábbi [beágyazott eszközök fejlesztésének](#embedded-device-development) elérési útját.

> [!TIP]
> Az első lépésekhez tekintse meg a nem [korlátozott eszköz SDK](about-iot-sdks.md#unconstrained-device-sdks) -kat.

### <a name="embedded-device-development"></a>Beágyazott eszközök fejlesztése
A beágyazott fejlesztési célok korlátozott számú memóriával és feldolgozással rendelkező eszközök. A korlátozott eszközök korlátozzák, hogy mit lehet megvalósítani a hagyományos fejlesztési platformhoz képest.

A beágyazott eszközök jellemzően valós idejű operációs rendszert (RTOS-t) használnak, vagy nincsenek operációs rendszerek. Az általános célú operációs rendszer hiánya miatt a beágyazott eszközök teljes mértékben szabályozzák a hardvert. Ez a tény lehetővé teszi, hogy a beágyazott eszközök jó választást biztosítanak a valós idejű rendszerekhez.

A jelenlegi beágyazott SDK- **k a C** nyelvet célozzák meg. A beágyazott SDK-k nem rendelkeznek operációs rendszerrel vagy Azure RTOS-támogatással. Ezeket a beágyazott célokat szem előtt tartva tervezték. A kialakítási szempontok közé tartozik a minimális helyigény, valamint a memórián kívüli kiosztási kialakítás szükségessége.

Ha az eszköze képes egy általános célú operációs rendszer futtatására, javasoljuk, hogy kövesse az [eszköz alkalmazás fejlesztési](#device-application-development) útvonalát. A fejlesztési lehetőségek széles körét biztosítja.

> [!TIP]
> Az első lépésekhez tekintse meg a [korlátozott eszköz SDK](about-iot-sdks.md#constrained-device-sdks) -kat.

## <a name="choosing-your-hardware"></a>A hardver kiválasztása
Az Azure IoT-eszközök egy IoT-megoldás alapszintű építőelemei, és felelősek a környezetük megfigyelésében és interakciójában. Számos különböző típusú IoT-eszköz létezik, és hasznos megérteni a létező eszközök típusát, valamint azt, hogy ezek milyen hatással lehetnek a fejlesztési folyamatra.

A jelen cikkben szereplő eszközök típusaival kapcsolatos további információkért olvassa el a IoT-eszközök [típusait ismertető](concepts-iot-device-types.md)cikket.

## <a name="choosing-an-sdk"></a>SDK kiválasztása
Az Azure IoT-eszköz fejlesztője az eszköz-kompatibilis felhőalapú alkalmazások kiépítésének megkönnyítéséhez számos SDK-készletet és Azure Service SDK-t tartalmaz. Az SDK-k egyszerűsítik a fejlesztési erőfeszítéseket, és egyszerűbbé teszik az eszközök csatlakoztatásának és kezelésének összetettségét. 

Ahogy azt az [eszköz fejlesztői szerepkörei](#device-development-roles) szakaszban is említettük, háromféle IoT SDK van az eszköz fejlesztéséhez:
- Beágyazott eszközök SDK-k (korlátozott eszközökhöz)
- Eszközök SDK-k (a magasabb szintű nyelvek használatához a meglévő eszközök IoT-alkalmazásokhoz való csatlakoztatásához)
- Service SDK-k (olyan Azure IoT-megoldások létrehozásához, amelyek eszközöket csatlakoznak a szolgáltatásokhoz)

További információ az Azure IoT-eszköz vagy a Service SDK kiválasztásáról: [Az Azure IoT Device SDK](about-iot-sdks.md)-k áttekintése.

## <a name="selecting-connection-options"></a>A kapcsolatok beállításainak kiválasztása
A fejlesztési folyamat fontos lépése az eszközök csatlakoztatásához és kezeléséhez használni kívánt beállítások kiválasztása. Két fontos szempontot kell figyelembe venni:
- IoT-alkalmazási platform kiválasztása az eszközök üzemeltetéséhez. Az Azure IoT esetében ez IoT Hub vagy IoT Central kiválasztását jelenti.
- Fejlesztői eszközök kiválasztása az eszközök csatlakoztatásához, kezeléséhez és figyeléséhez.

Az alkalmazás-platformok és-eszközök kiválasztásával kapcsolatos további információkért lásd [: az Azure IoT-eszközök fejlesztőinek kapcsolódási lehetőségei](concepts-overview-connection-options.md).

## <a name="next-steps"></a>Következő lépések
Válassza ki az alábbi rövid útmutató sorozat egyikét, amely leginkább a fejlesztési szerepköréhez szükséges. Ezek a cikkek bemutatják, hogyan hozhat létre Azure IoT-alkalmazást az eszközök üzemeltetéséhez, SDK használatával, eszköz csatlakoztatásához és telemetria küldéséhez.  
- Eszközkezelő: gyors útmutató  [: telemetria küldése az eszközről az Azure IoT Central](quickstart-send-telemetry-python.md)
- Beágyazott eszközök fejlesztéséhez: [Bevezetés az Azure IoT Embedded-eszközök fejlesztésével](quickstart-device-development.md)
