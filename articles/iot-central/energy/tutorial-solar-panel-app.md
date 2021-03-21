---
title: 'Oktatóanyag: Solar panel monitoring-alkalmazás létrehozása az Azure IoT Central'
description: 'Oktatóanyag: Ismerje meg, hogyan hozhat létre napelem-alkalmazást Azure IoT Central alkalmazás-sablonok használatával.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 509e31919dd974da253cd0478a70f889cc060fae
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831790"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>Oktatóanyag: a napelem-figyelő alkalmazás sablonjának létrehozása és megismerése 

Ez az oktatóanyag végigvezeti Önt a napelemes figyelő alkalmazás létrehozásának folyamatán, amely egy szimulált adattal rendelkező minta-eszköz modellt tartalmaz. Ebből az oktatóanyagból az alábbiakat sajátíthatja el:


> [!div class="checklist"]
> * Ingyenes Solar panel-alkalmazás létrehozása
> * Az alkalmazás végigvezeti
> * Az erőforrások eltávolítása


Ha nem rendelkezik előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez nincsenek előfeltételek. Az Azure-előfizetések használata ajánlott, de nem kötelező.


## <a name="create-a-solar-panel-monitoring-app"></a>Napelempanel monitorozására szolgáló alkalmazás létrehozása 

Ezt az alkalmazást három egyszerű lépésben hozhatja létre:

1. Nyissa meg az [Azure IoT Central](https://apps.azureiotcentral.com). Új alkalmazás létrehozásához válassza a **Létrehozás** lehetőséget. 

1. Válassza az **energia** lapot. A **nap panel figyelés** területén válassza az **alkalmazás létrehozása** lehetőséget. 

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép az Azure IoT Central Build lehetőségeiről.](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. Az **új alkalmazás** párbeszédpanelen adja meg a kért adatokat, majd válassza a **Létrehozás** lehetőséget:
    * **Alkalmazásnév**: válasszon egy nevet az Azure IoT Central alkalmazás számára. 
    * **URL**: válasszon ki egy Azure IoT Central URL-címet. A platform ellenőrzi az egyediségét.
    * **Díjszabási csomag**: Ha már rendelkezik Azure-előfizetéssel, az alapértelmezett beállítás ajánlott. Ha nem rendelkezik Azure-előfizetéssel, kezdje az ingyenes próbaverzióval.
    * **Számlázási információ**: maga az alkalmazás ingyenes. Az alkalmazás erőforrásainak kiépítéséhez a címtár, az Azure-előfizetés és a régió részletei szükségesek.
        ![Képernyőkép az új alkalmazásról.](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Képernyőkép a számlázási adatokról.](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Az alkalmazás és a szimulált adatértékek ellenőrzése

Bármikor módosíthatja az új Solar panel alkalmazást. Egyelőre ellenőrizze, hogy az alkalmazás telepítve van-e, és megfelelően működik-e a módosítás előtt.

Az alkalmazás létrehozásának és adatszimulációjának ellenőrzéséhez nyissa meg az **irányítópultot**. Ha a csempék adatai megtekinthetők, az alkalmazás üzembe helyezése sikeres volt. Az adatszimuláció eltarthat néhány percig az adat létrehozásakor. 

## <a name="application-walk-through"></a>Alkalmazás végigvezeti
Az alkalmazás sablonjának sikeres üzembe helyezése után érdemes megvizsgálni az alkalmazást. Figyelje meg, hogy a minta intelligens fogyasztásmérő eszköz, eszköz modell és irányítópult.

A adatum egy fiktív energetikai cég, amely a napelemeket figyeli és kezeli. A napelem-figyelési irányítópulton a napelemek tulajdonságai, az adathalmazok és a minták parancsai láthatók. Ez az irányítópult lehetővé teszi, hogy a támogatási csapat proaktív módon hajtsa végre a következő tevékenységeket, mielőtt bármilyen probléma további támogatásra szorul:
* Tekintse át a panel legújabb adatait és a hozzá tartozó telepített helyet a térképen.
* Keresse meg a panel állapotát és a kapcsolatok állapotát.
* Tekintse át az energia-előállítási és hőmérsékleti trendeket, hogy minden rendellenes mintázatot elkapjon.
* Nyomon követheti a teljes energiatermelést tervezési és számlázási célokra.
* Ha szükséges, aktiválja a panelt, és frissítse a belső vezérlőprogram verzióját. A sablonban a parancsgombok a lehetséges funkciókat mutatják, és nem küldenek valódi parancsokat.

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel a napelem figyelési sablonjának irányítópultról.](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Eszközök
Az alkalmazás egy minta napelemes eszközt tartalmaz. Az eszköz részleteinek megtekintéséhez válassza az **eszközök** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Képernyőkép a napelemes figyelési sablon eszközeiről.](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

Válassza ki a minta eszközt, **SP0123456789**. A **frissítés tulajdonságai** lapon frissítheti az eszköz írható tulajdonságait, és megtekintheti a frissített értékek vizualizációját az irányítópulton. 

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel a napelem-figyelési sablon frissítés tulajdonságai lapról.](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Eszköz sablonja
A napelemes eszköz modell megjelenítéséhez válassza az **eszközök sablonok** fület. A modell előre definiált csatolókat tartalmaz az adattípusokhoz, a tulajdonságokhoz, a parancsokhoz és a nézetekhez.

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel a napelemek figyeléséhez sablon eszköz sablonjairól.](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha úgy dönt, hogy nem folytatja az alkalmazás használatát, törölje az alkalmazást az alábbi lépésekkel:

1. A bal oldali panelen válassza az **Adminisztráció** lehetőséget.
1. Válassza az **Alkalmazásbeállítások**  >  **Törlés** lehetőséget. 

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a napelem figyelési sablon felügyeletéről.](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Következő lépések
 
> [!div class="nextstepaction"]
> [Azure IoT Central – napelemes alkalmazások architektúrája](./concept-iot-central-solar-panel-app.md)

