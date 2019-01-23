---
title: Konfigurálhatja az eszközöket a távoli figyelési megoldás oktatóanyag – Azure |} A Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhatja az eszközöket a távoli figyelési megoldásgyorsító csatlakozik.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/15/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: a2dc5653b7579d9c5db4667d84e6e5b5082abaab
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448074"
---
# <a name="tutorial-configure-devices-connected-to-your-monitoring-solution"></a>Oktatóanyag: A figyelési megoldáshoz csatlakoztatott eszközök konfigurálása

Ebben az oktatóanyagban a távoli monitorozási megoldásgyorsítóval konfigurálhatja és kezelheti a csatlakoztatott IoT-eszközöket. A megoldásgyorsító hozzáad egy új eszközt, és konfigurálja az eszközt.

A Contoso új gépet rendelt, amellyel az egyik létesítményét szeretné bővíteni. Amíg az új gép kézbesítésére vár, Ön lefuttat egy szimulációt a megoldás működésének tesztelése érdekében. A szimuláció futtatja, hozzáadhat új eszköz szimulált motor a távoli figyelési megoldásgyorsító és tesztelje, hogy ezt a szimulált eszközt megfelelően válaszol-konfiguráció frissítéseit. Bár ebben az oktatóanyagban a szimulált eszközök, eszköz fejlesztő megvalósítható közvetlen metódusok a [valós eszköz csatlakozik a távoli figyelési megoldásgyorsító](iot-accelerators-connecting-devices.md).

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

>[!div class="checklist"]
> * Szimulált eszköz üzembe helyezése.
> * Szimulált eszköz tesztelése.
> * Eszköz újrakonfigurálása.
> * Eszközök rendszerezése.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="add-a-simulated-device"></a>Szimulált eszköz hozzáadása

Navigáljon a megoldás **Devices** (Eszközök) lapjára, majd kattintson a **+ New device** (Új eszköz) gombra:

