---
title: 'Oktatóanyag: csatlakoztatott hulladékgazdálkodási alkalmazás létrehozása az Azure IoT Central'
description: 'Oktatóanyag: megtudhatja, hogyan hozhat létre csatlakoztatott hulladékgazdálkodási alkalmazást az Azure IoT Central alkalmazás-sablonok használatával'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ff7cfb8c7aa8469111d4531da17c7cd184920f9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727566"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>Oktatóanyag: csatlakoztatott hulladékgazdálkodási alkalmazás létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre csatlakoztatott hulladékgazdálkodási alkalmazást az Azure IoT Central használatával. 

Pontosabban a következőket sajátíthatja el: 

> [!div class="checklist"]
> * Az alkalmazás létrehozásához használja az Azure IoT Central *csatlakoztatott hulladékgazdálkodási* sablont.
> * Az operátor irányítópultjának megismerése és testreszabása. 
> * Fedezze fel a csatlakoztatott hulladékgyűjtő tároló eszköz sablonját.
> * Szimulált eszközök megismerése.
> * A szabályok megismerése és konfigurálása.
> * Feladatok konfigurálása.
> * Testre szabhatja az alkalmazás arculatát.

## <a name="prerequisites"></a>Előfeltételek

Az Azure-előfizetés használata ajánlott. Alternatív megoldásként ingyenes, 7 napos próbaverziót is használhat. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).

## <a name="create-your-app-in-azure-iot-central"></a>Az alkalmazás létrehozása az Azure IoT Centralban

Ebben a szakaszban a csatlakoztatott hulladékgazdálkodási sablonnal hozza létre az alkalmazást az Azure IoT Centralban. Ezt a következőképpen teheti meg:

