---
title: Rövid útmutató – Szimulált eszköz hozzáadása Azure IoT Central
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre eszközsablont, és hogyan adhat hozzá szimulált eszközt IoT Central alkalmazásához.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 2eb0e3ce2ac20c89d9c0176ca3e7b33dc839c923
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718826"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application"></a>Rövid útmutató: Szimulált eszköz hozzáadása a IoT Central alkalmazáshoz

Az eszközsablon határozza meg a saját alkalmazásához IoT Central eszközök képességeit. A képességek közé tartozik az eszköz által küldött telemetria, az eszköztulajdonságok és az eszköz által megválaszolt parancsok. Eszközsablon használatával valós és szimulált eszközöket is hozzáadhat egy alkalmazáshoz. A szimulált eszközök hasznosak a valós eszközök csatlakoztatása IoT Central az alkalmazás viselkedésének teszteléséhez.

Ebben a rövid útmutatóban egy eszközsablont fog hozzáadni egy ESP32-Azure IoT Kit fejlesztői alaplaphoz, és létrehoz egy szimulált eszközt. A rövid útmutató befejezéséhez nincs szükség valós eszközre, az eszköz szimulációját kell használnunk. Egy ESP32-eszköz:

* Telemetriát, például hőmérsékletet küld.
* Eszközspecifikus tulajdonságokat, például a maximális hőmérsékletet jelenti az eszköz újraindítása óta.
* Válaszol az olyan parancsokra, mint az újraindítás.
* Általános eszköztulajdonságokat, például belső vezérlőprogram verzióját és sorozatszámát jelenti.

## <a name="prerequisites"></a>Előfeltételek

A [Create an Azure IoT Central application (Alkalmazás](./quick-deploy-iot-central.md) létrehozása Azure IoT Central) rövid útmutató segítségével hozzon létre egy IoT Central-alkalmazást az **Egyéni alkalmazás > alkalmazássablonnal.**

## <a name="create-a-device-template"></a>Eszközsablon létrehozása

Ha új eszközsablont szeretne hozzáadni az alkalmazáshoz, válassza az **Eszközsablonok** lapot a bal oldali panelen.

:::image type="content" source="media/quick-create-simulated-device/device-definitions.png" alt-text="Képernyőkép az eszközsablonok üres listájáról":::

Az eszközsablonok a következőket meghatározó eszközmodellt tartalmaznak:

* Az eszköz által küldött telemetria.
* Eszköztulajdonságok.
* Az eszköz által megválaszolt parancsok.

### <a name="add-a-device-template"></a>Eszközsablon hozzáadása

Az eszközmodellt többféle módon is hozzá lehet IoT Central alkalmazáshoz. Létrehozhat új modellt, importálhat egy modellt egy fájlból, vagy kiválaszthat egy eszközt az eszközkatalógusból. IoT Central az eszközre  vonatkozó megközelítést is támogatja, amelyben az alkalmazás automatikusan importál egy modellt egy adattárból, amikor egy valós eszköz először csatlakozik.

Ebben a rövid útmutatóban kiválaszt egy eszközt az eszközkatalógusból egy eszközsablon létrehozásához.

A következő lépések azt mutatják be, hogyan importálhatja a modellt egy ESP32-eszközhöz az **eszközkatalógus** használatával. Ezek az eszközök telemetriát, például hőmérsékletet küldenek az alkalmazásnak:

1. Új eszközsablon hozzáadásához válassza az **+ Új** lehetőséget az **Eszközsablonok** lapon.

1. A **Típus kiválasztása lapon** görgessen lefelé, amíg meg nem találja az **ESP32-Azure IoT Kit** csempét az Előre konfigurált eszközsablon használata **szakaszban.**

1. Válassza az **ESP32-Azure IoT Kit csempét,** majd a **Tovább: Áttekintés lehetőséget.**

1. Az Áttekintés **lapon** válassza a Létrehozás **lehetőséget.**

1. Néhány másodperc elteltével láthatja az új eszközsablont:

    :::image type="content" source="media/quick-create-simulated-device/devkit-template.png" alt-text="Az ESP32-eszköz eszközsablonját bemutató képernyőkép":::

    A sablon neve **Sensor Controller.** A modell olyan összetevőket tartalmaz, mint a **Sensor Controller,** a **SensorTemp** és az **Eszközinformációs felület.** Az összetevők egy ESP32-eszköz képességeit határozzák meg. A képességek közé tartozik a telemetria, a tulajdonságok és a parancsok.

### <a name="add-cloud-properties"></a>Felhőtulajdonságok hozzáadása

Az eszközsablonok felhőtulajdonságokat is tartalmazhatnak. A felhőtulajdonságok csak a IoT Central alkalmazásban léteznek, és soha nem küldik el és nem kapják meg őket az eszközök. Két felhőtulajdonság hozzáadása:

1. Válassza a **Felhőtulajdonságok,** majd **a + Felhő hozzáadása tulajdonságot.** Az alábbi táblázatban található információk alapján adjon hozzá két felhőtulajdonságot az eszközsablonhoz:

    | Megjelenítendő név      | Szemantikai típus | Séma |
    | ----------------- | ------------- | ------ |
    | Utolsó szervizelés dátuma | Nincsenek          | Date   |
    | Ügyfél neve     | Nincsenek          | Sztring |

1. A **módosítások mentéshez** válassza a Mentés lehetőséget:

    :::image type="content" source="media/quick-create-simulated-device/cloud-properties.png" alt-text="Képernyőkép két felhőtulajdonságról":::

## <a name="views"></a>Megjelenítések

