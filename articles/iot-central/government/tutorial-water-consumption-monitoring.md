---
title: 'Oktatóanyag: vízfogyasztást figyelő alkalmazás létrehozása az Azure IoT Central'
description: 'Oktatóanyag: megtudhatja, hogyan hozhat létre vízfelhasználást figyelő alkalmazást Azure IoT Central alkalmazás-sablonok használatával.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 476a0c2d75dcbee5c1ed4d758e0cbc9d4726ff95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587196"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Oktatóanyag: vízfogyasztást figyelő alkalmazás létrehozása az Azure IoT Central

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Azure IoT Central Water-figyelő alkalmazást az Azure IoT Central Water-használat figyelési alkalmazás sablonjának használatával.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Használja az Azure IoT Central Water-használat figyelési sablonját a vízfogyasztás figyelésére szolgáló alkalmazás létrehozásához.
> * Az operátor irányítópultjának megismerése és testreszabása.
> * Fedezze fel az eszközök sablonjait.
> * Szimulált eszközök megismerése.
> * A szabályok megismerése és konfigurálása.
> * Feladatok konfigurálása.
> * Testreszabhatja az alkalmazás arculatát a fehér címkézés használatával.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Víz-használati monitorozási alkalmazás létrehozása az Azure IoT Central

Ebben a szakaszban az Azure IoT Central Water-használat figyelési sablonnal hozza létre a víz-felhasználás figyelése alkalmazást az Azure-ban IoT Central.

Új Azure IoT Central Water-használat figyelési alkalmazásának létrehozása:

