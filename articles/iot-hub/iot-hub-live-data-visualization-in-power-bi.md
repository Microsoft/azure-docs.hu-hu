---
title: Adatok valós idejű megjelenítése az Azure IoT Hub – Power BI
description: A Power BI használatával megjelenítheti az érzékelőből összegyűjtött hőmérsékleti és páratartalom-adatokat, és elküldheti azokat az Azure IoT hubhoz.
author: robinsh
keywords: valós idejű adatvizualizáció, élő adatvizualizáció, érzékelő adatvizualizációja
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: 82caf13618fe8483ab8d3a622c6c0d51ab05a206
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177334"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Valós idejű érzékelők adatainak megjelenítése az Azure IoT Hub használatával Power BI

![Végpontok közötti diagram](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Megtudhatja, hogyan jelenítheti meg a valós idejű érzékelők adatait, amelyeket az Azure IoT hub a Power BI használatával kap. Ha egy webalkalmazással szeretné megjeleníteni az IoT hub adatait, tekintse meg a [webalkalmazások használata az Azure IoT hub valós idejű érzékelők adatainak megjelenítéséhez](iot-hub-live-data-visualization-in-web-apps.md)című témakört.

## <a name="what-you-do"></a>Teendők

* Felhasználói csoport hozzáadásával az IoT hub készen áll az adathozzáférésre.

* Hozzon létre, konfiguráljon és futtasson egy Stream Analytics feladatot az IoT hub-ból az Ön Power BI-fiókjába való adatátvitel során.

* Hozzon létre és tegyen közzé egy Power BI jelentést az adatgyűjtés megjelenítéséhez.

## <a name="what-you-need"></a>Amire szükség lesz

* Fejezze be a [málna PI online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz egyik oktatóanyagát; például a [málna PI és a node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ezek a cikkek a következő követelményekre vonatkoznak:
  
  * Aktív Azure-előfizetés.
  * Az előfizetéshez tartozó Azure IoT hub.
  * Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT hub-nak.

* Power BI-fiók. ([Power bi ingyenes kipróbálása](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics-feladatok létrehozása, konfigurálása és futtatása

Kezdjük egy Stream Analytics feladatok létrehozásával. A feladatnak a létrehozása után meg kell határoznia a bemeneteket, a kimeneteket és az adatok lekéréséhez használt lekérdezést.

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforrás létrehozása**  >  **eszközök internetes hálózata**  >  **stream Analyticsi feladatot**.

2. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie.

   **Erőforráscsoport**: használja ugyanazt az erőforráscsoportot, amelyet az IoT hub használ.

   **Hely**: használja ugyanazt a helyet, mint az erőforráscsoport.

   ![Stream Analytics-feladatok létrehozása az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. Válassza a **Létrehozás** lehetőséget.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. Nyissa meg a Stream Analytics feladatot.

2. A **Feladattopológia** területen válassza a **Bemenetek** lehetőséget.

3. A **bemenetek** ablaktáblán válassza a **stream-bemenet hozzáadása** lehetőséget, majd a legördülő listából válassza a **IoT hub** lehetőséget. Az új beviteli panelen adja meg a következő adatokat:

   **Bemeneti alias**: adjon meg egy egyedi aliast a bevitelhez.

   **Válassza ki IoT hub az előfizetésből**: jelölje be ezt a választógombot.

   **Előfizetés**: válassza ki az oktatóanyaghoz használni kívánt Azure-előfizetést.

   **IoT hub**: válassza ki az oktatóanyaghoz használni kívánt IoT hub.

   **Végpont**: Válassza az **Üzenetkezelés** lehetőséget.

   **Megosztott elérési házirend neve**: válassza ki annak a megosztott hozzáférési szabályzatnak a nevét, amelyet az IoT hub-hoz használni kíván stream Analytics feladatokhoz. Ebben az oktatóanyagban a *szolgáltatás* lehetőséget választhatja. A *szolgáltatási* házirendet alapértelmezés szerint az új IoT-hubokon hozza létre, és engedélyt ad az IoT hub által elérhető Felhőbeli végpontok küldésére és fogadására. További információ: hozzáférés- [vezérlés és engedélyek](iot-hub-devguide-security.md#access-control-and-permissions).

   **Megosztott elérési házirend kulcsa**: Ez a mező automatikusan ki van töltve a megosztott elérési szabályzat nevének kiválasztása alapján.

   **Fogyasztói csoport**: válassza ki a korábban létrehozott fogyasztói csoportot.

   Hagyja meg az összes többi mezőt az alapértelmezett értékeken.

   ![Bemenet hozzáadása egy Stream Analytics feladathoz az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. Kattintson a **Mentés** gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen válassza a **Kimenetek** lehetőséget.

2. A **kimenetek** ablaktáblán válassza a **hozzáadás** és a **Power bi** lehetőséget.

3. Az **Power bi – új kimenet** ablaktáblán válassza az **Engedélyezés** lehetőséget, és kövesse az utasításokat, hogy bejelentkezzen a Power bi-fiókjába.

4. Miután bejelentkezett a Power BIba, adja meg a következő adatokat:

   **Kimeneti alias**: a kimenet egyedi aliasa.

   **Csoport munkaterület**: válassza ki a célcsoport-munkaterületet.

   **Adatkészlet neve**: adja meg az adatkészlet nevét.

   **Táblázat neve**: adjon meg egy Táblanév nevet.

   **Hitelesítési mód**: hagyja meg az alapértelmezett értéket.

   ![Kimenet hozzáadása Stream Analytics feladatokhoz az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. Kattintson a **Mentés** gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen válassza a **Lekérdezés** lehetőséget.

2. A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére.

3. A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére.

   ![Lekérdezés hozzáadása Stream Analytics feladatokhoz az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. Válassza a **lekérdezés mentése** lehetőséget.

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A stream Analyticsi feladatokban válassza az **Áttekintés** lehetőséget, majd kattintson **az indítás**  >  **most**  >  **Indítás** gombra. Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

![Stream Analytics-feladatok futtatása az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Power BI jelentés létrehozása és közzététele az adatgyűjtés megjelenítéséhez

A következő lépések bemutatják, hogyan hozhat létre és tehet közzé jelentést a Power BI szolgáltatás használatával. Ezeket a lépéseket néhány módosítással követheti, ha az "új Look" kifejezést szeretné használni Power BIban. A különbségek megismeréséhez és az "új nézet" megkereséséhez tekintse meg az [Power bi szolgáltatás új megjelenését](/power-bi/consumer/service-new-look).

1. Győződjön meg arról, hogy az eszközön fut a minta alkalmazás. Ha nem, tekintse át az oktatóanyagokat az [eszköz beállítása](./iot-hub-raspberry-pi-kit-node-get-started.md)alatt.

2. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/en-us/)-fiókjába.

3. Válassza ki a használni kívánt munkaterületet, **saját munkaterületet**.

4. Válassza az **adatkészletek** lehetőséget.

   A Stream Analytics-feladathoz tartozó kimenet létrehozásakor megadott adatkészlet jelenik meg.

5. A létrehozott adatkészlet esetében válassza a **jelentés hozzáadása** lehetőséget (az adatkészlet nevének jobb oldalán lévő első ikon).

   ![Microsoft Power BI-jelentés létrehozása](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. Hozzon létre egy vonaldiagramot, amely a valós időben jeleníti meg a hőmérséklet változását.

   1. A jelentés létrehozása lap **vizualizációk** paneljén válassza a diagram ikont egy vonalas diagram hozzáadásához.

   2. A **Mezők** panelen bontsa ki Stream Analytics-feladat kimenetének létrehozásakor megadott táblát.

   3. Az **EventEnqueuedUtcTime** elemet húzza a **Vizualizációk** panel **Tengely** részére.

   4. Húzza a **hőmérséklet** elemet az **Értékek** helyre.

      Létrejön a vonaldiagram. Az X tengely az UTC időzóna szerinti dátumot is időt mutatja. Az Y tengelyen az érzékelőből származó hőmérsékleti adatok láthatók.

      ![Vonali diagram hozzáadása a Microsoft Power BI jelentéshez](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. Hozzon létre egy másik vonaldiagramot, amely a valós időben jeleníti meg a páratartalom változását. Ehhez kattintson a vászon egy üres részére, és kövesse a fenti lépéseket, hogy az y tengely x tengelyén és **nedvességtartalmán** **EventEnqueuedUtcTime** .

   ![Diagram hozzáadása a páratartalomhoz egy Microsoft Power BI jelentéshez](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. A jelentés mentéséhez válassza a **Mentés** lehetőséget.

9. Válassza a **jelentések** lehetőséget a bal oldali ablaktáblán, majd válassza ki az imént létrehozott jelentést.

10. Válassza   >  **a fájl webes közzététel** lehetőséget.

    ![A Microsoft Power BI jelentés webes közzétételének kiválasztása](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Ha értesítést kap a rendszergazdától a beágyazási kód létrehozásának engedélyezéséhez, előfordulhat, hogy kapcsolatba kell lépnie velük. A lépés elvégzéséhez engedélyezni kell a beágyazási kód létrehozását.
    >
    > ![Forduljon a rendszergazdai értesítéshez](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. Válassza a **beágyazási kód létrehozása** lehetőséget, majd válassza a **Közzététel** lehetőséget.

Megadhatja a jelentés hivatkozását, amelyet bárki megoszthat a jelentésekhez való hozzáféréshez, valamint egy kódrészletet, amellyel a jelentést beépítheti a blogjába vagy a webhelyre.

![Microsoft Power BI-jelentés közzététele](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

A Microsoft a mobileszköz-irányítópultok és-jelentések megtekintését és Power BI interakcióját is biztosítja a [Power bi Mobile apps](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) szolgáltatásban.

## <a name="next-steps"></a>Következő lépések

Sikeresen felhasználta Power BI az Azure IoT hub valós idejű érzékelői adatainak megjelenítéséhez.

Az Azure IoT Hubból származó adatok megjelenítésének másik módja: [webalkalmazás használata az azure IoT hub valós idejű érzékelők adatainak megjelenítéséhez](iot-hub-live-data-visualization-in-web-apps.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
