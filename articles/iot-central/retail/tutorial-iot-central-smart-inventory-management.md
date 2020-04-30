---
title: A IoT intelligens leltár-kezelésének oktatóanyaga | Microsoft Docs
description: A IoT Central rendszerhez készült intelligens leltár-kezelési alkalmazás sablonjának oktatóanyaga
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 430f477422e040e0e7e28fd69a1cbc18bce7d656
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81000545"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Oktatóanyag: üzembe helyezés és útmutató intelligens leltár-kezelési alkalmazás sablonja



Ebből az oktatóanyagból megtudhatja, hogyan kezdheti el az első lépéseket egy IoT Central **intelligens leltár-kezelési** alkalmazás sablonjának üzembe helyezésével. Megtudhatja, hogyan helyezheti üzembe a sablont, mit tartalmaz a mező, és hogy mit szeretne tenni a következő lépésekkel.

Ebből az oktatóanyagból megtudhatja, hogyan, 
* intelligens leltár-felügyeleti alkalmazás létrehozása 
* az alkalmazás végigvezeti 

## <a name="prerequisites"></a>Előfeltételek

* Az alkalmazás üzembe helyezéséhez nem szükségesek konkrét előfeltételek
* Ajánlott az Azure-előfizetés használata, de anélkül is próbálkozhat

## <a name="create-smart-inventory-management-application-template"></a>Intelligens leltár-felügyeleti alkalmazás sablonjának létrehozása

Az alábbi lépések végrehajtásával hozhat létre alkalmazást

1. Navigáljon az Azure IoT Central Application Manager webhelyére. Válassza a **Létrehozás** lehetőséget a bal oldali navigációs sávon, majd kattintson a **kereskedelmi** fülre.

    > [!div class="mx-imgBorder"]
    > ![Intelligens leltár-kezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png)

2. Válassza a **kereskedelem** fület, és válassza az **alkalmazás létrehozása** az **intelligens leltár kezelése** alatt elemet.