1. Nyissa meg az [Azure IoT Central](https://aka.ms/iotcentral).

    Ha rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal. Ellenkező esetben jelentkezzen be Microsoft-fiók használatával:

    ![Képernyőkép a Microsoft bejelentkezésről.](./media/tutorial-connectedwastemanagement/sign-in.png)

1. A bal oldali panelen válassza a **Létrehozás** lehetőséget. Ezután válassza a **kormány** fület. A kormányzat oldalon számos kormányzati alkalmazás-sablon látható.

    ![Képernyőkép az Azure IoT Central Build oldaláról.](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. Válassza ki a **csatlakoztatott hulladékkezelési** alkalmazás sablonját. Ez a sablon egy példaként csatlakoztatott hulladéktároló-eszköz sablont, egy szimulált eszközt, egy kezelő irányítópultot és előre konfigurált figyelési szabályokat tartalmaz.    

1. Válassza az alkalmazás **létrehozása** lehetőséget, amely megnyitja az **új alkalmazás** párbeszédpanelt. Adja meg a következő mezők adatait:
    * Az **alkalmazás neve**. Alapértelmezés szerint az alkalmazás **csatlakoztatott hulladékgazdálkodást** használ, amelyet az Azure IoT Central által GENERÁLT egyedi azonosító sztring követ. Igény szerint kiválaszthatja az alkalmazás felhasználóbarát nevét is. Az alkalmazás neve később is módosítható.
    * **URL-cím**. Igény szerint kiválaszthatja a kívánt URL-címet. Az URL-címet később is módosíthatja. 
    * **Díjszabási csomag**. Ha rendelkezik Azure-előfizetéssel, adja meg a címtárát, az Azure-előfizetést és a régiót a **Számlázási adatok** párbeszédpanel megfelelő mezőiben. Ha nem rendelkezik előfizetéssel, válassza az **ingyenes** lehetőséget a 7 napos próbaverziós előfizetés engedélyezéséhez, és végezze el a szükséges kapcsolattartási adatokat.  

    További információ a címtárakról és az előfizetésekről: rövid útmutató [– Azure IoT Central-alkalmazás létrehozása](../core/quick-deploy-iot-central.md).

1. A lap alján válassza a **Létrehozás** lehetőséget. 

    ![Képernyőkép az Azure IoT Central új alkalmazás létrehozása párbeszédpanel.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Képernyőkép az Azure IoT Central számlázási adatok párbeszédpanelről.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
Az újonnan létrehozott alkalmazás előre konfigurált:
* Minta operátor irányítópultok.
* Minta előre definiált csatlakoztatott hulladéktároló-eszközök sablonjai.
* Szimulált csatlakoztatott hulladékgyűjtő tároló eszközök.
* Szabályok és feladatok.
* Minta arculata 

Ez az Ön alkalmazása, és bármikor módosítható. Most Ismerkedjen meg az alkalmazással, és végezze el a testreszabást.  

## <a name="explore-and-customize-the-operator-dashboard"></a>Az operátor irányítópultjának megismerése és testreszabása 

Tekintse meg a **széles körű globális hulladékgazdálkodási irányítópultot**, amelyet az alkalmazás létrehozása után láthat.

   ![Képernyőfelvétel a Wide World hulladék-kezelési irányítópultról.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Építőként létrehozhat és testre szabhatja a nézeteket az irányítópulton a kezelők számára. Először vizsgáljuk meg az irányítópultot. 

>[!NOTE]
>Az irányítópulton megjelenő összes információ szimulált eszköz-adatmennyiségen alapul, amelyet a következő szakaszban talál. 

Az irányítópult különböző csempéket tartalmaz:

* **Széles körű globális hulladékkezelési segédprogram képcsempe**: az irányítópult első csempéje egy fiktív hulladékkezelési segédprogram, a "széles világbeli hulladék" képcsempe. Testreszabhatja a csempét, és elhelyezheti saját rendszerképét, vagy eltávolíthatja. 

* **Hulladék bin képcsempe**: a kép és a tartalom csempével hozhatja létre a figyelt eszköz vizuális ábrázolását, valamint egy leírást is. 

* **Kitöltési szint KPI csempe**: Ez a csempe egy *kitöltési szintű* érzékelő által jelentett értéket jelenít meg egy adattárolóban. A kitöltési szint és az egyéb érzékelők, például a *szag-mérő* vagy a *súlyozás* egy adattárolóban távolról is megfigyelhetők. Az operátorok olyan műveleteket végezhetnek, mint például a trash Collection Truck elküldése. 

* A **hulladék megfigyelési területének leképezése**: Ez a csempe Azure Mapst használ, amelyet közvetlenül az Azure IoT Central konfigurálhat. A Térkép csempén az eszköz helye látható. Próbálja ki az egérmutatót a Térkép fölé, és próbálja ki a térképen a vezérlőket, például a nagyítást, a nagyítást vagy a kibővítést.

     ![Képernyőkép a csatlakoztatott hulladékgazdálkodási sablon irányítópult-térképéről.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Kitöltés, szag, súlyozási szint** sávdiagram: megjelenítheti egy vagy több típusú eszköz telemetria-adattípusát. A sávdiagram kibontása is megadható.  

  ![Képernyőfelvétel a csatlakoztatott hulladékgazdálkodási sablon irányítópult-diagramról.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Field Services**: az irányítópult egy hivatkozást tartalmaz, amely bemutatja, hogyan integrálható a Dynamics 365 Field Services az Azure IoT Central alkalmazással. A Field Services használatával például jegyek hozhatók létre a trash-gyűjtési szolgáltatások elküldéséhez. 


### <a name="customize-the-dashboard"></a>Az irányítópult testreszabása 

A **Szerkesztés** menü kiválasztásával testreszabhatja az irányítópultot. Ezután hozzáadhat új csempéket, vagy konfigurálhatja a meglévőket is. Az irányítópult így néz ki szerkesztési módban: 

![Képernyőfelvétel a csatlakoztatott hulladékgazdálkodási sablon irányítópultról szerkesztési módban.](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

Az **+ új** lehetőség kiválasztásával új irányítópultot hozhat létre, és a konfigurációt a semmiből is konfigurálhatja. Több irányítópulttal is rendelkezhet, és az irányítópultok menüjéből válthat az irányítópultok között. 

## <a name="explore-the-device-template"></a>Az eszköz sablonjának megismerése

Az Azure IoT Central egy eszköz, amely telemetria, tulajdonságokat vagy parancsokat is tartalmazhat. Építőként megadhatja azokat az eszközöket, amelyek a csatlakoztatott eszközök képességeit jelölik. 

A csatlakoztatott hulladékkezelési alkalmazáshoz egy minta sablon tartozik egy csatlakoztatott hulladéktároló eszközhöz.

Az eszköz sablonjának megtekintése:

1. Az Azure IoT Central az alkalmazás bal oldali paneljén válassza az **eszközök sablonjai** elemet. 

    ![Az alkalmazásban lévő eszközök sablonjait ábrázoló képernyőkép.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. A **Device templates (eszközök** ) listában válassza a **csatlakoztatott hulladékgyűjtő tároló** elemet.

1. Vizsgálja meg az eszköz sablonjának képességeit. Láthatja, hogy meghatározza az érzékelőket, például a **kitöltési szintet**, a **szag mérőjét**, a **súlyozást** és a **helyet**.

   ![A csatlakoztatott hulladéktároló eszköz sablonjának részleteit bemutató képernyőkép.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>Sablon testreszabása

Próbálja testre szabni a következőket:
1. Az eszköz sablonja menüben válassza a **Testreszabás** lehetőséget.
1. A **Odor Meter** telemetria típusának megkeresése
1. Frissítse a **szag-mérő** **megjelenítendő nevét** a **Odor szintre**.
1. Próbálja meg frissíteni a mértékegységet, vagy állítsa be a **minimális értéket** és a **maximális értéket**.
1. Kattintson a **Mentés** gombra. 

### <a name="add-a-cloud-property"></a>Felhőbeli tulajdonság hozzáadása 

Ezt a következőképpen teheti meg:
1. Az eszköz sablonja menüben válassza a **Cloud Property** elemet.
1. Válassza a **+ felhő hozzáadása tulajdonságot**. Az Azure IoT Centralban hozzáadhat egy olyan tulajdonságot, amely az eszközhöz kapcsolódik, de nem várható, hogy az eszköz elküldje azt. A Felhőbeli tulajdonságok például a telepítési területre, az eszközre és a karbantartási információkra jellemző riasztási küszöbértékek lehetnek. 
1. Kattintson a **Mentés** gombra. 
 
### <a name="views"></a>Megjelenítések 
A csatlakoztatott hulladéktároló eszköz sablonja előre meghatározott nézetekkel rendelkezik. Fedezze fel a nézeteket, és frissítse őket, ha szeretné. A nézetek határozzák meg, hogy az operátorok hogyan látják az eszköz és a bemeneti felhő tulajdonságait. 

  ![Képernyőfelvétel a csatlakoztatott hulladékgazdálkodási sablon eszköz sablonok nézeteiről.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Közzététel 

Ha módosította a módosításokat, ne felejtse el közzétenni az eszköz sablonját. 

### <a name="create-a-new-device-template"></a>Új eszközsablon létrehozása 

Új sablon létrehozásához válassza az **+ új** lehetőséget, majd kövesse a lépéseket. Létrehozhat egy teljesen új egyéni sablont, vagy kiválaszthat egy sablont az Azure-eszköz katalógusában. 

## <a name="explore-simulated-devices"></a>Szimulált eszközök megismerése

Az Azure IoT Central segítségével szimulált eszközöket hozhat létre az eszköz sablonjának és alkalmazásának teszteléséhez. 

A csatlakoztatott hulladékkezelési alkalmazáshoz két szimulált eszköz van társítva a csatlakoztatott hulladéktároló eszköz sablonnal. 

### <a name="view-the-devices"></a>Az eszközök megtekintése

1. Az Azure IoT Central bal oldali paneljén válassza az **eszköz** elemet. 

   ![Képernyőfelvétel a csatlakoztatott hulladékgazdálkodási sablon eszközeiről.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. Válassza a **csatlakoztatott hulladékgyűjtő tároló** eszközt.  

     ![Képernyőfelvétel a csatlakoztatott hulladékgazdálkodási sablon eszközének tulajdonságairól.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. Lépjen a **felhő tulajdonságai** lapra. Frissítse a **bin teljes riasztási küszöbértékét** **95** – **100** értékre. 

Fedezze fel az **eszköz tulajdonságait** és az **eszközök irányítópultjának** lapjait. 

> [!NOTE]
> Az összes lap konfigurálva lett az eszköz sablon nézetében.

### <a name="add-new-devices"></a>Új eszközök hozzáadása

Új eszközöket az **eszközök** lapon az **+ új** elem kiválasztásával adhat hozzá. 

## <a name="explore-and-configure-rules"></a>Szabályok megismerése és konfigurálása

Az Azure IoT Centralban szabályokat hozhat létre az eszközök telemetria automatikus figyeléséhez, illetve a műveletek elindításához, ha egy vagy több feltétel teljesül. A műveletek közé tartozhatnak az e-mail-értesítések küldése, a művelet kiváltása az automatizálásban, vagy egy webhook-művelet indítása, amely adatokat küld más szolgáltatásoknak.

A csatlakoztatott hulladékkezelési alkalmazásnak négy minta szabálya van.

### <a name="view-rules"></a>Szabályok megtekintése
1. Az Azure IoT Central bal oldali paneljén válassza a **szabályok** lehetőséget.

   ![Képernyőkép a csatlakoztatott hulladékgazdálkodási sablon szabályairól.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. Válassza ki a **bin teljes riasztás** elemet.

     ![Képernyőkép a bin teljes riasztásról.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. A **bin teljes riasztás** a következő feltételt ellenőrzi: a **kitöltési szint nagyobb vagy egyenlő, mint a teljes riasztási küszöbérték**.

    A **bin teljes riasztási küszöbértéke** a csatlakoztatott hulladéktároló eszköz sablonjában definiált Felhőbeli tulajdonság. 

Most hozzon létre egy e-mail-műveletet.

### <a name="create-an-email-action"></a>E-mail művelet létrehozása

A szabály **műveletek** listájában beállíthat egy e-mail-műveletet:
1. Válassza a **+ e-mail** lehetőséget. 
1. A **megjelenítendő név** mezőben adja meg a **magas pH-riasztást**.
1. A **to** esetében adja meg az Azure IoT Central-fiókjához társított e-mail-címet. 
1. Szükség esetén megadhat egy megjegyzést, amely az e-mail szövegében szerepel.
1. Válassza a **kész**  >  **Mentés** lehetőséget. 

Ekkor e-mailt fog kapni, amikor a konfigurált feltétel teljesül.

>[!NOTE]
>Az alkalmazás minden alkalommal küld e-mailt, amikor egy feltétel teljesül. Tiltsa le a szabályt, hogy ne kapjon e-mailt az automatizált szabálytól. 
  
Új szabály létrehozásához a **szabályok** bal oldali paneljén válassza az **+ új** lehetőséget.

## <a name="configure-jobs"></a>Feladatok konfigurálása

Az Azure IoT Centralban a feladatok lehetővé teszik az eszköz vagy a felhő tulajdonságainak frissítését több eszközön. A feladatok használatával több eszközön is aktiválhatja az eszközök parancsait. Az Azure IoT Central automatizálja a munkafolyamatot. 

1. Az Azure IoT Central bal oldali paneljén válassza a **feladatok** lehetőséget. 
1. Válassza az **+ új** lehetőséget, és konfiguráljon egy vagy több feladatot. 

## <a name="customize-your-application"></a>Az alkalmazás testreszabása 

Építőként több beállítást is módosíthat az alkalmazás felhasználói felületének testreszabásához.

### <a name="change-the-application-theme"></a>Az alkalmazás témájának módosítása

Ezt a következőképpen teheti meg:
1. Nyissa meg az **Adminisztráció**  >  **Testreszabás az alkalmazást**.
1. Válassza a **módosítás** lehetőséget, ha ki szeretne választani egy képet az **alkalmazás emblémájának** feltöltéséhez.
1. Válassza a **módosítás** lehetőséget a **böngésző ikonjára** feltöltendő rendszerkép kiválasztásához (egy, a böngésző lapjain megjelenő képet).
1. Az alapértelmezett böngésző színeit HTML hexadecimális színkódok hozzáadásával is lecserélheti. Erre a célra használja a **fejléc** és a **jelölőszín** mezőket.

   ![Képernyőkép a csatlakoztatott pazarlás-kezelési sablon alkalmazásának testreszabásáról.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. Módosíthatja az alkalmazás lemezképeit is. Válassza az **adminisztrációs**  >  **alkalmazás beállításai** lehetőséget, és válassza ki az  >   alkalmazási képként feltölteni kívánt képet.
1. Végül a témát úgy is megváltoztathatja, hogy kijelöli az alkalmazás fejlécének **beállításait** .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az alkalmazást az alábbi lépésekkel:

1. Az Azure IoT Central alkalmazás bal oldali paneljén válassza az **Adminisztráció** lehetőséget.
1. Válassza az **Alkalmazásbeállítások**  >  **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Csatlakoztatott hulladékgazdálkodási fogalmak](./concepts-connectedwastemanagement-architecture.md)
