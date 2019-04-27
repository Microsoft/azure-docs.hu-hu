---
title: Az operátori nézetek testreszabása az Azure IoT Centralban | Microsoft Docs
description: Szerkesztőként testreszabhatja az operátori nézeteket az Azure IoT Central-alkalmazásban.
author: sandeeppujar
ms.author: sandeepu
ms.date: 03/26/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 7fc42b24090b5fc68176fea2c7b993643ea24200
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60747159"
---
# <a name="tutorial-customize-the-azure-iot-central-operators-view"></a>Oktatóanyag: Az Azure IoT Central operátor nézet testreszabása

Ez az oktatóanyag bemutatja, hogy szerkesztőként hogyan szabhatja testre az alkalmazás operátori nézeteit. Amikor szerkesztőként módosítja az alkalmazást, megtekintheti az operátori nézetek előnézetét a Microsoft Azure IoT Central-alkalmazásban.

Ebben az oktatóanyagban úgy szabja testre az alkalmazást, hogy a csatlakoztatott légkondicionáló eszközre vonatkozó információkat megjelenítse egy operátornak. A testreszabások lehetővé teszik, hogy az operátor kezelhesse az alkalmazáshoz csatlakoztatott légkondicionáló eszközöket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eszköz irányítópultjának konfigurálása
> * Az eszközbeállítások elrendezésének konfigurálása
> * Az eszköztulajdonságok elrendezésének konfigurálása
> * Az eszköz előnézetének megtekintése operátorként
> * Az alapértelmezett alkalmazás irányítópult konfigurálása
> * Az alapértelmezett irányítópult kezelőként előzetes verzió

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt el kell végeznie a két előző oktatóanyagot:

* [Új eszköztípus definiálása az Azure IoT Central-alkalmazásban](tutorial-define-device-type.md).
* [Az eszközre vonatkozó szabályok és műveletek konfigurálása](tutorial-configure-rules.md).

## <a name="configure-your-device-dashboard"></a>Az eszköz irányítópultjának konfigurálása

Szerkesztőként meghatározhatja, milyen információk jelenjenek meg egy eszköz irányítópultján. Az a [az alkalmazásban egy új eszköztípus definiálása](tutorial-define-device-type.md) oktatóanyagban hozzáadott egy vonaldiagramot és egyéb adatokat a **Légkondicionálóját csatlakoztatott** irányítópultot.

1. Szerkesztése a **Légkondicionálóját csatlakoztatott** eszköz sablont, válassza a **eszközsablonok** a bal oldali navigációs menüben:

    ![Eszköz sablonok lap](media/tutorial-customize-operator/devicetemplates.png)

2. Testre szabhatja az eszköz irányítópulton, válassza ki a **Légkondicionálóját csatlakoztatva (1.0.0-s)** létrehozott sablon eszközt a [az alkalmazásban egy új eszköztípus definiálása](tutorial-define-device-type.md) oktatóanyag.

3. Az irányítópult szerkesztéséhez válassza ki a **irányítópult** fülre.

4. Ha egy fő teljesítménymutatói (KPI-) csempét hozzá szeretne adni az irányítópulthoz, válassza a **KPI** lehetőséget:

    A KPI meghatározásához használja a következő táblázatban szereplő információkat:

    | Beállítás     | Érték |
    | ----------- | ----- |
    | Name (Név)        | Maximális hőmérséklet |
    | Időtartomány  | Előző 1 hét |
    | Mérés típusa | Telemetria |
    | Mérés | hőmérséklet |
    | Összesítés | Maximum |
    | Láthatóság  | Enabled |

    ![KPI hozzáadása](media/tutorial-customize-operator/addkpi.png)

5. Kattintson a **Mentés** gombra. Most láthatja a KPI-csempét az irányítópulton:

    ![KPI-csempe](media/tutorial-customize-operator/temperaturekpi.png)

6. Az irányítópulton lévő csempék áthelyezéséhez vagy átméretezéséhez helyezze az egérmutatót a csempe fölé. Húzza át a csempét egy új helyre, és méretezze át.

## <a name="configure-your-settings-layout"></a>A beállítások elrendezésének konfigurálása

Szerkesztőként az eszközbeállítások operátori nézetét is konfigurálhatja. Az operátornak eszköz beállítása az eszköz beállításai lapon használ. Például az operátornak használja a beállítások lapon állítsa be a cél hőmérséklet számára a csatlakoztatott légkondicionálók.

1. A beállítások elrendezést a csatlakoztatott légkondicionálóját szerkesztéséhez válassza a **beállítások** fülre.

2. Áthelyezheti és átméretezheti a beállítások csempéket:

    ![A beállítások elrendezésének szerkesztése](media/tutorial-customize-operator/settingslayout.png)

## <a name="configure-your-properties-layout"></a>A tulajdonságok elrendezésének konfigurálása

Az irányítópult és a beállítások mellett az eszköztulajdonságok operátori nézetét is konfigurálhatja. Az operátor az eszköz tulajdonságai lap használatával kezeli az eszköz metaadatait. Például az operátornak egy eszköz sorozatszámát megtekintéséhez vagy frissítéséhez kapcsolattartási adatait a gyártó használja a Tulajdonságok lapon.

