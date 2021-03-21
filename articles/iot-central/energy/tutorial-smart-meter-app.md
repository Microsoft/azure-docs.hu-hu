---
title: 'Oktatóanyag: Smart Meter Analytics-alkalmazás létrehozása IoT Central'
description: 'Oktatóanyag: Ismerje meg, hogyan hozhat létre intelligens mérőműszer-figyelő alkalmazást az Azure IoT Central alkalmazás-sablonok használatával.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d9b5af45ab9a1003cc25e8b1ea2059b83bc715c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833303"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Oktatóanyag: az intelligens fogyasztásmérő monitorozási alkalmazás sablonjának létrehozása és végigvezeti 

Ez az oktatóanyag végigvezeti az intelligens mérőműszer-figyelő alkalmazás létrehozásának folyamatán, amely egy szimulált adattal rendelkező minta-eszköz modellt tartalmaz. Ezen oktatóanyag segítségével elsajátíthatja a következőket:

> [!div class="checklist"]
> * Hozzon létre ingyenes Smart Meter alkalmazást
> * Alkalmazás végigvezeti
> * Az erőforrások eltávolítása


Ha nincs előfizetése, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Előfeltételek
- Nincsenek
- Az Azure-előfizetés használata ajánlott, de nem szükséges a kipróbáláshoz

## <a name="create-a-smart-meter-monitoring-app"></a>Intelligens fogyasztásmérő monitorozására szolgáló alkalmazás létrehozása 

Ezt az alkalmazást három egyszerű lépésben hozhatja létre:

1. Nyissa meg az [Azure IoT Central kezdőlapját](https://apps.azureiotcentral.com) , és kattintson a **Build** (létrehozás) gombra egy új alkalmazás létrehozásához. 
1. Válassza **az energia** fület, majd kattintson az alkalmazás **létrehozása** elemre az **intelligens fogyasztásmérő figyelése** alkalmazás csempén.

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás összeállítása](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

1. Az alkalmazás **létrehozása** megnyitja az **új alkalmazás** űrlapot. Adja meg a kért adatokat az alábbi ábrán látható módon:
    * **Alkalmazás neve**: válasszon egy nevet a IoT Central alkalmazás számára. 
    * **URL**: válasszon egy IoT Central URL-címet, a platform ellenőrzi annak egyediségét.
    * **7 napos ingyenes próbaverzió**: Ha már rendelkezik Azure-előfizetéssel, az alapértelmezett beállítás ajánlott. Ha nem rendelkezik Azure-előfizetéssel, kezdje az ingyenes próbaverzióval.
    * **Számlázási információ**: maga az alkalmazás ingyenes. Az alkalmazás erőforrásainak kiépítéséhez a címtár, az Azure-előfizetés és a régió részletei szükségesek.
    * Kattintson a lap alján található **Létrehozás** gombra, és az alkalmazás egy percen belül létrejön.

        ![Új alkalmazás űrlapja](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Új alkalmazás-űrlap számlázási adatai](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)

### <a name="verify-the-application-and-simulated-data"></a>Az alkalmazás és a szimulált adatértékek ellenőrzése

Az újonnan létrehozott intelligens fogyasztásmérő alkalmazás az alkalmazás, és bármikor módosítható. Győződjön meg arról, hogy az alkalmazás telepítve van, és a módosítás előtt a várt módon működik.

Az alkalmazás létrehozásának és adatszimulációjának ellenőrzéséhez nyissa meg az **irányítópultot**. Ha a csempék adatai megtekinthetők, az alkalmazás üzembe helyezése sikeres volt. Az adat-szimuláció eltarthat néhány percig az adat létrehozásakor, így 1-2 percet is igénybe vehet. 

## <a name="application-walk-through"></a>Alkalmazás végigvezeti
Az alkalmazás sikeres üzembe helyezése után a minta intelligens fogyasztásmérő eszköz, az eszköz modellje és az irányítópult is megtalálható. 

A adatum egy kitalált energetikai vállalat, amely figyeli és felügyeli az intelligens fogyasztásmérőket. Az intelligens fogyasztásmérő monitorozási irányítópultján megjelenik az intelligens fogyasztásmérő tulajdonságai, az adathalmazok és a minták parancsai. Lehetővé teszi a kezelők és a támogatási csapatok számára, hogy proaktív módon végezzék el a következő tevékenységeket, mielőtt a támogatási incidensekre váltanak: 
* Tekintse át a legújabb mérési adatokat és a telepítési helyét a térképen
* Proaktív módon ellenőrizhető a mérési hálózat és a kapcsolatok állapota 
* A hálózat állapotának minimális és maximális feszültségének figyelése 
* Tekintse át az energia, a teljesítmény és a feszültség tendenciáit, hogy bármilyen rendellenes mintázatot kapjon 
* A teljes energiafogyasztás nyomon követése tervezési és számlázási célokra
* Parancs-és vezérlési műveletek, például újrakapcsolási mérő és a belső vezérlőprogram frissítése. A sablonban a parancsgombok a lehetséges funkciókat mutatják, és nem küldenek valós parancsokat. 

> [!div class="mx-imgBorder"]
> ![Intelligens fogyasztásmérő monitorozási irányítópultja](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Eszközök
Az alkalmazás egy minta intelligens mérőszám-eszközt tartalmaz. Az eszköz adatait a **Devices (eszközök** ) lapra kattintva tekintheti meg.

> [!div class="mx-imgBorder"]
> ![Intelligens fogyasztásmérő eszközei](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Az eszköz részleteinek megtekintéséhez kattintson a minta eszköz **SM0123456789** hivatkozására. Frissítheti az eszköz írható tulajdonságait a **frissítés tulajdonságai** lapon, és megjelenítheti a frissített értékeket az irányítópulton.

> [!div class="mx-imgBorder"]
> ![Intelligens fogyasztásmérő tulajdonságai](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Eszköz sablonja
Az intelligenskártya-modell megjelenítéséhez kattintson az **eszköz sablonok** lapfülre. A modell előre definiált felületet tartalmaz az adattípushoz, a tulajdonsághoz, a parancsokhoz és a nézetekhez.

> [!div class="mx-imgBorder"]
> ![Smart Meter-eszközök sablonjai](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha úgy dönt, hogy nem folytatja az alkalmazás használatát, törölje az alkalmazást a következő lépésekkel:

1. A bal oldali ablaktáblában nyissa meg az adminisztráció lapot.
1. Válassza az Alkalmazásbeállítások lehetőséget, majd kattintson a lap alján található Törlés gombra. 

    > [!div class="mx-imgBorder"]
    > ![Alkalmazás törlése](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)

## <a name="next-steps"></a>Következő lépések

A Smart Meter alkalmazás architektúrájának megismeréséhez lásd:

> [!div class="nextstepaction"]
> [Intelligens fogyasztásmérő alkalmazás architektúrája](./concept-iot-central-smart-meter-app.md)
