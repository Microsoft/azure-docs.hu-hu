---
title: A NFSv 4.1 Kerberos-titkosítás konfigurálása a Azure NetApp Fileshoz | Microsoft Docs
description: Útmutatás a NFSv 4.1 Kerberos-titkosítás konfigurálásához Azure NetApp Files és a teljesítményre gyakorolt hatáshoz.
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
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: b7e40eb936a6151f0f31c34c5a8030153a87f08c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571093"
---
# <a name="configure-nfsv41-kerberos-encryption-for-azure-netapp-files"></a>Az NFSv 4.1 Kerberos-titkosításának konfigurálása az Azure NetApp Fileshoz

Azure NetApp Files támogatja az NFS-ügyfél titkosítását Kerberos-módban (krb5, krb5i és krb5p) AES-256 titkosítással. Ez a cikk a NFSv 4.1-es kötet Kerberos-titkosítással való használatához szükséges konfigurációkat ismerteti.

## <a name="requirements"></a>Követelmények

Az NFSv 4.1-ügyfél titkosítására az alábbi követelmények vonatkoznak: 

* Active Directory Domain Services (AD DS) kapcsolódás a Kerberos-jegy megkönnyítése érdekében 
* A DNS A/PTR rekord létrehozása az ügyfél és a Azure NetApp Files NFS-kiszolgáló IP-címei esetében
* Linux-ügyfél  
    Ez a cikk útmutatást nyújt a RHEL és az Ubuntu-ügyfelekhez.  A többi ügyfél hasonló konfigurációs lépésekkel fog működni. 
* NTP-kiszolgáló elérése  
    Használhatja a gyakran használt Active Directory-tartomány vezérlő (AD DC) tartományvezérlők egyikét.

## <a name="create-an-nfs-kerberos-volume"></a>NFS Kerberos-kötet létrehozása

1.  A NFSv 4.1 kötet létrehozásához kövesse az [NFS-kötet létrehozása a Azure NetApp Files](azure-netapp-files-create-volumes.md) számára című témakör lépéseit.   

    A kötet létrehozása lapon állítsa be az NFS-verziót a **nfsv 4.1** értékre, és állítsa be a Kerberost, hogy **engedélyezve** legyen.

    > [!IMPORTANT] 
    > A kötet létrehozása után nem módosítható a Kerberos-engedélyezési beállítás.

    ![NFSv 4.1 Kerberos-kötet létrehozása](../media/azure-netapp-files/create-kerberos-volume.png)  

