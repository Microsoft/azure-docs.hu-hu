---
title: Hozzon létre egy kettős protokollú (NFSv3 és SMB) kötetet a Azure NetApp Files | Microsoft Docs
description: Ismerteti, hogyan hozhat létre olyan kötetet, amely az NFSv3 és az SMB kettős protokollját használja az LDAP-felhasználóleképezés támogatására.
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
ms.date: 04/19/2021
ms.author: b-juche
ms.openlocfilehash: c702c41228512eceebeaf45ccae709db38a85a51
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725680"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Hozzon létre egy kettős protokollú (NFSv3 és SMB) kötetet a Azure NetApp Files

Azure NetApp Files NFS (NFSv3 és NFSv4.1), SMB3 vagy kettős protokoll használatával támogatja a kötetek létrehozását. Ez a cikk bemutatja, hogyan hozhat létre olyan kötetet, amely az NFSv3 és az SMB kettős protokollját használja az LDAP-felhasználóleképezés támogatásával. 

NFS-kötetek létrehozásához lásd: [NFS-kötet létrehozása.](azure-netapp-files-create-volumes.md) SMB-kötetek létrehozásához lásd: [SMB-kötet létrehozása.](azure-netapp-files-create-volumes-smb.md) 

## <a name="before-you-begin"></a>Előkészületek 

* Már létrehozott egy kapacitáskészletet.  
    Lásd: [Kapacitáskészlet beállítása.](azure-netapp-files-set-up-capacity-pool.md)   
* Az alhálózatot delegálni kell a Azure NetApp Files.  
    Lásd: [Alhálózat delegálása](azure-netapp-files-delegate-subnet.md)a Azure NetApp Files.

## <a name="considerations"></a>Megfontolandó szempontok

