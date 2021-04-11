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
ms.date: 03/29/2021
ms.author: b-juche
ms.openlocfilehash: eeeaf01dd20e5b309884a01f954ceca576cbcbb9
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259625"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>SMB-kötet létrehozása az Azure NetApp Files számára

Azure NetApp Files támogatja a kötetek NFS használatával történő létrehozását (NFSv3 és NFSv 4.1), a SMB3 vagy a kettős protokollt (NFSv3 és SMB). A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. 

Ez a cikk bemutatja, hogyan hozhat létre SMB3-köteteket. Az NFS-kötetek esetében tekintse meg [az NFS-kötet létrehozása](azure-netapp-files-create-volumes.md)című témakört. A kettős protokollú kötetek esetében lásd: [kettős protokollú kötet létrehozása](create-volumes-dual-protocol.md).

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
    * Ha engedélyezni szeretné a folyamatos rendelkezésre állást az SMB-kötethez, válassza a **folyamatos rendelkezésre állás engedélyezése** lehetőséget.    

        > [!IMPORTANT]   
        > Az SMB folyamatos rendelkezésre állása szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. Be kell küldenie egy várólistára vonatkozó kérelmet a szolgáltatás eléréséhez az **[Azure NETAPP Files SMB folyamatos rendelkezésre állási megosztások nyilvános előzetese-várólista beküldési lapján](https://aka.ms/anfsmbcasharespreviewsignup)**. Várjon egy hivatalos visszaigazoló e-mailt a Azure NetApp Files csapattól a folyamatos rendelkezésre állási funkció használata előtt.   
        > 
        > A folyamatos rendelkezésre állást csak az SQL-munkaterhelések esetében ajánlott engedélyezni. Az SMB folyamatos rendelkezésre állási megosztások használata a SQL Servertól eltérő munkaterhelések esetén *nem* támogatott. Ez a funkció jelenleg Windows SQL Server rendszeren támogatott. A Linux SQL Server jelenleg nem támogatott. Ha nem rendszergazdai (tartományi) fiókot használ a SQL Server telepítéséhez, győződjön meg arról, hogy a fiók rendelkezik a szükséges biztonsági jogosultságokkal. Ha a tartományi fiók nem rendelkezik a szükséges biztonsági jogosultságokkal ( `SeSecurityPrivilege` ), és a jogosultság nem állítható be a tartományi szinten, akkor a jogosultságot a fiókhoz a Active Directory kapcsolatok **biztonsági jogosultságok felhasználói** mezőjének használatával adhatja meg. Lásd: [Active Directory-kapcsolatok létrehozása](create-active-directory-connections.md#create-an-active-directory-connection).

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![Az SMB-kötetek létrehozásának protokoll lapját ismertető képernyőkép.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. A kötet részleteinek áttekintéséhez kattintson a **felülvizsgálat + létrehozás** elemre.  Ezután kattintson a **Létrehozás** gombra az SMB-kötet létrehozásához.

    A létrehozott kötet megjelenik a kötetek lapon. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.

## <a name="control-access-to-an-smb-volume"></a>SMB-kötethez való hozzáférés szabályozása  

Az SMB-kötetekhez való hozzáférést az engedélyek szabályozzák.  

### <a name="share-permissions"></a>Megosztási engedélyek  

Alapértelmezés szerint az új kötet a **mindenki/teljes hozzáférés** megosztás engedélyekkel rendelkezik. A Tartománygazdák csoport tagjai a következőképpen módosíthatják a megosztási engedélyeket:  

1. Képezze le a megosztást egy meghajtóra.  
2. Kattintson a jobb gombbal a meghajtóra, válassza a **Tulajdonságok** lehetőséget, majd lépjen a **Biztonság** lapra.

[![Megosztási engedélyek beállítása](../media/azure-netapp-files/set-share-permissions.png)](../media/azure-netapp-files/set-share-permissions.png#lightbox)

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
