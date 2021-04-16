---
title: Adatok valós idejű vizualizációja Azure IoT Hub – Power BI
description: A Power BI az érzékelőből gyűjtött és az Azure IoT Hubnak küldött hőmérséklet- és páratartalom-adatok megjelenítésére.
author: robinsh
keywords: valós idejű adatvizualizáció, élő adatvizualizáció, érzékelőadatok vizualizációja
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/08/2020
ms.author: robinsh
ms.openlocfilehash: 0b099f4ce91fd24e8d7baec054bcfc5a6cf0b032
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567111"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Valós idejű érzékelőadatok vizualizációja a Azure IoT Hub a Power BI

![Végpontok között ábrázolt diagram](./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Ebből a cikkből megtudhatja, hogyan vizualizálhatja az Azure IoT Hub által kapott valós idejű érzékelőadatokat a Power BI. Ha webalkalmazással szeretné vizualizálni az IoT Hubban található adatokat, tekintse meg a következőt: Valós idejű érzékelőadatok vizualizációja webalkalmazással a [Azure IoT Hub.](iot-hub-live-data-visualization-in-web-apps.md)

## <a name="prerequisites"></a>Előfeltételek

* Töltse ki a [Raspberry Pi online szimulátor oktatóanyagát](iot-hub-raspberry-pi-web-simulator-get-started.md) vagy az eszköz egyik oktatóanyagát. A [Raspberry Pi-hoz ](iot-hub-raspberry-pi-kit-node-get-started.md) például használhatja a node.jsvagy a [Telemetria](quickstart-send-telemetry-dotnet.md) küldése gyorsútmutatók valamelyikét. Ezek a cikkek a következő követelményeket fedik le:
  
  * Aktív Azure-előfizetés.
  * Egy Azure IoT Hub az előfizetésben.
  * Egy ügyfélalkalmazás, amely üzeneteket küld az Azure IoT Hubnak.

* Power BI-fiók. ([Próbálja Power BI ingyenes )](https://powerbi.microsoft.com/)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Új feladat létrehozása, konfigurálása és Stream Analytics futtatása

Először hozzunk létre egy Stream Analytics feladatot. A feladat létrehozása után meg kell határoznia az adatok lekéréséhez használt bemeneteket, kimeneteket és lekérdezést.

### <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

1. A [Azure Portal](https://portal.azure.com)válassza az **Erőforrás létrehozása eszközök internetes hálózata** Stream Analytics  >    >  **feladatot.**

2. Adja meg a feladat alábbi adatait.

   **Feladat neve**: A feladat neve. A névnek globálisan egyedinek kell lennie.

   **Erőforráscsoport:** Használja ugyanazt az erőforráscsoportot, mint az IoT Hub.

   **Hely:** Használja ugyanazt a helyet, mint az erőforráscsoport.

   ![Új Stream Analytics létrehozása az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png)

3. Válassza a **Létrehozás** lehetőséget.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Bemenet hozzáadása a Stream Analytics-feladathoz

1. Nyissa meg a Stream Analytics feladatot.

2. A **Feladattopológia** területen válassza a **Bemenetek** lehetőséget.

3. A **Bemenetek panelen** válassza a **Streambemenet** hozzáadása lehetőséget, majd válassza IoT Hub lehetőséget a legördülő listából.  Az új beviteli panelen adja meg a következő adatokat:

   **Bemeneti alias:** Adjon meg egy egyedi aliast a bemenethez.

   **Válassza IoT Hub előfizetésből:** Jelölje be ezt a választógombot.

   **Előfizetés:** Válassza ki az oktatóanyaghoz használt Azure-előfizetést.

   **IoT Hub:** Válassza IoT Hub oktatóanyaghoz használt IoT Hub lehetőséget.

   **Végpont**: Válassza az **Üzenetkezelés** lehetőséget.

   **Megosztott hozzáférési szabályzat neve:** Válassza ki annak a megosztott hozzáférési szabályzatnak a nevét, Stream Analytics az IoT Hubhoz használni kívánt megosztott hozzáférési szabályzatot. Ebben az oktatóanyagban a szolgáltatás lehetőséget *választhatja.* A *szolgáltatási* szabályzat alapértelmezés szerint új IoT Hubon jön létre, és engedélyt ad az IoT Hub által elérhetővé tévő felhőoldali végpontokra való küldésre és fogadásra. További információ: [Hozzáférés-vezérlés és engedélyek.](iot-hub-devguide-security.md#access-control-and-permissions)

   **Megosztott hozzáférési szabályzat kulcsa:** Ez a mező automatikusan ki lesz töltve a megosztott hozzáférési szabályzat nevéhez kiválasztott beállítás alapján.

   **Fogyasztói csoport:** Válassza ki a korábban létrehozott fogyasztói csoportot.

   Hagyja meg az összes többi mező alapértelmezett beállítását.

   ![Bemenet hozzáadása egy Stream Analytics feladathoz az Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png)

4. Kattintson a **Mentés** gombra.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Kimenet hozzáadása a Stream Analytics-feladathoz

1. A **Feladattopológia** területen válassza a **Kimenetek** lehetőséget.

2. A **Kimenetek panelen** válassza a **Hozzáadás lehetőséget, majd** **kattintson Power BI.**

3. A Power BI – Új kimenet **panelen** válassza az Authorize (Engedély) lehetőséget, és kövesse az utasításokat a Power BI bejelentkezéshez. 

4. Miután bejelentkezett a Power BI, adja meg a következő adatokat:

   **Kimeneti alias:** A kimenet egyedi aliasa.

   **Csoport munkaterülete:** Válassza ki a célcsoport-munkaterületet.

   **Adatkészlet neve:** Adja meg az adatkészlet nevét.

   **Tábla neve:** Adja meg a tábla nevét.

   **Hitelesítési mód:** Hagyja meg az alapértelmezett értéket.

   ![Kimenet hozzáadása Stream Analytics Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png)

5. Kattintson a **Mentés** gombra.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>A Stream Analytics-feladat lekérdezésének konfigurálása

1. A **Feladattopológia** területen válassza a **Lekérdezés** lehetőséget.

2. A `[YourInputAlias]` elemet cserélje le a feladat bemeneti áljelére.

3. A `[YourOutputAlias]` elemet cserélje le a feladat kimeneti áljelére.

   ![Lekérdezés hozzáadása egy azure-Stream Analytics feladathoz](./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png)

4. Válassza a **Lekérdezés mentése lehetőséget.**

### <a name="run-the-stream-analytics-job"></a>Stream Analytics-feladat futtatása

A Stream Analytics válassza az **Áttekintés,** majd az **Indítás**  >  **most indítás**  >  **lehetőséget.** Ha a feladat sikeresen elindult, a feladat állapota **Leállítva** értékről **Fut** értékre változik.

![Feladat futtatása Stream Analytics Azure-ban](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Jelentés létrehozása és Power BI az adatok megjelenítéséhez

Az alábbi lépések azt mutatják be, hogyan hozhat létre és tehet közzé jelentést a Power BI szolgáltatás. Ha az "új megjelenést" szeretné használni a Power BI. A különbségek és az "új megjelenés" közötti navigáláshoz tekintse meg az új megjelenést a [Power BI szolgáltatás.](/power-bi/consumer/service-new-look)

1. Győződjön meg arról, hogy a mintaalkalmazás fut az eszközön. Ha nem, tekintse meg az Eszköz beállítása [alatt található oktatóanyagokat.](./iot-hub-raspberry-pi-kit-node-get-started.md)

2. Jelentkezzen be a [Power BI](https://powerbi.microsoft.com/en-us/)-fiókjába.

3. Válassza ki a használt Saját **munkaterületet.**

4. Válassza **az Adatkészletek lehetőséget.**

   Látnia kell az adatkészletet, amely a feladat kimenetének létrehozásakor Stream Analytics meg.

5. A létrehozott adatkészlethez válassza a Jelentés hozzáadása **lehetőséget** (az adatkészlet nevétől jobbra található első ikont).

   ![Microsoft-jelentés Power BI létrehozása](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png)

6. Hozzon létre egy vonaldiagramot, amely a valós időben jeleníti meg a hőmérséklet változását.

   1. A **jelentéskészítési oldal** Vizualizációk panelen válassza a vonaldiagram ikont vonaldiagram hozzáadásához.

   2. A **Mezők** panelen bontsa ki Stream Analytics-feladat kimenetének létrehozásakor megadott táblát.

   3. Az **EventEnqueuedUtcTime** elemet húzza a **Vizualizációk** panel **Tengely** részére.

   4. Húzza a **hőmérséklet** elemet az **Értékek** helyre.

      Létrejön a vonaldiagram. Az X tengely az UTC időzóna szerinti dátumot is időt mutatja. Az Y tengelyen az érzékelőből származó hőmérsékleti adatok láthatók.

      ![Hőmérsékleti vonaldiagram hozzáadása microsoftos Power BI jelentéshez](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png)

7. Hozzon létre egy másik vonaldiagramot, amely a valós időben jeleníti meg a páratartalom változását. Ehhez kattintson a vászon egy üres részére, és a fenti lépésekkel helyezze az **EventEnqueuedUtcTime** helyére az x tengelyt és a páratartalmat az y tengelyen. 

   ![Vonaldiagram hozzáadása a páratartalommal kapcsolatos Microsoft-Power BI jelentéshez](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. A **jelentés mentéséhez** válassza a Mentés lehetőséget.

9. A **bal oldali** panelen válassza a Jelentések lehetőséget, majd válassza ki az előbb létrehozott jelentést.

10. Válassza **a Fájl** közzététele a  >  **weben lehetőséget.**

    ![Válassza a Webes közzététel lehetőséget a Microsoft Power BI jelentéshez](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-publish-to-web.png)

    > [!NOTE]
    > Ha értesítést kap, hogy lépjen kapcsolatba a rendszergazdával a beágyazási kód létrehozásának engedélyezéséhez, előfordulhat, hogy kapcsolatba kell lépnie velük. A lépés befejezéséhez engedélyezni kell a beágyazási kód létrehozását.
    >
    > ![Kapcsolatfelvétel a rendszergazdai értesítéssel](./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png)

11. Válassza **a Beágyazási kód létrehozása,** majd a **Közzététel lehetőséget.**

Meg van biztosítanak egy jelentéshivatkozást, amely bárkivel megosztható a jelentéshez való hozzáféréshez, valamint egy kódrészletet, amely a jelentés a blogba vagy webhelybe való integrálására használható.

![Microsoft-jelentés Power BI közzététele](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png)

A Microsoft Power BI [mobilalkalmazásokat](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) is kínál a mobileszközén Power BI irányítópultok és jelentések megtekintéséhez és a velük való interakcióhoz.

## <a name="next-steps"></a>Következő lépések

Sikeresen használta a Power BI az Azure IoT Hub valós idejű érzékelői adatainak megjelenítésére.

A vizualizációk adatainak Azure IoT Hub lásd: Webalkalmazás használata valós idejű érzékelőadatok vizualizálására a [Azure IoT Hub.](iot-hub-live-data-visualization-in-web-apps.md)

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
