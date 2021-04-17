---
title: Rövid útmutató – Szabályok és műveletek konfigurálása a Azure IoT Central
description: Ez a rövid útmutató bemutatja szerkesztőként, hogyan konfigurálhatja a telemetriaalapú szabályokat és műveleteket az Azure IoT Central alkalmazásban.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: f65614de97e8ff6eed732e624ae30c3f2b70bd60
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589006"
---
# <a name="quickstart-configure-rules-and-actions-for-your-device-in-azure-iot-central"></a>Rövid útmutató: Az eszközre vonatkozó szabályok és műveletek konfigurálása az Azure IoT Centralban

Ebben a rövid útmutatóban egy olyan szabályt hoz létre, amely e-mailt küld, ha az eszközérzékelő által jelentett páratartalom meghaladja az 55%-ot.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, el kell kezdenie a két korábbi rövid útmutatót: [Create an Azure IoT Central application](./quick-deploy-iot-central.md) (Azure IoT Central-alkalmazás létrehozása) és Add a simulated device to your IoT Central application (Szimulált eszköz hozzáadása az IoT Central-alkalmazáshoz) a **Sensor Controller** eszközsablon létrehozásához. [](./quick-create-simulated-device.md)

## <a name="create-a-telemetry-based-rule"></a>Telemetria-alapú szabály létrehozása

1. Ha új telemetria-alapú szabályt szeretne hozzáadni az alkalmazáshoz, a bal oldali panelen válassza a Szabályok **lehetőséget.**

1. Új szabály létrehozásához válassza az **+ Új lehetőséget.**

1. Adja **meg a Környezeti páratartalom** nevet a szabály neveként.

1. A **Céleszközök szakaszban** válassza az **Érzékelővezérlő lehetőséget** eszközsablonként. Ez a beállítás az eszközsablon típusa alapján szűri a szabály által érvényes eszközöket. A + Szűrő lehetőség kiválasztásával további szűrési **feltételeket adhat hozzá.**

1. A Feltételek **szakaszban** adhatja meg, hogy mi aktiválja a szabályt. Az alábbi információk segítségével határozhat meg hőmérsékleti telemetria alapján feltételt:

    | Mező        | Érték            |
    | ------------ | ---------------- |
    | Mérés  | SensorHumid      |
    | Operátor     | nagyobb, mint  |
    | Érték        | 55               |

    További feltételek hozzáadásához válassza a **+ Feltétel lehetőséget.**

    :::image type="content" source="media/quick-configure-rules/condition.png" alt-text="Képernyőkép a szabály feltételről":::

1. A szabály aktiválódása esetén futtatott e-mail-művelet hozzáadásához válassza az **+ E-mail lehetőséget.**

1. A művelet meghatározásához használja az alábbi táblázatban található információkat, majd válassza a Kész **lehetőséget:**

    | Beállítás   | Érték                                             |
    | --------- | ------------------------------------------------- |
    | Megjelenített név | Kezelő e-mail-művelete                          |
    | Művelet        | Az Ön e-mail-címe                                |
    | Jegyzetek     | A környezeti páratartalom túllépte a küszöbértéket. |

    > [!NOTE]
    > Ha e-mailes értesítést szeretne kapni, az e-mail-címnek [az alkalmazásban lévő felhasználói azonosítónak](howto-administer.md) kell lennie, és ennek a felhasználónak be kellett jelentkeznie legalább egyszer az alkalmazásba.

    :::image type="content" source="media/quick-configure-rules/action.png" alt-text="A szabályhoz adott e-mail-műveletet bemutató képernyőkép":::

1. Kattintson a **Mentés** gombra. A szabály a megjelenik a **Szabályok** oldalon.

## <a name="test-the-rule"></a>A szabály tesztelése

A szabály a mentése után nem sokkal érvénybe lép. Ha a szabályban meghatározott feltételek teljesülnek, az alkalmazás e-mailt küld a műveletben megadott címre.

> [!NOTE]
> A tesztelés befejezése után kapcsolja ki a szabályt, így nem kap riasztásokat a beérkezett üzenetekben.

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Telemetria-alapú szabály létrehozása
* Művelet hozzáadása

Az alkalmazáshoz csatlakoztatott eszközök figyelésével kapcsolatos további információkért folytassa a rövid útmutatóval:

> [!div class="nextstepaction"]
> [A Azure IoT Central az eszközök figyelése.](quick-monitor-devices.md)
