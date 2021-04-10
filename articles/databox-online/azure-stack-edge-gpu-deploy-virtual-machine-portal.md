---
title: Virtuális gépek üzembe helyezése a Azure Stack Edge Pro-ban a Azure Portal használatával
description: Megtudhatja, hogyan hozhat létre és kezelhet virtuális gépeket a Azure Stack Edge Pro-ban a Azure Portal használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro device so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: c11a89d91693075ca54c0689223dcf2af06df521
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568511"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-the-azure-portal"></a>Virtuális gépek üzembe helyezése az Azure Stack Edge Pro GPU-eszközön a Azure Portal használatával

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Létrehozhat és kezelhet virtuális gépeket Azure Stack peremhálózati eszközön Azure Portal, sablonok, Azure PowerShell parancsmagok és Azure CLI/Python parancsfájlok használatával. Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet virtuális gépeket a Azure Stack peremhálózati eszközön a Azure Portal használatával. 

Ez a cikk Azure Stack Edge Pro GPU-ra, Azure Stack Edge Pro R-re és Azure Stack Edge mini R-eszközökre vonatkozik. 

> [!IMPORTANT] 
> Javasoljuk, hogy engedélyezze a többtényezős hitelesítést azon felhasználó számára, aki a felhőből az eszközre telepített virtuális gépeket felügyeli.
        
## <a name="vm-deployment-workflow"></a>Virtuális gép üzembe helyezésének munkafolyamata

Az üzembe helyezési munkafolyamat magas szintű összefoglalása a következő:

1. Hálózati adapter engedélyezése a számítási feladatokhoz az Azure Stack Edge-eszközön. Ez egy virtuális kapcsolót hoz létre a megadott hálózati adapteren.
1. A virtuális gépek felhőalapú felügyeletének engedélyezése a Azure Portalból.
1. Töltse fel a VHD-t egy Azure Storage-fiókba Storage Explorer használatával. 
1. A feltöltött VHD használatával töltse le a VHD-t az eszközre, és hozzon létre egy virtuálisgép-rendszerképet a VHD-ből. 
1. Használja az előző lépésekben létrehozott erőforrásokat:
    1. A létrehozott virtuálisgép-rendszerkép.
    1. Azon hálózati adapterhez társított VSwitch, amelyen engedélyezte a számítást.
    1. A VSwitch társított alhálózat.

    És hozza létre vagy állítsa be a következő erőforrásokat beágyazottan:
    1. Virtuális gép neve, válassza ki a virtuális gép támogatott méretét, a bejelentkezési hitelesítő adatokat a virtuális géphez. 
    1. Új adatlemezek létrehozása vagy meglévő adatlemezek csatolása.
    1. Konfigurálja a virtuális gép statikus vagy dinamikus IP-címét. Ha statikus IP-címet ad meg, válassza ki az ingyenes IP-címet a számítási feladatokhoz engedélyezett hálózati adapter alhálózat tartományában.

    A fenti erőforrások használatával hozzon létre egy virtuális gépet.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené az eszközön lévő virtuális gépek létrehozását és felügyeletét a Azure Portalon keresztül, ügyeljen rá, hogy:

1. Végrehajtotta az Azure Stack Edge Pro-eszköz hálózati beállításait az [1. lépés: Azure stack Edge Pro-eszköz konfigurálása](./azure-stack-edge-gpu-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device)című cikkben leírtak szerint.

    1. Engedélyezte a hálózati adaptert a számítási feladatokhoz. Ez a hálózati adapter IP-címe virtuális kapcsoló létrehozásához használatos a virtuális gép telepítéséhez. Az eszköz helyi felhasználói felületén lépjen a **számítás** elemre. Válassza ki azt a hálózati adaptert, amelyet a virtuális kapcsoló létrehozásához használni fog.

        > [!IMPORTANT] 
        > Csak egy portot lehet beállítani a számítási feladatokhoz.

    1. A hálózati adapteren engedélyezze a számítást. Azure Stack Edge Pro létrehoz és felügyel egy, az adott hálózati adapternek megfelelő virtuális kapcsolót.

1. Hozzáférése van egy Windows vagy Linux rendszerű virtuális merevlemezhez, amelyet a létrehozni kívánt virtuális gép virtuálisgép-rendszerképének létrehozásához fog használni.

## <a name="deploy-a-vm"></a>Virtuális gép üzembe helyezése

A következő lépésekkel hozhat létre egy virtuális gépet az Azure Stack Edge-eszközön.

