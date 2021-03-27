---
title: Újdonságok Kibocsátási megjegyzések – Azure Active Directory | Microsoft Docs
description: Ismerje meg a Azure Active Directory újdonságait; ilyenek például a legújabb kibocsátási megjegyzések, ismert problémák, hibajavítások, elavult funkciók és a közelgő változások.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/4/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03e783353f65ba2017dda9f6ca21a68ae6cff4de
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612245"
---
# <a name="whats-new-in-azure-active-directory"></a>A Azure Active Directory újdonságai

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` az ![ RSS-hírcsatorna olvasójának ikonját a ](./media/whats-new/feed-icon-16x16.png) hírcsatorna-olvasóba.

Az Azure AD folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- A változtatások tervei

Ez az oldal havonta frissül, ezért rendszeresen újra kell látogatnia. Ha hat hónapnál régebbi elemeket keres, akkor az archívumban találhatja meg a [Azure Active Directory újdonságait](whats-new-archive.md).

---
## <a name="february-2021"></a>2021. február

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>E-mailek egyszeri jelszavas hitelesítése alapértelmezés szerint október 2021

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 

2021. október 31-ig a Microsoft Azure Active Directory [e-mail egyszeri jelszavas hitelesítés](../external-identities/one-time-passcode.md) az alapértelmezett módszer a fiókok és bérlők meghívására a B2B együttműködési forgatókönyvek esetében. A Microsoft jelenleg nem teszi lehetővé a meghívások beváltását a nem felügyelt Azure Active Directory fiókok használatával. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>A nem kért, de a beleegyezett engedélyek többé nem lesznek hozzáadva a tokenekhez, ha feltételes hozzáférést fognak kiváltani.

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Platform
 
Jelenleg a [dinamikus engedélyeket](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) használó alkalmazások minden olyan engedélyt kapnak, amelyhez hozzáférése van. Ez magában foglalja a nem kérelmezett és a feltételes hozzáférést kiváltó alkalmazásokat is. Ilyen lehet például, ha egy alkalmazás csak azt kérő alkalmazást kéri `user.read` , hogy az `files.read` engedélyhez hozzárendelt feltételes hozzáférést kell továbbítania `files.read` . 

A szükségtelen feltételes hozzáférési kérések számának csökkentése érdekében az Azure AD megváltoztatja a nem kérelmezett hatóköröket az alkalmazásokhoz. Az alkalmazások csak feltételes hozzáférést aktiválnak az általuk kifejezetten kért engedélyekhez. További információért olvassa el a [hitelesítés újdonságait](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)ismertető témakört.
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Nyilvános előzetes verzió – a jelszóval nem rendelkező hitelesítő adatok regisztrálására szolgáló ideiglenes hozzáférési lépés használata

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme

Az ideiglenes hozzáférési pass egy időkorlátos PIN-kód, amely erős hitelesítő adatokkal szolgál, és lehetővé teszi a jelszó nélküli hitelesítő adatok és a helyreállítás bevezetését, ha egy felhasználó elvesztette vagy elfelejtette az erős hitelesítési tényezőt (például FIDO2 biztonsági kulcs vagy Microsoft Authenticator), és be kell jelentkeznie az új erős hitelesítési módszerek regisztrálásához. [További információ](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Nyilvános előzetes verzió – bejelentkezett (KMSI) a felhasználói folyamatok következő generációjában

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

A B2C felhasználói folyamatok következő generációja mostantól támogatja a [bejelentkezett (KMSI)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) funkciót, amely lehetővé teszi, hogy az ügyfelek a webes és natív alkalmazások felhasználói számára állandó cookie-val bővítsék a munkamenetek élettartamát.  a szolgáltatás akkor is aktív állapotban tartja a munkamenetet, ha a felhasználó bezárja és újra megnyitja a böngészőt, és a felhasználó kijelentkezik.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Nyilvános előzetes verzió – külső identitások Self-Service regisztráció a HRE MSA-fiókok használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
A külső felhasználók mostantól Microsoft-fiókokkal jelentkezhetnek be az Azure AD-ba az első fél és LOB alkalmazásokban. [További információ](../external-identities/self-service-sign-up-overview.md).

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Nyilvános előzetes verzió – a beváltási állapot visszaállítása vendég felhasználó számára

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
Az ügyfelek mostantól újra meghívhatják a meglévő külső vendég felhasználókat a beváltási állapotuk visszaállítására, ami lehetővé teszi a vendég felhasználói fiók számára, hogy ne veszítse el a hozzáférést. [További információ](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Nyilvános előzetes verzió – a/synchronization (kiépítés) API-k már támogatják az alkalmazás engedélyeit

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Az ügyfelek mostantól használhatják az Application. ReadWrite. ownedby alkalmazást a szinkronizálási API-k meghívására. Vegye figyelembe, hogy ez csak az Azure AD-ből származó külső alkalmazásokban (például AWS, adattéglák stb.) való kiépítés esetén támogatott. Jelenleg nem támogatott a HR-kiépítés (munkanap/SuccessFactors) vagy a Felhőbeli szinkronizálás (AD az Azure AD-hoz). [További információ](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Általános elérhetőség – a hitelesítési házirend rendszergazdájának beépített szerepköre

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
Az ezzel a szerepkörrel rendelkező felhasználók konfigurálhatják a hitelesítési módszerek házirendjét, a bérlői szintű MFA-beállításokat és a jelszavas védelemre vonatkozó házirendet. Ez a szerepkör engedélyt ad a jelszavas védelem beállításainak kezelésére: az intelligens zárolási konfigurációk és az egyéni tiltott jelszavak listájának frissítése. [További információ](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Általános rendelkezésre állás – a saját alkalmazások felhasználói gyűjteményei most már elérhetők!

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** Végfelhasználói élmények
 
A felhasználók mostantól létrehozhatják saját csoportosítási alkalmazásaikat a saját alkalmazások alkalmazás-Indítóban. A rendszergazda által a velük megosztott gyűjtemények átrendezésére és elrejtésére is képesek. [További információ](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>Általános elérhetőség – automatikus kitöltés a hitelesítő

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Microsoft Authenticator alkalmazás  
A **termék képességei:** Identitás-biztonsági & védelme
 
Microsoft Authenticator a többtényezős hitelesítés (MFA) és a fiókkezelés funkcióit biztosítja, és mostantól a webhelyeken és alkalmazásokban található jelszavakat is automatikusan kihasználhatja a mobilon (iOS és Android). 

Az automatikus kitöltés a hitelesítő eszközön való használatához a felhasználóknak hozzá kell adniuk a személyes Microsoft-fiók a hitelesítő számára, és használniuk kell őket a jelszavuk szinkronizálásához. A munkahelyi vagy iskolai fiókok jelenleg nem használhatók a jelszavak szinkronizálására. [További információ](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Általános elérhetőség – belső felhasználók meghívása B2B-együttműködésre

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
Az ügyfelek most meghívhatják a belső vendégeket a B2B-együttműködés használatára ahelyett, hogy meghívót küldene egy meglévő belső fiókra. Ez lehetővé teszi az ügyfeleknek, hogy megőrizze a felhasználó objektumazonosító-AZONOSÍTÓját, UPN-tagságát és az alkalmazás-hozzárendeléseket. [További információ](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Általános elérhetőség – a tartománynév-rendszergazda beépített szerepköre

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
Az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a tartományneveket (olvasás, Hozzáadás, ellenőrzés, frissítés és törlés). Emellett a felhasználókra, csoportokra és alkalmazásokra vonatkozó címtáradatokat is elolvashatnak, mivel ezek az objektumok tartományi függőségekkel rendelkeznek. 

A helyszíni környezetekben az ezzel a szerepkörrel rendelkező felhasználók konfigurálhatják az összevonáshoz tartozó tartományneveket, hogy a társított felhasználók mindig a helyszínen legyenek hitelesítve. Ezek a felhasználók ezután egyszeri bejelentkezéssel jelentkezhetnek be az Azure AD-alapú szolgáltatásba a helyszíni jelszavuk használatával. Az összevonási beállításokat Azure AD Connect használatával kell szinkronizálni, így a felhasználóknak a Azure AD Connect kezelésére is jogosultsággal kell rendelkezniük. [További információ](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – február 2021

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Február 2021-én a következő 37 új alkalmazásokat bővítettük az alkalmazás-galériában az összevonási támogatással:

[Loop Messenger-bővítmény](https://loopworks.com/loop-flow-messenger/), [SILVERFORT az Azure ad-adapter](http://www.silverfort.com/), a [interakciós tanulás](https://skilledtrades.interplaylearning.com/#login), a Yooz- [terület](https://dashboard.nuraspace.com/login), az [EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), a [UXPressia](https://uxpressia.com/users/sign-in), a [introDus előkészítő platform](http://app.introdus.dk/login), a [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), a [LeaksID](https://app.leaksid.com/), a [ShiftWizard](http://www.shiftwizard.com/), a [PingFlow SSO](https://app.pingview.io/), [a Swiftlane, a](https://admin.swiftlane.com/login) [Quasydoc SSO](https://www.quasydoc.eu/login), a [Fenwick Gold Account](https://businesscentral.dynamics.com/), [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft LMS & TMS](http://www.learnsoft.com/), [P-th +](https://p-th.jp/), [MyViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tartabit IoT Bridge](https://bridge-us.tartabit.com/), [AKASHI](../saas-apps/akashi-tutorial.md), [rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Parkalot-parkoló felügyelet](../saas-apps/parkalot-car-park-management-tutorial.md), [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid](../saas-apps/ibmid-tutorial.md), [SharingCloud](../saas-apps/sharingcloud-tutorial.md), [PoolParty szemantikus Suite](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), [Samsung Knox és Business Services](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis-méretezés agilis platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [Maptician](../saas-apps/maptician-tutorial.md), [Olfeo SaaS](../saas-apps/olfeo-saas-tutorial.md), [Sigma Computing](../saas-apps/sigma-computing-tutorial.md), [CloudKnox permissions Management platform](../saas-apps/cloudknox-permissions-management-platform-tutorial.md), [Klaxoon SAML](../saas-apps/klaxoon-saml-tutorial.md) [, Enablon](../saas-apps/enablon-tutorial.md)

Az összes alkalmazás dokumentációját itt tekintheti meg: https://aka.ms/AppsTutorial

Az alkalmazás az Azure AD-katalógusban való listázásához olvassa el a részleteket itt: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Új kiépítési összekötők az Azure AD Application Galleryben – február 2021

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Preciate](../saas-apps/preciate-provisioning-tutorial.md)

További információért olvassa el az [Azure ad-vel történő felhasználói üzembe helyezés automatizálása SaaS-alkalmazásokhoz](../app-provisioning/user-provisioning.md)című témakört.

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Általános rendelkezésre állás – 10 Azure Active Directory szerepkör most átnevezve

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
10 az Azure AD beépített szerepkörei át lettek nevezve, hogy a [Microsoft 365 felügyeleti központban](/microsoft-365/admin/microsoft-365-admin-center-preview), az [Azure ad-portálon](https://portal.azure.com/)és a [Microsoft Graphn](https://developer.microsoft.com/graph/)legyenek igazítva. Ha többet szeretne megtudni az új szerepkörökről, tekintse meg a [rendszergazdai szerepkörre vonatkozó engedélyeket Azure Active Directory](../roles/permissions-reference.md#all-roles).

![Az MS Graph APIban és a Azure Portalban található szerepkörök nevét, valamint az API, a Azure Portal és a Mac rendszerekben javasolt végső nevet bemutató táblázat.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Új vállalati arculat az MFA-ban/SSPR kombinált regisztráció

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Felhasználói élmény és felügyelet  
A **termék képességei:** Végfelhasználói élmények
 
A múltban a vállalati emblémák nem voltak használatban Azure Active Directory bejelentkezési lapokon. A cég arculata mostantól az MFA/SSPR kombinált regisztráció bal felső részén található. A vállalati arculatot a Sign-Ins és a biztonsági adatok lap is tartalmazza. [További információ](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Általános rendelkezésre állás – a második szintű kezelő másodlagos jóváhagyóként is beállítható

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés kezelése  
A **termék képességei:** Jogosultságok kezelése
 
Ha a jóváhagyók lehetőséget választja, a jogosultságok kezelése szolgáltatásban már elérhető egy további lehetőség. Ha az első jóváhagyóhoz a "kezelő mint jóváhagyó" lehetőséget választja, akkor a másodlagos jóváhagyó mezőben választhat egy másik "második szintű kezelő mint másodlagos jóváhagyó" beállítást. Ha ezt a beállítást választja, egy tartalék jóváhagyót kell hozzáadnia a kérelem továbbításához, ha a rendszer nem találja a második szintű kezelőt. [További információ](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>Hitelesítési módszerek tevékenység irányítópultja

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 

A frissített hitelesítési módszerek tevékenység irányítópultja a rendszergazdáknak áttekintést nyújt a hitelesítési módszer regisztrációjának és használati tevékenységeinek a bérlőben való használatáról. A jelentés összefoglalja az egyes metódusokhoz regisztrált felhasználók számát, valamint azt is, hogy mely módszereket használja a rendszer a bejelentkezés és a jelszó alaphelyzetbe állítása során. [További információ](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>A frissítés és a munkamenet-jogkivonat élettartamának megújítása a konfigurálható jogkivonat élettartama (CTL) esetében megszűnik

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Felhasználói hitelesítés
 
A frissítés és a munkamenet-jogkivonat élettartamának a CTL-ben való konfigurálásának kivonása folyamatban van. Azure Active Directory már nem veszi figyelembe a frissítési és a munkamenet-jogkivonat konfigurációját a meglévő házirendekben. [További információ](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---
 
## <a name="january-2021"></a>2021. január

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>A kiépítés konfigurálásakor a titkos jogkivonat kötelező mező lesz

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése

A múltban a titkos jogkivonat mező üresen maradhat a kiépítés beállításakor az egyéni/BYOA alkalmazásban. Ez a függvény kizárólag tesztelésre szolgál. Frissíti a felhasználói felületet, hogy kötelezővé tegye a mezőt. 

Ezt a követelményt tesztelési célból a böngésző URL-címében található szolgáltatás-jelölő használatával lehet megkerülni. [További információ](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>Nyilvános előzetes verzió – az androidos megosztott eszközök testreszabása és konfigurálása az élvonalbeli feldolgozók számára

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Eszközök regisztrációja és kezelése  
A **termék képességei:** Identitás-biztonsági & védelme
 
Az Azure AD és a Microsoft Endpoint Manager csapatainak együttes használata lehetővé teszi a front Worker-eszközök testreszabását, méretezését és védelmét.

A következő előzetes verzió funkciói lehetővé teszik a következőket:
- Androidos megosztott eszközök kiépítése a Microsoft Endpoint Managerrel
- Az eszközökön alapuló feltételes hozzáférés használatával biztosíthatja a hozzáférését a műszakban dolgozó munkavállalók számára
- Bejelentkezési élmények testreszabása felügyelt kezdőképernyő használatával

További információért lásd [: megosztott eszközök testreszabása és konfigurálása az élvonalbeli feldolgozók számára a skálán](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708).

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Nyilvános előzetes verzió – az üzembe helyezési naplók mostantól CSV-ként vagy JSON-ként tölthetők le

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése

Az ügyfelek a felhasználói felületen és a Graph API-n keresztül tölthetik le a kiépítési naplókat CSV-vagy JSON-fájlként. További információért tekintse meg a [jelentések kiépítési jelentéseit a Azure Active Directory portálon](../reports-monitoring/concept-provisioning-logs.md).

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Nyilvános előzetes verzió – felhőalapú csoportok társítása az Azure AD egyéni szerepköreihez és a felügyeleti egység hatókörű szerepköreihez

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
Az ügyfelek létrehozhatnak egy felhőalapú csoportot az Azure AD egyéni szerepköreihez vagy egy felügyeleti egység hatókörű szerepköréhez. A funkció használatának megismeréséhez tekintse meg a következő témakört [: felhőalapú csoportok használata a szerepkör-hozzárendelések Azure Active Directory való kezeléséhez](../roles/groups-concept.md).

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Általános rendelkezésre állás – Azure AD Connect Cloud Sync (korábbi nevén felhő-kiépítés)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Connect Cloud Sync  
A **termék képességei:** Identitás-életciklus kezelése
 
A Azure AD Connect Cloud Sync mostantól általánosan elérhető az összes ügyfél számára.

Azure AD Connect a felhő áthelyezi az átalakítási logikát a felhőbe, így csökkentve a helyszíni lábnyomot. Emellett több, a rendelkezésre állást biztosító ügynök üzembe helyezése is elérhető a nagyobb szinkronizálás érdekében. [További információ](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Általános rendelkezésre állás – a támadás szimulációjának rendszergazdája és a támadási adattartalom szerzője beépített szerepkörök

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
Role-Based Access Control két új szerepkört rendelhet hozzá a felhasználókhoz, a támadási szimulációs rendszergazdához és a támadási adatok létrehozásához. 

A [támadási szimulációs rendszergazdai](../roles/permissions-reference.md#attack-simulation-administrator) szerepkör felhasználói hozzáférhetnek a bérlő összes szimulációja számára, és a következőket tehetik:
- a támadási szimulációs létrehozás minden aspektusának létrehozása és kezelése
- szimuláció indítása/ütemezése
-  a Szimulációs eredmények áttekintése. 

A [támadási adatok szerzői](../roles/permissions-reference.md#attack-payload-author) szerepkörének felhasználói a támadási hasznos adatokat hozhatnak létre, de valójában nem indítják el vagy nem ütemezhetik azokat. A támadási adatok ezután a bérlő minden olyan rendszergazdája számára elérhetők lesznek, akik a szimuláció létrehozásához használhatják őket.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Általános elérhetőség – a használati összesítő jelentések olvasójának beépített szerepköre

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
A használati összesítő jelentéseket olvasó szerepkörrel rendelkező felhasználók a bérlői szintű összesített adatokat és a Microsoft 365 felügyeleti központban a használati és termelékenységi pontszámhoz kapcsolódó információkat érhetik el. Azonban nem férhetnek hozzá a felhasználói szint részleteihez vagy az információkhoz. 

A két jelentés Microsoft 365 felügyeleti központjában megkülönböztetjük a bérlői szint összesített adatait és a felhasználói szint részleteit. Ez a szerepkör további védelmi réteget biztosít az egyes felhasználók számára azonosítható adatokhoz. [További információ](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Általános rendelkezésre állás – az App Protection-szabályzatok engedélyezésének megkövetelése az Azure AD feltételes hozzáférésben

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme
 
Az Azure AD feltételes hozzáférésének engedélyezése az "app Protection-szabályzat megkövetelése" kifejezés most már GA. 

A házirend a következő képességeket biztosítja:
- Csak akkor engedélyezi a hozzáférést, ha az Intune app Protectiont támogató mobileszköz-alkalmazást használ
- Csak akkor engedélyezi a hozzáférést, ha a felhasználónak van egy Intune app Protection-szabályzata, amelyet a Mobile Application szolgáltatáshoz továbbítanak.

További információ az alkalmazás-védelem feltételes hozzáférési szabályzatának beállításáról [itt](../conditional-access/app-protection-based-conditional-access.md)található.
 
---

### <a name="general-availability---email-one-time-passcode"></a>Általános rendelkezésre állás – e-mail-One-Time PIN-kód

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
Az e-mailes egyszeri jelszavas szolgáltatás lehetővé teszi, hogy a világ bármely részén működjön együtt bárkivel, ha e-mailben küld egy hivatkozást A meghívott felhasználók ellenőrizhetik személyazonosságát az e-mail-címre küldött egyszeri jelszóval, hogy hozzáférjenek a partner erőforrásaihoz. [További információ](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Új kiépítési összekötők az Azure AD Application Galleryben – január 2021

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

További információ: [Mi az az Azure ad-ben az automatizált SaaS app User kiépítés?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – január 2021

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2021 januárjában a következő 29 új alkalmazást bővítettük az alkalmazás-galériában az összevonási támogatással:

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench](https://account.supportbench.net/agent/login/) [](../saas-apps/burp-suite-enterprise-edition-tutorial.md) ,[Bienvenue formáció](https://formation.bienvenue.pro/login), az [egészségügyi egyszeri bejelentkezés](https://aidaforparents.com/login/organizations) [, a](../saas-apps/contentsquare-sso-tutorial.md) [nemzetközi SOS-segítségnyújtó termékek](../saas-apps/international-sos-assistance-products-tutorial.md), a [NAVEX One](../saas-apps/navex-one-tutorial.md), a [LabLog](../saas-apps/lablog-tutorial.md), a [Oktopost SAML](../saas-apps/oktopost-saml-tutorial.md), a [EPHOTO Dam](../saas-apps/ephoto-dam-tutorial.md), a [fogalom](../saas-apps/notion-tutorial.md) [, a Syndio,](../saas-apps/syndio-tutorial.md)a Yello [Enterprise](../saas-apps/yello-enterprise-tutorial.md), a [TimeClock 365 SAML](../saas-apps/timeclock-365-saml-tutorial.md), a [Nalco E-adatok](https://www.ecolab.com/), a [betöltetlen kitöltő](https://app.vacancy-filler.co.uk/VFMVC/Account/Login), a [Synerise AI növekedési ökoszisztémája](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md) [, a](../saas-apps/aruba-user-experience-insight-tutorial.md)inperverz [adatok biztonsága](../saas-apps/imperva-data-security-tutorial.md), a csalóka [](../saas-apps/perimeter-81-tutorial.md) [-hálózatok,](../saas-apps/illusive-networks-tutorial.md) [a sPlan](../saas-apps/proware-tutorial.md) [-látogató 81](../saas-apps/splan-visitor-tutorial.md)

Itt megtalálhatja az összes alkalmazás dokumentációját is https://aka.ms/AppsTutorial

Az alkalmazás az Azure AD-katalógusban való listázásához olvassa el a részleteket itt: https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Nyilvános előzetes verzió – a második szintű kezelő másodlagos jóváhagyóként is beállítható

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés kezelése  
A **termék képességei:** Jogosultságok kezelése
 
Ha a jóváhagyók lehetőséget választja, a jogosultságok kezelése szolgáltatásban már elérhető egy további lehetőség. Ha az első jóváhagyóhoz a "kezelő mint jóváhagyó" lehetőséget választja, akkor a másodlagos jóváhagyó mezőben választhat egy másik "második szintű kezelő mint másodlagos jóváhagyó" beállítást. Ha ezt a beállítást választja, egy tartalék jóváhagyót kell hozzáadnia a kérelem továbbításához, ha a rendszer nem találja a második szintű kezelőt. [További információ](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Általános elérhetőség – közvetlenül a My Access portálról navigáljon a csapatokhoz

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés kezelése  
A **termék képességei:** Jogosultságok kezelése
 
Mostantól közvetlenül a saját hozzáférési portálról is elindíthatja a csapatokat. 

Ehhez jelentkezzen be a saját hozzáférésbe (Nyissa meg a https://myaccess.microsoft.com/) "hozzáférési csomagok" lehetőséget, majd lépjen az "aktív" lapra, ahol megtekintheti az összes olyan hozzáférési csomagot, amelyhez már van hozzáférése. Ha kibontja a kiválasztott hozzáférési csomagot, és a csapatok fölé viszi, a "Megnyitás" gombra kattintva indíthatja el. [További információ](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Továbbfejlesztett naplózási & End-User kockázatos vendég felhasználóinak

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme
 

A kockázatos vendég felhasználóinak naplózási és End-User kérése frissült. További információ az [Identity Protection és a B2B-felhasználók számára](../identity-protection/concept-identity-protection-b2b.md).
 
---
 
## <a name="december-2020"></a>2020. december

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Nyilvános előzetes verzió – Azure AD B2C telefonos regisztráció és bejelentkezés a beépített szabályzattal

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
A B2C telefonos regisztráció és bejelentkezés a beépített házirenddel lehetővé teszi a rendszergazdák és a szervezetek fejlesztői számára, hogy lehetővé tegyék a végfelhasználók számára a bejelentkezést és a felhasználói folyamatokban lévő telefonszám használatával történő regisztrációt. További információért olvassa el a [telefonos regisztráció és a felhasználói folyamatok bejelentkezésének beállítása (előzetes verzió)](../../active-directory-b2c/phone-authentication-user-flows.md) című témakört.

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Általános rendelkezésre állás – a biztonsági alapértékek mostantól engedélyezve vannak az összes új bérlő számára alapértelmezés szerint

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Identitás-biztonsági & védelme
 
A felhasználói fiókok védelme érdekében a 2020. november 12-én vagy azt követően létrehozott új bérlők a biztonsági alapértékek engedélyezve lesznek. A biztonsági alapértelmezett beállítások több szabályzatot kényszerítenek, többek között:
- Minden felhasználónak és rendszergazdának regisztrálnia kell az MFA-t az Microsoft Authenticator alkalmazás használatával
- A kritikus fontosságú rendszergazdai szerepköröket igényel a többtényezős hitelesítés használatához, valahányszor bejelentkeznek. Ha szükséges, minden más felhasználónál szükség lesz az MFA-ra. 
- A régi hitelesítés le lesz tiltva a bérlők számára. 

További információért olvassa el [a mi a biztonsági alapértékek?](../fundamentals/concept-fundamentals-security-defaults.md) című témakört.

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Általános rendelkezésre állás – a AADConnect legfeljebb 250K taggal rendelkező csoportok támogatása

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** AD-kapcsolat  
A **termék képességei:** Identitás-életciklus kezelése
 
A Microsoft új végpontot (API-t) helyezett üzembe a Azure AD Connect számára, amely javítja a szinkronizálási szolgáltatás műveleteinek teljesítményét Azure Active Directory. Az új [v2-végpont](../hybrid/how-to-connect-sync-endpoint-api-v2.md)használatakor észrevehető teljesítménybeli nyereség fog megjelenni az Azure ad-ba való exportálás és importálás során. Ez az új végpont a következő forgatókönyveket támogatja:

- Csoportok szinkronizálása legfeljebb 250k taggal
- Teljesítménybeli nyereség az Azure AD-ba való exportálás és importálás során

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Általános elérhetőség – az Azure China Cloud-beli bérlők számára elérhető jogosultságok kezelése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés kezelése  
A **termék képességei:** Jogosultságok kezelése
 

A jogosultságok kezelésének képességei mostantól elérhetők az Azure China Cloud összes bérlője számára. További információért látogasson el az [Identity irányítási dokumentációs](https://docs.azure.cn/zh-cn/active-directory/governance/) webhelyre.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Új kiépítési összekötők az Azure AD alkalmazás-katalógusban – december 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Bizagi Studio a digitális folyamatautomatizáláshoz](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](../app-provisioning/user-provisioning.md)használatával című témakört.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – december 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2020 decemberében a következő 18 új alkalmazást bővítettük az alkalmazás-galériában az összevonási támogatással:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow egyszeri bejelentkezés](https://gethownow.com/), [ZyLAB egy jogi megtartó](https://www.zylab.com/en/product/legal-hold), [vezető](http://www.guider-ai.com/), [Softcrisis](https://www.softcrisis.se/sv/), [PIMS 365](http://www.omega365.com/pims), [InformaCast](../saas-apps/informacast-tutorial.md), [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [Vonage](../saas-apps/vonage-tutorial.md), [Count me in-Operations irányítópult](../saas-apps/count-me-in-operations-dashboard-tutorial.md), RightCrowd-alapú [Tudásbázis](../saas-apps/proprofs-knowledge-base-tutorial.md), JLL- [munkatársak kezelése](../saas-apps/rightcrowd-workforce-management-tutorial.md), [TRIRIGA Shutterstock](../saas-apps/jll-tririga-tutorial.md), [FortiWeb](../saas-apps/shutterstock-tutorial.md), [Equinix webalkalmazási tűzfal](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [KFAdvance összevonási alkalmazás](../saas-apps/equinix-federation-app-tutorial.md) [,](../saas-apps/kfadvance-tutorial.md)

Itt megtalálhatja az összes alkalmazás dokumentációját is https://aka.ms/AppsTutorial

Az alkalmazás az Azure AD-katalógusban való listázásához olvassa el a részleteket itt: https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Közvetlenül a saját hozzáférési portálról navigáljon a csapatokhoz

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés-kezelő **termék funkció:** jogosultságok kezelése

Mostantól közvetlenül a saját hozzáférési portálról indíthat csapatokat. Ehhez jelentkezzen be a [saját hozzáférésbe](https://myaccess.microsoft.com/), navigáljon a **hozzáférési csomagok** elemre, majd lépjen az **aktív** lapra, és tekintse meg az összes olyan hozzáférési csomagot, amelyhez már van hozzáférése. Ha kibontja a hozzáférési csomagot, és a csapatok fölé viszi, akkor a **Megnyitás** gombra kattintva indíthatja el. 

Ha többet szeretne megtudni a saját hozzáférési portál használatával kapcsolatban, lépjen az [Azure ad-jogosultságok kezelése hozzáférési csomaghoz való hozzáférés kérése](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)című témakörre.

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Nyilvános előzetes verzió – a második szintű kezelő másodlagos jóváhagyóként is beállítható

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés kezelése  
A **termék képességei:** Jogosultságok kezelése

A jogosultságok kezelése szolgáltatásban már elérhető egy további lehetőség a jóváhagyási folyamatban. Ha az első jóváhagyóhoz kiválasztja a felettest, akkor a másodlagos jóváhagyó mezőben választhat másik lehetőséget, a második szintű felettest is. Ha ezt a beállítást választja, hozzá kell adnia egy tartalék jóváhagyót, hogy továbbítsa a kérést arra az esetre, ha a rendszer nem találja a második szintű kezelőt.

További információ: [hozzáférési csomag jóváhagyási beállításainak módosítása az Azure ad-jogosultságok kezelésében](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).

--- 

## <a name="november-2020"></a>2020. november

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1,0, TLS 1,1 és 3DES elavulás

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Minden Azure AD-alkalmazás  
A **termék képességei:** Szabványok

A Azure Active Directory a következő protokollokat fogja érvényteleníteni Azure Active Directory globális régiókban, 2021. június 30-ig:

- TLS 1.0
- TLS 1.1
- 3DES titkosítási csomag (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Az érintett környezetek a következők:
- Azure kereskedelmi felhő
- Office 365 GCC és WW

Kapcsolódó hirdetmény az összes ügyfél-kiszolgáló és böngésző-kiszolgáló kombinációnak a TLS 1,2 és a modern titkosítási csomagokat kell használnia az Azure-hoz, az Office 365-hoz és a Microsoft 365-szolgáltatásokhoz Azure Active Directory biztonságos kapcsolat fenntartásához. Ez a változás a [Azure Active Directory TLS 1,0 & 1,1 és a 3Des rejtjel Suite elavult US gov felhőben](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – november 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

November 2020-én a következő 52 új alkalmazásokat bővítettük az alkalmazás-galériában az összevonási támogatással:

[Utazási & költségelszámolás](https://app.expenseonce.com/Account/Login), [Tribeloo](../saas-apps/tribeloo-tutorial.md), [Itslearning file Picker](https://pmteam.itslearning.com/), [válságok](../saas-apps/crises-control-tutorial.md)kezelése, [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum-Study-sziget](https://app.studyisland.com/cfw/login/), [Virtual Risk Manager](../saas-apps/virtual-risk-manager-tutorial.md), a kitalált,, [szemlélő analitikai platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview-toborzás](https://recruit.talview.com/login), valós idejű [fordító, Klaxoon, podbean](https://access.klaxoon.com/login) [, zcal](../saas-apps/podbean-tutorial.md) [, expensemanager](https://zcal.co/signup) [, Netsparker](https://api.expense-manager.com/) [Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [en-Trak bérlői élmény platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md) [, Builterra,](https://portal.builterra.com/) [Eva bejelentkezés](https://my.evacheckin.com/organization) [,](../saas-apps/sailpoint-identitynow-tutorial.md) [HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md), [Coupe Risk értékelés](../saas-apps/coupa-risk-assess-tutorial.md), [világos](../saas-apps/lucid-tutorial.md)erőforrás [](https://portal.brightbooking.eu/)-[Közép](../saas-apps/resource-central-tutorial.md)-, [UiPathStudioO365App](https://www.uipath.com/product/platform)-, [Jedox](../saas-apps/jedox-tutorial.md)-, Cequence- [alkalmazások biztonsága](../saas-apps/cequence-application-security-tutorial.md), [PerimeterX](../saas-apps/perimeterx-tutorial.md), [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion](../saas-apps/lexion-tutorial.md), [WorkWare](../saas-apps/workware-tutorial.md), [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN](../saas-apps/aws-clientvpn-tutorial.md), [AppSec flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum](../saas-apps/luum-tutorial.md), [árufuvarozási mérték](https://www.gpcsl.com/freight.html), [Terraform felhő](../saas-apps/terraform-cloud-tutorial.md), [Nature Research](../saas-apps/nature-research-tutorial.md), [Play digitális aláírás](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus](../saas-apps/prolorus-tutorial.md), [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Sunrise Software Relations CRM](https://cloud.relations-crm.com/), [Procaire](../saas-apps/procaire-tutorial.md), [mentor® by eDriving: Business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/) [](../saas-apps/timu-tutorial.md)

Itt megtalálhatja az összes alkalmazás dokumentációját is https://aka.ms/AppsTutorial

Az alkalmazás az Azure AD-katalógusban való listázásához olvassa el a részleteket itt: https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Nyilvános előzetes verzió – a vállalati alkalmazások egyéni szerepkörei

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
 A [delegált vállalati alkalmazások felügyeletének egyéni RBAC szerepkörei](../roles/custom-available-permissions.md) már nyilvános előzetes verzióban érhetők el. Ezek az új engedélyek az alkalmazás regisztrációjának egyéni szerepköreire épülnek, ami lehetővé teszi a rendszergazdák számára elérhető hozzáférés részletes szabályozását. Az idő múlásával az Azure AD felügyeletének delegálására további engedélyek lesznek közzétéve.

Néhány gyakori delegálási forgatókönyv:
- az SAML-alapú egyszeri bejelentkezés alkalmazásaihoz hozzáférő felhasználók és csoportok hozzárendelése
- Azure AD Gallery-alkalmazások létrehozása
- az SAML-alapú egyszeri bejelentkezési alkalmazások alapszintű SAML-konfigurációinak frissítése és olvasása
- az SAML-alapú egyszeri bejelentkezési alkalmazások aláíró tanúsítványainak kezelése
- a tanúsítvány lejárati bejelentkezési tanúsítványainak frissítése értesítő e-mail-címek az SAML-alapú egyszeri bejelentkezési alkalmazásokhoz
- az SAML-jogkivonat aláírásának és bejelentkezési algoritmusának frissítése az SAML-alapú egyszeri bejelentkezés alkalmazásaihoz
- felhasználói attribútumok és jogcímek létrehozása, törlése és frissítése az SAML-alapú egyszeri bejelentkezés alkalmazásaihoz
- kiépítési feladatok bekapcsolásának, kikapcsolásának és újraindításának lehetősége
- attribútumok leképezésének frissítései
- az objektumhoz társított kiépítési beállítások olvasásának lehetősége
- az egyszerű szolgáltatáshoz társított kiépítési beállítások beolvasása
- lehetőség az alkalmazás-hozzáférés engedélyezésére a kiépítés során

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Nyilvános előzetes verzió – az Azure AD Application Proxy natív módon támogatja az egyszeri bejelentkezéses hozzáférést az olyan alkalmazásokhoz, amelyek a fejléceket használják a hitelesítéshez

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control
 
Az Azure Active Directory (Azure AD) alkalmazásproxy natív módon támogatja az egyszeri bejelentkezéses hozzáférést olyan alkalmazásokhoz, amelyek fejléceket használnak a hitelesítéshez. Az Azure AD-ben az alkalmazás által igényelt fejléc-értékeket is konfigurálhatja. A fejléc értékei az Application proxyn keresztül lesznek továbbítva az alkalmazásnak. További információ: [fejléc-alapú egyszeri bejelentkezés a helyi alkalmazásokhoz Azure ad alkalmazás proxyval](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md)
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Általános rendelkezésre állás – Azure AD B2C telefonos regisztráció és bejelentkezés egyéni házirend használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

A telefonos regisztrációval és bejelentkezéssel a fejlesztők és a vállalatok lehetővé tehetik ügyfeleiknek, hogy regisztráljanak, és jelentkezzenek be a felhasználó telefonszámára SMS-ben eljuttatott egyszeri jelszó használatával. Ez a funkció azt is lehetővé teszi, hogy az ügyfél megváltoztassa a telefonszámát, ha elvesztik a telefonjára való hozzáférést. Az egyéni szabályzatok erejével lehetővé teheti a fejlesztők és a vállalatok számára, hogy a márkákat az oldal testreszabásával kommunikáljanak. Megtudhatja, hogyan [állíthatja be a telefonos regisztrációt, és hogyan jelentkezhet be a Azure ad B2C egyéni házirendjeivel](../../active-directory-b2c/phone-authentication-user-flows.md).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – november 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic-tac Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

További információ: a [felhasználók üzembe helyezésének automatizálása SaaS-alkalmazásokhoz az Azure ad-vel](../app-provisioning/user-provisioning.md).
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Nyilvános előzetes verzió – e-mail-Sign-In a ProxyAddresses mostantól üzembe helyezhető az előkészített bevezetésen keresztül

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
A bérlői rendszergazdák mostantól a szakaszos bevezetéssel telepíthetik az e-mail-Sign-Inokat a ProxyAddresses-mel adott Azure AD-csoportokba Ez segíthet a szolgáltatás kipróbálásában, mielőtt üzembe helyezné a teljes bérlőn a Kezdőlap tartomány-felderítési házirend használatával. Az e-mailek Sign-In a ProxyAddresses használatával történő üzembe helyezésével kapcsolatos utasítások a [dokumentációban](../authentication/howto-authentication-use-email-signin.md)találhatók.
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Korlátozott előzetes verzió – bejelentkezési diagnosztika

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 
A bejelentkezési diagnosztika kezdeti előzetes kiadásával a rendszergazdák mostantól ellenőrizhetik a felhasználói bejelentkezéseket. A rendszergazdák a bejelentkezéskor és a problémák elhárítása során megkaphatják a kontextust, a konkrét és a kapcsolódó adatokat és útmutatást. A diagnosztika az Azure AD-szinten is elérhető, a feltételes hozzáférés pedig a pengék diagnosztizálására és megoldására szolgál. Az ebben a kiadásban szereplő diagnosztikai forgatókönyvek feltételes hozzáférés, Multi-Factor Authentication és sikeres bejelentkezés.

További információ: [Mi a bejelentkezési diagnosztika az Azure ad-ben?](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Továbbfejlesztett ismeretlen bejelentkezési tulajdonságok

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

  Az ismeretlen bejelentkezési tulajdonságok észlelése frissült. Előfordulhat, hogy az ügyfelek nagyobb kockázatú, ismeretlen bejelentkezési tulajdonságokat észlelnek. További információ: [Mi a kockázat?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Már elérhető a felhőalapú kiépítési ügynök nyilvános előzetes frissítése (verzió: 1.1.281.0)

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Azure AD-beli felhőalapú kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
A felhőalapú kiépítési ügynök nyilvános előzetes verzióban érhető el, és mostantól elérhető a portálon keresztül. Ez a kiadás számos fejlesztést tartalmaz, többek között a tartományok GMSA támogatását, ami jobb biztonságot, továbbfejlesztett kezdeti szinkronizálási ciklusokat és nagy csoportok támogatását teszi lehetővé. További részletekért tekintse meg a kiadási verziók [előzményeit](../app-provisioning/provisioning-agent-release-version-history.md) . 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>BitLocker helyreállítási kulcs API-végpontja most a/informationProtection alatt

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Eszköz-hozzáférés kezelése  
A **termék képességei:** Eszközök életciklusának kezelése
 
Korábban a/BitLocker-végponton keresztül helyreállíthatja a BitLocker-kulcsokat. Végül a végpont elavulttá válik, és az ügyfeleknek meg kell kezdeniük az API-t, amely most már a/informationProtection. alá esik 

A módosítások megjelenítéséhez tekintse meg a [BitLocker helyreállítási API](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) -t a dokumentáció frissítéseihez.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>Az alkalmazásproxy-támogatás általános elérhetősége Távoli asztali szolgáltatások HTML5 webes ügyfélprogramhoz

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control
 
Az Azure AD Application Proxy a Távoli asztali szolgáltatások (RDS) webes ügyfélprogram támogatása már általánosan elérhető. Az RDS webes ügyfélprogram lehetővé teszi, hogy a felhasználók bármilyen HTLM5-kompatibilis böngészővel hozzáférhessenek Távoli asztal-infrastruktúrához, például a Microsoft Edge, az Internet Explorer 11, a Google Chrome stb. A felhasználók ugyanúgy kezelhetik a távoli alkalmazásokat vagy asztalokat, mint a helyi eszközökkel. 

Az Azure AD Application Proxy használatával megnövelheti az RDS-környezet biztonságát, ha előhitelesítést és feltételes hozzáférési szabályzatokat alkalmaz az összes típusú Rich Client-alkalmazáshoz. További információ: [Távoli asztal közzététele az Azure](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md) -ban ad Application proxy
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Az új bővített dinamikus csoportos szolgáltatás nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés
 
A bővített dinamikus csoportos szolgáltatás már nyilvános előzetes verzióban érhető el. Az új szolgáltatással a bérlők számára dinamikus csoportokat létrehozó ügyfelek fognak használni. A dinamikus csoport létrehozásához szükséges idő a bérlő méretének helyett a létrehozandó csoport méretével arányos lesz. Ez a frissítés jelentősen javítja a nagy méretű bérlők teljesítményét, ha az ügyfelek kisebb csoportokat hoznak létre. 

Az új szolgáltatás emellett a tagok hozzáadásának és eltávolításának befejezését is célozza, mert néhány percen belül módosul az attribútum. Az egyetlen feldolgozási hiba nem blokkolja a bérlői feldolgozást. A dinamikus csoportok létrehozásával kapcsolatos további tudnivalókért tekintse meg a [dokumentációt](../enterprise-users/groups-create-rule.md).
 
---
## <a name="october-2020"></a>2020. október

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Az Azure AD helyszíni hibrid ügynökök által érintett Azure TLS-tanúsítványok változásai

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Platform

A Microsoft frissíti az Azure-szolgáltatásokat, hogy TLS-tanúsítványokat használjanak egy másik legfelső szintű hitelesítésszolgáltatóktól (CA). Ezt a frissítést az aktuális HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok jelentik, amelyek nem felelnek meg a CA/Browser fórum alapkövetelményének. Ez a változás hatással lesz a helyileg telepített Azure AD hibrid ügynökökre, amelyek megerősített környezettel rendelkeznek a főtanúsítványok rögzített listájával, és frissíteni kell az új tanúsítvány-kiállítók megbízhatóságára.

Ez a változás a szolgáltatás megszakadását eredményezi, ha nem azonnal intézkedik. Ezek az ügynökök többek között olyan [alkalmazásproxy-összekötőket](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) tartalmaznak, amelyek lehetővé teszik a helyszíni, [áteresztő hitelesítési](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) ügynökök számára, hogy a felhasználók ugyanazzal a jelszóval jelentkezzenek be az alkalmazásokba, és a [Felhőbeli kiépítési előnézet](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) ügynökei az ad-t az Azure ad Sync szolgáltatáshoz használják. 

Ha a tűzfalszabályok olyan környezettel rendelkeznek, amely engedélyezi a kimenő hívások számára a visszavont tanúsítványok listájának (CRL) letöltését, akkor engedélyeznie kell a következő CRL-eket és az OCSP URL-címeket. A módosítással és a CRL-ekkel, valamint az OCSP URL-címekkel kapcsolatos részletes információkért lásd: az  [Azure TLS-tanúsítvány változásai](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>A kiépítési események el lesznek távolítva a naplókból, és kizárólag az üzembe helyezési naplók számára lettek közzétéve.

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 
A SCIM [kiépítési szolgáltatásának](../app-provisioning/user-provisioning.md) tevékenységeit a rendszer naplózza a naplók és a kiépítési naplók között is. Ez olyan tevékenységeket foglal magában, mint például a felhasználó létrehozása a ServiceNow-ben, a csoport GSuite vagy az AWS szerepkör importálása. A későbbiekben ezek az események csak a kiépítési naplókban lesznek közzétéve. Ennek a változásnak a megvalósítása folyamatban van, hogy elkerülje a naplók ismétlődő eseményeit, és a log Analyticsben a naplókat fogyasztó ügyfelek által felmerülő további költségeket. 

A dátum befejeződik után egy frissítést biztosítunk. Ez az elavultság nem a 2020-es naptári évre van tervezve. 

> [!NOTE]
> Ez nem befolyásolja a naplókban lévő eseményeket a kiépítési szolgáltatás által kibocsátott szinkronizálási eseményeken kívül. Az eseményeket, például egy alkalmazás létrehozását, a feltételes hozzáférési szabályzatot, a címtárban lévő felhasználót, stb. továbbra is a naplók lesznek kibocsátva a naplókban. [További információ](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Az Azure AD helyszíni hibrid ügynökeit érinti az Azure Transport Layer Security (TLS) tanúsítványának változásai

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Platform
 
A Microsoft frissíti az Azure-szolgáltatásokat, hogy TLS-tanúsítványokat használjanak egy másik legfelső szintű hitelesítésszolgáltatóktól (CA). Az aktuális HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok nem a HITELESÍTÉSSZOLGÁLTATÓ/böngésző alapkonfigurációjának egyik követelményét követve frissülni fognak. Ez a változás hatással lesz a helyileg telepített Azure AD hibrid ügynökökre, amelyek megerősített környezettel rendelkeznek a főtanúsítványok rögzített listájával. Ezeket az ügynököket frissíteni kell, hogy megbízzanak az új tanúsítvány-kiállítókkal.

Ez a változás a szolgáltatás megszakadását eredményezi, ha nem azonnal intézkedik. Ezek az ügynökök a következők: 
- [Alkalmazásproxy-összekötők](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) a helyszíni távoli eléréshez 
- [Továbbító hitelesítési](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) ügynökök, amelyek lehetővé teszik a felhasználók számára, hogy ugyanazzal a jelszóval jelentkezzenek be az alkalmazásokba
- [Felhőalapú kiépítési előnézet](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) -ügynökök, amelyek ad az Azure ad Sync szolgáltatáshoz. 

Ha a tűzfalszabályok olyan környezettel rendelkeznek, amely engedélyezi a kimenő hívások számára a visszavont tanúsítványok listájának (CRL) letöltését, engedélyeznie kell a CRL-eket és az OCSP URL-címeket. A módosítással és a CRL-ekkel, valamint az OCSP URL-címekkel kapcsolatos részletes információkért lásd: az  [Azure TLS-tanúsítvány változásai](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1,0, TLS 1,1 és 3DES elavulttá US Gov-felhőben

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Minden Azure AD-alkalmazás  
A **termék képességei:** Szabványok
 
A Azure Active Directory a következő protokollok elavulttá válik a 2021. március 31-ig:
- TLS 1.0
- TLS 1.1
- 3DES titkosítási csomag (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Az összes ügyfél-kiszolgáló és böngésző-kiszolgáló kombinációnak a TLS 1,2 és a modern titkosítási csomagokat kell használnia az Azure-hoz, az Office 365-hoz és a Microsoft 365-szolgáltatásokhoz Azure Active Directoryhoz való biztonságos kapcsolódás fenntartásához.

Az érintett környezetek a következők:
- Azure-US Gov
- [Office 365 GCC High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Alkalmazások kiosztása szerepkörökhöz az AU és az objektum hatókörében

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
Ez a funkció lehetővé teszi, hogy az alkalmazás (SPN) rendszergazdai szerepkörhöz rendeljen a felügyeleti egység hatókörén. További információért lásd: [hatókörrel rendelkező szerepkörök társítása egy felügyeleti egységhez](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Mostantól letilthatja és törölheti a vendég felhasználókat, amikor nem férhetnek hozzá az erőforrásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Identitás-szabályozás
 
A Letiltás és törlés az Azure AD hozzáférési felülvizsgálatok speciális vezérlése, amely segít a szervezeteknek a csoportok és alkalmazások külső vendégeit jobban kezelni. Ha a vendégek megtagadják a hozzáférési felülvizsgálatot, a **Letiltás és törlés** funkció automatikusan letiltja a bejelentkezést 30 napig. 30 nap elteltével a rendszer teljesen eltávolítja a bérlőből.

További információ erről a szolgáltatásról: [külső identitások letiltása és törlése az Azure ad hozzáférési felülvizsgálatokkal](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>A hozzáférés-felülvizsgálati létrehozói egyéni üzeneteket adhatnak az e-mailekben a felülvizsgálók számára

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Identitás-szabályozás
 
Az Azure AD hozzáférési felülvizsgálatok szolgáltatásban az értékeléseket létrehozó rendszergazdák mostantól egyéni üzenetet írhatnak a véleményezők számára. A felülvizsgálók látni fogják a kapott e-mailben szereplő üzenetet, amely a felülvizsgálat befejezését kéri. Ha többet szeretne megtudni a funkció használatáról, tekintse meg az [egy vagy több hozzáférési felülvizsgálat létrehozása](../governance/create-access-review.md#create-one-or-more-access-reviews) című szakasz 14. lépését.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – október 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Globális Relay-identitás szinkronizálása](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](../app-provisioning/user-provisioning.md)használatával című témakört.
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Azure AD B2C integrációs asszisztense

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
Az Integration Assistant (előzetes verzió) felhasználói felülete mostantól Azure AD B2C Alkalmazásregisztrációk számára elérhető. Ez a megoldás segítséget nyújt az alkalmazás gyakori forgatókönyvekhez való konfigurálásában. További információ a [Microsoft Identity platform ajánlott eljárásairól és javaslatairól](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Szerepkör-sablon AZONOSÍTÓjának megtekintése Azure Portal felhasználói felületen

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure-szerepkörök  
A **termék képességei:** Access Control
 

Most már megtekintheti az egyes Azure AD-szerepkörökhöz tartozó sablon AZONOSÍTÓját a Azure Portal. Az Azure AD-ben válassza a kiválasztott szerepkör  **leírását** . 

Javasoljuk, hogy az ügyfelek a megjelenítendő név helyett a szerepkör-sablon azonosítóit használják a PowerShell-parancsfájlban és a kódban. A szerepkör-sablon azonosítója [directoryRoles](/graph/api/resources/directoryrole) és [definíciós](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) objektumokhoz használható. További információ a szerepkör-sablon azonosítóinak használatáról: [Azure ad beépített szerepkörök](../roles/permissions-reference.md).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>A Azure AD B2C regisztrációs felhasználói folyamatok API-összekötői mostantól nyilvános előzetes verzióban érhetők el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 

Az API-összekötők most már használhatók a Azure Active Directory B2C. Az API-összekötők lehetővé teszik a webes API-k használatát a regisztrációs felhasználói folyamatok testre szabására és a külső felhőalapú rendszerekkel való integrációra. Az API-összekötők a következőket vehetik igénybe:

- Integrálás egyéni jóváhagyási munkafolyamatokkal
- Felhasználói bemeneti adatok érvényesítése
- Felhasználói attribútumok felülírása 
- Egyéni üzleti logika futtatása 

 További információkért tekintse meg az [API-összekötők használata a regisztrációs dokumentáció testreszabásához és bővítéséhez](../../active-directory-b2c/api-connectors-overview.md) című témakört.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>A jogosultságok kezeléséhez tartozó csatlakoztatott szervezetek State tulajdonsága

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Címtárszolgáltatás- **képesség:** jogosultságok kezelése
 

 Az összes csatlakoztatott szervezet már rendelkezik egy "State" nevű további tulajdonsággal. Az állapot határozza meg, hogyan használja a rendszer a csatlakoztatott szervezetet az "összes konfigurált csatlakoztatott szervezetre" vonatkozó házirendekben. Az érték "konfigurálva" (ami azt jelenti, hogy a szervezet az "all" záradékot használó szabályzatok hatókörében van) vagy "javasolt" (ami azt jelenti, hogy a szervezet nincs a hatókörben).  

A csatlakoztatott szervezetek manuálisan létrehozott alapértelmezett beállítása a "configured". Eközben az automatikusan létrehozott (szabályzatok alapján létrehozott), amelyek lehetővé teszik bármely felhasználó számára, hogy hozzáférést kérjenek, alapértelmezés szerint "javasolt" lesz.  A 9 2020 szeptember előtt létrehozott csatlakoztatott szervezetek beállítása "konfigurálva" lesz. A rendszergazdák szükség szerint frissíthetik ezt a tulajdonságot. [További információ](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory külső identitások mostantól prémium szintű, speciális biztonsági beállításokkal rendelkeznek a B2C-hez

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
A kockázatalapú feltételes hozzáférés és a kockázatkezelési funkciók mostantól elérhetők [Azure ad B2Cban](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Ezekkel a speciális biztonsági funkciókkal az ügyfelek mostantól a következőket tehetik:
- Intelligens elemzéseket használhat a B2C-alkalmazások és a végfelhasználói fiókok kockázatának felméréséhez. Az észlelések közé tartoznak az atipikus utazások, a névtelen IP-címek, a kártevők által hivatkozott IP-címek és az Azure AD veszélyforrások felderítése. A portál és az API-alapú jelentések is elérhetők.
- Az adaptív hitelesítési házirendek a B2C-felhasználók számára történő konfigurálásával automatikusan kezeli a kockázatokat. Az alkalmazások fejlesztői és rendszergazdái a többtényezős hitelesítés (MFA) vagy a hozzáférés blokkolásának a felhasználói kockázati szinttől függően történő megkövetelésével csökkenthetik a valós idejű kockázatokat, és a hely, a csoport és az alkalmazás alapján további vezérlők is elérhetők.
- Integrálható Azure AD B2C felhasználói folyamatokkal és egyéni házirendekkel. A feltételek beindíthatók a Azure AD B2C beépített felhasználói folyamataiból, vagy beépíthető a B2C egyéni házirendjeibe. A B2C felhasználói folyamat más szempontjaihoz hasonlóan a végfelhasználói élmények is testreszabhatók. A Testreszabás a szervezet hang-, márka-és enyhítő alternatívái alapján történik.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – október 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2020 októberében a következő 27 új alkalmazást bővítettük az alkalmazás-galériában az összevonási támogatással:

[Sentry](../saas-apps/sentry-tutorial.md), [darázs – termelékenység superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys felhőalapú integráció az Azure](https://apps.mypurecloud.com/msteams-integration/)-hoz, a [Zone Technologies portál](https://portail.zonetechnologie.com/signin), a [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), a [Datawiza Access Broker](https://console.datawiza.com/), a [](../saas-apps/samsara-tutorial.md) [ZOKRI](https://app.zokri.com/), [](../saas-apps/hoxhunt-tutorial.md)a CheckProof, a Ecochallenge.org, a atSpoke [](../saas-apps/mevisio-tutorial.md) [](../saas-apps/retail-zipline-tutorial.md) [, a](http://atspoke.com/login) [találkozó emlékeztetője](https://app.appointmentreminder.co.nz/account/login), a [felhő. piac](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md) [,](https://hrm.workstem.com/login) [üdvözlés](https://app.greetly.com/), [](../saas-apps/starmind-tutorial.md) [OrgVitality egyszeri bejelentkezés](../saas-apps/orgvitality-sso-tutorial.md), [webes rakomány](../saas-apps/web-cargo-air-tutorial.md) [, Starmind](../saas-apps/loop-flow-crm-tutorial.md) [,](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md) Workstem [Traffic Manager](../saas-apps/nimbus-tutorial.md) [](../saas-apps/checkproof-tutorial.md) [](https://events.ecochallenge.org/users/login)

Itt megtalálhatja az összes alkalmazás dokumentációját is https://aka.ms/AppsTutorial

Az alkalmazás az Azure AD-katalógusban való listázásához olvassa el a részleteket itt: https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>A naplók üzembe helyezése mostantól a log Analytics szolgáltatásba is továbbítható

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 

Tegye közzé a kiépítési naplókat a log Analytics szolgáltatásban a következő sorrendben:
- Több mint 30 napig tárolja a kiépítési naplókat
- Egyéni riasztások és értesítések definiálása
- Irányítópultok létrehozása a naplók megjelenítéséhez
- Összetett lekérdezések végrehajtása a naplók elemzéséhez 

A szolgáltatás használatának megismeréséhez lásd: a [kiépítés integrálása Azure monitor naplókkal](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>A naplók kiépítési naplóit mostantól az alkalmazások tulajdonosai tekinthetik meg

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 
Mostantól engedélyezheti, hogy az alkalmazások tulajdonosai nyomon kövessék a tevékenységeket a kiépítési szolgáltatással, és hibaelhárítást végezzenek anélkül, hogy emelt szintű szerepkört kellene benyújtaniuk, vagy szűk keresztmetszetet kellene [További információ](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>10 Azure Active Directory szerepkör átnevezése

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Azure-szerepkörök  
A **termék képességei:** Access Control
 
Néhány Azure Active Directory (AD) beépített szerepkör olyan névvel rendelkezik, amely különbözik a Microsoft 365 felügyeleti központban, az Azure AD-Portálon és a Microsoft Graphban megjelenő nevektől. Ez az inkonzisztencia problémákat okozhat az automatizált folyamatokban. Ezzel a frissítéssel a rendszer 10 szerepkör-nevet átnevez, hogy azok konzisztensek legyenek. Az új szerepkörök nevei a következő táblázatban láthatók:

![Az MS Graph APIban és a Azure Portalban található szerepkörök nevei, valamint a M365 felügyeleti központban, a Azure Portal és az API-ban javasolt új szerepkör neve.](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C támogatás a MSAL JS 2. x használatával a fürdők hitelesítési kódjának folyamatához

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
A MSAL.js 2. x verziója mostantól támogatja az egyoldalas webalkalmazások (Gyógyfürdők) engedélyezési kódjának áramlását. A Azure AD B2C mostantól támogatja a SPA-alkalmazás típusának használatát a Azure Portal és a MSAL.js engedélyezési kód PKCE való használatát egyoldalas alkalmazásokhoz. Ez lehetővé teszi a fürdők számára, hogy a Azure AD B2C használatával megőrizze az SSO-t újabb böngészőkkel, és betartja a hitelesítési protokollok újabb javaslatait. Ismerkedjen meg az [egyoldalas alkalmazások (Spa) regisztrálásával Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) oktatóanyagban.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>A (z) Multi-Factor Authentication (MFA) frissítési megjegyzése egy megbízható eszköz beállításainál

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme
 

Nemrég frissítettük a [Remember multi-Factor Authentication (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) szolgáltatást egy megbízható eszköz-szolgáltatáson a hitelesítés legfeljebb 365 napig való meghosszabbításához. A Azure Active Directory (Azure AD) prémium szintű licencek a [feltételes hozzáférés – bejelentkezési gyakorisági szabályzatot](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) is használhatják, amely nagyobb rugalmasságot biztosít az újrahitelesítési beállításokhoz.

Az optimális felhasználói élmény érdekében javasoljuk, hogy a feltételes hozzáférés bejelentkezési gyakoriságának használatával terjessze ki a munkamenetek élettartamát a megbízható eszközökön, helyszíneken vagy alacsony kockázatú munkamenetekben a megbízható eszközre vonatkozó, a többtényezős hitelesítés helyett. Első lépésként tekintse át a [legújabb útmutatót az újrahitelesítés élményének optimalizálásához](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---

## <a name="september-2020"></a>2020. szeptember

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – szeptember 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [CODA](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [A Webroot biztonsági ismertetése](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](../app-provisioning/user-provisioning.md)használatával című témakört.
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>A Cloud kiépítés nyilvános előzetes verziójának frissítése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD-beli felhőalapú kiépítési **termék funkció:** identitás-életciklus kezelése
 
Azure AD Connect Cloud kiépítés nyilvános előzetes verziójának frissítése két fő fejlesztést fejlesztett ki az ügyfelek visszajelzései közül: 

- Attribútum-hozzárendelési élmény Azure Portal

    Ezzel a funkcióval a rendszergazdák leképezhetők a felhasználó-, csoport-vagy kapcsolattartási attribútumok az AD-ből az Azure AD-be a mai napon elérhető különböző leképezési típusok használatával. Az attribútum-hozzárendelés egy olyan szolgáltatás, amely a Active Directorytól a Azure Active Directoryig áramló attribútumok értékeinek egységesítésére szolgál. Megadhatja, hogy az attribútum értéke közvetlenül az AD-ből az Azure AD-ba kerüljön-e, vagy kifejezések használatával alakítsa át az attribútum értékeit a felhasználók kiépítés során. [További információ](../cloud-sync/how-to-attribute-mapping.md)

- Igény szerinti kiépítési vagy tesztelési felhasználói élmény

    Ha beállította a konfigurációt, érdemes lehet tesztelni, hogy a felhasználói átalakítás a várt módon működik-e, mielőtt az összes felhasználóra alkalmazná a hatókört. Igény szerinti kiépítés esetén a rendszergazdák megadhatják az AD-felhasználók megkülönböztető nevét (DN), és megtekinthetik, hogy a várt módon vannak-e szinkronizálva. Az igény szerinti kiépítés nagyszerű módot biztosít arra, hogy az attribútum-hozzárendelések a várt módon működjenek. [További információ](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>BitLocker-helyreállítás naplózása az Azure AD-ben – nyilvános előzetes verzió

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Eszköz-hozzáférés kezelése  
A **termék képességei:** Eszközök életciklusának kezelése
 
Ha a rendszergazdák vagy a végfelhasználók beolvasják a BitLocker helyreállítási kulcs (oka) t, Azure Active Directory most létrehoz egy naplót, amely rögzíti a helyreállítási kulcs elérését. Ugyanez a naplózás részletesen ismerteti az eszközt, amelyhez a BitLocker-kulcs hozzá volt rendelve.

A végfelhasználók a fiókomon [keresztül érhetik el a helyreállítási kulcsaikat](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). A rendszergazdák a [BitLocker helyreállítási kulcs API-](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) n keresztül érhetik el a helyreállítási kulcsokat a bétaverzióban, vagy az Azure ad portálon keresztül. További információ: [BitLocker-kulcsok megtekintése vagy másolása az Azure ad-portálon](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>A Teams-eszközök rendszergazdai beépített szerepköre

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
A [Teams-eszközök rendszergazdai](../roles/permissions-reference.md#teams-devices-administrator) szerepkörrel rendelkező felhasználók kezelhetik a csapatok [által hitelesített eszközöket](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) a csapatok felügyeleti központjából. 

Ez a szerepkör lehetővé teszi, hogy a felhasználó egyetlen pillantással megtekintse az összes eszközt, és képes legyen az eszközök keresésére és szűrésére. A felhasználó megtekintheti az egyes eszközök részleteit is, beleértve a bejelentkezett fiókot, valamint az eszköz gyártmányát és modelljét. A felhasználó módosíthatja az eszköz beállításait, és frissítheti a szoftver verzióit. Ez a szerepkör nem biztosít engedélyeket a csapatok ellenőrzéséhez és az eszköz minőségének meghívásához.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>A címtár-objektumok speciális lekérdezési képességei

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** MS Graph  
A **termék képességei:** Fejlesztői élmény
 
Az Azure AD API-k címtár-objektumaihoz bevezetett új lekérdezési képességek mostantól elérhetők a 1.0-s végponton és a termelésre készen. A fejlesztők a szabványos OData operátorok segítségével megszámíthatják, megkereshetik, szűrhetik és rendezhetik a címtár-objektumokat és a kapcsolódó hivatkozásokat.

További információkért tekintse meg [a dokumentációt](https://aka.ms/BlogPostMezzoGA), és visszajelzést is küldhet erről a [rövid kérdőívről](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Nyilvános előzetes verzió: a feltételes hozzáférési szabályzatokat konfigurált bérlők folyamatos hozzáférésének kiértékelése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Identitás-biztonsági & védelme
 
A folyamatos hozzáférés kiértékelése (CAE) mostantól elérhető nyilvános előzetes verzióban az Azure AD-bérlők számára feltételes hozzáférési szabályzatokkal. A CAE a kritikus fontosságú biztonsági eseményeket és szabályzatokat valós időben értékeli ki. Ide tartozik a fiókok letiltása, a jelszó alaphelyzetbe állítása és a hely módosítása. További információ: [folyamatos hozzáférés kiértékelése](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Nyilvános előzetes verzió: a jóváhagyási döntések javítása érdekében a hozzáférési csomagot kérő felhasználók további kérdéseket tehetnek fel

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés kezelése  
A **termék képességei:** Jogosultságok kezelése
 
A rendszergazdák mostantól megkövetelhetik, hogy a hozzáférési csomagot kérő felhasználók további kérdéseket válaszoljon meg az Azure AD-jogosultságok felügyeletének saját hozzáférési portálján. A felhasználók válaszai ezután megjelennek a jóváhagyók számára, hogy könnyebben meglegyenek a hozzáférés-jóváhagyási döntések. További tudnivalókért tekintse meg [a további kérelmező adatainak összegyűjtése jóváhagyásra (előzetes verzió)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)című témakört.
 
---

### <a name="public-preview-enhanced-user-management"></a>Nyilvános előzetes verzió: továbbfejlesztett felhasználói felügyelet

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Felhasználói felügyelet  
A **termék képességei:** Felhasználói felügyelet
 

Az Azure AD-portál frissült, így könnyebben megtalálhatja a felhasználókat a minden felhasználó és a törölt felhasználók oldalain. Az előzetes verzió változásai a következők: 
- További látható felhasználói tulajdonságok, például az objektumazonosító, a címtár-szinkronizálás állapota, a létrehozás típusa és az identitás kiállítója.
- A keresés mostantól lehetővé teszi a nevek, e-mailek és objektumazonosítók együttes keresését.
- Továbbfejlesztett szűrés felhasználói típus szerint (tag, vendég és nincs), címtár-szinkronizálási állapot, létrehozás típusa, cégnév és tartománynév.
- Új rendezési képességek olyan tulajdonságokhoz, mint a név, a felhasználó egyszerű neve és a törlés dátuma.
- Az új felhasználók száma minden kereséssel vagy szűrőkkel együtt frissül.

További információkért lásd: [a felhasználói felügyeleti fejlesztések (előzetes verzió) a Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Új megjegyzések mező vállalati alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások **termék képesség:** SSO

A vállalati alkalmazásokhoz ingyenes szöveges megjegyzéseket is hozzáadhat. Bármilyen releváns információt hozzáadhat, amely segítséget nyújt a vállalati alkalmazások kezelő alkalmazásai számára. További információ: gyors útmutató [: alkalmazás tulajdonságainak konfigurálása a Azure Active Directory (Azure ad) bérlőben](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – szeptember 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

Szeptember 2020-én a következő 34 új alkalmazásokat bővítettük az alkalmazás-galériában az összevonási támogatással:

[VMware Horizon-Unified Access Gateway](), [Pulse Secure PC-k](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [gender fitness](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [nyelvtanilag](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [mobil zárolás](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md) [, CloudCADI,](https://app.cloudcadi.com/login)Simfoni- [elemzés](https://simfonianalytics.com/accounts/microsoft/login/), [Priva Identity & hozzáférés-kezelés](https://my.priva.com/), [nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md), [biztonságos aláírás Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [biztonságos aláírás Enterprise Portal HRE telepítő](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [WISTEC online](https://wisteconline.com/auth/oidc), [Oracle PeopleSoft által védett F5 Big-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

Az összes alkalmazás dokumentációját itt tekintheti meg: https://aka.ms/AppsTutorial .

Az alkalmazás az Azure AD-katalógusban való listázásához olvassa el a következő részleteket: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Új delegálási szerepkör az Azure AD-jogosultságok kezelésében: hozzáférés a csomag hozzárendelés-kezelőjéhez

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés kezelése  
A **termék képességei:** Jogosultságok kezelése
 
Új hozzáférési csomag hozzárendelés-kezelői szerepkör lett hozzáadva az Azure AD-jogosultságok kezelése szolgáltatásban a hozzárendelések kezeléséhez szükséges részletes engedélyek biztosításához. Most már delegálhatja a feladatokat a szerepkör egyik felhasználója számára, aki egy hozzáférési csomag hozzárendelés-kezelését egy üzleti tulajdonosnak delegálhatja. A hozzáférési csomag hozzárendelés-kezelője azonban nem tudja módosítani a hozzáférési csomag szabályzatait vagy a rendszergazdák által beállított egyéb tulajdonságokat. 

Ezzel az új szerepkörrel kihasználhatja a hozzárendelések felügyeletének delegálásához és a felügyeleti ellenőrzés megtartásához szükséges legalacsonyabb jogosultságokat az összes többi hozzáférési csomag konfigurációjában. További információ: [jogosultságok felügyeleti szerepkörei](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Privileged Identity Management a bevezetési folyamat változásai

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Korábban a Privileged Identity Management (PIM) megkövetelt felhasználói engedély bevezetését és a PIM paneljén bevezetési folyamatot, amely tartalmazza az Azure AD MFA-ban való regisztrálást. A PIM-élmény az Azure AD szerepköreibe és a rendszergazdák paneljére való legutóbbi integrálásával eltávolítjuk ezt a folyamatot. Minden érvényes P2 licenccel rendelkező bérlő automatikusan bekerül a PIM-be.

A PIM-be való bevezetésnek nincs közvetlen káros hatása a bérlőre. A következő módosításokat várhatja el:
- További hozzárendelési beállítások, mint például az aktív és a befejezési időpont, ha a hozzárendelést a PIM vagy az Azure AD szerepkörei és a rendszergazdák panelen végzi el. 
- A további hatókör-mechanizmusok, például a felügyeleti egységek és az egyéni szerepkörök közvetlenül a hozzárendelési élményben jelennek meg. 
- Ha Ön globális rendszergazda vagy Kiemelt szerepkörű rendszergazda, elindíthat néhány további e-mailt, például a PIM heti kivonatát. 
- Előfordulhat, hogy az MS-PIM szolgáltatásnév is megjelenik a szerepkör-hozzárendeléshez kapcsolódó naplóban. Ez a várt változás nem befolyásolja a normál munkafolyamatot.

 További információ: a [Privileged Identity Management használatának megkezdése](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD-jogosultságok kezelése: a hozzáférési csomag erőforrásai közül a Select (kiválasztás) ablaktábla mostantól alapértelmezés szerint a kiválasztott katalógusban aktuálisan megjelenő erőforrásokat jeleníti meg.

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés kezelése  
A **termék képességei:** Jogosultságok kezelése
 

A hozzáférési csomag létrehozási folyamata alatt az erőforrás-szerepkörök lapon a kiválasztás ablaktábla viselkedése változik. Jelenleg az alapértelmezett viselkedés a kiválasztott katalógushoz hozzáadott felhasználó és erőforrások tulajdonában lévő összes erőforrás megjelenítése. 

Ez a felhasználói élmény úgy módosul, hogy csak a katalógusban jelenleg hozzáadott erőforrások jelenjenek meg alapértelmezés szerint, így a felhasználók könnyedén választhatják ki az erőforrásokat a katalógusból. A frissítés segítséget nyújt a csomagok eléréséhez hozzáadandó erőforrások felfedezésében, valamint a nem a katalógus részét képező felhasználó tulajdonában lévő erőforrások véletlen hozzáadásának kockázatának csökkentésében. További információk: [új hozzáférési csomag létrehozása az Azure ad-jogosultságok kezelésében](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---