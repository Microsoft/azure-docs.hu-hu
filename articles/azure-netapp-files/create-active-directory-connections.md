---
title: Active Directory kapcsolatok létrehozása és kezelése Azure NetApp Fileshoz | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet Active Directory kapcsolatokat Azure NetApp Fileshoz.
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
ms.openlocfilehash: 756bf1cd7a7e9435130a3ad2d3b530b7f2e5b1b4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100608961"
---
# <a name="create-and-manage-active-directory-connections-for-azure-netapp-files"></a>Active Directory kapcsolatok létrehozása és kezelése Azure NetApp Files

A Azure NetApp Files számos funkciója megköveteli, hogy Active Directory-kapcsolatban legyenek.  Egy [SMB-kötet](azure-netapp-files-create-volumes-smb.md) vagy egy [kétprotokollos kötet](create-volumes-dual-protocol.md)létrehozása előtt például Active Directory kapcsolat szükséges.  Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet Active Directory kapcsolatokat Azure NetApp Fileshoz.

## <a name="before-you-begin"></a>Előkészületek  

A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet.   
[Kapacitási készlet beállítása](azure-netapp-files-set-up-capacity-pool.md)   
Az alhálózatot delegálni kell Azure NetApp Files.  
[Alhálózat delegálása az Azure NetApp Fileshoz](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Az Active Directory-kapcsolatok követelményei

 A Active Directory kapcsolatokra vonatkozó követelmények a következők: 

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
        > A **RegistrationState** a `Registering` módosítás előtt legfeljebb 60 percig lehet `Registered` . Várjon, amíg az állapot megmarad a `Registered` Folytatás előtt.

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
        > A **RegistrationState** a `Registering` módosítás előtt legfeljebb 60 percig lehet `Registered` . Várjon, amíg az állapot megmarad a `Registered` Folytatás előtt.

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
        > A **RegistrationState** a `Registering` módosítás előtt legfeljebb 60 percig lehet `Registered` . Várjon, amíg az állapot megmarad a `Registered` Folytatás előtt.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Használhatja az [Azure CLI-parancsokat](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) is, `az feature register` és `az feature show` regisztrálhatja a funkciót, és megjelenítheti a regisztrációs állapotot. 

    * Hitelesítő adatok, beleértve a **felhasználónevet** és a **jelszót** is

        ![Hitelesítő adatok Active Directory](../media/azure-netapp-files/active-directory-credentials.png)

3. Kattintson a **Csatlakozás** parancsra.  

    Megjelenik a létrehozott Active Directory-kapcsolatok.

    ![Létrehozott Active Directory kapcsolatok](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="next-steps"></a>Következő lépések  

* [SMB-kötet létrehozása](azure-netapp-files-create-volumes-smb.md)
* [Kettős protokollú kötet létrehozása](create-volumes-dual-protocol.md)
* [Az NFSv 4.1 Kerberos-titkosításának konfigurálása](configure-kerberos-encryption.md)
* [Új Active Directory erdő telepítése az Azure CLI-vel](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm) 
