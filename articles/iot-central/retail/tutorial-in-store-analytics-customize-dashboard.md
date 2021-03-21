---
title: Oktatóanyag – az operátor irányítópultjának testreszabása az Azure-ban IoT Central
description: Ez az oktatóanyag bemutatja, hogyan szabhatja testre az operátor irányítópultját egy IoT Central alkalmazásban, és hogyan kezelheti az eszközöket.
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
ms.openlocfilehash: 74ce36652b1a00ac6813c62a17d44e2a5486645f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831713"
---
# <a name="tutorial--customize-the-operator-dashboard-and-manage-devices-in-azure-iot-central"></a>Oktatóanyag: az operátor irányítópultjának testreszabása és az eszközök kezelése az Azure-ban IoT Central


Ebben az oktatóanyagban megtudhatja, hogyan szabhatja testre az operátor irányítópultját az Azure IoT Central Store Analytics-alkalmazásban. Az alkalmazás-kezelők a testreszabott irányítópult használatával futtathatják az alkalmazást, és kezelhetik a csatlakoztatott eszközöket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Irányítópult nevének módosítása
> * Képcsempék testreszabása az irányítópulton
> * Csempék elrendezése az elrendezés módosításához
> * Telemetria csempe hozzáadása a feltételek megjelenítéséhez
> * Tulajdonságok csempék hozzáadása az eszköz részleteinek megjelenítéséhez
> * Parancs-csempék hozzáadása parancsok futtatásához

## <a name="prerequisites"></a>Előfeltételek

A szerkesztőnek el kell végeznie az oktatóanyagot az Azure IoT Central Store Analytics-alkalmazás létrehozásához és az eszközök hozzáadásához:

* [Áruházbeli elemzési alkalmazás létrehozása az Azure IoT Centralban](./tutorial-in-store-analytics-create-app.md) (kötelező)

## <a name="change-the-dashboard-name"></a>Irányítópult nevének módosítása
Az operátor irányítópultjának testreszabásához szerkesztenie kell az alapértelmezett irányítópultot az alkalmazásban. Emellett további új irányítópultokat is létrehozhat. Az irányítópult az alkalmazásban való testreszabásának első lépése a név módosítása.

1. Navigáljon az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyére.

1. Nyissa meg az [Azure IoT Central in store Analytics-alkalmazás létrehozása az Azure-](./tutorial-in-store-analytics-create-app.md) ban című oktatóanyagban létrehozott feltételt figyelő alkalmazást.

1. Válassza a **Szerkesztés** lehetőséget az irányítópult eszköztárán. Szerkesztési módban testreszabhatja az irányítópult megjelenését, elrendezését és tartalmát.

    ![Azure IoT Central irányítópult szerkesztése](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-edit.png)

1. Szükség esetén elrejtheti a bal oldali ablaktáblát. A bal oldali ablaktábla elrejtése nagyobb munkaterületet biztosít az irányítópult szerkesztéséhez.

1. Adjon meg egy rövid nevet az irányítópultnak az **irányítópult neve mezőben.** Ez az oktatóanyag egy contoso nevű kitalált vállalatot használ, és a példában szereplő irányítópult neve *contoso-irányítópult*. 

1. Kattintson a **Mentés** gombra. A módosítások az irányítópulton lesznek mentve, és a szerkesztési mód le van tiltva.

    ![Azure IoT Central-irányítópult nevének módosítása](./media/tutorial-in-store-analytics-customize-dashboard/dashboard-change-name.png)

## <a name="customize-image-tiles-on-the-dashboard"></a>Képcsempék testreszabása az irányítópulton
Egy Azure IoT Central alkalmazás irányítópultja egy vagy több csempéből áll. A csempe egy téglalap alakú tároló, amely egy irányítópulton jeleníti meg a tartalmat. Különböző típusú tartalmakat társíthat csempéhez, és a csempék áthúzásával testre szabhatja az irányítópultok elrendezését. A tartalmak megjelenítéséhez többféle típusú csempe szükséges. A képcsempe képeket tartalmaz, és hozzáadhat egy URL-címet, amely lehetővé teszi, hogy a felhasználók rákattintanak a képre. A felirat csempe egyszerű szöveget jelenít meg. A Markdown csempék formázott tartalmat tartalmaznak, és lehetővé teszik a HTML-ként megjelenített képek, URL-címek, címek és Markdown-kódok beállítását. Az telemetria, a Property vagy a Command csempék az eszközre vonatkozó adathalmazokat jelenítik meg. 

Ebből a szakaszból megtudhatja, hogyan szabhatja testre a képcsempéket az irányítópulton.

