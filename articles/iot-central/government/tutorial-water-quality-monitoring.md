---
title: 'Oktatóanyag: vízminőség-figyelési alkalmazás létrehozása az Azure IoT Central'
description: 'Oktatóanyag: megtudhatja, hogyan hozhat létre vízminőség-figyelési alkalmazást az Azure IoT Central alkalmazás-sablonok használatával.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 3e55970b0603da9be9bf28a50d4c474e34a1cad3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017239"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Oktatóanyag: vízminőség-figyelési alkalmazás létrehozása az Azure-ban IoT Central

Ez az oktatóanyag végigvezeti Önt egy vízminőség-figyelési alkalmazás létrehozásán az Azure IoT Centralban. Az alkalmazást az Azure IoT Central **Water Quality monitoring** Application sablon alapján hozza létre.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A **vízminőség-figyelési** sablon használatával hozhat létre vízminőség-figyelési alkalmazást.
> * Egy operátor irányítópultjának megismerése és testreszabása.
> * Fedezze fel a víz minőségének monitorozására szolgáló eszköz sablonját.
> * Szimulált eszközök megismerése.
> * A szabályok megismerése és konfigurálása.
> * Feladatok konfigurálása.
> * Az alkalmazás arculatának testreszabása fehér címkével.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy az oktatóanyag elvégzéséhez Azure-előfizetéssel rendelkezzen. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Vízminőség-figyelési alkalmazás létrehozása az Azure-ban IoT Central

Ebben a szakaszban az Azure IoT Central **vízminőség-figyelési** sablonnal hozza létre a vízminőség-figyelési alkalmazást.

