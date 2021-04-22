---
title: SMB-kötet létrehozása a Azure NetApp Files | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre SMB3-kötetet a Azure NetApp Files. Ismerje meg a kapcsolatok és Active Directory követelményeit.
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
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: d3ca94524c334a20f5ee75e5300ad419fa1542c5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873270"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>SMB-kötet létrehozása az Azure NetApp Files számára

Azure NetApp Files NFS (NFSv3 és NFSv4.1), SMB3 vagy kettős protokoll (NFSv3 és SMB) használatával támogatja a kötetek létrehozását. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. 

Ez a cikk bemutatja, hogyan hozhat létre SMB3-kötetet. NFS-kötetek esetén lásd: [NFS-kötet létrehozása.](azure-netapp-files-create-volumes.md) A kettős protokollú kötetekért [lásd: Kettős protokollú kötet létrehozása.](create-volumes-dual-protocol.md)

## <a name="before-you-begin"></a>Előkészületek 

* A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet. Lásd: [Kapacitáskészlet beállítása.](azure-netapp-files-set-up-capacity-pool.md)     
* Az alhálózatot delegálni kell a Azure NetApp Files. Lásd: [Alhálózat delegálása](azure-netapp-files-delegate-subnet.md)Azure NetApp Files.

## <a name="configure-active-directory-connections"></a>A Active Directory konfigurálása 

Az SMB-kötet létrehozása előtt létre kell hoznia egy Active Directory kapcsolatot. Ha még nem konfigurálta az Azure NetApp Active Directory kapcsolatokat, kövesse a következőben ismertetett utasításokat: Create [and manage Active Directory connections ..](create-active-directory-connections.md)

## <a name="add-an-smb-volume"></a>SMB-kötet hozzáadása