3. Az alkalmazás **létrehozásakor** megnyílik az új alkalmazás űrlapja, és az alább látható módon töltse ki a kért adatokat.
   **Alkalmazás neve**: az alapértelmezett javasolt nevet használhatja, vagy megadhatja a felhasználóbarát alkalmazás nevét.
   **URL**: használhatja a javasolt alapértelmezett URL-címet, vagy megadhatja a felhasználóbarát egyedi emlékezetes URL-címét. Ezt követően az alapértelmezett beállítás akkor ajánlott, ha már rendelkezik Azure-előfizetéssel. Elkezdheti a 7 napos ingyenes próbaverzió díjszabását, és úgy is dönthet, hogy az ingyenes nyomvonal lejárta előtt bármikor standard díjszabási csomagra vált.
   **Számlázási információ**: a címtár, az Azure-előfizetés és a régió részletei szükségesek az erőforrások kiépítéséhez.
   **Létrehozás**: válassza a létrehozás lehetőséget az oldal alján az alkalmazás üzembe helyezéséhez.

    > [!div class="mx-imgBorder"]
    > ![Intelligens leltár-kezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png)

    > [!div class="mx-imgBorder"]
    > ![Intelligens leltár felügyeletének számlázási adatai](./media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Az alkalmazás végigvezeti 

### <a name="dashboard"></a>Irányítópult 

Az alkalmazás sablonjának sikeres üzembe helyezése után az alapértelmezett irányítópult egy intelligens leltározási felügyeleti operátorok célzott portálja. A Northwind Trader egy fiktív intelligens leltározási szolgáltató, amely a (z) Bluetooth alacsony energiafogyasztású, valamint a kereskedelmi tárolót rádiófrekvenciás azonosítással (RFID) kezeli. Ebben az irányítópultban két különböző átjáró jelenik meg, amelyek a leltárral kapcsolatos telemetria biztosítanak, valamint a hozzájuk tartozó parancsokat, feladatokat és műveleteket is. Ez az irányítópult előre konfigurálva van a kritikus intelligens leltár felügyeleti eszköz műveleti tevékenységének megjelenítéséhez.
Az irányítópult logikailag oszlik meg két különböző átjáró-eszköz felügyeleti művelet között. 
   * A raktár üzembe helyezése rögzített kapcsolattal rendelkező átjáróval történik & a raklapokon található, a & nyomkövetési leltár nagyobb létesítményben való nyomon követéséhez
   * A kiskereskedelmi tároló egy rögzített RFID-átjáróval & RFID-címkékkel van implementálva az egyes elemek szintjén, hogy nyomon kövessék és nyomon kövessék az áruházbeli Outlet készletét
   * Az átjáró helyének, az állapot & kapcsolódó részleteknek a megtekintése 

> [!div class="mx-imgBorder"]
> ![Intelligens leltár-kezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Egyszerűen nyomon követheti az átjárók, az aktív és az ismeretlen címkék teljes számát.
   * Elvégezheti az Eszközkezelő műveleteit, például a belső vezérlőprogram frissítését, az érzékelő letiltását, az érzékelő engedélyezését, az érzékelő küszöbértékének frissítését, az telemetria intervallumok frissítését &
   * Az átjárók igény szerinti leltározását teljes vagy növekményes vizsgálattal is elvégezhetik.

> [!div class="mx-imgBorder"]
> ![Intelligens leltár-kezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Eszköz sablonja
Kattintson az eszközök sablonjai lapra, és látni fogja az átjáró képességének modelljét. A képesség modell két különböző interfész átjáró telemetria van strukturálva **& tulajdonság** -és **átjáró-parancsok**

**Átjáró telemetria & tulajdonsága** – ez a felület az érzékelőkkel, a hellyel, az eszköz adataival és az eszközök Twin tulajdonságával kapcsolatos képességeket jelképezi, például az átjáró küszöbértékeit és a frissítési intervallumokat.

> [!div class="mx-imgBorder"]
> ![Intelligens leltár-kezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Átjáró parancsai** – ez az illesztő az összes átjáró-parancs funkcióját rendszerezi

> [!div class="mx-imgBorder"]
> ![Intelligens leltár-kezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Szabályok
Válassza a szabályok lapot az alkalmazás sablonjában található két különböző szabály megjelenítéséhez. Ezek a szabályok úgy vannak konfigurálva, hogy e-mail-értesítéseket küldjön a kezelőknek további vizsgálatokra.

**Átjáró kapcsolat nélküli üzemmódban**: Ez a szabály akkor aktiválódik, ha az átjáró hosszabb ideig nem jelent jelentést a felhőnek. Az átjáró nem válaszol, mert kevés az akkumulátor mód, a kapcsolat elvesztése, az Eszközállapot.

**Ismeretlen címkék**: kritikus fontosságú, hogy nyomon követhesse az adott eszközhöz társított összes RFID-&. Ha az átjáró túl sok ismeretlen címkét észlel, akkor a szinkronizálási kihívások megjelölése a forrásként szolgáló alkalmazásokkal.

> [!div class="mx-imgBorder"]
> ![Intelligens leltár-kezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Feladatok
Válassza a feladatok fület az alkalmazás sablonjának részeként öt különböző feladat megjelenítéséhez: a feladatok szolgáltatással az egész megoldásra kiterjedő műveleteket hajthat végre. Itt a leltár-felügyeleti feladatok az eszköz parancsait és a Twin képességet használják olyan feladatok elvégzésére, mint például a,
   * az olvasók letiltása az összes átjárón keresztül
   * a telemetria küszöbértékének módosítása 
   * igény szerinti leltározási vizsgálat végrehajtása a teljes megoldáson keresztül.

> [!div class="mx-imgBorder"]
> ![Intelligens leltár-kezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje az alkalmazás sablonját a **felügyeleti** > **alkalmazás beállításainak** meglátogatásával, és kattintson a **Törlés**gombra.

> [!div class="mx-imgBorder"]
> ![Intelligens leltár-kezelési irányítópult](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>További lépések
* További információ az intelligens leltár felügyeletének [intelligens leltár-kezelési koncepcióról](./architecture-smart-inventory-management.md)
* További információ a [IoT Central kiskereskedelmi sablonokról](./overview-iot-central-retail.md)
* További információ a IoT Centralról [IoT Central áttekintés](../core/overview-iot-central.md)
