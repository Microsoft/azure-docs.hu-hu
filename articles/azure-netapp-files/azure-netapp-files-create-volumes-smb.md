---
title: SMB-kötet létrehozása a Azure NetApp Fileshoz | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre egy SMB3-kötetet a Azure NetApp Filesban. A Active Directory kapcsolatok és a tartományi szolgáltatások követelményeinek megismerése.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: 91f4f90658281282cdcb01b091bd9c9647d8d702
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635486"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>SMB-kötet létrehozása az Azure NetApp Files számára

Azure NetApp Files támogatja a kötetek NFS használatával történő létrehozását (NFSv3 és NFSv 4.1), a SMB3 vagy a kettős protokollt (NFSv3 és SMB). A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. Ez a cikk bemutatja, hogyan hozhat létre SMB3-köteteket.

## <a name="before-you-begin"></a>Előkészületek 

* A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet. Lásd: [Kapacitási készlet beállítása](azure-netapp-files-set-up-capacity-pool.md).     
* Az alhálózatot delegálni kell Azure NetApp Files. Lásd: [alhálózat delegálása Azure NetApp Filesra](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Active Directory kapcsolatok konfigurálása 

Az SMB-kötetek létrehozása előtt létre kell hoznia egy Active Directory-kapcsolatokat. Ha nem konfigurált Active Directory kapcsolatokat az Azure NetApp-fájlokhoz, kövesse az [Active Directory-kapcsolatok létrehozása és kezelése](create-active-directory-connections.md)című témakör útmutatását.

## <a name="add-an-smb-volume"></a>SMB-kötet hozzáadása

1. Kattintson a **kötetek** panelre a kapacitási készletek panelen. 

    ![A kötetek navigálása](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához.  
    Megjelenik a kötet létrehozása ablak.

3. A kötet létrehozása ablakban kattintson a **Létrehozás** gombra, és adja meg a következő mezők adatait az alapok lapon:   
    * **Kötet neve**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A kötet nevének egyedinek kell lennie az egyes kapacitási készleteken belül. Legalább három karakter hosszúnak kell lennie. Bármely alfanumerikus karaktert használhat.   

        `default` `bin` A vagy a kötet neve nem használható.

    * **Kapacitási készlet**  
        Határozza meg azt a kapacitási készletet, amelyben létre szeretné hozni a kötetet.

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Átviteli sebesség (MiB/S)**   
        Ha a kötet kézi QoS-kapacitású készletben lett létrehozva, akkor határozza meg a kötet kívánt átviteli sebességét.   

        Ha a kötet egy automatikus QoS-kapacitási készletben jön létre, az ebben a mezőben megjelenő érték (kvóta x szolgáltatási szint átviteli sebessége).   

    * **Virtuális hálózat**  
        Itt adhatja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet.  

        A megadott VNet rendelkeznie kell egy Azure NetApp Files delegált alhálózattal. A Azure NetApp Files szolgáltatás csak ugyanarról a VNet érhető el, vagy egy olyan VNet, amely ugyanabban a régióban található, mint a kötet VNet-társításon keresztül. Az Express Route használatával is elérheti a kötetet a helyszíni hálózatról.   

    * **Alhálózat**  
        Itt adhatja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell Azure NetApp Files. 
        
        Ha még nem delegált alhálózatot, kattintson az **új létrehozása** lehetőségre a kötet létrehozása lapon. Ezután az alhálózat létrehozása lapon adja meg az alhálózati adatokat, majd válassza a **Microsoft. NetApp/kötetek** lehetőséget az alhálózat delegálásához Azure NetApp Files számára. Minden VNet csak egy alhálózat delegálható Azure NetApp Filesra.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Ha meglévő pillanatkép-szabályzatot szeretne alkalmazni a kötetre, kattintson a **speciális szakasz megjelenítése** lehetőségre a kibontásához, adja meg, hogy el kívánja-e rejteni a pillanatkép elérési útját, majd válassza ki a pillanatkép-szabályzatot a legördülő menüben. 

        A pillanatkép-házirendek létrehozásával kapcsolatos információkért lásd: [Pillanatkép-házirendek kezelése](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Speciális kijelölés megjelenítése](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Kattintson a **protokoll** elemre, és hajtsa végre a következő információkat:  
    * Válassza az **SMB** lehetőséget a kötethez tartozó protokoll típusaként. 
    * Válassza ki a **Active Directory** -kapcsolatokat a legördülő listából.
    * Adja meg a megosztott kötet nevét a  **megosztási névben**.

    ![SMB protokoll meghatározása](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. A kötet részleteinek áttekintéséhez kattintson a **felülvizsgálat + létrehozás** elemre.  Ezután kattintson a **Létrehozás** gombra az SMB-kötet létrehozásához.

    A létrehozott kötet megjelenik a kötetek lapon. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.

## <a name="control-access-to-an-smb-volume"></a>SMB-kötethez való hozzáférés szabályozása  

Az SMB-kötetekhez való hozzáférést az engedélyek szabályozzák.  

### <a name="share-permissions"></a>Megosztási engedélyek  

Alapértelmezés szerint az új kötet a **mindenki/teljes hozzáférés** megosztás engedélyekkel rendelkezik. A Tartománygazdák csoport tagjai a Azure NetApp Files kötethez használt számítógépfiók számítógép-kezelés funkciójával módosíthatják a megosztási engedélyeket.

![SMB-csatlakoztatási útvonal ](../media/azure-netapp-files/smb-mount-path.png) 
 ![ beállítása megosztási engedélyek](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS-fájl és mappa engedélyei  

A fájlokra vagy mappákra vonatkozó engedélyeket a Windows SMB-ügyfélben található objektum tulajdonságainak **Biztonság** lapján állíthatja be.
 
![Fájl-és mappa engedélyeinek beállítása](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Következő lépések  

* [Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [SMB – gyakori kérdések](./azure-netapp-files-faqs.md#smb-faqs)
* [SMB-vagy kettős protokollú kötetek hibáinak megoldása](troubleshoot-dual-protocol-volumes.md)
* [Ismerje meg az Azure-szolgáltatások virtuális hálózati integrációját](../virtual-network/virtual-network-for-azure-services.md)
* [Új Active Directory erdő telepítése az Azure CLI-vel](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
