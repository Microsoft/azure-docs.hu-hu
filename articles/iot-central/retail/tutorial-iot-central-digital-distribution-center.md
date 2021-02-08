---
title: A IoT Digital Distribution Center oktatóanyaga | Microsoft Docs
description: A IoT Centralhez készült Digital Distribution Center-alkalmazás sablonjának oktatóanyaga
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 2b8ed417de966f8731542e8f5ca12a6a05e2dc8a
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820279"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Oktatóanyag: a Digital Distribution Center alkalmazás-sablon üzembe helyezése és átjárása

Ez az oktatóanyag bemutatja, hogyan kezdheti el egy IoT Central **Digital Distribution Center** -alkalmazás sablonjának üzembe helyezését. Megtudhatja, hogyan helyezheti üzembe a sablont, mit tartalmaz a mező, és hogy mit szeretne tenni a következő lépésekkel.

Ebből az oktatóanyagból megtudhatja, hogyan, 

> [!div class="checklist"]
> Digital Distribution Center-alkalmazás létrehozása az alkalmazáson keresztül 

## <a name="prerequisites"></a>Előfeltételek
* Az alkalmazás üzembe helyezéséhez nem szükségesek konkrét előfeltételek
* Ajánlott az Azure-előfizetés használata, de anélkül is próbálkozhat

## <a name="create-digital-distribution-center-application-template"></a>Digital Distribution Center-alkalmazás sablonjának létrehozása

Az alábbi lépések végrehajtásával hozhat létre alkalmazást

1. Navigáljon az Azure IoT Central Application Manager webhelyére. Válassza a **Létrehozás** lehetőséget a bal oldali navigációs sávon, majd kattintson a **kereskedelmi** fülre.

    :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-homepage.png" alt-text="A Digital Distribution Center alkalmazás sablonja":::
1. Válassza a **kereskedelem** fület, és válassza az alkalmazás **létrehozása** a **digitális terjesztési központ alkalmazás** alatt elemet.

1. Az alkalmazás **létrehozásakor** megnyílik az új alkalmazás űrlapja, és az alább látható módon töltse ki a kért adatokat.
   **Alkalmazás neve**: az alapértelmezett javasolt nevet használhatja, vagy megadhatja a felhasználóbarát alkalmazás nevét.
   **URL**: használhatja a javasolt alapértelmezett URL-címet, vagy megadhatja a felhasználóbarát egyedi emlékezetes URL-címét. Ezt követően az alapértelmezett beállítás akkor ajánlott, ha már rendelkezik Azure-előfizetéssel. Elkezdheti a 7 napos ingyenes próbaverzió díjszabását, és úgy is dönthet, hogy az ingyenes nyomvonal lejárta előtt bármikor standard díjszabási csomagra vált.
   **Számlázási információ**: a címtár, az Azure-előfizetés és a régió részletei szükségesek az erőforrások kiépítéséhez.
   **Létrehozás**: válassza a létrehozás lehetőséget az oldal alján az alkalmazás üzembe helyezéséhez.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create.png" alt-text="Képernyőfelvétel: alkalmazás létrehozása a Digital Distribution Center Application sablonból":::

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create-billinginfo.png" alt-text="Az alkalmazás létrehozásakor a számlázási beállításokat ábrázoló képernyőkép":::

## <a name="walk-through-the-application-dashboard"></a>Az alkalmazás irányítópultjának átjárása 

Az alkalmazás sablonjának sikeres üzembe helyezését követően az alapértelmezett irányítópult egy terjesztési központ operátorának célzott portálja. A Northwind Trader egy fiktív terjesztési központ megoldás-szolgáltatója, amely a továbbító rendszereket kezeli. 

Ebben az irányítópultban egy átjárót és egy IoT-eszközként működő kamerát fog látni. Az átjáró olyan csomagokat biztosít a telemetria, mint például az érvényes, érvénytelen, azonosítatlan és méret, valamint a társított eszközök Twin tulajdonságai. Az összes alárendelt parancs a IoT-eszközökön, például egy kamerán fut. Ez az irányítópult előre konfigurálva van, hogy bemutassa a kritikus terjesztési központ működési tevékenységét.