[![Szimulált eszköz üzembe helyezése](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovision-expanded.png#lightbox)

Az **új eszköz** paneljén válassza a **Simulated** (Szimulált) lehetőséget, az üzembe helyezendő eszközök száma maradjon **1**, válassza ki a **Faulty Engine** (Hibás motor) eszközmodellt, majd kattintson az **Apply** (Alkalmaz) gombra a szimulált eszköz létrehozásához:

[![Szimulált motoreszköz üzembe helyezése](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesprovisionengine-expanded.png#lightbox)

## <a name="test-the-simulated-device"></a>A szimulált eszköz tesztelése

Ha tesztelni szeretné, hogy a szimulált motoreszköz elküldi-e a telemetriai adatokat és a tulajdonságértékek jelentéseit, válassza ki az **eszközök** lapján található eszközlistából. A motor valós idejű információi a **Device Details** (Eszköz részletei) panelen jelennek meg:

[![Az új szimulált motoreszköz megtekintése](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesviewnew-expanded.png#lightbox)

A **Device Details** (Eszközök részletei) résznél ellenőrizze, hogy az új eszköz elküldi-e a telemetriai adatokat. Az eszköz által küldött rezgési telemetriastreamek megtekintéséhez kattintson a **Vibration** (Rezgés) gombra:

[![A megtekinteni kívánt telemetriastream kiválasztása](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesvibration-expanded.png#lightbox)

Az **eszközt részletező** panel további információkat is megjelenít (például címkeértékeket, támogatott metódusokat, az eszköz által támogatott tulajdonságokat).

A részletes diagnosztikai adatok megtekintéséhez görgessen lefelé a **Device Details** (Eszköz részletei) panelen a**Diagnostics** (Diagnosztika) szakasz megtekintéséhez.

## <a name="reconfigure-a-device"></a>Eszköz újrakonfigurálása

Ha tesztelni szeretné, hogy a motor konfigurációs tulajdonságai frissíthetők-e, válassza ki a motort az **eszközök** lapján található eszközlistából. Kattintson a **feladatok**, és válassza a **tulajdonságok**. A feladatok paneljén a kiválasztott eszköz frissíthető tulajdonságértékei láthatók:

[![Eszköz újrakonfigurálása](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigure-expanded.png#lightbox)

A motor földrajzi helyének frissítéséhez adja meg az **UpdateEngineLocation** nevet, állítsa be a **-122,15** hosszúsági és a **47,62** szélességi fokot, a helyszín legyen **Factory 2**, végül kattintson az **Apply** (Alkalmaz) gombra:

[![Eszköztulajdonság értékének frissítése](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesreconfigurephysical-expanded.png#lightbox)

A feladat állapotának nyomon követéséhez kattintson a **View job status** (Feladat állapotának megtekintése) elemre:

[![Eszköztulajdonság értékének frissítése](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-inline.png)](./media/iot-accelerators-remote-monitoring-manage/locationjobstatus-expanded.png#lightbox)

Ha a feladat befejeződött, lépjen vissza az **irányítópultra**. A motoreszköz az új helyén jelenik meg a térképen:

[![Motor földrajzi helyének megtekintése](./media/iot-accelerators-remote-monitoring-manage/enginelocation-inline.png)](./media/iot-accelerators-remote-monitoring-manage/enginelocation-expanded.png#lightbox)

## <a name="organize-your-devices"></a>Eszközök rendszerezése

Operátorként az eszközök rendszerezésének és kezelésének megkönnyítése érdekében érdemes őket ellátni csapatnevet tartalmazó címkével. A helyszíni szolgáltatási tevékenységek elvégzéséhez a Contoso két különböző csapatot állított fel:

* A Smart Vehicle csapat a tehergépkocsikat és a prototípusokat felügyeli.
* A Smart Building csapat a hűtőeszközökért, a liftekért és a motorokért felelős.

Az összes eszköz megjelenítéséhez lépjen az **eszközök** lapjára, és válassza az **All devices** (Minden eszköz) szűrőt:

[![Minden eszköz megjelenítése](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesalldevices-expanded.png#lightbox)

### <a name="add-tags"></a>Címkék hozzáadása

Válassza ki az összes **Trucks** (Tehergépkocsi) és **Prototyping** (Prototípus) eszközt. Ezután kattintson a **Jobs** (Feladatok) gombra.

A **Jobs** (Feladatok) panelen válassza a **Tag** (Címke) lehetőséget, a feladat neveként adja meg az**AddConnectedVehicleTag** nevet, majd adja hozzá a **FieldService** nevű szöveges címkét, amelynek értéke legyen **ConnectedVehicle**. Ezután kattintson az **Apply** (Alkalmaz) gombra:

[![Címke hozzáadása a prototípus- és teherautó-eszközökhöz](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag-expanded.png#lightbox)

Az eszköz lapon válassza ki az összes **Chiller** (Hűtőeszköz), **Elevator** (Lift) és **Engine** (Motor) eszközt. Ezután kattintson a **Jobs** (Feladatok) gombra.

A **Jobs** (Feladatok) panelen válassza a **Tag** (Címke) lehetőséget, a feladat neveként adja meg az **AddSmartBuildingTag** nevet, majd adja hozzá a**FieldService** nevű szöveges címkét, amelynek értéke legyen **SmartBuilding**. Ezután kattintson az **Apply** (Alkalmaz) gombra:

[![Címke hozzáadása a hűtőeszközökhöz, liftekhez és motorokhoz](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesaddtag2-expanded.png#lightbox)

### <a name="create-filters"></a>Szűrők létrehozása

A címkeértékek alapján létrehozhatja a szűrőket. Az **eszközök** lapján kattintson a **Manage device groups** (Eszközcsoportok kezelése) gombra:

[![Eszközcsoportok kezelése](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-inline.png)](./media/iot-accelerators-remote-monitoring-manage/devicesmanagefilters-expanded.png#lightbox)

Hozzon létre egy szöveges szűrőt, amely a **FieldService** címkenevet és a **SmartBuilding** értéket használja feltételként. Mentse a szűrőt **Smart Building** néven:

[![A Smart Building szűrő létrehozása](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/smartbuildingfilter-expanded.png#lightbox)

Hozzon létre egy szöveges szűrőt, amely a **FieldService** címkenevet és a **ConnectedVehicle** értéket használja feltételként. Mentse a szűrőt **Connected Vehicle** néven.

[![A Connected Vehicle szűrő létrehozása](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-inline.png)](./media/iot-accelerators-remote-monitoring-manage/connectedvehiclefilter-expanded.png#lightbox)

A Contoso operátora mostantól lekérdezheti az eszközöket az üzemeltetési csapat alapján:

[![A Connected Vehicle szűrő létrehozása](./media/iot-accelerators-remote-monitoring-manage/filterinaction-inline.png)](./media/iot-accelerators-remote-monitoring-manage/filterinaction-expanded.png#lightbox)

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan konfigurálhatja és kezelheti a távoli monitorozási megoldásgyorsítóhoz csatlakoztatott eszközöket. A következő oktatóanyagból megtudhatja, hogyan használhatja a megoldásgyorsítót egy váratlan riasztás kiváltó okának elemzésére.

> [!div class="nextstepaction"]
> [Riasztás kiváltó okainak elemzése](iot-accelerators-remote-monitoring-root-cause-analysis.md)
