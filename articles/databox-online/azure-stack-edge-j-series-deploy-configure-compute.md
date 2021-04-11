---
title: Oktatóanyag az adatok szűrésére, elemzésére a Azure Stack Edge Pro és a GPU használatával történő számításokkal | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat számítási szerepkört Azure Stack Edge Pro GPU-eszközön, és hogyan alakíthatja át az adatokat az Azure-ba való küldés előtt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 1dcf429ddc697dfbfdac721cb8de137b9382b234
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058782"
---
# <a name="tutorial-transform-data-with-azure-stack-edge-pro"></a>Oktatóanyag: az adatátalakítás Azure Stack Edge Pro-val

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez az oktatóanyag azt ismerteti, hogyan lehet számítási szerepkört konfigurálni a Azure Stack Edge Pro-eszközön. A számítási szerepkör konfigurálása után Azure Stack Edge Pro átalakíthatja az adatokat, mielőtt elküldené az Azure-ba.

Ez az eljárás körülbelül 10 – 15 percet is igénybe vehet.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt beállít egy számítási szerepkört a Azure Stack Edge Pro-eszközön, ügyeljen rá, hogy:
- Aktiválta Azure Stack Edge Pro-eszközét az [Azure stack Edge Pro aktiválása](azure-stack-edge-gpu-deploy-activate.md)című témakörben leírtak szerint.


## <a name="configure-compute"></a>Számítás konfigurálása

A Azure Stack Edge Pro-ban való számítás konfigurálásához létre kell hoznia egy IoT Hub-erőforrást.

1. Az Azure Stack Edge-erőforrás Azure Portal lépjen az **Áttekintés** elemre, és válassza a **IoT Edge** lehetőséget.

   ![Ismerkedés a számítási feladatokkal](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-1.png)

2. A **IoT Edge szolgáltatás engedélyezése** lapon válassza a **Hozzáadás** lehetőséget.

   ![Számítás konfigurálása](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-2.png)