Az irányítópulton található arculatot megjelenítő képcsempe testreszabása:

1. Válassza a **Szerkesztés** lehetőséget az irányítópult eszköztárán. 

1. Válassza a **Konfigurálás** lehetőséget a képcsempén, amely a Northwind arculatot jeleníti meg. 

    ![Azure IoT Central márka rendszerképének szerkesztése](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-edit.png)

1. Módosítsa a **címet**. A cím akkor jelenik meg, ha a felhasználó a képre mutat.

1. Válassza a **rendszerkép** lehetőséget. Megnyílik egy párbeszédpanel, amely lehetővé teszi egyéni rendszerkép feltöltését. 

1. Opcionálisan megadhatja a rendszerkép URL-címét.

1. Válassza a **konfiguráció frissítése** lehetőséget. A **konfiguráció frissítése** gomb menti az irányítópult módosításait, és engedélyezi a szerkesztési módot.

    ![Azure IoT Central arculat mentése](./media/tutorial-in-store-analytics-customize-dashboard/brand-image-save.png)

1. Ha szeretné, válassza a **Konfigurálás** lehetőséget a csempéhez tartozó **dokumentációban**, és adjon meg egy URL-címet a támogatási tartalomhoz. 

A tárolóban található érzékelő zónák térképét megjelenítő képcsempe testreszabása:

1. Válassza a **Konfigurálás** lehetőséget a rendszerkép csempén, amely megjeleníti az alapértelmezett tárolási zóna leképezését. 

1. Válassza ki a **képet**, és a párbeszédablak használatával töltse fel az áruházi zóna térképének egyéni rendszerképét. 

1. Válassza a **konfiguráció frissítése** lehetőséget.

    ![Azure IoT Central áruházbeli Térkép mentése](./media/tutorial-in-store-analytics-customize-dashboard/store-map-save.png)

    A példában szereplő contoso Store-Térkép négy zónát mutat: két pénztári zónát, egy ruházati és testápolási zónát, valamint egy, a élelmiszerekhez és a delihoz tartozó zónát. Ebben az oktatóanyagban a telemetria biztosításához ezeket a zónákat társítja az érzékelőkhöz.

    ![Azure IoT Central tároló zónák](./media/tutorial-in-store-analytics-customize-dashboard/store-zones.png)

1. Kattintson a **Mentés** gombra. 

## <a name="arrange-tiles-to-modify-the-layout"></a>Csempék elrendezése az elrendezés módosításához
Az irányítópultok testreszabásának egyik fő lépése a csempék átrendezése egy hasznos nézet létrehozásához. Az alkalmazások kezelői az irányítópult használatával jelenítik meg az eszközök telemetria, kezelhetik az eszközöket, és megfigyelik a feltételeket egy adott tárolóban. Az Azure IoT Central leegyszerűsíti az irányítópult létrehozásához használható Application Builder-feladatot. Az irányítópult-szerkesztési mód lehetővé teszi csempék gyors hozzáadását, áthelyezését, átméretezését és törlését. Az **áruházbeli elemzés – pénztár** alkalmazás sablonja leegyszerűsíti az irányítópult létrehozásának feladatát is. Egy munkairányítópult-elrendezést biztosít, amely a csatlakoztatott érzékelőkkel, valamint a pénztári sorok számát és a környezeti feltételeket megjelenítő csempéket tartalmaz.

Ebben a szakaszban átrendezi az irányítópultot a **Store Analytics-pénztár** alkalmazás sablonjában egyéni elrendezés létrehozásához.

Az alkalmazásban használni nem tervezett csempék eltávolítása:

1. Válassza a **Szerkesztés** lehetőséget az irányítópult eszköztárán. 

1. Az **X delete** lehetőség kiválasztásával távolítsa el a következő csempéket: **vissza az összes zónához**, **keresse fel az áruház irányítópultját**, a **várakozási időt**, és a **3. pénztárhoz** tartozó mindhárom csempét A contoso áruház irányítópultja nem használja ezeket a csempéket. 

    ![Azure IoT Central csempe törlése](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles.png)

1. Görgessen a hátralévő irányítópult-csempék nézetbe.

1. Az **X delete** lehetőség kiválasztásával távolítsa el a következő csempéket: **Warm-up pénztári zóna**, **legördülő pénztári zóna**, a **foglaltság érzékelő beállításai**, a **termosztát szenzor beállításai** és a **környezeti feltételek**. 

   ![Azure IoT Central a fennmaradó csempék törlése](./media/tutorial-in-store-analytics-customize-dashboard/delete-tiles-2.png)

