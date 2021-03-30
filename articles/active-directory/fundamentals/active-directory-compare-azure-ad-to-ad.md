---
title: Az Active Directory és az Azure Active Directory összehasonlítása
description: Ez a dokumentum összehasonlítja Active Directory Domain Services (HOZZÁADJA) a Azure Active Directory (AD) szolgáltatáshoz. Ismerteti mindkét identitási megoldás legfontosabb fogalmait, és elmagyarázza, hogy miben különbözik vagy hasonlóak.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 64a8dabaedc3922ebd8d163b1ea162b7d1584de2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371919"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Az Active Directory és az Azure Active Directory összehasonlítása

A Azure Active Directory a Felhőbeli identitás-és hozzáférés-kezelési megoldások következő fejlődése. A Microsoft Active Directory Domain Servicest vezetett be a Windows 2000-ben, hogy a szervezetek több helyszíni infrastruktúra-összetevőt és rendszert is kezelhetnek felhasználónként egyetlen identitás használatával.

Az Azure AD ezt a megközelítést a következő szintre emeli azáltal, hogy a felhőben és a helyszínen egyaránt elérhető IDaaS-megoldást biztosít a szervezetek számára.

A legtöbb rendszergazda ismeri a Active Directory Domain Services fogalmakat. Az alábbi táblázat a Active Directory fogalmak és Azure Active Directory közötti különbségeket és hasonlóságokat ismerteti.

