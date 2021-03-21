---
title: Oktatóanyag – új átjáró típusú eszköz megadása az Azure IoT Centralban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan definiálhat egy új IoT-átjárót az Azure IoT Central-alkalmazásban.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e42e1791693342592e391a14422b1441c9cbbd31
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832385"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Oktatóanyag – új IoT átjáró-eszköz típusának meghatározása az Azure IoT Central-alkalmazásban

*Ez a cikk a megoldás-építők és az eszközök fejlesztőire vonatkozik.*

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a megoldás-előkészítőt az átjáró-eszközök a IoT Central alkalmazásban való definiálásához. Ezután konfigurálhat több alsóbb rétegbeli eszközt, amely az átjáró-eszközön keresztül csatlakozik a IoT Central-alkalmazáshoz. 

Ebben az oktatóanyagban létrehoz egy **intelligens felépítési** átjáró-eszköz sablonját. Az **intelligens felépítési** átjáró eszközei más alsóbb rétegbeli eszközökkel való kapcsolattal rendelkeznek.

![Az átjáró-eszköz és az alsóbb rétegbeli eszközök közötti kapcsolat diagramja](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

A IoT Central alkalmazással folytatott kommunikációt lehetővé tevő alsóbb szintű eszközök esetében az átjáró eszköz is a következőket teheti:

* Küldje el saját telemetria, például a hőmérsékletet.
* Válaszoljon az operátor által készített írható tulajdonságokra. Egy operátor például módosíthatja a telemetria küldési intervallumát.
* Válaszoljon a parancsokra, például az eszköz újraindítására.

> [!div class="checklist"]
> Alsóbb rétegbeli sablonfájlok létrehozása átjáró-sablon létrehozása a szimulált eszközök létrehozásához az eszköz sablonjának közzététele

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez [létre kell hoznia egy Azure IoT Central alkalmazást](./quick-deploy-iot-central.md).

## <a name="create-downstream-device-templates"></a>Alsóbb rétegbeli eszközök sablonjainak létrehozása

Ez az oktatóanyag egy **S1 szenzoros** eszközhöz és egy **RS40-befoglalási érzékelő** eszközhöz készült eszközök sablonjait használja a szimulált alsóbb szintű eszközök létrehozásához.

Eszköz sablonjának létrehozása **S1 szenzoros** eszközhöz:

1. A bal oldali panelen válassza az **eszközök sablonjai** elemet. Ezután válassza a lehetőséget **+** a sablon hozzáadásának megkezdéséhez.

1. Görgessen lefelé, amíg meg nem jelenik az **S1 érzékelő** eszköz csempéje. Válassza ki a csempét, majd válassza a **Tovább: testreszabás** lehetőséget.

1. A **felülvizsgálat** lapon válassza a **Létrehozás** lehetőséget az eszköz sablonjának az alkalmazáshoz való hozzáadásához. 

Eszköz sablonjának létrehozása ***RS40 foglaltság érzékelő** eszközhöz:

1. A bal oldali panelen válassza az **eszközök sablonjai** elemet. Ezután válassza a lehetőséget **+** a sablon hozzáadásának megkezdéséhez.

1. Görgessen lefelé, amíg meg nem jelenik a ***RS40 foglaltság érzékelő** eszköz csempéje. Válassza ki a csempét, majd válassza a **Tovább: testreszabás** lehetőséget.

1. A **felülvizsgálat** lapon válassza a **Létrehozás** lehetőséget az eszköz sablonjának az alkalmazáshoz való hozzáadásához. 

Most már rendelkezik a két alsóbb rétegbeli eszközhöz tartozó eszközök sablonjaival:

![Eszköz sablonjai az alárendelt eszközökhöz](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Átjáró-eszközsablon létrehozása

Ebben az oktatóanyagban egy eszköz sablonját hozza létre az átjáró-eszköz számára. Ezt a sablont később egy szimulált átjáró-eszköz létrehozásához használhatja az alkalmazásban.

Új Gateway-eszköz sablonjának hozzáadása az alkalmazáshoz:

1. A bal oldali panelen válassza az **eszközök sablonjai** elemet. Ezután válassza a lehetőséget **+** a sablon hozzáadásának megkezdéséhez.

1. A **sablon típusának kiválasztása** lapon válassza ki a **IoT-eszköz** csempéjét, majd válassza a **Tovább: testreszabás** lehetőséget.

1. Az **eszköz testreszabása** lapon jelölje be az **átjáró eszköz** jelölőnégyzetet.

1. Az **Áttekintés** lapon válassza a **Létrehozás** lehetőséget. 

1. Adja meg az **intelligens Building Gateway eszközt** a sablon neveként.

1. A **képesség modell létrehozása** lapon válassza az **Egyéni** csempét.

1. Válassza **+** a lehetőséget egy felület hozzáadásához.  Válassza ki az **eszköz adatai** standard felületet.

### <a name="add-relationships"></a>Kapcsolatok hozzáadása

Következő lépésként vegyen fel kapcsolatokat az alsóbb rétegbeli eszközök sablonjaihoz:

1. Az **intelligens építési átjáró eszköz** sablonjában válassza a **kapcsolatok** lehetőséget.

1. Válassza a **+ kapcsolat hozzáadása** elemet. Adja meg a **környezeti érzékelőt** a megjelenítendő név mezőben, majd válassza az **S1 szenzor** lehetőséget célként.

1. Válassza a **+ kapcsolat hozzáadása** elemet. Adja meg a **kihasználtsági érzékelőt** a megjelenítendő név mezőben, és válassza ki a **RS40-kihasználtsági érzékelőt** célként.

1. Kattintson a **Mentés** gombra.

![Smart Building Gateway-eszköz sablon, kapcsolatok megjelenítése](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Felhőtulajdonságok hozzáadása

Az átjáró-eszköz sablonja tartalmazhat Felhőbeli tulajdonságokat. A felhő tulajdonságai csak a IoT Central alkalmazásban érhetők el, és a rendszer soha nem továbbítja, vagy nem fogadja az eszközt.

Felhő tulajdonságainak hozzáadása az **intelligens felépítési átjáró eszköz** sablonhoz.

1. Az **intelligens építési átjáró eszköz** sablonjában válassza a **felhő tulajdonságai** elemet.

1.  A következő táblázatban található információk segítségével két Felhőbeli tulajdonságot adhat hozzá az átjáró-eszköz sablonhoz.

    | Megjelenített név      | Szemantikai típus | Séma |
    | ----------------- | ------------- | ------ |
    | Utolsó szervizelés dátuma | Nincsenek          | Date   |
    | Ügyfél neve     | Nincsenek          | Sztring |

2. Kattintson a **Mentés** gombra.

### <a name="create-views"></a>Nézetek létrehozása

Építőként testreszabhatja az alkalmazást, hogy a megfelelő információkat jelenítse meg a környezeti érzékelő eszközéről egy operátorra. A testreszabások lehetővé teszik, hogy az operátor felügyelje az alkalmazáshoz kapcsolódó környezeti érzékelők eszközeit. Kétféle nézetet hozhat létre az operátorok számára az eszközök használatához:

* Az eszközök és a felhő tulajdonságainak megtekintésére és szerkesztésére szolgáló űrlapok.
* Irányítópultok az eszközök megjelenítéséhez.

Az **intelligens építési átjáró eszköz** sablonjának alapértelmezett nézeteinek előállítása:

1. Az **intelligens építési átjáró eszköz** sablonjában válassza a **nézetek** elemet.

1. Válassza az **alapértelmezett nézetek előállítása** csempét, és győződjön meg arról, hogy az összes beállítás ki van választva.

1. Válassza az **alapértelmezett irányítópult-nézet (ek) előállítása** lehetőséget.

## <a name="publish-the-device-template"></a>Az eszközsablon közzététele

Szimulált átjáró létrehozása előtt vagy valódi átjáró-eszköz csatlakoztatása előtt közzé kell tennie az eszköz sablonját.

Az átjáró-eszköz sablonjának közzététele:

1. Válassza ki az **intelligens felépítési átjáró eszköz** sablonját az **eszközök sablonjai** lapon.

2. Válassza a **Közzététel** lehetőséget.

3. Az **eszköz sablonjának közzététele** párbeszédpanelen válassza a **Közzététel** lehetőséget.

Egy sablon közzététele után az eszköz megjelenik az **eszközök** lapon és a kezelőben. Egy közzétett eszköz sablonjában nem szerkesztheti az eszköz modelljét új verzió létrehozása nélkül. A közzétett eszköz sablonjában azonban frissítheti a felhő tulajdonságait, testreszabásait és nézeteit. Ezek a frissítések nem okozzák új verzió létrehozását. A módosítások elvégzése után válassza a **Közzététel**  lehetőséget a módosítások elküldéséhez az operátornak.

## <a name="create-the-simulated-devices"></a>Szimulált eszközök létrehozása

Ez az oktatóanyag szimulált alsóbb rétegbeli eszközöket és szimulált átjárót használó eszközt használ.

Szimulált átjáró eszköz létrehozása:

1. Az **eszközök** lapon válassza az **intelligens felépítési átjáró eszköz** elemet az eszközök listájában.

1. **+** Új eszköz hozzáadásának megkezdéséhez válassza a lehetőséget.

1. Tartsa meg a generált **eszköz azonosítóját** és az **eszköz nevét**. Győződjön meg arról, hogy a **szimulált** kapcsoló **be van kapcsolva**. Válassza a **Létrehozás** lehetőséget.

Szimulált alsóbb szintű eszközök létrehozása:

1. Az **eszközök** lapon válassza a **RS40-kihasználtsági érzékelő** elemet az eszközök listájában.

1. **+** Új eszköz hozzáadásának megkezdéséhez válassza a lehetőséget.

1. Tartsa meg a generált **eszköz azonosítóját** és az **eszköz nevét**. Győződjön meg arról, hogy a **szimulált** kapcsoló **be van kapcsolva**. Válassza a **Létrehozás** lehetőséget.

1. Az **eszközök** lapon válassza az **S1 Sensor** elemet az eszközök listájában.

1. **+** Új eszköz hozzáadásának megkezdéséhez válassza a lehetőséget.

1. Tartsa meg a generált **eszköz azonosítóját** és az **eszköz nevét**. Győződjön meg arról, hogy a **szimulált** kapcsoló **be van kapcsolva**. Válassza a **Létrehozás** lehetőséget.

![Szimulált eszközök az alkalmazásban](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Alsóbb rétegbeli eszköz kapcsolatainak hozzáadása egy átjáró-eszközhöz

Most, hogy már rendelkezik a szimulált eszközökkel az alkalmazásban, létrehozhatók az alsóbb rétegbeli eszközök és az átjáró eszköz közötti kapcsolatok:

1. Az **eszközök** lapon válassza az **S1 Sensor** elemet az eszközök listájában, majd válassza ki a szimulált **S1 érzékelő** eszközt.

1. Válassza **a kapcsolódás átjáróhoz** lehetőséget.

1. A **Kapcsolódás átjáróhoz** párbeszédpanelen válassza ki az **intelligens felépítési átjáró eszköz** sablonját, majd válassza ki a korábban létrehozott szimulált példányt.

1. Válassza a **Csatlakozás** lehetőséget.

1. Az **eszközök** lapon válassza a **RS40-kihasználtsági érzékelő** elemet az eszközbeállítások listájában, majd válassza ki a szimulált **RS40 foglalási érzékelő** eszközt.

1. Válassza **a kapcsolódás átjáróhoz** lehetőséget.

1. A **Kapcsolódás átjáróhoz** párbeszédpanelen válassza ki az **intelligens felépítési átjáró eszköz** sablonját, majd válassza ki a korábban létrehozott szimulált példányt.

1. Válassza a **Csatlakozás** lehetőséget.

A szimulált alsóbb szintű eszközök most már csatlakozva vannak a szimulált átjáró-eszközhöz. Ha az átjáró-eszközhöz tartozó **alsóbb rétegbeli eszközök** nézetre navigál, láthatja a kapcsolódó alsóbb rétegbeli eszközöket:

![Alsóbb rétegbeli eszközök nézet](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Válassza ki az átjáró-eszköz sablonját és az átjáró eszköz példányát, és válassza a **Csatlakozás** lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Hozzon létre egy új IoT-átjárót eszköz sablonként.
* Felhő tulajdonságainak létrehozása
* Testreszabások létrehozása.
* Adjon meg egy vizualizációt az eszköz telemetria.
* Kapcsolatok hozzáadása.
* Tegye közzé az eszköz sablonját.

A következő lépésként az eszköz fejlesztője a következőket ismerteti:

> [!div class="nextstepaction"]
> [Azure IoT Edge-eszköz hozzáadása az Azure IoT Central-alkalmazáshoz](tutorial-add-edge-as-leaf-device.md)