### <a name="add-a-vm-image"></a>Virtuálisgép-rendszerkép hozzáadása

1. Töltse fel a VHD-t egy Azure Storage-fiókba. Kövesse a [virtuális merevlemez feltöltése Azure Storage Explorer használatával](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md)című témakör lépéseit.

1. A Azure Portal nyissa meg az Azure Stack Edge-eszköz Azure Stack Edge-erőforrását. Ugrás az **Edge számítási > Virtual Machines**.

    ![VM-rendszerkép hozzáadása 1](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

1. Válassza a **Virtual Machines** lehetőséget az **Áttekintés** oldalra való ugráshoz. A virtuális gépek Felhőbeli felügyeletének **engedélyezése** .
    ![VM-rendszerkép hozzáadása 2](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)

1. Első lépésként adjon hozzá egy virtuálisgép-rendszerképet. A korábbi lépésben már feltöltött egy virtuális merevlemezt a Storage-fiókba. A virtuális MEREVLEMEZt a virtuálisgép-rendszerkép létrehozásához fogja használni.

    Válassza a **rendszerkép hozzáadása** lehetőséget a virtuális merevlemez a Storage-fiókból való letöltéséhez és az eszközhöz való hozzáadásához. A letöltési folyamat több percet is igénybe vehet, a virtuális merevlemez méretétől és a letöltéshez elérhető internetes sávszélességtől függően. 

    ![VM-rendszerkép hozzáadása 3](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-3.png)

1. A **rendszerkép hozzáadása panelen adja** meg a következő paramétereket. Válassza a **Hozzáadás** lehetőséget.


    |Paraméter  |Leírás  |
    |---------|---------|
    |Letöltés a Storage blobból    |Tallózással keresse meg a Storage-blob helyét abban a Storage-fiókban, ahová a VHD-t feltöltötte.         |
    |Letöltés ide    | Automatikusan az aktuális eszközre lesz beállítva, ahol a virtuális gépet üzembe helyezi.        |
    |Rendszerkép mentése másként      | A Storage-fiókba feltöltött virtuális merevlemezről létrehozott virtuálisgép-rendszerkép neve.        |
    |Operációs rendszer típusa     |Válassza ki a Windows vagy a Linux rendszerből a virtuális MEREVLEMEZt, amelyet a virtuálisgép-rendszerkép létrehozásához fog használni.         |
   

    ![VM-rendszerkép hozzáadása 4](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-6.png)

1. A rendszer letölti a virtuális merevlemezt, és létrehozza a virtuálisgép-rendszerképet. A rendszerkép létrehozása több percet is igénybe vehet. Ekkor megjelenik egy értesítés a virtuálisgép-rendszerkép sikeres befejezéséről.

    ![VM-rendszerkép hozzáadása 5](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-8.png)


1. A virtuális gép rendszerképének létrehozása **után a rendszer** hozzáadja a lemezképek panel lemezképek listájához.
    ![VM-rendszerkép hozzáadása 6](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-9.png)

    A **központi telepítések** panel frissítései jelzik az üzemelő példány állapotát.

    ![VM-rendszerkép hozzáadása 7](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-10.png)

    Az újonnan hozzáadott rendszerkép az **Áttekintés** oldalon is megjelenik.
    ![VM-rendszerkép hozzáadása 8](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-11.png)


### <a name="add-a-vm"></a>Virtuális gép hozzáadása

Az alábbi lépéseket követve hozzon létre egy virtuális gépet, miután létrehozott egy virtuálisgép-rendszerképet.

1. Az **Áttekintés** lapon válassza a **virtuális gép hozzáadása** elemet.

    ![1. VM hozzáadása](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-1.png)

1. Az **alapvető beállítások** lapon adja meg a következő paramétereket.


    |Paraméter |Leírás  |
    |---------|---------|
    |Virtuális gép neve     |         |
    |Kép     | Válassza ki az eszközön elérhető virtuálisgép-lemezképeket.        |
    |Méret     | Válasszon a [virtuális gépek támogatott méretei](azure-stack-edge-gpu-virtual-machine-sizes.md)közül.        |
    |Felhasználónév     | Használja az alapértelmezett Felhasználónév *azureuser*.        |
    |Hitelesítéstípus    | Válasszon az SSH nyilvános kulcs vagy a felhasználó által megadott jelszó közül.       |
    |Jelszó     | Adjon meg egy jelszót a virtuális gépre való bejelentkezéshez. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a meghatározott [összetettségi követelményeknek](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).        |
    |Jelszó megerősítése    | Adja meg újra a jelszót.        |


    ![2. virtuális gép hozzáadása](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-basics-1.png)

    Válassza a **Tovább: lemezek** lehetőséget.

1. A **lemezek** lapon lemezeket fog csatolni a virtuális géphez. 
    
    1. Dönthet úgy is, hogy **új lemezt hoz létre és csatol** , vagy **meglévő lemezt csatol**.

        ![3. virtuális gép hozzáadása](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-1.png)

    1. Válassza **a létrehozás lehetőséget, és csatlakoztassa az új lemezt**. Az **új lemez létrehozása** panelen adja meg a lemez nevét és méretét a GIB-ban.

        ![4. virtuális gép hozzáadása](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-2.png)

    1.  További lemezek hozzáadásához ismételje meg a fenti műveletet. A lemezek létrehozása után megjelennek a **lemezek** lapon.

        ![5. virtuális gép hozzáadása](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-disks-3.png)

        Válassza a **Tovább: Hálózatkezelés** lehetőséget.

1. A **hálózatkezelés** lapon konfigurálni fogja a virtuális gép hálózati kapcsolatát.

    
    |Paraméter  |Leírás |
    |---------|---------|
    |Virtuális hálózat    | A legördülő listából válassza ki az Azure Stack Edge-eszközön létrehozott virtuális kapcsolót, ha a hálózati adapteren a számítás engedélyezve van.    |
    |Alhálózat     | A program automatikusan kitölti ezt a mezőt azzal a hálózati adapterrel társított alhálózattal, amelyen engedélyezte a számítást.         |
    |IP-cím     | Adjon meg statikus vagy dinamikus IP-címet a virtuális géphez. A statikus IP-cím legyen elérhető, szabad IP-cím a megadott alhálózati tartományból.        |

    ![6. virtuális gép hozzáadása](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-networking-1.png)

    Válassza a **Next (tovább): felülvizsgálat + létrehozás** elemet.

1. A **felülvizsgálat + létrehozás** lapon tekintse át a virtuális gép specifikációit, és válassza a **Létrehozás** lehetőséget.

    ![7. virtuális gép hozzáadása](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-review-create-1.png)

1. A virtuális gép létrehozása megkezdődik, és akár 20 percet is igénybe vehet. A virtuális gépek létrehozásának figyeléséhez lépjen a **központi telepítések** lehetőségre.

    ![8. virtuális gép hozzáadása](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-deployments-page-1.png)

    
1. A virtuális gép sikeres létrehozása után az **áttekintő** oldal frissül, és megjeleníti az új virtuális gépet.

    ![9. virtuális gép hozzáadása](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-overview-page-1.png)

1. Válassza ki az újonnan létrehozott virtuális GÉPET a **virtuális gépekhez** való ugráshoz.

    ![10. virtuális gép hozzáadása](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-page-1.png)

    A részletek megtekintéséhez válassza ki a virtuális gépet. 

    ![11. virtuális gép hozzáadása](media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-details-1.png)

## <a name="connect-to-a-vm"></a>Csatlakozás virtuális géphez

Attól függően, hogy létrehozott-e Windows vagy Linux rendszerű virtuális gépet, a kapcsolódás lépései különbözőek lehetnek. Nem lehet csatlakozni az eszközön a Azure Portal keresztül központilag telepített virtuális gépekhez. A Linux vagy Windows rendszerű virtuális géphez való kapcsolódáshoz a következő lépéseket kell végrehajtania.

### <a name="connect-to-linux-vm"></a>Kapcsolódás Linux rendszerű virtuális géphez

A Linux rendszerű virtuális gépekhez való kapcsolódáshoz kövesse az alábbi lépéseket.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-linux.md)]

### <a name="connect-to-windows-vm"></a>Kapcsolódás Windows rendszerű virtuális géphez

A Windows rendszerű virtuális gépekhez való kapcsolódáshoz kövesse az alábbi lépéseket.

[!INCLUDE [azure-stack-edge-gateway-connect-vm](../../includes/azure-stack-edge-gateway-connect-virtual-machine-windows.md)]

## <a name="next-steps"></a>Következő lépések

Az Azure Stack Edge Pro-eszköz felügyeletével kapcsolatos információkért lásd: a[helyi webes felhasználói felület használata a Azure stack Edge Pro felügyeletéhez](azure-stack-edge-manage-access-power-connectivity-mode.md).