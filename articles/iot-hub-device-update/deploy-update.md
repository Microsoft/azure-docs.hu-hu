---
title: Frissítés telepítése az Azure IoT Hub eszköz frissítése használatával | Microsoft Docs
description: Telepítsen egy frissítést az Azure IoT Hub eszköz frissítése segítségével.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679516"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>Frissítés telepítése a IoT Hub eszköz frissítése használatával

Megtudhatja, hogyan telepíthet frissítést egy IoT-eszközre a IoT Hub eszköz frissítése segítségével.

## <a name="prerequisites"></a>Előfeltételek

* [Hozzáférés egy IoT hubhoz az eszköz frissítésével IoT hub engedélyezve](create-device-update-account.md). Javasoljuk, hogy a IoT Hub S1 (standard) vagy újabb verzióját használja. 
* [A kiépített eszközhöz legalább egy frissítés importálása sikeres volt.](import-update.md) 
* Az eszköz frissítéséhez kiépített IoT-eszköz (vagy szimulátor) IoT Hubon belül.
* [A frissíteni kívánt IoT-eszközhöz címke van hozzárendelve. Az eszköz legalább egy frissítési csoport részét képezi.](create-update-group.md)
* Támogatott böngészők:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>Frissítés központi telepítése

1. Ugrás a [Azure Portal](https://portal.azure.com)

2. Navigáljon a IoT Hub eszköz frissítése paneljére.

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="IoT Hub" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. Válassza a csoportok fület az oldal tetején. [További](device-update-groups.md) információ az eszközök csoportjairól. 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="Csoportok lap" lightbox="media/deploy-update/updated-view.png":::

4. Tekintse meg a frissítési megfelelőségi diagram és csoportok listát. Meg kell jelennie egy új frissítésnek az eszközosztály számára, amely a frissítésre mutató hivatkozást is tartalmazza (Előfordulhat, hogy egyszer kell frissítenie). [További információ a frissítés megfelelőségéről.](device-update-compliance.md) 

5. Válassza ki az elérhető frissítést.

6. Ellenőrizze, hogy a megfelelő csoport van-e kiválasztva célcsoportként. Ütemezze az üzemelő példányt, majd válassza a frissítés telepítése lehetőséget.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Frissítés kiválasztása" lightbox="media/deploy-update/select-update.png":::

7. Tekintse meg a megfelelőségi diagramot. Ekkor már folyamatban van a frissítés. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Frissítés folyamatban" lightbox="media/deploy-update/update-in-progress.png":::

8. Az eszköz sikeres frissítése után a megfelelőségi diagram és az üzembe helyezési adatok frissítése megjelenik, hogy az tükrözze ugyanezt. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Sikeres frissítés" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Frissítések központi telepítésének figyelése

1. Válassza a központi telepítések fület az oldal tetején.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Központi telepítések lap" lightbox="media/deploy-update/deployments-tab.png":::

2. Válassza ki a létrehozott központi telepítést a központi telepítés részleteinek megtekintéséhez.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Központi telepítés részletei" lightbox="media/deploy-update/deployment-details.png":::

3. Kattintson a frissítés elemre a legutóbbi állapot részleteinek megtekintéséhez. Folytassa ezt a folyamatot, amíg az állapot sikeresen nem változik.


## <a name="retry-an-update-deployment"></a>Frissítés központi telepítésének újrapróbálása

Ha a központi telepítés valamilyen okból meghiúsul, megismételheti a sikertelen eszközök üzembe helyezését. 

1. Lépjen a központi telepítések lapra, és válassza ki a sikertelen telepítést. 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Központi telepítés részletei" lightbox="media/deploy-update/deployment-details.png":::

2. Kattintson a "sikertelen" eszköz állapotára a részletes központi telepítési információ panelen.

3. Kattintson a "sikertelen eszközök újrapróbálása" elemre, és fogadja el a megerősítő értesítést. 

## <a name="next-steps"></a>Következő lépések

[Gyakori problémák megoldása](troubleshoot-device-update.md)
