---
title: A Azure IoT Central kiterjesztése egyéni elemzési | Microsoft Docs
description: Megoldásfejlesztőként konfiguráljon egy IoT Central egyéni elemzésekhez és vizualizációkhoz. Ez a megoldás a Azure Databricks.
author: philmea
ms.author: philmea
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 9ada9947b217944d9aec9f785f4716bfe43315b1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872766"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Az Azure IoT Central kibővítése egyéni elemzésekkel az Azure Databricks használatával

Ez az útmutató azt mutatja be megoldásfejlesztőként, hogyan bővítheti IoT Central alkalmazásait egyéni elemzésekkel és vizualizációk használatával. A példa egy [Azure Databricks](/azure/azure-databricks/) használ a telemetriastream IoT Central és vizualizációk, például dobozábrázolások [létrehozásához.](https://wikipedia.org/wiki/Box_plot)  

Ez az útmutató bemutatja, hogyan bővítheti a IoT Central a beépített elemzőeszközökkel már meglévő [funkciókon túl.](./howto-create-custom-analytics.md)

Ez az útmutató a következő lehetőségekkel ismerkedik meg:

* Streamelési telemetria egy IoT Central folyamatos *adatexportációval.*
* Hozzon létre Azure Databricks környezetet az eszköz telemetriai adatainak elemzéséhez és ábrázolása érdekében.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseit egy aktív Azure-előfizetéssel kell végrehajtania.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="iot-central-application"></a>IoT Central alkalmazás

Hozzon létre IoT Central alkalmazást a [Azure IoT Central alkalmazáskezelő webhelyén](https://aka.ms/iotcentral) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Díjszabási csomag | Standard |
| Alkalmazássablon | Áruházbeli elemzés – feltételfigyelés |
| Alkalmazásnév | Fogadja el az alapértelmezett nevet, vagy válassza ki a saját nevét |
| URL-cím | Fogadja el az alapértelmezett url-címet, vagy válasszon saját egyedi URL-előtagot |
| Címtár | Saját Azure Active Directory bérlője |
| Azure-előfizetés | Az Azure-előfizetése |
| Region | Az Ön legközelebbi régiója |

A cikkben található példák és képernyőképek a Egyesült Államok **használják.** Válasszon egy közeli helyet, és győződjön meg arról, hogy az összes erőforrást ugyanabban a régióban hozza létre.

Ez az alkalmazássablon két szimulált termosztátos eszközt tartalmaz, amelyek telemetriát küldenek.

### <a name="resource-group"></a>Erőforráscsoport

A Azure Portal [hozzon létre](https://portal.azure.com/#create/Microsoft.ResourceGroup) egy **IoTCentralAnalysis** nevű erőforráscsoportot, amely tartalmazza a többi létrehozott erőforrást. Hozza létre az Azure-erőforrásokat ugyanazon a helyen, ahol a IoT Central is.

### <a name="event-hubs-namespace"></a>Event Hubs-névtér

A Azure Portal [hozzon létre egy Event Hubs a](https://portal.azure.com/#create/Microsoft.EventHub) következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Név    | A névtér nevének kiválasztása |
| Tarifacsomag | Alapszintű |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | IoTCentralAnalysis |
| Hely | USA keleti régiója |
| Átviteli egységek | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks munkaterület

A Azure Portal [hozzon létre egy Azure Databricks szolgáltatást](https://portal.azure.com/#create/Microsoft.Databricks) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Munkaterület neve    | A munkaterület nevének kiválasztása |
| Előfizetés | Az Ön előfizetése |
| Erőforráscsoport | IoTCentralAnalysis |
| Hely | USA keleti régiója |
| Tarifacsomag | Standard |

A szükséges erőforrások létrehozása után az **IoTCentralAnalysis** erőforráscsoport az alábbi képernyőképen láthatóhoz hasonlít:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="az IoT Central erőforráscsoportot.":::

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Konfigurálhat egy IoT Central, hogy folyamatosan exportálja a telemetriát egy eseményközpontba. Ebben a szakaszban egy eseményközpontot hoz létre, amely telemetriát fogad a IoT Central alkalmazásból. Az eseményközpont továbbítja a telemetriát a Stream Analytics feldolgozásra.

1. A Azure Portal lépjen a saját Event Hubs, és válassza **az + Eseményközpont lehetőséget.**
1. Nevezze el az eseményközpontot **központilagexportálva.**
1. A névtérben található eseményközpontok listájában válassza a **centralexport lehetőséget.** Ezután válassza **a Megosztott hozzáférési szabályzatok lehetőséget.**
1. Válassza a **+ Hozzáadás** lehetőséget. Hozzon létre egy **SendListen nevű szabályzatot** a **Send** és **Listen jogcímekkel.**
1. Ha a szabályzat elkészült, jelölje ki a listában, majd másolja ki a **Kapcsolati sztring – elsődleges kulcs értékét.**
1. Jegyezze fel ezt a kapcsolati sztringet, mert később, amikor konfigurálja a Databricks-jegyzetfüzetet az eseményközpontból való olvasásra.

A Event Hubs a következő képernyőképhez hasonló:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="a névtér Event Hubs képe.":::

## <a name="configure-export-in-iot-central"></a>Exportálás konfigurálása a IoT Central

Ebben a szakaszban úgy konfigurálja az alkalmazást, hogy telemetriát streamel a szimulált eszközeiről az eseményközpontba.

Az [alkalmazáskezelő Azure IoT Central](https://aka.ms/iotcentral) keresse meg a korábban IoT Central alkalmazáshoz. Az exportálás konfiguráláshoz először hozzon létre egy célhelyet:

1. Lépjen az **Adatexportáció oldalra,** majd válassza a **Célhely lehetőséget.**
1. Válassza **az + Új cél lehetőséget.**
1. Célhely létrehozásához használja az alábbi táblázatban található értékeket:

    | Beállítás | Érték |
    | ----- | ----- |
    | Célnév | Telemetriai eseményközpont |
    | Cél típusa | Azure Event Hubs |
    | Kapcsolati sztring | Az eseményközpont korábban feljegyett kapcsolati sztring |

    Az **eseményközpont központilag** **támogatottként jelenik meg.**

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Képernyőkép az adatexport célhelyről.":::

1. Kattintson a **Mentés** gombra.

Az exportálási definíció létrehozása:

1. Lépjen az **Adatexportál oldalra,** és válassza **az + Új exportálás lehetőséget.**

1. Az alábbi táblázatban található értékekkel konfigurálhatja az exportálást:

    | Beállítás | Érték |
    | ------- | ----- |
    | Exportálás neve | Eseményközpont exportálása |
    | Engedélyezve | Be |
    | Az exportálni szükséges adatok típusa | Telemetria |
    | Célhelyek | Válassza **a + Cél,** majd a **Telemetria eseményközpont lehetőséget** |

1. Kattintson a **Mentés** gombra.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Képernyőkép az adatexport-definícióról.":::

A folytatás előtt várja meg, amíg az exportálás **állapota Kifogástalan** lesz az Adatok **exportálása** lapon.

## <a name="configure-databricks-workspace"></a>Databricks-munkaterület konfigurálása

A Azure Portal nyissa meg a Azure Databricks szolgáltatást, és válassza a **Munkaterület indítása lehetőséget.** Megnyílik egy új lap a böngészőben, és bejelentkezik a munkaterületre.

### <a name="create-a-cluster"></a>Fürt létrehozása

A **Azure Databricks** a gyakori feladatok listájában válassza az Új **fürt lehetőséget.**

A fürt létrehozásához használja az alábbi táblázatban található információkat:

| Beállítás | Érték |
| ------- | ----- |
| Fürt neve | centralanalysis |
| Fürt üzemmód | Standard |
| Databricks Runtime verziója | 5.5 LTS (Scala 2.11, Spark 2.4.5) |
| Python-verzió | 3 |
| Automatikus skálázás engedélyezése | No |
| Le kell ásni néhány perc inaktivitás után | 30 |
| Feldolgozó típusa | Standard_DS3_v2 |
| Munkavállalók | 1 |
| Illesztőprogram típusa | Ugyanaz, mint a feldolgozó |

A fürt létrehozása eltarthat néhány percig. A folytatás előtt várja meg, amíg a fürt létrehozása befejeződik.

### <a name="install-libraries"></a>Kódtárak telepítése

A **Fürtök lapon** várja meg, amíg a fürt állapota **Fut.**

A következő lépések azt mutatják be, hogyan importálhatja a mintához szükséges kódtárat a fürtbe:

1. A **Fürtök lapon** várja meg, amíg a **centralanalysis** interaktív fürt állapota **Fut.**

1. Válassza ki a fürtöt, majd válassza a **Kódtárak** lapot.

1. A **Kódtárak lapon** válassza az **Új telepítése lehetőséget.**

1. Az Install **Library (Könyvtár telepítése) lapon** válassza a **Maven** könyvtárforrást.

1. A **Koordináták** szövegmezőbe írja be a következő értéket: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Válassza **a Telepítés** lehetőséget a kódtár fürtre való telepítéséhez.

1. A kódtár állapota mostantól **Telepítve:**

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Képernyőkép a telepített kódtárról.":::

### <a name="import-a-databricks-notebook"></a>Databricks-jegyzetfüzet importálása

A következő lépésekkel importálhat egy Databricks-jegyzetfüzetet, amely a Python-kódot tartalmazza a telemetria elemzéséhez és IoT Central megjelenítéséhez:

1. Lépjen a **Munkaterület lapra** a Databricks-környezetben. Válassza ki a fiók neve melletti legördülő menüt, majd válassza az **Importálás lehetőséget.**

1. Válassza az URL-címből való importálást, és adja meg a következő címet: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. A jegyzetfüzet importáláshoz válassza az Importálás **lehetőséget.**

1. Az importált **jegyzetfüzet megtekintéséhez** válassza ki a munkaterületet:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Képernyőkép az importált jegyzetfüzetről.":::

5. Szerkessze az első Python-cellában lévő kódot, és adja hozzá Event Hubs korábban mentett kapcsolati sztringet:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Elemzés futtatása

Az elemzés futtatásához csatolnia kell a jegyzetfüzetet a fürthöz:

1. Válassza **a Leválasztva** lehetőséget, majd válassza **ki a központianalysis fürtöt.**
1. Ha a fürt nem fut, indítsa el.
1. A jegyzetfüzet futtatásához kattintson a Futtatás gombra.

Az utolsó cellában hiba jelenhet meg. Ha igen, ellenőrizze, hogy futnak-e az előző cellák, várjon egy percet, amíg a rendszer adatokat ír a tárolóba, majd futtassa újra az utolsó cellát.

### <a name="view-smoothed-data"></a>Smoothed data (Elsimított adatok megtekintése)

A jegyzetfüzetben görgessen le a 14. cellába a gördülő átlagos páratartalom eszköztípus szerint ábrázoló ábrázolásért. Ez a rajz folyamatosan frissül a streamelési telemetria érkezésekor:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Képernyőkép a Smoothed telemetria ábrázolásról.":::

A diagramot átméretezheti a jegyzetfüzetben.

### <a name="view-box-plots"></a>Mező-ábrázolás megtekintése

A jegyzetfüzetben görgessen le a 20. cellára, és tekintse meg [a mező ábrázolásokat.](https://en.wikipedia.org/wiki/Box_plot) A mező-ábrázolás statikus adatokon alapul, ezért a frissítésük érdekében újra kellfuttatni a cellát:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Képernyőkép a doboz ábrázolásról.":::

A diagramokat átméretezheti a jegyzetfüzetben.

## <a name="tidy-up"></a>Tidy up

Ha el szeretné kerülni a felesleges költségeket, törölje az **IoTCentralAnalysis** erőforráscsoportot a Azure Portal.

A felügyeleti IoT Central az alkalmazás **Felügyelet** lapján törölheti.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban a következőt sajátta el:

* Streamelési telemetria egy IoT Central folyamatos *adatexportációval.*
* Hozzon létre egy Azure Databricks a telemetriai adatok elemzéséhez és ábrázolása érdekében.

Most, hogy már tudja, hogyan hozhat létre egyéni elemzéseket, a javasolt következő lépés az, hogy megtanulja, hogyan vizualizálhatja és elemezheti a Azure IoT Central adatait [egy Power BI irányítópulton.](howto-connect-powerbi.md)
