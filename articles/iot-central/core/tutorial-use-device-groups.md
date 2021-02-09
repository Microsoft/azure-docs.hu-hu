---
title: Oktatóanyag – erőforráscsoportok használata az Azure IoT Central alkalmazásban | Microsoft Docs
description: Oktatóanyag – kezelőként megtudhatja, hogyan használhatja az telemetria az Azure IoT Central-alkalmazás eszközeiből származó eszközök elemzéséhez.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a7d26eebb24662a448d8ccb44d037e7706fe776b
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832844"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Oktatóanyag: eszköz-csoportok használata az eszközök telemetria elemzéséhez

Ez a cikk azt ismerteti, hogyan használhatók az eszközök a telemetria az Azure IoT Central-alkalmazásban található eszközökhöz.

Az eszközcsoport azoknak az eszközöknek a listája, amelyek egy csoportba vannak csoportosítva, mert megfelelnek néhány megadott feltételnek. Az eszközcsoport megkönnyíti az eszközök méretezését, megjelenítését és elemzését az eszközök kisebb és logikai csoportokba csoportosításával. Létrehozhat például egy eszközt a Seattle-ben lévő összes légkondicionáló eszköz listázásához, hogy a technikus megkeresse azokat az eszközöket, amelyekhez felelősek.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszközcsoport létrehozása
> * Eszköz-csoport használata az eszközök telemetria elemzéséhez

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, hajtsa végre az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md) és [egy szimulált eszköz hozzáadása a IoT Central alkalmazás](./quick-create-simulated-device.md) -gyors útmutatóhoz az **érzékelő vezérlő** -eszköz sablonjának létrehozásához.

## <a name="create-simulated-devices"></a>Szimulált eszközök létrehozása

Mielőtt létrehoz egy eszközcsoport-csoportot, adjon hozzá legalább öt szimulált eszközt az oktatóanyagban használni kívánt **Sensor Controller** -sablon alapján:


:::image type="content" source="media/tutorial-use-device-groups/simulated-devices.png" alt-text="Képernyőfelvétel, amely öt szimulált érzékelő vezérlő eszközt mutat":::

Négy szimulált érzékelő eszköz esetében használja az **eszköz kezelése** nézetet az ügyfél nevének a *contoso*-ba történő beállításához:

:::image type="content" source="media/tutorial-use-device-groups/customer-name.png" alt-text="Képernyőkép, amely bemutatja, hogyan állíthatja be az ügyfél neve Cloud Property":::

## <a name="create-a-device-group"></a>Eszközcsoport létrehozása

Eszközcsoport létrehozása:

1. Válassza ki az eszközcsoport **elemet a bal** oldali ablaktáblán.

1. Válassza a **+ Új** lehetőséget.

1. Nevezze el az eszköz csoport *contoso-eszközeit*. Leírást is hozzáadhat a szolgáltatáshoz. Az eszközcsoport csak egyetlen eszköz sablonból származó eszközöket tartalmazhat. Válassza ki a csoporthoz használni kívánt **Sensor Controller** -sablont.

1. Ha úgy szeretné testre szabni az erőforráscsoportot, hogy csak a **contosohoz** tartozó eszközöket tartalmazza, válassza a **+ szűrő** lehetőséget. Válassza ki az **ügyfél neve** tulajdonságot, az **Equals** összehasonlító operátort és a **contoso** értéket. Több szűrőt és eszközt is hozzáadhat, amelyek megfelelnek az **összes** szűrési feltételnek az eszköz csoportban. Az Ön által létrehozott eszközcsoport mindenki számára elérhető, aki hozzáfér az alkalmazáshoz, így bárki megtekintheti, módosíthatja vagy törölheti az eszközt.

    > [!TIP]
    > Az eszközcsoport dinamikus lekérdezés. Minden alkalommal, amikor megtekinti az eszközök listáját, lehet, hogy a listában különböző eszközök szerepelnek. A lista attól függ, hogy mely eszközök felelnek meg a lekérdezés feltételeinek.

1. Válassza a **Mentés** elemet.

:::image type="content" source="media/tutorial-use-device-groups/device-group-query.png" alt-text="Az eszközcsoport lekérdezési konfigurációját megjelenítő képernyőkép":::

> [!NOTE]
> Azure IoT Edge eszközök esetében válassza ki Azure IoT Edge sablonokat az eszközcsoport létrehozásához.

## <a name="analytics"></a>Elemzés

Az **elemzési** eszköz segítségével a csoport eszközeiből elemezheti a telemetria. Például a contoso környezeti érzékelők által jelentett átlagos hőmérsékletet ábrázolhatja.

Eszközcsoport telemetria elemzése:

1. Válassza az **elemzés** lehetőséget a bal oldali ablaktáblán.

1. Válassza ki a **contoso-eszközök** létrehozott csoportját. Ezután adja hozzá a **hőmérséklet** és a **páratartalom** telemetria típusát:

    :::image type="content" source="media/tutorial-use-device-groups/create-analysis.png" alt-text="Az elemzéshez kiválasztott telemetria-típusokat megjelenítő képernyőkép":::

    Az összesítési típus kiválasztásához használja a telemetria-típusok melletti fogaskerék ikonokat. Az alapértelmezett érték az **átlag**. A **Group By** paranccsal módosíthatja az összesített adatokat. Ha például az eszköz AZONOSÍTÓját választja, akkor az **elemzés** lehetőség kiválasztásakor megjelenik az egyes eszközökre vonatkozó ábra.

1. Az átlagos telemetria-értékek megtekintéséhez válassza az **elemzés** lehetőséget:

    :::image type="content" source="media/tutorial-use-device-groups/view-analysis.png" alt-text="Az összes contoso-eszköz átlagos értékeit megjelenítő képernyőkép":::

    Testreszabhatja a nézetet, megváltoztathatja a megjelenített időszakot, és exportálhatja az adategységeket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja az eszközök csoportjait az Azure IoT Central-alkalmazásban, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Telemetria-szabályok létrehozása](tutorial-create-telemetry-rules.md)
