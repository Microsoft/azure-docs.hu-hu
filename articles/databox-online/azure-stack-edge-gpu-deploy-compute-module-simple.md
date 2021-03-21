---
title: IoT Edge modul használata számítási feladatok üzembe helyezéséhez Azure Stack Edge Pro-ban GPU-val | Microsoft Docs
description: Megtudhatja, hogyan futtathat számítási feladatokat egy előre létrehozott IoT Edge modul használatával a Azure Stack Edge Pro GPU-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 6c067fb5f39e82bb1601ce7b4d9dc5e2ce4ac624
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102440139"
---
# <a name="tutorial-run-a-compute-workload-with-iot-edge-module-on-azure-stack-edge-pro-gpu"></a>Oktatóanyag: számítási feladatok futtatása IoT Edge modullal Azure Stack Edge Pro GPU-val

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Ez az oktatóanyag azt ismerteti, hogyan futtathat számítási feladatokat egy IoT Edge modul használatával a Azure Stack Edge Pro GPU-eszközön. A számítás konfigurálása után az eszköz átalakítja az adatokat, mielőtt elküldené az Azure-ba.

Ez az eljárás körülbelül 10 – 15 percet is igénybe vehet.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Megosztások hozzáadása
> * Számítási modul hozzáadása
> * Adatok átalakításának ellenőrzése és adatok átvitele

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt beállít egy számítási szerepkört a Azure Stack Edge Pro GPU-eszközön, győződjön meg a következőket:

- Aktiválta Azure Stack Edge Pro-eszközét az [Azure stack Edge Pro aktiválása](azure-stack-edge-gpu-deploy-activate.md)című témakörben leírtak szerint.
- Rendelkezik egy IoT Edge modullal, amelyet futtathat az adatain. Ebben az oktatóanyagban egy modult használunk, `filemove2` amely áthelyezi az adatok helyi megosztását az eszközön a peremhálózat megosztására, ahonnan az adatok Azure Storage-fiókba kerülnek.


## <a name="configure-compute"></a>Számítás konfigurálása

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]


## <a name="add-shares"></a>Megosztások hozzáadása

Az oktatóanyag egyszerű üzembe helyezéséhez két megosztásra lesz szüksége: egy peremhálózati megosztásra és egy másik peremhálózati helyi megosztásra.

1. Az eszközhöz tartozó peremhálózati megosztás hozzáadásához hajtsa végre a következő lépéseket:

    1. A Azure Stack Edge-erőforrásban nyissa meg a **Cloud Storage gateway >-megosztások** lapot.
    2. A parancssorban válassza a **+ megosztás hozzáadása** elemet.
    3. A **megosztás hozzáadása** panelen adja meg a megosztás nevét, és válassza ki a megosztás típusát.
    4. Az Edge-megosztás csatlakoztatásához jelölje be a **megosztás Edge-számítással való használatának** jelölőnégyzetét.
    5. Válassza ki a **Storage-fiókot**, a **Storage szolgáltatást**, egy meglévő felhasználót, majd válassza a **Létrehozás** lehetőséget.

        ![Peremhálózati megosztás hozzáadása](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-1.png) 


    > [!NOTE]
    > Ha az NFS-megosztást a számításhoz szeretné csatlakoztatni, a számítási hálózatot ugyanazon az alhálózaton kell konfigurálni, mint az NFS virtuális IP-címet. A számítási hálózat konfigurálásával kapcsolatos részletekért nyissa meg a [számítási hálózat engedélyezése a Azure stack Edge Pro](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)-ban című témakört.

    A rendszer létrehozza az Edge-megosztást, és sikeres létrehozási értesítést fog kapni. Előfordulhat, hogy a megosztási lista frissül, de meg kell várnia, amíg a megosztás létrehozása be nem fejeződik.

