---
title: Virtuális gépek hálózati adapterek kezelése a Azure Stack Edge Pro-n keresztül a Azure Portal
description: Ismerje meg, hogyan kezelheti a hálózati adaptereket a Azure Stack Edge Pro GPU-n üzembe helyezett virtuális gépeken a Azure Portal használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/23/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage network interfaces on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 84077f174fabd02afcd5171e8d365e8cbd3a52c2
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027649"
---
# <a name="use-the-azure-portal-to-manage-network-interfaces-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>A Azure Portal segítségével kezelheti a virtuális gépeken lévő hálózati adaptereket a Azure Stack Edge Pro GPU-ban

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Létrehozhat és kezelhet virtuális gépeket Azure Stack peremhálózati eszközön Azure Portal, sablonok, Azure PowerShell parancsmagok és Azure CLI/Python parancsfájlok használatával. Ez a cikk azt ismerteti, hogyan kezelhetők a hálózati adapterek egy Azure Stack peremhálózati eszközön futó virtuális gépen a Azure Portal használatával. 

Virtuális gép létrehozásakor meg kell adnia egy létrehozni kívánt virtuális hálózati adaptert. A létrehozást követően érdemes lehet egy vagy több hálózati adaptert hozzáadni a virtuális géphez. Előfordulhat, hogy módosítani szeretné egy meglévő hálózati adapter alapértelmezett hálózati adapterének beállításait is.

Ez a cikk azt ismerteti, hogyan lehet hálózati adaptert hozzáadni egy meglévő virtuális géphez, módosítani a meglévő beállításokat, például az IP-típust (statikus vagy dinamikus), végül eltávolítani vagy leválasztani egy meglévő felületet. 

        
## <a name="about-network-interfaces-on-vms"></a>A virtuális gépek hálózati adapterei

A hálózati adapterek lehetővé teszik az Azure-beli és a helyszíni erőforrásokkal folytatott kommunikációhoz Azure Stack Edge Pro-eszközön futó virtuális gép (VM) használatát. Amikor engedélyez egy portot a számítási hálózat számára az eszközön, a rendszer létrehoz egy virtuális kapcsolót az adott hálózati adapteren. A rendszer ezt a virtuális kapcsolót használja a számítási feladatok, például virtuális gépek vagy tároló alkalmazások üzembe helyezéséhez az eszközön. 

Az eszköz csak egy virtuális kapcsolót, de több virtuális hálózati adaptert támogat. A virtuális gépen lévő összes hálózati adapterhez statikus vagy dinamikus IP-cím tartozik. A virtuális gépen több hálózati adapterhez hozzárendelt IP-címek esetében bizonyos képességek engedélyezve vannak a virtuális gépen. A virtuális gép például több webhelyet vagy szolgáltatást tárolhat különböző IP-címekkel és SSL-tanúsítványokkal egyetlen kiszolgálón. Az eszközön található virtuális gép hálózati virtuális készülékként, például tűzfallal vagy terheléselosztóként is használható. <!--Is it possible to do that on ASE?-->

<!--There is a limit to how many virtual network interfaces can be created on the virtual switch on your device. See the Azure Stack Edge Pro limits article for details.--> 


## <a name="prerequisites"></a>Előfeltételek

Mielőtt megkezdené az eszközön lévő virtuális gépek kezelését a Azure Portalon keresztül, ügyeljen rá, hogy:

1. Engedélyezte a hálózati adaptert a számítási feladatokhoz az eszközön. Ez a művelet létrehoz egy virtuális kapcsolót a virtuális GÉPEN lévő hálózati adapteren. 
    1. Az eszköz helyi felhasználói felületén lépjen a **számítás** elemre. Válassza ki azt a hálózati adaptert, amelyet a virtuális kapcsoló létrehozásához használni fog.

        > [!IMPORTANT] 
        > Csak egy portot lehet beállítani a számítási feladatokhoz.

    1. A hálózati adapteren engedélyezze a számítást. Azure Stack Edge Pro GPU létrehoz és felügyel egy, az adott hálózati adapternek megfelelő virtuális kapcsolót.

1. Legalább egy virtuális gépet üzembe helyezett az eszközön. A virtuális gép létrehozásához tekintse meg a [virtuális gép üzembe helyezése a Azure stack Edge Pro](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)-ban című témakör utasításait a Azure Portal használatával.