1. Nyissa meg az [Azure IoT Central Kezdőlap](https://aka.ms/iotcentral) webhelyét.

    Ha rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal. Ellenkező esetben jelentkezzen be Microsoft-fiók használatával.

    ![Lépjen a céges fiókjába](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Válassza a **Létrehozás** lehetőséget a bal oldali ablaktáblán, és válassza a **kormányzat** fület. A **kormányzat** oldalon számos kormányzati alkalmazás-sablon látható.

   ![Kormányzati alkalmazás-sablonok készítése](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Válassza ki a **víz-felhasználás figyelése** alkalmazás sablonját.
Ez a sablon egy minta vízfogyasztási eszköz sablonját, egy szimulált eszközt, egy kezelő irányítópultot és előre konfigurált figyelési szabályokat tartalmaz.

1. Válassza az alkalmazás **létrehozása** lehetőséget az **új alkalmazás** -létrehozási űrlap megnyitásához a következő mezőkkel:
    * **Alkalmazásnév**: alapértelmezés szerint az alkalmazás a *vízfogyasztás figyelését* , majd egy egyedi azonosító karakterláncot használ, amelyet az Azure IoT Central generál. Igény szerint válasszon egy felhasználóbarát nevet. Az alkalmazás neve később is módosítható.
    * **URL**: az Azure IoT Central automatikusan létrehozza az URL-címet az alkalmazás neve alapján. Megadhatja, hogy az URL-címet saját ízlése szerint frissítse. Az URL-címet később is megváltoztathatja.
    * Ha rendelkezik Azure-előfizetéssel, adja meg a **címtárat**, az **Azure-előfizetést** és a **helyadatok** adatait. Ha nem rendelkezik előfizetéssel, kiválaszthatja a **7 napos ingyenes próbaverzió** lehetőséget, és elvégezheti a szükséges kapcsolattartási adatokat.

    A címtárakkal és az előfizetésekkel kapcsolatos további információkért lásd: alkalmazás-útmutató [létrehozása](../core/quick-deploy-iot-central.md).

1. A lap alján kattintson a **Létrehozás** gombra.

    ![Azure IoT Central új alkalmazás lap](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Azure IoT Central számlázási adatok lap](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Ezzel létrehozott egy vízfelhasználás-figyelési alkalmazást az Azure IoT Central Water-felhasználás figyelési sablonnal.

A víz-felhasználás figyelése alkalmazás előre konfigurált:

* Minta operátor irányítópultok.
* Példa előre definiált víz flow-és Valve-sablonokhoz.
* Szimulált vízfolyás és intelligens Valve-eszközök.
* Szabályok és feladatok.
* Minta branding a fehér címkézés használatával.

Ez az Ön alkalmazása, és bármikor módosítható. Most Ismerkedjen meg az alkalmazással, és végezze el a testreszabást.

## <a name="explore-and-customize-the-operator-dashboard"></a>Az operátor irányítópultjának megismerése és testreszabása

Az alkalmazás létrehozása után megnyílik a minta **Wide World Water-felhasználás irányítópult** .

   ![Vízfogyasztás figyelése irányítópult](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Építőként létrehozhat és testre szabhatja a nézeteket az irányítópulton a kezelők számára. Ismerkedjen meg az irányítópulttal, és próbálja meg testreszabni.

> [!NOTE]
> Az irányítópulton megjelenő összes érték szimulált eszközön alapul, amelyet a következő szakaszban vizsgálunk meg.
  
Az irányítópult különböző típusú csempéket tartalmaz:

* A **globális vízkészletek rendszerképek csempe**: az irányítópult első csempe a kitalált víz-segédprogram széles világának képcsempe-képe. A csempét testreszabhatja saját rendszerkép beszúrásával vagy eltávolításával.
* **Átlagos vízfolyás KPI-csempe**: a KPI csempe úgy van konfigurálva, hogy az *átlagot az elmúlt 30 percben* jelenítse meg. Testreszabhatja a KPI csempét, és beállíthatja egy másik típusra és időtartományra is.
* **Eszköz-parancssori csempék**: ezek a csempék közé tartozik a **szelep lezárása**, az **Open Valve** és a **Valve position** csempék beállítása. A parancsok kiválasztásával elvégezheti a szimulált eszköz parancs lapját. Az Azure IoT Centralban a *parancs* egy *eszköz-képesség* típusú. Ezt a koncepciót később a jelen oktatóanyag "eszköz sablonja" szakaszában fogjuk feltárni.
* **Vízterjesztési terület leképezése**: a Térkép Azure Maps használ, amelyet közvetlenül az Azure IoT Central konfigurálhat. A Térkép csempén az eszköz helye látható. Vigye a kurzort a Térkép fölé, és próbálja ki a vezérlőelemeket a térképen, például *Nagyítás*, *kicsinyítés* vagy *kibővítés*.

    ![Vízfogyasztás figyelése irányítópult-Térkép](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* A **víz átlagos folyamatábrája** és a **környezeti feltétel diagramja**: megjelenítheti egy vagy több, a kívánt időtartományon belül telemetriáiról ábrázoló eszközt.
* Az **átlagos Valve Pressure hő diagramja**: kiválaszthatja a hő megjelenítési típusát, amelyet az adott időtartományon belül, egy színindexszel rendelkező telemetria szeretne megjeleníteni.
* **Riasztási küszöbértékek alaphelyzetbe állítása csempe**: megadhatja a hívás – művelet tartalmú csempéket, és beágyazhat egy hivatkozást egy művelet lapra. Ebben az esetben a riasztások alaphelyzetbe állítása küszöbértékre viszi az alkalmazás **feladatait**, ahol a frissítéseket futtathatja az eszköz tulajdonságai között. Ezt a lehetőséget később a jelen oktatóanyag "feladatok konfigurálása" szakaszában fogjuk feltárni.
* **Tulajdonságlapok**: az irányítópulton megjelennek a **Valve működési adatai**, a **flow riasztási küszöbértékei** és a **karbantartási információk** csempéi.

### <a name="customize-the-dashboard"></a>Az irányítópult testreszabása

Építőként testreszabhatja a nézeteket az irányítópulton a kezelők számára.

1. Válassza a **Szerkesztés** lehetőséget a **globális vízfelhasználás irányítópultjának** testreszabásához. A **Szerkesztés** menü kiválasztásával testreszabhatja az irányítópultot. Miután az irányítópult **szerkesztési** módban van, hozzáadhat új csempéket, vagy beállíthatja azt.

     ![Irányítópult szerkesztése](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Válassza az **+ új** lehetőséget, ha új irányítópultot szeretne létrehozni, és azt a semmiből konfigurálja. Több irányítópulttal is rendelkezhet, és az irányítópultok menüjében válthat az irányítópultok között.

## <a name="explore-the-device-template"></a>Az eszköz sablonjának megismerése

Az Azure IoT Central egy eszköz, amely a telemetria, a Property vagy a parancshoz használható eszköz képességeit határozza meg. A Builder segítségével megadhat egy vagy több, az Azure IoT Centralban megjelenő eszközt, amely a csatlakoztatott eszközök képességeit képviseli.

A víz-felhasználás figyelésére szolgáló alkalmazás két, a *flow-mérőt* és egy *intelligens Valve* -eszközt képviselő, hivatkozással rendelkező eszközt tartalmaz.

Az eszköz sablonjának megtekintése:

1. Az Azure IoT Centralban az alkalmazás bal oldali ablaktábláján válassza az **eszközök sablonjait** . Az **eszközök** listájában két eszköz sablonja, az **intelligens szelep** és a flow- **mérő** látható.

   ![Eszköz sablonja](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Válassza ki a **flow-mérő** eszköz sablonját, és ismerkedjen meg az eszköz képességeivel.

     ![Eszköz sablonjának folyamatábrája](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Sablon testreszabása

Az eszköz sablonjának testreszabása:

1. Kattintson a **Testreszabás** elemre az **eszközök sablonok** menüjében.
1. Keresse meg a `Temperature` telemetria típusát.
1. Frissítse a **megjelenítendő nevét** a következőre: `Temperature` `Reported temperature` .
1. Frissítse a mértékegységet, vagy állítsa be a **minimális értéket** és a **maximális értéket**.
1. A módosítások mentéséhez kattintson a **Mentés** gombra.

### <a name="add-a-cloud-property"></a>Felhőbeli tulajdonság hozzáadása

1. Lépjen a **felhő tulajdonságai** elemre az **eszközök sablonok** menüjében.
1. Vegyen fel egy új Cloud tulajdonságot a **+ Cloud tulajdonság hozzáadásával**.
    Az Azure IoT Centralban hozzáadhat egy, az eszközhöz kapcsolódó tulajdonságot is. A Felhőbeli tulajdonság például a telepítési területre, az eszközre és egyéb karbantartási információkra vonatkozó riasztási küszöbérték lehet.
1. A módosítások mentéséhez kattintson a **Mentés** gombra.

### <a name="views"></a>Megjelenítések

A víz-felhasználási figyelő eszköz sablonja előre meghatározott nézetekkel rendelkezik. Ismerkedjen meg a nézetekkel, és frissítheti a frissítéseket. A nézetek határozzák meg, hogy az operátorok hogyan látják az eszköz adatokat, hanem a felhő tulajdonságait is.

  ![Eszköz sablon nézetei](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Közzététel

Ha módosította a módosításokat, ügyeljen arra, hogy **tegye közzé** az eszköz sablonját.

### <a name="create-a-new-device-template"></a>Új eszközsablon létrehozása

Válassza az **+ új** lehetőséget egy új sablon létrehozásához, és kövesse a létrehozási folyamatot.
Létrehozhat egy teljesen új egyéni sablont, vagy kiválaszthat egy sablont az Azure-eszköz katalógusában.

## <a name="explore-simulated-devices"></a>Szimulált eszközök megismerése

Az Azure IoT Central segítségével szimulált eszközöket hozhat létre az eszköz sablonjának és alkalmazásának teszteléséhez. A vízfelhasználás-figyelési alkalmazás két szimulált eszközhöz van rendelve a **áramlásmérő** és az **intelligens Valve** -eszközök sablonjaihoz.

### <a name="view-the-devices"></a>Az eszközök megtekintése

1. Válassza az **eszközök**  >  **minden eszköz** lehetőséget a bal oldali ablaktáblán.

   ![Minden eszköz panel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Válassza az **1. intelligens szelep** elemet.

    ![1. intelligens szelep](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. A **parancsok** lapon megtekintheti a három eszköz parancsait (a **Valve**, a **Valve** és a **set Valve pozícióját**), amelyek az **intelligens Valve** -eszköz sablonjában meghatározott képességek.

1. Fedezze fel az **eszköz tulajdonságai** lapot és az **eszköz irányítópultját** .

> [!NOTE]
> Vegye figyelembe, hogy az összes lap az eszköz sablon nézeteiből van konfigurálva.

### <a name="add-new-devices"></a>Új eszközök hozzáadása

Új eszközök hozzáadásához válassza az **eszközök** lap **+ új** elemét.

## <a name="explore-and-configure-rules"></a>Szabályok megismerése és konfigurálása

Az Azure IoT Centralban olyan szabályokat hozhat létre, amelyek automatikusan figyelik az eszközök telemetria és aktiválják a műveleteket, ha egy vagy több feltétel teljesül. A műveletek közé tartozhatnak az e-mail-értesítések küldése vagy a Microsoft energiagazdálkodási műveletének elindítása vagy egy webhook művelet, amely adatokat küld más szolgáltatásoknak.

A létrehozott víz-felhasználási figyelő alkalmazás három előre konfigurált szabályt tartalmaz.

### <a name="view-rules"></a>Szabályok megtekintése

1. Válassza a **szabályok** lehetőséget a bal oldali ablaktáblán.

   ![Szabályok ablaktábla](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Válassza a **magas pH-riasztás** lehetőséget, amely az alkalmazás előre konfigurált szabályainak egyike.

     ![Magas pH-riasztás](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    A `High flow alert` szabály úgy van konfigurálva, hogy a feltételnek való vizsgálatra van beállítva `Acidity (pH)` `greater than` `Max flow threshold` . A maximális forgalom küszöbértéke az **intelligens Valve** -eszköz sablonjában definiált Felhőbeli tulajdonság. A értéke eszközönkénti `Max flow threshold` példányként van beállítva.

Most hozzon létre egy e-mail-műveletet.

Művelet hozzáadása a szabályhoz:

1. Válassza a **+ e-mail** lehetőséget.
1. Adja meg a **magas pH-riasztást** a művelet felhasználóbarát **megjelenítendő neveként** .
1. Adja meg az Azure IoT Central-fiókjához tartozó e-mail-címet **a alkalmazásban**.
1. Szükség esetén megadhat egy megjegyzést, amely az e-mail szövegében szerepel.
1. A művelet befejezéséhez kattintson a **kész** gombra.
1. A Save ( **Mentés** ) gombra kattintva mentse és aktiválja az új szabályt.

Néhány percen belül egy e-mailt kell kapnia a beállított feltétel teljesülése után.

> [!NOTE]
> Az alkalmazás minden alkalommal elküld egy e-mailt, amikor egy feltétel teljesül. A **Letiltás** lehetőség kiválasztásával letilthatja a szabályt, hogy leállítsa az e-maileket az automatikus szabályból.
  
Új szabály létrehozása:

* A bal oldali ablaktábla **szabályok** lapján válassza a **+ új** lehetőséget.

## <a name="configure-jobs"></a>Feladatok konfigurálása

Az Azure IoT Centralban a feladatok lehetővé teszik, hogy az eszköz vagy a Felhőbeli tulajdonságok frissítései több eszközön is aktiválva legyenek. A tulajdonságok mellett a feladatok segítségével több eszközön is aktiválhatja az eszközök parancsait. Az Azure IoT Central automatizálja a munkafolyamatot.

1. A bal oldali panelen válassza a **feladatok** lehetőséget.
1. Válassza az **+ új** lehetőséget, és konfiguráljon egy vagy több feladatot.

## <a name="customize-your-application"></a>Az alkalmazás testreszabása

Építőként több beállítást is módosíthat az alkalmazás felhasználói felületének testreszabásához.

1. Válassza   >  **az adminisztráció testre szabása alkalmazást**.
1. Ha ki szeretne választani egy képet, amelyet az **alkalmazás emblémája** szeretne feltölteni, kattintson a **módosítás** gombra.
1. A böngésző lapjain megjelenő **böngésző ikonjának** kiválasztásához kattintson a **módosítás** gombra.
1. Az alapértelmezett **böngésző színeit** HTML hexadecimális színkódok hozzáadásával is lecserélheti.

   ![Az alkalmazás emblémájának, a böngésző ikonjának és a böngésző színének kiválasztása](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Az alkalmazás lemezképeit az **adminisztrációs**  >  **alkalmazás beállításai** lehetőség kiválasztásával is módosíthatja. Ha ki szeretne választani egy képet, amelyet az alkalmazás képként szeretne feltölteni, válassza a **rendszerkép kiválasztása** gombot.
1. Végül az alkalmazás jobb felső sarkában található **Beállítások** ikonra kattintva módosíthatja a **témát** is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje azt.

1. Az Azure IoT Central-alkalmazás bal oldali paneljén válassza az **Adminisztráció** lehetőséget.
1. Válassza az **Alkalmazásbeállítások** lehetőséget, majd a lap alján kattintson a **Törlés** elemre.

## <a name="next-steps"></a>Következő lépések

* További információ 

> [!div class="nextstepaction"]
> A [víz felhasználásának monitorozása fogalmak](./concepts-waterconsumptionmonitoring-architecture.md).
