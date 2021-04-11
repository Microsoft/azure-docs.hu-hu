---
title: A Azure Active Directory Authentication Management üzemeltetési útmutatója
description: Ez a műveleti útmutató ismerteti a hitelesítés biztonságossá tételéhez szükséges ellenőrzéseket és műveleteket.
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
ms.openlocfilehash: 0d425111e151d60ab38a60e38cd1805cef39f606
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641775"
---
# <a name="azure-active-directory-authentication-management-operations-reference-guide"></a>A Azure Active Directory Authentication Management üzemeltetési útmutatója

Az [Azure ad üzemeltetési útmutatójának](active-directory-ops-guide-intro.md) jelen szakasza azokat az ellenőrzéseket és műveleteket ismerteti, amelyeket a hitelesítő adatok biztonságossá tételéhez és kezeléséhez, a hitelesítési élmény meghatározásához, a hozzárendelés delegálásához, a használat méréséhez és a vállalati biztonsági helyzet alapján történő hozzáférési szabályzatok definiálásához kell végrehajtania.

> [!NOTE]
> Ezek a javaslatok a közzététel dátumától kezdve érvényesek, de idővel változhatnak. A szervezeteknek folyamatosan értékelniük kell az identitásuk gyakorlatát, mivel a Microsoft termékei és szolgáltatásai idővel fejlődnek.

## <a name="key-operational-processes"></a>Fő működési folyamatok

### <a name="assign-owners-to-key-tasks"></a>Tulajdonosok kiosztása a legfontosabb feladatokhoz

A Azure Active Directory kezelése a legfontosabb működési feladatok és folyamatok folyamatos végrehajtásához szükséges, ami esetleg nem része a bevezetési projektnek. Továbbra is fontos, hogy ezeket a feladatokat a környezet optimalizálása érdekében állítsa be. A legfontosabb feladatok és az ajánlott tulajdonosok a következők:

| Feladat | Tulajdonos |
| :- | :- |
| Az egyszeri bejelentkezési (SSO) konfiguráció életciklusának kezelése az Azure AD-ben | IAM Operations csapat |
| Feltételes hozzáférési szabályzatok tervezése Azure AD-alkalmazásokhoz | InfoSec-architektúra csapata |
| Egy SIEM rendszerbeli bejelentkezési tevékenység archiválása | InfoSec-műveleti csapat |
| Egy SIEM rendszerbeli kockázati események archiválása | InfoSec-műveleti csapat |
| Biztonsági jelentések osztályozása és vizsgálata | InfoSec-műveleti csapat |
| Kockázati események osztályozása és vizsgálata | InfoSec-műveleti csapat |
| A kockázat-és sebezhetőségi jelentésekhez megjelölt felhasználók osztályozása és vizsgálata Azure AD Identity Protection | InfoSec-műveleti csapat |

