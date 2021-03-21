---
title: Szimulált telemetria megjelenítése Time Series Insights-Azure-val | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja Time Series Insights környezetét az eszköz-szimulációs megoldás-gyorsító által generált telemetria megismeréséhez és elemzéséhez.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: e1409b43f0ce1fc0d8c622dda79e857ac6abdd33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854563"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Time Series Insights használata az eszköz-szimulációs megoldás-gyorsító által eljuttatott telemetria megjelenítéséhez

Az eszköz-szimulációs megoldás gyorsítása lehetővé teszi a szimulált eszközökről származó telemetria létrehozását a IoT-megoldások teszteléséhez. Ez a útmutató bemutatja, hogyan jelenítheti meg és elemezheti a szimulált telemetria Time Series Insights-környezet használatával.

## <a name="prerequisites"></a>Előfeltételek

A jelen útmutató lépéseinek követéséhez aktív Azure-előfizetésre van szükség. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

A útmutató lépései azt feltételezik, hogy telepítette az eszköz szimulációs megoldásának gyorssegédét az Azure-előfizetéséhez. Ha még nem telepítette az eszköz-szimulációt, tekintse meg a következőt: [eszköz-szimulációs telepítés](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) a githubon.

Ez a cikk azt feltételezi, hogy a megoldás-gyorsító neve **contoso-szimulációs**. A következő lépések elvégzése után cserélje le a **contoso-szimulációs** nevet a megoldás-gyorsító nevére.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Fogyasztói csoport létrehozása

Létre kell hoznia egy dedikált fogyasztói csoportot az IoT hub-ban, hogy Time Series Insights stream-telemetria. Az Time Series Insights egy IoT Hub fogyasztói csoport kizárólagos használatával kell rendelkezniük.

A következő lépések a Azure Cloud Shell Azure parancssori felületét használják a fogyasztói csoport létrehozásához:

1. Az IoT hub az eszköz-szimulációs megoldás-gyorsító üzembe helyezése során létrehozott számos erőforrás egyike. Futtassa a következő parancsot a IoT hub nevének megkereséséhez – ne felejtse el használni a megoldás-gyorsító nevét:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Az IoT hub a **Microsoft. Devices/IotHubs** típusú erőforrás.

1. Vegyen fel egy **devicesimulationtsi** nevű fogyasztói csoportot a hubhoz. A következő parancsban használja a hub és a megoldás-gyorsító nevét:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Most már bezárhatja a Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Új Time Series Insights-környezet létrehozása