1. Kattintson a **Mentés** gombra. Ha eltávolítja a nem használt csempéket, felszabadítja a területet a szerkesztési oldalon, és leegyszerűsíti az irányítópult nézetet a kezelők számára.

1. Megtekintheti az irányítópult módosításait.

   ![Az Azure IoT Central csempe törlése után](./media/tutorial-in-store-analytics-customize-dashboard/after-delete-tiles.png)

A nem használt csempék eltávolítását követően átrendezheti a többi csempét egy szervezett elrendezés létrehozásához. Az új elrendezés egy későbbi lépésben hozzáadott csempék területét foglalja magában.

A fennmaradó csempék átrendezése:

1. Válassza a **Szerkesztés** elemet.

1. Válassza ki a **kihasználtsági vezérlőprogram** csempét, és húzza a **kihasználtsági** elem csempéje jobb oldalára.

1. Válassza a **termosztát belső vezérlőprogram** csempét, és húzza a **termosztát** akkumulátor csempétől jobbra.

1. Kattintson a **Mentés** gombra.

1. Megtekintheti az elrendezés változásait. 

    ![Azure IoT Central belső vezérlőprogram akkumulátor-csempéi](./media/tutorial-in-store-analytics-customize-dashboard/firmware-battery-tiles.png)

## <a name="add-telemetry-tiles-to-display-conditions"></a>Telemetria csempe hozzáadása a feltételek megjelenítéséhez
Miután testreszabta az irányítópult elrendezését, készen áll a csempék hozzáadására a telemetria megjelenítéséhez. Telemetria csempe létrehozásához válassza ki az eszköz sablonját és az eszköz példányát, majd válassza ki az eszközre jellemző telemetria a csempén való megjelenítéshez. Az **in-store Analytics-Checkout** alkalmazás sablonja több telemetria-csempét is tartalmaz az irányítópulton. A két pénztári zóna négy csempéje a szimulált telemetria jeleníti meg. A **People Traffic** csempén a két pénztár zónában láthatók a számok. 

Ebben a szakaszban két további telemetria-csempét ad hozzá, amelyek a RuuviTag-érzékelőkből származó környezeti telemetria jelenítik meg, amelyek az Azure IoT Central-oktatóanyagban az [in-store Analytics-alkalmazás létrehozása](./tutorial-in-store-analytics-create-app.md) című cikkben lettek hozzáadva. 

Csempék hozzáadása a környezeti adatok RuuviTag-érzékelőkből való megjelenítéséhez:

1. Válassza a **Szerkesztés** elemet.

1. Válassza ki `RuuviTag` az **eszköz sablonjának** listáját. 

1. Válassza ki a két RuuviTag-érzékelő egyikének **eszköz-példányát** . A példában a contoso-tárolóban válassza a `Zone 1 Ruuvi` telemetria csempe létrehozásához 1. zóna. 

1. Válassza ki `Relative humidity` a és a elemet `temperature` a **telemetria** listán. Ezek a csempén lévő egyes zónákhoz megjelenített telemetria elemek.

1. Válassza a **összevonás** lehetőséget. 

    ![Azure IoT Central RuuviTag csempe hozzáadása 1](./media/tutorial-in-store-analytics-customize-dashboard/add-zone1-ruuvi.png)

    Egy új csempe jelenik meg, amely a kiválasztott érzékelő összesített nedvességtartalmát és hőmérséklet-telemetria jeleníti meg. 

1. Válassza a **Konfigurálás** lehetőséget az új csempén a RuuviTag-érzékelőhöz. 

1. Módosítsa a **címet** *1. zóna környezetre*. 

1. Válassza a **konfiguráció frissítése** lehetőséget.

1. Az előző lépések megismétlésével hozzon létre egy csempét a második érzékelő példányához. Állítsa a **címet** *2. zóna környezetre* , majd válassza a **konfiguráció frissítése elemet.**

1. Húzza a **2. zóna környezet** nevű csempét a **termosztát belső vezérlőprogram** csempére. 

1. Húzza a **1. zóna környezet** című csempét a **People Traffic** csempe alatt. 

1. Kattintson a **Mentés** gombra. Az irányítópult a két új csempén jeleníti meg a telemetria.

    ![Az Azure IoT Central minden RuuviTag csempe](./media/tutorial-in-store-analytics-customize-dashboard/all-ruuvitag-tiles.png)

A **People Traffic** csempe szerkesztése a csak két telemetria megjelenítéséhez:

1. Válassza a **Szerkesztés** elemet. 

1. Válassza a **Konfigurálás** lehetőséget a **People Traffic** csempén.