> [!NOTE]
> A Azure AD Identity Protection prémium szintű Azure AD P2-licencet igényel. A követelmények megfelelő licencének megkereséséhez tekintse meg [a ingyenes Azure ad és prémium szintű Azure ad kiadások általánosan elérhető funkcióinak összevetését](https://azure.microsoft.com/pricing/details/active-directory/)ismertető témakört.

A lista áttekintése során előfordulhat, hogy tulajdonost kell rendelnie olyan feladatokhoz, amelyek nem rendelkeznek tulajdonossal, vagy nem módosítanak tulajdonjogot olyan feladatokhoz, amelyek nem illeszkednek a fenti javaslatokhoz.

#### <a name="owner-recommended-reading"></a>Tulajdonos által ajánlott olvasás

- [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](../roles/permissions-reference.md)
- [Irányítás az Azure-ban](../../governance/index.yml)

## <a name="credentials-management"></a>Hitelesítő adatok kezelése

### <a name="password-policies"></a>Jelszószabályzatok

A jelszavak biztonságos kezelése az identitás-és hozzáférés-kezelés egyik legkritikusabb része, és gyakran a támadások legnagyobb célpontja. Az Azure AD több olyan funkciót is támogat, amelyek segítenek megakadályozni a támadás sikerességét.

Az alábbi táblázat segítségével megtalálhatja a megoldandó problémát enyhítő ajánlott megoldást:

| Probléma | Ajánlás |
| :- | :- |
| Nincs olyan mechanizmus, amely védelmet nyújt a gyenge jelszavak ellen | Az Azure AD önkiszolgáló [jelszó-visszaállítás (SSPR)](../authentication/concept-sspr-howitworks.md) és [jelszavas védelem](../authentication/concept-password-ban-bad-on-premises.md) engedélyezése |
| Nincs mechanizmus a kiszivárgott jelszavak észleléséhez | A [jelszó-kivonat szinkronizálásának](../hybrid/how-to-connect-password-hash-synchronization.md) (PHS) engedélyezése az elemzések megszerzéséhez |
| AD FS használata és nem lehet áthelyezni a felügyelt hitelesítésre | [AD FS extranetes intelligens zárolás](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection) és/vagy [Azure ad intelligens zárolás](../authentication/howto-password-smart-lockout.md) engedélyezése |
| A jelszóházirend olyan komplexitás-alapú szabályokat használ, mint például a hossz, a több karakterkészlet vagy a lejárat | Gondolja át a [Microsoft ajánlott eljárásait](https://www.microsoft.com/research/publication/password-guidance/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F265143%2Fmicrosoft_password_guidance.pdf) , és váltson át a jelszavas felügyeletre, és telepítse az [Azure ad jelszavas védelmet](../authentication/concept-password-ban-bad.md). |
| A felhasználók nem regisztráltak a többtényezős hitelesítés (MFA) használatára | Az [összes felhasználó biztonsági információinak regisztrálása](../identity-protection/howto-identity-protection-configure-mfa-policy.md) , hogy a rendszer használható legyen a felhasználó identitásának és jelszavának ellenőrzésére. |
| A jelszavak nem vonhatók visszavonásra a felhasználói kockázat alapján | Az Azure AD [Identity Protection felhasználói kockázati házirendjeinek](../identity-protection/howto-identity-protection-configure-risk-policies.md) üzembe helyezése a kiszivárgott hitelesítő adatok jelszavas változásainak kényszerítéséhez a SSPR használatával |
| Nincs olyan intelligens zárolási mechanizmus, amely az azonosított IP-címekről érkező hibás szereplőktől származó rosszindulatú hitelesítést véd. | Felhő által felügyelt hitelesítés üzembe helyezése jelszó-kivonatoló szinkronizálással vagy [átmenő hitelesítéssel](../hybrid/how-to-connect-pta-quick-start.md) (PTA ESP) |

#### <a name="password-policies-recommended-reading"></a>A jelszóházirend ajánlott olvasmánya

- [Azure AD és AD FS ajánlott eljárások: a jelszó-szórási támadások elleni védelem – Enterprise Mobility + Security](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

### <a name="enable-self-service-password-reset-and-password-protection"></a>Önkiszolgáló jelszó-visszaállítás és jelszavas védelem engedélyezése

A felhasználók az ügyfélszolgálati hívások egyik legnagyobb forrásaként és a hozzájuk tartozó jelszavak módosítására vagy alaphelyzetbe állítására szorulnak. A költséghatékonyság mellett a felhasználói kockázat enyhítésére szolgáló eszközként a jelszó módosításának alapvető lépése a szervezet biztonsági helyzetének javítása.

Legalább azt javasoljuk, hogy az Azure AD önkiszolgáló [jelszó-visszaállítás](../authentication/concept-sspr-howitworks.md) (SSPR) és a helyszíni [jelszavas védelem](../authentication/howto-password-ban-bad-on-premises-deploy.md) üzembe helyezését az alábbiak szerint végezze el:

- Az ügyfélszolgálati hívások alakváltozása.
- Cserélje le az ideiglenes jelszavak használatát.
- Cserélje le a meglévő önkiszolgáló jelszavas felügyeleti megoldást, amely egy helyszíni megoldásra támaszkodik.
- [Távolítsa el a szervezet gyenge jelszavait](../authentication/concept-password-ban-bad.md) .

> [!NOTE]
> A prémium szintű Azure AD P2 előfizetéssel rendelkező szervezetek esetében ajánlott a SSPR üzembe helyezése, és annak használata az [Identity Protection felhasználói kockázati szabályzatának](../identity-protection/howto-identity-protection-configure-risk-policies.md)részeként.

### <a name="strong-credential-management"></a>Erős hitelesítőadat-kezelés

A jelszó önmagában nem elég biztonságos ahhoz, hogy a rossz szereplők hozzáférjenek a környezetéhez. A többtényezős hitelesítés (MFA) számára legalább egy kiemelt fiókkal rendelkező felhasználónak engedélyezve kell lennie. Ideális esetben engedélyeznie kell a [kombinált regisztrációt](../authentication/concept-registration-mfa-sspr-combined.md) , és minden felhasználónak regisztrálnia kell az MFA-és SSPR a [kombinált regisztrációs élmény](../user-help/security-info-setup-signin.md)használatával. Végül azt javasoljuk, hogy olyan stratégiát alkalmazzon, amely [rugalmasságot biztosít](../authentication/concept-resilient-controls.md) a zárolás kockázatának csökkentése érdekében az előre nem látható körülmények miatt.

![Összetett felhasználói élmény folyamata](./media/active-directory-ops-guide/active-directory-ops-img4.png)

### <a name="on-premises-outage-authentication-resiliency"></a>Helyszíni kimaradások hitelesítésének rugalmassága

Az egyszerűség és a kiszivárgott hitelesítő adatok észlelésének engedélyezése mellett az Azure AD Password hash Sync (PHS) és az Azure AD MFA lehetővé teszi a felhasználóknak az SaaS-alkalmazások elérését, Microsoft 365 és a helyszíni leállások maihoz, például [NotPetya](https://www.microsoft.com/security/blog/2018/02/05/overview-of-petya-a-rapid-cyberattack/)miatt. Az PHS is engedélyezhető az összevonással együtt. A PHS engedélyezése lehetővé teszi a hitelesítés tartalékát, ha az összevonási szolgáltatások nem érhetők el.

Ha a helyszíni szervezet nem rendelkezik leállás utáni rugalmassági stratégiával, vagy amely nem integrált az Azure AD-vel, akkor az Azure AD-PHS kell telepítenie, és meg kell határoznia egy vész-helyreállítási tervet, amely PHS tartalmaz. Az Azure AD-PHS engedélyezése lehetővé teszi a felhasználók számára az Azure AD-beli hitelesítését, ha a helyszíni Active Directory elérhetetlenné válnak.

![jelszó-kivonat szinkronizálási folyamata](./media/active-directory-ops-guide/active-directory-ops-img5.png)

A hitelesítési lehetőségek jobb megismeréséhez tekintse [meg a megfelelő hitelesítési módszer kiválasztása a Azure Active Directory Hybrid Identity megoldáshoz](../hybrid/choose-ad-authn.md)című témakört.

### <a name="programmatic-usage-of-credentials"></a>A hitelesítő adatok programozott használata

A PowerShellt vagy az Microsoft Graph API-t használó alkalmazásokat használó Azure AD-parancsfájlok biztonságos hitelesítést igényelnek. A parancsfájlokat és eszközöket végrehajtó, helytelen hitelesítő adatok kezelése növelheti a hitelesítő adatok ellopásának kockázatát. Ha olyan parancsfájlokat vagy alkalmazásokat használ, amelyek nehezen kódolt jelszavakat vagy jelszó kérését használják, először tekintse át a jelszavakat a konfigurációs fájlokban vagy a forráskódban, majd cserélje le ezeket a függőségeket, és használja az Azure által felügyelt identitásokat, Integrated-Windows hitelesítést vagy [tanúsítványokat](../reports-monitoring/tutorial-access-api-with-certificates.md) , amikor csak lehetséges. Az olyan alkalmazások esetében, amelyeknél az előző megoldások nem lehetségesek, érdemes lehet [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)használni.

Ha azt állapítja meg, hogy vannak-e olyan egyszerű szolgáltatások, amelyek jelszavas hitelesítő adatokkal rendelkeznek, és nem biztos benne, hogy ezek a jelszavak hitelesítő adatai a parancsfájlok vagy alkalmazások által védettek, a használati minták jobb megismeréséhez lépjen kapcsolatba az alkalmazás tulajdonosával.

A Microsoft azt is javasolja, hogy vegye fel a kapcsolatot az alkalmazás tulajdonosainak, hogy megértsék a használati mintákat, ha a hitelesítő adatokkal rendelkező egyszerű szolgáltatások vannak.

## <a name="authentication-experience"></a>Hitelesítési élmény

### <a name="on-premises-authentication"></a>Helyszíni hitelesítés

Az összevont hitelesítés integrált Windows-hitelesítéssel (IWA) vagy zökkenőmentes, egyszeri Sign-On (SSO) felügyelt hitelesítéssel, jelszó-kivonatolási szinkronizálással vagy átmenő hitelesítéssel a legjobb felhasználói élmény a vállalati hálózaton belül, a helyszíni tartományvezérlőkön. Ez csökkenti a hitelesítő adatok kérésének fáradtságát, és csökkenti annak a kockázatát, hogy a felhasználók az adathalászat elleni támadásoknak áldozatául esnek Ha már használja a PHS vagy a PTA felhőben felügyelt hitelesítést, de a felhasználóknak továbbra is be kell írniuk a jelszavukat a helyszíni hitelesítés során, azonnal [telepítenie kell a zökkenőmentes egyszeri bejelentkezést](../hybrid/how-to-connect-sso.md). Ha azonban jelenleg összevonta azokat a csomagokat, amelyeket végül a felhőben felügyelt hitelesítésre kíván áttelepíteni, akkor az áttelepítési projekt részeként zökkenőmentes egyszeri bejelentkezést kell megvalósítani.

### <a name="device-trust-access-policies"></a>Eszköz-megbízhatósági hozzáférési szabályzatok

A szervezetben lévő felhasználóhoz hasonlóan az eszköz a védelemmel ellátni kívánt alapvető identitás. Az eszközök identitásával bármikor és bárhonnan biztosíthatja erőforrásait. Az eszköz hitelesítése és a megbízhatóság típusának nyilvántartása javítja a biztonsági helyzeteket és a használhatóságot:

- A súrlódás elkerülése, például MFA esetén, ha az eszköz megbízható
- Nem megbízható eszközökről való hozzáférés letiltása
- A Windows 10-es eszközök esetében [zökkenőmentesen biztosítson egyszeri bejelentkezést a helyszíni erőforrásokhoz](../devices/azuread-join-sso.md).

Ezt a célt az eszköz identitásának és az Azure AD-ben való kezelésének az alábbi módszerek egyikével végezheti el:

- A szervezetek a [Microsoft Intune](/intune/what-is-intune) használatával kezelhetik az eszközt, és kikényszerítik a megfelelőségi szabályzatokat, tanúsítják az eszköz állapotát, valamint feltételes hozzáférési házirendeket állíthatnak be attól függően, hogy az eszköz megfelel-e Microsoft Intune felügyelheti az iOS-eszközöket, a Mac-asztalokat (JAMF-integráción keresztül), a Windows-asztalokat (natív módon a Windows 10 mobileszköz-felügyeletét, valamint a Microsoft Endpoint Configuration Manager) és az androidos mobileszközök közös felügyeletét.
- A [hibrid Azure ad-csatlakozás](../devices/hybrid-azuread-join-managed-domains.md) a csoportházirendekkel vagy a Microsoft Endpoint Configuration managerekkel való felügyeletet biztosít Active Directory tartományhoz csatlakoztatott számítógépeket használó környezetekben. A szervezetek a PHS vagy a PTA, zökkenőmentes SSO használatával telepíthetnek felügyelt környezetet. Ha az eszközöket az Azure AD-be szeretné állítani, a Felhőbeli és a helyszíni erőforrások egyszeri bejelentkezésével maximalizálja a felhasználói hatékonyságot, miközben lehetővé teszi a Felhőbeli és helyszíni erőforrások biztonságos elérését a [feltételes hozzáféréssel](../conditional-access/overview.md) egyidejűleg.

Ha olyan tartományhoz csatlakoztatott Windows-eszközöket használ, amelyek nincsenek a felhőben regisztrálva, vagy a felhőben regisztrált, de feltételes hozzáférési házirendek nélküli tartományhoz csatlakoztatott Windows-eszközök, akkor regisztrálnia kell a nem regisztrált eszközöket, és mindkét esetben [használja a hibrid Azure ad JOIN vezérlőt](../conditional-access/require-managed-devices.md) a feltételes hozzáférési szabályzatokban.

![A hibrid eszközt igénylő feltételes hozzáférési szabályzatban való engedélyezés képernyőképe](./media/active-directory-ops-guide/active-directory-ops-img6.png)

Ha MDM vagy Microsoft Intune eszközzel felügyeli az eszközöket, de a feltételes hozzáférési szabályzatok nem használnak eszköz-vezérlőelemeket, akkor javasoljuk, [hogy az eszköz megfelelőként való megjelölését](../conditional-access/require-managed-devices.md#require-device-to-be-marked-as-compliant) használja a szabályzatokban.

![Az eszköz megfelelőségét igénylő feltételes hozzáférési szabályzatban való engedélyezés képernyőképe](./media/active-directory-ops-guide/active-directory-ops-img7.png)

#### <a name="device-trust-access-policies-recommended-reading"></a>Az eszköz megbízhatóságához való hozzáférési szabályzatok ajánlott olvasmányok

- [Útmutató: a hibrid Azure Active Directory JOIN implementációjának megtervezése](../devices/hybrid-azuread-join-plan.md)
- [Identitás- és eszköz-hozzáférési konfigurációk](/microsoft-365/enterprise/microsoft-365-policies-configurations)

### <a name="windows-hello-for-business"></a>Vállalati Windows Hello

A Windows 10 rendszerben a [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification) a jelszavakat erős, Kéttényezős hitelesítéssel helyettesíti a számítógépeken. A vállalati Windows Hello lehetővé teszi a hatékonyabb MFA-élményt a felhasználók számára, és csökkenti a jelszavak függőségét. Ha még nem kezdte meg a Windows 10-es eszközök kihelyezését, vagy csak részben telepítette őket, javasoljuk, hogy frissítsen a Windows 10-es verzióra, és [engedélyezze a vállalati Windows Hello](/windows/security/identity-protection/hello-for-business/hello-manage-in-organization) szolgáltatást minden eszközön.

Ha további információra van szüksége a jelszó nélküli hitelesítésről, tekintse meg [a globális jelszavak nélkül Azure Active Directory](../authentication/concept-authentication-passwordless.md).

## <a name="application-authentication-and-assignment"></a>Alkalmazás-hitelesítés és-hozzárendelés

### <a name="single-sign-on-for-apps"></a>Egyszeri bejelentkezés az alkalmazásokhoz

Egy szabványosított egyszeri bejelentkezési mechanizmus biztosítása a teljes vállalat számára létfontosságú a legjobb felhasználói élmény, a kockázat csökkentése, a jelentés és a szabályozás terén. Ha olyan alkalmazásokat használ, amelyek támogatják az egyszeri bejelentkezést az Azure AD-ben, de jelenleg helyi fiókok használatára vannak konfigurálva, ezeket az alkalmazásokat újra be kell állítania az egyszeri bejelentkezés az Azure AD-vel való használatára. Hasonlóképpen, ha olyan alkalmazást használ, amely támogatja az egyszeri bejelentkezést az Azure AD-ben, de más identitás-szolgáltatót használ, ezeket az alkalmazásokat újra be kell állítania az egyszeri bejelentkezés az Azure AD-vel való használatára. Azon alkalmazások esetében, amelyek nem támogatják az összevonási protokollok használatát, de támogatják az űrlapalapú hitelesítést, javasoljuk, hogy konfigurálja az alkalmazást az Azure AD Application Proxy-hoz tartozó [jelszavas](../manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) tár használatára.

![AppProxy jelszó-alapú bejelentkezés](./media/active-directory-ops-guide/active-directory-ops-img8.png)

> [!NOTE]
> Ha nem rendelkezik olyan mechanizmussal, amely a szervezeten belüli nem felügyelt alkalmazásokat deríti fel, javasoljuk, hogy a felderítési folyamat megvalósítását egy felhőalapú hozzáférési biztonsági közvetítő megoldással (CASB), például a [Microsoft Cloud app Security](https://www.microsoft.com/enterprise-mobility-security/cloud-app-security)használatával hajtsa végre.

Végül, ha rendelkezik Azure AD-alkalmazás-katalógussal, és olyan alkalmazásokat használ, amelyek támogatják az egyszeri bejelentkezést az Azure AD-ben, javasoljuk, hogy az [alkalmazást az App Galleryben](../develop/v2-howto-app-gallery-listing.md)tartalmazza.

#### <a name="single-sign-on-recommended-reading"></a>Egyszeri bejelentkezés ajánlott olvasás

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

### <a name="migration-of-ad-fs-applications-to-azure-ad"></a>AD FS alkalmazások áttelepítése az Azure AD-be

Az [alkalmazások ad FSról az Azure ad-be való áttelepítése](../manage-apps/migrate-adfs-apps-to-azure.md) további képességeket tesz lehetővé a biztonság, az egységes kezelhetőség és a jobb együttműködési élmény érdekében. Ha olyan AD FS konfigurált alkalmazásokkal rendelkezik, amelyek támogatják az egyszeri bejelentkezést az Azure AD-ben, akkor ezeket az alkalmazásokat újra kell konfigurálnia az egyszeri bejelentkezés az Azure AD-vel való használatához. Ha olyan alkalmazásokkal rendelkezik, amelyekben az Azure AD nem támogatja az olyan általános konfigurációkat, amelyek nem támogatják az AD FS, akkor vegye fel a kapcsolatot az alkalmazás tulajdonosával, és Ismerje meg, hogy a Speciális konfiguráció az alkalmazás abszolút követelménye-e. Ha nincs rá szükség, konfigurálja újra az alkalmazást az egyszeri bejelentkezés az Azure AD-vel való használatára.

![Az Azure AD elsődleges identitás-szolgáltatóként](./media/active-directory-ops-guide/active-directory-ops-img9.png)

> [!NOTE]
> [Az ADFS Azure ad Connect Health az](../hybrid/how-to-connect-health-adfs.md) Azure ad-be esetleg áttelepíthető alkalmazások konfigurációs adatainak gyűjtésére használható.

### <a name="assign-users-to-applications"></a>Felhasználók kiosztása alkalmazásokhoz

A [felhasználók az alkalmazásokhoz való hozzárendelését](../manage-apps/assign-user-or-group-access-portal.md) a csoportok használatával lehet legjobban leképezni, mivel ezek nagyobb rugalmasságot és nagy mennyiségű felügyeletet tesznek lehetővé. A csoportok használatának előnyei közé tartoznak az [attribútum-alapú dinamikus csoporttagság](../enterprise-users/groups-dynamic-membership.md) és az [alkalmazás-tulajdonosok delegálása](../fundamentals/active-directory-accessmanagement-managing-group-owners.md). Ezért ha már használja és kezeli a csoportokat, javasoljuk, hogy a következő műveleteket hajtsa végre a nagy léptékű felügyelet javítása érdekében:

- A csoportok kezelésének és irányításának delegálása az alkalmazás tulajdonosainak.
- Az alkalmazáshoz való önkiszolgáló hozzáférés engedélyezése.
- Dinamikus csoportok meghatározása, ha a felhasználói attribútumok konzisztens módon határozhatják meg az alkalmazásokhoz való hozzáférést.
- Az [Azure ad hozzáférési felülvizsgálatait](../governance/access-reviews-overview.md)használó alkalmazásokhoz való hozzáféréshez használt csoportok tanúsítványának megvalósítása.

Ha azonban olyan alkalmazásokat talál, amelyek egyéni felhasználókhoz rendelnek hozzárendelést, mindenképpen hajtsa végre az alkalmazások [irányítását](../governance/index.yml) .

#### <a name="assign-users-to-applications-recommended-reading"></a>Felhasználók kiosztása ajánlott olvasáshoz

- [Felhasználók és csoportok társítása egy alkalmazáshoz Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md)
- [Alkalmazás-regisztrációs engedélyek delegálása Azure Active Directory](../roles/delegate-app-roles.md)
- [A Azure Active Directory csoportok dinamikus tagsági szabályai](../enterprise-users/groups-dynamic-membership.md)

## <a name="access-policies"></a>Hozzáférési szabályzatok

### <a name="named-locations"></a>Nevesített helyek

Az Azure AD-ben [elnevezett helyekkel](../reports-monitoring/quickstart-configure-named-locations.md) megbízható IP-címtartományok is felcímkézhető a szervezetében. Az Azure AD a nevesített helyeket a következő célokra használja:

- A kockázati események hamis pozitív értékének megakadályozása. A megbízható hálózati helyről való bejelentkezés csökkenti a felhasználó bejelentkezési kockázatát.
- Konfigurálja a [hely-alapú feltételes hozzáférést](../reports-monitoring/quickstart-configure-named-locations.md).

![Névvel ellátott helyek](./media/active-directory-ops-guide/active-directory-ops-img10.png)

A prioritás alapján az alábbi táblázat segítségével megtalálhatja a szervezete igényeinek leginkább megfelelő ajánlott megoldást:

| **Prioritás** | **Forgatókönyv** | **Ajánlás** |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1 | Ha nem definiált PHS vagy PTA nevű helyet használ | Elnevezett helyszínek definiálása a kockázati események észlelésének javításához |
| 2 | Ha összevont, és nem használja a "insideCorporateNetwork" jogcímet, és a nevesített helyszínek nem lettek definiálva | Elnevezett helyszínek definiálása a kockázati események észlelésének javításához |
| 3 | Ha nem használ nevesített helyet a feltételes hozzáférési házirendekben, és a feltételes hozzáférési szabályzatok nem rendelkeznek kockázat-vagy eszköz-ellenőrzésekkel | A feltételes hozzáférési szabályzat beállítása a nevesített helyszínek belefoglalására |
| 4 | Ha összevont, és nem definiált "insideCorporateNetwork" jogcímet és elnevezett helyet használ | Elnevezett helyszínek definiálása a kockázati események észlelésének javításához |
| 5 | Ha olyan megbízható IP-címeket használ, mint az MFA, nem pedig elnevezett helyek, és nem megbízhatóként jelöli meg őket | Elnevezett helyek definiálása és megbízhatóként való megjelölése a kockázati események észlelésének javítása érdekében |

### <a name="risk-based-access-policies"></a>Kockázatalapú hozzáférési házirendek

Az Azure AD kiszámíthatja az összes bejelentkezés és minden felhasználó kockázatát. Ha a hozzáférési házirendekben a kockázati tényezőt feltételként használja, jobb felhasználói élményt biztosíthat, például kevesebb hitelesítési kérést és nagyobb biztonságot, például csak szükség esetén kéri a felhasználókat, és automatizálhatja a választ és a szervizelést.

![Bejelentkezési kockázati szabályzat](./media/active-directory-ops-guide/active-directory-ops-img11.png)

Ha már rendelkezik olyan prémium szintű Azure AD P2-licencekkel, amelyek a hozzáférési házirendekben kockázatot jelentenek, de nem használják őket, javasoljuk, hogy vegye fel a kockázatot a biztonsági helyzetbe.

#### <a name="risk-based-access-policies-recommended-reading"></a>A kockázatalapú hozzáférési szabályzatok ajánlott olvasmányok

- [Útmutató: a bejelentkezési kockázati házirend konfigurálása](../identity-protection/howto-identity-protection-configure-risk-policies.md)
- [Útmutató: a felhasználói kockázati házirend konfigurálása](../identity-protection/howto-identity-protection-configure-risk-policies.md)

### <a name="client-application-access-policies"></a>Ügyfélalkalmazás-hozzáférési házirendek

Microsoft Intune az alkalmazás-felügyelet (MAM) lehetővé teszi az olyan adatvédelmi szabályozások leküldését, mint például a tárolási titkosítás, a PIN-kód, a távoli tárterület tisztítása stb. a kompatibilis ügyfelek mobil alkalmazásai, például az Outlook Mobile használatával. Emellett a feltételes hozzáférési szabályzatok úgy is létrehozhatók, hogy korlátozzák a felhőalapú szolgáltatásokhoz, például az Exchange Online-hoz a jóváhagyott vagy kompatibilis alkalmazásokból [való hozzáférést](../conditional-access/app-based-conditional-access.md) .

Ha az alkalmazottai MAM-kompatibilis alkalmazásokat (például Office Mobile apps) telepítenek a vállalati erőforrások, például az Exchange Online vagy a SharePoint Online eléréséhez, és Ön is támogatja a BYOD (saját eszközök használata), javasoljuk, hogy az Application MAM-szabályzatokat úgy telepítse, hogy a személyes tulajdonban lévő eszközökön MDM regisztráció nélkül kezelhesse a hozzáférést

![Feltételes hozzáférés engedélyezésének vezérlése](./media/active-directory-ops-guide/active-directory-ops-img12.png)

Ha az alkalmazottaknak MAM-kompatibilis alkalmazásokat kell telepíteniük a vállalati erőforrásokra, és a hozzáférés korlátozott az Intune által felügyelt eszközökön, akkor érdemes az alkalmazás-MAM-szabályzatokat telepíteni a személyes eszközökhöz tartozó alkalmazások konfigurációjának kezelésére, és frissíteni a feltételes hozzáférési szabályzatokat, hogy csak a MAM-kompatibilis ügyfelek hozzáférjenek.

### <a name="conditional-access-implementation"></a>Feltételes hozzáférés megvalósítása

A feltételes hozzáférés elengedhetetlen eszköz a szervezet biztonsági helyzetének javításához. Ezért fontos, hogy kövesse az alábbi ajánlott eljárásokat:

- Győződjön meg arról, hogy minden SaaS-alkalmazáshoz legalább egy házirend van alkalmazva
- A zárolási kockázat elkerülése érdekében ne egyesítse a **minden alkalmazás** szűrőt a **Letiltás** vezérlőelemkel
- Kerülje a **minden felhasználó** szűrőként való használatát és a **vendégek** véletlen hozzáadását
- **Az összes "örökölt" szabályzat migrálása a Azure Portalba**
- A felhasználók, eszközök és alkalmazások összes feltételének befogása
- Feltételes hozzáférési szabályzatok használata az [MFA megvalósításához](../conditional-access/plan-conditional-access.md), nem pedig felhasználónkénti **MFA** használata
- Több alkalmazásra is alkalmazható alapszabályzatok kis készlete
- Hozzon létre üres kivételi csoportokat, és vegye fel őket a szabályzatokhoz, hogy legyen egy kivételi stratégia
- A [break Glass](../roles/security-planning.md#break-glass-what-to-do-in-an-emergency) -fiókok MFA-vezérlők nélküli megtervezése
- Egységes felhasználói élményt biztosíthat Microsoft 365 ügyfélalkalmazások, például csapatok, OneDrive, Outlook stb.) számára. ugyanazon vezérlők megvalósításával olyan szolgáltatásokhoz, mint az Exchange Online és a SharePoint Online
- A szabályzatokhoz való hozzárendelést olyan csoportokon keresztül kell megvalósítani, amelyek nem egyéniek
- A szabályzatokban használt kivételi csoportok rendszeres felülvizsgálatával korlátozható, hogy a felhasználók ne legyenek a biztonsági testhelyzetben. Ha saját Azure AD P2-t használ, a hozzáférési felülvizsgálatok segítségével automatizálhatja a folyamatot

#### <a name="conditional-access-recommended-reading"></a>Feltételes hozzáférés ajánlott olvasmánya

- [Ajánlott eljárások a feltételes hozzáféréshez Azure Active Directory](../conditional-access/overview.md)
- [Identitás- és eszköz-hozzáférési konfigurációk](/microsoft-365/enterprise/microsoft-365-policies-configurations)
- [Azure Active Directory feltételes hozzáférési beállítások ismertetése](../conditional-access/concept-conditional-access-conditions.md)
- [Általános feltételes hozzáférési szabályzatok](../conditional-access/concept-conditional-access-policy-common.md)

## <a name="access-surface-area"></a>Elérési felület

### <a name="legacy-authentication"></a>Örökölt hitelesítés

Az olyan erős hitelesítő adatok, mint például az MFA, nem tudják védelemmel ellátni az alkalmazásokat az örökölt hitelesítési protokollok használatával, amelyek a rosszindulatú szereplők által előnyben részesített támadási vektor Az örökölt hitelesítés zárolása létfontosságú a hozzáférés biztonsági helyzetének javításához.

Az örökölt hitelesítés olyan kifejezés, amely az alkalmazások által használt hitelesítési protokollokra hivatkozik, például:

- A modern hitelesítést nem használó régebbi Office-ügyfelek (például az Office 2010-ügyfél)
- Levelezési protokollokat (például IMAP/SMTP/POP) használó ügyfelek

A támadók határozottan előnyben részesítik ezeket a protokollokat – a [jelszó-szórási támadások csaknem 100%-a](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) örökölt hitelesítési protokollokat használ! A hackerek az örökölt hitelesítési protokollokat használják, mivel nem támogatják az interaktív bejelentkezést, ami további biztonsági problémákhoz szükséges, például a többtényezős hitelesítéshez és az eszközök hitelesítéséhez.

Ha az örökölt hitelesítés széles körben használatos a környezetben, a lehető leghamarabb tervezze meg a régi ügyfelek áttelepíteni a [modern hitelesítést](/office365/enterprise/modern-auth-for-office-2013-and-2016) támogató ügyfelekre. Ugyanabban a jogkivonatban, ha vannak olyan felhasználók, akik már modern hitelesítést használnak, de mások, akik továbbra is örökölt hitelesítést használnak, az alábbi lépéseket kell végrehajtania a korábbi hitelesítési ügyfelek zárolásához:

1. A [bejelentkezési tevékenységgel kapcsolatos jelentések](../reports-monitoring/concept-sign-ins.md) segítségével azonosíthatja azokat a felhasználókat, akik továbbra is örökölt hitelesítést használnak, és megtervezik a szervizelést:

   a. Frissítsen a modern hitelesítésre képes ügyfelekre az érintett felhasználók számára.
   
   b. Tervezze meg a átváltás időkeretét az alábbi lépésekkel.
   
   c. Azonosítsa, hogy az örökölt alkalmazások mennyire függenek az örökölt hitelesítéstől. Lásd az alábbi 3. lépést.

2. Tiltsa le az örökölt protokollokat a forrásnál (például az Exchange-postaládánál) azon felhasználók számára, akik nem használnak örökölt hitelesítést a további expozíció elkerüléséhez.
3. A fennmaradó fiókok (ideális esetben nem emberi identitások, például a szolgáltatásfiókok) esetében a [feltételes hozzáférés használatával korlátozhatja az örökölt protokollok hitelesítés utáni korlátozását](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Conditional-Access-support-for-blocking-legacy-auth-is/ba-p/245417) .

#### <a name="legacy-authentication-recommended-reading"></a>Örökölt hitelesítés ajánlott olvasmánya

- [POP3-vagy IMAP4-hozzáférés engedélyezése vagy letiltása a postaládákhoz az Exchange Serveren](/exchange/clients/pop3-and-imap4/configure-mailbox-access)

### <a name="consent-grants"></a>Hozzájárulási támogatás

A támadók a tiltott engedélyezési támadás során létrehoznak egy Azure AD-regisztrált alkalmazást, amely hozzáférést kér az adatokhoz, például a kapcsolattartási adatokhoz, az e-mailekhez vagy a dokumentumokhoz. A felhasználók a rosszindulatú webhelyekre való leszálláskor adathalászattal kapcsolatos támadások útján engedélyezhetik a kártékony alkalmazásoknak való hozzájárulást.

Az alábbi lista azokat az alkalmazásokat tartalmazza, amelyekkel megvizsgálhatja a Microsoft Cloud Servicest:

- Alkalmazással vagy delegált alkalmazásokkal rendelkező alkalmazások \* . ReadWrite engedélyek
- A delegált engedélyekkel rendelkező alkalmazások elolvashatják, elküldhetik vagy kezelhetik az e-maileket a felhasználó nevében
- A következő engedélyek használatával kapott alkalmazások:

| Erőforrás | Engedély |
| :- | :- |
| Exchange Online | EAS. AccessAsUser. All |
| | EWS. AccessAsUser. All |
| | Mail. Read |
| Microsoft Graph API | Mail. Read |
| | Mail. Read. Shared |
| | Mail. ReadWrite |

- Az alkalmazások a bejelentkezett felhasználó teljes körű felhasználó megszemélyesítését adtak meg. Például:

|Erőforrás | Engedély |
| :- | :- |
| Microsoft Graph API| Directory. AccessAsUser. All |
| Azure REST API | user_impersonation |

Ennek elkerüléséhez tekintse meg a [tiltott engedélyezési támogatások észlelését és szervizelését az Office 365-ben](/office365/securitycompliance/detect-and-remediate-illicit-consent-grants) , hogy azonosítsa és javítsa azokat az alkalmazásokat, amelyek nem rendelkeznek olyan illegális támogatásokkal vagy alkalmazásokkal, amelyek a szükségesnél több támogatással rendelkeznek. Ezután [távolítsa el](../manage-apps/configure-user-consent.md) az önkiszolgáló szolgáltatást teljesen, és [hozzon létre irányítási eljárásokat](../manage-apps/configure-admin-consent-workflow.md). Végezetül ütemezze az alkalmazás engedélyeinek rendszeres felülvizsgálatát, és távolítsa el őket, ha nincs rá szükség.

#### <a name="consent-grants-recommended-reading"></a>Hozzájárulás a javasolt olvasáshoz

- [API-engedélyek Microsoft Graph](/graph/permissions-reference)

### <a name="user-and-group-settings"></a>Felhasználói és csoport beállításai

Az alábbiakban láthatók azok a felhasználói és csoportos beállítások, amelyeket le lehet zárni, ha nincs kifejezett üzleti igény:

#### <a name="user-settings"></a>Felhasználói beállítások

- **Külső felhasználók** – a külső együttműködés szerves módon történhet a vállalaton belül olyan szolgáltatásokkal, mint a csapatok, a Power bi, a SharePoint Online és a Azure Information Protection. Ha kifejezett korlátozásokkal rendelkezik a felhasználó által kezdeményezett külső együttműködés szabályozására, javasoljuk, hogy engedélyezze a külső felhasználókat az [Azure ad-jogosultságok kezelése](../governance/entitlement-management-overview.md) vagy egy ellenőrzött művelet, például az ügyfélszolgálat használatával. Ha nem kívánja engedélyezni a szolgáltatások szerves külső együttműködését, [megakadályozhatja, hogy a tagok teljesen meghívjanak külső felhasználókat](../external-identities/delegate-invitations.md). Azt is megteheti, hogy engedélyezi vagy letiltja a külső felhasználói meghívásokban lévő [egyes tartományokat](../external-identities/allow-deny-list.md) .
- **Alkalmazás-regisztráció** – ha a Alkalmazásregisztrációk engedélyezve vannak, a végfelhasználók maguk is előkészíthetik az alkalmazásokat, és hozzáférést biztosíthatnak az adatkezeléshez. Az alkalmazások regisztrálásának tipikus példája az Outlook beépülő moduljainak engedélyezése, illetve az olyan hangsegédek, mint az Alexa és a Siri, hogy olvassák az e-maileket és a naptárt, vagy e-maileket küldjenek a nevükben. Ha az ügyfél úgy dönt, hogy kikapcsolja az alkalmazás regisztrációját, az InfoSec-és a IAM-csapatoknak részt kell venniük a kivételek kezelésében (az üzleti követelményeken alapuló alkalmazás-regisztrációk esetében), mivel az alkalmazásoknak rendszergazdai fiókkal kell regisztrálniuk, és a legvalószínűbb, hogy a folyamat működővé tenni kell megtervezniük a folyamatot.
- **Felügyeleti portál** – a szervezetek zárolják az Azure ad panelt a Azure Portalban, így a nem rendszergazdák nem férhetnek hozzá az Azure ad Managementhez a Azure Portalban, és nem tévesztendő össze. A hozzáférés korlátozásához nyissa meg az Azure AD felügyeleti portáljának felhasználói beállításait:

![Korlátozott hozzáférés az adminisztrációs portálhoz](./media/active-directory-ops-guide/active-directory-ops-img13.png)

> [!NOTE]
> A nem rendszergazdák továbbra is hozzáférhetnek az Azure AD felügyeleti felületekhez a parancssori felületen és más programozási felületeken keresztül.

#### <a name="group-settings"></a>Csoportbeállítások

**Önkiszolgáló csoportok kezelése/a felhasználók létrehozhatnak biztonsági csoportokat vagy Microsoft 365 csoportokat.** Ha a felhőben nincsenek a csoportokra érvényes önkiszolgáló kezdeményezés, az ügyfelek dönthetnek úgy, hogy kikapcsolják, amíg készen nem állnak a funkció használatára.

#### <a name="groups-recommended-reading"></a>Ajánlott olvasási csoportok

- [Mi az Azure Active Directory B2B együttműködés?](../external-identities/what-is-b2b.md)
- [Alkalmazások integrálása a Azure Active Directory](../develop/quickstart-register-app.md)
- [Alkalmazások, engedélyek és beleegyezett Azure Active Directory.](../develop/quickstart-register-app.md)
- [Csoportok használata a Azure Active Directory erőforrásaihoz való hozzáférés kezeléséhez](./active-directory-manage-groups.md)
- [Önkiszolgáló alkalmazás-hozzáférés felügyeletének beállítása Azure Active Directory](../enterprise-users/groups-self-service-management.md)

### <a name="traffic-from-unexpected-locations"></a>Nem várt helyekről érkező forgalom

A támadók a világ különböző részeiből származnak. Ezt a kockázatot a feltételes hozzáférési szabályzatok és a feltételnek megfelelően kezelheti. A feltételes hozzáférési szabályzat [helyének feltétele](../conditional-access/location-condition.md) lehetővé teszi, hogy letiltsa a hozzáférést azokhoz a helyekhez, amelyekről nincs üzleti ok a bejelentkezéshez.

![Új elnevezett hely létrehozása](./media/active-directory-ops-guide/active-directory-ops-img14.png)

Ha elérhető, egy biztonsági információ és egy eseménykezelő (SIEM) megoldás használatával elemezze és keresse meg a régiók közötti hozzáférési mintákat. Ha nem használ SIEM-terméket, vagy nem végez hitelesítési adatokat az Azure AD-től, javasoljuk, hogy a [Azure monitor](../../azure-monitor/overview.md) használatával azonosítsa a régiók közötti hozzáférés mintáit.

## <a name="access-usage"></a>Hozzáférés használata

### <a name="azure-ad-logs-archived-and-integrated-with-incident-response-plans"></a>Az Azure AD-naplók archiválása és integrálva van az incidensek válaszának csomagjaival

A bejelentkezési tevékenységhez való hozzáférés, az Azure AD naplózási és kockázati eseményei elengedhetetlenek a hibaelhárításhoz, a használati elemzésekhez és a kriminalisztikai vizsgálatokhoz. Az Azure AD hozzáférést biztosít ezekhez a forrásokhoz olyan REST API-kon keresztül, amelyek korlátozott megőrzési időtartammal rendelkeznek. A auditok és a támogatás hosszú távú tárolásának kulcsa a biztonsági információ-és eseménykezelő (SIEM) rendszer vagy az azzal egyenértékű archiválási technológia. Az Azure AD-naplók hosszú távú tárolásának engedélyezéséhez hozzá kell adnia azokat a meglévő SIEM-megoldáshoz, vagy [Azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md)kell használnia. Az incidensek válaszának csomagjai és a vizsgálatok részeként használható archivált naplók.

#### <a name="logs-recommended-reading"></a>A naplók ajánlott olvasása

- [Azure Active Directory audit API-referenciája](/graph/api/resources/directoryaudit?view=graph-rest-beta)
- [Azure Active Directory bejelentkezési tevékenység jelentésének API-referenciája](/graph/api/resources/signin?view=graph-rest-beta)
- [Adatok lekérése az Azure AD Reporting API és tanúsítványok használatával](../reports-monitoring/tutorial-access-api-with-certificates.md)
- [Azure Active Directory Identity Protection Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)
- [Office 365 felügyeleti tevékenység API-referenciája](/office/office-365-management-api/office-365-management-activity-api-reference)
- [Az Azure Active Directory Power BI-tartalomcsomag használata](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

## <a name="summary"></a>Összefoglalás

A biztonságos identitás-infrastruktúrának 12 aspektusa van. Ez a lista segítséget nyújt a hitelesítő adatok biztonságossá tételéhez és kezeléséhez, a hitelesítési élmény meghatározásához, a hozzárendelés delegálásához, a használat méréséhez és a hozzáférési házirendek definiálásához a vállalati biztonsági helyzet alapján.

- Rendeljen tulajdonosokat a legfontosabb feladatokhoz.
- Megoldások implementálása a gyenge vagy kiszivárgott jelszavak észleléséhez, a jelszavak kezeléséhez és védelméhez, valamint az erőforrásokhoz való felhasználói hozzáférés további biztonságossá tételéhez.
- Kezelheti az eszközök identitását, hogy az erőforrások bármikor és bárhonnan védve legyenek.
- Jelszó-alapú hitelesítés implementálása.
- Adjon meg egy szabványosított egyszeri bejelentkezési mechanizmust a szervezeten belül.
- Az alkalmazások AD FSról az Azure AD-be való migrálása nagyobb biztonságot és konzisztens kezelhetőséget tesz lehetővé.
- Felhasználók társítása az alkalmazásokhoz csoportok használatával a nagyobb rugalmasság és a nagy léptékű felügyelet érdekében.
- Kockázatalapú hozzáférési házirendek konfigurálása.
- Az örökölt hitelesítési protokollok zárolása.
- A tiltott engedélyezési támogatások észlelése és szervizelése.
- A felhasználók és a csoportok beállításainak zárolása.
- Az Azure AD-naplók hosszú távú tárolásának engedélyezése a hibaelhárításhoz, a használati elemzésekhez és a kriminalisztikai vizsgálatokhoz.

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a [személyazonosság-irányítási operatív ellenőrzésekkel és műveletekkel](active-directory-ops-guide-govern.md).