1. Nyissa meg az [Azure IoT Central kezdőlapját](https://aka.ms/iotcentral).

    Ha rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal. Ellenkező esetben jelentkezzen be Microsoft-fiók:

    ![Jelentkezzen be a szervezeti fiókjába](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Válassza a **Build** elemet az Azure IoT Central bal szélső paneljén, és válassza a **kormányzat** fület. A kormányzat ablaktáblán számos kormányzati alkalmazás-sablon látható.

    ![Kormányzati alkalmazások sablonjai](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. Válassza ki a **víz minőségének figyelése** alkalmazás sablonját. Ez az alkalmazási sablon tartalmaz egy vízminőség-eszköz sablont, a szimulált eszközöket, az operátor irányítópultot és az előre konfigurált figyelési szabályokat.

1. Válassza az **Alkalmazás létrehozása** lehetőséget. Megnyílik az **új alkalmazás** panel, és megjeleníti a következő elemeket:

    * **Alkalmazásnév**: alapértelmezés szerint az alkalmazás neve a **víz minőségének monitorozása** , amelyet az Azure IOT Central által generált egyedi azonosító sztring követ. Ha szeretné, megadhatja a megjelenítendő nevet, vagy később módosíthatja az alkalmazás nevét.
    * **URL**: bármilyen URL-címet megadhat, vagy később módosíthatja az URL-címet.
    * Ha rendelkezik Azure-előfizetéssel, adja meg a **címtár**, az **Azure-előfizetés** és a **régió** értékeit. Ha nem rendelkezik előfizetéssel, bekapcsolhatja a **7 napos ingyenes próbaverziót** , és elvégezheti a szükséges kapcsolattartási adatokat.

    További információ a címtárakról és az előfizetésekről: [alkalmazás létrehozása](../core/quick-deploy-iot-central.md) – rövid útmutató.

1. Válassza a **Létrehozás** gombot a lap bal alsó részén.

    ![Az Azure IoT Central új alkalmazás oldal](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

    ![Az Azure IoT Central új alkalmazás számlázási adatai](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1-billinginfo.png)

Ezzel létrehozott egy vízminőség-figyelési alkalmazást az Azure IoT Central **Water minőség-figyelési** sablonnal.

Az új alkalmazás a következő előre konfigurált összetevőkből áll:

* Kezelői irányítópultok
* Víz minőségének figyelésére szolgáló eszközök sablonjai
* Szimulált víz minőségének monitorozására szolgáló eszközök
* Szabályok és feladatok
* Fehér címkét használó branding

Az alkalmazást bármikor módosíthatja.

Ezután vizsgálja meg az alkalmazást, és végezze el a testreszabásokat.

## <a name="explore-and-customize-the-operator-dashboard"></a>Az operátor irányítópultjának megismerése és testreszabása

Az alkalmazás létrehozása után megnyílik a **Wide World Water Quality irányítópult** panel.

   ![A víz minőségének monitorozási irányítópultja](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Építőként létrehozhat és testreszabhat nézeteket az irányítópulton a kezelők általi használatra. A Testreszabás előtt azonban először vizsgálja meg az irányítópultot.

Az irányítópulton megjelenő összes érték szimulált eszközön alapul, amelyet a következő szakaszban ismertetünk.

Az irányítópult a következő típusú csempéket tartalmazza:

* A **globális vízkészlet-rendszerkép csempe**: az irányítópult bal felső sarkában található első csempe egy olyan rendszerkép, amely a széles világ nevű fiktív segédprogramot jeleníti meg. A csempét testreszabhatja saját rendszerképének használatára, vagy eltávolíthatja a csempét.

* **Átlagos pH KPI-csempék**: a KPI-csempék, például az **átlagos pH-érték az utolsó 30 percben** az irányítópult panel felső részén találhatók. Testreszabhatja a KPI-csempéket, és beállíthatja, hogy egy másik típus-és időtartomány legyen.

* **Vízfigyelési terület leképezése**: az Azure IoT Central Azure Maps használ, amelyet közvetlenül az alkalmazásban lehet beállítani az eszköz helyének megjelenítéséhez. Az alkalmazásból az eszközre is leképezheti a tartózkodási hely adatait, majd a Azure Maps használatával megjelenítheti az adatokat egy térképen. Vigye a kurzort a Térkép fölé, és próbálja meg a vezérlőket.

* **Átlagos pH-eloszlási hő – Térkép diagram**: kiválaszthat különböző vizualizációs diagramokat az eszköz telemetria megjelenítéséhez az alkalmazás számára legmegfelelőbb módon.

* **Kritikus minőségi mutatók diagramja**: az telemetria ábrázolhatja egy időtartományon belül.  

* **Kémiai ágensek sávdiagram-diagramja**: megjelenítheti az eszköz telemetria egy oszlopdiagramon.

* **Művelet gomb**: az irányítópult olyan műveleteket tartalmaz, amelyeket az operátor közvetlenül a figyelési irányítópulton indíthat el. Az eszköz tulajdonságainak alaphelyzetbe állítása egy példa az ilyen műveletekre.

* **Tulajdonságlap-csempék**: az irányítópulton több tulajdonságlap is szerepel, amelyek a küszöbértéket, az eszköz állapotát és a karbantartási adatokat jelölik.

### <a name="customize-the-dashboard"></a>Az irányítópult testreszabása

Építőként testreszabhatja a nézeteket az irányítópulton a kezelők általi használatra.

1. Válassza a **Szerkesztés** lehetőséget a **globális vízminőség irányítópult** -ablaktábla testreszabásához. Testreszabhatja az irányítópultot a **szerkesztési** menü parancsainak kiválasztásával. Miután az irányítópult szerkesztési módban van, új csempéket adhat hozzá, vagy beállíthatja a meglévő fájlokat.

    ![Irányítópult szerkesztése](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Válassza az **+ új** lehetőséget a konfigurálható új irányítópult létrehozásához. Több irányítópult is lehet, és az irányítópult menüjéből is lehet navigálni.

## <a name="explore-a-water-quality-monitoring-device-template"></a>A víz minőségének monitorozására szolgáló eszköz sablonjának megismerése

Az Azure IoT Central egy eszköz képességeit határozza meg. Az elérhető képességek a következők: telemetria, Properties és parancsok. Építőként megadhatja az Azure IoT Centralban a csatlakoztatott eszközök képességeit képviselő eszközöket. Az eszköz sablonjának és alkalmazásának teszteléséhez szimulált eszközöket is létrehozhat.

A létrehozott vízminőség-figyelési alkalmazáshoz tartozik egy vízminőség-figyelési eszköz sablonja.

Az eszköz sablonjának megtekintése:

1. Válassza ki az **eszköz sablonjait** az alkalmazás bal szélső paneljén az Azure IoT Centralban.
1. Az eszközök listájában válassza a **vízminőség-figyelő** elemet. Megnyílik az eszköz sablonja.

    ![Az eszköz sablonja](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Sablon testreszabása

A következő eszközbeállítások beállításainak testreszabása:

1. Az eszköz sablonja menüben válassza a **Testreszabás** lehetőséget.
1. Lépjen a **hőmérséklet** telemetria típusra.
1. Módosítsa a **megjelenített név** értékét a **jelentett hőmérséklet** értékre.
1. Módosítsa a mértékegységet, vagy állítsa be a **minimális értéket** és a **maximális értéket**.
1. Kattintson a **Mentés** gombra.

#### <a name="add-a-cloud-property"></a>Felhőbeli tulajdonság hozzáadása

1. Az eszköz sablonja menüben válassza a **Cloud Property** elemet.
1. Új Felhőbeli tulajdonság hozzáadásához válassza a **+ Cloud Property hozzáadása** elemet. Az Azure IoT Centralban hozzáadhat egy olyan tulajdonságot, amely egy eszközhöz kapcsolódik, de az nem várható, hogy az eszköz elküldje őket. Ilyen tulajdonság például a telepítési területre, az eszköz adataira vagy a karbantartási információkra jellemző riasztási küszöbérték.
1. Kattintson a **Mentés** gombra.

### <a name="explore-views"></a>Nézetek megismerése

A víz minőségének monitorozására szolgáló eszköz sablonja előre meghatározott nézetekkel rendelkezik. A nézetek határozzák meg, hogy az operátorok hogyan látják az eszköz információit és a felhő tulajdonságainak beállítását. Ismerkedjen meg a nézetekkel és a gyakorlat módosításával.

  ![Eszköz sablon nézetei](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Az eszközsablon közzététele

Ha bármilyen módosítást végez, ügyeljen arra, hogy a **Közzététel** lehetőségre kattintva tegye közzé az eszköz sablonját.

### <a name="create-a-new-device-template"></a>Új eszközsablon létrehozása

1. Válassza az **+ új** lehetőséget egy új sablon létrehozásához, és kövesse a létrehozási folyamatot.
1. Hozzon létre egy egyéni sablont, vagy válasszon egy sablont az Azure IoT-eszköz katalógusában.

## <a name="explore-simulated-devices"></a>Szimulált eszközök megismerése

Az alkalmazás sablonjában létrehozott víz minőség-figyelési alkalmazás két szimulált eszközzel rendelkezik. Ezek az eszközök a víz minőségének monitorozására szolgáló eszköz sablonját képezik le.

### <a name="view-the-devices"></a>Az eszközök megtekintése

1. Válassza az **eszközök** lehetőséget az alkalmazás bal szélső paneljén.

   ![Eszközök](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Válasszon ki egy szimulált eszközt.

    ![1. eszköz kiválasztása](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. A **felhő tulajdonságai** lapon módosítsa a **savasság (pH) küszöbértékét** **8** – **9** értékre.
1. Fedezze fel az **eszköz tulajdonságai** lapot és az **eszköz irányítópultját** .

> [!NOTE]
> Minden lap a **sablon nézeteiből** lett konfigurálva.

### <a name="add-new-devices"></a>Új eszközök hozzáadása

Új eszköz hozzáadásához az **eszközök** lapon válassza az **+ új** lehetőséget.

## <a name="explore-and-configure-rules"></a>Szabályok megismerése és konfigurálása

Az Azure IoT Centralban olyan szabályokat hozhat létre, amelyek automatikusan figyelik az eszközök telemetria. Ezek a szabályok egy műveletet aktiválnak, ha bármelyik feltétel teljesül. Az egyik lehetséges művelet az e-mail-értesítések küldése. A további lehetőségek közé tartozik az automatizálási művelet vagy egy webhook művelet, amellyel az adatküldés más szolgáltatásoknak.

A létrehozott vízminőség-figyelési alkalmazásnak két előre konfigurált szabálya van.

### <a name="view-rules"></a>Szabályok megtekintése

1. Válassza ki a **szabályokat** az alkalmazás bal szélső paneljén.

   ![Szabályok](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Válassza a **magas pH-riasztás** lehetőséget, amely az alkalmazás előre konfigurált szabályainak egyike.

   ![A magas pH-riasztási szabály](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   A **magas pH-értékű riasztási** szabály úgy van konfigurálva, hogy a savasság (pH) a 8-asnél nagyobb állapotát vizsgálja.

Ezután adjon hozzá egy e-mail-műveletet a szabályhoz:

1. Válassza a **+ e-mail** lehetőséget.
1. A **megjelenítendő név** mezőbe írja be a **magas pH-riasztást**.
1. A **to** mezőben adja meg az Azure IoT Central-fiókjához társított e-mail-címet.
1. Szükség esetén megadhat egy megjegyzést, amely az e-mail szövegében szerepel.
1. A művelet befejezéséhez kattintson a **kész** gombra.
1. A Save ( **Mentés** ) gombra kattintva mentse és aktiválja az új szabályt.

Néhány percen belül e-mailt kell kapnia a beállított feltétel teljesülése esetén.

> [!NOTE]
> Az alkalmazás minden alkalommal küld e-mailt, amikor egy feltétel teljesül. Válassza a **Letiltás** lehetőséget egy szabályhoz, hogy ne kapjon automatikus e-mailt az adott szabálytól.
  
Új szabály létrehozásához válassza a **szabályok** elemet az alkalmazás bal szélső paneljén, majd válassza az **+ új** lehetőséget.

## <a name="configure-jobs"></a>Feladatok konfigurálása

Az Azure IoT Central-feladatokkal több eszközön is aktiválhatja a frissítéseket az eszköz vagy a felhő tulajdonságaihoz. A feladatok használatával több eszközön is aktiválhatja az eszközök parancsait. Az Azure IoT Central automatizálja a munkafolyamatot.

1. Válassza a **feladatok** lehetőséget az alkalmazás bal szélső paneljén.
1. Válassza az **+ új** lehetőséget, és konfiguráljon egy vagy több feladatot.

## <a name="customize-your-application"></a>Az alkalmazás testreszabása

Építőként több beállítást is módosíthat az alkalmazás felhasználói felületének testreszabásához.

1. Válassza   >  **az adminisztráció testre szabása alkalmazást**.
1. Az **alkalmazás emblémája** területen válassza a **módosítás** lehetőséget, hogy kiválassza a logóként feltölteni kívánt képet.
1. A **böngésző ikonja** alatt válassza a **módosítás** lehetőséget a böngésző lapjain megjelenő rendszerkép kiválasztásához.
1. A **böngésző színei** területen lecserélheti az alapértelmezett értékeket HTML hexadecimális színkódokkal.
1. Válassza a **Beállítások** lehetőséget a **téma** értékének módosításához.

   ![Az alkalmazás testreszabása](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Az alkalmazás rendszerképének frissítése

1. Válassza az **adminisztrációs**  >  **alkalmazás beállításai** lehetőséget.

1. A **rendszerkép kiválasztása** gomb használatával kiválaszthatja az alkalmazási képként feltölteni kívánt képet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az alkalmazást az alábbi lépésekkel:

1. Nyissa meg az **Adminisztráció** fület az alkalmazás bal szélső paneljén.
1. Válassza az **Alkalmazásbeállítások** lehetőséget, majd kattintson a **Törlés** gombra.

    ![Az alkalmazás törlése](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Következő lépések

* További információ 

> [!div class="nextstepaction"]
> a [víz minőségének monitorozása](./concepts-waterqualitymonitoring-architecture.md).
