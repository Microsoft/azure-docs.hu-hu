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
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017493"
---
Az Azure IoT Central-alkalmazásban a következőket teheti:

* Tekintse meg az eszköz által az **Áttekintés** lapon eljuttatott telemetria:

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Eszköztelemetria megtekintése":::

* Tekintse meg az eszköz tulajdonságait a **Névjegy** oldalon:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Eszköz tulajdonságainak megtekintése":::

## <a name="customize-the-device-template"></a>Sablon testreszabása

Megoldás fejlesztőként testreszabhatja a IoT Central automatikusan létrehozott sablont, amikor a termosztátos eszköz csatlakoztatva van.

Felhőbeli tulajdonság hozzáadása az eszközhöz társított ügyfél nevének tárolásához:

1. A IoT Central alkalmazásban navigáljon a **termosztát** eszköz sablonhoz az **eszközök sablonjai** oldalon.

1. A **termosztát** -eszköz sablonjában válassza a **felhő tulajdonságai** elemet.

1. Válassza a **Felhőbeli tulajdonság hozzáadása** lehetőséget. Adja meg az *ügyfél nevét* a **megjelenítendő név** mezőben, majd válassza a **karakterlánc** lehetőséget a **séma** elemnél. Kattintson a **Mentés** gombra.

Ha testre szeretné szabni, hogyan jelenjen meg az **Max-Min jelentés beolvasása** parancs a IoT Central alkalmazásban, válassza a **Testreszabás** lehetőséget az eszköz sablonjában. Cserélje le a **Get Max-Min jelentést.** az *Állapotjelentés beolvasása jelentéssel*. Kattintson a **Mentés** gombra.

A **termosztát** modell tartalmazza a **megcélzott hőmérséklet** írható tulajdonságot, az eszköz sablonja tartalmazza a Cloud Property **nevet** . Hozzon létre egy nézetet, amellyel a kezelő a következő tulajdonságokat szerkesztheti:

1. Válassza a **nézetek** lehetőséget, majd válassza ki az **eszköz és a Felhőbeli adatcsempe szerkesztését** .

1. Adja meg a _tulajdonságokat_ az űrlap neveként.

1. Válassza ki a **cél hőmérsékletét** és az **ügyfél neve** tulajdonságokat. Ezután válassza a **Hozzáadás szakaszt**.

1. Mentse a módosításokat.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Tulajdonságértékek frissítésének megtekintése":::

## <a name="publish-the-device-template"></a>Az eszközsablon közzététele

Mielőtt egy operátor megtekintheti és használni tudja a testreszabott beállításokat, közzé kell tennie az eszköz sablonját.

A **termosztát** -eszköz sablonjában válassza a **Közzététel** lehetőséget. Az **eszköz sablonjának közzététele az alkalmazás** panelen válassza a **Közzététel** lehetőséget.

Az operátor mostantól használhatja a **Tulajdonságok** nézetet a tulajdonságértékek frissítéséhez, és meghívhat egy **Get status Report** nevű parancsot az eszköz parancsai oldalon:

* Írható tulajdonságok értékének frissítése a **Tulajdonságok** lapon:

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Az eszköz tulajdonságainak frissítése":::

* Hívja meg a parancsokat a **parancsok** lapról:

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="A parancs hívása":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="A parancs válaszának megtekintése":::