3. A **IoT Edge szolgáltatás létrehozása** területen adja meg IoT hub erőforrásának beállításait:

   |Mező   |Érték    |
   |--------|---------|
   |Előfizetés      | Az Azure Stack Edge-erőforrás által használt előfizetés. |
   |Erőforráscsoport    | Az Azure Stack peremhálózati erőforrás által használt erőforráscsoport. |
   |IoT Hub           | Válassza az **új létrehozása** lehetőséget, vagy **használja a meglévőt**. <br> Alapértelmezés szerint IoT-erőforrás létrehozásához a rendszer Standard szintet (S1) használ. Ingyenes szintű IoT-erőforrás használatához hozzon létre egyet, majd válassza ki a létrehozott erőforrást. <br> Az IoT Hub-erőforrás mindig ugyanazt az előfizetést és erőforráscsoportot használja, mint az Azure Stack Edge-erőforrás.     |
   |Name              | Ha nem szeretné használni az új IoT Hub erőforráshoz megadott alapértelmezett nevet, adjon meg egy másik nevet. |

    ![Ismerkedés a 2. számítási feladatokkal](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

4. A beállítások befejezését követően válassza a **felülvizsgálat + létrehozás** lehetőséget. Tekintse át a IoT Hub erőforrás beállításait, és válassza a **Létrehozás** lehetőséget.

   Egy IoT Hub erőforrás létrehozása több percet vesz igénybe. Az erőforrás létrehozása után az **Áttekintés** azt jelzi, hogy a IoT Edge szolgáltatás már fut.

    ![Ismerkedés a 3. számítási feladatokkal](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. A peremhálózati számítási szerepkör konfigurálásának megerősítéséhez válassza a **Tulajdonságok** lehetőséget.

   ![Ismerkedés a 4. számítási feladatokkal](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

   Amikor a peremhálózati eszközön beállította a peremhálózat számítási szerepkört, két eszközt hoz létre: egy IoT eszközt és egy IoT Edge eszközt. Mindkét eszköz megtekinthető a IoT Hub erőforrásban. Ezen a IoT Edge eszközön a IoT Edge futtatókörnyezet is fut. Ezen a ponton csak a Linux platform érhető el a IoT Edge eszközhöz.


## <a name="add-shares"></a>Megosztások hozzáadása

Az oktatóanyag egyszerű üzembe helyezéséhez két megosztásra lesz szüksége: egy peremhálózati megosztásra és egy másik peremhálózati helyi megosztásra.

1. Vegyen fel egy peremhálózati megosztást az eszközön a következő lépések végrehajtásával:

    1. Az Azure Stack Edge-erőforrásban lépjen az **Edge számítás > első lépések** elemre.
    2. A **megosztás (ok) hozzáadása** csempén válassza a **Hozzáadás** lehetőséget.

        ![Megosztási csempe hozzáadása](./media/azure-stack-edge-j-series-deploy-configure-compute/add-share-1.png) 

    3. A **megosztás hozzáadása** panelen adja meg a megosztás nevét, és válassza ki a megosztás típusát.
    4. Az Edge-megosztás csatlakoztatásához jelölje be a **megosztás Edge-számítással való használatának** jelölőnégyzetét.
    5. Válassza ki a **Storage-fiókot**, a **Storage szolgáltatást**, egy meglévő felhasználót, majd válassza a **Létrehozás** lehetőséget.

        ![Peremhálózati megosztás hozzáadása](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-1.png) 

    Ha létrehozott egy helyi NFS-megosztást, a következő `rsync` parancssori kapcsoló használatával másolhatja a fájlokat a megosztásra:

    `rsync <source file path> < destination file path>`

    A paranccsal kapcsolatos további információkért `rsync` keresse fel a [ `Rsync` dokumentációt](https://www.computerhope.com/unix/rsync.htm).

    > [!NOTE]
    > Ha az NFS-megosztást a számításhoz szeretné csatlakoztatni, a számítási hálózatot ugyanazon az alhálózaton kell konfigurálni, mint az NFS virtuális IP-címet. A számítási hálózat konfigurálásával kapcsolatos részletekért nyissa meg a [számítási hálózat engedélyezése a Azure stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)-ban című témakört.

    A rendszer létrehozza az Edge-megosztást, és sikeres létrehozási értesítést fog kapni. Előfordulhat, hogy a megosztási lista frissül, de meg kell várnia, amíg a megosztás létrehozása be nem fejeződik.

2. Vegyen fel egy peremhálózati helyi megosztást a peremhálózati eszközön úgy, hogy megismétli az előző lépés összes lépését, és bejelöli a **beállítás helyi megosztásként** jelölőnégyzetet. A helyi megosztásban lévő adatmennyiség az eszközön marad.

    ![Peremhálózat helyi megosztásának hozzáadása](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-2.png)

  
3. Válassza a **megosztások hozzáadása** lehetőséget a megosztások frissített listájának megtekintéséhez.

    ![Megosztások frissített listája](./media/azure-stack-edge-j-series-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Modul hozzáadása

Hozzáadhat egy egyéni vagy egy előre elkészített modult. Ezen a peremhálózati eszközön nincsenek egyéni modulok. Ha szeretné megismerni, hogyan hozhat létre egyéni modult, ugorjon a [C# modul fejlesztése a Azure stack Edge Pro-eszközhöz](./azure-stack-edge-gpu-create-iot-edge-module.md)című témakörre.

Ebben a szakaszban egy egyéni modult ad hozzá a IoT Edge eszközhöz, amelyet a [C# modul fejlesztése a Azure stack Edge Pro](./azure-stack-edge-gpu-create-iot-edge-module.md)-hoz című témakörben hozott létre. Ez az egyéni modul a peremhálózati eszköz peremhálózati helyi megosztásának fájljait veszi át, és áthelyezi őket egy peremhálózati (Felhőbeli) megosztásra az eszközön. A felhőalapú megosztás ezután leküldi a fájlokat a Felhőbeli megosztáshoz társított Azure Storage-fiókba.

1. Ugrás az **Edge-számításra > első lépések**. A **modulok hozzáadása** csempén válassza ki a forgatókönyv típusát **egyszerűként**. Válassza a **Hozzáadás** lehetőséget.
2. A **modul konfigurálása és hozzáadása panelen adja** meg a következő értékeket:

    
    |Mező  |Érték  |
    |---------|---------|
    |Név     | A modul egyedi neve. Ez a modul egy Docker-tároló, amelyet a Azure Stack Edge Pro-hoz társított IoT Edge eszközre telepíthet.        |
    |Rendszerkép URI-ja     | A modulhoz tartozó tároló rendszerképéhez tartozó képuri.        |
    |Szükséges hitelesítő adatok     | Ha be van jelölve, a rendszer a felhasználónevet és a jelszót használja a modulok megfelelő URL-címmel való lekéréséhez.        |
    |Bemeneti megosztás     | Válasszon egy bemeneti megosztást. Ebben az esetben a peremhálózat helyi megosztása a bemeneti megosztás. Az itt használt modul áthelyezi a fájlokat a peremhálózati helyi megosztásból egy peremhálózati megosztásba, ahol azokat a rendszer feltölti a felhőbe.        |
    |Kimeneti megosztás     | Válasszon kimeneti megosztást. Ebben az esetben az Edge-megosztás a kimeneti megosztás.        |
    |Trigger típusa     | Válasszon a **fájlból** vagy az **ütemtervből**. Egy fájltrigger aktiválódik, valahányszor bekövetkezik egy fájlesemény, például amikor a rendszer egy fájlt ír a bemeneti megosztásba. Az ütemezett triggerek egy Ön által meghatározott ütemezés alapján aktiválódnak.         |
    |Trigger neve     | Az trigger egyedi neve.         |
    |Környezeti változók| Nem kötelezően megadandó információk, amelyek segítenek meghatározni azt a környezetet, amelyben a modul futni fog.   |

    ![Modul hozzáadása és konfigurálása](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-1.png)

3. Válassza a **Hozzáadás** lehetőséget. A modul hozzá lesz adva. Ugrás az **Áttekintés** oldalra. A **modulok** csempéje frissül, és jelzi, hogy a modul telepítve van. 

    ![Telepített modul](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Adatok átalakításának ellenőrzése és adatok átvitele

Az utolsó lépés az, hogy ellenőrizze, hogy a modul csatlakoztatva van-e, és hogy a várt módon fut-e. A modul futtatási ideje állapotának futnia kell a IoT Edge eszközön a IoT Hub erőforrásban.

Annak ellenőrzéséhez, hogy a modul fut-e, tegye a következőket:

1. Válassza a **modul hozzáadása** csempét. Ekkor a **modulok** panelre kerül. A modulok listájában azonosítsa a telepített modult. A hozzáadott modul futásidejű állapotának *futnia* kell.

    ![Telepített modul megtekintése](./media/azure-stack-edge-j-series-deploy-configure-compute/add-module-3.png)
 
1. A Fájlkezelőben a korábban létrehozott Edge helyi és peremhálózati megosztásokhoz is csatlakozhat.

    ![Az adatátalakítás ellenőrzése – 1](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-2.png) 
 
1. Adja hozzá az adatokat a helyi megosztáshoz.

    ![Az adatátalakítás ellenőrzése – 2](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-3.png) 
 
   Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![Az adatátalakítás ellenőrzése – 3](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-4.png)  

   Az adatok ezután a Felhőbeli megosztásról a Storage-fiókba kerülnek. Az adatmegjelenítéshez használhatja a Storage Explorer.

    <!--![Verify data transform](./media/azure-stack-edge-j-series-deploy-configure-compute/verify-data-5.png)-->
 
Végrehajtotta az érvényesítési folyamatot.


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

Az Azure Stack Edge Pro-eszköz felügyeletének megismeréséhez lásd:

> [!div class="nextstepaction"]
> [Helyi webes felhasználói felület használata Azure Stack Edge Pro felügyeletéhez](azure-stack-edge-manage-access-power-connectivity-mode.md)