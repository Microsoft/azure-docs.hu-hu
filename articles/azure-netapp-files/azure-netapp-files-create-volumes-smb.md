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
ms.date: 12/01/2020
ms.author: b-juche
ms.openlocfilehash: 7c5f18d33efa1838fccab3935054ea19227bc06c
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102852"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>SMB-kötet létrehozása az Azure NetApp Files számára

Azure NetApp Files támogatja a kötetek NFS használatával történő létrehozását (NFSv3 és NFSv 4.1), a SMB3 vagy a kettős protokollt (NFSv3 és SMB). A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. Ez a cikk bemutatja, hogyan hozhat létre SMB3-köteteket.

## <a name="before-you-begin"></a>Előkészületek 
A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet.   
[Kapacitási készlet beállítása](azure-netapp-files-set-up-capacity-pool.md)   
Az alhálózatot delegálni kell Azure NetApp Files.  
[Alhálózat delegálása az Azure NetApp Fileshoz](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Az Active Directory-kapcsolatok követelményei

 SMB-kötet létrehozása előtt létre kell hoznia Active Directory kapcsolatokat. A Active Directory kapcsolatokra vonatkozó követelmények a következők: 

* A használt rendszergazdai fióknak képesnek kell lennie arra, hogy számítógép-fiókokat hozzon létre a szervezeti egység (OU) elérési útjában, amelyet meg fog adni.  

* A megfelelő portokat meg kell nyitni a megfelelő Windows Active Directory (AD) kiszolgálón.  
    A szükséges portok a következők: 

    |     Szolgáltatás           |     Port     |     Protokoll     |
    |-----------------------|--------------|------------------|
    |    AD Web Services    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    Visszhangos válasz    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS-név       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    W32Time            |    123       |    UDP           |

* A célként megadott Active Directory Domain Services helyének topológiájának meg kell felelnie az irányelveknek, különösen az Azure-VNet, ahol a Azure NetApp Files telepítve van.  

    Egy új vagy meglévő Active Directory-helyhez hozzá kell adni a Azure NetApp Files központilag telepített virtuális hálózat címterület (ahol a tartományvezérlő elérhető a Azure NetApp Files) használatával. 

* A megadott DNS-kiszolgálóknak elérhetőknek kell lenniük a Azure NetApp Files [delegált alhálózatán](./azure-netapp-files-delegate-subnet.md) .  

    Lásd: [Azure NetApp Files hálózati tervezéssel kapcsolatos irányelvek](./azure-netapp-files-network-topologies.md) a támogatott hálózati topológiák esetében.

    A hálózati biztonsági csoportoknak (NSG) és a tűzfalaknak megfelelően konfigurált szabályokkal kell rendelkezniük a Active Directory és a DNS forgalmi kéréseinek engedélyezéséhez. 

* A Azure NetApp Files delegált alhálózatnak képesnek kell lennie arra, hogy elérje a tartomány összes Active Directory Domain Services (Hozzáadás) tartományvezérlőjét, beleértve az összes helyi és távoli tartományvezérlőt is. Ellenkező esetben a szolgáltatás megszakítása is bekövetkezhet.  

    Ha olyan tartományvezérlővel rendelkezik, amely nem érhető el a Azure NetApp Files delegált alhálózat számára, megadhat egy Active Directory helyet az Active Directory-kapcsolat létrehozása során.  Azure NetApp Files csak azokkal a tartományvezérlőkkel kell kommunikálni, amelyeken a Azure NetApp Files delegált alhálózati címtartomány található.

    Lásd: [a hely topológiájának megtervezése az](/windows-server/identity/ad-ds/plan/designing-the-site-topology) ad-helyekre és-szolgáltatásokra vonatkozóan. 
    
* Az AD-hitelesítéshez tartozó AES-titkosítás engedélyezéséhez ellenőrizze az **AES-titkosítást** az [illesztési Active Directory](#create-an-active-directory-connection) ablakban. Azure NetApp Files támogatja a DES, a Kerberos AES 128 és a Kerberos AES 256 titkosítási típusokat (a legkevésbé biztonságostól a legbiztonságosabbig). Ha engedélyezi az AES-titkosítást, akkor a Active Directoryhoz való csatlakozáshoz használt felhasználói hitelesítő adatoknak engedélyezve kell lennie a legfelső szintű fiók beállításnak, amely megfelel a Active Directory számára engedélyezett képességeknek.    

    Ha például a Active Directory csak az AES-128 képességgel rendelkezik, engedélyeznie kell a felhasználói hitelesítő adatokhoz tartozó AES-128 fiók lehetőséget. Ha a Active Directory AES-256 képességgel rendelkezik, engedélyeznie kell az AES-256 fiók lehetőséget (amely szintén támogatja az AES-128-t). Ha a Active Directory nem rendelkezik Kerberos-titkosítási képességgel, akkor a Azure NetApp Files alapértelmezés szerint DES-t használ.  

    Engedélyezheti a fiók beállításait a Active Directory felhasználók és számítógépek Microsoft Management Console (MMC) tulajdonságaiban:   

    ![Active Directory felhasználók és számítógépek MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)

* A Azure NetApp Files támogatja az [LDAP-aláírást](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), ami lehetővé teszi az LDAP-forgalom biztonságos átvitelét a Azure NetApp Files szolgáltatás és a megcélozt [Active Directory tartományvezérlők](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)között. Ha az LDAP-aláíráshoz a Microsoft tanácsadói [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) útmutatását követi, akkor a Azure NetApp Files LDAP-aláírási funkciójának engedélyezéséhez a [Csatlakozás Active Directory](#create-an-active-directory-connection) ablakban jelölje be az **LDAP** -aláírás jelölőnégyzetet. 

    Az [LDAP-csatorna kötési](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) konfigurációja önmagában nincs hatással a Azure NetApp Files szolgáltatásra. Ha azonban az LDAP-csatorna kötését és a Secure LDAP-t (például LDAPs vagy `start_tls` ) használja, akkor az SMB-kötet létrehozása sikertelen lesz.

További információ a további AD-információkról: Azure NetApp Files [SMB-gyakori kérdések](./azure-netapp-files-faqs.md#smb-faqs) . 

## <a name="decide-which-domain-services-to-use"></a>A használni kívánt tartományi szolgáltatások kiválasztása 

Azure NetApp Files támogatja az AD-kapcsolatok [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (Hozzáadás) és Azure Active Directory Domain Services (AADDS) használatát.  Az AD-kapcsolatok létrehozása előtt el kell döntenie, hogy használja-e a Add vagy a AADDS.  

További információ: az [önálló felügyelt Active Directory Domain Services, Azure Active Directory és felügyelt Azure Active Directory Domain Services összehasonlítása](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

A Azure NetApp Fileshez használhatja az előnyben részesített [Active Directory helyek és szolgáltatások](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) hatókörét. Ez a beállítás lehetővé teszi az olvasási és írási műveleteket a [Azure NetApp Files által elérhető](azure-netapp-files-network-topologies.md)tartományvezérlők Active Directory Domain Services (hozzáadásával). Azt is megakadályozza, hogy a szolgáltatás olyan tartományvezérlőkkel kommunikáljon, amelyek nem szerepelnek a megadott Active Directory helyek és szolgáltatások helyen. 

Ha a HOZZÁADÁSAkor a hely nevét szeretné megkeresni, vegye fel a kapcsolatot a szervezetében a Active Directory Domain Services felelős felügyeleti csoporttal. Az alábbi példa a Active Directory helyek és szolgáltatások beépülő modult mutatja, ahol a hely neve jelenik meg: 

![Active Directory - helyek és szolgáltatások](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Ha Azure NetApp Files AD-kapcsolatát konfigurálja, a hely nevét a hatókörben adja meg az **Active Directory-hely neve** mezőben.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory tartományi szolgáltatások 

A Azure Active Directory Domain Services (AADDS) konfigurálásához és útmutatásához tekintse meg a [Azure ad Domain Services dokumentációját](../active-directory-domain-services/index.yml).

A Azure NetApp Files további AADDS szempontokat is figyelembe kell venni: 

* Győződjön meg arról, hogy a VNet vagy az alhálózat, ahol a AADDS telepítve van, ugyanabban az Azure-régióban található, mint a Azure NetApp Files üzemelő példány.
* Ha egy másik VNet használ abban a régióban, amelyben Azure NetApp Files van telepítve, hozzon létre egy társítást a két virtuális hálózatok között.
* Azure NetApp Files támogatja `user` és `resource forest` típusokat.
* A szinkronizálás típusa beállításnál válassza a `All` vagy a lehetőséget `Scoped` .   
    Ha bejelöli `Scoped` , győződjön meg arról, hogy a megfelelő Azure ad-csoport van kiválasztva az SMB-megosztások eléréséhez.  Ha bizonytalan, használhatja a `All` szinkronizálási típust.
* A nagyvállalati vagy prémium szintű SKU használatát kötelező megadni. A standard SKU nem támogatott.

Active Directory-kapcsolatok létrehozásakor vegye figyelembe a következő AADDS vonatkozó jellemzőket:

* Az **elsődleges DNS**-, **másodlagos DNS**-és **ad DNS-tartománynevek** adatait a AADDS menüben találja.  
A DNS-kiszolgálók esetében két IP-cím lesz használva a Active Directory-kapcsolatok konfigurálásához. 
* A **szervezeti egység elérési útja** : `OU=AADDC Computers` .  
Ez a beállítás a **NetApp-fiókhoz** tartozó **Active Directory-kapcsolatokban** van konfigurálva:

  ![Szervezeti egység elérési útja](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* A **Felhasználónév** hitelesítő adatai bármely olyan felhasználó lehetnek, akik tagja az Azure ad-csoportnak az **Azure ad DC-rendszergazdáknak**.


## <a name="create-an-active-directory-connection"></a>Active Directory-kapcsolatok létrehozása

1. A NetApp-fiókból kattintson **Active Directory kapcsolatok** elemre, majd kattintson a **Csatlakozás** elemre.  

    ![Active Directory kapcsolatok](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. A csatlakozás Active Directory ablakban adja meg a következő információkat a használni kívánt tartományi szolgáltatások alapján:  

    Az Ön által használt tartományi szolgáltatásokra vonatkozó információkért lásd: a [használni kívánt tartományi szolgáltatások kiválasztása](#decide-which-domain-services-to-use). 

    * **Elsődleges DNS**  
        Ez az a DNS, amely szükséges a Active Directory tartományhoz való csatlakozáshoz és az SMB-hitelesítési műveletekhez. 
    * **Másodlagos DNS**   
        Ez a másodlagos DNS-kiszolgáló a redundáns Name Services biztosításához. 
    * **AD DNS-tartománynév**  
        Ez annak a Active Directory Domain Services a tartományneve, amelyhez csatlakozni szeretne.
    * **AD-hely neve**  
        Ennek a helynek a neve, amelyet a tartományvezérlő felderítése korlátozni fog. Ennek meg kell egyeznie a hely nevével Active Directory helyeken és szolgáltatásokban.
    * **SMB-kiszolgáló (számítógépfiók) előtagja**  
        Ez az Active Directory lévő számítógépfiók elnevezési előtagja, amelyet a Azure NetApp Files új fiókok létrehozásához fog használni.

        Ha például a szervezet által a fájlkiszolgálók számára használt elnevezési szabvány a NAS-01, a NAS-02..., a NAS-045, akkor a "NAS" értéket kell megadnia az előtaghoz. 

        A szolgáltatás szükség szerint további számítógép-fiókokat hoz létre Active Directory.

        > [!IMPORTANT] 
        > Az SMB-kiszolgáló előtagjának átnevezése az Active Directory-kapcsolatok létrehozása után zavaró. Az SMB-kiszolgáló előtagjának átnevezése után újra kell csatlakoztatnia a meglévő SMB-megosztásokat.

    * **Szervezeti egység elérési útja**  
        Ez a szervezeti egység (OU) LDAP-elérési útja, ahol a rendszer az SMB-kiszolgáló számítógép-fiókjait hozza létre. Ez a szervezeti egység = második szint, OU = első szint. 

        Ha a Azure NetApp Filest használja a Azure Active Directory Domain Services, a szervezeti egység elérési útja az `OU=AADDC Computers` Active Directory beállítása a NetApp-fiókhoz.

    ![Csatlakozás Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **AES-titkosítás**   
        Jelölje be ezt a jelölőnégyzetet, ha engedélyezni szeretné az AES-titkosítást egy SMB-köteten. A követelmények [Active Directory kapcsolatok követelményeivel](#requirements-for-active-directory-connections) kapcsolatban lásd:. 

        ![AES-titkosítás Active Directory](../media/azure-netapp-files/active-directory-aes-encryption.png)

        Az **AES titkosítási** funkciója jelenleg előzetes verzióban érhető el. Ha első alkalommal használja ezt a funkciót, regisztrálja a szolgáltatást a használata előtt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        A szolgáltatás regisztrálási állapotának ellenõrzése: 

        > [!NOTE]
        > A **RegistrationState** a `Registering` módosítás előtt legfeljebb 60 percig lehet `Registered` . A folytatás előtt várjon, amíg az állapot **regisztrálva** lesz.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        Használhatja az [Azure CLI-parancsokat](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) is, `az feature register` és `az feature show` regisztrálhatja a funkciót, és megjelenítheti a regisztrációs állapotot. 

    * **LDAP-aláírás**   
        Jelölje be ezt a jelölőnégyzetet az LDAP-aláírás engedélyezéséhez. Ez a funkció lehetővé teszi a biztonságos LDAP-kereséseket a Azure NetApp Files szolgáltatás és a felhasználó által megadott [Active Directory Domain Services-tartományvezérlők](/windows/win32/ad/active-directory-domain-services)között. További információ: [ADV190023 | Microsoft-útmutató az LDAP-csatornák kötésének és az LDAP-aláírás engedélyezéséhez](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![LDAP-aláírás Active Directory](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        Az **LDAP-aláírás** funkció jelenleg előzetes verzióban érhető el. Ha első alkalommal használja ezt a funkciót, regisztrálja a szolgáltatást a használata előtt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        A szolgáltatás regisztrálási állapotának ellenõrzése: 

        > [!NOTE]
        > A **RegistrationState** a `Registering` módosítás előtt legfeljebb 60 percig lehet `Registered` . A folytatás előtt várjon, amíg az állapot **regisztrálva** lesz.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        Használhatja az [Azure CLI-parancsokat](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) is, `az feature register` és `az feature show` regisztrálhatja a funkciót, és megjelenítheti a regisztrációs állapotot. 

     * **Biztonsági mentési szabályzat felhasználói**  
        Olyan további fiókokat is hozzáadhat, amelyekhez emelt szintű jogosultságok szükségesek a Azure NetApp Fileshoz való használatra létrehozott számítógépfiók számára. A megadott fiókok a fájl vagy mappa szintjén módosíthatják az NTFS-engedélyeket. Megadhat például egy nem Kiemelt szolgáltatásfiók-fiókot, amely az adatáttelepítés során az SMB-fájlmegosztás Azure NetApp Files-ben való áttelepítésére szolgál.  

        ![Active Directory biztonsági mentési szabályzat felhasználói](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        A **biztonsági mentési házirend felhasználói** szolgáltatás jelenleg előzetes verzióban érhető el. Ha első alkalommal használja ezt a funkciót, regisztrálja a szolgáltatást a használata előtt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        A szolgáltatás regisztrálási állapotának ellenõrzése: 

        > [!NOTE]
        > A **RegistrationState** a `Registering` módosítás előtt legfeljebb 60 percig lehet `Registered` . A folytatás előtt várjon, amíg az állapot **regisztrálva** lesz.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Használhatja az [Azure CLI-parancsokat](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) is, `az feature register` és `az feature show` regisztrálhatja a funkciót, és megjelenítheti a regisztrációs állapotot. 

    * Hitelesítő adatok, beleértve a **felhasználónevet** és a **jelszót** is

        ![Hitelesítő adatok Active Directory](../media/azure-netapp-files/active-directory-credentials.png)

3. Kattintson a **Csatlakozás** parancsra.  

    Megjelenik a létrehozott Active Directory-kapcsolatok.

    ![Létrehozott Active Directory kapcsolatok](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

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
