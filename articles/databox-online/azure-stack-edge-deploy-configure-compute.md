---
title: Oktatóanyag a szűréshez, az adatok elemzéséhez Azure Stack Edge Pro-beli számításokkal | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja a számítási szerepkört Azure Stack Edge Pro-ban, és hogyan alakíthatja át az adatokat az Azure-ba való küldés előtt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/06/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 10741fbf2deb61d63f444ff9e2247bc59f41af38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97968729"
---
# <a name="tutorial-transform-the-data-with-azure-stack-edge-pro"></a>Oktatóanyag: az adatgyűjtés Azure Stack Edge Pro-val

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

- Aktiválta a Azure Stack Edge Pro-eszközt a következő témakörben leírtak szerint: a [Azure stack Edge Pro csatlakoztatása, beállítása és aktiválása](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Számítás konfigurálása

A Azure Stack Edge Pro-ban való számítás konfigurálásához létre kell hoznia egy IoT Hub-erőforrást.

1. Az Azure Stack Edge-erőforrás Azure Portal válassza az **Áttekintés** lehetőséget. A jobb oldali ablaktáblában válassza a **IoT Edge** lehetőséget.

    ![Ismerkedés a számítási feladatokkal](./media/azure-stack-edge-deploy-configure-compute/configure-compute-1.png)

1. A **IoT Edge engedélyezése** csempén válassza a **Hozzáadás** lehetőséget. Ez lehetővé teszi a IoT Edge szolgáltatást, amely lehetővé teszi IoT Edge modulok helyi telepítését az eszközön.

    ![Ismerkedés a 2. számítási feladatokkal](./media/azure-stack-edge-deploy-configure-compute/configure-compute-2.png)

1. A **IoT Edge szolgáltatás létrehozása** panelen adja meg a következőket:

   
    |Mező  |Érték  |
    |---------|---------|
    |Előfizetés     |Válasszon egy előfizetést a IoT Hub erőforráshoz. Használhatja ugyanazt az előfizetést, mint amelyet az Azure Stack Edge-erőforrás használ.         |
    |Erőforráscsoport     |Válasszon ki egy erőforráscsoportot a IoT Hub erőforráshoz. Használhatja ugyanazt az erőforráscsoportot, mint amelyet az Azure Stack Edge-erőforrás használ.         |
    |IoT Hub     | Válasszon az **új** vagy a **meglévő** listából. <br> Alapértelmezés szerint IoT-erőforrás létrehozásához a rendszer Standard szintet (S1) használ. Ingyenes szintű IoT-erőforrás használatához hozzon létre egyet, majd válassza ki a létrehozott erőforrást. <br> Az IoT Hub-erőforrás mindig ugyanazt az előfizetést és erőforráscsoportot használja, mint az Azure Stack Edge-erőforrás.     |
    |Name     |Adja meg a IoT Hub erőforrás nevét.         |

    ![Ismerkedés a 3. számítási feladatokkal](./media/azure-stack-edge-deploy-configure-compute/configure-compute-3.png)

4. Válassza a **Felülvizsgálat és létrehozás** lehetőséget. A IoT Hub erőforrás létrehozása néhány percet vesz igénybe. A IoT Hub erőforrás létrehozása után az **áttekintő** frissítések jelzik, hogy a IoT Edge szolgáltatás fut. 

    ![Ismerkedés a 4. számítási feladatokkal](./media/azure-stack-edge-deploy-configure-compute/configure-compute-4.png)    
    
    Ha a IoT Edge szolgáltatás a peremhálózati eszközön van konfigurálva, két eszközt hoz létre: egy IoT-eszközt és egy IoT Edge-eszközt. Mindkét eszköz megtekinthető a IoT Hub erőforrásban. Ezen a IoT Edge eszközön a IoT Edge futtatókörnyezet is fut. Ezen a ponton csak a Linux platform érhető el a IoT Edge eszközhöz.

    Annak ellenőrzéséhez, hogy a peremhálózati számítási szerepkör konfigurálva van-e, válassza **IoT Edge szolgáltatás > tulajdonságok** lehetőséget, és tekintse meg a IoT eszközt és a IoT Edge eszközt.

    ![Ismerkedés a számítási feladatokkal 5](./media/azure-stack-edge-deploy-configure-compute/configure-compute-5.png) 

## <a name="add-shares"></a>Megosztások hozzáadása

Az oktatóanyag egyszerű üzembe helyezéséhez két megosztásra lesz szüksége: egy peremhálózati megosztásra és egy másik peremhálózati helyi megosztásra.

1. Vegyen fel egy peremhálózati megosztást az eszközön a következő lépések végrehajtásával:

    1. Az Azure Stack Edge-erőforrásban válassza a **IoT Edge > megosztások** lehetőséget.
    2. A parancssorban válassza a **+ megosztás hozzáadása** elemet.
    3. A **megosztás hozzáadása** panelen adja meg a megosztás nevét, és válassza ki a megosztás típusát.
    4. Az Edge-megosztás csatlakoztatásához jelölje be a **megosztás Edge-számítással való használatának** jelölőnégyzetét.
    5. Válassza ki a **Storage-fiókot**, a **Storage szolgáltatást**, egy meglévő felhasználót, majd válassza a **Létrehozás** lehetőséget.

        ![Peremhálózati megosztás hozzáadása](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-1.png) 

    Ha létrehozott egy helyi NFS-megosztást, a következő parancssori kapcsoló használatával másolja a fájlokat a megosztásba:

    `rsync <source file path> < destination file path>`

    Az rsync-paranccsal kapcsolatos további információkért lépjen az [rsync dokumentációra](https://www.computerhope.com/unix/rsync.htm).

    A rendszer létrehozza az Edge-megosztást, és sikeres létrehozási értesítést fog kapni. Előfordulhat, hogy a megosztási lista frissül, de meg kell várnia, amíg a megosztás létrehozása be nem fejeződik.

2. Vegyen fel egy peremhálózati helyi megosztást a peremhálózati eszközön úgy, hogy megismétli az előző lépés összes lépését, és bejelöli a **beállítás helyi megosztásként** jelölőnégyzetet. A helyi megosztásban lévő adatmennyiség az eszközön marad.

    ![Peremhálózat helyi megosztásának hozzáadása](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. A megosztások frissített listájának megtekintéséhez nyissa meg a **IoT Edge >-megosztásokat** .

    ![Megosztások frissített listája](./media/azure-stack-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Modul hozzáadása

Hozzáadhat egy egyéni vagy egy előre elkészített modult. Ezen a peremhálózati eszközön nincsenek egyéni modulok. Ha szeretné megismerni, hogyan hozhat létre egyéni modult, ugorjon a [C# modul fejlesztése a Azure stack Edge Pro-eszközhöz](azure-stack-edge-create-iot-edge-module.md)című témakörre.

Ebben a szakaszban egy egyéni modult ad hozzá a IoT Edge eszközhöz, amelyet a [C# modul fejlesztése a Azure stack Edge Pro](azure-stack-edge-create-iot-edge-module.md)-hoz című témakörben hozott létre. Ez az egyéni modul a peremhálózati eszköz peremhálózati helyi megosztásának fájljait veszi át, és áthelyezi őket egy peremhálózati (Felhőbeli) megosztásra az eszközön. A felhőalapú megosztás ezután leküldi a fájlokat a Felhőbeli megosztáshoz társított Azure Storage-fiókba.

1. Nyissa meg **IoT Edge > modulokat**. Az eszköz parancssáv területén válassza a **+ modul hozzáadása** lehetőséget.
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

    ![Modul hozzáadása és konfigurálása](./media/azure-stack-edge-deploy-configure-compute/add-module-1.png)

3. Válassza a **Hozzáadás** lehetőséget. A modul hozzá lesz adva. A **IoT Edge > áttekintő** lap frissítései jelzik, hogy a modul telepítve van. 

    ![Telepített modul](./media/azure-stack-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Adatok átalakításának ellenőrzése és adatok átvitele

Az utolsó lépés az, hogy ellenőrizze, hogy a modul csatlakoztatva van-e, és hogy a várt módon fut-e. A modul futtatási ideje állapotának futnia kell a IoT Edge eszközön a IoT Hub erőforrásban.

Annak ellenőrzéséhez, hogy a modul fut-e, tegye a következőket:

1. Válassza a **modul hozzáadása** csempét. Ekkor a **modulok** panelre kerül. A modulok listájában azonosítsa a telepített modult. A hozzáadott modul futásidejű állapotának *futnia* kell.

    ![Adatok átalakításának ellenőrzése](./media/azure-stack-edge-deploy-configure-compute/verify-data-1.png)
 
1. A Fájlkezelőben a korábban létrehozott Edge helyi és peremhálózati megosztásokhoz is csatlakozhat.

    ![Az adatátalakítás 2 ellenőrzése](./media/azure-stack-edge-deploy-configure-compute/verify-data-2.png) 
 
1. Adja hozzá az adatokat a helyi megosztáshoz.

    ![Az adatátalakító 3 ellenőrzése](./media/azure-stack-edge-deploy-configure-compute/verify-data-3.png) 
 
    Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![Az adatátalakítás 4 ellenőrzése](./media/azure-stack-edge-deploy-configure-compute/verify-data-4.png)  

    Az adatok ezután a Felhőbeli megosztásról a Storage-fiókba kerülnek. Az adatmegjelenítéshez lépjen a Storage Explorer.

    ![Az 5. adatátalakítás ellenőrzése](./media/azure-stack-edge-deploy-configure-compute/verify-data-5.png) 
 
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