* Győződjön meg arról, hogy [megfelel a kapcsolatokra vonatkozó Active Directory követelményeknek.](create-active-directory-connections.md#requirements-for-active-directory-connections) 
* Hozzon létre egy névkeresési zónát a DNS-kiszolgálón, majd adjon hozzá egy mutató (PTR) rekordot az AD-gazdagéphez a névkeresési zónában. Ellenkező esetben a kettős protokollú kötet létrehozása sikertelen lesz.
* Ellenőrizze, hogy az NFS-ügyfél naprakész állapotban van-e, illetve hogy az operációs rendszer legfrissebb verziója fut-e rajta.
* Győződjön meg arról, Active Directory (AD) LDAP-kiszolgáló működik az AD-on. Ezt az AD-gépen Active Directory Lightweight Directory-szolgáltatások [(AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) szerepkör telepítésével és konfigurálásával teheti meg.
* A kettős protokollú kötetek jelenleg nem támogatják a Azure Active Directory Domain Services (AADDS) protokollt. Az AADDS használata esetén a TLS-hez tartozó LDAP nem engedélyezhető.
* A kettős protokollú kötet által használt NFS-verzió az NFSv3. Ezért a következő szempontokat kell figyelembe venni:
    * A kettős protokoll nem támogatja az NFS-ügyfelektől származó Kiterjesztett Windows `set/get` ACLS-attribútumokat.
    * Az NFS-ügyfelek nem módosíthatják az NTFS biztonsági stílus engedélyeit, és a Windows-ügyfelek nem módosíthatják a UNIX stílusú kettős protokollú kötetek engedélyeit.   

    A következő táblázat a biztonsági stílusokat és azok hatásait ismerteti:  
    
    | Biztonsági stílus    | Engedélyeket módosító ügyfelek   | Az ügyfelek által használható engedélyek  | Hatékony biztonsági stílus    | Fájlokhoz hozzáférő ügyfelek     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | NFSv3 módú bitek   | UNIX  | NFS és Windows   |
    | `Ntfs`    | Windows   | NTFS ACL-ek     | NTFS  |NFS és Windows|
* Az NTFS biztonsági stílusú kötetET NFS-sel felrakó UNIX-felhasználóként és az összes többi felhasználónál `root` `root` `pcuser` windowsos felhasználóként lesz hitelesítve. Győződjön meg arról, hogy ezek a felhasználói fiókok léteznek a Active Directory a kötet csatlakoztatása előtt NFS használata esetén. 
* Ha nagyméretű topológiákkal rendelkezik, és a biztonsági stílust kettős protokollú kötethez vagy LDAP-hoz használja kiterjesztett csoportokkal, előfordulhat, hogy az Azure NetApp Files nem tudja elérni a topológiákban található összes `Unix` kiszolgálót.  Ha ez a helyzet, forduljon a fiók ügyfélszolgálathoz segítségért.  <!-- NFSAAS-15123 --> 
* Két protokollos kötet létrehozásához nincs szükség kiszolgálói legfelső szintű hitelesítésszolgáltatói tanúsítványra. Csak akkor szükséges, ha a TLS-hez tartozó LDAP engedélyezve van.


## <a name="create-a-dual-protocol-volume"></a>Kettős protokollú kötet létrehozása

1.  Kattintson **a Kötetek panelre** a Kapacitáskészletek panelen. Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához. 

    ![Navigálás a kötetek között](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  A Kötet létrehozása ablakban kattintson a **Létrehozás** elemre, és adja meg az alábbi mezők adatait az Alapvető beállítások lapon:   
    * **Kötet neve**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A kötet nevének minden kapacitáskészleten belül egyedinek kell lennie. Legalább három karakter hosszúnak kell lennie. Bármilyen alfanumerikus karaktert használhat.   

        Kötetnévként `default` nem használhatja a vagy `bin` a et.

    * **Kapacitáskészlet**  
        Adja meg a kapacitáskészletet, ahol létre szeretné hozatni a kötetet.

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Átviteli sebesség (MiB/S)**   
        Ha a kötet manuális QoS-kapacitáskészletben jön létre, adja meg a kötet kívánt átviteli sebességét.   

        Ha a kötet egy automatikus QoS-kapacitáskészletben jön létre, az ebben a mezőben megjelenő érték (kvóta x szolgáltatási szint átviteli sebesség).   

    * **Virtuális hálózat**  
        Adja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről hozzá szeretne férni a kötethez.  

        A megadott virtuális hálózatnak delegált alhálózatot kell Azure NetApp Files. A Azure NetApp Files szolgáltatás csak ugyanattól a virtuális hálózattól vagy egy olyan virtuális hálózatról érhető el, amely a kötettel azonos régióban van virtuális hálózatok közötti társviszony létesítésen keresztül. A kötetet a helyszíni hálózatról is elérheti az Express Route-on keresztül.   

    * **Alhálózat**  
        Adja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell a Azure NetApp Files. 
        
        Ha nem delegált alhálózatot,  a Kötet létrehozása lapon kattintson az Új létrehozása elemre. Ezután az Alhálózat létrehozása lapon adja meg az alhálózati adatokat, és válassza a **Microsoft.NetApp/volumes** lehetőséget az alhálózat delegálható Azure NetApp Files. Minden virtuális hálózatban csak egy alhálózat delegálható a Azure NetApp Files.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Ha meglévő pillanatkép-házirendet szeretne alkalmazni a  kötetre, kattintson a Speciális szakasz megjelenítése elemre annak kibontásához, adja meg, hogy el szeretné-e rejteni a pillanatkép elérési útját, majd válasszon ki egy pillanatkép-szabályzatot a lekért menüben. 

        A pillanatkép-házirendek létrehozásával kapcsolatos információkért lásd: [Pillanatkép-házirendek kezelése.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

        ![Speciális kijelölés megjelenítése](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Kattintson **a Protokoll** elemre, majd a következő műveleteket kell végrehajtania:  
    * Válassza **a kettős protokoll (NFSv3 és SMB)** protokolltípust a kötethez.   

    * Adja meg **a kötet kötetútvonalát.**   
    Ez a kötet elérési útja a megosztott kötet neve. A névnek betűrendes karakterrel kell kezdődnie, és egyedinek kell lennie az egyes előfizetések és régiók között.  

    * Adja meg **a használni kívánt biztonsági** stílust: NTFS (alapértelmezett) vagy UNIX.

    * Ha engedélyezni szeretné az SMB3 protokolltitkosítást a kettős protokollú köteten, válassza az **SMB3 protokolltitkosítás engedélyezése lehetőséget.**   

        Ez a funkció csak az SMB3-adatokat titkosítja. Nem titkosítja az NFSv3 adatokat. Az SMB3-titkosítást nem használó SMB-ügyfelek nem fogják tudni elérni ezt a kötetet. A rendszer a beállítástól függetlenül titkosítja az adatok titkosítását. További [információ: SMB-titkosítás](azure-netapp-files-faqs.md#smb-encryption-faqs) – gyakori kérdések. 

        Az **SMB3 protokolltitkosítási** szolgáltatás jelenleg előzetes verzióban érhető el. Ha most használja először ezt a funkciót, regisztrálja a funkciót a használata előtt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Ellenőrizze a szolgáltatásregisztráció állapotát: 

        > [!NOTE]
        > A **RegistrationState** állapot akár 60 percig is lehet, `Registering` mielőtt a következőre vált: `Registered` . A folytatás előtt várja meg, amíg `Registered` az állapot el nem áll.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        A szolgáltatás regisztrálására és a regisztráció állapotának megjelenítésére [azure CLI-parancsokat](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` is `az feature show` használhat.  

    * Ha szükséges, [konfigurálja a kötet exportálási szabályzatát.](azure-netapp-files-configure-export-policy.md)

    ![Kettős protokoll megadása](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Kattintson **a Felülvizsgálat + létrehozás elemre** a kötet részleteinek áttekintéshez. Ezután kattintson a **Létrehozás** gombra a kötet létrehozásához.

    A létrehozott kötet megjelenik a Kötetek lapon. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>Az LDAP-t használó helyi NFS-felhasználók hozzáférhetnek egy kettős protokollú kötethez 

Engedélyezheti a Windows LDAP-kiszolgálón nem jelen található helyi NFS-ügyfélfelhasználók számára, hogy hozzáférjenek egy olyan kettős protokollt használó kötethez, amelynél engedélyezve vannak a bővített csoportokkal rendelkező LDAP-k. A következőképpen engedélyezze az LDAP-val használó helyi **NFS-felhasználók** engedélyezése beállítást:

1. Kattintson **a Active Directory elemre.**  Egy meglévő kapcsolaton Active Directory kattintson a helyi menüre (három pont), majd válassza a `…` **Szerkesztés lehetőséget.**  

2. A megjelenő **Edit Active Directory settings (Helyi** NFS-felhasználók engedélyezése **LDAP-val)** lehetőséget.  

    ![Képernyőkép az LDAP-val használó helyi NFS-felhasználók engedélyezése lehetőségről](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX-attribútumok kezelése

A POSIX-attribútumok, például az UID, a Kezdőkönyvtár és más értékek az MMC beépülő modul Active Directory - felhasználók és számítógépek kezelhetők.  Az alábbi példa a Active Directory attribútumszerkesztőt mutatja be:  

![Active Directory attribútumszerkesztő](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Az LDAP-felhasználókhoz és LDAP-csoportokhoz a következő attribútumokat kell beállítania: 
* LdAP-felhasználók kötelező attribútumai:   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* LdAP-csoportokhoz szükséges attribútumok:   
    `objectClass: posixGroup`, `gidNumber: 555`

## <a name="configure-the-nfs-client"></a>Az NFS-ügyfél konfigurálása 

Az NFS-ügyfél konfigurálásán az [NFS-ügyfél konfigurálásán Azure NetApp Files NFS-ügyfél](configure-nfs-clients.md) konfigurálásán.  

## <a name="next-steps"></a>Következő lépések  

* [NFS-ügyfél konfigurálása az Azure NetApp Fileshoz](configure-nfs-clients.md)
* [SMB- vagy kettős protokollú kötetek hibaelhárítása](troubleshoot-dual-protocol-volumes.md)
* [LDAP-kötetekkel kapcsolatos problémák elhárítása](troubleshoot-ldap-volumes.md)
