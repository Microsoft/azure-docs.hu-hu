---
title: Az Azure IoT Central kiterjesztése egyéni elemzéssel | Microsoft Docs
description: Megoldás fejlesztőként konfiguráljon egy IoT Central alkalmazást egyéni elemzések és vizualizációk végrehajtásához. Ez a megoldás Azure Databricks használ.
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 5f9b255e8aa370184ec244ed418f02e55fc149b3
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70049164"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>Az Azure IoT Central kiterjesztése egyéni elemzésekkel

Ez az útmutató bemutatja, hogyan bővíthető a IoT Central alkalmazása egyéni elemzésekkel és vizualizációkkal. A példa egy [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) munkaterületet használ a IoT Central telemetria stream elemzéséhez, valamint vizualizációk, például [Box](https://wikipedia.org/wiki/Box_plot)-ábrázolások létrehozásához.

Ez a útmutató azt mutatja be, hogyan terjeszthető ki IoT Central, hogy mit tehet a [beépített elemzési eszközökkel](howto-create-analytics.md).

Ebben a útmutatóban a következőket sajátíthatja el:

* Stream telemetria egy IoT Central alkalmazásból *folyamatos adatexportálás*használatával.
* Azure Databricks-környezet létrehozása az eszközök telemetria elemzéséhez és ábrázolásához.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek végrehajtásához aktív Azure-előfizetésre van szükség.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

### <a name="iot-central-application"></a>IoT Central alkalmazás

Hozzon létre egy IoT Central alkalmazást az [Azure IoT Central-My Applications](https://aka.ms/iotcentral) lapon a következő beállításokkal:

| Beállítás | Value |
| ------- | ----- |
| Fizetési terv | Utólagos, használatalapú fizetés |
| Alkalmazássablon | Contoso-példa |
| Alkalmazás neve | Fogadja el az alapértelmezett értéket, vagy válassza ki a saját nevét |
| URL | Fogadja el az alapértelmezett értéket, vagy válassza ki a saját egyedi URL-előtagját |
| Címtár | Azure Active Directory bérlő |
| Azure-előfizetés | Az Azure-előfizetése |
| Régió | East US |

A jelen cikkben szereplő példák és képernyőképek az **USA keleti** régióját használják. Válasszon egy helyet az Ön számára, és győződjön meg róla, hogy az összes erőforrást ugyanabban a régióban hozza létre.

### <a name="resource-group"></a>Resource group

A [Azure Portal használatával hozzon létre egy](https://portal.azure.com/#create/Microsoft.ResourceGroup) **IoTCentralAnalysis** nevű erőforráscsoportot a létrehozott egyéb erőforrások tárolására. Hozza létre az Azure-erőforrásokat a IoT Central alkalmazással megegyező helyen.

### <a name="event-hubs-namespace"></a>Event Hubs-névtér

A [Azure Portal használatával hozzon létre egy Event Hubs névteret](https://portal.azure.com/#create/Microsoft.EventHub) a következő beállításokkal:

| Beállítás | Érték |
| ------- | ----- |
| Name (Név)    | Adja meg a névtér nevét |
| Tarifacsomag | Alapszintű |
| Subscription | Az Ön előfizetése |
| Resource group | IoTCentralAnalysis |
| Location | East US |
| Átviteli egységek | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks munkaterület

A [Azure Portal használatával hozzon létre egy Azure Databricks szolgáltatást](https://portal.azure.com/#create/Microsoft.Databricks) a következő beállításokkal:

| Beállítás | Value |
| ------- | ----- |
| Munkaterület neve    | Válassza ki a munkaterület nevét |
| Subscription | Az Ön előfizetése |
| Resource group | IoTCentralAnalysis |
| Location | East US |
| Tarifacsomag | Standard |

A szükséges erőforrások létrehozásakor a **IoTCentralAnalysis** -erőforráscsoport a következő képernyőképhez hasonlóan néz ki:

![IoT Central Analysis Resource Group](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

IoT Central alkalmazást úgy konfigurálhatja, hogy folyamatosan exportálja a telemetria egy Event hubhoz. Ebben a szakaszban egy Event hub-t hoz létre, amely telemetria fogad a IoT Central alkalmazásból. Az Event hub a telemetria az Stream Analytics-feladatokhoz továbbítja a feldolgozáshoz.

1. A Azure Portal navigáljon a Event Hubs névtérhez, és válassza a **+ Event hub**elemet.
1. Nevezze el az Event hub- **centralexport**, majd válassza a **Létrehozás**lehetőséget.
1. A névtérben található Event hubok listájában válassza a **centralexport**lehetőséget. Ezután válassza a **megosztott hozzáférési házirendek**elemet.
1. Válassza a **+ Hozzáadás** lehetőséget. Hozzon létre egy **figyelés** nevű szabályzatot a **figyelési** jogcímen.
1. Ha a házirend elkészült, jelölje ki azt a listában, majd másolja ki a **kapcsolódási karakterlánc – elsődleges kulcs** értékét.
1. Jegyezze fel ezt a kapcsolati karakterláncot, később, amikor konfigurálja a Databricks-jegyzetfüzetet az Event hub-ból való olvasáshoz.

A Event Hubs névtere a következő képernyőképre hasonlít:

![Event Hubs-névtér](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Az Exportálás konfigurálása IoT Central

Navigáljon a contoso-sablonból létrehozott [IoT Central alkalmazáshoz](https://aka.ms/iotcentral) . Ebben a szakaszban úgy konfigurálja az alkalmazást, hogy a szimulált eszközökről az telemetria továbbítsa az alkalmazást. Az Exportálás konfigurálása:

1. Navigáljon a **folyamatos adatexportálás** lapra, válassza az **+ új**, majd az **Azure Event Hubs**elemet.
1. Az Exportálás konfigurálásához használja a következő beállításokat, majd válassza a **Mentés**lehetőséget:

    | Beállítás | Value |
    | ------- | ----- |
    | Megjelenítendő név | Exportálás az Event Hubs szolgáltatásba |
    | Enabled | Bekapcsolva |
    | Event Hubs-névtér | Az Event Hubs névtér neve |
    | Eseményközpont | centralexport |
    | Mérések | Bekapcsolva |
    | Eszközök | Ki |
    | Eszközsablonok | Ki |

![Folyamatos adatexportálási konfiguráció](media/howto-create-custom-analytics/cde-configuration.png)

A folytatás előtt várjon, amíg az Exportálás állapota **fut** .

## <a name="configure-databricks-workspace"></a>Databricks-munkaterület konfigurálása

A Azure Portal navigáljon a Azure Databricks szolgáltatáshoz, és válassza a **munkaterület elindítása**lehetőséget. Megnyílik egy új lap a böngészőben, és bejelentkezik a munkaterületre.

### <a name="create-a-cluster"></a>Fürt létrehozása

A **Azure Databricks** oldalon, a gyakori feladatok listájában válassza az **új fürt**elemet.

A fürt létrehozásához használja a következő táblázatban található információkat:

| Beállítás | Érték |
| ------- | ----- |
| Fürt neve | centralanalysis |
| Fürt üzemmód | Standard |
| Databricks Runtime verziója | 5,3 (Scala 2,11, Spark 2.4.0) |
| Python-verzió | 3 |
| Automatikus skálázás engedélyezése | Nem |
| Megszakítás ennyi perc inaktivitás után | 30 |
| Feldolgozó típusa | Standard_DS3_v2 |
| Feldolgozók | 1 |
| Illesztőprogram típusa | Ugyanaz, mint a feldolgozó |

A fürt létrehozása több percet is igénybe vehet, amíg a folytatás előtt várnia kell, hogy a fürt létrehozása befejeződjön.

### <a name="install-libraries"></a>Tárak telepítése

A **fürtök** lapon várjon, amíg a fürt állapota nem **fut**.

A következő lépések bemutatják, hogyan importálhatja a mintát a fürthöz szükséges könyvtárba:

1. A **fürtök** lapon várjon, amíg a **centralanalysis** interaktív fürt állapota **fut**.

1. Válassza ki a fürtöt, majd kattintson a **tárak** fülre.

1. A **tárak** lapon válassza az **új telepítése**lehetőséget.

1. A **könyvtár telepítése** lapon válassza a **Maven** lehetőséget a könyvtár forrásaként.

1. A **koordináták** szövegmezőbe írja be a következő értéket:`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. A **telepítés** gombra kattintva telepítheti a tárat a fürtön.

1. A könyvtár állapota most **telepítve**van:

    ![Telepített tár](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Databricks-jegyzetfüzet importálása

A következő lépésekkel importálhatja a Python-kódot tartalmazó Databricks-jegyzetfüzetet a IoT Central telemetria elemzéséhez és megjelenítéséhez:

1. Navigáljon a **munkaterület** lapra a Databricks-környezetben. Válassza ki a fiók neve melletti legördülő menüt, majd válassza az **Importálás**lehetőséget.

1. Válassza az importálás egy URL-címről lehetőséget, és adja meg a következő címet:[https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. A jegyzetfüzet importálásához válassza az **Importálás**lehetőséget.

1. Válassza ki a munkaterületet az importált jegyzetfüzet megtekintéséhez:

    ![Importált jegyzetfüzet](media/howto-create-custom-analytics/import-notebook.png)

1. Szerkessze a kódot az első Python-cellában, és adja hozzá a korábban mentett Event Hubs-kapcsolódási karakterláncot:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Elemzés futtatása

Az elemzés futtatásához csatolni kell a jegyzetfüzetet a fürthöz:

1. Válassza a leválasztva lehetőséget, majd válassza ki a **centralanalysis** -fürtöt.
1. Ha a fürt nem fut, indítsa el.
1. A jegyzetfüzet elindításához kattintson a Futtatás gombra.

Előfordulhat, hogy az utolsó cellában hibaüzenet jelenik meg. Ha igen, ellenőrizze, hogy az előző cellák futnak-e, várjon egy percet, amíg egyes adatfájlok tárolva lesznek a tárolóba, majd futtassa újra az utolsó cellát.

### <a name="view-smoothed-data"></a>Simított adatnézetek megtekintése

A jegyzetfüzetben görgessen le a 14. cellába, és tekintse meg a mozgóátlag nedvességtartalmát az eszköz típusa alapján. Ez a nyomtatás folyamatosan frissül, mivel a streaming telemetria megérkezik:

![Simított telemetria ábrázolása](media/howto-create-custom-analytics/telemetry-plot.png)

A diagramot átméretezheti a jegyzetfüzetben.

### <a name="view-box-plots"></a>Nézet Box-Telkek

A jegyzetfüzetben görgessen le a 20. cellába, és tekintse meg a [mezők ábrázolását](https://en.wikipedia.org/wiki/Box_plot). A Box-mintaterületek a statikus adattartalomon alapulnak, így a frissítéshez újra kell futtatnia a cellát:

![Box-ábrázolások](media/howto-create-custom-analytics/box-plots.png)

A ábrákat átméretezheti a jegyzetfüzetben.

## <a name="tidy-up"></a>Kitakarítás

A fenti útmutató és a szükségtelen költségek elkerülése érdekében törölje a **IoTCentralAnalysis** erőforráscsoportot a Azure Portal.

A IoT Central alkalmazást a **felügyeleti** lapról törölheti az alkalmazáson belül.

## <a name="next-steps"></a>További lépések

Ebben a útmutatóban megtanulta, hogyan végezheti el a következőket:

* Stream telemetria egy IoT Central alkalmazásból *folyamatos adatexportálás*használatával.
* Hozzon létre egy Azure Databricks környezetet a telemetria-adatelemzéshez és a nyomtatáshoz.

Most, hogy már tudja, hogyan hozhat létre egyéni elemzéseket, a következő lépés az, hogy megtudja, hogyan jelenítheti meg [és elemezheti az Azure IoT Central-adatait egy Power bi irányítópulton](howto-connect-powerbi.md).
