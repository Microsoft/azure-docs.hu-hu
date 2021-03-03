---
title: 'Rövid útmutató: a Gen2 bemutató környezet megismerése – Azure Time Series Insights Gen2 | Microsoft Docs'
description: Ismerkedjen meg a Azure Time Series Insights Gen2 bemutató környezet főbb funkcióival.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 03/01/2021
ms.openlocfilehash: 25340af243217313ce6d3f7205c102d4bcd350b4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698110"
---
# <a name="quickstart-explore-the-azure-time-series-insights-gen2-demo-environment"></a>Gyorsútmutató: Ismerkedés az Azure Time Series Insights Gen2 bemutatókörnyezettel

Ez a rövid útmutató egy Azure Time Series Insights Gen2-környezettel kapcsolatos első lépéseket mutat be. Az ingyenes bemutatóban megtekintheti az Azure Time Series Insights Gen2 felvett főbb funkciókat.

A Azure Time Series Insights Gen2 bemutató környezet a contoso egy olyan forgatókönyvét tartalmazza, amely két szélerőmű-farmon működik. Minden Farm 10 turbinával rendelkezik. Minden turbina 20 érzékelővel rendelkezik, amely percenként jelentést készít az Azure IoT Hub. Az érzékelők információt gyűjtenek az időjárási feltételekről, a panel szurok és a kitérés pozícióról. A rendszer rögzíti a generátorok teljesítményéről, a sebességváltó működéséről és a biztonsági figyelőről szóló információkat is.

Ebből a rövid útmutatóból megtudhatja, hogyan használhatók a Azure Time Series Insights Gen2, hogy gyakorlatban hasznosítható információkat találjanak a contoso-információkban. Emellett rövid kiváltó okokat is elvégezhet, hogy jobban megjósolja a kritikus hibákat, és hogy elvégezze a karbantartást.

> [!IMPORTANT]
> Ha még nem rendelkezik ilyennel, hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)   .

## <a name="explore-the-azure-time-series-insights-gen2-explorer-in-a-demo-environment"></a>Ismerkedés a Azure Time Series Insights Gen2 Explorer bemutató környezetében

A Azure Time Series Insights Gen2 Explorer az előzmények és a kiváltó okok elemzését mutatja be. Első lépések:

1. Nyissa meg a [contoso szélerőműpark bemutató](https://insights.timeseries.azure.com/preview/samples) környezetét.  

1. Ha a rendszer kéri, jelentkezzen be az Azure Time Series Insights Gen2 Explorer alkalmazásba az Azure-fiók hitelesítő adataival.

## <a name="work-with-historical-data"></a>Korábbi adatmennyiségek használata

1. Válassza a **contoso WindFarm-hierarchia** lehetőséget.

   [![Contoso WindFarm-hierarchia](media/quickstart-explore/quick-start-contoso-1.png)](media/quickstart-explore/quick-start-contoso-1.png#lightbox)

1. A **contoso-üzem 1**. részében tekintse meg a Wind gázturbinás **W7** című részt.

   [![W7 a contoso üzem 1](media/quickstart-explore/quick-start-contoso-2.png)](media/quickstart-explore/quick-start-contoso-2.png#lightbox)

   1. Módosítsa a nézet tartományát **1/1/17 20:00:00.00 – 3/10/17 20:00:00.00 (UTC) értékre**.

      [![Tartomány nézet](media/quickstart-explore/range-setting-1.png)](media/quickstart-explore/range-setting-1.png#lightbox)

      [![Tartomány nézet beállítása](media/quickstart-explore/range-setting-2.png)](media/quickstart-explore/range-setting-2.png#lightbox)

   1. Állítsa be a tartomány nézetet a nagyítás **(+)** és a kimenő **(-)** és a görgetősáv mozgatása segítségével.

      [![Tartomány nézetének módosítása](media/quickstart-explore/view-range-setting.png)](media/quickstart-explore/view-range-setting.png#lightbox)

   1. Ha ki szeretne választani egy érzékelőt, válassza ki a **contoso Plant 1**  >  .**W7**-  >  **generátor rendszer**  >  **GeneratorSpeed**. Ezután tekintse át a megjelenített értékeket.

      [![Generátor sebessége](media/quickstart-explore/quick-start-generator-speed-1.png)](media/quickstart-explore/quick-start-generator-speed-1.png#lightbox)

1. Az utóbbi időben a contoso tüzet észlelt a Wind Turbine **W7**-ben. A vélemények eltérőek lehetnek a tüzet kiváltó okok miatt. Azure Time Series Insights Gen2 a Fire riasztási érzékelő, amely a tűz alatt aktiválódik, megjelenik.

   1. Módosítsa a nézet tartományát **3/9/17 20:00:00.00 – 3/10/17 20:00:00.00 (UTC) értékre**.
   1. Válassza a **biztonsági rendszerek**  >  **FireAlert** lehetőséget.

      [![A contoso tüzet észlelt a Wind gázturbinás W7-ben](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Tekintse át a további eseményeket a tűz idején, hogy megértse, mi történt. Az olajnyomás és az aktív figyelmeztetések közvetlenül a tűz előtt vannak megszegve.

   1. Válassza a **pitch System**  >  **HydraulicOilPressure** elemet.
   1. Válassza a **pitch System**  >  **ActiveWarning** elemet.

      [![Tekintse át a többi eseményt egy időben](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Az olajnyomás és az aktív figyelmeztetési érzékelők közvetlenül a tűz előtt vannak megszegve. A megjelenített idősorozat kibontásával áttekintheti a tüzet eredményező más jeleket. Mindkét érzékelő következetesen ingadozik az idő múlásával. Az ingadozások állandó és aggasztó mintát jeleznek.

    * Módosítsa a nézet tartományát **2/24/17 20:00:00.00 – 3/10/17 20:00:00.00 (UTC) értékre**.

      [![Olajnyomás és aktív figyelmeztetési érzékelők is](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. A két éves korábbi adatmennyiség vizsgálatával egy másik, az érzékelővel megegyező állapotú esemény is kiderül.

    * Módosítsa a nézet tartományát **1/1/16-12/31/17 értékre** (az összes adatértékre).

      [![Korábbi minták megkeresése](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

A Azure Time Series Insights Gen2 és az érzékelő telemetria használatával felderítjük a régi adatokat tartalmazó hosszú távú trendet. Az új ismeretekkel a következőket teheti:

* Magyarázza el, mi történt valójában.
* Javítsa ki a problémát.
* Helyezzen üzembe jobb riasztási értesítési rendszereket.

## <a name="root-cause-analysis"></a>Kiváltó okok elemzése

1. Egyes forgatókönyvek kifinomult elemzést igényelnek az adatokban található nyomok feltárásához. Válassza ki a szélmalom **W6** a **6/25**. időpontban.

    1. Módosítsa a nézet tartományát **6/1/17 20:00:00.00 – 7/1/17 20:00:00.00 (UTC) értékre**.
    1. Válassza a **contoso növény 1**  >  **W6**  >  **biztonsági rendszerek**  >  **VoltageActuatorSwitchWarning** lehetőséget.

       [![A nézet tartományának módosítása és a W6 kiválasztása](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. A figyelmeztetés a generátortól kapott feszültséggel kapcsolatos hibát jelez. A generátor teljes teljesítménye az aktuális intervallumban található normál paramétereken belül van. Az intervallum növelésével egy másik minta jön létre. A legördülő lista egyértelmű.

    1. Távolítsa el a **VoltageActuatorSwitchWarning** -érzékelőt.
    1. Válassza ki a **Generator System**  >  **ActivePower** elemet.
    1. Módosítsa az intervallumot a **3D** értékre.

       [![Az intervallum módosítása a 3D értékre](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Az időtartomány kibővítésével meghatározhatja, hogy a probléma leállt-e, vagy folytatja-e a problémát.

    * Az időtartam meghosszabbítása 60 napra.

      [![Az időtartam meghosszabbítása 60 napra](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Más szenzor-adatpontok is hozzáadhatók a nagyobb kontextus érdekében. Minél több érzékelők is megtekinthetők, a probléma megismertetését. A tényleges értékek megjelenítéséhez dobjunk egy jelölőt.

    1. Válassza a **Generátorrendszer** lehetőséget, majd válassza ki a három érzékelőt: **GridVoltagePhase1**, **GridVoltagePhase2** és **GridVoltagePhase3**.
    1. Dobjon egy jelölőt a látható terület utolsó adatpontjára.

       [![Jelölő eldobása](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    A feszültség-érzékelők közül kettő egyformán működik, a normál paramétereken belül. Úgy tűnik, a **GridVoltagePhase3** -érzékelő a bűnös.

1. A fokozottan környezetfüggő adatbevitelsel a 3. fázis kidobása még ennél a problémánál is megjelenik. Most már jó a figyelmeztetés oka. Készen áll arra, hogy a problémát a karbantartási csapatunk is felhasználja.  

    * Módosítsa a megjelenítést úgy, hogy az összes **generátor** rendszerérzékelője ugyanazon a diagramon legyen.

      [![A Megjelenítés módosítása a következőre: mindent tartalmaz](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="next-steps"></a>Következő lépések

Készen áll saját Azure Time Series Insights Gen2-környezet létrehozására. Kezdés:

> [!div class="nextstepaction"]
> [A Azure Time Series Insights Gen2-környezet megtervezése](./how-to-plan-your-environment.md)

Ismerje meg a bemutató és funkcióinak használatát:

> [!div class="nextstepaction"]
> [A Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md)