2. Válassza a **házirend exportálása** lehetőséget a kötethez szükséges hozzáférési és biztonsági beállítások (Kerberos 5, Kerberos-5I vagy Kerberos-5p) egyeztetéséhez.   

    A Kerberos teljesítményére gyakorolt hatásért lásd: a [Kerberos teljesítményére gyakorolt hatás a nfsv 4.1 rendszeren](#kerberos_performance).  

    A kötet Kerberos biztonsági módszerei a Azure NetApp Files navigációs ablaktáblán a házirend exportálása lehetőségre kattintva is módosíthatók.

3.  A NFSv 4.1 kötet létrehozásához kattintson a **felülvizsgálat + létrehozás** gombra.

## <a name="configure-the-azure-portal"></a>A Azure Portal konfigurálása 

1.  Kövesse az Active Directory- [kapcsolatok létrehozása](create-active-directory-connections.md)című témakör utasításait.  

    A Kerberos megköveteli, hogy legalább egy számítógépfiókot hozzon létre Active Directory. Az Ön által megadott fiókadatok az SMB *-és nfsv 4.1-es Kerberos-* kötetek fiókjainak létrehozására szolgálnak. A rendszer automatikusan létrehozza ezt a gépet a kötet létrehozásakor.

2.  A **Kerberos tartomány** területen adja meg az **ad-kiszolgáló nevét** és a **KDC IP-** címét.

    Az Active Directory-kiszolgáló és a KDC IP-címe lehet ugyanaz a kiszolgáló. Ezek az információk az Azure NetApp Files által használt egyszerű szolgáltatásnév-számítógépfiók létrehozásához használhatók. A számítógépfiók létrehozása után a Azure NetApp Files a DNS-kiszolgálói rekordokat fogja használni a további KDC-kiszolgálók szükség szerinti megkereséséhez. 

    ![Kerberos-tartomány](../media/azure-netapp-files/kerberos-realm.png)
 
3.  Kattintson a **Csatlakozás** elemre a konfiguráció mentéséhez.

## <a name="configure-active-directory-connection"></a>Active Directory-kapcsolatok konfigurálása 

A NFSv 4.1 Kerberos konfigurálása két számítógépfiókot hoz létre Active Directoryban:
* Egy számítógépfiók az SMB-megosztásokhoz
* Egy számítógépfiók a NFSv 4.1-hez – ezt a fiókot az előtag alapján is azonosíthatja `NFS-` . 

Miután létrehozta az első NFSv 4.1-es Kerberos-kötetet, állítsa be a számítógépfiók titkosítási típusát a következő PowerShell-parancs használatával:

`Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256`

## <a name="configure-the-nfs-client"></a>Az NFS-ügyfél konfigurálása 

Az NFS-ügyfél konfigurálásához kövesse az [NFS-ügyfél konfigurálása Azure NetApp Fileshoz](configure-nfs-clients.md) című témakör útmutatását.  

## <a name="mount-the-nfs-kerberos-volume"></a><a name="kerberos_mount"></a>Az NFS Kerberos-kötet csatlakoztatása

1. A **kötetek** lapon válassza ki a csatlakoztatni kívánt NFS-kötetet.

2. Az utasítások megjelenítéséhez kattintson a kötet **csatlakoztatási utasításai** elemre.

    Például: 

    ![A Kerberos-kötetek csatlakoztatására vonatkozó utasítások](../media/azure-netapp-files/mount-instructions-kerberos-volume.png)  

3. Hozza létre a könyvtárat (csatlakoztatási pont) az új kötethez.  

4. Állítsa be az alapértelmezett titkosítási típust az AES 256 értékre a számítógépfiók esetében:  
    `Set-ADComputer $NFSCOMPUTERACCOUNT -KerberosEncryptionType AES256 -Credential $ANFSERVICEACCOUNT`

    * Ezt a parancsot minden számítógépfiók esetében csak egyszer kell futtatnia.
    * Ezt a parancsot egy tartományvezérlőről vagy egy, az [RSAT](https://support.microsoft.com/help/2693643/remote-server-administration-tools-rsat-for-windows-operating-systems) használatával telepített számítógépről futtathatja. 
    * A `$NFSCOMPUTERACCOUNT` változó a Kerberos-kötet telepítésekor Active Directoryban létrehozott számítógépfiók. Ez az a fiók, amely a előtaggal van ellátva `NFS-` . 
    * A `$ANFSERVICEACCOUNT` változó egy nem Kiemelt jogosultságú Active Directory felhasználói fiók, amely delegált vezérlőkkel rendelkezik azon a szervezeti egységen, amelyben a számítógépfiók létrejött. 

5. Csatlakoztassa a kötetet a gazdagépen: 

    `sudo mount -t nfs -o sec=krb5p,rw,hard,rsize=1048576,wsize=1048576,vers=4.1,tcp $ANFEXPORT $ANFMOUNTPOINT`

    * A `$ANFEXPORT` változó a `host:/export` csatlakoztatási utasításokban található elérési út.
    * A `$ANFMOUNTPOINT` változó a felhasználó által létrehozott mappa a Linux-gazdagépen.

## <a name="performance-impact-of-kerberos-on-nfsv41"></a><a name="kerberos_performance"></a>A Kerberos teljesítményére gyakorolt hatás a NFSv 4.1 rendszeren 

Ez a szakasz segítséget nyújt a Kerberos NFSv 4.1-es teljesítményére gyakorolt hatásának megismerésében.

### <a name="available-security-options"></a>Elérhető biztonsági beállítások 

A NFSv 4.1-kötetek jelenleg elérhető biztonsági beállítások a következők: 

* a **SEC = sys** helyi UNIX UID és GID használ az NFS-műveletek hitelesítéséhez AUTH_SYS használatával.
* **mp = a krb5** a Kerberos V5-et használja a helyi UNIX-UID és a GID helyett a felhasználók hitelesítéséhez.
* **SEC = a Krb5i** Kerberos V5 protokollt használ a felhasználók hitelesítéséhez, és az adatok illetéktelen módosításának megakadályozásához biztonságos ellenőrzőösszegek használatával végzi az NFS-műveletek integritásának ellenőrzését.
* **mp = a Krb5p** Kerberos V5 protokollt használ a felhasználók hitelesítéséhez és az integritás ellenőrzéséhez. Titkosítja az NFS-forgalmat, hogy megakadályozza a forgalom elemzését. Ez a lehetőség a legbiztonságosabb beállítás, de a legnagyobb teljesítménnyel is jár.

### <a name="performance-vectors-tested"></a>Tesztelt teljesítmény-vektorok

Ez a szakasz a különböző beállítások egyetlen ügyféloldali teljesítményére gyakorolt hatását ismerteti `sec=*` .

* A teljesítményre gyakorolt hatás két szinten lett tesztelve: alacsony Egyidejűség (alacsony terhelés) és magas Egyidejűség (az I/O és az átviteli sebesség felső határértéke).  
* A munkaterhelések három típusát tesztelték:  
    * Kis művelet véletlenszerű olvasási/írási (FIO használatával)
    * Nagyméretű művelet szekvenciális olvasási/írási (FIO használatával)
    * Metaadatok nagy mennyiségű számítási feladattal, például a git-vel generált alkalmazások

### <a name="expected-performance-impact"></a>Várható teljesítmény-hatás 

A fókusz két területből áll: a kis-és nagybetűkből álló terheléssel. Az alábbi listában a teljesítményre gyakorolt hatás biztonsági beállításai láthatók a biztonsági beállítások és forgatókönyvek szerint. Minden összehasonlítás a `sec=sys` biztonsági paraméterrel történik. A teszt egyetlen köteten, egyetlen ügyfél használatával lett végrehajtva. 

A krb5 teljesítményére gyakorolt hatás:

* Alacsony Egyidejűség (r/w):
    * A szekvenciális késés megnövelte a 0,3 MS-ot.
    * A véletlenszerű I/O-késés nagyobb, mint 0,2 MS.
    * A metaadatok I/O-késése nagyobb, mint 0,2 MS.
* Magas Egyidejűség (r/w): 
    * A krb5 nem befolyásolta a maximális szekvenciális átviteli sebességet.
    * A teljes véletlenszerű I/O-érték 30%-kal csökkent a tiszta olvasási munkaterhelések esetében, mivel a számítási feladatok a tiszta írásra váltanak. 
    * A metaadatok maximális munkaterhelése 30%-kal csökkent.

A krb5i teljesítményére gyakorolt hatás: 

* Alacsony Egyidejűség (r/w):
    * A szekvenciális késés megnövelte a 0,5 MS-ot.
    * A véletlenszerű I/O-késés nagyobb, mint 0,2 MS.
    * A metaadatok I/O-késése nagyobb, mint 0,2 MS.
* Magas Egyidejűség (r/w): 
    * A maximális soros átviteli sebesség 70%-kal csökkent, a munkaterhelés-elegytől függetlenül.
    * A maximális véletlenszerű I/O-érték 50%-kal csökkent a tiszta olvasási munkaterhelések esetében, és az általános hatás 25%-ra csökken, mivel a munkaterhelés a tiszta írásra vált. 
    * A metaadatok maximális munkaterhelése 30%-kal csökkent.

A krb5p teljesítményére gyakorolt hatás:

* Alacsony Egyidejűség (r/w):
    * A szekvenciális késés megnövelte a 0,8 MS-ot.
    * A véletlenszerű I/O-késés nagyobb, mint 0,2 MS.
    * A metaadatok I/O-késése nagyobb, mint 0,2 MS.
* Magas Egyidejűség (r/w): 
    * A maximális soros átviteli sebesség 85%-kal csökkent, a munkaterhelés-elegytől függetlenül. 
    * A teljes véletlenszerű I/O-érték 65%-kal csökkent a tiszta olvasási munkaterhelések esetében, és az általános hatás 43%-ra csökken, mivel a munkaterhelés a tiszta írásra változik. 
    * A metaadatok maximális munkaterhelése 30%-kal csökkent.

## <a name="next-steps"></a>Következő lépések  

* [NFSv 4.1 – Kerberos mennyiségi problémák elhárítása](troubleshoot-nfsv41-kerberos-volumes.md)
* [Gyakori kérdések a Azure NetApp Files](azure-netapp-files-faqs.md)
* [NFS-kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)
* [Active Directory-kapcsolatok létrehozása](create-active-directory-connections.md)
* [NFS-ügyfél konfigurálása az Azure NetApp Fileshoz](configure-nfs-clients.md) 