Az irányítópult logikailag úgy van rendszerezve, hogy megjelenjen az Azure IoT Gateway és a IoT eszköz eszköz-felügyeleti képességei.  
   * Az átjáró parancs végrehajtása & vezérlési feladatokat hajthat végre
   * A megoldás részét képező összes kamera kezelése. 

> [!div class="mx-imgBorder"]
> ![A Digital Distribution Center irányítópultját ábrázoló képernyőfelvétel](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Eszköz sablonja

Kattintson az eszközök sablonjai lapra, és látni fogja az átjáró képességének modelljét. A képességi modell két különböző interfész **kamera** és **digitális terjesztési átjáró** köré szerveződik

> [!div class="mx-imgBorder"]
> ![Az alkalmazásban található Digital Distribution Gateway-eszköz sablonját ábrázoló képernyőfelvétel](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Kamera** – ez a felület az összes kamera-specifikus parancssori funkciót rendszerezi 

> [!div class="mx-imgBorder"]
> ![A Digital Distribution Gateway eszköz sablonjának kamera felületét ábrázoló képernyőkép](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Digital Distribution Gateway** – ez az interfész a fényképezőgépből, a felhőben definiált eszköz Twin tulajdonságaiból és az átjáró adataiból származó összes telemetria jelöli.

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel: a Digital Distribution Gateway Interface a Digital Distribution Gateway Device sablonban](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Átjáró parancsai
Ez a felület az összes átjáró-parancs funkcióját rendszerezi

> [!div class="mx-imgBorder"]
> ![A Digital Distribution Gateway-eszköz sablonjának átjáró parancsai felületét ábrázoló képernyőkép](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Szabályok
Válassza a szabályok lapot az alkalmazás sablonjában található két különböző szabály megjelenítéséhez. Ezek a szabályok úgy vannak konfigurálva, hogy e-mail-értesítéseket küldjön a kezelőknek további vizsgálatokra.

 **Túl sok érvénytelen csomag-riasztás** – ez a szabály akkor aktiválódik, ha a kamera nagy mennyiségű érvénytelen csomagot észlel a továbbító rendszeren keresztül.
 
**Nagyméretű csomag** – ez a szabály akkor aktiválódik, ha a kamera olyan hatalmas csomagot észlel, amely nem vizsgálható meg a minőség tekintetében. 

> [!div class="mx-imgBorder"]
> ![A Digital Distribution Center alkalmazásban található szabályok listáját bemutató képernyőkép](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Feladatok
Válassza a feladatok fület az alkalmazás sablonjának részeként öt különböző feladat megjelenítéséhez: a feladatok funkció kihasználható az egész megoldásra kiterjedő műveletek végrehajtásához. Itt a Digital Distribution Center-feladatok az eszköz parancsaival & Twin képességgel végezhetnek feladatokat, például
   * a kamera kalibrálása a csomagok észlelésének megkezdése előtt 
   * a kamera belső vezérlőprogram rendszeres frissítése
   * az adatfeltöltés kezelése telemetria intervallumának módosítása

> [!div class="mx-imgBorder"]
> ![A Digital Distribution Center alkalmazásban található feladatok listáját bemutató képernyőkép](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha nem folytatja az alkalmazás használatát, törölje az alkalmazás sablonját a **felügyeleti**  >  **alkalmazás beállításainak** meglátogatásával, és kattintson a **Törlés** gombra.

> [!div class="mx-imgBorder"]
> ![Képernyőfelvétel az alkalmazás törléséről, ha elkészült vele](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Következő lépések
* További információ a Digital Distribution Center megoldási architektúráról 
> [!div class="nextstepaction"]
> [a Digital Distribution Center koncepciója](./architecture-digital-distribution-center.md)
* További információ a [IoT Central kiskereskedelmi sablonokról](./overview-iot-central-retail.md)
* További információ a IoT Centralról [IoT Central áttekintés](../core/overview-iot-central.md)