1. Kattintson **a Kötetek panelre** a Kapacitáskészletek panelen. 

    ![Lépjen a Kötetek lapra](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához.  
    Megjelenik a Kötet létrehozása ablak.

3. A Kötet létrehozása ablakban kattintson a **Létrehozás** gombra, és adja meg az Alapvető beállítások lap következő mezőinek adatait:   
    * **Kötet neve**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A kötet nevének minden kapacitáskészleten belül egyedinek kell lennie. Legalább három karakter hosszúságúnak kell lennie. Bármilyen alfanumerikus karaktert használhat.   

        Kötetnévként nem `default` `bin` használhatja a vagy a et.

    * **Kapacitáskészlet**  
        Adja meg a kapacitáskészletet, ahol létre szeretné hozatni a kötetet.

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Átviteli sebesség (MiB/S)**   
        Ha a kötet manuális QoS-kapacitáskészletben jön létre, adja meg a kötet kívánt átviteli sebességét.   

        Ha a kötet egy automatikus QoS-kapacitáskészletben jön létre, az ebben a mezőben megjelenített érték (kvóta x szolgáltatási szint átviteli sebesség).   

    * **Virtuális hálózat**  
        Adja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről hozzá szeretne férni a kötethez.  

        A megadott virtuális hálózatnak delegált alhálózatot kell Azure NetApp Files. A Azure NetApp Files szolgáltatás csak ugyanatról a virtuális hálózatról vagy egy olyan virtuális hálózatról érhető el, amely ugyanabban a régióban van, mint a kötet virtuális hálózatok közötti társviszony-létesítésen keresztül. A kötetet a helyszíni hálózatról is elérheti az Express Route-on keresztül.   

    * **Alhálózat**  
        Adja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell a Azure NetApp Files. 
        
        Ha még nem delegált alhálózatot, a Kötet létrehozása lapon kattintson az **Új** létrehozása elemre. Ezután az Alhálózat létrehozása lapon adja meg az alhálózati adatokat, és válassza a **Microsoft.NetApp/volumes** lehetőséget az alhálózat delegálhatja a Azure NetApp Files. Minden virtuális hálózatban csak egy alhálózat delegálható a Azure NetApp Files.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Ha meglévő pillanatkép-házirendet szeretne alkalmazni a  kötetre, kattintson a Speciális szakasz megjelenítése elemre annak kibontásához, adja meg, hogy el szeretné-e rejteni a pillanatkép elérési útját, majd válasszon ki egy pillanatkép-szabályzatot a lekért menüben. 

        A pillanatkép-házirendek létrehozásával kapcsolatos információkért lásd: [Pillanatkép-házirendek kezelése.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

        ![Speciális kijelölés megjelenítése](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Kattintson **a Protokoll elemre,** és töltse ki a következő információkat:  
    * A **kötet protokolltípusaként** válassza az SMB lehetőséget. 
    * Válassza ki **Active Directory** kapcsolatát a legördülő listából.
    * Adja meg a megosztott kötet nevét a **Megosztás neve alatt.**
    * Ha engedélyezni szeretné az SMB3 titkosítását, válassza az **SMB3 protokolltitkosítás engedélyezése lehetőséget.**   
        Ez a funkció lehetővé teszi az SMB3-adatok titkosítását. Az SMB3-titkosítást nem használó SMB-ügyfelek nem fogják tudni elérni ezt a kötetet.  A rendszer a beállítástól függetlenül titkosítja az adatok titkosítását.  
        További [információ: SMB-titkosítás –](azure-netapp-files-faqs.md#smb-encryption-faqs) gyakori kérdések. 

        Az **SMB3 protokolltitkosítási** szolgáltatás jelenleg előzetes verzióban érhető el. Ha most használja először ezt a funkciót, regisztrálja a funkciót a használata előtt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Ellenőrizze a szolgáltatásregisztráció állapotát: 

        > [!NOTE]
        > A **RegistrationState** állapotra váltása akár `Registering` 60 percig is `Registered` lehet. A folytatás előtt várja meg, amíg `Registered` az állapot be nem áll.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        Használhatja az Azure CLI és [parancsát](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) is a funkció regisztrálására és a regisztráció `az feature register` `az feature show` állapotának megjelenítésére.  
    * Ha engedélyezni szeretné a folyamatos rendelkezésre állást az SMB-köteten, válassza a Folyamatos rendelkezésre állás **engedélyezése lehetőséget.**    

        > [!IMPORTANT]   
        > Az SMB folyamatos rendelkezésre állása szolgáltatás jelenleg nyilvános előzetes verzióban érhető el. A szolgáltatáshoz való hozzáférésre vonatkozó várakozásilistás kérelmet kell beküldött Azure NetApp Files SMB folyamatos rendelkezésre állási megosztások nyilvános előzetes verziójú **[várakozásilista-beküldési oldalán.](https://aka.ms/anfsmbcasharespreviewsignup)** A folyamatos rendelkezésre állás funkció használata előtt várja meg a Azure NetApp Files megerősítő e-mailt.   
        > 
        > A folyamatos rendelkezésre állást csak az SQL Server [FSLogix felhasználóiprofil-tárolókhoz engedélyezze.](../virtual-desktop/create-fslogix-profile-container.md) Az SMB folyamatos rendelkezésre állási megosztások használata nem támogatott a SQL Server és az FSLogix felhasználói profiltárolóktól különböző számítási *feladatokhoz.* Ez a funkció jelenleg Windows rendszerű SQL Server. A Linux SQL Server jelenleg nem támogatott. Ha nem rendszergazdai (tartományi) fiókot használ az SQL Server, győződjön meg arról, hogy a fiókhoz hozzá van rendelve a szükséges biztonsági jogosultság. Ha a tartományi fiók nem rendelkezik a szükséges biztonsági jogosultságokkal ( ), és a jogosultságot nem lehet tartományszinten beállítani, akkor a jogosultságot a kapcsolat Biztonsági jogosultságú felhasználók mezőjében Active Directory `SeSecurityPrivilege` meg.  Lásd: [Kapcsolat Active Directory létrehozása.](create-active-directory-connections.md#create-an-active-directory-connection)

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

    ![Az SMB-kötet létrehozásának Protokoll lapját leíró képernyőkép.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kattintson **a Felülvizsgálat + létrehozás elemre** a kötet részleteinek áttekintéshez.  Ezután kattintson a **Létrehozás** gombra az SMB-kötet létrehozásához.

    A létrehozott kötet megjelenik a Kötetek lapon. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.

## <a name="control-access-to-an-smb-volume"></a>SMB-kötethez való hozzáférés szabályozása  

Az SMB-kötethez való hozzáférést engedélyekkel lehet kezelni.  

### <a name="share-permissions"></a>Megosztási engedélyek  

Alapértelmezés szerint egy új kötet rendelkezik a **Mindenki/Teljes** hozzáférés megosztási engedélyekkel. A Tartománygazdák csoport tagjai a következőképpen módosíthatják a megosztási engedélyeket:  

1. Leképezi a megosztást egy meghajtóra.  
2. Kattintson a jobb gombbal a meghajtóra, válassza a **Tulajdonságok lehetőséget,** majd válassza a **Biztonság lapot.**

[![Megosztási engedélyek beállítása](../media/azure-netapp-files/set-share-permissions.png)](../media/azure-netapp-files/set-share-permissions.png#lightbox)

### <a name="ntfs-file-and-folder-permissions"></a>NTFS-fájl- és mappaengedélyek  

Egy fájlra vagy mappára vonatkozó engedélyeket az objektum tulajdonságainak Biztonság lapján állíthat be a Windows SMB-ügyfélben. 
 
![Fájl- és mappaengedélyek beállítása](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Következő lépések  

* [Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [SMB – gyakori kérdések](./azure-netapp-files-faqs.md#smb-faqs)
* [SMB- vagy kettős protokollú kötetek hibaelhárítása](troubleshoot-dual-protocol-volumes.md)
* [Tudnivalók az Azure-szolgáltatások virtuális hálózati integrációjáról](../virtual-network/virtual-network-for-azure-services.md)
* [Új új Active Directory telepítése az Azure CLI használatával](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
