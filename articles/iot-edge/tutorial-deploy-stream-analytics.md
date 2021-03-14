---
title: Oktatóanyag – Stream Analytics az Edge használatával Azure IoT Edge
description: Ebben az oktatóanyagban üzembe helyezi Azure Stream Analytics modulként egy IoT Edge eszközön
author: kgremban
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 323973b7646acee07a0c4dbc59834e0aceca75ee
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462048"
---
# <a name="tutorial-deploy-azure-stream-analytics-as-an-iot-edge-module"></a>Oktatóanyag: Azure Stream Analytics üzembe helyezése IoT Edge modulként

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Számos IoT-megoldás használja az analitikai szolgáltatásokat, hogy betekintést kapjon a felhőben a IoT-eszközökről érkező adatokkal. Az Azure IoT Edge segítségével az [Azure Stream Analytics](../stream-analytics/index.yml)-logikát kihelyezheti magára az eszközre is. A telemetriastreamek peremeszközökön végzett feldolgozásával csökkenthető a feltöltött adatok mennyisége, valamint a gyakorlatban is használható elemzésekre való reagáláshoz szükséges idő.

A Azure IoT Edge és Azure Stream Analytics integrálva van a számítási feladatok fejlesztésének egyszerűbbé tételéhez. Létrehozhat egy Azure Stream Analytics feladatot a Azure Portalban, majd üzembe helyezheti IoT Edge modulként, további kód nélkül.  

A Azure Stream Analytics a felhőben és a IoT Edge eszközökön egyaránt részletesen strukturált lekérdezési szintaxist biztosít az adatelemzéshez. További információ: [Azure stream Analytics dokumentáció](../stream-analytics/stream-analytics-edge.md).

Ebben az oktatóanyagban a Stream Analytics modul az átlaghőmérsékletet számítja egy gördülő 30 másodperces ablakon belül. Ha az átlag eléri a 70-es értéket, a modul egy riasztást küld az eszköznek, amely egy műveletet hajt végre. Esetünkben a művelet a szimulált hőmérséklet-érzékelő visszaállítása. Éles környezetben ezzel a funkcióval leállíthat egy gépet vagy óvintézkedéseket tehet, amint a hőmérséklet veszélyes szintet ér el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Azure Stream Analytics-feladat létrehozása az adatok a peremeszközökön való feldolgozásához.
> * Az új Azure Stream Analytics-feladat csatlakoztatása más IoT Edge-modulokhoz.
> * Az Azure Stream Analytics üzembe helyezése egy IoT Edge-eszközön az Azure Portalról.

<center>

![Diagram – oktatóanyag architektúrája: az ASA-feladatok előkészítése és üzembe helyezése](./media/tutorial-deploy-stream-analytics/asa-architecture.png)
</center>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Az Azure-beli virtuális gépeket IoT Edge eszközként is használhatja a gyors útmutató a [Linux](quickstart-linux.md) vagy [Windows rendszerű eszközökhöz](quickstart.md)című témakör lépéseit követve.

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.

## <a name="create-an-azure-stream-analytics-job"></a>Azure Stream Analytics-feladat létrehozása

Ebben a szakaszban egy Azure Stream Analytics feladatot hoz létre, amely végrehajtja a következő lépéseket:

* Adatok fogadása a IoT Edge eszközről.
* A telemetria-adatok lekérdezése egy meghatározott tartományon kívüli értékekhez.
* A lekérdezés eredményei alapján tegyen lépéseket a IoT Edge eszközön.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

Amikor Azure Stream Analytics-feladatot hoz létre egy IoT Edge-eszköz futtatásához, azt úgy kell tárolni, hogy meghívható legyen az eszközről. Használhat meglévő Azure Storage-fiókot, vagy létrehozhat egy újat.

1. A Azure Portal válassza az **erőforrás létrehozása**  >  **Storage**  >  **Storage-fiók** lehetőséget.

1. Adja meg a következő értékeket a tárfiók létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Előfizetés | Válassza ugyanazt az előfizetést, mint az IoT Hub esetében. |
   | Erőforráscsoport | Azt javasoljuk, hogy az IoT Edge rövid útmutatók és oktatóanyagok esetében ugyanazt az erőforráscsoportot használja az összes tesztelési erőforráshoz. Például: **IoTEdgeResources**. |
   | Name | Adja meg a tárfiók egyedi nevét. |
   | Hely | Válassza ki az Önhöz legközelebb eső helyet. |

