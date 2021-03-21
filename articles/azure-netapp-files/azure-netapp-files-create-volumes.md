---
title: NFS-kötet létrehozása a Azure NetApp Fileshoz | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre NFS-köteteket Azure NetApp Files. Ismerje meg a szempontokat, például a használni kívánt verziót és az ajánlott eljárásokat.
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
ms.date: 09/24/2020
ms.author: b-juche
ms.openlocfilehash: 2cc9d3e0fb711a0662852ce4f2c5a08dc626f246
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854733"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>NFS-kötet létrehozása az Azure NetApp Files számára

Azure NetApp Files támogatja a kötetek NFS használatával történő létrehozását (NFSv3 és NFSv 4.1), a SMB3 vagy a kettős protokollt (NFSv3 és SMB). A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. Ebből a cikkből megtudhatja, hogyan hozhat létre NFS-köteteket. 

## <a name="before-you-begin"></a>Előkészületek 
* A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet.  
    Lásd: [Kapacitási készlet beállítása](azure-netapp-files-set-up-capacity-pool.md).   
* Az alhálózatot delegálni kell Azure NetApp Files.  
    Lásd: [alhálózat delegálása Azure NetApp Filesra](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Megfontolandó szempontok 

* A használni kívánt NFS-verzió meghatározása  
  A NFSv3 sokféle használati esetet kezelhet, és általában a legtöbb vállalati alkalmazásban üzembe helyezhető. Ellenőriznie kell, hogy az alkalmazás melyik verziót (NFSv3 vagy NFSv 4.1) igényli, és a megfelelő verzió használatával hozza létre a kötetet. Ha például az [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)-t használja, a nfsv 4.1-es fájl zárolása ajánlott a NFSv3-en keresztül. 

* Biztonság  
  A UNIX módú BITS (olvasás, írás és végrehajtás) támogatása a NFSv3 és a NFSv 4.1 esetében érhető el. A gyökérszintű hozzáférés szükséges az NFS-ügyfélen az NFS-kötetek csatlakoztatásához.

* Helyi felhasználó/csoport és LDAP-támogatás a NFSv 4.1-hez  
  A NFSv 4.1 jelenleg csak a kötetek számára támogatja a gyökérszintű hozzáférést. Lásd: [a nfsv 4.1 alapértelmezett tartományának konfigurálása Azure NetApp Fileshoz](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Ajánlott eljárás

* Győződjön meg arról, hogy a megfelelő csatlakoztatási utasításokat használja a kötethez.  Lásd: [kötetek csatlakoztatása vagy leválasztása Windows vagy Linux rendszerű virtuális gépekhez](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* Az NFS-ügyfélnek ugyanabban a VNet vagy összetartozó VNet kell lennie, mint a Azure NetApp Files kötetnek. A VNet kívülről való csatlakozás támogatott; azonban további késleltetést fog bevezetni, és csökkenti az általános teljesítményt.

* Győződjön meg arról, hogy az NFS-ügyfél naprakész, és futtatja az operációs rendszer legújabb frissítéseit.

## <a name="create-an-nfs-volume"></a>NFS-kötet létrehozása

1.  Kattintson a **kötetek** panelre a kapacitási készletek panelen. Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához. 

    ![A kötetek navigálása](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  A kötet létrehozása ablakban kattintson a **Létrehozás** gombra, és adja meg a következő mezők adatait az alapok lapon:   
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

        A megadott vnet rendelkeznie kell egy Azure NetApp Files delegált alhálózattal. A Azure NetApp Files szolgáltatás csak ugyanarról a vnet érhető el, vagy egy olyan vnet, amely ugyanabban a régióban található, mint a kötet vnet-társításon keresztül. Az Express Route használatával is elérheti a kötetet a helyszíni hálózatról.   

    * **Alhálózat**  
        Itt adhatja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell Azure NetApp Files. 
        
        Ha nem delegált alhálózatot, a kötet létrehozása lapon kattintson az **új létrehozása** lehetőségre. Ezután az alhálózat létrehozása lapon adja meg az alhálózati adatokat, majd válassza a **Microsoft. NetApp/kötetek** lehetőséget az alhálózat delegálásához Azure NetApp Files számára. Minden vnet csak egy alhálózat delegálható Azure NetApp Filesra.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Ha meglévő pillanatkép-szabályzatot szeretne alkalmazni a kötetre, kattintson a **speciális szakasz megjelenítése** lehetőségre a kibontásához, adja meg, hogy el kívánja-e rejteni a pillanatkép elérési útját, majd válassza ki a pillanatkép-szabályzatot a legördülő menüben. 

        A pillanatkép-házirendek létrehozásával kapcsolatos információkért lásd: [Pillanatkép-házirendek kezelése](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Speciális kijelölés megjelenítése](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Kattintson a **protokoll** elemre, majd hajtsa végre a következő műveleteket:  
    * Válassza az **NFS** lehetőséget a kötethez tartozó protokoll típusaként.   
    * Itt adhatja meg a **fájl elérési útját** , amelyet az új kötet exportálási útvonalának létrehozásához fog használni. A rendszer az exportálási útvonal használatával csatlakoztatja és éri el a kötetet.

        Az elérési út neve csak betűket, számokat és kötőjeleket („-”) tartalmazhat. A név 16–40 karakter hosszú lehet. 

        A fájl elérési útjának egyedinek kell lennie az egyes előfizetésekben és az egyes régiókban. 

    * Válassza ki a kötet NFS-verzióját (**NFSv3** vagy **nfsv 4.1**).  

    * Ha a NFSv 4.1-et használja, jelezze, hogy engedélyezni szeretné-e a kötet **Kerberos** -titkosítását.  

        További konfigurációk szükségesek, ha a NFSv 4.1-et használó Kerberost használ. Kövesse az [nfsv 4.1 Kerberos-titkosítás konfigurálása](configure-kerberos-encryption.md)című témakör utasításait.

    * Szükség esetén [az NFS-kötet exportálási házirendjét is konfigurálhatja](azure-netapp-files-configure-export-policy.md).

    ![NFS-protokoll meghatározása](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

4. A kötet részleteinek áttekintéséhez kattintson a **felülvizsgálat + létrehozás** elemre.  Ezután kattintson a **Létrehozás** gombra a kötet létrehozásához.

    A létrehozott kötet megjelenik a kötetek lapon. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.


## <a name="next-steps"></a>Következő lépések  

* [Az NFSv 4.1 alapértelmezett tartományának konfigurálása az Azure NetApp Fileshoz](azure-netapp-files-configure-nfsv41-domain.md)
* [Az NFSv 4.1 Kerberos-titkosításának konfigurálása](configure-kerberos-encryption.md)
* [Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Exportálási szabályzat konfigurálása NFS-kötethez](azure-netapp-files-configure-export-policy.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [Ismerje meg az Azure-szolgáltatások virtuális hálózati integrációját](../virtual-network/virtual-network-for-azure-services.md)