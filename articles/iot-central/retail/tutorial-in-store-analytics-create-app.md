---
title: Oktatóanyag – áruházbeli elemzési alkalmazás létrehozása az Azure-ban IoT Central
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy áruházbeli elemzési kiskereskedelmi alkalmazást IoT Centralban. Létrehozza, testreszabhatja és érzékelő eszközöket adhat hozzá.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 112039b9227d5ff34e68b6dc6643597f335fbdb5
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832232"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Oktatóanyag: áruházbeli elemzési alkalmazás létrehozása az Azure-ban IoT Central

Az oktatóanyag bemutatja, hogyan hozhat létre Azure IoT Central Store-beli elemzési alkalmazást a megoldás-építők számára. A minta alkalmazás egy kiskereskedelmi áruházban található. Ez egy olyan megoldás, amely a közös üzleti igényeknek megfelelően figyeli és alkalmazkodik a kihasználtsági és környezeti feltételekhez.

A létrehozott minta alkalmazás három valódi eszközt tartalmaz: egy Rigado Cascade 500-átjárót és két RuuviTag érzékelőt. Az oktatóanyag azt is bemutatja, hogyan használhatók tesztelési célokra az alkalmazás sablonjában található szimulált foglaltság érzékelő. A Rigado C500-átjáró az alkalmazás kommunikációs központjaként szolgál. A tárolóban lévő érzékelőkkel kommunikál, és kezeli a felhővel létesített kapcsolataikat. A RuuviTag olyan környezeti érzékelő, amely telemetria biztosít, beleértve a hőmérsékletet, a páratartalomot és a nyomást. A szimulált foglaltság érzékelő lehetővé teszi a mozgás és a jelenlét nyomon követését egy áruház pénztári területein. 

Ez az oktatóanyag a Rigado és a RuuviTag eszközök alkalmazáshoz való csatlakoztatásának utasításait tartalmazza. Ha rendelkezik egy másik átjáróval és érzékelőkkel, akkor továbbra is követheti az alkalmazás felépítésének lépéseit. Az oktatóanyag azt is bemutatja, hogyan hozhat létre szimulált RuuviTag-érzékelőket. A szimulált érzékelők lehetővé teszik az alkalmazás felépítését, ha nem rendelkezik valós eszközökkel. 

A pénztári és a feltétel-figyelési megoldást három részből fejlesztheti:

