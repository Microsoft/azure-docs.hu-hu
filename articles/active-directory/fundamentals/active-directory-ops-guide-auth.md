---
title: Azure Active Directory hitelesítési felügyeleti műveletek referencia-útmutatója
description: Ez a műveleti referencia-útmutató ismerteti a hitelesítéskezelés biztonságossá tere érdekében szükséges ellenőrzéseket és műveleteket
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 26b5331aa9242978f0f097c8e90bc807fc65f745
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531941"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>Azure Active Directory hitelesítési felügyeleti műveletek referencia-útmutatója

Az Azure [AD](active-directory-ops-guide-intro.md) üzemeltetési referencia-útmutatónak ez a szakasza ismerteti a hitelesítő adatok biztonságossá és kezelésére, a hitelesítési élmény meghatározására, a delegálás kiosztására, a használat mérésére és a vállalati biztonsági környezeten alapuló hozzáférési szabályzatok meghatározására szolgáló ellenőrzéseket és műveleteket.

> [!NOTE]
> Ezek a javaslatok a közzététel dátumával aktuálisak, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell az identitással kapcsolatos eljárásokat, ahogy a Microsoft termékei és szolgáltatásai folyamatosan fejlődnek.

## <a name="key-operational-processes"></a>Fő működési folyamatok

### <a name="assign-owners-to-key-tasks"></a>Tulajdonosok hozzárendelése a fő feladatokhoz

A Azure Active Directory kezeléséhez szükség van a kulcsfontosságú üzemeltetési feladatok és folyamatok folyamatos végrehajtására, amelyek nem részei egy bevezetési projektnek. Továbbra is fontos, hogy ezeket a feladatokat a környezet optimalizálása érdekében állítsa be. A legfontosabb feladatok és a javasolt tulajdonosok a következők:

| Feladat | Tulajdonos |
| :- | :- |
| Az egyszeri bejelentkezés (SSO) konfiguráció életciklusának kezelése az Azure AD-ban | IAM üzemeltetési csapat |
| Feltételes hozzáférési szabályzatok tervezése Azure AD-alkalmazásokhoz | InfoSec-architektúra csapat |
| Bejelentkezési tevékenység archiválása SIEM-rendszerben | InfoSec üzemeltetési csapat |
| Kockázati események archiválása SIEM-rendszerben | InfoSec üzemeltetési csapat |
| Biztonsági jelentések osztályozása és kivizsgálása | InfoSec üzemeltetési csapat |
| Kockázati események osztályozása és kivizsgálása | InfoSec üzemeltetési csapat |
| A kockázatosként megjelölt felhasználók és biztonsági rések jelentésének osztályozása és kivizsgálása a Azure AD Identity Protection | InfoSec üzemeltetési csapat |