Az alkalmazást testreszabhatja úgy, hogy releváns információkat jelenítsen meg az eszközről. A testreszabások lehetővé teszik, hogy más felügyelni tudja az alkalmazáshoz csatlakoztatott eszközöket. Kétféle nézetet hozhat létre az eszközökkel való interakcióhoz:

* Űrlapok az eszköz- és felhőtulajdonságok megtekintéséhez és szerkesztéséhez.
* Irányítópultok az eszközök megjelenítéséhez, beleértve az eszközök által küldött telemetriát is.

### <a name="default-views"></a>Alapértelmezett nézetek

Az alapértelmezett nézetekkel gyorsan hozzá lehet kezdeni a fontos eszközinformációk megjelenítésének. Az eszközsablonhoz legfeljebb három alapértelmezett nézetet lehet létrehozni:

* A **Parancsok nézetben** parancsokat küld az eszközre.
* Az **Áttekintés** nézet diagramok és metrikák segítségével jeleníti meg az eszköz telemetriai adatait.
* Az **About (About)** nézet megjeleníti az eszköztulajdonságokat.

Válassza ki **a Nézetek csomópontot** az eszközsablonban. Láthatja, hogy IoT Central a sablon hozzáadásakor létrehozott **Áttekintés,** Az adatok és a **Nyers** adatok nézetet.

Új űrlap hozzáadása az eszköz kezeléséhez:

1. Válassza a **Nézetek csomópontot,** majd válassza az **Eszköz-** és felhőadatok szerkesztése csempét egy új nézet hozzáadásához.

1. Módosítsa az űrlap nevét az **Eszköz kezelése névre.**

1. Válassza ki **a Customer Name (Ügyfél neve) és** a Last Service Date (Utolsó szolgáltatás **dátuma)** felhőtulajdonságokat, valamint a **Target Temperature (Célhőmérséklet)** tulajdonságot. Ezután válassza **a Szakasz hozzáadása lehetőséget:**

    :::image type="content" source="media/quick-create-simulated-device/new-form.png" alt-text="Képernyőkép az eszközsablonhoz hozzáadott új űrlapról":::

1. Az **új űrlap mentéshez** válassza a Mentés lehetőséget.

## <a name="publish-device-template"></a>Eszközsablon közzététele

Mielőtt szimulált eszközt hoz létre, vagy valódi eszközt csatlakoztat, közzé kell tennie az eszközsablont. Bár IoT Central első létrehozásakor közzétette a sablont, közzé kell tennie a frissített verziót.

Eszközsablon közzététele:

1. Lépjen az **Érzékelővezérlő** eszközsablonjára az **Eszközsablonok** lapon.

1. Az **oldal** tetején található parancssávon válassza a Közzététel lehetőséget.

1. A megjelenő párbeszédpanelen válassza a Közzététel **lehetőséget.**

Miután közzétett egy eszközsablont, az adatokat az Eszközök **lapon is látni** lehet. A közzétett eszközsablonok új verzió létrehozása nélkül nem szerkeszthetik az eszközmodelleket. A közzétett eszközsablonok felhőtulajdonságai, testreszabásai és nézetei azonban verziószámozás nélkül módosíthatók. Miután végrehajtotta a módosításokat, válassza a **Közzététel**  lehetőséget a módosítások valódi és szimulált eszközök számára való leküldéséhez.

## <a name="add-a-simulated-device"></a>Szimulált eszköz hozzáadása

Ha szimulált eszközt szeretne hozzáadni az alkalmazáshoz, használja a létrehozott **ESP32-eszközsablont.**

1. Új eszköz hozzáadásához válassza az **Eszközök lehetőséget** a bal oldali panelen. Az **Eszközök lapon** a Minden **eszköz** és az ESP32-eszköz **Érzékelővezérlő** eszközsablonja látható. Válassza **a Sensor Controller (Érzékelővezérlő) lehetőséget.**

1. Szimulált DevKit-eszköz hozzáadásához válassza **az + Új lehetőséget.** Használja a javasolt **eszközazonosítót,** vagy adja meg a sajátját. Az eszközazonosítók betűket, számokat és karaktereket `-` tartalmazhatnak. Az új eszköz nevét is megadhatja. Győződjön meg arról, hogy **az Eszköz szimulálása** beállítás igen, majd válassza a Létrehozás **lehetőséget.** 

    :::image type="content" source="media/quick-create-simulated-device/simulated-device.png" alt-text="A szimulált Sensor Controller eszközt bemutató képernyőkép":::

Most már használhatja a korábban létrehozott nézeteket szimulált adatok használatával:

1. Válassza ki a szimulált eszközt az **Eszközök lapon**

    * Az **Áttekintés** nézet a szimulált telemetria ábrázolása:

        :::image type="content" source="media/quick-create-simulated-device/simulated-telemetry.png" alt-text="Képernyőkép a szimulált eszköz áttekintő oldalának megjelenítéséről":::

    * Az **About** (About) nézet a tulajdonságértékeket jeleníti meg.

    * A **Parancsok nézetben** parancsokat futtathat, például **újraindíthatja** az eszközt.

    * Az **Eszközök kezelése** nézet az eszköz kezeléséhez létrehozott űrlap.

    * A **Nyers adatok** nézetben megtekintheti az eszköz által küldött nyers telemetriai adatokat és tulajdonságértékeket. Ez a nézet az eszközök hibakereséséhez hasznos.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre egy **Érzékelővezérlő** eszközsablont egy ESP32-eszközhöz, és hogyan adhat hozzá szimulált eszközt az alkalmazáshoz.

Az alkalmazáshoz csatlakoztatott eszközök figyelésével kapcsolatos további információkért folytassa a rövid útmutatóval:

> [!div class="nextstepaction"]
> [Szabályok és műveletek konfigurálása](./quick-configure-rules.md)