A [Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amellyel kezelheti a Felhőbeli IoT. Új Time Series Insights környezet létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza **az erőforrás létrehozása**  >  **eszközök internetes hálózata**  >  **Time Series Insights**:

    ![Új Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Ha a Time Series Insights környezetet a megoldás-gyorssegédtel azonos erőforráscsoporthoz szeretné létrehozni, használja az alábbi táblázatban szereplő értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Környezet neve | A következő képernyőkép a **contoso-ÁME** nevet használja. A lépés elvégzése után válassza ki a saját egyedi nevét. |
    | Előfizetés | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Erőforráscsoport | **contoso – szimuláció**. Használja a megoldás-gyorsító nevét. |
    | Hely | Ez a példa az **USA keleti** régióját használja. Hozza létre a környezetet ugyanabban a régióban, mint az eszköz szimulációs gyorsítása. |
    | SKU |**S1** |
    | Kapacitás | **1** |

    ![A Time Series Insights létrehozása](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Ha hozzáadja a Time Series Insights környezetet ugyanahhoz az erőforráscsoporthoz, mint a megoldás-gyorsító, azt jelenti, hogy törli a megoldás-gyorsító törlését.

1. Kattintson a **Létrehozás** lehetőségre. A környezet létrehozása több percet is igénybe vehet.

## <a name="create-event-source"></a>Eseményforrás létrehozása

Hozzon létre egy új eseményforrás az IoT hubhoz való kapcsolódáshoz. Használja az előző lépésekben létrehozott fogyasztói csoportot. A Time Series Insights eseményforrás olyan dedikált fogyasztói csoportot igényel, amelyet más szolgáltatás nem használ.

1. A Azure Portal navigáljon az új idősorozat-környezethez.

1. A bal oldalon kattintson az **események forrása** elemre:

    ![Eseményforrás megtekintése](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Kattintson a **Hozzáadás** gombra:

    ![Eseményforrás hozzáadása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Az IoT hub új eseményforrásként való konfigurálásához használja az alábbi táblázatban szereplő értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Eseményforrás neve | A következő képernyőkép a **contoso-IOT-hub** nevet használja. A lépés elvégzéséhez használja a saját egyedi nevét. |
    | Forrás | **IoT Hub** |
    | Importálási beállítás | **IoT Hub használata az elérhető előfizetések közül** |
    | Előfizetés azonosítója | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | IoT Hub neve | **contoso – simulation7d894**. Használja az IoT hub nevét az eszköz szimulációs megoldásának gyorssegédje alapján. |
    | Iot Hub szabályzatneve | **iothubowner** |
    | IOT hub-házirend kulcsa | Ez a mező automatikusan fel van töltve. |
    | IoT Hub fogyasztói csoport | **devicesimulationtsi** |
    | Eseményszerializációs formátum | **JSON** |
    | Időbélyeg-tulajdonság neve | Hagyja üresen |

    ![Eseményforrás létrehozása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Kattintson a **Létrehozás** lehetőségre.

> [!NOTE]
> [További felhasználóknak is hozzáférést biztosíthat](../time-series-insights/concepts-access-policies.md#grant-data-access) a Time Series Insights Explorerrel.

## <a name="start-a-simulation"></a>Szimuláció indítása

A Time Series Insights Explorer használata előtt konfigurálja az eszköz-szimulációs megoldás-gyorsító eszközt a telemetria létrehozásához. A következő képernyőképen egy futó szimuláció látható 10 Chiller eszközzel:

![Eszköz szimulációjának futtatása](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

A Time Series Insights Explorer egy webalkalmazás, amelyet a telemetria megjelenítésére használhat.

1. A Azure Portal válassza a Time Series Insights **Áttekintés** lapot.

1. A Time Series Insights Explorer webalkalmazás megnyitásához kattintson az **Ugrás a** következőhöz: környezet:

    ![Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Az idő kiválasztása panelen válassza a gyors időpontok menüből az **elmúlt 30 perc** lehetőséget, majd kattintson a **Keresés** gombra:

    ![Time Series Insights Explorer-keresés](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. A bal oldali feltételek panelen válassza a **hőmérséklet** lehetőséget a **mérték** és a **iothub-Device-ID** értékként a **felosztás** értékeként:

    ![A "feltételek" panelt Time Series Insights megjelenítő képernyőkép a "mérték" és a "Split by" értékek kiemelésével.](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Kattintson a jobb gombbal a diagramra, és válassza az **események feltárása** lehetőséget:

    ![Time Series Insights Explorer eseményei](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Az esemény egy rácsban jelenik meg:

    ![Time Series Insights Explorer-táblázat](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Kattintson a perspektíva nézet gombra:

    ![Time Series Insights Explorer perspektívája](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kattintson ide **+** egy új lekérdezés perspektívához való hozzáadásához:

    ![Time Series Insights Explorer lekérdezés hozzáadása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Adja meg az **utolsó 30 percet** az időtartomány, a **páratartalom** , a **mérték**, valamint a **iothub-Device-ID** értékeként a **felosztás** érték szerint:

    ![Time Series Insights Explorer-lekérdezés](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. A perspektíva nézet gombra kattintva megtekintheti az eszköz telemetria irányítópultját:

    ![Time Series Insights Explorer irányítópult](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy további felfedezést szeretne, hagyja üzembe a megoldás-gyorsító telepítését.

Ha már nincs szüksége a megoldás-gyorssegédre, törölje azt a [kiépített megoldások](https://www.azureiotsolutions.com/Accelerators#dashboard) lapról, jelölje ki, majd kattintson a **megoldás törlése** elemre.

Ha a Time Series Insights környezetet a megoldás-gyorsító erőforráscsoporthoz adta hozzá, akkor a rendszer automatikusan törli a megoldás-gyorsító törlésekor. Ellenkező esetben manuálisan el kell távolítania a Time Series Insights környezetet a Azure Portalról.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Time Series Insights Explorer adatainak megismeréséről és lekérdezéséről, tekintse meg a [Azure Time Series Insights Explorert](../time-series-insights/time-series-insights-explorer.md).