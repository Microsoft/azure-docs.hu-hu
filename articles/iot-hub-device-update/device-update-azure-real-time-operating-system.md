---
title: Az Azure valós idejű eszközének frissítése – operációs rendszer | Microsoft Docs
description: Első lépések az Azure valós idejű operációs rendszerhez készült eszköz frissítésével
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 66da860a5cdae1f5c7c18e4136b1f2d960492ca8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629053"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Azure IoT Hub-oktatóanyag az Azure valós idejű operációs rendszer (RTOS) használatával

Ez az oktatóanyag végigvezeti a IoT Hub-ügynök frissítésének létrehozásán az Azure RTOS NetX Duo-ban. Emellett egyszerű API-kat biztosít a fejlesztők számára az eszköz frissítési képességének integrálásához az alkalmazásban. [Ismerkedjen](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) meg a Key Semiconductors próbaverziós tábláival, amelyek tartalmazzák az első lépéseket ismertető útmutatókat, amelyekkel megtudhatja, hogyan konfigurálhatja, építheti és helyezheti üzembe az eszközökön a over-Air (OTA) frissítéseket.

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Bevezetés
> * Az eszköz címkézése
> * Eszközcsoport létrehozása
> * Lemezkép frissítésének központi telepítése
> * A frissítés központi telepítésének figyelése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek
* Hozzáférés egy IoT Hubhoz. Javasoljuk, hogy S1 (standard) vagy újabb szintet használjon.
* Egy eszköz frissítési példánya és a IoT Hubhoz társított fiók. Ha korábban még nem tette meg, kövesse az útmutatót az eszköz frissítési fiókjának [létrehozásához és összekapcsolásához](http://create-device-update-account.md/) .

## <a name="get-started"></a>Bevezetés

Minden táblára jellemző Azure RTOS-projekt tartalmaz egy kódot és egy dokumentációt arról, hogyan használhatja IoT Hub az eszköz frissítését. 
1. Töltse le az Üzenőfal-specifikus mintákat az [Azure RTOS és az eszköz frissítési mintái](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU)közül.
2. Keresse meg a docs mappát a letöltött mintából.
3. A dokumentumokból megtudhatja, hogyan készítheti elő az Azure-erőforrásokat, és hogyan regisztrálhat IoT-eszközöket.
5. Ezután kövesse a dokumentációt, és hozzon létre egy új belső vezérlőprogram-rendszerképet, és importálja a jegyzékfájlt a táblához.
6. Ezután tegye közzé a belső vezérlőprogram rendszerképét és a jegyzékfájlt a IoT Hub eszköz frissítéséhez.
7. Végül töltse le és futtassa a projektet az eszközén.

További információ az [Azure RTOS](https://docs.microsoft.com/azure/rtos/)-ről.  

## <a name="tag-your-device"></a>Az eszköz címkézése

1. Tartsa meg az eszközön futó alkalmazást az előző lépésből.
2. Jelentkezzen be [Azure Portalba](https://portal.azure.com) , és navigáljon a IoT hub.
3. A bal oldali navigációs panelen válassza a IoT-eszközök elemet, keresse meg a IoT eszközt, és navigáljon a Twin (eszközök) elemre.
4. A Twin (eszközök) készletben törölje a meglévő eszköz frissítési címke értékét a NULL értékre állításával.
5. Vegyen fel egy új eszköz frissítési címke értékét az alább látható módon.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>Frissítési csoport létrehozása

1. Nyissa meg az eszköz frissítési példányához korábban csatlakoztatott IoT Hub.
2. Válassza ki az eszközök frissítései lehetőséget a bal oldali navigációs sávon az automatikus eszközkezelés lehetőség alatt.
3. Válassza a csoportok fület az oldal tetején. 
4. Új csoport létrehozásához kattintson a Hozzáadás gombra.
5. Válassza ki az előző lépésben létrehozott IoT Hub címkét a listából. Válassza a frissítési csoport létrehozása lehetőséget.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="A címke kijelölését bemutató képernyőfelvétel" lightbox="media/create-update-group/select-tag.PNG":::

[További](create-update-group.md) információ a címkék hozzáadásáról és a frissítési csoportok létrehozásáról

## <a name="deploy-new-firmware"></a>Új belső vezérlőprogram üzembe helyezése

1. Miután létrejött a csoport, megjelenik egy új frissítés az eszközcsoport számára, amely a frissítésre mutató hivatkozást is tartalmazza a függőben lévő frissítések területen. Előfordulhat, hogy egyszer kell frissítenie. 
2. Kattintson az elérhető frissítésre.
3. Ellenőrizze, hogy a megfelelő csoport van-e kiválasztva célcsoportként. Ütemezze az üzemelő példányt, majd válassza a frissítés telepítése lehetőséget.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Frissítés kiválasztása" lightbox="media/deploy-update/select-update.png":::

4. Tekintse meg a megfelelőségi diagramot. Ekkor már folyamatban van a frissítés. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Frissítés folyamatban" lightbox="media/deploy-update/update-in-progress.png":::

5. Az eszköz sikeres frissítése után a megfelelőségi diagram és az üzembe helyezési adatok frissítése megjelenik, hogy az tükrözze ugyanezt. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Sikeres frissítés" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Frissítések központi telepítésének figyelése

1. Válassza a központi telepítések fület az oldal tetején.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Központi telepítések lap" lightbox="media/deploy-update/deployments-tab.png":::

2. Válassza ki a létrehozott központi telepítést a központi telepítés részleteinek megtekintéséhez.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Központi telepítés részletei" lightbox="media/deploy-update/deployment-details.png":::

3. Kattintson a frissítés elemre a legutóbbi állapot részleteinek megtekintéséhez. Folytassa ezt a folyamatot, amíg az állapot sikeresen nem változik.

Sikeresen befejezte a teljes rendszerkép-frissítést a IoT Hub eszköz frissítésével a málna PI 3 B + eszközön. 

## <a name="cleanup-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az eszköz frissítési fiókjának, példányának, IoT Hubának és IoT eszközének törlésére. 

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure RTOS, valamint arról, hogy miként működik az Azure IoT, tekintse meg a következőt: https://azure.com/rtos .