1. A Tulajdonságok elrendezést a csatlakoztatott légkondicionálóját szerkesztéséhez válassza a **tulajdonságok** fülre.

2. Áthelyezheti és átméretezheti a tulajdonságok mezőit:

    ![A tulajdonságok elrendezésének szerkesztése](media/tutorial-customize-operator/propertieslayout.png)

## <a name="preview-the-device"></a>Az eszköz előzetes verzió

Használja a **eszközsablonok** lapon szabhatja testre az irányítópult, a beállítások és a Tulajdonságok lapon olyan operátorra. Használja a **Device Explorer** megtekintése és használata az eszköz sablon lap.

1. Megtekintheti, és a csatlakoztatott légkondicionálóját sablont használja, az operátornak, lépjen a **Device Explorer** lapon, és válassza ki a szimulált eszközt, hogy a sablon alapján létrehozott IoT-központ:

    ![Megtekintése és használata az eszköz sablon](media/tutorial-customize-operator/usetemplate.png)

2. Frissítse az eszköz helyét, válassza a **tulajdonságok** és szerkesztheti a hely csempe értéket. Válassza ki **mentése**:

    ![Tulajdonságérték szerkesztése](media/tutorial-customize-operator/editproperty.png)

3. Ha egy beállítást szeretne küldeni a csatlakoztatott légkondicionálóhoz, válassza a **Beállítások** lehetőséget, módosítsa egy csempe beállításértékét, és válassza a **Frissítés** lehetőséget:

    ![Beállítás küldése az eszközre](media/tutorial-customize-operator/sendsetting.png)

    Amikor az eszköz elfogadja az új beállításértéket, a beállítás **szinkronizálva** értékkel jelenik meg a csempén.

4. Operátorként a szerkesztő által konfigurált módon tekintheti meg az eszköz irányítópultját:

    ![Az eszköz irányítópultjának operátori nézete](media/tutorial-customize-operator/operatordashboard.png)

## <a name="configure-the-default-dashboard"></a>Az alapértelmezett irányítópult konfigurálása

Ha a jelentéskészítő vagy operátor jelentkezik be egy Azure IoT Central alkalmazáshoz, az irányítópult megjelenik. Mint szerkesztő konfigurálhatja a tartalom az alapértelmezett irányítópult, ha hozzá szeretné adni az operátornak a hasznos és a kapcsolódó tartalmat.

> [!NOTE]
> A felhasználók a saját személyes irányítópultok kialakítása és válasszon egyet az alapértelmezésként is.

1. Testre szabhatja az alapértelmezett irányítópult, keresse meg a **irányítópult** lapon, és válassza **szerkesztése** felső jobb. Megjelenik egy panel, egy erőforrástár-objektumok is hozzáadhat az irányítópulthoz.

    ![Irányítópult-oldalon](media/tutorial-customize-operator/builderhome.png)

2. Testre szabhatja az irányítópultot, a csempék hozzáadása a **könyvtár**. Válassza a **Hivatkozás** lehetőséget, és adja hozzá a cég webhelyének részleteit. Ezután válassza a **Mentés** lehetőséget:

    ![Hivatkozás hozzáadása az irányítópulthoz](media/tutorial-customize-operator/addlink.png)

    > [!NOTE]
    > Az Azure IoT Central-alkalmazásban lévő oldalakhoz hivatkozásokat is adhat. Hozzáadhatja például egy eszköz irányítópultjának vagy a beállítások oldalának a hivatkozását.

3. Szükség esetén válasszon **kép** és töltsön fel egy képet az irányítópulton megjeleníthető. Kép URL-címe, amelyhez lépjen, ha kijelöli veheti fel:

    ![Kép hozzáadása az irányítópulthoz](media/tutorial-customize-operator/addimage.png)

    További tudnivalókért lásd a [képek előkészítését és az Azure IoT Central-alkalmazásba való feltöltését](howto-prepare-images.md) ismertető szakaszt.

## <a name="preview-the-dashboard"></a>Az irányítópult megtekintése

Kezelőként az irányítópult megtekintéséhez válassza ki a **kész** felső jobb.

![A Tervezési mód be- és kikapcsolása](media/tutorial-customize-operator/operatorviewhome.png)

Keresse meg az URL-címeket lehet beállítani egy jelentéskészítő-hivatkozásokon és csempéket is választhat.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte, hogyan szabhatja testre az alkalmazás operátori nézetét.

<!-- Repeat task list from intro -->
> [!div class="nextstepaction"]
> * Az eszköz irányítópultjának konfigurálása
> * Az eszközbeállítások elrendezésének konfigurálása
> * Az eszköztulajdonságok elrendezésének konfigurálása
> * Az eszköz előnézetének megtekintése operátorként
> * Az alapértelmezett kezdőlap konfigurálása
> * Az alapértelmezett kezdőlap előnézetének megtekintése operátorként

Most, hogy megismerte, hogyan szabhatja testre az alkalmazás operátori nézetét, az alábbiakban megtalálja a javasolt következő lépéseket:

* [Az eszközök monitorozása (operátorként)](tutorial-monitor-devices.md)
* [Új eszköz hozzáadása az alkalmazáshoz (operátorként és eszközfejlesztőként)](tutorial-add-device.md)