2. Ha egy helyi helyi megosztást szeretne hozzáadni az eszközön, ismételje meg az előző lépés összes lépését, és jelölje be a **helyi megosztásként való konfigurálás** jelölőnégyzetét. A helyi megosztásban lévő adatmennyiség az eszközön marad.

    ![Peremhálózat helyi megosztásának hozzáadása](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-2.png)

    Ha létrehozott egy helyi NFS-megosztást, a következő parancssori kapcsoló használatával másolja a fájlokat a megosztásba:

    `rsync <source file path> < destination file path>`

    A paranccsal kapcsolatos további információkért `rsync` keresse fel a [ `Rsync` dokumentációt](https://www.computerhope.com/unix/rsync.htm).
 
3. A megosztások frissített listájának megtekintéséhez nyissa meg a **Cloud Storage gateway >-megosztásokat** .

    ![Megosztások frissített listája](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Modul hozzáadása

Hozzáadhat egy egyéni vagy egy előre elkészített modult. Az eszközhöz nem tartoznak előre elkészített vagy egyéni modulok. Ha szeretné megismerni, hogyan hozhat létre egyéni modult, ugorjon a [C# modul fejlesztése a Azure stack Edge Pro-eszközhöz](azure-stack-edge-j-series-create-iot-edge-module.md)című témakörre.

Ebben a szakaszban egy egyéni modult ad hozzá a IoT Edge eszközhöz, amelyet a [C# modul fejlesztése a Azure stack Edge Pro](azure-stack-edge-j-series-create-iot-edge-module.md)-hoz című témakörben hozott létre. Ez az egyéni modul a peremhálózati eszköz peremhálózati helyi megosztásának fájljait veszi át, és áthelyezi őket egy peremhálózati (Felhőbeli) megosztásra az eszközön. A felhőalapú megosztás ezután leküldi a fájlokat a Felhőbeli megosztáshoz társított Azure Storage-fiókba.

Modul hozzáadásához hajtsa végre a következő lépéseket:

1. Nyissa meg **IoT Edge > modulokat**. A parancssorban válassza a **+ modul hozzáadása** lehetőséget. 

2. A **modul hozzáadása panelen adja** meg a következő értékeket:

    
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

    ![Modul hozzáadása és konfigurálása](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-1.png)

3. Válassza a **Hozzáadás** lehetőséget. A modul hozzá lesz adva. A **IoT Edge > modulok** lapon a frissítések jelzik, hogy a modul telepítve van. A hozzáadott modul futásidejű állapotának *futnia* kell.

    ![Telepített modul](./media/azure-stack-edge-gpu-deploy-compute-module-simple/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Adatok átalakításának ellenőrzése és adatok átvitele

Az utolsó lépés annak biztosítása, hogy a modul fut, és az elvárt módon dolgozza fel az adatfeldolgozást. A modul futtatási ideje állapotának futnia kell a IoT Edge eszközön a IoT Hub erőforrásban.

A következő lépésekkel ellenőrizheti, hogy a modul fut-e, és a várt módon dolgozza fel az adatfeldolgozást:


1. A Fájlkezelőben a korábban létrehozott Edge helyi és peremhálózati megosztásokhoz is csatlakozhat. Lásd a lépéseket 

    ![Kapcsolódás a helyi és a peremhálózati Felhőbeli megosztásokhoz](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-1.png) 
 
1. Adja hozzá az adatokat a helyi megosztáshoz.

    ![A fájl a peremhálózat helyi megosztására lett másolva](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-2.png) 
 
   Az adatok áthelyeződnek a felhőalapú megosztásba.

    ![A fájl át lett helyezve a Felhőbeli megosztásba](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-3.png)  

   Az adatok ezután a Felhőbeli megosztásról a Storage-fiókba kerülnek. Az adatmegjelenítéshez Storage Explorer vagy Azure Storage-t használhat a portálon.

    ![A Storage-fiókban tárolt adatellenőrzés](./media/azure-stack-edge-gpu-deploy-compute-module-simple/verify-data-4.png)
 
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
> [Helyi webes felhasználói felület használata Azure Stack Edge Pro felügyeletéhez](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md)
