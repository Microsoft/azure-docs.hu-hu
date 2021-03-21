---
title: Azure VMware-megoldás CloudSimple használatával – az Azure AD-t használhatja a saját Felhőbeli identitás forrásaként
description: Ismerteti, hogyan lehet az Azure AD-t identitás-szolgáltatóként hozzáadni a saját CloudSimple-felhőben az Azure-CloudSimple hozzáférő felhasználók hitelesítéséhez
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f55a0f52f5e028f9cbf7a9fabbb3c24ad43c3800
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898606"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-cloudsimple-private-cloud"></a>Az Azure AD használata identitás-szolgáltatóként a vCenter a CloudSimple Private Cloud szolgáltatásban

A CloudSimple saját Felhőbeli vCenter a VMware-rendszergazdák számára a vCenter eléréséhez a Azure Active Directory (Azure AD) használatával végezheti el a hitelesítést. Az egyszeri bejelentkezési Identity forrás beállítása után a **cloudowner** -felhasználó hozzáadhat felhasználókat az Identity forrásból a vCenter.  

A Active Directory tartománya és tartományvezérlői a következő módokon állíthatók be:

* A helyszínen futó tartomány-és tartományvezérlők Active Directory
* Az Azure-ban virtuális gépekként működő tartomány-és tartományvezérlők Active Directory Azure-előfizetésében
* Új Active Directory tartomány-és tartományvezérlők a CloudSimple-ben futó saját felhőben
* Azure Active Directory szolgáltatás

Ez az útmutató ismerteti az Azure AD identitás-forrásként való beállításához szükséges feladatokat.  Az Azure-ban futó helyszíni Active Directory vagy Active Directory használatával kapcsolatos információkért tekintse meg a [vCenter-identitások beállítása az Active Directory használatához](set-vcenter-identity.md) című témakört az Identity forrás beállítása című témakörben.

## <a name="about-azure-ad"></a>Az Azure AD ismertetése

Az Azure AD a Microsoft több-bérlős, felhőalapú címtár-és Identitáskezelés-kezelő szolgáltatása.  Az Azure AD méretezhető, konzisztens és megbízható hitelesítési mechanizmust biztosít a felhasználók számára az Azure különböző szolgáltatásainak hitelesítéséhez és eléréséhez.  Emellett biztonságos LDAP-szolgáltatásokat biztosít bármely harmadik féltől származó szolgáltatás számára az Azure AD hitelesítés/identitás forrásaként való használatához.  Az Azure AD ötvözi az alapszintű címtárszolgáltatások, a speciális identitások szabályozását és az alkalmazás-hozzáférés kezelését, amellyel hozzáférést biztosíthat a privát felhőhöz a privát felhőt felügyelő felhasználók számára.

Ha az Azure AD-t identitás-forrásként szeretné használni a vCenter-ben, be kell állítania az Azure AD-t és az Azure AD tartományi szolgáltatásokat. Kövesse az alábbi utasításokat:

1. [Az Azure AD és az Azure AD tartományi szolgáltatások beállítása](#set-up-azure-ad-and-azure-ad-domain-services)
2. [Személyazonossági forrás beállítása a saját felhőalapú vCenter](#set-up-an-identity-source-on-your-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Az Azure AD és az Azure AD tartományi szolgáltatások beállítása

Az első lépések előtt globális rendszergazdai jogosultságokkal kell rendelkeznie az Azure-előfizetéséhez.  Az alábbi lépések általános útmutatást nyújtanak. A részleteket az Azure dokumentációja tartalmazza.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Ha már rendelkezik Azure AD-val, kihagyhatja ezt a szakaszt.

1. Állítsa be az Azure AD-t az előfizetéséhez az  [Azure ad dokumentációjában](../active-directory/fundamentals/active-directory-whatis.md)leírtak szerint.
2. Engedélyezze az előfizetéshez prémium szintű Azure Active Directory a [regisztráció a prémium szintű Azure Active Directoryra](../active-directory/fundamentals/active-directory-get-started-premium.md)című témakörben leírtak szerint.
3. Állítson be egy egyéni tartománynevet, és ellenőrizze az egyéni tartománynevet az [Egyéni tartománynév hozzáadása a Azure Active Directoryhoz](../active-directory/fundamentals/add-custom-domain.md)című témakörben leírtak szerint.
    1. Hozzon létre egy DNS-rekordot a tartományregisztrálónál az Azure-ban megadott információval.
    2. Állítsa be az egyéni tartománynevet elsődleges tartományként.

Opcionálisan más Azure AD-funkciókat is konfigurálhat.  Ezek nem szükségesek az Azure AD-vel történő vCenter-hitelesítés engedélyezéséhez.

### <a name="azure-ad-domain-services"></a>Azure AD tartományi szolgáltatások

> [!NOTE]
> Ez egy fontos lépés az Azure AD-nek a vCenter identitási forrásaként való engedélyezéséhez.  A problémák elkerülése érdekében győződjön meg arról, hogy az összes lépést helyesen hajtja végre.

1. Engedélyezze az Azure AD tartományi szolgáltatásokat [a Azure Active Directory tartományi szolgáltatások engedélyezése a Azure Portal használatával](../active-directory-domain-services/tutorial-create-instance.md)című témakörben leírtak szerint.
2. Állítsa be azt a hálózatot, amelyet az Azure AD tartományi szolgáltatások használni fognak a [Azure Active Directory Domain Services engedélyezése a Azure Portal használatával](../active-directory-domain-services/tutorial-create-instance.md)című témakörben leírtak szerint.
3. Konfigurálja a felügyeleti csoportot a Azure AD Domain Services kezeléséhez az [Azure Portal használatával Azure Active Directory Domain Services engedélyezése](../active-directory-domain-services/tutorial-create-instance.md)című témakörben leírtak szerint.
4. Frissítse a Azure AD Domain Services DNS-beállításait a [Azure Active Directory Domain Services engedélyezése](../active-directory-domain-services/tutorial-create-instance.md)című témakörben leírtak szerint.  Ha az interneten keresztül szeretne csatlakozni az AD-hez, állítsa be az Azure AD tartományi szolgáltatások nyilvános IP-címéhez tartozó DNS-rekordot a tartománynévre.
5. Jelszó-kivonatolási szinkronizálás engedélyezése a felhasználók számára.  Ez a lépés lehetővé teszi az NT LAN Manager (NTLM) és a Kerberos-hitelesítéshez szükséges jelszó-kivonatok szinkronizálását Azure AD Domain Services. A jelszókivonat-adatok szinkronizálásának beállítása után a felhasználók a vállalati hitelesítő adataikkal jelentkezhetnek be a felügyelt tartományba. Lásd: [jelszó-kivonatolási szinkronizálás engedélyezése Azure Active Directory Domain Servicesra](../active-directory-domain-services/tutorial-create-instance.md).
    1. Ha csak felhőalapú felhasználók jelennek meg, akkor az <a href="https://myapps.microsoft.com/" target="_blank">Azure ad hozzáférési paneljén</a> meg kell változtatniuk a jelszavukat, hogy a jelszó-kivonatok az NTLM vagy a Kerberos által megkövetelt formátumban legyenek tárolva.  A [csak felhőalapú felhasználói fiókok esetében kövesse a jelszó-kivonat szinkronizálásának engedélyezése a felügyelt tartományhoz](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)című témakör utasításait.  Ezt a lépést az egyéni felhasználók és az Azure AD-címtárban létrehozott új felhasználók, az Azure Portal vagy az Azure AD PowerShell-parancsmagok használatával kell elvégezni. Az Azure AD tartományi szolgáltatásokhoz hozzáférést igénylő felhasználóknak az <a href="https://myapps.microsoft.com/" target="_blank">Azure ad hozzáférési paneljén</a> kell megadniuk a profilt a jelszó módosításához.

        > [!NOTE]
        > Ha a szervezet rendelkezik csak felhőalapú felhasználói fiókokkal, az Active Directory Domain Servicest használó összes felhasználónak módosítania kell a jelszavát. A csak felhőalapú felhasználói fiókok olyan fiókok, amelyek az Azure AD-címtárban lettek létrehozva az Azure Portal vagy Azure AD PowerShell-parancsmagok használatával. Az ilyen felhasználói fiókok nem a helyszíni címtárból szinkronizálódnak.

    2. Ha a helyszíni Active Directoryból szinkronizálja a jelszavakat, kövesse az [Active Directory dokumentációjának](../active-directory-domain-services/tutorial-configure-password-hash-sync.md)lépéseit.

6.  Konfigurálja a biztonságos LDAP-t a Azure Active Directory Domain Services a [biztonságos LDAP (LDAPS) konfigurálása Azure ad Domain Services felügyelt tartományhoz](../active-directory-domain-services/tutorial-configure-ldaps.md)című témakörben leírtak szerint.
    1. Töltse fel az Azure-témakörben leírtak szerint a biztonságos LDAP-hez készült tanúsítványt a Secure LDAP használatára vonatkozó tanúsítvány [beszerzése](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)érdekében.  A CloudSimple azt javasolja, hogy a hitelesítésszolgáltató által kiadott aláírt tanúsítványt használja, hogy a vCenter megbízható legyen a tanúsítványban.
    2. A biztonságos LDAP engedélyezése a [Azure ad Domain Services felügyelt tartomány biztonságos LDAP (LDAPS) engedélyezése](../active-directory-domain-services/tutorial-configure-ldaps.md)című részében leírtak szerint.
    3. Mentse a tanúsítvány nyilvános részét (a titkos kulcs nélkül). cer formátumban a vCenter való használathoz az Identity forrás konfigurálásakor.
    4. Ha az Azure AD tartományi szolgáltatásokhoz való internet-hozzáférésre van szükség, engedélyezze a biztonságos hozzáférés engedélyezése az interneten keresztül lehetőséget.
    5. Adja hozzá a bejövő biztonsági szabályt az Azure AD tartományi szolgáltatások NSG a 636-as TCP-porthoz.

## <a name="set-up-an-identity-source-on-your-private-cloud-vcenter"></a>Személyazonossági forrás beállítása a saját felhőalapú vCenter

1. Megadhatja a saját felhőalapú vCenter vonatkozó [jogosultságokat](escalate-private-cloud-privileges.md) .
2. Gyűjtsön a személyazonossági forrás beállításához szükséges konfigurációs paramétereket.

    | **Beállítás** | **Leírás** |
    |------------|-----------------|
    | **Név** | Az Identity forrás neve. |
    | **A felhasználók alapszintű megkülönböztető neve** | A felhasználók alapszintű megkülönböztető neve.  Az Azure AD esetében használja a következőt: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  példa: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com` .|
    | **Tartománynév** | A tartomány teljes tartományneve, például example.com. Ne adjon meg IP-címet ebben a szövegmezőben. |
    | **Tartomány aliasa** | *(nem kötelező)* A tartomány NetBIOS-neve. Adja hozzá a Active Directory tartomány NetBIOS-nevét az Identity forrás aliasként, ha az SSPI-hitelesítést használja. |
    | **A csoportok alapszintű megkülönböztető neve** | A csoportok alapszintű megkülönböztető neve. Az Azure AD esetében használja a következőt: `OU=AADDC Users,DC=<domain>,DC=<domain suffix>`  példa: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **Elsődleges kiszolgáló URL-címe** | A tartomány elsődleges tartományvezérlője LDAP-kiszolgálója.<br><br>Használja a következő formátumot: `ldaps://hostname:port`. A port általában a 636 LDAP-kapcsolatokhoz. <br><br>Az `ldaps://` elsődleges vagy másodlagos LDAP URL-cím használata esetén olyan tanúsítványra van szükség, amely a Active Directory kiszolgáló LDAPS végpontjának megbízhatóságát hozza létre. |
    | **Másodlagos kiszolgáló URL-címe** | A feladatátvételhez használt másodlagos tartományvezérlői LDAP-kiszolgáló címe. |
    | **Tanúsítvány kiválasztása** | Ha LDAPs-t szeretne használni a Active Directory LDAP-kiszolgálóval vagy a OpenLDAP-kiszolgáló identitásával, akkor `ldaps://` az URL-cím szövegmezőbe való beírása után a tanúsítvány választása gomb jelenik meg. Másodlagos URL-cím megadása nem kötelező. |
    | **Felhasználónév** | Azon felhasználó azonosítója, aki legalább olvasási hozzáféréssel rendelkezik a felhasználók és csoportok alapszintű DN-hez. |
    | **Jelszó** | A Felhasználónév által megadott felhasználó jelszava. |

3. A jogosultságok kiterjesztését követően jelentkezzen be a saját Felhőbeli vCenter.
4. A Azure Active Directory identitás forrásaként való beállításához kövesse az [Identity forrás hozzáadása a vCenter](set-vcenter-identity.md#add-an-identity-source-on-vcenter) az előző lépésekben szereplő értékek alapján című témakör utasításait.
5. Felhasználók/csoportok hozzáadása az Azure AD-ből vCenter-csoportokhoz a következő témakörben leírtak szerint: [Tagok hozzáadása vCenter egyetlen Sign-On csoporthoz](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

> [!CAUTION]
> Az új felhasználókat csak a *Cloud-Owner-Group*, a *Cloud-Global-cluster-admin-Group*, a *Cloud-Global-Storage-admin-Group*, a *Cloud-Global-Network-admin-Group* vagy a *Cloud-Global-VM-admin-Group* szolgáltatáshoz kell hozzáadni.  A *rendszergazdák* csoportba felvett felhasználók automatikusan el lesznek távolítva.  Csak a *rendszergazdák* csoporthoz kell tartoznia a szolgáltatásfiókok hozzáadásához.

## <a name="next-steps"></a>Következő lépések

* [További információ a Private Cloud Permission modelről](learn-private-cloud-permissions.md)