* Az alkalmazás létrehozása és az eszközök csatlakoztatása a figyelési feltételekhez
* Az irányítópult testreszabása a kezelők számára az eszközök figyeléséhez és kezeléséhez
* Az adatexportálás konfigurálása az áruházbeli kezelők számára az elemzések futtatásához és az elemzések megjelenítéséhez

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hozzon létre egy kiskereskedelmi áruházbeli alkalmazást az Azure IoT Central **in-store Analytics-Checkout** sablon használatával
> * Az Alkalmazásbeállítások testreszabása
> * IoT-sablonok létrehozása és testreszabása
> * Eszközök csatlakoztatása az alkalmazáshoz
> * Szabályok és műveletek hozzáadása a feltételek figyeléséhez

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag-Sorozat elvégzéséhez a következőkre lesz szüksége:
* Az Azure-előfizetés használata ajánlott. Igény szerint ingyenes 7 napos próbaverziót is használhat. Ha nem rendelkezik Azure-előfizetéssel, létrehozhat egyet az [Azure regisztrációs oldalán](https://aka.ms/createazuresubscription).
* Hozzáférés egy átjáró-eszközhöz és két környezeti érzékelőhöz (az oktatóanyagban leírtak szerint szimulált eszközöket is használhat)
* A használt eszközökhöz tartozó eszközök sablonjai (a sablonok az oktatóanyagban használt összes eszközhöz elérhetők)

## <a name="create-an-application"></a>Alkalmazás létrehozása
Ebben a szakaszban új Azure IoT Central alkalmazást hoz létre egy sablonból. Ezt az alkalmazást az oktatóanyag-sorozatban fogja használni teljes megoldás létrehozásához.

Új Azure IoT Central-alkalmazás létrehozása:

1. Navigáljon az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyére.

1. Ha rendelkezik Azure-előfizetéssel, jelentkezzen be az eléréséhez használt hitelesítő adatokkal, máskülönben jelentkezzen be Microsoft-fiók használatával:

    ![Lépjen a céges fiókjába](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Egy új Azure IoT Central-alkalmazás létrehozásának megkezdéséhez válassza az **Új alkalmazás** lehetőséget.

1. Válassza a **kereskedelmi** lehetőséget.  A kiskereskedelmi oldalon számos kereskedelmi alkalmazás-sablon látható.

Új, áruházbeli elemzési pénztári alkalmazás létrehozása:  

1. Válassza ki az **áruházbeli elemzés-pénztár** alkalmazás sablonját. Ez a sablon az oktatóanyagban használt összes eszközhöz tartalmaz RuuviTag-érzékelők kivételével. A sablon egy operátori irányítópultot is biztosít a pénztárak és a környezeti feltételek, valamint az eszköz állapotának figyeléséhez. 

1. Igény szerint válasszon egy felhasználóbarát **nevet**. Ez az alkalmazás a contoso nevű kitalált kiskereskedelmi áruházon alapul. Az oktatóanyag a *contoso-pénztár* **alkalmazás nevét** használja. Az alkalmazás sablonja a vállalati Northwind vállalaton alapul. Ebben az oktatóanyagban a contoso használatával megtudhatja, hogyan szabhatja testre az alkalmazást.

    > [!NOTE]
    > Ha felhasználóbarát **alkalmazás-nevet** használ, továbbra is egyedi értéket kell használnia az alkalmazás **URL-címéhez**.

1. Ha Azure-előfizetéssel rendelkezik, adja meg a *címtárat, az Azure-előfizetést és a régiót*. Ha nem rendelkezik előfizetéssel, engedélyezheti a **7 napos ingyenes próbaidőszakot** , és elvégezheti a szükséges kapcsolattartási adatokat.  

    A könyvtárakkal és előfizetésekkel kapcsolatban további információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](../core/quick-deploy-iot-central.md).

1. Válassza a **Létrehozás** lehetőséget.

    ![Azure IoT Central Alkalmazás létrehozása oldal](./media/tutorial-in-store-analytics-create-app/preview-application-template.png)

    ![Azure IoT Central alkalmazás számlázási adatainak létrehozása](./media/tutorial-in-store-analytics-create-app/preview-application-template-billinginfo.png)

## <a name="customize-application-settings"></a>Alkalmazásbeállítások testreszabása

Építőként több beállítást is módosíthat az alkalmazás felhasználói felületének testreszabásához. Ebben a szakaszban egy előre definiált alkalmazási témát fogunk kijelölni. Megtudhatja, hogyan hozhat létre egyéni témákat, és hogyan frissítheti az alkalmazás rendszerképét. Az egyéni témák lehetővé teszik az alkalmazás böngészője színeinek, a böngésző ikonjának és a fejlécben megjelenő alkalmazás emblémájának megadását.

Előre definiált alkalmazás-téma kiválasztása:

1. Válassza a **Beállítások** elemet a fejlécben.

    ![Az Azure IoT Central alkalmazás beállításai](./media/tutorial-in-store-analytics-create-app/settings-icon.png)

2. Válasszon ki egy új **témát**.

3. Kattintson a **Mentés** gombra.

Az előre definiált témák helyett létrehozhat egy egyéni témát is. Ha minta lemezképek készletét szeretné használni az alkalmazás testreszabásához és az oktatóanyag elvégzéséhez, töltse le a [contoso-minta lemezképeit](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Egyéni téma létrehozása:

1. A bal oldali ablaktábla kibontása, ha még nincs kibontva.

    ![Azure IoT Central bal oldali ablaktábla](./media/tutorial-in-store-analytics-create-app/dashboard-expand.png)

1. Válassza **az adminisztráció > az alkalmazás testreszabása** lehetőséget.

1. A **módosítás** gombbal kiválaszthatja az **alkalmazás emblémájának** feltöltendő képet. Opcionálisan megadhat egy értéket a **logo helyettesítő szöveghez**. 

1. A **módosítás** gomb használatával kiválaszthatja a böngésző lapjain megjelenő **böngészőablak-ikont** .

1. Igény szerint cserélje le a **böngésző alapértelmezett színét** a HTML hexadecimális színkódok hozzáadásával. A **fejléchez** adja hozzá a *#008575*.  A **Hangsúlyhoz** adja hozzá a *#A1F3EA*. 

1. Kattintson a **Mentés** gombra. 

    ![Az Azure IoT Central testreszabott emblémája](./media/tutorial-in-store-analytics-create-app/select-application-logo.png)

    A mentés után az alkalmazás frissíti a böngésző színeit, az emblémát a fejlécben és a böngésző ikonját. 

    ![Az Azure IoT Central frissített Alkalmazásbeállítások](./media/tutorial-in-store-analytics-create-app/saved-application-settings.png)

Az alkalmazás rendszerképének frissítése:

1. Válassza az **adminisztráció > az Alkalmazásbeállítások** lehetőséget.

1. A **rendszerkép kiválasztása** gomb használatával kiválaszthatja az alkalmazási képként feltölteni kívánt képet. Ez a rendszerkép a IoT Central Application Manager **saját alkalmazások** lapjának alkalmazás csempén jelenik meg.

1. Kattintson a **Mentés** gombra.

1. Szükség esetén navigáljon a **saját alkalmazások** nézethez az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén. Az alkalmazás csempe megjeleníti a frissített alkalmazás rendszerképét.

    ![Azure IoT Central alkalmazás rendszerképének testreszabása](./media/tutorial-in-store-analytics-create-app/customize-application-image.png)

## <a name="create-device-templates"></a>Eszközök sablonjainak létrehozása
Építőként létrehozhat olyan sablonokat, amelyek lehetővé teszik az Ön és az alkalmazások kezelői számára az eszközök konfigurálását és kezelését. Létrehozhat egy sablont egy egyéni sablon létrehozásával, egy meglévő sablonfájl importálásával vagy egy sablon importálásával az Azure IoT-eszköz katalógusból. Miután létrehozta és testreszabta az eszköz sablonját, a használatával valós eszközöket kapcsolhat az alkalmazásához. Igény szerint a szimulált eszközök teszteléséhez is használhat egy eszközt.

A **tárolón belüli elemzés – pénztár** alkalmazás sablon több eszközhöz is rendelkezik.  Az alkalmazásban használt három eszköz közül kettőhöz vannak az eszközök sablonjai. A RuuviTag-eszköz sablonja nem szerepel a **Store Analytics-Checkout** alkalmazás sablonjában. Ebben a szakaszban egy RuuviTag-érzékelőkhöz tartozó sablont ad hozzá az alkalmazáshoz.

RuuviTag-eszköz sablonjának hozzáadása az alkalmazáshoz:

1. A bal oldali ablaktáblán válassza ki az **eszközök sablonjait** .

1. Új sablon létrehozásához válassza az **+ új** lehetőséget.

1. Keresse meg és válassza ki a **RuuviTag** -érzékelő eszköz sablonját az Azure IoT-eszköz katalógusában. 

1. Kattintson a **Tovább: Testreszabás** elemre.

    ![Képernyőfelvétel: a Next (Testreszabás) gomb kiemelése.](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png)

1. Válassza a **Létrehozás** lehetőséget. Az alkalmazás hozzáadja a RuuviTag-eszköz sablonját.

1. A bal oldali ablaktáblán válassza ki az **eszközök sablonjait** . A lap megjeleníti az alkalmazás sablonjában szereplő összes eszközt, valamint az imént hozzáadott RuuviTag-sablont.

    ![Azure IoT Central RuuviTag-érzékelő eszköz sablonja](./media/tutorial-in-store-analytics-create-app/device-templates-list.png)

## <a name="customize-device-templates"></a>Az eszközök sablonjainak testreszabása
Az alkalmazásban háromféle módon szabhatja testre az eszközök sablonjait. Először testreszabhatja az eszközök natív beépített felületeit az eszköz képességeinek módosításával. Például egy hőmérséklet-érzékelővel megváltoztathatja az adatokat, például a hőmérséklet-interfész megjelenítendő nevét, az adattípust, a mértékegységet, a minimális és a maximális működési tartományt. 

Másodszor, a felhő tulajdonságainak hozzáadásával testre szabhatja az eszközök sablonjait. A felhő tulajdonságai nem részei a beépített eszköz képességeinek. A felhő tulajdonságai olyan egyéni adatértékek, amelyeket az Azure IoT Central alkalmazás hoz létre, tárol és társít az eszközeihez. Egy Felhőbeli tulajdonságra példa lehet számított érték, vagy olyan metaadatok, mint például az eszközök készletéhez társítandó hely. 

Harmadszor, egyéni nézetek létrehozásával testreszabhatja az eszközök sablonjait. A nézetek lehetővé teszik, hogy az operátorok vizuálisan jelenítsék meg az eszközök telemetria és metaadatait, például az eszközök mérőszámait és állapotát.

Itt az első két módszer használatával testreszabhatja a RuuviTag-érzékelőkhöz tartozó eszköz sablonját. Az érzékelők nézeteinek létrehozásával kapcsolatos információkért tekintse meg a [szimulált eszköz hozzáadása a IoT Central alkalmazáshoz](../core/quick-create-simulated-device.md) című rövid útmutatót.

A RuuviTag-eszköz beépített felületének testreszabása:

1. A bal oldali ablaktáblán válassza ki az **eszközök sablonjait** . 

1. Válassza ki a RuuviTag-érzékelőkhöz tartozó sablont. 

1. A bal oldali ablaktábla elrejtése A sablon összegző nézete az eszköz képességeit jeleníti meg.

    ![Azure IoT Central RuuviTag-sablon összegző nézete](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png)

1. Válassza a **Testreszabás** lehetőséget a RuuviTag-eszköz sablon menüjében. 

1. Görgessen a lehetőségek listájához, és keresse meg a `humidity` telemetria típusát. Ez az a sor, amelynek a *nedvességtartalma* a szerkeszthető **megjelenítendő név** értékével rendelkezik.

A következő lépésekben testreszabhatja a `humidity` RuuviTag érzékelők telemetria-típusát. Igény szerint testreszabhat néhány más telemetria-típust.

A `humidity` telemetria típusnál végezze el a következő módosításokat:

1. A **Kibontás** vezérlő kiválasztásával bontsa ki a sor sémájának részleteit.

1. Frissítse a **megjelenítendő név** értékét a *nedvességtől* egy egyéni értékre, például a *relatív páratartalom* értékre.

1. Módosítsa a **szemantikai típus** beállítást a *nincs* értékről a *nedvességre*.  Megadhatja a séma értékeit a kibontott séma nézetben a páratartalom telemetria típushoz. A séma beállításai lehetővé teszik, hogy részletes ellenőrzési követelményeket hozzon létre az érzékelők által nyomon követett adathoz. Megadhatja például a minimális és a maximális operációsrendszer-értékeket egy adott csatolóhoz.

1. A módosítások mentéséhez válassza a **Mentés** gombot.

    ![Képernyőkép, amely a testreszabási képernyőt jeleníti meg, és kiemeli a Mentés gombot.](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png)

Felhőbeli tulajdonság hozzáadása az alkalmazásban lévő eszköz sablonhoz:

1. A RuuviTag eszköz sablon menüjében válassza a **felhő tulajdonságai** elemet.

1. Válassza a **Felhőbeli tulajdonság hozzáadása** lehetőséget. 

A következő értékek megadásával hozzon létre egyéni tulajdonságot az egyes eszközök helyének tárolásához:

1. Adja meg a **megjelenítendő név** értékének *helyét* . A rendszer automatikusan átmásolja ezt az értéket a **név** mezőbe, amely a tulajdonság rövid neve. Használhatja a másolt értéket, vagy módosíthatja.

1. Válassza a *sztring* elemet a **séma** legördülő menüben. A karakterlánc típusa lehetővé teszi, hogy a sablon alapján bármilyen eszközzel társítsa a hely neve karakterláncot. Előfordulhat például, hogy egy tárolóban lévő területeket társít az egyes eszközökhöz. Igény szerint beállíthatja a tulajdonság **szemantikai típusát** a *tartózkodási helyükre*, és ez automatikusan beállítja a **séma** *Geopoint*. Lehetővé teszi, hogy a GPS-koordinátákat társítsa egy eszközhöz. 

1. Állítsa be a **minimális hosszt** *2* értékre. 

1. Állítsa be a **szóközöket** **a** be értékre.

1. Válassza a **Mentés** lehetőséget az egyéni Felhőbeli tulajdonság mentéséhez.

    ![Azure IoT Central RuuviTag-sablon testreszabása](./media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png)

1. Válassza a **Közzététel** lehetőséget. 

    Az eszközök közzétételével láthatóvá válik az alkalmazások operátorai. Miután közzétett egy sablont, a használatával szimulált eszközöket állíthat elő teszteléshez, vagy valódi eszközöket kapcsolhat az alkalmazáshoz. Ha már rendelkezik az alkalmazáshoz csatlakoztatott eszközökkel, a testreszabott sablon közzététele leküldi a módosításokat az eszközökön.

## <a name="add-devices"></a>Eszközök felvétele
Miután létrehozta és testreszabta az eszközök sablonjait, itt az ideje, hogy hozzáadja az eszközöket. 

Ebben az oktatóanyagban a következő valós és szimulált eszközöket használja az alkalmazás létrehozásához:
- Egy valós Rigado C500-átjáró
- Két valós RuuviTag érzékelő
- Szimulált **kihasználtsági** érzékelő. A szimulált érzékelőt az alkalmazás sablonja tartalmazza, így nem kell létrehoznia. 

> [!NOTE]
> Ha nem rendelkezik valós eszközökkel, a szimulált RuuviTag-érzékelők létrehozásával továbbra is elvégezheti ezt az oktatóanyagot. A következő utasítások a szimulált RuuviTag létrehozásának lépéseit foglalják magukban. Nem kell szimulált átjárót létrehoznia.

A Real Rigado Gateway és a RuuviTag érzékelők összekapcsolásához hajtsa végre a következő két cikk lépéseit. Ha elkészült, térjen vissza ehhez az oktatóanyaghoz. Mivel ebben az oktatóanyagban már létrehozta az eszközöket, a következő irányokba nem kell újból létrehoznia őket.

- A Rigado-átjárók összekapcsolásával kapcsolatban lásd: [Rigado Cascade 500 összekötése az Azure IoT Central-alkalmazással](../core/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json).
- A RuuviTag-érzékelők összekapcsolásával kapcsolatban lásd: [RuuviTag-érzékelő összekötése az Azure IoT Central-alkalmazással](../core/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). Ezeket az utasításokat két szimulált érzékelő létrehozásához is használhatja, ha szükséges.

## <a name="add-rules-and-actions"></a>Szabályok és műveletek hozzáadása
Az Azure IoT Central-alkalmazásban a feltételek figyelésére szolgáló érzékelők használatának részeként szabályokat hozhat létre, amelyekkel bizonyos feltételek teljesülése esetén futtathat műveleteket. Egy szabály társítva van egy eszköz sablonhoz és egy vagy több eszközhöz, és olyan feltételeket tartalmaz, amelyeknek az eszköz telemetria vagy eseményei alapján kell teljesülniük. Egy szabályhoz egy vagy több társított művelet is tartozik. A műveletek tartalmazhatják e-mail-értesítések küldését vagy egy webhook-művelet aktiválását, amely adatokat küld más szolgáltatásoknak. Az **in-store Analytics-Checkout** alkalmazás sablonja tartalmaz néhány előre meghatározott szabályt az alkalmazásban lévő eszközökhöz.

Ebben a szakaszban egy új szabályt hoz létre, amely a RuuviTag-érzékelő telemetria alapján ellenőrzi a relatív páratartalom maximális szintjét. Adjon hozzá egy műveletet a szabályhoz, hogy ha a páratartalom meghaladja a maximális értéket, az alkalmazás e-mailt küld. 

Szabály létrehozása: 

1. Bontsa ki a bal oldali panelt.

1. Válassza a **szabályok** lehetőséget.

1. Válassza a **+ Új** lehetőséget.

1. A szabály neveként adja meg a *páratartalom szintjét* . 

1. Válassza ki a RuuviTag-eszköz sablonját a **hatókörökben**. Az Ön által definiált szabály a sablon alapján az összes érzékelőre érvényes lesz. Szükség esetén létrehozhat egy szűrőt, amely csak az érzékelők egy meghatározott részhalmazára alkalmazza a szabályt. 

1. Válassza ki `Relative humidity` a **telemetria**. Ez az eszköz azon funkciója, amelyet az előző lépésben testreszabtak.

1. Válassza ki `Is greater than` az **operátort**. 

1. Adjon meg egy tipikus felső tartományba eső, a környezetnek megfelelő belső páratartalom- **szintet.** Adja meg például a *65* értéket. Megadta a szabály feltételét, amely akkor fordul elő, ha a RuuviTag valós vagy szimulált érzékelő relatív páratartalma meghaladja ezt az értéket. Előfordulhat, hogy a környezete normál nedvességtartalmának megfelelően módosítania kell az értéket.  

   ![Azure-IoT Central szabály feltételének hozzáadása](./media/tutorial-in-store-analytics-create-app/rules-add-conditions.png)

Művelet hozzáadása a szabályhoz:

1. Válassza a **+ e-mail** lehetőséget. 

1. Adja meg a *magas páratartalom-értesítést* a művelet felhasználóbarát **megjelenítendő neveként** . 

1. Adja meg a fiókjához tartozó e-mail- **címet a alkalmazásban.** Ha más e-mailt használ, a használt címnek olyan felhasználónak kell lennie, aki hozzá lett adva az alkalmazáshoz. A felhasználónak emellett legalább egyszer be kell jelentkeznie.

1. Szükség esetén megadhat egy, az e-mail-szövegbe felvenni kívánt megjegyzést is.

1. A művelet befejezéséhez kattintson a **kész** gombra.

   ![Azure IoT Central műveletek hozzáadása a szabályokhoz](./media/tutorial-in-store-analytics-create-app/rules-add-action.png)

1. A Save ( **Mentés** ) gombra kattintva mentse és aktiválja az új szabályt. 

    Néhány percen belül a megadott e-mail-fióknak meg kell kezdenie az e-mailek fogadását. Az alkalmazás minden alkalommal küld e-mailt, amikor egy érzékelő azt jelzi, hogy a nedvességtartalom túllépte a feltételben megadott értéket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Hozzon létre egy kiskereskedelmi áruházbeli alkalmazást az Azure IoT Central **in-store Analytics-Checkout** sablon használatával
* Az Alkalmazásbeállítások testreszabása
* IoT-sablonok létrehozása és testreszabása
* Eszközök csatlakoztatása az alkalmazáshoz
* Szabályok és műveletek hozzáadása a feltételek figyeléséhez

Most, hogy létrehozott egy Azure IoT Central állapot-figyelési alkalmazást, a következő lépés a javasolt:

> [!div class="nextstepaction"]
> [Az operátor irányítópultjának testreszabása](./tutorial-in-store-analytics-customize-dashboard.md)
