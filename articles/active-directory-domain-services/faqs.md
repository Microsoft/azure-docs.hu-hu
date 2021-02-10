---
title: Gyakori kérdések a Azure AD Domain Servicesról | Microsoft Docs
description: Olvassa el és Ismerje meg a konfigurációval, a felügyelettel és a rendelkezésre állással kapcsolatos gyakori kérdéseket Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/09/2021
ms.author: justinha
ms.openlocfilehash: 3d0f2b44f37cb318be2117b5dc5d8b42b418ff19
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100090989"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Gyakori kérdések (GYIK) a Azure Active Directory (AD) tartományi szolgáltatásokkal kapcsolatban

Ez az oldal válaszol a Azure Active Directory Domain Servicesekkel kapcsolatos gyakori kérdésekre.

## <a name="configuration"></a>Konfiguráció

* [Több felügyelt tartományt is Létrehozhatok egyetlen Azure AD-címtárhoz?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Engedélyezhető a Azure AD Domain Services egy klasszikus virtuális hálózaton?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Engedélyezhető a Azure AD Domain Services egy Azure Resource Manager virtuális hálózaton?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Áttelepíthetem a meglévő felügyelt tartományt egy klasszikus virtuális hálózatról egy Resource Manager-alapú virtuális hálózatra?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Engedélyezhető a Azure AD Domain Services egy Azure CSP-előfizetésben?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Engedélyezhető Azure AD Domain Services egy összevont Azure AD-címtárban? Nem szinkronizálom a jelszó-kivonatokat az Azure AD-vel. Engedélyezhető Azure AD Domain Services ehhez a címtárhoz?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Elérhetővé tehetek Azure AD Domain Services az előfizetésen belüli több virtuális hálózatban?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Engedélyezhető Azure AD Domain Services a PowerShell használatával?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Engedélyezhető a Azure AD Domain Services Resource Manager-sablon használatával?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Adhatok tartományvezérlőket Azure AD Domain Services felügyelt tartományhoz?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Meghívhatják a vendég felhasználókat a saját könyvtárra Azure AD Domain Services?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [Áthelyezhetek egy meglévő Azure AD Domain Services felügyelt tartományt egy másik előfizetésbe, erőforráscsoporthoz, régióba vagy virtuális hálózatra?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [A Azure AD Domain Services magas rendelkezésre állási lehetőségeket is tartalmaz?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Több felügyelt tartományt is Létrehozhatok egyetlen Azure AD-címtárhoz?
Nem. Egyetlen Azure AD-címtárhoz Azure AD Domain Services egyetlen felügyelt tartományt hozhat létre.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Engedélyezhető a Azure AD Domain Services egy klasszikus virtuális hálózaton?
A klasszikus virtuális hálózatok nem támogatottak új központi telepítések esetén. A klasszikus virtuális hálózatokban üzembe helyezett meglévő felügyelt tartományok továbbra is támogatottak, amíg az 2023-es március 1-én megszűnik. A meglévő telepítések esetében [áttelepítheti Azure ad Domain Services a klasszikus virtuális hálózati modellből a Resource Managerbe](migrate-from-classic-vnet.md).

További információkért tekintse meg a [hivatalos elavult közleményt](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Engedélyezhető a Azure AD Domain Services egy Azure Resource Manager virtuális hálózaton?
Igen. Azure AD Domain Services engedélyezhető Azure Resource Manager virtuális hálózaton. A klasszikus Azure-beli virtuális hálózatok már nem érhetők el felügyelt tartomány létrehozásakor.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Áttelepíthetem a meglévő felügyelt tartományt egy klasszikus virtuális hálózatról egy Resource Manager-alapú virtuális hálózatra?
Igen. További információ: [Azure ad Domain Services migrálása a klasszikus virtuális hálózati modellből a Resource Managerbe](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Engedélyezhető a Azure AD Domain Services egy Azure CSP-előfizetésben?
Igen. További információ: [Azure ad Domain Services engedélyezése az Azure CSP-előfizetésekben](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Engedélyezhető Azure AD Domain Services egy összevont Azure AD-címtárban? Nem szinkronizálom a jelszó-kivonatokat az Azure AD-vel. Engedélyezhető Azure AD Domain Services ehhez a címtárhoz?
Nem. Ha NTLM-vagy Kerberos-kapcsolaton keresztül kívánja hitelesíteni a felhasználókat, Azure AD Domain Servicesnak hozzá kell férnie a felhasználói fiókok jelszavas kivonatához. Összevont címtárban a jelszó-kivonatok nem az Azure AD-címtárban tárolódnak. Ezért Azure AD Domain Services nem működik ilyen Azure AD-címtárakkal.

Ha azonban Azure AD Connect használ a jelszó-kivonatolási szinkronizáláshoz, használhatja a Azure AD Domain Services, mert a jelszó-kivonatoló értékeket az Azure AD tárolja.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Elérhetővé tehetek Azure AD Domain Services az előfizetésen belüli több virtuális hálózatban?
Maga a szolgáltatás nem támogatja közvetlenül ezt a forgatókönyvet. A felügyelt tartomány egyszerre csak egy virtuális hálózatban érhető el. A több virtuális hálózat közötti kapcsolat azonban beállítható úgy, hogy a Azure AD Domain Servicest más virtuális hálózatokhoz tegye elérhetővé. További információ: [Hogyan csatlakoztathatók a virtuális hálózatok az Azure-ban VPN-átjárók](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) vagy [virtuális hálózati](../virtual-network/virtual-network-peering-overview.md)kapcsolatok használatával.

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Engedélyezhető Azure AD Domain Services a PowerShell használatával?
Igen. További információ: [a Azure ad Domain Services engedélyezése a PowerShell használatával](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Engedélyezhető a Azure AD Domain Services Resource Manager-sablon használatával?
Igen, létrehozhat egy Azure AD Domain Services felügyelt tartományt egy Resource Manager-sablon használatával. A sablon üzembe helyezése előtt létre kell hozni egy egyszerű szolgáltatásnevet és egy Azure AD-csoportot az Azure Portal vagy Azure PowerShell használatával. További információ: [Azure AD DS Managed domain létrehozása Azure Resource Manager sablon használatával](template-create-instance.md). Ha Azure AD Domain Services felügyelt tartományt hoz létre a Azure Portalban, akkor is lehetőség van a sablon exportálására további üzemelő példányokkal való használatra.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Adhatok tartományvezérlőket Azure AD Domain Services felügyelt tartományhoz?
Nem. A Azure AD Domain Services által megadott tartomány felügyelt tartomány. A tartományhoz tartozó tartományvezérlőket nem kell kiépíteni, konfigurálni vagy más módon kezelni. Ezeket a felügyeleti tevékenységeket a Microsoft szolgáltatásként biztosítjuk. Ezért nem adhat hozzá további tartományvezérlőket (írható vagy olvasható) a felügyelt tartományhoz.

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Meghívhatják a vendég felhasználókat a saját könyvtárra Azure AD Domain Services?
Nem. Az Azure ad [B2B](../active-directory/external-identities/what-is-b2b.md) Meghívási folyamatát használó vendég felhasználók a Azure ad Domain Services felügyelt tartományba vannak szinkronizálva. A felhasználók jelszava azonban nem az Azure AD-címtárban van tárolva. Ezért a Azure AD Domain Services nem lehet szinkronizálni az NTLM-és Kerberos-kivonatokat a felhasználók számára a felügyelt tartományba. Ilyen felhasználók nem jelentkezhetnek be, illetve nem csatlakozhatnak számítógépekhez a felügyelt tartományhoz.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Áthelyezhetek egy meglévő Azure AD Domain Services felügyelt tartományt egy másik előfizetésbe, erőforráscsoporthoz, régióba vagy virtuális hálózatra?
Nem. Azure AD Domain Services felügyelt tartomány létrehozása után nem helyezheti át a felügyelt tartományt más erőforráscsoporthoz, virtuális hálózatra, előfizetésre stb. Ügyeljen arra, hogy a felügyelt tartomány központi telepítésekor a legmegfelelőbb előfizetést, erőforráscsoportot, régiót és virtuális hálózatot válassza ki.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>A Azure AD Domain Services magas rendelkezésre állási lehetőségeket is tartalmaz?

Igen. Minden Azure AD Domain Services felügyelt tartomány két tartományvezérlőt tartalmaz. Ezek a tartományvezérlők nem kezelhetők és nem csatlakoznak, hanem a felügyelt szolgáltatás részét képezik. Ha Availability Zonest támogató régióba telepít Azure AD Domain Services, a tartományvezérlők a zónák között oszlanak meg. A Availability Zonest nem támogató régiókban a tartományvezérlők a rendelkezésre állási csoportokon keresztül oszlanak meg. Ehhez a terjesztéshez nincsenek konfigurációs beállítások vagy felügyeleti vezérlők. További információ: a [virtuális gépek rendelkezésre állási lehetőségei az Azure-ban](../virtual-machines/availability.md).

## <a name="administration-and-operations"></a>Felügyelet és műveletek

* [Csatlakozhatok a felügyelt tartományhoz tartozó tartományvezérlőhöz a Távoli asztal használatával?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Engedélyeztem Azure AD Domain Services. Milyen felhasználói fiókot használok a tartományhoz való csatlakozáshoz ehhez a tartományhoz?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Vannak tartományi rendszergazdai jogosultságok a Azure AD Domain Services által biztosított felügyelt tartományhoz?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Módosíthatom a csoporttagság a felügyelt tartományokban található LDAP vagy más AD felügyeleti eszközök használatával?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Mennyi időt vesz igénybe, hogy az Azure AD-címtárban végrehajtott módosítások megjelenjenek a felügyelt tartományban?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Bővíthető a Azure AD Domain Services által biztosított felügyelt tartomány sémája?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Módosíthatom vagy Hozzáadhatok DNS-rekordokat a saját felügyelt tartományában?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Mi a jelszó élettartama házirend egy felügyelt tartományon?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Biztosít Azure AD Domain Services AD-fiók zárolásának védelmét?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Konfigurálható a elosztott fájlrendszer (DFS) és a replikáció Azure AD Domain Serviceson belül?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Hogyan történik a Windows-frissítések alkalmazása Azure AD Domain Services?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Csatlakozhatok a felügyelt tartományhoz tartozó tartományvezérlőhöz a Távoli asztal használatával?
Nem. Nem rendelkezik engedéllyel a felügyelt tartományhoz való kapcsolódáshoz a Távoli asztal használatával. A *HRE DC rendszergazdák* csoport tagjai felügyelhetik a felügyelt TARTOMÁNYT az ad felügyeleti eszközök, például a Active Directory felügyeleti központ (ADAC) vagy az ad PowerShell használatával. Ezek az eszközök a felügyelt tartományhoz csatlakoztatott Windows Server *Távoli kiszolgálófelügyelet eszközei* funkciójával vannak telepítve. További információ: [felügyeleti virtuális gép létrehozása Azure ad Domain Services felügyelt tartomány konfigurálásához és felügyeletéhez](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Engedélyeztem Azure AD Domain Services. Milyen felhasználói fiókot használok a tartományhoz való csatlakozáshoz ehhez a tartományhoz?
A felügyelt tartomány részét képező felhasználói fiókok csatlakozhatnak egy virtuális géphez. A *HRE DC rendszergazdák* csoport tagjai távoli asztali hozzáférést kapnak a felügyelt tartományhoz csatlakoztatott gépekhez.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Vannak tartományi rendszergazdai jogosultságok a Azure AD Domain Services által biztosított felügyelt tartományhoz?
Nem. Nem kap rendszergazdai jogosultságokat a felügyelt tartományon. A tartományi *rendszergazda* és a *vállalati rendszergazdai* jogosultságok nem használhatók a tartományon belül. A helyszíni Active Directory tartományi rendszergazda vagy vállalati rendszergazdai csoportok tagjai nem kapnak tartományi/vállalati rendszergazdai jogosultságokat a felügyelt tartományhoz.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Módosíthatom a csoporttagság a felügyelt tartományokban található LDAP vagy más AD felügyeleti eszközök használatával?
Azok a felhasználók és csoportok, amelyek Azure Active Directoryról Azure AD Domain Services szinkronizálva, nem módosíthatók, mert a forrás forrása Azure Active Directory. Ide tartoznak a felhasználók vagy csoportok áthelyezése a AADDC felhasználók által felügyelt szervezeti egységből egy egyéni szervezeti egységbe. Előfordulhat, hogy a felügyelt tartományból származó bármely felhasználó vagy csoport módosítva lesz.  

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Mennyi időt vesz igénybe, hogy az Azure AD-címtárban végrehajtott módosítások megjelenjenek a felügyelt tartományban?
Az Azure AD-címtárban az Azure AD felhasználói felület vagy a PowerShell használatával végzett módosítások automatikusan szinkronizálhatók a felügyelt tartományba. Ez a szinkronizálási folyamat a háttérben fut. Nincs meghatározott időtartam a szinkronizáláshoz az összes objektum módosításának befejezéséhez.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Bővíthető a Azure AD Domain Services által biztosított felügyelt tartomány sémája?
Nem. A sémát a Microsoft felügyeli a felügyelt tartományhoz. A Azure AD Domain Services nem támogatja a séma-bővítményeket.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Módosíthatom vagy Hozzáadhatok DNS-rekordokat a saját felügyelt tartományában?
Igen. A *HRE DC-rendszergazdák* csoport tagjai *DNS-rendszergazdai* jogosultságokat kapnak a felügyelt tartomány DNS-rekordjainak módosításához. Azok a felhasználók, akik a felügyelt tartományhoz csatlakoznak a DNS-kezelő konzolon a Windows Servert futtató gépen, a DNS-t kezelhetik. A DNS-kezelő konzol használatához telepítse a *DNS-kiszolgáló eszközöket*, amelyek a kiszolgáló *Távoli kiszolgálófelügyelet eszközei* választható funkciójának részét képezik. További információ: a [DNS felügyelete Azure ad Domain Services felügyelt tartományban](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Mi a jelszó élettartama házirend egy felügyelt tartományon?
Egy Azure AD Domain Services felügyelt tartomány alapértelmezett jelszójának élettartama 90 nap. A jelszó élettartama nincs szinkronizálva az Azure AD-ben konfigurált jelszó-élettartammal. Ezért előfordulhat, hogy a felhasználók jelszavai lejárnak a felügyelt tartományban, de továbbra is érvényesek az Azure AD-ben. Ilyen esetekben a felhasználóknak módosítaniuk kell a jelszavukat az Azure AD-ben, és az új jelszó szinkronizálva lesz a felügyelt tartományba. Ha módosítani szeretné a jelszó alapértelmezett élettartamát egy felügyelt tartományban, [létrehozhat és konfigurálhat egyéni jelszóházirendek.](password-policy.md)

Emellett a *DisablePasswordExpiration* Azure ad-beli jelszavas szabályzata szinkronizálva lett egy felügyelt tartományba. Ha a *DisablePasswordExpiration* az Azure ad-ben egy felhasználóra alkalmazza, a felügyelt tartományban lévő szinkronizált felhasználó *UserAccountControl* értékét *DONT_EXPIRE_PASSWORD* alkalmazta a rendszer.

Amikor a felhasználók visszaállítják a jelszavukat az Azure AD-ben, a rendszer a *forceChangePasswordNextSignIn = True* attribútumot alkalmazza. A felügyelt tartomány szinkronizálja ezt az attribútumot az Azure AD-ből. Ha a felügyelt tartomány észleli a *forceChangePasswordNextSignIn* egy szinkronizált felhasználó számára az Azure ad-ből, a felügyelt tartomány *pwdLastSet* attribútuma *0* értékre van állítva, amely érvényteleníti a jelenleg beállított jelszót.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Biztosít Azure AD Domain Services AD-fiók zárolásának védelmét?
Igen. A felügyelt tartomány 2 percen belül öt érvénytelen jelszóval próbálkozik, mert a felhasználói fiók 30 percig kizárja a felhasználót. 30 perc elteltével a felhasználói fiók automatikusan fel lesz oldva. A felügyelt tartomány jelszavas próbálkozásai érvénytelenek, az Azure AD-ben nem zárhatók ki a felhasználói fiókok. A felhasználói fiók csak a Azure AD Domain Services felügyelt tartományon belül van zárolva. További információ: [jelszó-és fiókzárolási házirendek a felügyelt tartományokon](password-policy.md).

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Konfigurálható a elosztott fájlrendszer és a replikálás a Azure AD Domain Serviceson belül?
Nem. Azure AD Domain Services használata esetén a elosztott fájlrendszer (DFS) és a replikáció nem érhető el.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Hogyan történik a Windows-frissítések alkalmazása Azure AD Domain Services?
A felügyelt tartomány tartományvezérlőinek automatikusan alkalmazzák a szükséges Windows-frissítéseket. Itt nem lehet konfigurálni vagy felügyelni. Győződjön meg arról, hogy nem hoz létre olyan hálózati biztonsági csoportra vonatkozó szabályokat, amelyek letiltják a Windows-frissítések kimenő forgalmát. A felügyelt tartományhoz csatlakoztatott saját virtuális gépek esetében Ön felelős a szükséges operációs rendszer-és alkalmazás-frissítések konfigurálásához és alkalmazásához.

## <a name="billing-and-availability"></a>Számlázás és rendelkezésre állás

* [Díjköteles szolgáltatás Azure AD Domain Services?](#is-azure-ad-domain-services-a-paid-service)
* [Van ingyenes próbaverzió a szolgáltatáshoz?](#is-there-a-free-trial-for-the-service)
* [Szüneteltethető Azure AD Domain Services felügyelt tartomány?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Végezhetek feladatátvételt Azure AD Domain Services egy másik régióba egy DR eseményhez?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Lekérhetek Azure AD Domain Services a nagyvállalati mobilitási csomag (EMS) részeként? Szükség van-e a Azure AD Domain Services használatára prémium szintű Azure AD?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [Milyen Azure-régiók érhetők el a szolgáltatásban?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Díjköteles szolgáltatás Azure AD Domain Services?
Igen. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/active-directory-ds/) olvasható.

### <a name="is-there-a-free-trial-for-the-service"></a>Van ingyenes próbaverzió a szolgáltatáshoz?
Azure AD Domain Services az Azure ingyenes próbaverziójában szerepel. Regisztrálhat az [Azure ingyenes, egy hónapos próbaidőszakára](https://azure.microsoft.com/pricing/free-trial/).

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Szüneteltethető Azure AD Domain Services felügyelt tartomány?
Nem. Miután engedélyezte Azure AD Domain Services felügyelt tartományt, a szolgáltatás a kiválasztott virtuális hálózaton belül elérhető, amíg nem törli a felügyelt tartományt. Nem lehet szüneteltetni a szolgáltatást. A számlázás óránként folytatódik, amíg nem törli a felügyelt tartományt.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Végezhetek feladatátvételt Azure AD Domain Services egy másik régióba egy DR eseményhez?
Igen, a felügyelt tartomány földrajzi rugalmasságának biztosításához létrehozhat egy további [replikát](tutorial-create-replica-set.md) egy olyan Azure-régióban, amely támogatja az Azure AD DS-t. A replika-készletek ugyanazt a névteret és konfigurációt használják a felügyelt tartományhoz.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Lekérhetek Azure AD Domain Services a nagyvállalati mobilitási csomag (EMS) részeként? Szükség van-e a Azure AD Domain Services használatára prémium szintű Azure AD?
Nem. A Azure AD Domain Services egy utólagos elszámolású Azure-szolgáltatás, amely nem része az EMS-nek. Azure AD Domain Services az Azure AD összes kiadásával használható (ingyenes és prémium szintű). A használattól függően óradíjat számolunk fel.

### <a name="what-azure-regions-is-the-service-available-in"></a>Milyen Azure-régiók érhetők el a szolgáltatásban?
Az Azure- [szolgáltatások régiónként](https://azure.microsoft.com/regions/#services/) lapon megtekintheti az Azure-régiók listáját, ahol a Azure ad Domain Services elérhető.

## <a name="troubleshooting"></a>Hibaelhárítás

A Azure AD Domain Services konfigurálásával és felügyeletével kapcsolatos gyakori problémák megoldásához tekintse meg a [hibaelhárítási útmutatót](troubleshoot.md) .

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure AD Domain Servicesről, tekintse meg a [Mi az a Azure Active Directory Domain Services?](overview.md)című témakört.

Első lépésként tekintse meg [Azure Active Directory Domain Services felügyelt tartomány létrehozása és konfigurálása](tutorial-create-instance.md)című témakört.