1. A virtuális gépnek **leállított** állapotban kell lennie. A virtuális gép leállításához lépjen a **Virtual machines > áttekintés** elemre, és válassza ki a leállítani kívánt virtuális gépet. A virtuális gép tulajdonságai lapon válassza a **Leállítás** lehetőséget, majd válassza az **Igen** lehetőséget, ha a rendszer megerősítést kér. A hálózati adapterek hozzáadása, szerkesztése vagy törlése előtt le kell állítania a virtuális gépet.

    ![VIRTUÁLIS gép leállítása a virtuális gép tulajdonságai lapról](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="add-a-network-interface"></a>Hálózati adapter hozzáadása

A következő lépésekkel adhat hozzá egy hálózati adaptert az eszközön üzembe helyezett virtuális géphez. 

1. Nyissa meg a leállított virtuális gépet, és lépjen a virtuális gép **tulajdonságai** lapra. Válassza a **Hálózat** lehetőséget.
    
    ![Válassza a hálózatkezelés a virtuális gép tulajdonságai lapon](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-1.png)

2. A **hálózatkezelés** panelen, a parancssorban válassza a **+ hálózati adapter hozzáadása** lehetőséget.

    ![Válassza a hálózati adapter hozzáadása lehetőséget](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-2.png)

3. A **hálózati adapter hozzáadása** panelen adja meg a következő paramétereket:

    
    |1\. oszlop  |2\. oszlop  |
    |---------|---------|
    |Name     | Az erőforráscsoporthoz tartozó egyedi név. A hálózati adapter létrehozása után a név nem módosítható. Ha egyszerűen több hálózati adaptert szeretne kezelni, használja az [elnevezési konvenciókban](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming)megadott javaslatokat.     |
    |Virtuális hálózat| Az eszközön létrehozott virtuális kapcsolóhoz társított virtuális hálózat, amikor a hálózati adapteren engedélyezi a számítást. Az eszközhöz csak egy virtuális hálózat van társítva.         |         
    |Alhálózat     | Egy alhálózat a kiválasztott virtuális hálózaton belül. A program automatikusan kitölti ezt a mezőt azzal a hálózati adapterrel társított alhálózattal, amelyen engedélyezte a számítást.         |       
    |IP-hozzárendelés   | Statikus vagy dinamikus IP-cím a hálózati adapterhez. A statikus IP-cím legyen elérhető, szabad IP-cím a megadott alhálózati tartományból. Ha a környezetben létezik DHCP-kiszolgáló, válassza a dinamikus lehetőséget.        | 

    ![Hálózati adapter hozzáadása](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-3.png)

4. Ekkor megjelenik egy értesítés arról, hogy a hálózati adapter létrehozása folyamatban van.

    ![Értesítés a hálózati adapter létrehozásakor](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-4.png)

5.  A hálózati adapter sikeres létrehozása után a hálózati adapterek listája frissül, és megjeleníti az újonnan létrehozott felületet.

    ![A hálózati adapterek frissített listája](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/add-nic-5.png)


## <a name="edit-a-network-interface"></a>Hálózati adapter szerkesztése

Az alábbi lépéseket követve szerkesztheti az eszközön üzembe helyezett virtuális gépekhez társított hálózati adaptereket.

1. Nyissa meg a leállított virtuális gépet, és lépjen a virtuális gép **tulajdonságai** lapra. Válassza a **Hálózat** lehetőséget.

1. A hálózati adapterek listájában válassza ki a szerkeszteni kívánt felületet. A kiválasztott hálózati adapter jobb szélén kattintson a Szerkesztés ikonra (ceruza).  

    ![Válassza ki a szerkeszteni kívánt hálózati adaptert](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-1.png)

1. A **hálózati adapter szerkesztése** panelen csak a hálózati adapter IP-hozzárendelését lehet megváltoztatni. A hálózati adapterhez társított név, virtuális hálózat és alhálózat létrehozása után nem módosítható. Módosítsa az **IP-hozzárendelést** statikusra, és mentse a módosításokat.

    ![Hálózati adapter IP-hozzárendelésének módosítása](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/edit-nic-2.png)

1. A hálózati adapter frissítéseinek listája a frissített hálózati adapter megjelenítéséhez.


## <a name="detach-a-network-interface"></a>Hálózati adapter leválasztása

Az alábbi lépéseket követve leválaszthatja vagy eltávolíthatja az eszközön üzembe helyezett virtuális gépekhez társított hálózati adaptereket.

1. Nyissa meg a leállított virtuális gépet, és lépjen a virtuális gép **tulajdonságai** lapra. Válassza a **Hálózat** lehetőséget.

1. A hálózati adapterek listájában válassza ki a szerkeszteni kívánt felületet. A kiválasztott hálózati adapter jobb szélén kattintson a leválasztás ikonra (kikapcsolás).  

    ![Válassza ki a leválasztani kívánt hálózati adaptert](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/detach-nic-1.png)

1. Miután az interfész teljesen le van választva, a rendszer frissíti a hálózati adapterek listáját a fennmaradó felületek megjelenítéséhez.

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan helyezhet üzembe virtuális gépeket a Azure Stack Edge Pro-eszközön, tekintse meg [a virtuális gépek telepítése a Azure Portal segítségével](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)című témakört.