1. Tartsa meg a többi mező alapértelmezett értékeit, és válassza a **felülvizsgálat + létrehozás** lehetőséget.

1. Tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget.

### <a name="create-a-new-job"></a>Új feladat létrehozása

1. A Azure Portal válassza az **erőforrás létrehozása**  >  **eszközök internetes hálózata**  >  **stream Analytics feladatot**.

1. Adja meg a következő értékeket a feladat létrehozásához:

   | Mező | Érték |
   | ----- | ----- |
   | Feladat neve | Adja meg a feladat nevét. Például: **IoTEdgeJob** |
   | Előfizetés | Válassza ugyanazt az előfizetést, mint az IoT Hub esetében. |
   | Erőforráscsoport | Javasoljuk, hogy az IoT Edge rövid útmutatók és oktatóanyagok során elkészített erőforráscsoportot használja minden teszterőforráshoz. Például: **IoTEdgeResources**. |
   | Hely | Válassza ki az Önhöz legközelebb eső helyet. |
   | Üzemeltetési környezet | Válassza az **Edge** lehetőséget. |

1. Válassza a **Létrehozás** lehetőséget.

### <a name="configure-your-job"></a>A feladat konfigurálása

Miután létrejött a Stream Analytics-feladat az Azure Portalon, konfigurálhatja azt egy bemenettel, egy kimenettel és egy lekérdezéssel az áthaladó adatokon való futtatáshoz.

Ebben a szakaszban három elem (bemenet, kimenet és lekérdezés) használatával hozhat létre egy feladatot, amely hőmérsékleti adatokat fogad az IoT Edge-eszköztől. Egy gördülő 30 másodperces ablakon belül elemzi az adatokat. Ha az ablakban az átlaghőmérséklet 70 fok fölé kerül, akkor a rendszer riasztást küld az IoT Edge-eszköznek. A következő szakaszban a feladat üzembe helyezésekor fogja pontosan meghatározni, honnan érkeznek az adatok, és hová tartanak.  

1. Keresse meg a Stream Analytics-feladatot az Azure Portalon.

1. A **Feladattopológia** területen válassza a **Bemenetek** lehetőséget, majd a **Streambemenet hozzáadása** elemet.

   ![Azure Stream Analytics – bemenet hozzáadása](./media/tutorial-deploy-stream-analytics/asa-input.png)

1. Válassza az **Edge Hub** elemet a legördülő listából.

1. Az **Új bemenet** panelen adja meg a **temperature** (hőmérséklet) nevet a bemenet aliasaként.

1. Hagyja változatlanul a többi mező értékét, és válassza a **Mentés** lehetőséget.

1. A **Feladattopológia** területen nyissa meg a **Kimenetek** pontot, és válassza a **Hozzáadás** elemet.

   ![Azure Stream Analytics – kimenet hozzáadása](./media/tutorial-deploy-stream-analytics/asa-output.png)

1. Válassza az **Edge Hub** elemet a legördülő listából.

1. Az **Új kimenet** panelen adja meg az **alert** (riasztás) nevet a kimenet aliasaként.

1. Hagyja változatlanul a többi mező értékét, és válassza a **Mentés** lehetőséget.

1. A **feladatok topológiája** területen válassza a **lekérdezés** lehetőséget.

1. Cserélje le az alapértelmezett szöveget a következő lekérdezésre. Az SQL-kód visszaállítás parancsot küld a riasztás kimenetének, ha az átlagos géphőmérséklet bármely 30 másodperces időablakban eléri a 70 fokot. A visszaállítás parancs előre be van programozva az érzékelőbe elvégezhető műveletként.

    ```sql
    SELECT  
        'reset' AS command
    INTO
       alert
    FROM
       temperature TIMESTAMP BY timeCreated
    GROUP BY TumblingWindow(second,30)
    HAVING Avg(machine.temperature) > 70
    ```

1. Válassza a **lekérdezés mentése** lehetőséget.

### <a name="configure-iot-edge-settings"></a>Az IoT Edge-beállítások konfigurálása

Ha elő szeretné készíteni a Stream Analytics-feladatot egy IoT Edge-eszközre való üzembe helyezésre, a feladatot egy tárfiókon lévő tárolóval kell társítania. Amikor a feladat üzembe helyezésébe kezd, a feladat definícióját exportálja a tárolóba.