1. A **telemetria** válassza a **Count 1**, a **2**. és a **3**. számú elemet. 

1. Válassza a **konfiguráció frissítése** lehetőséget. Törli a csempe meglévő konfigurációját. 

1. Válassza ismét a **configure (Konfigurálás** ) lehetőséget a **People Traffic** csempén.

1. A **telemetria** válassza a **Count 1** és a **2. szám** elemet. 

1. Válassza a **konfiguráció frissítése** lehetőséget. 

1. Kattintson a **Mentés** gombra.  A frissített irányítópult csak a két pénztári zónára vonatkozó számlálást jeleníti meg, amelyek a szimulált kihasználtsági érzékelőn alapulnak.

    ![Az Azure IoT Central az emberek két sávot forgalma](./media/tutorial-in-store-analytics-customize-dashboard/people-traffic-two-lanes.png)

## <a name="add-property-tiles-to-display-device-details"></a>Tulajdonságok csempék hozzáadása az eszköz részleteinek megjelenítéséhez
Az alkalmazások kezelői az irányítópultot használják az eszközök felügyeletére és az állapot figyelésére. Az egyes RuuviTag csempe hozzáadásával engedélyezheti a kezelők számára a szoftververzió megtekintését. 

Tulajdonságok csempe hozzáadása az egyes RuuviTag:

1. Válassza a **Szerkesztés** elemet.

1. Válassza ki `RuuviTag` az **eszköz sablonjának** listáját. 

1. Válassza ki a két RuuviTag-érzékelő egyikének **eszköz-példányát** . A példában a contoso-tárolóban válassza a `Zone 1 Ruuvi` telemetria csempe létrehozásához 1. zóna. 

1. Válassza a **tulajdonságok > a szoftver verziója** lehetőséget.

1. Válassza a **összevonás** lehetőséget. 

1. Válassza a **configure (Konfigurálás** ) lehetőséget az újonnan létrehozott csempe **szoftver verziójában**. 

1. Módosítsa a **címet** a *ruuvi 1 verzióra*.

1. Válassza a **konfiguráció frissítése** lehetőséget. 

1. Húzza a csempét a **Ruuv 1 szoftververzió** címére a **1. zóna környezet** csempe alatt.

1. Az előző lépések megismétlésével hozzon létre egy szoftververzió-tulajdonságot a második RuuviTag. 

1. Kattintson a **Mentés** gombra.  

    ![Azure IoT Central RuuviTag-tulajdonságok csempéi](./media/tutorial-in-store-analytics-customize-dashboard/add-ruuvi-property-tiles.png)

## <a name="add-command-tiles-to-run-commands"></a>Parancs-csempék hozzáadása parancsok futtatásához
Az alkalmazások kezelői az irányítópult használatával is kezelhetik az eszközöket a parancsok futtatásával. Az irányítópultra olyan parancssori csempéket adhat hozzá, amelyek előre definiált parancsokat futtatnak az eszközön. Ebben a szakaszban egy olyan parancssori csempét ad hozzá, amely lehetővé teszi, hogy a kezelők újraindítsák a Rigado-átjárót. 

Parancs hozzáadása az átjáró újraindításához:

1. Válassza a **Szerkesztés** elemet. 

1. Válassza ki `C500` az **eszköz sablonjának** listáját. Ez a sablon a Rigado C500-átjáróhoz. 

1. Válassza ki az átjáró példányát az **eszköz példányában**.

1. Válassza a **parancs > újraindítás** lehetőséget, majd húzza azt az irányítópultra az áruházi Térkép mellett. 

1. Kattintson a **Mentés** gombra. 

1. Megtekintheti a befejezett contoso-irányítópultot. 

    ![Az Azure IoT Central teljes irányítópultjának testreszabása](./media/tutorial-in-store-analytics-customize-dashboard/completed-dashboard.png)

1. Ha szeretné, a reboot **(újraindítás) csempét** választva futtassa az újraindítás parancsot az átjárón.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Irányítópult nevének módosítása
* Képcsempék testreszabása az irányítópulton
* Csempék elrendezése az elrendezés módosításához
* Telemetria csempe hozzáadása a feltételek megjelenítéséhez
* Tulajdonságok csempék hozzáadása az eszköz részleteinek megjelenítéséhez
* Parancs-csempék hozzáadása parancsok futtatásához

Most, hogy testre szabta az irányítópultot az Azure IoT Central in-store Analytics-alkalmazásban, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Az adatexportálás és az elemzések megjelenítése](./tutorial-in-store-analytics-export-data-visualize-insights.md)
