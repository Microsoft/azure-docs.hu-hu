---
title: Szimulált telemetria vizualizációja Time Series Insights használatával – Azure | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a Time Series Insights az eszközszimulációs megoldásgyorsító által generált telemetriai adatok feltárására és elemzésére.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 90a4b808daccc76e8cc9125973c69b13e8086fbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713965"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Az Time Series Insights az eszközszimulációs megoldásgyorsítóból küldött telemetria vizualizációja a Time Series Insights használatával

Az eszközszimulációs megoldásgyorsítóval telemetriát hozhat létre szimulált eszközökből az IoT-megoldások teszteléséhez. Ez az útmutató bemutatja, hogyan vizualizálhatja és elemezheti a szimulált telemetriát egy Time Series Insights környezetben.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseit egy aktív Azure-előfizetéssel kell követnie. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Az útmutató lépései azt feltételezik, hogy üzembe helyezett egy eszközszimulációs megoldásgyorsítót az Azure-előfizetésében. Ha még nem telepítette az Eszközszimulációt, tekintse meg az [](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) eszközszimuláció üzembe helyezését a GitHubon.

Ez a cikk feltételezi, hogy a megoldásgyorsító neve **contoso-simulation.** A következő lépések során cserélje le a **contoso-simulation** helyére a megoldásgyorsító nevét.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Fogyasztói csoport létrehozása

Létre kell hoznia egy dedikált fogyasztói csoportot az IoT Hubban, hogy telemetriát streamelhet a Time Series Insights. Az eseményforrásnak Time Series Insights kizárólagos felhasználói csoporttal kell IoT Hub használnia.

A következő lépések az Azure CLI használatával Azure Cloud Shell a fogyasztói csoport létrehozásához:

1. Az IoT Hub az eszközszimulációs megoldásgyorsító üzembe helyezésekor létrehozott számos erőforrás egyike. Hajtsa végre a következő parancsot, és keresse meg az IoT Hub nevét. Ne felejtse el a megoldásgyorsító nevét használni:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Az IoT Hub a **Microsoft.Devices/IotHubs típusú erőforrás.**