1. A **Konfigurálás** területen válassza a **Storage-fiók beállításai** lehetőséget, majd kattintson a **Storage-fiók hozzáadása** lehetőségre.

   ![Azure Stream Analytics – Storage-fiók hozzáadása](./media/tutorial-deploy-stream-analytics/add-storage-account.png)

1. Válassza ki az oktatóanyag elején létrehozott **Storage-fiókot** a legördülő menüből.

1. A **Tároló** mezőben válassza az **Új létrehozása** lehetőséget, és adja meg a tárfiók nevét.

1. Kattintson a **Mentés** gombra.

## <a name="deploy-the-job"></a>A feladat üzembe helyezése

Most készen áll az Azure Stream Analytics-feladat IoT Edge-eszközön való üzembe helyezésére.

Ebben a szakaszban az Azure Portal **Modulok beállítása** varázslójával hozhat létre *üzembehelyezési jegyzéket*. Az üzembehelyezési jegyzék egy olyan JSON-fájl, amely leírja az eszközre üzembe helyezendő összes modult, a modul rendszerképeit tároló tárolóregisztrációs adatbázisokat, a modulok kezelésének módját és azt, hogyan kommunikálhatnak egymással a modulok. Az IoT Edge-eszköz lekéri az üzembehelyezési jegyzéket az IoT Hubról, majd az abban lévő információkkal üzembe helyezi és konfigurálja az összes hozzárendelt modult.

Ebben az oktatóanyagban két modult helyezhet üzembe. Az első a **SimulatedTemperatureSensor**, amely egy hőmérséklet-és páratartalom-érzékelőt szimuláló modul. A második a Stream Analytics-feladat. Az érzékelőmodul biztosítja az adatstreamet, amelyet a feladatlekérdezés elemezni fog.

1. Az Azure Portalon keresse meg az IoT-központot.

1. Lépjen **IoT Edge**, majd nyissa meg a IoT Edge eszköz részletek lapját.

1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.  

1. Ha korábban telepítette az SimulatedTemperatureSensor modult ezen az eszközön, az automatikusan feltölthető. Amennyiben nem, vegye fel a modult a következő lépésekkel:

   1. Kattintson a **Hozzáadás** gombra, és válassza az **IoT Edge-modul** lehetőséget.
   1. A név mezőbe írja be a következőt: **SimulatedTemperatureSensor**.
   1. A kép URI-címénél adja meg az **mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0** címet.
   1. Hagyja változatlanul a többi beállítást, és válassza a **Hozzáadás** lehetőséget.

1. Adja hozzá az Azure Stream Analytics Edge-feladatot a következő lépésekkel:

   1. Kattintson a **Hozzáadás** gombra, és válassza az **Azure Stream Analytics-modul** lehetőséget.
   1. Válassza ki az előfizetést, és a létrehozott Azure Stream Analytics Edge-feladatot.
   1. Kattintson a **Mentés** gombra.

   A módosítások mentése után a Stream Analyticsi feladatának részletes adatait a rendszer közzéteszi a létrehozott tárolóban.

1. Ha a Stream Analytics modul hozzá van adva a modulok listájához, válassza ki a nevét, hogy megtekintse a strukturált állapotát, és frissítse a beállításait a **frissítés IoT Edge modul** lapon.

   A **modul beállításai** lapon a **rendszerkép URI-ja** látható, amely egy standard Azure stream Analytics képre mutat. Ezt a lemezképet minden olyan Stream Analytics-modulhoz használni fogja, amely egy IoT Edge eszközön lesz üzembe helyezve.

   A **modul Twin beállítások** lapon látható a **ASAJobInfo** nevű Azure stream Analytics (ASA) tulajdonságot meghatározó JSON. A tulajdonság értéke a feladat tárolóban lévő definíciójára mutat. Ez a tulajdonság azt szemlélteti, hogy a Stream Analytics rendszerkép hogyan van konfigurálva az adott feladatok részleteivel.

   Alapértelmezés szerint a Stream Analytics modul ugyanazokat a nevet adja, mint az alapul szolgáló feladatoknak. Ha szeretné, módosíthatja a modul nevét ezen a lapon, de nem szükséges.

1. Válassza a **frissítés** vagy a **Mégse** lehetőséget.

1. Jegyezze fel Stream Analytics moduljának nevét, mert a következő lépésben szüksége lesz rá. Ezután válassza a **Tovább: útvonalak** a folytatáshoz lehetőséget.

