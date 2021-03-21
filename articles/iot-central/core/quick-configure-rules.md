---
title: Gyors útmutató – szabályok és műveletek konfigurálása az Azure IoT Central
description: Ez a rövid útmutató bemutatja, hogyan konfigurálhatja a telemetria szabályokat és műveleteket az Azure IoT Central alkalmazásban.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 90fc1385afb2ef921828465ba030674281e96ebf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99833847"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Rövid útmutató: Az eszközre vonatkozó szabályok és műveletek konfigurálása az Azure IoT Centralban

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Ebben a rövid útmutatóban egy olyan szabályt hoz létre, amely e-mailt küld, ha az eszköz érzékelője által jelentett páratartalom meghaladja a 55%-ot.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, hajtsa végre a két előző rövid útmutatót, és [hozzon létre egy Azure IoT Central alkalmazást](./quick-deploy-iot-central.md) , és [adjon hozzá egy szimulált eszközt a IoT Central alkalmazáshoz](./quick-create-simulated-device.md) , és hozzon létre egy **érzékelő vezérlőt** az eszközhöz való együttműködéshez.

## <a name="create-a-telemetry-based-rule"></a>Telemetria-alapú szabály létrehozása

1. Új telemetria-szabály az alkalmazáshoz való hozzáadásához a bal oldali ablaktáblán válassza a **szabályok** elemet.

1. Új szabály létrehozásához válassza az **+ új** lehetőséget.

1. Adja meg a **környezeti páratartalom** nevet a szabály neveként.

1. A **cél eszközök** szakaszban válassza ki az **érzékelő vezérlőt** eszköz sablonként. Ez a beállítás szűri azokat az eszközöket, amelyekre a szabály vonatkozik. A **+ szűrő** lehetőség kiválasztásával további szűrési feltételeket adhat hozzá.

1. A **feltételek** szakaszban megadhatja, hogy mi indítja el a szabályt. A következő információk segítségével határozhat meg egy olyan feltételt, amely a hőmérséklet telemetria:

    | Mező        | Érték            |
    | ------------ | ---------------- |
    | Mérés  | SensorHumid      |
    | Operátor     | nagyobb, mint  |
    | Érték        | 55               |

    További feltételek hozzáadásához válassza a **+ feltétel** lehetőséget.

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="A szabály feltételét bemutató képernyőfelvétel":::

1. A szabály indításakor futtatandó e-mail-művelet hozzáadásához válassza a **+ e-mail** lehetőséget.

1. A következő táblázatban található információk segítségével határozza meg a műveletet, majd válassza a **kész** lehetőséget:

    | Beállítás   | Érték                                             |
    | --------- | ------------------------------------------------- |
    | Megjelenített név | Kezelői e-mail művelet                          |
    | Művelet        | Az Ön e-mail-címe                                |
    | Jegyzetek     | A környezeti páratartalom túllépte a küszöbértéket. |

    > [!NOTE]
    > Ha e-mailes értesítést szeretne kapni, az e-mail-címnek [az alkalmazásban lévő felhasználói azonosítónak](howto-administer.md) kell lennie, és ennek a felhasználónak be kellett jelentkeznie legalább egyszer az alkalmazásba.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="A szabályhoz hozzáadott e-mail-műveletet megjelenítő képernyőkép":::

1. Kattintson a **Mentés** gombra. A szabály a megjelenik a **Szabályok** oldalon.

## <a name="test-the-rule"></a>A szabály tesztelése

A szabály a mentése után nem sokkal érvénybe lép. Ha a szabályban meghatározott feltételek teljesülnek, az alkalmazás e-mailt küld a műveletben megadott címre.

> [!NOTE]
> A tesztelés befejezése után kapcsolja ki a szabályt, hogy ne kapjon riasztásokat a Beérkezett üzenetek mappájában.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Telemetria-alapú szabály létrehozása
* Művelet hozzáadása

Ha többet szeretne megtudni az alkalmazáshoz csatlakoztatott eszközök monitorozásáról, folytassa a gyors üzembe helyezéssel:

> [!div class="nextstepaction"]
> Az [Azure IoT Central segítségével figyelheti az eszközöket](quick-monitor-devices.md).
