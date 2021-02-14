---
title: Azure IoT Central-irányítópultok létrehozása | Microsoft Docs
description: Ismerje meg, hogyan hozhatja létre és kezelheti az irányítópultokat.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 6fc99470fdc52a2dc6553056f305226f8348550c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366721"
---
# <a name="create-and-manage-multiple-dashboards"></a>Több irányítópult létrehozása és kezelése

Az **irányítópult** az az oldal, amely az alkalmazás első megnyitásakor betöltődik. Az alkalmazás egyik **szerkesztője** határozza meg az alapértelmezett alkalmazás-irányítópultot az összes felhasználó számára. Emellett létrehozhat saját, személyre szabott alkalmazás-irányítópultot is. Több irányítópultot is beállíthat, amelyek különböző adathalmazokat jelenítenek meg, és válthatnak egymás között.

Ha Ön az alkalmazás **rendszergazdája** , létrehozhat akár 10 alkalmazás szintű irányítópultot is, amelyekkel megoszthatja az alkalmazás más felhasználóit. Csak **rendszergazdák** hozhatnak létre, szerkeszthetnek és törölhetnek alkalmazás szintű irányítópultokat.  

## <a name="create-dashboard"></a>Irányítópult létrehozása

Az alábbi képernyőfelvételen az **egyéni alkalmazás** sablonjában létrehozott alkalmazás irányítópultja látható. Az alapértelmezett alkalmazás-irányítópultot lecserélheti személyes irányítópultra, vagy ha Ön rendszergazda, egy másik alkalmazás szintű irányítópult. Ehhez az oldal bal felső részén válassza az **+ új** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Az "egyéni alkalmazás" sablonon alapuló alkalmazások irányítópultja](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Az **+ új** lehetőség kiválasztásával megnyílik az irányítópult-szerkesztő. A szerkesztőben megadhatja az irányítópult nevét, és a könyvtárból is választhat elemeket. A függvénytár tartalmazza az irányítópult testreszabásához használható csempéket és irányítópult-primitíveket.

> [!div class="mx-imgBorder"]
> ![Irányítópult-könyvtár](media/howto-create-personal-dashboards/dashboard-library.png)

Ha Ön az alkalmazás **rendszergazdája** , akkor lehetősége van arra, hogy létrehoz egy személyes szintű irányítópultot vagy egy alkalmazás szintű irányítópultot. Ha létrehoz egy személyes szintű irányítópultot, akkor csak megtekintheti. Ha létrehoz egy alkalmazás szintű irányítópultot, az alkalmazás minden felhasználója láthatja. Miután megadta a címet, és kiválasztja a létrehozni kívánt irányítópult típusát, később is mentheti és hozzáadhatja a csempéket. Ha már készen áll, és hozzáadta az eszköz sablonját és az eszköz példányát, akkor az első csempe is létrehozható.  

> [!div class="mx-imgBorder"]
> ![Az eszköz részleteinek konfigurálása a hőmérséklettel kapcsolatos részletekkel](media/howto-create-personal-dashboards/device-details.png)

Hozzáadhat például egy **telemetria** csempét az eszköz aktuális hőmérsékletéhez. Ehhez tegye a következőket:

1. Válassza ki az **eszköz sablonját**
1. Válasszon ki egy eszközt az **eszközök** közül az irányítópult csempén megjeleníteni kívánt eszközhöz. Ezután megjelenik az eszköz azon tulajdonságainak listája, amelyeket a csempén lehet használni.
1. Ha a csempét az irányítópulton szeretné létrehozni, válassza a **hőmérséklet** lehetőséget, és húzza azt az irányítópult-területen. Jelölje be a **hőmérséklet** melletti jelölőnégyzetet, majd kattintson a **csempe hozzáadása** lehetőségre. Az alábbi képernyőfelvételen egy eszköz sablonjának és eszközének kiválasztását láthatja, majd létrehoz egy hőmérséklet-telemetria csempét az irányítópulton.
1. A bal felső sarokban található **Mentés** elemre kattintva mentheti a módosításokat az irányítópulton.

> [!div class="mx-imgBorder"]
> ![Irányítópult lap a hőmérséklet csempe részleteivel](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Most, hogy megtekinti a személyes irányítópultot, megjelenik az új csempe az eszköz **hőmérséklet** -beállításával:

> [!div class="mx-imgBorder"]
> ![Képernyőkép, amely megjeleníti az új csempét az eszköz hőmérséklet-beállításával.](media/howto-create-personal-dashboards/temperature-tile-complete.png)

A könyvtárban található egyéb csempe-típusok megismerésével megtudhatja, hogyan szabhatja testre a személyes irányítópultok testreszabását.

Ha többet szeretne megtudni a csempék használatáról az Azure IoT Centralban, tekintse meg a [csempék hozzáadása az irányítópulthoz](howto-add-tiles-to-your-dashboard.md)című témakört.

## <a name="manage-dashboards"></a>Irányítópultok kezelése

Több személyes irányítópultot is használhat, és válthat egymás között, vagy választhat az alapértelmezett alkalmazás-irányítópultok közül:

> [!div class="mx-imgBorder"]
> ![Váltás az irányítópultok között](media/howto-create-personal-dashboards/switch-dashboards.png)

Szerkesztheti a személyes irányítópultokat, és törölheti azokat az irányítópultokat, amelyekre már nincs szüksége. Ha Ön **rendszergazda**, szerkesztheti vagy törölheti az alkalmazás szintű irányítópultokat is.

> [!div class="mx-imgBorder"]
> ![Irányítópultok törlése](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan hozhat létre és kezelhet személyes irányítópultokat, [megtudhatja, hogyan kezelheti az alkalmazás beállításait](howto-manage-preferences.md).
