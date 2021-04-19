---
title: 'Oktatóanyag: Vízfogyasztás-monitorozási alkalmazás létrehozása Azure IoT Central'
description: 'Oktatóanyag: Megtudhatja, hogyan hozhat létre vízfogyasztás-monitorozási alkalmazást Azure IoT Central alkalmazássablonok használatával.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: b65013bbf21faa8bffdcf799a991952b69f5fead
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714469"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Oktatóanyag: Vízfogyasztás-monitorozási alkalmazás létrehozása Azure IoT Central

Ez az oktatóanyag bemutatja, hogyan hozhat létre Azure IoT Central vízfogyasztás-monitorozási alkalmazást a Azure IoT Central vízfogyasztás-figyelő alkalmazássablon használatával.

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * A Azure IoT Central monitorozási sablonnal létrehozhatja a vízfogyasztás-monitorozási alkalmazást.
> * Az operátor irányítópultjának megismerés és testreszabása.
> * Eszközsablonok megismerése.
> * Ismerkedés a szimulált eszközökkel.
> * Szabályok megismerés és konfigurálása.
> * Feladatok konfigurálása.
> * Az alkalmazás védjegyezésének testreszabása fehér címkézés használatával.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure bejelentkezési oldalán.](https://aka.ms/createazuresubscription)

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Vízfogyasztás-monitorozási alkalmazás létrehozása Azure IoT Central

Ebben a szakaszban a Azure IoT Central monitorozási sablonnal hozhatja létre a vízfogyasztás-monitorozási alkalmazást a Azure IoT Central.

Új vízfogyasztás-monitorozási Azure IoT Central létrehozásához:

1. Ugrás a [Azure IoT Central webhelyére.](https://aka.ms/iotcentral)

    Ha rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal. Ellenkező esetben jelentkezzen be egy Microsoft-fiók.

    ![Lépjen a céges fiókjába](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. A **bal oldali** panelen válassza a Build (Felépítés) lehetőséget, majd a Government **(Kormányzati)** lapot. A **Government oldal** számos kormányzati alkalmazássablont jelenít meg.

   ![Kormányzati alkalmazássablonok létrehozása](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. Válassza a **Vízfogyasztás-figyelő** alkalmazássablont.
Ez a sablon tartalmaz egy minta vízfogyasztási eszközsablont, egy szimulált eszközt, egy operátori irányítópultot és egy előre konfigurált figyelési szabályokat.

1. Válassza **az Alkalmazás létrehozása lehetőséget** az Új alkalmazás létrehozása űrlap **megnyitásához** a következő mezőkkel:
    * **Alkalmazás neve:** Alapértelmezés szerint  az alkalmazás a vízfogyasztás monitorozását, majd egy egyedi azonosító sztringet használ, amelyet a Azure IoT Central generál. Ha szükséges, válasszon egy rövid alkalmazásnevet. Az alkalmazás nevét később is módosíthatja.
    * **URL:** Azure IoT Central automatikusan generál egy URL-címet az alkalmazás neve alapján. Dönthet úgy, hogy az URL-címet saját kedvének megfelelőre frissíti. Az URL-címet később is módosíthatja.
    * Ha rendelkezik Azure-előfizetéssel, adja meg a **címtár,** **az Azure-előfizetés** és a **Hely adatait.** Ha nem rendelkezik előfizetéssel, válassza a **7 napos** ingyenes próbaverzió lehetőséget, és töltse ki a szükséges kapcsolattartási adatokat.

    A könyvtárakkal és előfizetésekkel kapcsolatos további információkért lásd: [Alkalmazás létrehozása – rövid útmutató.](../core/quick-deploy-iot-central.md)

1. A **lap** alján válassza a Létrehozás lehetőséget.

    ![Azure IoT Central Új alkalmazás lap](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Azure IoT Central Számlázási adatok lap](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Ezzel létrehozott egy vízfogyasztás-monitorozási alkalmazást a Azure IoT Central monitorozási sablon használatával.

A vízfogyasztás-monitorozási alkalmazás előre konfigurált et tartalmaz:

* Minta operátori irányítópultok.
* Előre definiált vízáramlási és -berendezéssablonok mintája.
* Szimulált vízáramlás és intelligens berendezések.
* Szabályok és feladatok.
* Minta védjegyezés fehér címkével.

Ez az Ön alkalmazása, és bármikor módosíthatja. Most vizsgáljuk meg az alkalmazást, és készítsünk néhány testreszabást.

## <a name="explore-and-customize-the-operator-dashboard"></a>Az operátor irányítópultjának megismerés és testreszabása

Az alkalmazás létrehozása után megnyílik a **Wide World-minta vízfogyasztási irányítópultja.**

   ![A vízfogyasztás monitorozásának irányítópultja](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Szerkesztőként operátorok számára hozhat létre és szabhat testre nézeteket az irányítópulton. Vizsgáljuk meg az irányítópultot, mielőtt megpróbáljuk testre szabni.

> [!NOTE]
> Az irányítópulton megjelenített összes adat szimulált eszközadatokon alapul, amelyeket a következő szakaszban vizsgálunk meg.
  
Az irányítópult különböző csempékből áll:

* **Wide World Water Utility képcsempe:** Az irányítópult első csempéje a fiktív Wide World Water vízművelet képcsempe. A csempét testreszabhatja saját kép beszúrása vagy eltávolítása által.
* **Átlagos vízáramlási KPI csempe:** A KPI csempe úgy van konfigurálva, hogy példaként jelenítse meg az elmúlt *30 perc átlagát.* Testreszabhatja a KPI csempét, és beállíthatja egy másik típusra és időtartományra.
* **Eszközparancsok csempéi:** Ezek a csempék tartalmazzák a **bezárás vagy** a nyitott zárat, valamint a burkolólapok **pozíciójának beállítását.** A parancsok kiválasztásakor meg kell jelenni a szimulált eszköz parancslapján. A Azure IoT Central a *parancs* egy *eszközképesség-típus.* Ezt a fogalmat az oktatóanyag "Eszközsablon" szakaszában fogjuk megismerni.
* **Vízeloszlási** területtérkép: A térkép Azure Maps, amelyet közvetlenül konfigurálhat a Azure IoT Central. A térképcsempe megjeleníti az eszköz helyét. Vigye a kurzort a térkép fölé, és próbálja ki a térkép vezérlőelemeket, például *nagyít,* *kicsinyít* vagy *kibont.*

    ![A vízfogyasztás monitorozásának irányítópult-térképe](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Átlagos vízáramlási vonaldiagram** és Környezeti feltétel **vonaldiagram:** Egy vagy több eszköztelemet ábrázolhat vonaldiagramként egy kívánt időtartományon.
* **Átlagos légnyomási hőtérkép** diagram: Kiválaszthatja az eszköz telemetriai adatainak hőtérkép-vizualizációtípusát, ha egy színindexszel időtartományban szeretné elosztani.
* **Riasztási küszöbértékek alaphelyzetbe állítása tartalomcsempe:** Műveletindító tartalomcsempék is tartalmazhatnak, és beágyazhat egy műveletoldalra mutató hivatkozást. Ebben az esetben az alaphelyzetbe állítási riasztás küszöbértéke a Feladatok alkalmazáshoz **vezet,** ahol futtathatja az eszköztulajdonságok frissítéseit. Ezt a lehetőséget az oktatóanyag "Feladatok konfigurálása" szakaszában vizsgáljuk meg.
* **Tulajdonságcsempék:** Az irányítópult a **Működési információk,** a **Folyamatriasztás** küszöbértékei és a **Karbantartási adatok csempéket jeleníti** meg.

### <a name="customize-the-dashboard"></a>Az irányítópult testreszabása

Szerkesztőként testreszabhatja az irányítópulton lévő nézeteket az operátorok számára.

1. Válassza **a Szerkesztés lehetőséget** a Wide World **vízfogyasztási irányítópultjának testreszabásához.** Az irányítópultot a Szerkesztés menü kiválasztásával **szabhatja** testre. Miután az irányítópult szerkesztési **módban** van, hozzáadhat új csempéket, vagy konfigurálhatja azt.

     ![Irányítópult szerkesztése](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Új **irányítópult létrehozásához és** nulláról való konfigurálához válassza az + Új lehetőséget. Több irányítópultja is lehet, és az irányítópultok között az irányítópult menüjében mozoghat.

## <a name="explore-the-device-template"></a>Az eszközsablon megismerés

A Azure IoT Central eszközsablon határozza meg az eszköz képességeit, amely lehet telemetria, tulajdonság vagy parancs. Szerkesztőként meghatározhat egy vagy több eszközsablont a Azure IoT Central, amelyek a csatlakoztatni fogja eszközök képességeit képviselik.

A vízfogyasztás-figyelő alkalmazás két referenciaeszköz-sablont tartalmaz, amelyek *egy* folyamatmérőt és egy intelligens *mérőeszközt képviselnek.*

Az eszközsablon megtekintése:

1. Az **alkalmazás bal** oldali panelén válassza az Eszközsablonok lehetőséget a Azure IoT Central. Az **Eszközsablonok listában** két eszközsablont láthat: **Smart Meter (Intelligens szerelvény)** és **Flow meter (Folyamatmérő).**

   ![Eszközsablon](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. Válassza ki **a Folyamatmérő** eszközsablont, és ismerkedjen meg az eszköz képességeivel.

     ![Eszközsablon flow-mérő](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Az eszközsablon testreszabása

Az eszközsablon testreszabása:

1. Az **Eszközsablonok menüben** válassza **a Testreszabás** lehetőséget.
1. Keresse meg `Temperature` a telemetria típusát.
1. Frissítse **a megjelenített nevét a** `Temperature` `Reported temperature` következőre: .
1. Frissítse a mértékegységet, vagy állítsa be a **Min és** a **Max értéket.**
1. A **módosítások mentéshez** válassza a Mentés lehetőséget.

### <a name="add-a-cloud-property"></a>Felhőtulajdonság hozzáadása

1. Az **Eszközsablonok menüben** válassza a **Felhőtulajdonságok** lehetőséget.
1. Adjon hozzá egy új felhőtulajdonságt a **+ Felhőtulajdonság hozzáadása lehetőség kiválasztásával.**
    A Azure IoT Central hozzáadhat egy, az eszközre vonatkozó tulajdonságot. A felhőtulajdonság lehet például egy telepítési területre, eszközinformációra vagy egyéb karbantartási információra vonatkozó riasztási küszöbérték.
1. A **módosítások mentéshez** válassza a Mentés lehetőséget.

### <a name="views"></a>Megjelenítések

A vízfogyasztás-figyelő eszközsablon előre meghatározott nézetekkel rendelkezik. Megismerheti a nézeteket, és frissítéseket is lehet tenni. A nézetek határozzák meg, hogy az operátorok hogyan látják az eszközadatokat, de a felhőtulajdonságokat is meg kell határozni.

  ![Eszközsablonnézetek](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Közzététel

Ha bármilyen módosítást végrehajtott, mindenképpen tegye közzé **az** eszközsablont.

### <a name="create-a-new-device-template"></a>Új eszközsablon létrehozása

Új **eszközsablon létrehozásához** válassza az + Új lehetőséget, és kövesse a létrehozási folyamatot.
Létrehozhat egy egyéni eszközsablont az nulláról, vagy választhat egy eszközsablont az Azure-eszközkatalógusból.

## <a name="explore-simulated-devices"></a>Szimulált eszközök megismerés

A Azure IoT Central létrehozhat szimulált eszközöket az eszközsablon és az alkalmazás teszteléséhez. A vízfogyasztás-figyelő alkalmazás két szimulált eszközzel rendelkezik, amelyek a Flow-mérőre **és** a Smart **Meter eszközsablonra** vannak leképezve.

### <a name="view-the-devices"></a>Az eszközök megtekintése

1. A **bal oldali**  >  **panelen** válassza az Eszközök Minden eszköz lehetőséget.

   ![Minden eszköz panel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. Válassza a **SmartBillentyű 1 lehetőséget.**

    ![SmartBillentyű 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. A **Parancsok lapon** három olyan eszközparancsot láthat (**Zárjuk** a **motort,** a nyitott zárat és a **beállítsuk** a szekrény pozícióját) , amelyek az **intelligens** szerelvény-eszközsablonban meghatározott képességek.

1. Tekintse meg **az Eszköztulajdonságok** lapot és az Eszköz **irányítópultja** lapot.

> [!NOTE]
> Vegye figyelembe, hogy az összes lap az eszközsablon nézetében van konfigurálva.

### <a name="add-new-devices"></a>Új eszközök hozzáadása

Új eszközök hozzáadásához válassza **az + Új gombra az** Eszközök **lapon.**

## <a name="explore-and-configure-rules"></a>Szabályok megismerés és konfigurálása

A Azure IoT Central létrehozhat szabályokat az eszköz telemetriának automatikus figyelése és műveletek aktiválása egy vagy több feltétel teljesülése esetén. A műveletek közé tartozhat az e-mail-értesítések küldése vagy a Microsoft Power Automate aktiválása, vagy egy webhookművelet, amely adatokat küld más szolgáltatásoknak.

A létrehozott vízfogyasztás-figyelő alkalmazás három előre konfigurált szabályt tartalmaz.

### <a name="view-rules"></a>Szabályok megtekintése

1. A **bal oldali panelen** válassza a Szabályok lehetőséget.

   ![Szabályok panel](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Válassza **a Magas vízáramlási riasztás** lehetőséget, amely az alkalmazás egyik előre konfigurált szabálya.

     ![Magas pH-riasztás](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    A `High water flow alert` szabály úgy van konfigurálva, hogy ellenőrizze a `Flow` `greater than` feltételt: `Max flow threshold` . A maximális folyamat küszöbértéke egy felhőtulajdonság, amely az **Intelligens billentyű** eszközsablonban van meghatározva. A értéke `Max flow threshold` eszközpéldányonként van beállítva.

Most hozzunk létre egy e-mail-műveletet.

Művelet hozzáadása a szabályhoz:

1. Válassza **az + E-mail lehetőséget.**
1. A **művelet rövid Megjelenítendő** neveként adja meg a Magas **folyamat** riasztása nevet.
1. Adja meg az e-mail-címét, amely a Azure IoT Central-fiókjához van társítva a **Következőbe:**.
1. Ha szükséges, írjon be egy megjegyzést, amely szerepel az e-mail szövegében.
1. A **művelet befejezéséhez** válassza a Kész lehetőséget.
1. Az **új szabály** mentéshez válassza a Mentés lehetőséget.
1. Engedélyezze a szabályt.

Néhány percen belül e-mailt kell kapnia, miután a konfigurált feltétel teljesült.

> [!NOTE]
> Az alkalmazás e-mailt küld minden alkalommal, amikor teljesül egy feltétel. Válassza **a Letiltás** lehetőséget, ha le szeretné tiltani a szabályt az automatikus szabálytól való e-mail fogadásának letiltásához.
  
Új szabály létrehozása:

* A **bal oldali panel** **Szabályok** lapján válassza az + Új lehetőséget.

## <a name="configure-jobs"></a>Feladatok konfigurálása

A Azure IoT Central feladatok lehetővé teszik, hogy több eszközön is elindítsa az eszköz- vagy felhőtulajdonság-frissítéseket. A tulajdonságok mellett a feladatokkal eszközparancsokat is aktiválhat több eszközön. Azure IoT Central automatizálja a munkafolyamatot.

1. A **bal oldali panelen** válassza a Feladatok lehetőséget.
1. Válassza **az + Új** lehetőséget, és konfigurálja a feladatokat.

## <a name="customize-your-application"></a>Az alkalmazás testreszabása

Szerkesztőként számos beállítást módosíthat az alkalmazás felhasználói felületének testreszabásához.

1. Válassza **az Adminisztráció Az** alkalmazás  >  **testreszabása lehetőséget.**
1. Az alkalmazásemblémaként feltölteni kívánt kép **kiválasztásához** kattintson a **Módosítás gombra.**
1. A **böngészőlapon** megjelenő Böngésző ikonkép kiválasztásához válassza a **Módosítás** gombot.
1. Az alapértelmezett böngészőszíneket  hexadecimális HTML-színkódok hozzáadásával is lecserélheti.

   ![Az alkalmazásembléma, a böngészőikon és a böngészőszínek kijelölései](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Az alkalmazásképeket az Adminisztrációs alkalmazás **beállításai**  >  **lehetőséget választva is módosíthatja.** Az alkalmazásképként feltölteni kívánt kép kiválasztásához kattintson a **Kép kiválasztása gombra.**
1. Végül az alkalmazás jobb  felső sarkában található Beállítások ikonra kattintva is módosíthatja a témát. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje azt.

1. Válassza **az Adminisztráció** lehetőséget a Azure IoT Central panelen.
1. Válassza **az Alkalmazásbeállítások,** majd a **Törlés** lehetőséget a lap alján.

## <a name="next-steps"></a>Következő lépések

* További információ 

> [!div class="nextstepaction"]
> [A vízfogyasztás monitorozásának fogalmai.](./concepts-waterconsumptionmonitoring-architecture.md)
