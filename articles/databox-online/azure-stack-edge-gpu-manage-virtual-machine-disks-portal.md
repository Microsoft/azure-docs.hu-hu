---
title: Virtuális gépek lemezeinek kezelése Azure Stack Edge Pro GPU, Pro R, mini R használatával Azure Portal
description: Megtudhatja, hogyan kezelheti a lemezeket, beleértve az adatlemezek hozzáadását vagy leválasztását a Azure Stack Edge Pro GPU-n üzembe helyezett virtuális gépeken, Azure Stack Edge Pro R-re, és Azure Stack Edge mini R-t a Azure Portaln keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: dff3f4bafdb35d89e8f1792c1aca68824a9bc685
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080320"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>A Azure Portal segítségével kezelheti a virtuális gépek lemezeit a Azure Stack Edge Pro GPU-ban

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

A Azure Portal használatával kiépítheti az Azure Stack Edge Pro-eszközön üzembe helyezett virtuális gépeken található lemezeket. A lemezeket a helyi Azure Resource Manager használatával kell kiépíteni az eszközön, és fel kell használni az eszköz kapacitását. Az olyan műveletek, mint például a lemez hozzáadása, a lemez leválasztása a Azure Portal keresztül végezhető el, ami lehetővé teszi a helyi Azure Resource Manager meghívását a tároló kiépítéséhez. 

Ez a cikk azt ismerteti, hogyan lehet adatlemezt hozzáadni egy meglévő virtuális géphez, leválasztani egy adatlemezt, és végül átméretezni a virtuális gépet a Azure Portal használatával. 

        
## <a name="about-disks-on-vms"></a>A virtuális gépek lemezei

A virtuális gépnek operációsrendszer-lemeze és adatlemeze is lehet. Az eszközön telepített összes virtuális gép egy csatlakoztatott operációsrendszer-lemezzel rendelkezik. Az operációsrendszer-lemez rendelkezik egy előre telepített operációs rendszerrel, amely a virtuális gép létrehozásakor lett kiválasztva. Ez a lemez tartalmazza a rendszerindító kötetet.

> [!NOTE]
> Az eszközön a virtuális gép operációsrendszer-lemezének mérete nem módosítható. Az operációsrendszer-lemez méretét a kiválasztott VM-méret határozza meg. 


Egy másik oldali adatlemez egy felügyelt lemez, amely az eszközön futó virtuális géphez van csatlakoztatva. Az alkalmazásadatok tárolására használt adatlemez. Az adatlemezek általában SCSI-meghajtók. A virtuális gép mérete határozza meg, hány adatlemezt tud csatlakoztatni egy virtuális géphez. Alapértelmezés szerint a Premium Storage a lemezek üzemeltetésére szolgál.

Előfordulhat, hogy egy eszközön üzembe helyezett virtuális gép időnként tartalmaz egy ideiglenes lemezt. Az ideiglenes lemez rövid távú tárolást biztosít az alkalmazások és folyamatok számára, és kizárólag az olyan adattárolók tárolására szolgál, mint például az oldal vagy a lapozófájlok. Előfordulhat, hogy az ideiglenes lemezen lévő adatvesztés egy karbantartási esemény vagy egy virtuális gép újratelepítése során elvész. A virtuális gép sikeres újraindítása során az ideiglenes lemezen tárolt adatmennyiség továbbra is fennáll. 


## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdené az eszközön futó virtuális gépek lemezeinek kezelését a Azure Portal keresztül, ügyeljen rá, hogy:


1. Egy aktivált Azure Stack Edge Pro GPU-eszközhöz férhet hozzá. Egy hálózati adaptert is engedélyezett a számítási feladatokhoz az eszközön. Ez a művelet létrehoz egy virtuális kapcsolót a virtuális GÉPEN lévő hálózati adapteren. 
    1. Az eszköz helyi felhasználói felületén lépjen a **számítás** elemre. Válassza ki azt a hálózati adaptert, amelyet a virtuális kapcsoló létrehozásához használni fog.

        > [!IMPORTANT] 
        > Csak egy portot lehet beállítani a számítási feladatokhoz.

    1. A hálózati adapteren engedélyezze a számítást. Azure Stack Edge Pro GPU létrehoz és felügyel egy, az adott hálózati adapternek megfelelő virtuális kapcsolót.

1. Legalább egy virtuális gép telepítve van az eszközön. A virtuális gép létrehozásához tekintse meg a [virtuális gép üzembe helyezése a Azure stack Edge Pro](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)-ban című témakör utasításait a Azure Portal használatával.



## <a name="add-a-data-disk"></a>Adatlemez hozzáadása

Az alábbi lépéseket követve hozzáadhat egy lemezt az eszközön üzembe helyezett virtuális géphez. 

