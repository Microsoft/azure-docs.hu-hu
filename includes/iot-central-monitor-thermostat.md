---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 77fdaf297fff0e145b1dd53908887bc14f9d3f14
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491141"
---
<!-- All needs updating -->
Az Azure IoT Central-alkalmazásban a következőket teheti:

* Tekintse meg a két termosztát-összetevő által eljuttatott telemetria az **Áttekintés** oldalon:

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Eszköztelemetria megtekintése":::

* Tekintse meg az eszköz tulajdonságait a **Névjegy** oldalon. Ezen a lapon láthatók a tulajdonságok az eszköz adatai összetevőből és a két termosztát-összetevőből:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Eszköz tulajdonságainak megtekintése":::

## <a name="customize-the-device-template"></a>Sablon testreszabása

Megoldás fejlesztőként testreszabhatja a IoT Central automatikusan létrehozott sablont, amikor a hőmérséklet-vezérlő eszköz csatlakoztatva van.

Felhőbeli tulajdonság hozzáadása az eszközhöz társított ügyfél nevének tárolásához:

1. A IoT Central alkalmazásban navigáljon a **hőmérséklet-vezérlő** eszköz sablonhoz az **eszközök sablonjai** oldalon.

1. A **hőmérséklet-vezérlő** eszköz sablonjában válassza a **felhő tulajdonságai** elemet.

1. Válassza a **Felhőbeli tulajdonság hozzáadása** lehetőséget. Adja meg az *ügyfél nevét* a **megjelenítendő név** mezőben, majd válassza a **karakterlánc** lehetőséget a **séma** elemnél. Kattintson a **Mentés** gombra.

A **Max-Min jelentés beolvasása** parancsok megjelenítésének testreszabása a IoT Central alkalmazásban:

1. Válassza a **Testreszabás** lehetőséget az eszköz sablonjában.

1. A **getMaxMinReport (thermostat1)** elemnél cserélje le a *Get Max-Min jelentést.* az *thermostat1 beolvasása jelentéssel*.

1. A **getMaxMinReport (thermostat2)** elemnél cserélje le a *Get Max-Min jelentést.* az *thermostat2 beolvasása jelentéssel*.

1. Kattintson a **Mentés** gombra.

Annak testreszabása, hogy a **cél hőmérsékletének** írható tulajdonságai hogyan jelenjenek meg a IoT Central alkalmazásban:

1. Válassza a **Testreszabás** lehetőséget az eszköz sablonjában.

1. A **targetTemperature (thermostat1)** esetében cserélje le a *célként megadott hőmérsékletet* a *célként megadott hőmérsékletre (1)*.

1. A **targetTemperature (thermostat2)** esetében cserélje le a *célként megadott hőmérsékletet* a *célként megadott hőmérsékletre (2)*.

1. Kattintson a **Mentés** gombra.

A **hőmérséklet-vezérlési** modellben található termosztát-összetevők közé tartozik a **célként megadott hőmérséklet** írható tulajdonsága, az eszköz sablonja tartalmazza a Cloud Property **nevet** . Hozzon létre egy nézetet, amellyel a kezelő a következő tulajdonságokat szerkesztheti:

1. Válassza a **nézetek** lehetőséget, majd válassza ki az **eszköz és a Felhőbeli adatcsempe szerkesztését** .

1. Adja meg a _tulajdonságokat_ az űrlap neveként.

1. Válassza ki a **célként megadott hőmérsékletet (1)**, a  **célként megadott hőmérsékletet (2)** és az **ügyfél neve** tulajdonságokat. Ezután válassza a **Hozzáadás szakaszt**.

1. Mentse a módosításokat.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Tulajdonságértékek frissítésének megtekintése":::

## <a name="publish-the-device-template"></a>Az eszközsablon közzététele

Mielőtt egy operátor megtekintheti és használni tudja a testreszabott beállításokat, közzé kell tennie az eszköz sablonját.

A **termosztát** -eszköz sablonjában válassza a **Közzététel** lehetőséget. Az **eszköz sablonjának közzététele az alkalmazás** panelen válassza a **Közzététel** lehetőséget.

Az operátor mostantól a **Tulajdonságok** nézettel frissítheti a tulajdonságértékeket, és meghívhatja az **thermostat1-jelentés beolvasása** és a **thermostat2 állapotjelentés beolvasása** az eszköz parancsai oldalon:

* Írható tulajdonságok értékének frissítése a **Tulajdonságok** lapon:

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Az eszköz tulajdonságainak frissítése":::

* Hívja meg a parancsokat a **parancsok** lapról:

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="A parancs hívása":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="A parancs válaszának megtekintése":::
