---
title: Gyorsan reagálhat a biztonságos identitásokra Azure Active Directory
description: Gyorsan reagálhat a fenyegetésekre az Azure AD felhőalapú identitásai alapján
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c4301a61a79ab2351c18af0c76cccc3d07dd202
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836682"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>Az Azure AD-vel gyorsan reagálhat a biztonságos identitásokra

Úgy tűnhet, hogy a mai világban megnehezíti a dolgozók védelmét, különösen akkor, ha gyorsan reagál, és számos szolgáltatáshoz biztosít hozzáférést. Ebből a cikkből megtudhatja, hogy az összes elvégzendő műveletből rövid listát adjon meg, amellyel azonosíthatja és rangsorolhatja, hogy az Azure AD-szolgáltatások milyen sorrendben helyezhetők üzembe a saját licenc típusa alapján. Az Azure AD számos funkciót kínál, és számos biztonsági réteget biztosít az identitások számára, és a releváns funkciók megkeresése esetenként nagy mértékben elárasztható. Számos szervezet már a felhőben van, vagy gyorsan a felhőbe kerül, ennek a dokumentumnak a célja, hogy lehetővé tegye a szolgáltatások gyors üzembe helyezését az identitások elsődleges megfontolásként való biztonságossá tételével. 

Minden táblázat egy egységes biztonsági javaslatot nyújt, amely a rendszergazdai és a felhasználói identitások védelmét is biztosítja a fő biztonsági támadásoktól (a visszajátszástól, az adathalászattól és a jelszó-permetezéstől), miközben minimalizálja a felhasználói élményt és javítja a felhasználói élményt. 

Az útmutató azt is lehetővé teszi a rendszergazdák számára, hogy biztonságos és védett módon konfiguráljanak a SaaS-és a helyszíni alkalmazásokhoz való hozzáférést, és a Felhőbeli vagy hibrid (szinkronizált) identitásokra is érvényesek legyenek, és a távolról vagy az irodában működő felhasználókra is érvényesek legyenek.

Ez az ellenőrzőlista segítséget nyújt a kritikus fontosságú ajánlott műveletek gyors üzembe helyezéséhez a szervezet azonnali védelméhez, amely ismerteti a következőket:

- Erősítse meg hitelesítő adatait.
- Csökkentse a támadási felületet.
- A veszélyforrások megválaszolásának automatizálása.
- Használja a Felhőbeli intelligenciát.
- Végfelhasználói önkiszolgáló engedélyezése.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató azt feltételezi, hogy a felhőben csak a hibrid identitások lettek létrehozva az Azure AD-ben. Az azonosító típus kiválasztásával kapcsolatos segítségért tekintse meg a [megfelelő hitelesítési módszert a Azure Active Directory Hybrid Identity megoldáshoz](../hybrid/choose-ad-authn.md) . 

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-infrastruktúrának számos aspektusa van, de ez a ellenőrzőlista egy biztonságos és biztonságos identitás-infrastruktúrára koncentrál, amely lehetővé teszi a felhasználók számára a távoli munkát. Az identitás védelme csak a biztonsági történet részét képezi, az adatok, az alkalmazások és az eszközök védelmét is figyelembe kell venni.

### <a name="guidance-for-azure-ad-free-office-365-or-microsoft-365-customers"></a>Útmutató ingyenes Azure AD, Office 365 vagy Microsoft 365 ügyfelekhez.

Számos javaslattal rendelkezik, amelyekkel az ingyenes Azure AD, az Office 365 vagy Microsoft 365 alkalmazás ügyfeleinek meg kell tenniük a felhasználói identitások elleni védekezést, az alábbi táblázat a következő előfizetések fő műveleteit célozza meg:

- Office 365 (Office 365 E1, E3, E5, F1, a1, a3, a5)
- Microsoft 365 (üzleti alapszintű, vállalati alkalmazások, Business Standard, Business Premium, a1)
- Ingyenes Azure AD (az Azure, a Dynamics 365, az Intune és a Power platform részeként)