|Fogalom|Active Directory (AD)|Azure Active Directory |
|:-|:-|:-|
|**Felhasználók**|||
|Kiépítés: felhasználók | A szervezetek a belső felhasználókat manuálisan, vagy egy házon belüli vagy automatizált kiépítési rendszer (például a Microsoft Identity Manager) segítségével integrálják a HR rendszerbe.|A meglévő AD-szervezetek a [Azure ad Connect](../hybrid/how-to-connect-sync-whatis.md) használatával szinkronizálják az identitásokat a felhőbe.</br> Az Azure AD támogatást nyújt a [Cloud HR-rendszerekből](../saas-apps/workday-tutorial.md)származó felhasználók automatikus létrehozásához. </br>Az Azure AD identitásokat építhet ki a [scim-kompatibilis](../app-provisioning/use-scim-to-provision-users-and-groups.md) SaaS-alkalmazásokban, így automatikusan biztosíthatja az alkalmazások számára a szükséges adatokat a felhasználók hozzáférésének engedélyezéséhez. |
|Kiépítés: külső identitások| A szervezetek a külső felhasználókat manuálisan, egy dedikált külső AD-erdőben lévő normál felhasználóként hoznak létre, ami adminisztrációs terhelést eredményez a külső identitások életciklusának kezeléséhez (vendég felhasználók)| Az Azure AD speciális identitási osztályt biztosít a külső identitások támogatásához. Az [Azure ad B2B](/azure/active-directory/b2b/) a külső felhasználói identitásra mutató hivatkozást fogja kezelni, hogy azok érvényesek legyenek. |
| Jogosultságok kezelése és csoportjai| A rendszergazdák a felhasználókat a csoportok tagjai számára teszik elérhetővé. Az alkalmazás-és erőforrás-tulajdonosok ezután csoportokat biztosítanak az alkalmazásokhoz vagy erőforrásokhoz.| A [csoportok](./active-directory-groups-create-azure-portal.md) az Azure ad-ben is elérhetők, és a rendszergazdák csoportokat is használhatnak az erőforrásokra vonatkozó engedélyek megadásához. Az Azure AD-ben a rendszergazdák manuálisan is hozzárendelhetők a csoportokhoz, vagy egy lekérdezés használatával dinamikusan belefoglalhatják a felhasználókat egy csoportba. </br> A rendszergazdák a [jogosultsági felügyeletet](../governance/entitlement-management-overview.md) az Azure ad-ben használhatják a munkafolyamatok használatával, valamint szükség esetén az időalapú feltételek alapján hozzáférést biztosíthatnak a felhasználóknak az alkalmazások és erőforrások gyűjteményéhez. |
| Felügyeleti felügyelet|A szervezetek tartományokat, szervezeti egységeket és csoportokat fognak használni az AD-ben a rendszergazdai jogosultságok delegálásához a címtár és az általa felügyelt erőforrások kezeléséhez.| Az Azure AD [beépített szerepköröket](./active-directory-users-assign-role-azure-portal.md) biztosít az Azure ad szerepköralapú hozzáférés-vezérlési (Azure ad RBAC) rendszerével, amely korlátozott támogatást nyújt az [Egyéni szerepkörök létrehozásához](../roles/custom-overview.md) , hogy az identitásrendszer, az alkalmazások és az általa felügyelt erőforrások jogosultsági szintű hozzáférését delegálja.</br>A Szerepkörök kezelése [Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) segítségével biztosítható, hogy igény szerint, időben korlátozott vagy munkafolyamat-alapú hozzáférést biztosítson a Kiemelt szerepkörökhöz. |
| Hitelesítő adatok kezelése| A Active Directory hitelesítő adatai jelszavak, tanúsítványalapú hitelesítés és intelligens kártyás hitelesítésen alapulnak. A jelszavak a jelszó hosszán, lejáratán és összetettségén alapuló jelszóházirend használatával kezelhetők.|Az Azure AD intelligens [jelszavas védelmet](../authentication/concept-password-ban-bad.md) használ a felhőben és a helyszínen. A védelem magában foglalja az intelligens zárolást, valamint blokkolja a gyakori és az egyéni jelszavakkal kapcsolatos kifejezéseket és helyettesítéseket. </br>Az Azure AD jelentősen növeli [a biztonságot a többtényezős hitelesítés](../authentication/concept-mfa-howitworks.md) és a [jelszóval](../authentication/concept-authentication-passwordless.md) nem rendelkező technológiák, például a FIDO2 révén. </br>Az Azure AD csökkenti a támogatási költségeket azáltal, hogy az [önkiszolgáló jelszó-visszaállítási](../authentication/concept-sspr-howitworks.md) rendszerét biztosítja a felhasználóknak. |
| **Alkalmazások**|||
| Infrastruktúra-alkalmazások|Active Directory a helyszíni infrastruktúra számos összetevőjének, például a DNS, a DHCP, az IPSec, a WiFi, a hálózati házirend-kiszolgáló és a VPN-hozzáférés alapját képezi.|Az új Felhőbeli világban az Azure AD az alkalmazások eléréséhez és a hálózatkezelési vezérlőkre támaszkodó új vezérlő síkja. Ha a felhasználók hitelesítik[, a feltételes hozzáférés (CA)](../conditional-access/overview.md)ellenőrzi, hogy mely felhasználók férhetnek hozzá a szükséges feltételekhez tartozó alkalmazásokhoz.|
| Hagyományos és örökölt alkalmazások| A legtöbb helyszíni alkalmazás LDAP-t, Windows-Integrated hitelesítést (NTLM és Kerberos) vagy fejléc-alapú hitelesítést használ a felhasználókhoz való hozzáférés szabályozásához.| Az Azure AD a helyszínen futó [Azure ad](../manage-apps/application-proxy.md) -alkalmazásproxy-ügynökök használatával biztosít hozzáférést az ilyen típusú helyszíni alkalmazásokhoz. Ha ezt a módszert használja, az Azure AD hitelesítheti Active Directory helyszíni felhasználóit Kerberos használatával a Migrálás során, vagy az örökölt alkalmazásokkal együtt kell létezni. |
| SaaS-alkalmazások|A Active Directory natív módon nem támogatja az SaaS-alkalmazásokat, és szükség van összevonási rendszerre, például AD FSre.|Az OAuth2, SAML és WS-Authentication szolgáltatást támogató SaaS-alkalmazások \* integrálhatók az Azure ad hitelesítéshez való használatához. |
| Üzletági (LOB) alkalmazások modern hitelesítéssel|A szervezetek a AD FS és a Active Directory használatával támogatják a modern hitelesítést igénylő LOB-alkalmazásokat.| A modern hitelesítést igénylő LOB-alkalmazások konfigurálhatók az Azure AD hitelesítés használatára. |
| Mid-szintű/Daemon-szolgáltatások|A helyszíni környezetekben futó szolgáltatások általában az AD-szolgáltatásfiókok vagy a csoportosan felügyelt szolgáltatásfiókok (gMSA) használatával futnak. Ezek az alkalmazások ezután öröklik a szolgáltatásfiók engedélyeit.| Az Azure AD [felügyelt identitásokat](../managed-identities-azure-resources/index.yml) biztosít más számítási feladatok futtatásához a felhőben. Ezen identitások életciklusát az Azure AD kezeli, és az erőforrás-szolgáltatóhoz van kötve, nem használható más célra a Backdoor-hozzáférés eléréséhez.|
| **Eszközök**|||
| Mobil|A Active Directory nem támogatja natív módon a mobileszközök külső megoldások nélküli használatát.| A Microsoft mobileszköz-kezelési megoldása (Microsoft Intune) integrálva van az Azure AD-vel. A Microsoft Intune eszköz állapotadatokat biztosít az identitásrendszer számára a hitelesítés során kiértékeléshez. |
| Windows rendszerű asztali számítógépek|Active Directory lehetővé teszi a Windows-eszközök tartományhoz való csatlakoztatását Csoportházirend, System Center Configuration Manager vagy más, harmadik féltől származó megoldások használatával.|A Windows-eszközök csatlakoztathatók [Az Azure ad-hez](../devices/index.yml). A feltételes hozzáférés segítségével ellenőrizhető, hogy egy eszköz csatlakozik-e az Azure AD-hez a hitelesítési folyamat részeként. A Windows-eszközök a [Microsoft Intune](/intune/what-is-intune)használatával is kezelhetők. Ebben az esetben a feltételes hozzáférés azt mérlegeli, hogy az eszköz megfelelő-e (például naprakész biztonsági javítások és vírus-aláírások), mielőtt engedélyezi a hozzáférést az alkalmazásokhoz.|
| Windows-kiszolgálók| A Active Directory Csoportházirend vagy más felügyeleti megoldásokkal erős felügyeleti képességeket biztosít a helyszíni Windows-kiszolgálók számára.| A Windows Server rendszerű virtuális gépek az Azure-ban kezelhetők [Azure ad Domain Services](../../active-directory-domain-services/index.yml)használatával. A [felügyelt identitások](../managed-identities-azure-resources/index.yml) akkor használhatók, ha a virtuális gépeknek hozzáférésre van szükségük az Identity System könyvtárához vagy erőforrásaihoz.|
| Linux/UNIX rendszerű számítási feladatok|A Active Directory nem támogatja natív módon a nem Windows rendszerek használatát külső megoldások nélkül, bár a Linux rendszerű gépek úgy konfigurálhatók, hogy Kerberos-tartományként hitelesítsék magukat a Active Directory használatával.|A Linux/UNIX rendszerű virtuális gépek [felügyelt identitásokat](../managed-identities-azure-resources/index.yml) használhatnak az identitásrendszer vagy erőforrások eléréséhez. Néhány szervezet áttelepíti ezeket a számítási feladatokat a Felhőbeli tároló technológiákba, amelyek felügyelt identitásokat is használhatnak.|

## <a name="next-steps"></a>Következő lépések

- [Mi az az Azure Active Directory?](./active-directory-whatis.md)
- [Az önállóan felügyelt Active Directory Domain Services, Azure Active Directory és felügyelt Azure Active Directory Domain Services összehasonlítása](../../active-directory-domain-services/compare-identity-solutions.md)
- [Gyakori kérdések a Azure Active Directory](./active-directory-faq.md)
- [A Azure Active Directory újdonságai](./whats-new.md)