1. Adjon hozzá egy **devicesimulationtsi** nevű fogyasztói csoportot a központhoz. A következő parancsban használja a központ és a megoldásgyorsító nevét:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Most már bezárhatja a Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Új új Time Series Insights létrehozása

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) egy teljes körűen felügyelt elemzési, tárolási és vizualizációs szolgáltatás, amely IoT-léptékű idősorozat-adatok kezelésére használható a felhőben. Új új Time Series Insights létrehozása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Válassza **az Erőforráscsoport**  >  **létrehozása eszközök internetes hálózata**  >  **Time Series Insights:**

    ![Új Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. A Time Series Insights a megoldásgyorsítóval azonos erőforráscsoportban való létrehozásához használja az alábbi táblázatban található értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Környezet neve | Az alábbi képernyőképen a **Contoso-TSI nevet használjuk.** A lépés befejezésekor válassza ki a saját egyedi nevét. |
    | Előfizetés | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | Erőforráscsoport | **contoso-simulation**. Használja a megoldásgyorsító nevét. |
    | Hely | Ez a példa az USA **keleti régiója régiót használja.** Hozza létre a környezetet az eszközszimulációs gyorsítóval azonos régióban. |
    | SKU |**S1** |
    | Kapacitás | **1** |

    ![A Time Series Insights létrehozása](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Ha a Time Series Insights a megoldásgyorsítóval azonos erőforráscsoporthoz adja hozzá, az a megoldásgyorsító törlésekor törlődik.

1. Kattintson a **Létrehozás** lehetőségre. A környezet létrehozása eltarthat néhány percig.

## <a name="create-event-source"></a>Eseményforrás létrehozása

Hozzon létre egy új eseményforrást az IoT Hubhoz való csatlakozáshoz. Használja az előző lépésekben létrehozott fogyasztói csoportot. A Time Series Insights eseményforráshoz olyan dedikált fogyasztói csoportra van szükség, amelyet egy másik szolgáltatás nem használ.

1. A Azure Portal keresse meg az új Time Series Environment-környezetet.

1. A bal oldalon kattintson az **Eseményforrások elemre:**

    ![Eseményforrások megtekintése](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Kattintson a **Hozzáadás gombra:**

    ![Eseményforrás hozzáadása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Az IoT Hub új eseményforrásként való konfigurálához használja az alábbi táblázatban található értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Eseményforrás neve | Az alábbi képernyőképen a **contoso-iot-hub nevet használjuk.** A lépés befejezésekor használja a saját egyedi nevét. |
    | Forrás | **IoT Hub** |
    | Importálási beállítás | **Az IoT Hub előfizetésből származó adatok használata** |
    | Előfizetés azonosítója | Válassza ki saját Azure-előfizetését a legördülő menüből. |
    | IoT Hub neve | **contoso-simulation7d894**. Használja az IoT Hub nevét az eszközszimulációs megoldásgyorsítóból. |
    | Iot Hub szabályzatneve | **iothubowner** |
    | Az Iot Hub szabályzatkulcsa | Ez a mező automatikusan ki lesz töltve. |
    | IoT Hub fogyasztói csoport | **devicesimulationtsi** |
    | Eseményszerializációs formátum | **JSON** |
    | Időbélyeg-tulajdonság neve | Hagyja üresen |

    ![Eseményforrás létrehozása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Kattintson a **Létrehozás** lehetőségre.

> [!NOTE]
> További felhasználóknak [adhat hozzáférést a](../time-series-insights/concepts-access-policies.md#grant-data-access) Time Series Insights explorerhez.

## <a name="start-a-simulation"></a>Szimuláció elindítani

A Time Series Insights előtt konfigurálja az eszközszimulációs megoldásgyorsítót telemetria generálása érdekében. Az alábbi képernyőkép egy futó szimulációt mutat be 10 hűtő eszközzel:

![Eszközszimuláció futtatása](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

A Time Series Insights Explorer egy webalkalmazás, amely a telemetria megjelenítésére használható.

1. A Azure Portal válassza az Áttekintés Time Series Insights **lapot.**

1. A Time Series Insights Explorer webalkalmazás megnyitásához kattintson a Go to Environment (Ugrás a **környezetbe) elemre:**

    ![Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Az Időválasztás panelen válassza az Utolsó **30 perc** lehetőséget a gyorsidők menüből, majd kattintson a **Keresés elemre:**

    ![Time Series Insights Keresési ablak](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. A bal oldali terms panelen mértékként válassza a **hőmérsékletet,** felosztási értékként pedig az **iothub-connection-device-id** **értéket:** 

    ![Képernyőkép a "Time Series Insights" panelről, kiemelt "Mérték" és "Felosztási alap" értékekkel.](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Kattintson a jobb gombbal a diagramra, és válassza az **Események megismerése lehetőséget:**

    ![Time Series Insights Explorer-események](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Az eseményadatok egy rácsban megjelenik:

    ![Time Series Insights Explorer táblázata](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Kattintson a perspektívanézet gombra:

    ![Time Series Insights Explorer perspektívája](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kattintson ide **+** egy új lekérdezés perspektívához való hozzáadásához:

    ![Time Series Insights Explorer Lekérdezés hozzáadása](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Válassza **az Elmúlt 30 perc** lehetőséget időtartományként,  a Páratartalom értéket mértékként, az **iothub-connection-device-id** értéket pedig a **Felosztási idő értékeként:** 

    ![Time Series Insights Explorer lekérdezése](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Kattintson a perspektívanézet gombra az eszköz telemetriai irányítópultjának megtekintéséhez:

    ![Time Series Insights Explorer irányítópultja](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy tovább ismerkedik, hagyja üzembe helyezni a megoldásgyorsítót.

Ha már nincs szüksége a megoldásgyorsítóra, törölje azt a Kiépített megoldások lapról, jelölje ki, majd kattintson a **Megoldás törlése parancsra.** [](https://www.azureiotsolutions.com/Accelerators#dashboard)

Ha hozzáadta a Time Series Insights környezetet a megoldásgyorsító erőforráscsoporthoz, a rendszer automatikusan törli azt a megoldásgyorsító törlésekor. Ellenkező esetben manuálisan kell eltávolítania a Time Series Insights környezetet a Azure Portal.

## <a name="next-steps"></a>Következő lépések

További információ az adatok feltárásról és lekérdezésről a Time Series Insights Explorerben: [Azure Time Series Insights Explorer.](../time-series-insights/time-series-insights-explorer.md)