| Javasolt művelet | Részletek |
| --- | --- |
| [Biztonsági alapértékek engedélyezése](concept-fundamentals-security-defaults.md) | Az összes felhasználói identitás és alkalmazás védetté tétele az MFA engedélyezésével és a régi hitelesítés blokkolásával |
| [Jelszó-kivonat szinkronizálásának engedélyezése](../hybrid/how-to-connect-password-hash-synchronization.md) (hibrid identitások használata esetén) | Redundancia biztosítása a hitelesítéshez és a biztonság növelése (beleértve az intelligens zárolást, az IP-zárolást és a kiszivárgott hitelesítő adatok felderítésének lehetőségét) |
| [Az ADFS intelligens zárolásának engedélyezése](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (ha van ilyen) | Megvédi a felhasználókat abban, hogy rosszindulatú tevékenységekből származó extranetes fiókzárolási problémát tapasztalnak. |
| [Azure Active Directory intelligens zárolás engedélyezése](../authentication/howto-password-smart-lockout.md) (felügyelt identitások használata esetén) | Az intelligens zárolás segíti a felhasználók jelszavainak kiszűrését, illetve a találgatásos kényszerítési módszerek használatát. |
| [Végfelhasználói engedély letiltása az alkalmazásokhoz](../manage-apps/configure-user-consent.md) | A rendszergazdai hozzájárulási munkafolyamat lehetővé teszi a rendszergazdák számára, hogy hozzáférést biztosítanak a rendszergazdai jóváhagyást igénylő alkalmazásokhoz, így a végfelhasználók nem tehetik elérhetővé a vállalati adatszolgáltatásokat. A Microsoft javasolja a jövőbeli felhasználói engedélyezési műveletek letiltását a felület csökkentése és a kockázat enyhítése érdekében. |
| [Támogatott SaaS-alkalmazások integrálása a katalógusból az Azure AD-be és az egyszeri bejelentkezés engedélyezése](../manage-apps/add-application-portal.md) | Az Azure AD olyan katalógussal rendelkezik, amely több ezer előre integrált alkalmazást tartalmaz. A szervezet által használt alkalmazások némelyike valószínűleg a katalógusban érhető el, közvetlenül a Azure Portal. Hozzáférés biztosítása a vállalati SaaS-alkalmazásokhoz távolról és biztonságosan a továbbfejlesztett felhasználói felülettel (SSO) |
| [A felhasználók üzembe helyezésének és megszüntetésének automatizálása SaaS-alkalmazásokból](../app-provisioning/user-provisioning.md) (ha vannak ilyenek) | Automatikusan létrehozhat olyan felhasználói identitásokat és szerepköröket a felhőalapú (SaaS) alkalmazásokban, amelyekhez a felhasználóknak hozzá kell férniük. A felhasználói identitások létrehozása mellett az automatikus kiépítés magában foglalja a felhasználói identitások karbantartását és eltávolítását az állapot vagy a szerepkörök változása mellett, a szervezet biztonságának növelésével. |
| [Biztonságos hibrid hozzáférés engedélyezése: meglévő app Delivery controllerekkel és hálózatokkal biztosíthatja a régi alkalmazásokat](../manage-apps/secure-hybrid-access.md) (ha vannak ilyenek) | A helyszíni és a Felhőbeli örökölt hitelesítési alkalmazások közzétételére és védelemre való csatlakoztatását az Azure AD-hez a meglévő Application Delivery Controller vagy Network használatával csatlakoztathatja. |
| Önkiszolgáló [jelszó-visszaállítás engedélyezése](../authentication/tutorial-enable-sspr.md) (csak Felhőbeli fiókokra érvényes) | Ez a funkció csökkenti az ügyfélszolgálati hívásokat és a termelékenység elvesztését, ha a felhasználó nem tud bejelentkezni az eszközére vagy alkalmazására. |
| [A nem globális rendszergazdai szerepkörök használata, ahol lehetséges](../roles/permissions-reference.md) | Csak azokhoz a területekhez adjon hozzáférést a rendszergazdáknak, akiknek hozzáférésre van szükségük. Nem minden rendszergazdának kell globális rendszergazdának lennie. |
| [A Microsoft jelszavas útmutatásának engedélyezése](https://www.microsoft.com/research/publication/password-guidance/) | Állítsa le, hogy a felhasználóknak ne kelljen módosítaniuk a jelszavukat a beállított időpontokban, le kell tiltaniuk a bonyolultsági követelményeket, és a felhasználók jobban feltehetik a jelszavukat, és így biztonságban tarthatják őket. |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>Útmutató az prémium szintű Azure AD 1. csomag ügyfeleinek.

A következő táblázat célja, hogy kiemelje a következő licenc-előfizetések főbb műveleteit:

- Prémium szintű Azure Active Directory P1 (Azure AD P1)
- Enterprise Mobility + Security (EMS E3)
- Microsoft 365 (M365 E3, a3, F1, F3)

| Javasolt művelet | Részletek |
| --- | --- |
| [Az Azure AD MFA és a SSPR együttes regisztrációs felületének engedélyezése a felhasználói regisztrációs élmény egyszerűsítése érdekében](../authentication/howto-registration-mfa-sspr-combined.md) | Lehetővé teszi, hogy a felhasználók az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási szolgáltatás egyik gyakori felhasználói felületéről regisztráljon. |
| [A szervezet MFA-beállításainak konfigurálása](../authentication/howto-mfa-getstarted.md) | Gondoskodjon arról, hogy a fiókok védve legyenek a többtényezős hitelesítéssel |
| [Új jelszó önkiszolgáló kérésének engedélyezése](../authentication/tutorial-enable-sspr.md) | Ez a funkció csökkenti az ügyfélszolgálati hívásokat és a hatékonyság elvesztését, ha a felhasználó nem tud bejelentkezni az eszközére vagy egy alkalmazásba |
| [Jelszó-visszaírási implementálása](../authentication/tutorial-enable-sspr-writeback.md) (hibrid identitások használata esetén) | A Felhőbeli jelszavak módosításának engedélyezése a helyi Windows Server Active Directory környezetbe való visszaíráshoz. |
| Feltételes hozzáférési szabályzatok létrehozása és engedélyezése | [MFA a rendszergazdák számára a rendszergazdai jogosultságokat hozzárendelő fiókok védelméhez.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Örökölt hitelesítési protokollok letiltása az örökölt hitelesítési protokollokkal kapcsolatos megnövekedett kockázat miatt.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [MFA minden felhasználó és alkalmazás számára, hogy kiegyensúlyozott MFA-szabályzatot hozzon létre a környezet számára, biztosítva a felhasználók és az alkalmazások védelmét.](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [A többtényezős hitelesítés megkövetelése az Azure-erőforrásokhoz hozzáférő felhasználók többtényezős hitelesítésének megkövetelése az Azure-felügyelethez.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Jelszó-kivonat szinkronizálásának engedélyezése](../hybrid/how-to-connect-password-hash-synchronization.md) (hibrid identitások használata esetén) | Redundancia biztosítása a hitelesítéshez és a biztonság növelése (beleértve az intelligens zárolást, az IP-zárolást és a kiszivárgott hitelesítő adatok felderítésének lehetőségét) |
| [Az ADFS intelligens zárolásának engedélyezése](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (ha van ilyen) | Megvédi a felhasználókat abban, hogy rosszindulatú tevékenységekből származó extranetes fiókzárolási problémát tapasztalnak. |
| [Azure Active Directory intelligens zárolás engedélyezése](../authentication/howto-password-smart-lockout.md) (felügyelt identitások használata esetén) | Az intelligens zárolás segíti a felhasználók jelszavainak kiszűrését, illetve a találgatásos kényszerítési módszerek használatát. |
| [Végfelhasználói engedély letiltása az alkalmazásokhoz](../manage-apps/configure-user-consent.md) | A rendszergazdai hozzájárulási munkafolyamat lehetővé teszi a rendszergazdák számára, hogy hozzáférést biztosítanak a rendszergazdai jóváhagyást igénylő alkalmazásokhoz, így a végfelhasználók nem tehetik elérhetővé a vállalati adatszolgáltatásokat. A Microsoft javasolja a jövőbeli felhasználói engedélyezési műveletek letiltását a felület csökkentése és a kockázat enyhítése érdekében. |
| [Távoli hozzáférés engedélyezése a helyszíni örökölt alkalmazásokhoz alkalmazásproxy használatával](../manage-apps/application-proxy-add-on-premises-application.md) | Az Azure AD Application Proxy engedélyezése és az örökölt alkalmazások integrálása a felhasználók számára a helyszíni alkalmazások biztonságos eléréséhez az Azure AD-fiókkal való bejelentkezéssel. |
| [Biztonságos hibrid hozzáférés engedélyezése: meglévő app Delivery controllerekkel és hálózatokkal biztosíthatja a régi alkalmazásokat](../manage-apps/secure-hybrid-access.md) (ha vannak ilyenek). | A helyszíni és a Felhőbeli örökölt hitelesítési alkalmazások közzétételére és védelemre való csatlakoztatását az Azure AD-hez a meglévő Application Delivery Controller vagy Network használatával csatlakoztathatja. |
| [Támogatott SaaS-alkalmazások integrálása a katalógusból az Azure AD-be és az egyszeri bejelentkezés engedélyezése](../manage-apps/add-application-portal.md) | Az Azure AD olyan katalógussal rendelkezik, amely több ezer előre integrált alkalmazást tartalmaz. A szervezet által használt alkalmazások némelyike valószínűleg a katalógusban érhető el, közvetlenül a Azure Portal. Távolról és biztonságosan biztosíthatja a vállalati SaaS-alkalmazásokhoz való hozzáférést a továbbfejlesztett felhasználói felülettel (SSO). |
| [A felhasználók üzembe helyezésének és megszüntetésének automatizálása SaaS-alkalmazásokból](../app-provisioning/user-provisioning.md) (ha vannak ilyenek) | Automatikusan létrehozhat olyan felhasználói identitásokat és szerepköröket a felhőalapú (SaaS) alkalmazásokban, amelyekhez a felhasználóknak hozzá kell férniük. A felhasználói identitások létrehozása mellett az automatikus kiépítés magában foglalja a felhasználói identitások karbantartását és eltávolítását az állapot vagy a szerepkörök változása mellett, a szervezet biztonságának növelésével. |
| [Feltételes hozzáférés engedélyezése – eszköz alapú](../conditional-access/require-managed-devices.md) | A biztonság és a felhasználói élmény fokozása az eszközökön alapuló feltételes hozzáféréssel. Ez a lépés biztosítja, hogy a felhasználók csak a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről férhessenek hozzá. Ezeket az eszközöket felügyelt eszközöknek is nevezzük. A felügyelt eszközök lehetnek Intune-kompatibilis vagy hibrid Azure AD-hez csatlakoztatott eszközök. |
| [Jelszavas védelem engedélyezése](../authentication/howto-password-ban-bad-on-premises-deploy.md) | A felhasználók számára a gyenge és könnyen megfejthető jelszavak használata. |
| [Egynél több globális rendszergazda kijelölése](../roles/security-emergency-access.md) | Ha vészhelyzet van, rendeljen legalább két kizárólag felhőalapú, állandó globális rendszergazdai fiókot. Ezeket a fiókokat nem kell naponta használni, és hosszú és összetett jelszavakkal kell rendelkezniük. A break Glass-fiókok biztosítják, hogy vészhelyzetben is hozzáférjen a szolgáltatáshoz. |
| [A nem globális rendszergazdai szerepkörök használata, ahol lehetséges](../roles/permissions-reference.md) | Csak azokhoz a területekhez adjon hozzáférést a rendszergazdáknak, akiknek hozzáférésre van szükségük. Nem minden rendszergazdának kell globális rendszergazdának lennie. |
| [A Microsoft jelszavas útmutatásának engedélyezése](https://www.microsoft.com/research/publication/password-guidance/) | Állítsa le, hogy a felhasználóknak ne kelljen módosítaniuk a jelszavukat a beállított időpontokban, le kell tiltaniuk a bonyolultsági követelményeket, és a felhasználók jobban feltehetik a jelszavukat, és így biztonságban tarthatják őket. |
| [Csomag létrehozása a vendég felhasználói hozzáféréshez](../external-identities/what-is-b2b.md) | Együttműködés a vendég felhasználókkal az alkalmazások és szolgáltatások saját munkahelyi, iskolai vagy közösségi identitással való bejelentkezni. |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>Útmutató a prémium szintű Azure AD 2. csomag ügyfeleinek.

A következő táblázat célja, hogy kiemelje a következő licenc-előfizetések főbb műveleteit:

- Prémium szintű Azure Active Directory P2 (Azure AD P2)
- Enterprise Mobility + Security (EMS E5)
- Microsoft 365 (M365 E5, a5)

| Javasolt művelet | Részletek |
| --- | --- |
| [Az Azure AD MFA és a SSPR együttes regisztrációs felületének engedélyezése a felhasználói regisztrációs élmény egyszerűsítése érdekében](../authentication/howto-registration-mfa-sspr-combined.md) | Lehetővé teszi, hogy a felhasználók az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási szolgáltatás egyik gyakori felhasználói felületéről regisztráljon. |
| [A szervezet MFA-beállításainak konfigurálása](../authentication/howto-mfa-getstarted.md) | Gondoskodjon arról, hogy a fiókok védve legyenek a többtényezős hitelesítéssel |
| [Új jelszó önkiszolgáló kérésének engedélyezése](../authentication/tutorial-enable-sspr.md) | Ez a funkció csökkenti az ügyfélszolgálati hívásokat és a hatékonyság elvesztését, ha a felhasználó nem tud bejelentkezni az eszközére vagy egy alkalmazásba |
| [Jelszó-visszaírási implementálása](../authentication/tutorial-enable-sspr-writeback.md) (hibrid identitások használata esetén) | A Felhőbeli jelszavak módosításának engedélyezése a helyi Windows Server Active Directory környezetbe való visszaíráshoz. |
| [Identitás-védelmi házirendek engedélyezése az MFA-regisztráció érvényesítéséhez](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | Felügyelheti az Azure AD Multi-Factor Authentication (MFA) üzembe helyezését. |
| [Identity Protection-felhasználói és bejelentkezési kockázati házirendek engedélyezése](../identity-protection/howto-identity-protection-configure-risk-policies.md) | Engedélyezze az Identity Protection felhasználói és bejelentkezési szabályzatait. Az ajánlott bejelentkezési szabályzat a közepes kockázatú bejelentkezések megcélzása és az MFA megkövetelése. A felhasználói házirendek esetében a jelszó-módosítási műveletet igénylő magas kockázatú felhasználóknak kell megcélozni. |
| Feltételes hozzáférési szabályzatok létrehozása és engedélyezése | [MFA a rendszergazdák számára a rendszergazdai jogosultságokat hozzárendelő fiókok védelméhez.](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [Örökölt hitelesítési protokollok letiltása az örökölt hitelesítési protokollokkal kapcsolatos megnövekedett kockázat miatt.](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [A többtényezős hitelesítés megkövetelése az Azure-erőforrásokhoz hozzáférő felhasználók többtényezős hitelesítésének megkövetelése az Azure-felügyelethez.](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [Jelszó-kivonat szinkronizálásának engedélyezése](../hybrid/how-to-connect-password-hash-synchronization.md) (hibrid identitások használata esetén) | Redundancia biztosítása a hitelesítéshez és a biztonság növelése (beleértve az intelligens zárolást, az IP-zárolást és a kiszivárgott hitelesítő adatok felderítésének lehetőségét) |
| [Az ADFS intelligens zárolásának engedélyezése](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) (ha van ilyen) | Megvédi a felhasználókat abban, hogy rosszindulatú tevékenységekből származó extranetes fiókzárolási problémát tapasztalnak. |
| [Azure Active Directory intelligens zárolás engedélyezése](../authentication/howto-password-smart-lockout.md) (felügyelt identitások használata esetén) | Az intelligens zárolás segíti a felhasználók jelszavainak kiszűrését, illetve a találgatásos kényszerítési módszerek használatát. |
| [Végfelhasználói engedély letiltása az alkalmazásokhoz](../manage-apps/configure-user-consent.md) | A rendszergazdai hozzájárulási munkafolyamat lehetővé teszi a rendszergazdák számára, hogy hozzáférést biztosítanak a rendszergazdai jóváhagyást igénylő alkalmazásokhoz, így a végfelhasználók nem tehetik elérhetővé a vállalati adatszolgáltatásokat. A Microsoft javasolja a jövőbeli felhasználói engedélyezési műveletek letiltását a felület csökkentése és a kockázat enyhítése érdekében. |
| [Távoli hozzáférés engedélyezése a helyszíni örökölt alkalmazásokhoz alkalmazásproxy használatával](../manage-apps/application-proxy-add-on-premises-application.md) | Az Azure AD Application Proxy engedélyezése és az örökölt alkalmazások integrálása a felhasználók számára a helyszíni alkalmazások biztonságos eléréséhez az Azure AD-fiókkal való bejelentkezéssel. |
| [Biztonságos hibrid hozzáférés engedélyezése: meglévő app Delivery controllerekkel és hálózatokkal biztosíthatja a régi alkalmazásokat](../manage-apps/secure-hybrid-access.md) (ha vannak ilyenek). | A helyszíni és a Felhőbeli örökölt hitelesítési alkalmazások közzétételére és védelemre való csatlakoztatását az Azure AD-hez a meglévő Application Delivery Controller vagy Network használatával csatlakoztathatja. |
| [Támogatott SaaS-alkalmazások integrálása a katalógusból az Azure AD-be és az egyszeri bejelentkezés engedélyezése](../manage-apps/add-application-portal.md) | Az Azure AD olyan katalógussal rendelkezik, amely több ezer előre integrált alkalmazást tartalmaz. A szervezet által használt alkalmazások némelyike valószínűleg a katalógusban érhető el, közvetlenül a Azure Portal. Távolról és biztonságosan biztosíthatja a vállalati SaaS-alkalmazásokhoz való hozzáférést a továbbfejlesztett felhasználói felülettel (SSO). |
| [A felhasználók üzembe helyezésének és megszüntetésének automatizálása SaaS-alkalmazásokból](../app-provisioning/user-provisioning.md) (ha vannak ilyenek) | Automatikusan létrehozhat olyan felhasználói identitásokat és szerepköröket a felhőalapú (SaaS) alkalmazásokban, amelyekhez a felhasználóknak hozzá kell férniük. A felhasználói identitások létrehozása mellett az automatikus kiépítés magában foglalja a felhasználói identitások karbantartását és eltávolítását az állapot vagy a szerepkörök változása mellett, a szervezet biztonságának növelésével. |
| [Feltételes hozzáférés engedélyezése – eszköz alapú](../conditional-access/require-managed-devices.md) | A biztonság és a felhasználói élmény fokozása az eszközökön alapuló feltételes hozzáféréssel. Ez a lépés biztosítja, hogy a felhasználók csak a biztonsági és megfelelőségi szabványoknak megfelelő eszközökről férhessenek hozzá. Ezeket az eszközöket felügyelt eszközöknek is nevezzük. A felügyelt eszközök lehetnek Intune-kompatibilis vagy hibrid Azure AD-hez csatlakoztatott eszközök. |
| [Jelszavas védelem engedélyezése](../authentication/howto-password-ban-bad-on-premises-deploy.md) | A felhasználók számára a gyenge és könnyen megfejthető jelszavak használata. |
| [Egynél több globális rendszergazda kijelölése](../roles/security-emergency-access.md) | Ha vészhelyzet van, rendeljen legalább két kizárólag felhőalapú, állandó globális rendszergazdai fiókot. Ezeket a fiókokat nem kell naponta használni, és hosszú és összetett jelszavakkal kell rendelkezniük. A break Glass-fiókok biztosítják, hogy vészhelyzetben is hozzáférjen a szolgáltatáshoz. |
| [A nem globális rendszergazdai szerepkörök használata, ahol lehetséges](../roles/permissions-reference.md) | Csak azokhoz a területekhez adjon hozzáférést a rendszergazdáknak, akiknek hozzáférésre van szükségük. Nem minden rendszergazdának kell globális rendszergazdának lennie. |
| [A Microsoft jelszavas útmutatásának engedélyezése](https://www.microsoft.com/research/publication/password-guidance/) | Állítsa le, hogy a felhasználóknak ne kelljen módosítaniuk a jelszavukat a beállított időpontokban, le kell tiltaniuk a bonyolultsági követelményeket, és a felhasználók jobban feltehetik a jelszavukat, és így biztonságban tarthatják őket. |
| [Csomag létrehozása a vendég felhasználói hozzáféréshez](../external-identities/what-is-b2b.md) | Együttműködés a vendég felhasználókkal az alkalmazások és szolgáltatások saját munkahelyi, iskolai vagy közösségi identitással való bejelentkezni. |
| [Privileged Identity Management engedélyezése](../privileged-identity-management/pim-configure.md) | Lehetővé teszi a szervezet fontos erőforrásaihoz való hozzáférés felügyeletét, vezérlését és figyelését, valamint a rendszergazdák hozzáférését csak szükség esetén és jóváhagyással |

## <a name="next-steps"></a>Következő lépések

- Az Azure AD egyes funkcióinak részletes üzembe helyezési útmutatóját az [Azure ad Project üzembehelyezési csomagjaiban](active-directory-deployment-plans.md)tekintheti meg.

- A végpontok közötti Azure AD üzembe helyezési ellenőrzőlista a következő cikkben található: [Azure Active Directory szolgáltatások telepítési útmutatója](active-directory-deployment-checklist-p2.md)