> [!NOTE]
> Azure AD Identity Protection P2 prémium szintű Azure AD van szüksége. A követelményekhez megfelelő licenc megkeresésért lásd: A ingyenes Azure AD és prémium szintű Azure AD általánosan elérhető [szolgáltatásainak összehasonlítása.](https://azure.microsoft.com/pricing/details/active-directory/)

A lista áttekintése során előfordulhat, hogy tulajdonost kell rendelnie az olyan feladatokhoz, amelyekhez hiányzik egy tulajdonos, vagy módosítania kell a tulajdonosi tulajdonjogot a fenti javaslatokhoz nem igazodó tulajdonosokkal.

#### <a name="owner-recommended-reading"></a>Tulajdonos által javasolt olvasás

- [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](../roles/permissions-reference.md)
- [Irányítás az Azure-ban](../../governance/index.yml)

## <a name="credentials-management"></a>Hitelesítő adatok kezelése

### <a name="password-policies"></a>Jelszószabályzatok

A jelszavak biztonságos kezelése az identitás- és hozzáférés-kezelés egyik legfontosabb része, és gyakran a támadások legnagyobb célja. Az Azure AD számos olyan funkciót támogat, amely segíthet a támadások sikeres megelőzésében.

Az alábbi táblázatban találja meg a megoldani szükséges probléma megoldásához javasolt megoldást:

| Probléma | Ajánlás |
| :- | :- |
| Nincs mechanizmus a gyenge jelszavakkal szembeni védelemre | Az Azure AD [önkiszolgáló jelszó-visszaállítás (SSPR) és](../authentication/concept-sspr-howitworks.md) [jelszóvédelem engedélyezése](../authentication/concept-password-ban-bad-on-premises.md) |
| Nincs mechanizmus a kiszivárgott jelszavak észlelésére | Jelszó [kivonatszinkronizálásának](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) engedélyezése elemzésekhez |
| A AD FS használata, és nem lehet átlépni a felügyelt hitelesítésre | [Extranetes AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) és/vagy [Azure AD](../authentication/howto-password-smart-lockout.md) intelligens zárolás engedélyezése |
| A jelszószabályság összetettség-alapú szabályokat használ, például hosszt, több karakterkészletet vagy lejáratot | Gondolja át a [Microsoft ajánlott](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) eljárásait, és váltson a jelszókezelésre, és telepítse az [Azure AD jelszóvédelmet.](../authentication/concept-password-ban-bad.md) |
| A felhasználók nincsenek regisztrálva a többtényezős hitelesítés (MFA) használatára | [Regisztrálja az összes felhasználó biztonsági adatait,](../identity-protection/howto-identity-protection-configure-mfa-policy.md) hogy használhatók legyen a felhasználó identitásának és jelszavának ellenőrzésére. |
| A jelszavakat nem lehet felhasználói kockázat alapján visszavonni | Az Azure AD [Identity Protection felhasználói kockázati szabályzatok](../identity-protection/howto-identity-protection-configure-risk-policies.md) üzembe helyezése jelszóváltozások kényszerítése kiszivárgott hitelesítő adatokon az SSPR használatával |
| Nincs intelligens zárolási mechanizmus az azonosított IP-címekről érkező rosszindulatú hitelesítések elleni védelemhez | Felhőben felügyelt hitelesítés üzembe helyezése jelszó-kivonatszinkronizálással vagy átmenő [hitelesítéssel](../hybrid/how-to-connect-pta-quick-start.md) (PTA) |

#### <a name="password-policies-recommended-reading"></a>Javasolt jelszó-szabályzatok olvasása

- [Azure AD és AD FS eljárások: A szórásos jelszópúra-támadások elleni védelem – Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Új jelszó önkiszolgáló beállításának és védelmének engedélyezése

A felhasználóknak módosítaniuk vagy alaphelyzetbe kell állítaniuk a jelszavukat, ez az ügyfélszolgálati hívások mennyiségének és költségeinek egyik legnagyobb forrása. A költségek mellett a jelszónak a felhasználói kockázatok csökkentése érdekében eszközként való módosítása alapvető lépés a szervezet biztonságának javításában.

A következő feladatok elvégzéséhez legalább az Azure [AD](../authentication/concept-sspr-howitworks.md) önkiszolgáló jelszóát állítását [](../authentication/howto-password-ban-bad-on-premises-deploy.md) (SSPR) és a helyszíni jelszóvédelmet ajánlott üzembe helyezni:

- Ügyfélszolgálati hívások eltérítése.
- Cserélje le az ideiglenes jelszavakat.
- Cserélje le a meglévő önkiszolgáló jelszókezelési megoldásokat, amelyek helyszíni megoldásra támaszkodnak.
- [A gyenge jelszavak kiküszöbölése](../authentication/concept-password-ban-bad.md) a szervezetben.

> [!NOTE]
> A P2 prémium szintű Azure AD szervezetek számára javasoljuk, hogy telepítse az SSPR-t, és használja az Identity Protection felhasználói kockázati [szabályzatának részeként.](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="strong-credential-management"></a>A hitelesítő adatok erős kezelése

A jelszavak önmagukban nem elég biztonságosak ahhoz, hogy megakadályozzák a rossz szereplők hozzáférését a környezethez. Legalább az emelt szintű fiókkal rendelkező minden felhasználót engedélyezni kell a többtényezős hitelesítéshez (MFA). Ideális esetben engedélyeznie kell a kombinált [regisztrációt,](../authentication/concept-registration-mfa-sspr-combined.md) és minden felhasználónak regisztrálnia kell az MFA-re és az SSPR-re a [kombinált regisztrációs folyamat használatával.](../user-help/security-info-setup-signin.md) Végül azt javasoljuk, hogy [](../authentication/concept-resilient-controls.md) egy olyan stratégiát adjon meg, amely rugalmasságot biztosít az előre nem látható körülmények miatti zárolás kockázatának csökkentése érdekében.

![Kombinált felhasználóiélmény-folyamat](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Helyszíni kimaradás esetén a hitelesítés rugalmassága

Az egyszerűség és a kiszivárgott hitelesítő adatok észlelésének engedélyezése mellett az Azure AD jelszó-kivonatszinkronizálás (PHS) és az Azure AD MFA lehetővé teszi a felhasználóknak, hogy hozzáférjenek az SaaS-alkalmazásokhoz és az Microsoft 365-hoz a kibertámadások, például a [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)miatt kibertámadások esetén is. A PHS az összevonással együtt is engedélyezhető. A PHS engedélyezése lehetővé teszi a hitelesítés tartalékát, ha az összevonási szolgáltatások nem érhetők el.

Ha a helyszíni szervezet nem rendelkezik szolgáltatáskimaradási rugalmassági stratégiával, vagy nincs integrálva az Azure AD-be, üzembe kell helyeznie az Azure AD PHS-t, és meg kell határoznia egy vészhelyreállítási tervet, amely tartalmazza a PHS-t. Az Azure AD PHS engedélyezése lehetővé teszi a felhasználók számára az Azure AD-hitelesítést, ha a helyi Active Directory elérhetetlenné válik.

![jelszó kivonatszinkronizálási folyamat](./media/active-directory-ops-guide/active-directory-ops-img5.png)

A hitelesítési lehetőségek jobb érthetőbbek: A hibrid identitásmegoldáshoz megfelelő [hitelesítési módszer Azure Active Directory kiválasztása.](../hybrid/choose-ad-authn.md)

### <a name="programmatic-usage-of-credentials"></a>Hitelesítő adatok programozott használata

A PowerShellt használó Azure AD-szkriptek vagy a Microsoft Graph API-t használó alkalmazások biztonságos hitelesítést igényelnek. A szkriptek és eszközök gyenge hitelesítőadat-kezelése növeli a hitelesítő adatok ellopási kockázatát. Ha nem kódolt jelszavakra vagy jelszóra vonatkozó kéréseket használó szkripteket vagy alkalmazásokat használ, először tekintse át a jelszavakat a konfigurációs fájlokban vagy a forráskódban, majd cserélje le ezeket a függőségeket, és használja az Azure Managed Identities, Integrated-Windows Authentication vagy tanúsítványok használatát, [amikor](../reports-monitoring/tutorial-access-api-with-certificates.md) csak lehetséges. Olyan alkalmazások esetén, ahol a korábbi megoldások nem lehetségesek, fontolja meg a [Azure Key Vault.](https://azure.microsoft.com/services/key-vault/)

Ha úgy dönt, hogy vannak jelszóval hitelesítő adatokkal megadott szolgáltatásnév, és nem tudja biztosan, hogyan vannak biztosítva ezek a jelszó-hitelesítő adatok parancsfájlok vagy alkalmazások által, lépjen kapcsolatba az alkalmazás tulajdonosával a használati minták jobb érthetőség érdekében.

A Microsoft azt is javasolja, hogy lépjen kapcsolatba az alkalmazás tulajdonosaival, hogy megértse a használati mintákat, ha vannak jelszóval hitelesítő adatokkal megadott szolgáltatásnévk.

## <a name="authentication-experience"></a>Hitelesítési élmény

### <a name="on-premises-authentication"></a>Helyszíni hitelesítés

Az összevont hitelesítés integrált Windows-hitelesítés (IWA Sign-On) vagy közvetlen egyszeri bejelentkezéses (SSO) által felügyelt hitelesítés jelszó-kivonatszinkronizálással vagy átmenő hitelesítéssel a legjobb felhasználói élmény, ha a vállalati hálózaton belül, a helyszíni tartományvezérlőkre való rálátással lát. Minimálisra csökkenti a hitelesítő adatokra vonatkozó kérések elszennyedését, és csökkenti annak kockázatát, hogy a felhasználók adathalász támadásoknak esnek át. Ha már használ felhő által felügyelt hitelesítést a PHS-sel vagy PTA-val, de a felhasználóknak továbbra is be kell gépelniük a jelszavukat a helyszíni hitelesítés során, azonnal üzembe kell helyeznie a közvetlen [egyszeri bejelentkezést.](../hybrid/how-to-connect-sso.md) Ha azonban jelenleg olyan tervekkel van összevágva, amelyek a felhőben felügyelt hitelesítésre való végső migrálást tervezik, akkor a migrálási projekt részeként zökkenőmentes egyszeri bejelentkezést kell implementálnia.

### <a name="device-trust-access-policies"></a>Eszköz megbízhatósági hozzáférési szabályzata

A szervezet felhasználóihoz hasonló az eszköz is egy védeni kívánt alapvető identitás. Az eszközök identitásával bármikor és bármilyen helyről megvédheti erőforrásait. Az eszköz hitelesítése és a megbízhatósági típus nyilvántartása a következővel javítja a biztonsági helyzet és a használhatóság biztonságát:

- Elkerülheti például az MFA-val való súrlódást, ha az eszköz megbízható
- Nem megbízható eszközökről való hozzáférés letiltása
- A Windows 10 eszközökhöz zökkenőmentesen biztosítson egyszeri bejelentkezést a helyszíni [erőforrásokhoz.](../devices/azuread-join-sso.md)

Ezt a célt az alábbi módszerek egyikével használhatja az eszköz identitásának felhasználásával és az Azure AD-ban való kezelésével:

- A szervezetek [](/intune/what-is-intune) a Microsoft Intune kezelhetik az eszközt, betartatják a megfelelőségi szabályzatokat, igazolják az eszköz állapotát, és feltételes hozzáférési szabályzatokat állíthatnak be az eszköz megfelelősége alapján. Microsoft Intune iOS-eszközöket, Mac asztali számítógépeket (JAMF-integráción keresztül), Windows-asztalokat (natív módon a Mobile Eszközkezelés-t az Windows 10-hez, valamint az Microsoft Endpoint Configuration Manager-val való közös kezeléshez) és Android-mobileszközöket is képes kezelni.
- [A hibrid Azure AD-csatlakozás](../devices/hybrid-azuread-join-managed-domains.md) csoportházirendekkel vagy Microsoft Endpoint Configuration Manager a tartományhoz Active Directory számítógépekkel. A szervezetek üzembe helyezhetnek egy felügyelt környezetet PHS-n vagy PTA-n keresztül, zökkenőmentes egyszeri bejelentkezéssel. Ha eszközeit az Azure AD-be hozza, azzal maximalizálhatja a felhasználók hatékonyságát az SSO-n keresztül a [](../conditional-access/overview.md) felhőben és a helyszíni erőforrásokban, ugyanakkor lehetővé teszi a felhőbeli és helyszíni erőforrásokhoz való hozzáférés biztonságossá tételét feltételes hozzáféréssel.

Ha olyan tartományhoz csatlakozott Windows-eszközökkel rendelkezik, amelyek nincsenek regisztrálva a felhőben, vagy tartományhoz csatlakozott Windows-eszközökkel, amelyek regisztrálva vannak a felhőben, de nincsenek feltételes hozzáférési szabályzatok, akkor regisztrálnia kell a nem regisztrált eszközöket, és mindkét esetben hibrid [Azure AD-csatlakozást](../conditional-access/require-managed-devices.md) kell használnia a feltételes hozzáférési szabályzatok vezérlőjeként.

![Képernyőkép a hibrid eszközt igénylő feltételes hozzáférési szabályzatban található engedélyről](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Ha MDM-et vagy Microsoft Intune-t használ, de nem használ eszközvezérlőket a [](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) feltételes hozzáférési szabályzatban, javasoljuk, hogy használja az Eszköz megfelelőként való megjelölve beállítás használatát a szabályzatok vezérlőjeként.

![Képernyőkép az eszköz megfelelőségét igénylő feltételes hozzáférési szabályzatban található engedélyről](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Az eszköz megbízhatósági hozzáférési szabályzatának ajánlott olvasása

- [How To: Plan your hybrid Azure Active Directory join implementation](../devices/hybrid-azuread-join-plan.md)
- [Identitás- és eszköz-hozzáférési konfigurációk](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Vállalati Windows Hello

Ebben Windows 10 a [Vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-identity-verification) jelszavakat erős, kétfaktoros hitelesítésre cseréli a számítógépeken. Vállalati Windows Hello egyszerűbb MFA-élményt tesz lehetővé a felhasználók számára, és csökkenti a jelszavaktól való függőséget. Ha még nem kezdte el az Windows 10 üzembe helyezését, vagy csak részlegesen telepítette őket, [](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) javasoljuk, hogy frissítsen az Windows 10-verzióra, és engedélyezze Vállalati Windows Hello összes eszközön.

Ha többet szeretne megtudni a jelszó nélküli hitelesítésről, tekintse meg a jelszó [nélküli](../authentication/concept-authentication-passwordless.md)világ a Azure Active Directory.

## <a name="application-authentication-and-assignment"></a>Alkalmazáshitelesítés és -hozzárendelés

### <a name="single-sign-on-for-apps"></a>Egyszeri bejelentkezés alkalmazásokhoz

A legjobb felhasználói élmény, a kockázatcsökkentés, a jelentési és cégirányítási képességek szempontjából elengedhetetlen, hogy a teljes vállalat számára szabványosított egyszeri bejelentkezési mechanizmust biztosítsunk. Ha olyan alkalmazásokat használ, amelyek támogatják az SSO-t az Azure AD-val, de jelenleg helyi fiókok használatára vannak konfigurálva, ezeket az alkalmazásokat újra kell konfigurálnia az SSO Azure AD-val való használatára. Hasonlóképpen, ha olyan alkalmazásokat használ, amelyek támogatják az SSO-t az Azure AD-val, de egy másik identitásszolgáltatót használ, akkor ezeket az alkalmazásokat is újra kell konfigurálnia az SSO Azure AD-val való használatára. Az olyan alkalmazások esetében, amelyek nem támogatják az összevonási protokollokat, de támogatják az űrlapalapú hitelesítést, javasoljuk, hogy konfigurálja az alkalmazást úgy, hogy jelszótartót használjon [az](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) Azure AD-alkalmazásproxy.

![AppProxy jelszóalapú bejelentkezés](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Ha nem rendelkezik a nem felügyelt alkalmazások szervezeten belül való felderítésére vonatkozó mechanizmussal, javasoljuk, hogy a felderítési folyamatot egy felhőalapú hozzáférésű biztonsági közvetítőmegoldás (CASB) használatával, például a [Microsoft Cloud App Security.](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)

Végül, ha van Azure AD-alkalmazásgyűjteménye, és olyan alkalmazásokat használ, amelyek támogatják az SSO-t az Azure AD-val, javasoljuk, hogy az alkalmazást listába sorolja [az alkalmazásgyűjteményben.](../develop/v2-howto-app-gallery-listing.md)

#### <a name="single-sign-on-recommended-reading"></a>Egyszeri bejelentkezés ajánlott olvasása

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>Alkalmazások migr AD FS Azure AD-be

[Az alkalmazásoknak a AD FS Azure AD-be](../manage-apps/migrate-adfs-apps-to-azure.md) való áttelepítése további biztonsági képességeket, konzisztensebb kezelhetőséget és jobb együttműködési élményt biztosít. Ha olyan alkalmazásokat konfigurált a AD FS, amelyek támogatják az SSO-t az Azure AD-val, akkor ezeket az alkalmazásokat újra kell konfigurálnia, hogy az SSO-t használják az Azure AD-val. Ha olyan alkalmazásokat konfigurált az AD FS-ban, amelyek nem gyakori, az Azure AD által nem támogatott konfigurációkkal vannak konfigurálva, lépjen kapcsolatba az alkalmazás tulajdonosaival, és kérdezze meg, hogy a speciális konfiguráció az alkalmazás abszolút követelménye-e. Ha nem szükséges, konfigurálja újra az alkalmazást, hogy az SSO-t használja az Azure AD-val.

![Az Azure AD mint elsődleges identitásszolgáltató](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Azure AD Connect Health ADFS-hez](../hybrid/how-to-connect-health-adfs.md) használhatók az Azure AD-be migrálható alkalmazások konfigurációs részleteinek gyűjtésére.

### <a name="assign-users-to-applications"></a>Felhasználók hozzárendelése alkalmazásokhoz

[A felhasználók alkalmazásokhoz való](../manage-apps/assign-user-or-group-access-portal.md) hozzárendelése csoportok használatával a legjobb, mivel ezek nagyobb rugalmasságot és nagyobb mértékű kezelhetőségüket teszik lehetővé. A csoportok használatának előnyei közé tartozik az attribútumalapú dinamikus [csoporttagság](../enterprise-users/groups-dynamic-membership.md) és az [alkalmazástulajdonosoknak való delegálás.](../fundamentals/active-directory-accessmanagement-managing-group-owners.md) Ezért ha már használ és kezeli a csoportokat, javasoljuk, hogy a következő műveleteket használja a felügyelet nagy léptékű javítására:

- Csoportkezelés és irányítás delegálható az alkalmazástulajdonosoknak.
- Önkiszolgáló hozzáférés engedélyezése az alkalmazáshoz.
- Dinamikus csoportokat definiálhat, ha a felhasználói attribútumok konzisztensen határozzák meg az alkalmazásokhoz való hozzáférést.
- Igazolás megvalósítása az alkalmazás-hozzáféréshez használt csoportokhoz [az Azure AD hozzáférési felülvizsgálatok használatával.](../governance/access-reviews-overview.md)

Ha azonban olyan alkalmazásokat talál, amelyek egyéni felhasználókhoz vannak hozzárendelve, mindenképpen implementáljon szabályozást [ezekhez](../governance/index.yml) az alkalmazásokhoz.

#### <a name="assign-users-to-applications-recommended-reading"></a>Felhasználók hozzárendelése alkalmazásokhoz – ajánlott olvasás

- [Felhasználók és csoportok hozzárendelése egy alkalmazáshoz a Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Alkalmazásregisztrációs engedélyek delegálásának Azure Active Directory](../roles/delegate-app-roles.md)
- [Dinamikus tagsági szabályok az Azure Active Directory](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Hozzáférési szabályzatok

### <a name="named-locations"></a>Nevesített helyek

Az [](../reports-monitoring/quickstart-configure-named-locations.md) Azure AD megnevezett helyeinél megcímkézheti a szervezet megbízható IP-címtartományát. Az Azure AD a nevesített helyeket a következő célokra használja:

- A téves riasztások megelőzése kockázati eseményekben. A megbízható hálózati helyről való bejelentkezés csökkenti a felhasználó bejelentkezési kockázatát.
- Helyalapú [feltételes hozzáférés konfigurálása.](../reports-monitoring/quickstart-configure-named-locations.md)

![Névvel ellátott helyek](./media/active-directory-ops-guide/active-directory-ops-img10.png)

A prioritás alapján az alábbi táblázatban találja meg a szervezet igényeinek leginkább megfelelő ajánlott megoldást:

| **Prioritás** | **Forgatókönyv** | **Ajánlás** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Ha PHS-t vagy PTA-t használ, és a megnevezett helyek még nincsenek meghatározva | Megnevezett helyek meghatározása a kockázati események észlelésének javításához |
| 2 | Ha összevont, és nem használja az "insideCorporateNetwork" jogcímet, és a nevesített helyek még nincsenek meghatározva | Megnevezett helyek meghatározása a kockázati események észlelésének javításához |
| 3 | Ha nem használ megnevezett helyeket a feltételes hozzáférési szabályzatban, és a feltételes hozzáférési szabályzatok nem kockázatokat vagy eszközvezérlőket használhatja | A feltételes hozzáférési szabályzat konfigurálása elnevezett helyekre |
| 4 | Ha összevont, és használja az "insideCorporateNetwork" jogcímet, és a nevesített helyek még nincsenek meghatározva | Megnevezett helyek meghatározása a kockázati események észlelésének javításához |
| 5 | Ha megbízható IP-címeket használ MFA-val, nem pedig elnevezett helyekkel, és megbízhatóként megjelöli őket | Megnevezett helyek meghatározása és megbízhatóként való megjelölése a kockázati események észlelésének javítása érdekében |

### <a name="risk-based-access-policies"></a>Kockázatalapú hozzáférési szabályzatok

Az Azure AD kiszámíthatja az összes bejelentkezés és felhasználó kockázatát. Ha a kockázatokat feltételként használja a hozzáférési házirendekben, jobb felhasználói élményt nyújthat, például kevesebb hitelesítési kérést és nagyobb biztonságot nyújthat, például csak akkor kéri a felhasználókat, amikor szükség van rájuk, és automatizálhatja a választ és a szervizelést.

![Bejelentkezési kockázati szabályzat](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Ha már rendelkezik olyan prémium szintű Azure AD P2 licencekkel, amelyek támogatják a hozzáférési szabályzatok kockázatkezelését, de nincsenek használatban, javasoljuk, hogy jelentsen kockázatot a biztonsági rendszerében.

#### <a name="risk-based-access-policies-recommended-reading"></a>Kockázatalapú hozzáférési szabályzatok ajánlott olvasása

- [How To: Configure the sign-in risk policy (A bejelentkezési kockázati szabályzat konfigurálása)](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Útmutató: A felhasználói kockázati szabályzat konfigurálása](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Ügyfélalkalmazás-hozzáférési házirendek

Microsoft Intune Alkalmazáskezelés (MAM) lehetővé teszi az olyan adatvédelmi vezérlők leküldését, mint a tárhelytitkosítás, a PIN-kód, a távoli tárhely-tisztítás stb. a kompatibilis ügyfél-mobilalkalmazások, például az Outlook Mobile. Emellett feltételes hozzáférési szabályzatokat is [](../conditional-access/app-based-conditional-access.md) létre lehet hozva a felhőszolgáltatásokhoz, például az Exchange Online-hoz való hozzáférés jóváhagyott vagy kompatibilis alkalmazásokból való korlátozására.

Ha az alkalmazottak MAM-kompatibilis alkalmazásokat telepítnek ( például Az Office mobilalkalmazások) a vállalati erőforrások eléréséhez (például Exchange Online vagy SharePoint Online), és támogatja a BYOD-t is (saját eszköz használata), javasoljuk, hogy telepítse az alkalmazás MAM-szabályzatait az alkalmazáskonfiguráció kezeléséhez a személyes tulajdonban lévő, MDM-regisztráció nélküli eszközökön, majd frissítse a feltételes hozzáférési szabályzatokat, hogy csak a MAM-kompatibilis ügyfelek számára engedélyezze a hozzáférést.

![Feltételes hozzáférés engedélyének vezérlése](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Ha az alkalmazottak MAM-kompatibilis alkalmazásokat telepítnek a vállalati erőforrásokra, és a hozzáférés korlátozva van az Intune által felügyelt eszközökön, érdemes lehet alkalmazás-MAM-szabályzatokat telepíteni a személyes eszközök alkalmazáskonfigurációjának kezeléséhez, és frissíteni a feltételes hozzáférési szabályzatokat, hogy csak a MAM-kompatibilis ügyfelek hozzáférését engedélyezték.

### <a name="conditional-access-implementation"></a>Feltételes hozzáférés implementációja

A feltételes hozzáférés elengedhetetlen eszköz a szervezet biztonságának javításához. Ezért fontos, hogy kövesse az alábbi ajánlott eljárásokat:

- Győződjön meg arról, hogy minden SaaS-alkalmazásra legalább egy szabályzat érvényes
- Kerülje a **Minden alkalmazás** szűrő és a **blokkvezérlő kombinálása** a zárolási kockázat elkerülése érdekében
- Kerülje a **Minden felhasználó szűrőként** való használatát és a vendégek véletlen **hozzáadását**
- **Az összes "örökölt" szabályzat áttelepítése a Azure Portal**
- A felhasználókra, eszközökre és alkalmazásokra vonatkozó összes feltétel elfogy
- Feltételes hozzáférési szabályzatok használata [az MFA megvalósításához](../conditional-access/plan-conditional-access.md)felhasználónkénti **MFA használata helyett**
- Az alapvető szabályzatok kis készlete több alkalmazásra is alkalmazható
- Üres kivételcsoportok definiálása és hozzáadása a szabályzathoz kivételstratégia érdekében
- Az [MFA-vezérlők nélküli](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) töréses fiókok megterve
- Egységes felhasználói élmény biztosítása Microsoft 365 ügyfélalkalmazások, például a Teams, a OneDrive, az Outlook stb. esetében. a vezérlők azonos készletének megvalósításával olyan szolgáltatásokhoz, mint az Exchange Online és a Sharepoint Online
- A szabályzatok hozzárendelését csoportokon keresztül kell megvalósítani, nem egyéneken keresztül
- Rendszeresen tekintse át a szabályzatok által használt kivételcsoportokat, hogy korlátozza a felhasználók biztonsági rendszerből való kizárásának idejét. Ha Ön az Azure AD P2 a felhasználója, akkor hozzáférési felülvizsgálatok használatával automatizálhatja a folyamatot

#### <a name="conditional-access-recommended-reading"></a>Feltételes hozzáférés – ajánlott olvasás

- [Ajánlott eljárások a feltételes hozzáféréshez a Azure Active Directory](../conditional-access/overview.md)
- [Identitás- és eszköz-hozzáférési konfigurációk](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory feltételes hozzáférés beállításainak referenciája](../conditional-access/concept-conditional-access-conditions.md)
- [Gyakori feltételes hozzáférési szabályzatok](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Hozzáférési felület

### <a name="legacy-authentication"></a>Régi hitelesítés

Az erős hitelesítő adatok, például az MFA nem védheti meg az alkalmazásokat a régi hitelesítési protokollok használatával, így rosszindulatú aktorok ezt a támadási vektort részesítik előnyben. Az örökölt hitelesítés zárolása elengedhetetlen a hozzáférés biztonságának javításához.

Az örökölt hitelesítés kifejezés az alkalmazások által használt hitelesítési protokollokra utal, például:

- Régebbi, modern hitelesítést nem felhasználó Office-ügyfelek (például Office 2010-ügyfél)
- Levelezési protokollokat (például IMAP/SMTP/POP) felhasználó ügyfelek

A támadók erősen előnyben részesítik ezeket a protokollokat – valójában a szórásos jelszópóriák közel [100%-a](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) használ örökölt hitelesítési protokollokat! A támadók régi hitelesítési protokollokat használnak, mert nem támogatják az interaktív bejelentkezést, ami további biztonsági kihívásokhoz, például a többtényezős hitelesítéshez és az eszközhitelesítéshez szükséges.

Ha a környezetben széles körben használják az örökölt hitelesítést, érdemes a [](/office365/enterprise/modern-auth-for-office-2013-and-2016) lehető leghamarabb átemelni az örökölt ügyfeleket a modern hitelesítést támogató ügyfelekre. Ugyanabban a jogkivonatban, ha néhány felhasználó már modern hitelesítést használ, mások viszont továbbra is régi hitelesítést használnak, a következő lépésekkel zárolhatja az örökölt hitelesítési ügyfeleket:

1. A [bejelentkezési tevékenységre vonatkozó jelentésekkel](../reports-monitoring/concept-sign-ins.md) azonosíthatja azokat a felhasználókat, akik még mindig használják az örökölt hitelesítést és megtervezik a szervizelést:

   a. Frissítés modern hitelesítésre képes ügyfelekre az érintett felhasználók számára.
   
   b. Tervezze meg az átváltási időkeretet, hogy az alábbi lépések szerint zárolja a rendszert.
   
   c. Azonosítsa, hogy melyik örökölt alkalmazások függnek az örökölt hitelesítéstől. Lásd az alábbi 3. lépést.

2. A nagyobb kitettség elkerülése érdekében tiltsa le az örökölt protokollokat a forrásnál (például Exchange-postaládában) az örökölt hitelesítést nem használó felhasználók számára.
3. A fennmaradó fiókok (ideális esetben nem emberi identitások, például szolgáltatásfiókok) esetében feltételes hozzáféréssel korlátozza a régi protokollokat [a](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) hitelesítés után.

#### <a name="legacy-authentication-recommended-reading"></a>Régi hitelesítés javasolt olvasása

- [POP3- vagy IMAP4-hozzáférés engedélyezése vagy letiltása az Exchange Server postaládáiban](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>Hozzájárulási hozzájárulás

Egy hamis hozzájárulás megadására irányuló támadás során a támadó létrehoz egy Azure AD-ben regisztrált alkalmazást, amely hozzáférést kér az olyan adatokhoz, mint a kapcsolattartási adatok, e-mailek vagy dokumentumok. Előfordulhat, hogy a felhasználók adathalász támadásokkal járulnak hozzá a rosszindulatú alkalmazásokhoz, amikor rosszindulatú webhelyeket érnek el.

Az alábbi lista azokat az alkalmazásokat sorolja fel, amelyek olyan engedélyekkel rendelkezik, amelyek a Microsoft felhőszolgáltatásait is meg szeretnék vizsgálni:

- Alkalmazásokkal vagy delegált \* alkalmazásokkal. ReadWrite Permissions
- A delegált engedélyekkel rendelkező alkalmazások a felhasználó nevében olvashatják, küldhetik el vagy kezelhetik az e-maileket
- A következő engedélyekkel rendelkező alkalmazások:

| Erőforrás | Engedély |
| :- | :- |
| Exchange Online | Eas. AccessAsUser.All |
| | EWS. AccessAsUser.All |
| | Mail.Read |
| Microsoft Graph API | Mail.Read |
| | Mail.Read.Shared |
| | Mail.ReadWrite |

- A bejelentkezett felhasználó teljes felhasználói megszemélyesítését biztosított alkalmazások. Például:

|Erőforrás | Engedély |
| :- | :- |
| Microsoft Graph API| Directory.AccessAsUser.All |
| Azure REST API | user_impersonation |

A forgatókönyv elkerülése érdekében érdemes az [Office 365-ben](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) a hamis hozzájárulási hozzájárulás észlelésére és orvoslására vonatkozó eljárást felhozni, hogy azonosítsa és kijavítsa a hamis engedélyekkel vagy a szükségesnél több támogatással bíró alkalmazásokat. Következő [lépésként távolítsa el az önkiszolgáló szolgáltatást,](../manage-apps/configure-user-consent.md) és [hozzon létre irányítási eljárásokat.](../manage-apps/configure-admin-consent-workflow.md) Végül ütemezz az alkalmazásengedélyek rendszeres áttekintését, és távolítsa el őket, amikor nincs rájuk szükség.

#### <a name="consent-grants-recommended-reading"></a>A hozzájárulás javasolt olvasást biztosít

- [Microsoft Graph API-engedélyek](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Felhasználói és csoportbeállítások

Az alábbiakban azok a felhasználói és csoportbeállítások olvashatók, amelyek zárolhatóak, ha nincs kifejezett üzleti szükség:

#### <a name="user-settings"></a>Felhasználói beállítások

- **Külső felhasználók** – a külső együttműködés természetes módon történhet a vállalaton belül olyan szolgáltatásokkal, mint a Teams, Power BI, a Sharepoint Online és a Azure Information Protection. Ha explicit korlátozások vannak érvényben a felhasználó által kezdeményezett külső együttműködés szabályozásához, javasoljuk, hogy engedélyezze a külső felhasználókat [az Azure AD-jogosultságkezelés](../governance/entitlement-management-overview.md) vagy egy szabályozott művelet, például az ügyfélszolgálat segítségével. Ha nem szeretné engedélyezni a szolgáltatások természetes külső együttműködését, letilthatja, hogy a tagok külső felhasználókat [is meghívnak.](../external-identities/delegate-invitations.md) A külső felhasználói [meghívókban](../external-identities/allow-deny-list.md) adott tartományokat is engedélyezhet vagy blokkolhat.
- **Alkalmazásregisztrációk** – ha Alkalmazásregisztrációk, a végfelhasználók maguk is elővehetik az alkalmazásokat, és hozzáférést adhatnak az adataikhoz. Az alkalmazásregisztráció tipikus példája, hogy a felhasználók engedélyezik az Outlook beépülő modulokat, vagy olyan hangsegédeket, mint Alexa és Siri, hogy elolvassák az e-mailjeiket és naptárukat, vagy e-maileket küldjenek a nevükben. Ha az ügyfél úgy dönt, hogy kikapcsolja az alkalmazásregisztrációt, az InfoSec- és az IAM-csapatnak részt kell vennie a kivételek (az üzleti követelmények alapján szükséges alkalmazásregisztrációk) kezelésében, mivel az alkalmazásokat rendszergazdai fiókkal kell regisztrálniuk, és valószínűleg meg kell tervezniük egy folyamatot a folyamat működőképességéhez.
- **Felügyeleti portál** – a szervezetek zárolhatják az Azure AD panelt a Azure Portal, így a rendszergazdák nem férhetnek hozzá az Azure AD felügyeletéhez a Azure Portal és összezavarhatják őket. A hozzáférés korlátozását az Azure AD felügyeleti portáljának felhasználói beállításai között korlátozhatja:

![A felügyeleti portál korlátozott hozzáférése](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> A nem rendszergazda jogosultságú rendszergazdák továbbra is hozzáférhetnek az Azure AD felügyeleti felületéhez parancssori és egyéb programozott felületeken keresztül.

#### <a name="group-settings"></a>Csoportbeállítások

**Önkiszolgáló csoportkezelés / A felhasználók létrehozhatnak biztonsági csoportokat/Microsoft 365 csoportokat.** Ha jelenleg nincs önkiszolgáló kezdeményezés a felhőbeli csoportokhoz, az ügyfelek dönthetnek úgy, hogy kikapcsolják azt, amíg készen nem állnak erre a képességre.

#### <a name="groups-recommended-reading"></a>Csoportok ajánlott olvasása

- [Mi az Azure Active Directory B2B együttműködés?](../external-identities/what-is-b2b.md)
- [Alkalmazások integrálása Azure Active Directory](../develop/quickstart-register-app.md)
- [Alkalmazások, engedélyek és hozzájárulás a Azure Active Directory.](../develop/quickstart-register-app.md)
- [Csoportok használata az erőforrásokhoz való hozzáférés kezeléséhez a Azure Active Directory](./active-directory-manage-groups.md)
- [Önkiszolgáló alkalmazás-hozzáférés-kezelés beállítása a Azure Active Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Nem várt helyekről származó forgalom

A támadók a világ különböző részeiről származnak. Ezt a kockázatot feltételes hozzáférési szabályzatok használatával kezelheti, amelyek feltétele a hely. A [feltételes hozzáférési szabályzat](../conditional-access/location-condition.md) hely feltétele lehetővé teszi, hogy letiltsa az olyan helyekhez való hozzáférést, ahonnan nincs üzleti ok a bejelentkezésre.

![Új nevestű hely létrehozása](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Ha elérhető, használjon biztonsági információ- és eseménykezelési (SIEM) megoldást a régiók közötti hozzáférési minták elemzéséhez és kereséséhez. Ha nem használ SIEM-terméket, vagy nem ad be hitelesítési adatokat az Azure AD-ból, javasoljuk, hogy a [Azure Monitor](../../azure-monitor/overview.md) használatával azonosítsa a régiók közötti hozzáférési mintákat.

## <a name="access-usage"></a>Hozzáférés a használathoz

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Az Incidensválasz-tervekkel archivált és integrált Azure AD-naplók

Az Azure AD bejelentkezési tevékenységéhez, naplózási és kockázati eseményeihez való hozzáférés elengedhetetlen a hibaelhárításhoz, a használatelemzéshez és a törvényszéki vizsgálatokhoz. Az Azure AD korlátozott megőrzési időtartamú REST API-kon keresztül biztosít hozzáférést ezekhez a forrásokhoz. A biztonsági információk és események kezelési (SIEM) rendszere vagy ezzel egyenértékű archiválási technológia kulcsfontosságú a naplók és a támogatási funkciók hosszú távú tárolásához. Az Azure AD-naplók hosszú távú tárolásának engedélyezéséhez hozzá kell adni őket a meglévő SIEM-megoldáshoz, vagy a [Azure Monitor.](../reports-monitoring/concept-activity-logs-azure-monitor.md) Archiválhatja az incidensválasz-tervek és vizsgálatok részeként használható naplókat.

#### <a name="logs-recommended-reading"></a>Naplók ajánlott olvasása

- [Azure Active Directory audit API-referencia](/graph/api/resources/directoryaudit)
- [Azure Active Directory tevékenységjelentés API-referenciája](/graph/api/resources/signin)
- [Adatok lekérése az Azure AD Reporting API és tanúsítványok használatával](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Microsoft Graph a Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md)
- [Office 365 Management Activity API-referencia](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Az Azure Active Directory Power BI-tartalomcsomag használata](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-infrastruktúra 12 aspektusa van. Ez a lista segítséget nyújt a hitelesítő adatok további biztonságához és kezeléséhez, a hitelesítési élmény meghatározásához, a hozzárendelés delegálásához, a használat méréséhez és a hozzáférési szabályzatok vállalati biztonsági környezeten alapuló meghatározásához.

- Tulajdonosok hozzárendelése a fő feladatokhoz.
- Olyan megoldásokat valósíthat meg, amelyek észlelik a gyenge vagy kiszivárgott jelszavakat, javítják a jelszókezelést és -védelmet, valamint biztonságossá teszi a felhasználók erőforrásokhoz való hozzáférését.
- Az eszközök identitásának kezelésével bármikor és helyről megvédheti erőforrásait.
- Valósítsa meg a jelszó nélküli hitelesítést.
- Szabványos egyszeri bejelentkezési mechanizmust biztosít a szervezeten belül.
- Alkalmazások áttelepítése a AD FS Azure AD-be a jobb biztonság és egységesebb kezelhetőség érdekében.
- Csoportok használatával felhasználókat rendelhet az alkalmazásokhoz a nagyobb rugalmasság és a nagy méretekben való kezelés lehetősége érdekében.
- Kockázatalapú hozzáférési szabályzatok konfigurálása.
- Régi hitelesítési protokollok zárolása.
- A hamis hozzájárulási hozzájárulás észlelése és orvoslása.
- Zárolja a felhasználói és csoportbeállításokat.
- Az Azure AD-naplók hosszú távú tárolásának engedélyezése hibaelhárításhoz, használatelemzéshez és törvényszéki vizsgálatokhoz.

## <a name="next-steps"></a>Következő lépések

Első lépések az [Identity governance működési ellenőrzésekkel és műveletekkel.](active-directory-ops-guide-govern.md)