1. Az **útvonalak** lapon megadhatja, hogyan adja át az üzeneteket a modulok és a IoT hub között. Az üzenetek név/érték párokkal vannak kiépítve. Cserélje le az alapértelmezett `route` és a `upstream` nevet és az értékeket az alábbi táblázatban szereplő párokra, a következő név/érték párokat, a _{moduleName}_ példányait pedig a Azure stream Analytics modul nevére cseréli.

    | Name | Érték |
    | --- | --- |
    | `telemetryToCloud` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO $upstream` |
    | `alertsToCloud` | `FROM /messages/modules/{moduleName}/* INTO $upstream` |
    | `alertsToReset` | `FROM /messages/modules/{moduleName}/* INTO BrokeredEndpoint("/modules/SimulatedTemperatureSensor/inputs/control")` |
    | `telemetryToAsa` | `FROM /messages/modules/SimulatedTemperatureSensor/* INTO BrokeredEndpoint("/modules/{moduleName}/inputs/temperature")`|

    Az itt megadott útvonalak határozzák meg az adatok áramlását az IoT Edge-eszközben. A rendszer elküldi a SimulatedTemperatureSensor telemetria adatait a IoT Hub és a Stream Analytics feladatban konfigurált **hőmérséklet** -bemenetre. A rendszer elküldi a **riasztás** kimeneti üzeneteit IoT hub és a SimulatedTemperatureSensor modulnak az Alaphelyzetbe állítás parancs elindításához.

1. Válassza a **Next (tovább): felülvizsgálat + létrehozás** elemet.

1. A **felülvizsgálat + létrehozás** lapon megtekintheti, hogy a varázslóban megadott információk hogyan lesznek átalakítva JSON üzembe helyezési jegyzékbe. Ha végzett a jegyzékfájl áttekintésével, válassza a **Létrehozás** lehetőséget.

1. Visszaadja az eszköz adatai lapot. Válassza a **frissítés** lehetőséget.  

    Meg kell jelennie az új Stream Analytics modulnak, amely a IoT Edge Agent és IoT Edge hub-modulokkal együtt jelenik meg. Eltarthat néhány percig, amíg az információ elérheti IoT Edge eszközét, majd az új modulok elindításához. Ha nem látja azonnal a modulokat, folytassa az oldal frissítésével.

    ![Az eszköz által jelentett SimulatedTemperatureSensor és ASA-modul](./media/tutorial-deploy-stream-analytics/module-output2.png)

## <a name="view-data"></a>Adatok megtekintése

Most nyissa meg IoT Edge eszközét, és tekintse meg az Azure Stream Analytics modul és a SimulatedTemperatureSensor modul közötti interakciót.

1. Ellenőrizze, hogy fut-e mindegyik modul a Dockerben:

   ```cmd/sh
   iotedge list  
   ```

1. Tekintse át az összes rendszernaplót és metrikaadatot. Használja a Stream Analytics-modul nevét:

   ```cmd/sh
   iotedge logs -f {moduleName}  
   ```

1. Az Alaphelyzetbe állítás parancs hatására a SimulatedTemperatureSensor az érzékelő naplófájljainak megtekintésével lehet megtekinteni:

   ```cmd/sh
   iotedge logs SimulatedTemperatureSensor
   ```

   A gép hőmérsékletét fokozatosan megtekintheti, amíg 30 másodpercig eléri a 70 fokos értéket. A Stream Analytics-modul ekkor kiváltja a rendszer alaphelyzetbe állítását, és a géphőmérséklet visszaáll 21 fokra.

   ![Parancs kimenetének visszaállítása modul-naplókba](./media/tutorial-deploy-stream-analytics/docker_log.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben törölheti a cikkben használt helyi konfigurációkat és az Azure-erőforrásokat a díjak elkerüléséhez.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Stream Analytics-feladatot hozott létre az IoT Edge-eszközből származó adatok elemzéséhez. Ezután letöltötte az Azure Stream Analytics-modult az IoT Edge-eszközre, hogy helyileg lehessen feldolgozni és megválaszolni a hőmérséklet-növekedési adatokat, valamint az összesített adatstreamet a felhőbe feltölteni. A többi oktatóanyagra tovább lépve megtudhatja, hogyan lehet az Azure IoT Edge használatával egyéb üzleti megoldásokat létrehozni.

> [!div class="nextstepaction"]
> [Azure Machine Learning-modell üzembe helyezése modulként](tutorial-deploy-machine-learning.md)