1. Nyissa meg azt a virtuális gépet, amelyhez adatlemezt kíván hozzáadni, majd nyissa meg az **Áttekintés** lapot. Válassza a **Lemezek** lehetőséget.
    
    ![Lemezek kiválasztása az Áttekintés oldalon](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. A **lemezek** panelen az **adatlemezek** területen válassza a **Létrehozás lehetőséget, és csatlakoztassa az új lemezt**.

    ![Új lemez létrehozása és csatolása](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. Az **új lemez létrehozása** panelen adja meg a következő paramétereket:

    
    |Mező  |Leírás  |
    |---------|---------|
    |Név     | Az erőforráscsoporthoz tartozó egyedi név. Az adatlemez létrehozása után a név nem módosítható.     |
    |Méret| Az adatlemez mérete a GiB-ban. Az adatlemezek maximális méretét a kiválasztott VM-méret határozza meg. Egy lemez kiépítésekor figyelembe kell vennie az eszközön található tényleges helyet és a kapacitást használó egyéb virtuális gépek munkaterheléseit is.  |         

    ![Új lemez létrehozása panel](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    Válassza **az OK** , majd a Folytatás lehetőséget.

1. Az **Áttekintés** lap **lemezek** területén megjelenik az új lemeznek megfelelő bejegyzés. Fogadja el az alapértelmezett értéket, vagy rendeljen hozzá egy érvényes logikai egységet (LUN) a lemezhez, majd válassza a **Mentés** lehetőséget. A LUN egy SCSI-lemez egyedi azonosítója. További információ: [Mi az a LUN?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun).

    ![Új lemez az Áttekintés oldalon](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Ekkor megjelenik egy értesítés arról, hogy a lemez létrehozása folyamatban van. A lemez sikeres létrehozása után a rendszer frissíti a virtuális gépet. 

    ![Értesítés lemez létrehozására](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. Váltson vissza az **Áttekintés** lapra. A lemezek listájának frissítése az újonnan létrehozott adatlemez megjelenítéséhez.

    ![Az adatlemezek frissített listája](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Adatlemez módosítása

Kövesse az alábbi lépéseket az eszközön üzembe helyezett virtuális gépekhez társított lemez módosításához.

1. Nyissa meg azt a virtuális gépet, amelyen a módosítandó adatlemez található, és nyissa meg az **Áttekintés** lapot. Válassza a **Lemezek** lehetőséget.

1. Az adatlemezek listájában válassza ki a módosítani kívánt lemezt. A kiválasztott lemez jobb szélén válassza a Szerkesztés ikont (ceruza).  

    ![Válassza ki a módosítani kívánt lemezt](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. A **lemez módosítása** panelen csak a lemez mérete módosítható. A lemezhez társított név nem módosítható a létrehozás után. Módosítsa a **méretet** , és mentse a módosításokat.

    ![Adatlemez méretének módosítása](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > Az adatlemezek kibontása csak akkor lehetséges, ha nem tudja csökkenteni a lemezt.

1. Az **Áttekintés** oldalon a lemezek listája frissül a frissített lemez megjelenítéséhez.


## <a name="attach-an-existing-disk"></a>Meglévő lemez csatlakoztatása

Kövesse az alábbi lépéseket egy meglévő lemez csatlakoztatásához az eszközön üzembe helyezett virtuális géphez.

1. Nyissa meg azt a virtuális gépet, amelyhez csatlakoztatni kívánja a meglévő lemezt, majd nyissa meg az **Áttekintés** lapot. Válassza a **Lemezek** lehetőséget.
    
    ![Lemezek kiválasztása ](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. A **lemezek** panel **adatlemezek** területén válassza a **meglévő lemez csatolása** lehetőséget.

    ![Válassza a meglévő lemez csatolása lehetőséget.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Fogadja el az alapértelmezett logikai egységet, vagy rendeljen hozzá egy érvényes logikai egységet. Válasszon ki egy meglévő adatlemezt a legördülő listából. Kattintson a Mentés gombra.

    ![Meglévő lemez kiválasztása](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    Válassza a **Mentés** és Folytatás lehetőséget.

1. Ekkor megjelenik egy értesítés arról, hogy a virtuális gép frissült. A virtuális gép frissítése után váltson vissza az **Áttekintés** lapra. Frissítse a lapot az újonnan csatlakoztatott lemez megtekintéséhez az adatlemezek listájában.

    ![Az adatlemezek frissített listájának megtekintése az Áttekintés oldalon](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Adatlemez leválasztása

Az alábbi lépéseket követve leválaszthatja vagy eltávolíthatja az eszközön üzembe helyezett virtuális gépekhez társított adatlemezeket.

> [!NOTE]
> - A virtuális gép futása közben eltávolíthat egy adatlemezt. Győződjön meg arról, hogy a lemez nem használja aktívan a lemezt a virtuális gépről való leválasztás előtt.
> - Ha leválaszt egy lemezt, az nem törlődik automatikusan.

1. Nyissa meg azt a virtuális gépet, amelyről egy adatlemezt szeretne leválasztani, és nyissa meg az **Áttekintés** lapot. Válassza a **Lemezek** lehetőséget.

    ![Lemezek kiválasztása](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. A lemezek listájában válassza ki a leválasztani kívánt lemezt. A kiválasztott lemez jobb szélén kattintson a leválasztás ikonra (keresztre). A kiválasztott bejegyzés le lesz választva. Kattintson a **Mentés** gombra. 

    ![Válassza ki a leválasztani kívánt lemezt](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. A lemez leválasztása után a rendszer frissíti a virtuális gépet. Az adatlemezek frissített listájának megtekintéséhez frissítse az **Áttekintés** lapot.

    ![Mentés kiválasztása](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan helyezhet üzembe virtuális gépeket a Azure Stack Edge Pro-eszközön, tekintse meg [a virtuális gépek telepítése a Azure Portal segítségével](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)című témakört.
