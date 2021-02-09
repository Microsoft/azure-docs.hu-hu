---
title: A Azure Portal használata a Azure Stack Edge Pro megoldásához | Microsoft Docs
description: Ismerje meg, hogyan lehet elhárítani az Azure Stack Edge Pro-ban felmerülő problémákat. Futtathat diagnosztikai adatokat, információkat gyűjthet a támogatáshoz, és hibakeresési naplókat is használhat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: ff6d1f46299875354a72a6a93a828df7960169ad
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831542"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-issues"></a>Az Azure Stack Edge Pro-problémák elhárítása

Ez a cikk ismerteti, hogyan lehet elhárítani a Azure Stack Edge Pro-ban felmerülő problémákat. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
>
> * Diagnosztika futtatása
> * Támogatási csomag összeállítása
> * Hibaelhárítás naplók használatával
> * IoT Edge hibák elhárítása

## <a name="run-diagnostics"></a>Diagnosztika futtatása

Az eszközök diagnosztizálásához és a hibák elhárításához futtassa le a diagnosztikai teszteket. Eszközének helyi webes felhasználói felületen kövesse az alábbi lépéseket a diagnosztikai tesztek futtatásához.

1. A helyi webes felhasználói felületen válassza a **Hibaelhárítás > Diagnosztikai tesztek** lehetőséget. Válassza ki a futtatni kívánt tesztet, és válassza a **teszt futtatása** lehetőséget. Megtörténik a hálózat, az eszköz, a webalkalmazás-proxy, az idő és a felhő beállításaival kapcsolatos lehetséges problémák diagnosztizálása. Értesítést kap arról, hogy az eszköz teszteket futtat.

    ![Tesztek kiválasztása](media/azure-stack-edge-troubleshoot/run-diag-1.png)

2. A tesztek lefutása után megjelennek az eredmények.

    ![Tesztek eredményeinek áttekintése](media/azure-stack-edge-troubleshoot/run-diag-2.png)

    Ha egy teszt sikertelen, megjelenik a javasolt művelet URL-címe. Válassza ki az URL-címet a javasolt művelet megtekintéséhez.

    ![A sikertelen tesztek figyelmeztetések áttekintése](media/azure-stack-edge-troubleshoot/run-diag-3.png)

## <a name="collect-support-package"></a>Támogatási csomag összeállítása

A naplócsomag tartalmazza az összes naplót, amely segít a Microsoft ügyfélszolgálatának az eszközproblémák elhárításában. A naplócsomagot a helyi webes felhasználói felületen hozhatja létre.

Kövesse az alábbi lépéseket a támogatási csomag összeállításához.

1. A helyi webes felhasználói felületen válassza a **Hibaelhárítás > Támogatás** lehetőséget. Válassza a **támogatási csomag létrehozása** lehetőséget. A rendszer megkezdi a támogatási csomag összeállítását. A csomagok gyűjteményének létrehozása eltarthat néhány percig.

    ![Kattintson a Felhasználó hozzáadása gombra](media/azure-stack-edge-troubleshoot/collect-logs-1.png)

2. A támogatási csomag létrehozása után válassza a **támogatási csomag letöltése** lehetőséget. A rendszer a tömörített csomagot letölti a megadott helyre. Csomagolja ki a csomagot, és tekintse meg a rendszernapló fájljait.

    ![Kattintson a felhasználó hozzáadása 2](media/azure-stack-edge-troubleshoot/collect-logs-2.png)

## <a name="use-logs-to-troubleshoot"></a>Hibaelhárítás naplók használatával

A feltöltési és frissítési folyamat során észlelt hibákat a megfelelő hibafájlok tartalmazzák.

1. A hibaüzenetek megtekintéséhez lépjen a megosztásra, és válassza ki a tartalmat a tartalom megtekintéséhez. 

      ![Kapcsolódás a megosztáshoz és a tartalom megtekintése](media/azure-stack-edge-troubleshoot/troubleshoot-logs-1.png)

2. Válassza ki a _Microsoft Azure stack Edge Pro mappát_. Ebben a mappában két almappát talál:

    * A feltöltési mappa, amely a feltöltési hibákat tartalmazó naplófájlokat tárolja.
    * A frissítési mappa a frissítés során bekövetkezett hibák számára.

    Íme egy minta naplófájl, amely a frissítési hibákat tartalmazza.

    ```xml
    <root container="test1" machine="VM15BS020663" timestamp="03/18/2019 00:11:10" />
    <file item="test.txt" local="False" remote="True" error="16001" />
    <summary runtime="00:00:00.0945320" errors="1" creates="2" deletes="0" insync="3" replaces="0" pending="9" />
    ```

3. Ha hibaüzenet jelenik meg ebben a fájlban (a mintában), jegyezze fel a hibakódot, ebben az esetben ez 16001. Keresse meg a hibakód leírását a következő hibaadatok alapján.

    [!INCLUDE [data-box-edge-edge-upload-error-reference](../../includes/data-box-edge-gateway-upload-error-reference.md)]

## <a name="troubleshoot-iot-edge-errors"></a>IoT Edge hibák elhárítása

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]

## <a name="next-steps"></a>Következő lépések

* További információk a [jelen kiadás ismert problémáiról](../databox-gateway/data-box-gateway-release-notes.md).