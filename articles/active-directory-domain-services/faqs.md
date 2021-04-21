---
title: Gyakori kérdések a Azure AD Domain Services | Microsoft Docs
description: Olvassa el és értse meg a konfigurációval, felügyelettel és rendelkezésre állásmal kapcsolatos gyakori kérdéseket a Azure Active Directory Domain Services
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
ms.openlocfilehash: 32dcf2b387231d50796de0036388b53cab83bf72
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749329"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Gyakori kérdések (GYIK) az Azure Active Directory (AD) Tartományi szolgáltatásokról

Ez az oldal a gyakori kérdésekre ad választ a Azure Active Directory Domain Services.

## <a name="configuration"></a>Konfiguráció

* [Létrehozhatok több felügyelt tartományt egyetlen Azure AD-címtárhoz?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Engedélyezem a Azure AD Domain Services virtuális hálózaton?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Engedélyezem a Azure AD Domain Services virtuális Azure Resource Manager való hozzáférését?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Átemelheti a meglévő felügyelt tartományomat egy klasszikus virtuális hálózatról egy Resource Manager virtuális hálózatra?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Engedélyezem a Azure AD Domain Services egy Azure CSP (Felhőszolgáltató) előfizetésben?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Engedélyezek egy Azure AD Domain Services Azure AD-címtárban? Nem szinkronizálom a jelszó-hasheket az Azure AD-be. Engedélyezem a Azure AD Domain Services a címtárhoz?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Elérhetővé Azure AD Domain Services több virtuális hálózatban az előfizetésen belül?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [Engedélyezem a Azure AD Domain Services PowerShell használatával?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Engedélyezem a Azure AD Domain Services egy Resource Manager sablonnal?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Hozzáadhatok tartományvezérlőket egy felügyelt Azure AD Domain Services tartományhoz?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Meghívhatóak a vendégfelhasználók a címtárhasználati Azure AD Domain Services?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [Áthelyezhetek egy meglévő Azure AD Domain Services felügyelt tartományt egy másik előfizetésbe, erőforráscsoportba, régióba vagy virtuális hálózatba?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Átnevezhető egy meglévő Azure AD Domain Services tartománynév?](#can-i-rename-an-existing-azure-ad-domain-services-domain-name)
* [Tartalmaz Azure AD Domain Services magas rendelkezésre állási lehetőségeket?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Létrehozhatok több felügyelt tartományt egyetlen Azure AD-címtárhoz?
Nem. Csak egyetlen felügyelt tartományt hozhat létre, amelyet a Azure AD Domain Services egyetlen Azure AD-címtárhoz.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Engedélyezem a Azure AD Domain Services klasszikus virtuális hálózaton?
A klasszikus virtuális hálózatok nem támogatottak az új üzemelő példányok esetében. A klasszikus virtuális hálózatokon üzembe helyezett meglévő felügyelt tartományok támogatása 2023. március 1-jén való elaánláig támogatott marad. Meglévő üzemelő példányok esetén a klasszikus Azure AD Domain Services [modellből](migrate-from-classic-vnet.md)a virtuális gépre Resource Manager.

További információért tekintse meg a hivatalos elalasztó [értesítést.](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Engedélyezem a Azure AD Domain Services virtuális Azure Resource Manager virtuális hálózaton?
Igen. Azure AD Domain Services virtuális hálózaton engedélyezhetők Azure Resource Manager virtuális hálózatokon. Felügyelt tartomány létrehozásakor a klasszikus Azure-beli virtuális hálózatok már nem érhetők el.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Át tudom miulálni a meglévő felügyelt tartományomat egy klasszikus virtuális hálózatról egy Resource Manager virtuális hálózatra?
Igen. További információ: [Áttelepítés](migrate-from-classic-vnet.md)Azure AD Domain Services a klasszikus virtuális hálózati modellből a Resource Manager.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Engedélyezem a Azure AD Domain Services egy Azure CSP (Felhőszolgáltató) előfizetésben?
Igen. További információért tekintse meg, hogyan engedélyezheti a Azure AD Domain Services [a Azure CSP előfizetésben.](csp.md)

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Engedélyezem a Azure AD Domain Services egy összevont Azure AD-címtárban? Nem szinkronizálom a jelszó-hasheket az Azure AD-be. Engedélyezem a Azure AD Domain Services címtárhoz?
Nem. A felhasználók NTLM-en vagy Kerberoson keresztüli hitelesítéséhez Azure AD Domain Services kell hozzáférnie a felhasználói fiókok jelszó-előjeléhez. Az összevont címtárakban a jelszó-hashe-ket a rendszer nem az Azure AD-címtárban tárolja. Ezért Azure AD Domain Services nem működik ilyen Azure AD-könyvtárakkal.

Ha azonban jelszó-kivonatszinkronizáláshoz Azure AD Connect, használhatja a Azure AD Domain Services, mert a jelszó kivonatértékeket az Azure AD tárolja.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Elérhetővé Azure AD Domain Services több virtuális hálózatban az előfizetésen belül?
Maga a szolgáltatás nem támogatja közvetlenül ezt a forgatókönyvet. A felügyelt tartomány egyszerre csak egy virtuális hálózatban érhető el. A több virtuális hálózat közötti kapcsolatot azonban konfigurálhatja úgy, hogy az Azure AD Domain Services más virtuális hálózatok számára is elérhetővé tegye. További információ: Virtuális hálózatok csatlakoztatása az [Azure-ban VPN-átjárók](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) vagy virtuális hálózatok közötti [társviszony-létesítés használatával.](../virtual-network/virtual-network-peering-overview.md)

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>Engedélyezem a Azure AD Domain Services PowerShell használatával?
Igen. További információért tekintse meg, hogyan engedélyezheti a Azure AD Domain Services [a PowerShell használatával.](powershell-create-instance.md)

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Engedélyezem a Azure AD Domain Services egy Resource Manager sablonnal?
Igen, létrehozhat egy Azure AD Domain Services tartományt egy Resource Manager használatával. A sablon üzembe helyezése előtt létre kell Azure Portal vagy Azure PowerShell szolgáltatásnévvel és felügyeleti Azure AD-csoporttal. További információkért [lásd: Felügyelt Azure AD DS létrehozása Azure Resource Manager sablon használatával.](template-create-instance.md) Amikor felügyelt Azure AD Domain Services hoz létre a Azure Portal, lehetősége van exportálni a sablont további üzemelő példányokkal való használatra.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Hozzáadhatok tartományvezérlőket egy felügyelt Azure AD Domain Services tartományhoz?
Nem. A felhasználó által Azure AD Domain Services tartomány egy felügyelt tartomány. Ehhez a tartományhoz nem kell tartományvezérlőket kiépítene, konfigurálnia vagy egyéb módon kezelnie. Ezeket a felügyeleti tevékenységeket a Microsoft biztosítja szolgáltatásként. Ezért a felügyelt tartományhoz nem adhat hozzá további tartományvezérlőket (írás-írás vagy csak olvasható).

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Meghívhatóak a vendégfelhasználók a címtárhasználati Azure AD Domain Services?
Nem. Az [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) meghívási folyamatával az Azure AD-címtárba meghívott vendégfelhasználók szinkronizálva lesznek Azure AD Domain Services felügyelt tartományba. A felhasználók jelszavai azonban nem az Azure AD-címtárban vannak tárolva. Ezért Azure AD Domain Services a felhasználók NTLM- és Kerberos-hashei nem szinkronizálhatóak a felügyelt tartományba. Az ilyen felhasználók nem tudnak bejelentkezni vagy számítógépeket belépni a felügyelt tartományba.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Áthelyezhetek egy meglévő Azure AD Domain Services felügyelt tartományt egy másik előfizetésbe, erőforráscsoportba, régióba vagy virtuális hálózatba?
Nem. Miután létrehozott egy Azure AD Domain Services tartományt, nem tudja áthelyezni a felügyelt tartományt másik erőforráscsoportba, virtuális hálózatba, előfizetésbe stb. A felügyelt tartomány üzembe helyezésekor mindig a legmegfelelőbb előfizetést, erőforráscsoportot, régiót és virtuális hálózatot válassza ki.

### <a name="can-i-rename-an-existing-azure-ad-domain-services-domain-name"></a>Átnevezhető egy meglévő Azure AD Domain Services tartománynév?
Nem. Miután létrehozott egy Azure AD Domain Services tartományt, nem módosíthatja a DNS-tartománynevet. A felügyelt tartomány létrehozásakor körültekintően válassza ki a DNS-tartománynevet. A DNS-tartománynév kiválasztásakor megfontolandó szempontokért tekintse meg a felügyelt tartomány Azure AD Domain Services [konfigurálási oktatóanyagát.](tutorial-create-instance.md#create-a-managed-domain)

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Tartalmaz Azure AD Domain Services magas rendelkezésre állási lehetőségeket?

Igen. Minden Azure AD Domain Services tartomány két tartományvezérlőt tartalmaz. Nem Ön kezeli ezeket a tartományvezérlőket, és nem csatlakozik ezekhez a tartományvezérlőkhöz, ezek a felügyelt szolgáltatás részei. Ha olyan Azure AD Domain Services telepít, amely támogatja a Availability Zones, a tartományvezérlők zónák között vannak elosztva. Az olyan régiókban, amelyek nem támogatják a Availability Zones, a tartományvezérlők el vannak osztva a rendelkezésre állási készletek között. Ez a disztribúció nem konfigurálható és nem szabályozható. További információ: [Azure-beli virtuális gépek rendelkezésre állási lehetőségei.](../virtual-machines/availability.md)

## <a name="administration-and-operations"></a>Felügyelet és műveletek

* [Csatlakozhatok a felügyelt tartományom tartományvezérlőihez a Távoli asztal?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Engedélyeztem a Azure AD Domain Services. Milyen felhasználói fiókot használok a gépek tartományhoz való csatlakozáshoz?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Tartományi rendszergazdai jogosultságom van a felügyelt tartományhoz, amelyet a Azure AD Domain Services?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Módosítható a csoporttagság LDAP vagy más AD felügyeleti eszközökkel a felügyelt tartományokon?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Mennyi ideig tart, amíg az Azure AD-címtárban végrehajtott módosítások láthatók lesznek a felügyelt tartományban?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Kiterjesztheti a felügyelt tartomány sémáját a Azure AD Domain Services?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Módosítok vagy hozzáadhatok DNS-rekordokat a felügyelt tartományomhoz?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Mi a jelszó élettartam-szabályzata egy felügyelt tartományon?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Biztosítja Azure AD Domain Services az AD-fiók zárolásának védelmét?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Konfigurálható a elosztott fájlrendszer (DFS) és a replikáció a Azure AD Domain Services?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Hogyan alkalmazzák a Windows-frissítéseket a Azure AD Domain Services?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Csatlakozhatok a felügyelt tartományom tartományvezérlőihez a Távoli asztal?
Nem. Nem rendelkezik engedélyekkel a felügyelt tartomány tartományvezérlőihez való csatlakozáshoz a Távoli asztal. Az *AAD tartományvezérlő-rendszergazdák* csoport tagjai az AD felügyeleti eszközök, például az Active Directory Felügyeleti központ (ADAC) vagy az AD PowerShell használatával felügyelheti a felügyelt tartományt. Ezek az eszközök a felügyelt *tartományhoz Távoli kiszolgálófelügyelet eszközei* Windows-kiszolgáló Távoli kiszolgálófelügyelet eszközei szolgáltatásával telepíthetők. További információkért lásd: Felügyeleti virtuális gép létrehozása felügyelt tartományok Azure AD Domain Services [felügyeletéhez.](tutorial-create-management-vm.md)

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Engedélyeztem a Azure AD Domain Services. Milyen felhasználói fiókot használok a gépek tartományhoz való csatlakozáshoz?
Bármely felhasználói fiók, amely a felügyelt tartomány része, csatlakozhat egy virtuális géphez. Az *AAD tartományvezérlői rendszergazdák* csoport tagjai távoli asztali hozzáférést kapnak a felügyelt tartományhoz csatlakozó gépekhez.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Tartományi rendszergazdai jogosultságom van a felügyelt tartományhoz, amelyet a Azure AD Domain Services?
Nem. Nem kap rendszergazdai jogosultságokat a felügyelt tartományon. *A tartományi* *rendszergazdai* és vállalati rendszergazdai jogosultságok nem használhatók a tartományon belül. A felügyelt tartomány tartományi rendszergazdai vagy vállalati rendszergazdai csoportjainak helyi Active Directory nem kapnak tartományi/vállalati rendszergazdai jogosultságokat a felügyelt tartományon.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Módosítható a csoporttagság LDAP vagy más AD felügyeleti eszközökkel a felügyelt tartományokon?
A felhasználók és csoportok között szinkronizált Azure Active Directory és Azure AD Domain Services nem módosíthatók, mert a forrásuk nem Azure Active Directory. Ez magában foglalja a felhasználók vagy csoportok áthelyezését az AADDC Felhasználók által felügyelt szervezeti egységből egy egyéni szervezeti egységbe. A felügyelt tartományból származó bármely felhasználó vagy csoport módosítható.  

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Mennyi ideig tart, amíg az Azure AD-címtárban végrehajtott módosítások láthatók lesznek a felügyelt tartományban?
Az Azure AD-címtárban az Azure AD felhasználói felület vagy a PowerShell használatával végrehajtott módosítások automatikusan szinkronizálódnak a felügyelt tartománnyal. Ez a szinkronizálási folyamat a háttérben fut. A szinkronizálásnak nincs meghatározott időszaka az összes objektumváltozás befejezéséhez.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Kiterjesztheti a felügyelt tartomány sémáját a Azure AD Domain Services?
Nem. A sémát a Microsoft felügyeli a felügyelt tartományhoz. A sémabővítményeket nem támogatják a Azure AD Domain Services.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Módosítok vagy hozzáadhatok DNS-rekordokat a felügyelt tartományomhoz?
Igen. Az *AAD tartományvezérlő* rendszergazdái csoport tagjai *DNS-rendszergazdai* jogosultságot kapnak a felügyelt tartomány DNS-rekordjainak módosítására. Ezek a felhasználók a DNS-kezelő konzolt a felügyelt tartományhoz csatlakozott Windows Servert futtató gépen kezelhetik a DNS-t. A DNS-kezelő konzol használatához telepítse a *DNS-kiszolgáló* eszközeit, amelyek a Távoli kiszolgálófelügyelet eszközei *részét* képezi a kiszolgálón. További információ: DNS felügyelete [felügyelt Azure AD Domain Services tartományban.](manage-dns.md)

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Mi a jelszó élettartam-szabályzata egy felügyelt tartományon?
Az alapértelmezett jelszó-élettartam Azure AD Domain Services tartomány esetén 90 nap. Ez a jelszó-élettartam nincs szinkronizálva az Azure AD-ban konfigurált jelszó-élettartammal. Emiatt előfordulhat, hogy a felhasználók jelszavai lejárnak a felügyelt tartományban, de továbbra is érvényesek az Azure AD-ban. Ilyen esetekben a felhasználóknak módosítaniuk kell a jelszavukat az Azure AD-ban, és az új jelszó szinkronizálva lesz a felügyelt tartománnyal. Ha módosítani szeretné a jelszó alapértelmezett élettartamát egy felügyelt tartományban, létrehozhat és konfigurálhat egyéni [jelszó-házirendeket.](password-policy.md).

Emellett a *DisablePasswordExpiration* Azure AD jelszó-szabályzata szinkronizálva van egy felügyelt tartománnyal. Amikor *a DisablePasswordExpiration* értéket alkalmazza egy Azure AD-felhasználóra, a felügyelt tartomány szinkronizált *felhasználója UserAccountControl* értéke *DONT_EXPIRE_PASSWORD* lesz alkalmazva.

Amikor a felhasználók új jelszót kérnek az Azure AD-ban, a rendszer a *forceChangePasswordNextSignIn=True* attribútumot alkalmazza. A felügyelt tartomány szinkronizálja ezt az attribútumot az Azure AD-ból. Ha a felügyelt tartomány azt észleli, hogy a *forceChangePasswordNextSignIn* be van állítva egy Szinkronizált felhasználóhoz az Azure AD-ból, a felügyelt tartomány *pwdLastSet* attribútuma 0-ra van *állítva,* ami érvényteleníti a jelenleg beállított jelszót.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Nyújt Azure AD Domain Services AD-fiókzárolás elleni védelmet?
Igen. Öt érvénytelen jelszóra tett kísérlet 2 percen belül a felügyelt tartományon egy felhasználói fiók 30 percre való zároltságához. 30 perc múlva a rendszer automatikusan feloldja a felhasználói fiók zárolását. A felügyelt tartományon az érvénytelen jelszóra tett kísérletek nem zárják ki a felhasználói fiókot az Azure AD-ban. A felhasználói fiók csak a felügyelt Azure AD Domain Services belül van zárolva. További információ: Jelszó- és [fiókzárolási szabályzatok felügyelt tartományokon.](password-policy.md)

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Konfigurálható a elosztott fájlrendszer és a replikáció a Azure AD Domain Services?
Nem. elosztott fájlrendszer (DFS) és a replikáció nem érhető el az Azure AD Domain Services.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Hogyan alkalmazzák a Windows-frissítéseket a Azure AD Domain Services?
A felügyelt tartományok tartományvezérlői automatikusan alkalmazzák a szükséges Windows-frissítéseket. Itt semmit nem kell konfigurálnia vagy felügyelni. Győződjön meg arról, hogy nem hoz létre olyan hálózati biztonsági csoportszabályokat, amelyek blokkolják a Windows-frissítések kimenő forgalmát. A felügyelt tartományhoz csatlakozott saját virtuális gépek esetén Az Ön feladata az operációs rendszer és az alkalmazás szükséges frissítésének konfigurálása és alkalmazása.

## <a name="billing-and-availability"></a>Számlázás és rendelkezésre állás

* [Fizetős Azure AD Domain Services a szolgáltatás?](#is-azure-ad-domain-services-a-paid-service)
* [Van ingyenes próbaverzió a szolgáltatáshoz?](#is-there-a-free-trial-for-the-service)
* [Szüneteltethetek egy Azure AD Domain Services tartományt?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Feladatátvételt tudok Azure AD Domain Services egy másik régióba egy dr. esemény esetén?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Kaphatok Azure AD Domain Services Nagyvállalati mobilitási csomag (EMS) részeként? Szükségem van prémium szintű Azure AD a Azure AD Domain Services?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [Milyen Azure-régiókban érhető el a szolgáltatás?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Fizetős Azure AD Domain Services a szolgáltatás?
Igen. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/active-directory-ds/) olvasható.

### <a name="is-there-a-free-trial-for-the-service"></a>Van ingyenes próbaverzió a szolgáltatáshoz?
Azure AD Domain Services az Azure ingyenes próbaverziója tartalmazza. Regisztrálhat az Azure ingyenes [egy hónapos próbaverziójára.](https://azure.microsoft.com/pricing/free-trial/)

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Szüneteltethetek egy Azure AD Domain Services tartományt?
Nem. Miután engedélyezett egy felügyelt Azure AD Domain Services, a szolgáltatás elérhetővé válik a kiválasztott virtuális hálózaton belül, amíg nem törli a felügyelt tartományt. A szolgáltatást nem lehet szüneteltetni. A számlázás óránként folytatódik, amíg nem törli a felügyelt tartományt.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Feladatátvételt tudok Azure AD Domain Services egy másik régióba egy dr. esemény esetén?
Igen, egy felügyelt tartomány földrajzi rugalmasságának biztosítása érdekében [](tutorial-create-replica-set.md) létrehozhat egy további replikakészletet egy társviszonyban álló virtuális hálózatra bármely olyan Azure-régióban, amely támogatja a Azure AD DS. A replikakészletek ugyanazt a névteret és konfigurációt osztják meg a felügyelt tartománnyal.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Kaphatok Azure AD Domain Services Nagyvállalati mobilitási csomag (EMS) részeként? Szükségem van prémium szintű Azure AD a Azure AD Domain Services?
Nem. Azure AD Domain Services használat alapú Azure-szolgáltatás, és nem része az EMS-nek. Azure AD Domain Services az Azure AD összes kiadásával használható (ingyenes és prémium). A számlázás a használattól függően óránként történik.

### <a name="what-azure-regions-is-the-service-available-in"></a>Milyen Azure-régiókban érhető el a szolgáltatás?
Tekintse meg [az Azure-szolgáltatások régiónként oldalt](https://azure.microsoft.com/regions/#services/) azon Azure-régiók listájának a listájáért, amelyekben Azure AD Domain Services elérhető.

## <a name="troubleshooting"></a>Hibaelhárítás

A problémák [konfigurálásával](troubleshoot.md) vagy felügyeletével kapcsolatos gyakori problémák megoldásához tekintse meg a hibaelhárítási Azure AD Domain Services.

## <a name="next-steps"></a>Következő lépések

További információ a [Azure AD Domain Services: Mi a Azure Active Directory Domain Services?](overview.md).

Első lépésekért lásd: Felügyelt tartomány létrehozása [Azure Active Directory Domain Services konfigurálása.](tutorial-create-instance.md)
