---
title: Újdonságok Kibocsátási megjegyzések – Azure Active Directory | Microsoft Docs
description: Ismerje meg a Azure Active Directory; például a legújabb kibocsátási megjegyzéseket, az ismert problémákat, a hibajavításokat, az elavult funkciókat és a közelgő módosításokat.
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
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a909d946b55ee8b06d68aa8bee53bc50d2190e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532303"
---
# <a name="whats-new-in-azure-active-directory"></a>Újdonságok a Azure Active Directory?

>Az RSS-hírcsatorna olvasó ikonolvasójának bemásolásával és beillesztésével értesítést kap arról, hogy mikor kell újra az oldalra frissítéseket `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` ![ ](./media/whats-new/feed-icon-16x16.png) keresve.

Az Azure AD folyamatosan kap fejlesztéseket. Annak érdekében, hogy naprakész maradjon a legújabb fejlesztésekkel kapcsolatban, ez a cikk a következő információkkal rendelkezik:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Terv a változásokhoz

Ez az oldal havonta frissül, ezért rendszeresen vissza kell látogatni. Ha hat hónapnál régebbi elemeket keres, az Archívumban találhatja meg őket az Újdonságok a [Azure Active Directory.](whats-new-archive.md)

---

## <a name="march-2021"></a>2021. március

### <a name="guidance-on-how-to-enable-support-for-tls-12-in-your-environment-in-preparation-for-upcoming-azure-ad-tls-1011-deprecation"></a>Útmutató a TLS 1.2-támogatás engedélyezéséhez a környezetben az Azure AD TLS 1.0/1.1 hamarosan elalasztása előtt

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** N/a  
**Termékképesség:** Szabványok

Azure Active Directory 2021. június 30-Azure Active Directory kezdve a következő protokollok elavultak lesznek a globális régiókban:


- TLS 1.0
- TLS 1.1
- 3DES-rejtjelcsomag (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Az érintett környezetek a következők:

- Azure Commercial Cloud
- Office 365 GCC és WW

További útmutatásért lásd: [Enable support for TLS 1.2 in your environment for Azure AD TLS 1.1 and 1.0 deprecation (A TLS 1.2](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)támogatásának engedélyezése a környezetben az Azure AD TLS 1.1 és 1.0 elalasztása esetén).

---

### <a name="public-preview---azure-ad-entitlement-management-now-supports-multi-geo-sharepoint-online"></a>Nyilvános előzetes verzió – Az Azure AD jogosultságkezelése mostantól támogatja a több földrajzi helyből álló SharePoint Online-t

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Más  
**Termékképesség:** Jogosultságkezelés
 
A multi-geo SharePoint Online-t használó szervezetek mostantól adott multi-geo környezetből származó webhelyeket is tartalmazhatnak a jogosultságkezelési hozzáférési csomagokba. [További információ](../governance/entitlement-management-catalog-create.md#add-a-multi-geo-sharepoint-site-preview).

---

### <a name="public-preview---restore-deleted-apps-from-app-registrations"></a>Nyilvános előzetes verzió – Törölt alkalmazások visszaállítása a Alkalmazásregisztrációk

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Más  
**Termékképesség:** Fejlesztői élmény
 
Az ügyfelek mostantól megtekinthetik, visszaállíthatja és véglegesen eltávolíthatjak a törölt alkalmazásregisztrációkat a Azure Portal. Ez csak a címtárhoz társított alkalmazásokra vonatkozik, a személyes címtárakból származó Microsoft-fiók. [További információ](../develop/quickstart-restore-app.md).
 
---

### <a name="public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices"></a>Nyilvános előzetes verzió – Új "Felhasználói művelet" a feltételes hozzáférésben az eszközök regisztrálásához vagy beléptetéséhez

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Feltételes hozzáférés  
**Termékképesség:** Identity Security & Protection
 
 A feltételes hozzáférésben elérhető egy "Eszközök regisztrálása vagy beléptethető" nevű új felhasználói művelet. Ez a felhasználói művelet lehetővé teszi a többtényezős hitelesítési (MFA) szabályzatok szabályozását az Azure AD-eszközregisztrációhoz. 

Ez a felhasználói művelet jelenleg csak akkor teszi lehetővé az MFA engedélyezését vezérlőként, amikor a felhasználók eszközöket regisztrálnak vagy csatlakoznak az Azure AD-hez. Ez a felhasználói művelet letiltja az Azure AD-eszközregisztrációtól függő vagy az arra nem alkalmazható egyéb vezérlőket. [További információ](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions). 
 
---

### <a name="public-preview---optimize-connector-groups-to-use-the-closest-application-proxy-cloud-service"></a>Nyilvános előzetes verzió – Összekötőcsoportok optimalizálása a felhőszolgáltatás alkalmazásproxy használatára

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazásproxy  
**Termékképesség:** Access Control
 
Ezzel az új képességgel az összekötőcsoportok az alkalmazás által üzemeltetett alkalmazásproxy legközelebbi régióhoz rendelhetők. Ez javíthatja az alkalmazások teljesítményét olyan esetekben, amikor az alkalmazásokat nem az otthoni bérlő régiójában üzemeltetik. [További információ](../manage-apps/application-proxy-network-topology.md#optimize-connector-groups-to-use-closest-application-proxy-cloud-service-preview). 
 
---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-email-one-time-passcode-accounts"></a>Nyilvános előzetes verzió – External Identities Self-Service regisztráció az AAD-be e-mailes One-Time PIN-kód-fiókok használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C

A külső felhasználók mostantól e-mailes pin-One-Time használhatnak az Azure AD 1. féltől származó és LOB-alkalmazásokba való regisztrációhoz. [További információ](../external-identities/one-time-passcode.md).

---

### <a name="public-preview---availability-of-ad-fs-sign-ins-in-azure-ad"></a>Nyilvános előzetes verzió – A AD FS Sign-Ins rendelkezésre állása az Azure AD-ban

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Jelentéskészítés & monitorozása
 
AD FS bejelentkezési tevékenység mostantól integrálható az Azure AD tevékenységjelentési szolgáltatásával, és egységes nézetet biztosít a hibrid identitás-infrastruktúra számára. Az Azure AD Sign-Ins-jelentés, a Log Analytics és az Azure Monitor-munkafüzetek használatával részletes elemzéseket végezhet az AAD-hez és az AD FS bejelentkezési forgatókönyvekhez is, például AD FS fiókzárolások, a rossz jelszóra tett kísérletek és a váratlan bejelentkezési kísérletek kiugróan magas számaihoz.

További tudnivalókért látogasson el az [Azure AD-AD FS](../hybrid/how-to-connect-health-ad-fs-sign-in.md)a Connect Health használatával.

---

### <a name="general-availability---staged-rollout-to-cloud-authentication"></a>Általános rendelkezésre állás – Szakaszos bevezetés a felhőalapú hitelesítésbe

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** AD Connect  
**Termékképesség:** Felhasználóhitelesítés
 
Általánosan elérhető a felhőalapú hitelesítésre való szakaszos bevezetés. Az szakaszos bevezetési funkció lehetővé teszi a felhőalapú hitelesítési módszerekkel, például az áteresztett hitelesítéssel (PTA) vagy a jelszó-kivonatszinkronizálással (PHS) használó felhasználók csoportjainak szelektív tesztelését. Eközben az összevont tartományokban található összes többi felhasználó továbbra is összevonási szolgáltatásokat használ, például AD FS vagy bármely más összevonási szolgáltatást a felhasználók hitelesítéséhez. [További információ](../hybrid/how-to-connect-staged-rollout.md).

---

### <a name="general-availability---user-type-attribute-can-now-be-updated-in-the-azure-admin-portal"></a>Általános rendelkezésre állás – A felhasználótípus attribútum mostantól frissíthető az Azure felügyeleti portálon

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Felhasználói élmény és felügyelet  
**Termékképesség:** Felhasználókezelés
 
Az ügyfelek mostantól frissítheti az Azure AD-felhasználók felhasználótípusát, amikor frissítik a felhasználói profiljuk adatait az Azure felügyeleti portálról. A felhasználó típusa a Microsoft Graph is frissíthető. További információ: [Felhasználói profil információinak hozzáadása vagy frissítése.](active-directory-users-profile-azure-portal.md)
 
---

### <a name="general-availability---replica-sets-for-azure-active-directory-domain-services"></a>Általános rendelkezésre állás – Replikakészletek a Azure Active Directory Domain Services

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Azure AD Domain Services  
**Termékképesség:** Azure AD Domain Services
 
A replikakészletek a Azure AD DS általánosan elérhetők. [További információ](https://docs.microsoft.com/azure/active-directory-domain-services/concepts-replica-sets).
 
---

### <a name="general-availability---collaborate-with-your-partners-using-email-one-time-passcode-in-the-azure-government-cloud"></a>Általánosan elérhető – Együttműködés a partnerekkel e-mailes One-Time PIN-kód használatával a Azure Government felhőben

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 
A felhőszolgáltatásban Microsoft Azure Government szervezetek mostantól lehetővé tehetik a vendégek számára, hogy meghívókat beválthassanak e-mailes One-Time PIN-kódjukkal. Ez biztosítja, hogy a Azure Government-felhőben azure AD-, Microsoft- vagy Gmail-fiókkal nem rendelkező vendégfelhasználók továbbra is együttműködhetnek a partnerekkel egy ideiglenes kód kérésével és megadásával a megosztott erőforrásokba való bejelentkezéshez. [További információ](../external-identities/one-time-passcode.md#note-for-azure-us-government-customers).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---march-2021"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2021. március

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Külső fél integrációja
 
2021 márciusában a következő 37 új alkalmazást adtunk hozzá az alkalmazásgyűjteményhez összevonási támogatással:

[Bambuser Live Video Shopping](https://lcx.bambuser.com/), [DeepDyve Inc](https://www.deepdyve.com/azure-sso), [Moqups,](../saas-apps/moqups-tutorial.md) [FLIPH Spaces Mobile](https://ricohspaces.app/welcome), [Flipgrid,](https://auth.flipgrid.com/) [hCaptcha Enterprise,](../saas-apps/hcaptcha-enterprise-tutorial.md) [SchoolStream ASA](https://jsd.schoolstreamk12.com/ASA/ASAlogin.aspx), [TransPerfect GlobalLink Dashboard](../saas-apps/transperfect-globallink-dashboard-tutorial.md), [SimplificaCI](https://app.simplificaci.com.br/), [Thrive LXP](../saas-apps/thrive-lxp-tutorial.md), [Lexonis Talent Lép](../saas-apps/lexonis-talentscape-tutorial.md), [Exium,](../saas-apps/exium-tutorial.md) [Sapient](../saas-apps/sapient-tutorial.md), [TrueChoice](../saas-apps/truechoice-tutorial.md), [ARRAH Spaces](https://ricohspaces.app/welcome), [Saba Cloud](../saas-apps/learning-at-work-tutorial.md), [Afületix 360](../saas-apps/acunetix-360-tutorial.md), [Exceed.ai](../saas-apps/exceed-ai-tutorial.md), [GitHub](../saas-apps/github-enterprise-managed-user-tutorial.md)Enterprise Managed User , [Enterprise Vault.cloud for Outlook](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=openid%20profile%20User.Read&client_id=7176efe5-e954-4aed-b5c8-f5c85a980d3a&nonce=4b9e1981-1bcb-4938-a283-86f6931dc8cb), [Smartlook](../saas-apps/smartlook-tutorial.md), [Accenture Academy](../saas-apps/accenture-academy-tutorial.md), [Onshape](../saas-apps/onshape-tutorial.md), [Tradeshift](../saas-apps/tradeshift-tutorial.md), [JuriBlox](../saas-apps/juriblox-tutorial.md), [SecurityStudio](../saas-apps/securitystudio-tutorial.md), [ClicData](https://app.clicdata.com/), [Evergreen](../saas-apps/evergreen-tutorial.md), [Patchdeck](https://patchdeck.com/ad_auth/authenticate/), [FAX. PLUS,](../saas-apps/fax.plus-tutorial.md) [ValidSign,](../saas-apps/validsign-tutorial.md) [AWS Single Sign-on,](../saas-apps/aws-single-sign-on-tutorial.md) [Nura Space,](https://dashboard.nuraspace.com/login) [Broadcom DX SaaS,](../saas-apps/broadcom-dx-saas-tutorial.md) [Interplay Learning,](https://skilledtrades.interplaylearning.com/#login) [SendPro Enterprise,](../saas-apps/sendpro-enterprise-tutorial.md) [FortiSASE SIA](../saas-apps/fortisase-sia-tutorial.md)

Az összes alkalmazás dokumentációját itt is megtalálja: https://aka.ms/AppsTutorial

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásához olvassa el a részleteket itt: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---march-2021"></a>Új kiépítési összekötők az Azure AD Alkalmazáskatatárban – 2021. március

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Külső fél integrációja

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [AWS egyszeri bejelentkezés](../saas-apps/aws-single-sign-on-provisioning-tutorial.md)
- [Bpanda](../saas-apps/bpanda-provisioning-tutorial.md)
- [Britive](../saas-apps/britive-provisioning-tutorial.md)
- [GitHub Enterprise Managed User](../saas-apps/github-enterprise-managed-user-provisioning-tutorial.md)
- [Grammarly](../saas-apps/grammarly-provisioning-tutorial.md)
- [LogicGate](../saas-apps/logicgate-provisioning-tutorial.md)
- [SecureLogin](../saas-apps/secure-login-provisioning-tutorial.md)
- [TravelPerk](../saas-apps/travelperk-provisioning-tutorial.md)

A szervezet automatizált felhasználói fiókok automatikus kiépítése által történő biztonságossá tevésével kapcsolatos további információkért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val.](../app-provisioning/user-provisioning.md)
 
---

### <a name="introducing-ms-graph-api-for-company-branding"></a>Introducing MS Graph API for Company Branding

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** MS Graph  
**Termékképesség:** B2B/B2C

[A Graph API](https://docs.microsoft.com/graph/api/resources/organizationalbrandingproperties)  védjegyezéshez elérhető ms-Microsoft 365 az Azure AD-hez vagy a bejelentkezési élményhez, hogy lehetővé tegye a márkajelzési paraméterek programozott kezelését.

---

### <a name="general-availability---header-based-authentication-sso-with-application-proxy"></a>Általános rendelkezésre állás – Fejlécalapú hitelesítési SSO alkalmazásproxy

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Alkalmazásproxy  
**Termékképesség:** Access Control
 
Az Azure AD alkalmazásproxy fejlécalapú hitelesítés natív támogatása mostantól általánosan elérhető. Ezzel a funkcióval konfigurálhatja az alkalmazás HTTP-fejléceiként szükséges felhasználói attribútumokat anélkül, hogy további összetevőkre lenne szükség az üzembe helyezéshez. [További információ](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

---

### <a name="two-way-sms-for-mfa-server-is-no-longer-supported"></a>Az MFA-kiszolgálóra vonatkozó kétútos SMS-ek már nem támogatottak

**Írja be a következőt:** Elavult  
**Szolgáltatáskategória:** Mfa  
**Termékképesség:** Identity Security & Protection
 

Az MFA-kiszolgálóra vonatkozó kétes SMS-ek eredetileg 2018-ban elavultak, és 2021. február 24-től nem támogatottak. A rendszergazdáknak egy másik módszert kell engedélyezniük a még mindig kétútos SMS-t használó felhasználók számára.

A rendszer 2020. december 8-án és 2021. január 28-án elküldte az e-mail-értesítéseket és az Azure Portal Service Health értesítéseket az érintett rendszergazdáknak. A riasztások az előfizetések tulajdonosi, társtulajdonosi, rendszergazdai és szolgáltatás-rendszergazdai RBAC-szerepköréhez mentek. [További információ](../authentication/how-to-authentication-two-way-sms-unsupported.md).
 
---
 
## <a name="february-2021"></a>2021. február

### <a name="email-one-time-passcode-authentication-on-by-default-starting-october-2021"></a>Az e-mailes pin-kódos hitelesítés alapértelmezés szerint 2021 októbere óta be van kapcsolva

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 

2021. október 31-től Microsoft Azure Active Directory e-mailes, egyszeres [PIN-kódos](../external-identities/one-time-passcode.md) hitelesítés lesz a fiókok és bérlők B2B együttműködési forgatókönyvekben való meghívásának alapértelmezett módja. A Microsoft jelenleg nem engedélyezi a meghívók érvényesítését nem Azure Active Directory fiókok használatával. 

---

### <a name="unrequested-but-consented-permissions-will-no-longer-be-added-to-tokens-if-they-would-trigger-conditional-access"></a>A nem szükséges, de hozzájárulással rendelkező engedélyek a továbbiakban nem lesznek hozzáadva a jogkivonathoz, ha feltételes hozzáférést aktiválnak

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Platform
 
Jelenleg a dinamikus [engedélyeket](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent) használó alkalmazások minden olyan engedélyt kapnak, amelyekhez hozzáférést kapnak. Ez vonatkozik a nem lekért alkalmazásokra, és akkor is, ha feltételes hozzáférést aktiválnak. Ez például azt okozhatja, hogy egy alkalmazás, amely csak azt kéri, amely szintén rendelkezik hozzájárulással a -hez, kényszerítve lesz az engedélyhez rendelt feltételes `user.read` `files.read` `files.read` hozzáférésre. 

A szükségtelen feltételes hozzáférési kérések számának csökkentése érdekében az Azure AD megváltoztatja a nem szükséges hatókörök alkalmazásokhoz való rendelkezésre bocsátott módját. Az alkalmazások csak a kifejezetten kért engedélyekhez aktiválják a feltételes hozzáférést. További információ: [Újdonságok a hitelesítésben.](../develop/reference-breaking-changes.md#conditional-access-will-only-trigger-for-explicitly-requested-scopes)
 
---
 
### <a name="public-preview---use-a-temporary-access-pass-to-register-passwordless-credentials"></a>Nyilvános előzetes verzió – Jelszó nélküli hitelesítő ideiglenes hozzáférési kód regisztrálása jelszóval

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Mfa  
**Termékképesség:** Identity Security & Protection

ideiglenes hozzáférési kód egy időkorlikált PIN-kód, amely erős hitelesítő adatokként szolgál, és lehetővé teszi jelszó nélküli hitelesítő adatok és helyreállítás felvételét, ha egy felhasználó elveszett vagy elfelejtette az erős hitelesítési tényezőt (például FIDO2 biztonsági kulcs vagy Microsoft Authenticator), és be kell jelentkeznie az új erős hitelesítési módszerek regisztrálásához. [További információ](../authentication/howto-authentication-temporary-access-pass.md).

---

### <a name="public-preview---keep-me-signed-in-kmsi-in-next-generation-of-user-flows"></a>Nyilvános előzetes verzió – Bejelentkezve vagyok (KMSI) a felhasználói folyamatok következő generációjának

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** B2B/B2C

A B2C felhasználói folyamatok következő generációja mostantól támogatja a bejelentkezve maradás [(KMSI)](../../active-directory-b2c/session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) funkciót, amely lehetővé teszi az ügyfelek számára, hogy egy állandó cookie használatával hosszabbítsa meg a webes és natív alkalmazások felhasználóinak munkamenet-élettartamát.  A funkció akkor is aktívvá teszi a munkamenetet, amikor a felhasználó bezárja és újra megnyitja a böngészőt, és visszavonja, amikor a felhasználó bejelentkezik.

---

### <a name="public-preview---external-identities-self-service-sign-up-in-aad-using-msa-accounts"></a>Nyilvános előzetes verzió – External Identities Self-Service regisztráció az AAD-be MSA-fiókokkal

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 
A külső felhasználók mostantól Microsoft-fiókokkal is bejelentkeznek az Azure AD belső és LOB-alkalmazásba. [További információ](../external-identities/self-service-sign-up-overview.md).

---

### <a name="public-preview---reset-redemption-status-for-a-guest-user"></a>Nyilvános előzetes verzió – A vendégfelhasználó érvényesítési állapotának alaphelyzetbe állítása

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 
Az ügyfelek mostantól újraviheti a meglévő külső vendégfelhasználókat az érvényesítési állapot visszaállításához, így a vendégfelhasználói fiók a hozzáférés elvesztése nélkül maradhat. [További információ](../external-identities/reset-redemption-status.md).
 
---

### <a name="public-preview---synchronization-provisioning-apis-now-support-application-permissions"></a>Nyilvános előzetes verzió – A /synchronization (provisioning) API-k mostantól támogatják az alkalmazásengedélyeket

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése
 
Az ügyfelek mostantól az application.readwrite.ownedby alkalmazást is használhatjak alkalmazásengedélyként a szinkronizálási API-k meghívásához. Vegye figyelembe, hogy ez csak az Azure AD-ból külső alkalmazásokba (például AWS-be, Data Bricksbe stb.) történő kiépítéshez támogatott. Jelenleg nem támogatott a HR-kiépítés (Workday/ Successfactors) vagy a Cloud Sync (AD–Azure AD) esetén. [További információ](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true).
 
---

### <a name="general-availability---authentication-policy-administrator-built-in-role"></a>Általános rendelkezésre állás – Hitelesítési házirend-rendszergazda beépített szerepkör

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Rbac  
**Termékképesség:** Access Control
 
Az ezzel a szerepkörrel kapcsolatos felhasználók konfigurálják a hitelesítési módszerek házirendet, a bérlői szintű MFA-beállításokat és a jelszóvédelmi szabályzatot. Ez a szerepkör engedélyezi a jelszóvédelem beállításainak kezelését: intelligens zárolási konfigurációk és az egyéni tiltott jelszavak listájának frissítése. [További információ](../roles/permissions-reference.md#authentication-policy-administrator).

---

### <a name="general-availability---user-collections-on-my-apps-are-available-now"></a>Általánosan elérhető – A felhasználói gyűjtemények Saját alkalmazások már elérhetők!

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Saját alkalmazások  
**Termékképesség:** Végfelhasználói élmények
 
A felhasználók saját alkalmazáscsoportokat hozhatnak létre a Saját alkalmazások alkalmazásindítóján. Átrendezheti és elrejtheti a rendszergazda által velük megosztott gyűjteményeket. [További információ](../user-help/my-apps-portal-user-collections.md).

---

### <a name="general-availability---autofill-in-authenticator"></a>Általánosan elérhető – Automatikus kitöltés az Authenticatorban

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Microsoft Authenticator alkalmazás  
**Termékképesség:** Identity Security & Protection
 
Microsoft Authenticator többtényezős hitelesítést (MFA) és fiókkezelési képességeket biztosít, és mostantól automatikusan ki is fogja töltődni a jelszavakat a mobileszközökön (iOS és Android) a felhasználók által meglátogatott webhelyeken és alkalmazásokban. 

Ha automatikus kitöltést szeretne használni az Authenticatoron, a felhasználóknak hozzá kell adniuk személyes Microsoft-fiók az Authenticatorhoz, és a jelszavuk szinkronizálását kell használniuk. Munkahelyi vagy iskolai fiókok jelenleg nem használhatók jelszavak szinkronizálásra. [További információ](../user-help/user-help-auth-app-faq.md#autofill-for-it-admins).

---

### <a name="general-availability---invite-internal-users-to-b2b-collaboration"></a>Általános rendelkezésre állás – Belső felhasználók meghívása B2B-együttműködésbe

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 
Az ügyfelek mostantól meghívhat belső vendégeket a B2B-együttműködés használatára ahelyett, hogy meghívót küldenének egy meglévő belső fióknak. Ez lehetővé teszi, hogy az ügyfelek megtartsák a felhasználó objektumazonosítóját, upn-ját, csoporttagságát és alkalmazás-hozzárendelését. [További információ](../external-identities/invite-internal-users.md).

---

### <a name="general-availability---domain-name-administrator-built-in-role"></a>Általános rendelkezésre állás – A tartománynév-rendszergazda beépített szerepköre

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Rbac  
**Termékképesség:** Access Control
 
Az ezzel a szerepkört használó felhasználók kezelhetik (olvashatják, hozzáadhatják, ellenőrizhetik, frissítheti és törölhetik) a tartományneveket. A felhasználókkal, csoportokkal és alkalmazásokkal kapcsolatos címtárinformációkat is olvashatják, mivel ezek az objektumok tartományi függőségekkel is vannak. 

A helyszíni környezetek esetében az ezzel a szerepkörhöz tartozó felhasználók konfigurálni tudnak tartományneveket az összevonáshoz, hogy a társított felhasználók hitelesítése mindig a helyszínen történik. Ezek a felhasználók ezután egyszeri bejelentkezéssel bejelentkeznek az Azure AD-alapú szolgáltatásokba a helyszíni jelszavukat használva. Az összevonási beállításokat szinkronizálni kell a Azure AD Connect, hogy a felhasználók a hozzáférések kezeléséhez is Azure AD Connect. [További információ](../roles/permissions-reference.md#domain-name-administrator).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---february-2021"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2021. február

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Külső fél integrációja
 
2021 februárjában a következő 37 új alkalmazást adtunk hozzá az alkalmazásgyűjteményhez összevonási támogatással:

Loop [Üzenetküldő](https://loopworks.com/loop-flow-messenger/)bővítmény, [Silverfort Azure AD-adapter,](http://www.silverfort.com/) [Interplay Learning,](https://skilledtrades.interplaylearning.com/#login) [Nura Space](https://dashboard.nuraspace.com/login), [Yooz EU](https://eu1.getyooz.com/?kc_idp_hint=microsoft), [UXPressia](https://uxpressia.com/users/sign-in), [introDus pre- és](http://app.introdus.dk/login)előkészítési platform, [Happybot](https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize?client_id=34353e1e-dfe5-4d2f-bb09-2a5e376270c8&response_type=code&redirect_uri=https://api.happyteams.io/microsoft/integrate&response_mode=query&scope=offline_access%20User.Read%20User.Read.All), [LeaksID](https://app.leaksid.com/), [ShiftWizard](http://www.shiftwizard.com/), [PingFlow SSO](https://app.pingview.io/), [Swiftlane](https://admin.swiftlane.com/login), [Quasydoc SSO](https://www.quasydoc.eu/login), Fen gold account , [SeamlessDesk](https://www.seamlessdesk.com/login), [Learnsoft](https://businesscentral.dynamics.com/) [LMS & TMS](http://www.learnsoft.com/), [P-TH+](https://p-th.jp/), [myViewBoard](https://api.myviewboard.com/auth/microsoft/), [Tartabit IoT Bridge](https://bridge-us.tartabit.com/), [AKGL](../saas-apps/akashi-tutorial.md), [Rewatch](../saas-apps/rewatch-tutorial.md), [Zuddl](../saas-apps/zuddl-tutorial.md), [Parkalot -](../saas-apps/parkalot-car-park-management-tutorial.md)Car park management , [HSB ThoughtSpot](../saas-apps/hsb-thoughtspot-tutorial.md), [IBMid,](../saas-apps/ibmid-tutorial.md) [SharingCloud,](../saas-apps/sharingcloud-tutorial.md) [PoolParty Semantic Suite](../saas-apps/poolparty-semantic-suite-tutorial.md), [GlobeSmart](../saas-apps/globesmart-tutorial.md), Samsung Knox and Business [Services](../saas-apps/samsung-knox-and-business-services-tutorial.md), [Penji](../saas-apps/penji-tutorial.md), [Kendis- Scaling Agile Platform](../saas-apps/kendis-scaling-agile-platform-tutorial.md), [Maptician](../saas-apps/maptician-tutorial.md), [Olfeo SAAS](../saas-apps/olfeo-saas-tutorial.md), [Sigma Computing](../saas-apps/sigma-computing-tutorial.md), [CloudKnox Permissions](../saas-apps/cloudknox-permissions-management-platform-tutorial.md)Management Platform , [Amit saal](../saas-apps/klaxoon-saml-tutorial.md), [Enablon](../saas-apps/enablon-tutorial.md)

Az összes alkalmazás dokumentációját itt is megtalálja: https://aka.ms/AppsTutorial

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásához olvassa el a részleteket itt: https://aka.ms/AzureADAppRequest

--- 

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2021"></a>Új kiépítési összekötők az Azure AD Application Galleryben – 2021. február

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** külső fél integrációja
 

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Atea](../saas-apps/atea-provisioning-tutorial.md)
- [Getabstract](../saas-apps/getabstract-provisioning-tutorial.md)
- [HelloID](../saas-apps/helloid-provisioning-tutorial.md)
- [Hoxhunt](../saas-apps/hoxhunt-provisioning-tutorial.md)
- [Iris Intranet](../saas-apps/iris-intranet-provisioning-tutorial.md)
- [Előzetes kieső érték](../saas-apps/preciate-provisioning-tutorial.md)

További információ: [Felhasználókiépítés automatizálása SaaS-alkalmazásokban az Azure AD-val.](../app-provisioning/user-provisioning.md)

---

### <a name="general-availability---10-azure-active-directory-roles-now-renamed"></a>Általános rendelkezésre állás – 10 Azure Active Directory szerepkör átnevezve

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Rbac  
**Termékképesség:** Access Control
 
10 beépített Azure AD-szerepkör lett átnevezve, hogy a [Microsoft 365 Felügyeleti központ,](/microsoft-365/admin/microsoft-365-admin-center-preview)az [Azure AD](https://portal.azure.com/)portál és a [Microsoft Graph.](https://developer.microsoft.com/graph/) További információ az új szerepkörökről: Rendszergazdai szerepkör engedélyei a [Azure Active Directory.](../roles/permissions-reference.md#all-roles)

![Az MS Graph API szerepkörneveket és a Azure Portal, valamint a javasolt végső nevet az API-ban, a Azure Portal Macben.](media/whats-new/roles-table-rbac.png)

---

### <a name="new-company-branding-in-mfasspr-combined-registration"></a>Új vállalati arculat az MFA/SSPR kombinált regisztrációban

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Felhasználói élmény és felügyelet  
**Termékképesség:** Végfelhasználói élmények
 
A céges emblémákat korábban nem használták Azure Active Directory bejelentkezési oldalakon. A vállalati védjegyezés most az MFA/SSPR kombinált regisztráció bal felső részen található. A Vállalati arculat megjelenítése a Saját Sign-Ins a Biztonsági adatok oldalon is megtalálható. [További információ](../fundamentals/customize-branding.md).

---

### <a name="general-availability---second-level-manager-can-be-set-as-alternate-approver"></a>Általános rendelkezésre állás – A második szintű felettes alternatív jóváhagyóként is beállítható

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Felhasználói hozzáférések kezelése  
**Termékképesség:** Jogosultságkezelés
 
A jóváhagyók kiválasztásakor egy további lehetőség is elérhető a Jogosultságkezelésben. Ha az Első jóváhagyó mezőben a "Vezető jóváhagyóként" lehetőséget választja, egy másik lehetőség, a "Második szintű vezető másodlagos jóváhagyóként" lesz elérhető, amely a másodlagos jóváhagyó mezőben választható. Ha ezt a lehetőséget választja, hozzá kell adni egy tartalék jóváhagyót, aki továbbítja a kérést arra az esetre, ha a rendszer nem találja a második szintű felettest. [További információ](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers).
 
---

### <a name="authentication-methods-activity-dashboard"></a>Hitelesítési módszerek tevékenység irányítópultja

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Jelentési  
**Termékképesség:** Jelentéskészítés & monitorozása
 

A frissített hitelesítési módszerek tevékenység irányítópultja áttekintést nyújt a rendszergazdáknak a hitelesítési módszerek regisztrációjáról és a bérlőjükben végzett használati tevékenységről. A jelentés összegzi az egyes módszerekhez regisztrált felhasználók számát, valamint a bejelentkezés és a jelszó-visszaállítás során használt módszereket. [További információ](../authentication/howto-authentication-methods-activity.md).
 
---

### <a name="refresh-and-session-token-lifetimes-configurability-in-configurable-token-lifetime-ctl-are-retired"></a>A frissítési és munkamenet-jogkivonatok konfigurálható élettartama a konfigurálható jogkivonatok élettartamának (CTL) kivezetve

**Írja be a következőt:** Elavult  
**Szolgáltatáskategória:** Más  
**Termékképesség:** Felhasználóhitelesítés
 
A CTL-ben a frissítési és munkamenet-jogkivonatok konfigurálhatóságának élettartama ki vanvezetve. Azure Active Directory többé nem tiszteletben tartani a meglévő szabályzatok frissítési és munkamenet-jogkivonat-konfigurációját. [További információ](../develop/active-directory-configurable-token-lifetimes.md#token-lifetime-policies-for-refresh-tokens-and-session-tokens).
 
---
 
## <a name="january-2021"></a>2021. január

### <a name="secret-token-will-be-a-mandatory-field-when-configuring-provisioning"></a>A titkos jogkivonat kötelező mező lesz a kiépítés konfigurálásakor

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése

A múltban a titkos jogkivonat mező üresen tartható az egyéni /BYOA-alkalmazásban való kiépítés beállításakor. Ezt a függvényt kizárólag tesztelésre szánták. Frissítjük a felhasználói felületet, hogy kötelező legyen a mező. 

Ezt a követelményt tesztelési célokra a böngésző URL-címében található funkciójelölővel lehet kihozni. [További információ](../app-provisioning/use-scim-to-provision-users-and-groups.md#authorization-to-provisioning-connectors-in-the-application-gallery).
 
---

### <a name="public-preview---customize-and-configure-android-shared-devices-for-frontline-workers-at-scale"></a>Nyilvános előzetes verzió – Android rendszerű megosztott eszközök testreszabása és konfigurálása a frontline workers számára nagy méretekben

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Eszközregisztráció és -kezelés  
**Termékképesség:** Identity Security & Protection
 
Az Azure AD és a Microsoft Endpoint Manager együttesen lehetővé teszi az előtér-feldolgozó eszközök testreszabását, méretezését és biztonságát.

Az alábbi előzetes verziójú képességek a következőket teszik lehetővé:
- Megosztott Android-eszközök nagy léptékű kiépítése a Microsoft Endpoint Manager
- Biztonságos hozzáférés az áttűnő munkatársak számára eszközalapú feltételes hozzáféréssel
- A műszakban dolgozók bejelentkezési élményének testreszabása a Managed Home Screen (Felügyelt kezdőképernyő) alkalmazással

További tudnivalókért lásd: Megosztott eszközök testreszabása és konfigurálása [az elővonali dolgozók számára nagy méretekben.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-and-configure-shared-devices-for-firstline-workers-at/ba-p/1751708)

---

### <a name="public-preview---provisioning-logs-can-now-be-downloaded-as-a-csv-or-json"></a>Nyilvános előzetes verzió – A kiépítési naplók mostantól letölthetők CSV- vagy JSON-fájlként

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése

Az ügyfelek CSV- vagy JSON-fájlként tölthetik le a kiépítési naplókat a felhasználói felületen vagy a Graph API-n keresztül. További információért tekintse meg a [kiépítési jelentéseket a Azure Active Directory portálon.](../reports-monitoring/concept-provisioning-logs.md)

---

### <a name="public-preview---assign-cloud-groups-to-azure-ad-custom-roles-and-admin-unit-scoped-roles"></a>Nyilvános előzetes verzió – Felhőcsoportok hozzárendelése egyéni Azure AD-szerepkörökhöz és rendszergazdai egység hatókörű szerepkörökhöz

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Rbac  
**Termékképesség:** Access Control
 
Az ügyfelek hozzárendelnek egy felhőcsoportot az Azure AD egyéni szerepköreihez vagy egy rendszergazdai egység hatókörű szerepkörhöz. A funkció használatával kapcsolatban lásd: Szerepkör-hozzárendelések kezelése felhőcsoportok használatával a [Azure Active Directory.](../roles/groups-concept.md)

---

### <a name="general-availability---azure-ad-connect-cloud-sync-previously-known-as-cloud-provisioning"></a>Általánosan elérhető – Azure AD Connect szinkronizálása (korábbi nevén felhőalapú kiépítés)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Azure AD Connect szinkronizálása  
**Termékképesség:** Identitás-életciklus kezelése
 
Azure AD Connect felhőalapú szinkronizálás mostantól általánosan elérhető az összes ügyfél számára.

Azure AD Connect a felhő átemeli az átalakítási logika jelentős terhelését a felhőbe, így csökken a helyszíni memóriaigény. Emellett több kis súlyú ügynöktelepítés is elérhető a nagyobb szinkronizálási rendelkezésre állás érdekében. [További információ](https://aka.ms/cloudsyncGA).
 
---
### <a name="general-availability---attack-simulation-administrator-and-attack-payload-author-built-in-roles"></a>Általános rendelkezésre állás – A támadásszimuláció rendszergazdája és a hasznos adatok szerzőjének beépített szerepkörei

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Rbac  
**Termékképesség:** Access Control
 
Két új szerepkör érhető Role-Based Access Control felhasználókhoz, a Támadásszimuláció rendszergazdája és a Hasznos adat támadás szerzője. 

A [Támadásszimuláció](../roles/permissions-reference.md#attack-simulation-administrator) rendszergazdája szerepkörben a felhasználók a bérlő összes szimulációjához hozzáférhetnek, és a következő jogosultságokkal:
- a támadásszimuláció létrehozásának minden aspektusának létrehozása és kezelése
- szimuláció indítása/ütemezése
-  a szimulációs eredmények áttekintése. 

A Hasznos adat [támadása szerző](../roles/permissions-reference.md#attack-payload-author) szerepkörben a felhasználók támadási hasznos okat hozhatnak létre, de ténylegesen nem indítják el vagy ütemezik azokat. Ezután a támadási hasznos adatok a bérlő összes rendszergazdája számára elérhetők, akik szimuláció létrehozásához használhatja őket.

---

### <a name="general-availability---usage-summary-reports-reader-built-in-role"></a>Általános rendelkezésre állás – Használati adatokat összegző jelentések olvasó beépített szerepköre

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Rbac  
**Termékképesség:** Access Control
 
A Használati adatokat összegző jelentések olvasója szerepkörű felhasználók hozzáférhetnek a bérlői szintű összesített adatokhoz és a kapcsolódó elemzésekhez a Microsoft 365 Felügyeleti központban a használati és termelékenységi pontszám alapján. Felhasználói szintű részleteket és elemzéseket azonban nem tudnak elérni. 

A Microsoft 365 jelentés felügyeleti központjában megkülönböztetjük a bérlői szintű összesített adatokat és a felhasználói szint részleteit. Ez a szerepkör egy további védelmi réteggel egészíti ki az egyéni felhasználóazonosításra alkalmas adatokat. [További információ](../roles/permissions-reference.md#usage-summary-reports-reader).

---

### <a name="general-availability---require-app-protection-policy-grant-in-azure-ad-conditional-access"></a>Általános rendelkezésre állás – Szabályzatok Alkalmazásvédelem megkövetelve az Azure AD feltételes hozzáférésben

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Feltételes hozzáférés  
**Termékképesség:** Identity Security & Protection
 
Az Azure AD feltételes hozzáférési engedély a "Require App Protection policy" (Alkalmazásvédelmi szabályzat megkövetelte) esetében mostantól GA. 

A szabályzat a következő képességeket biztosítja:
- Csak akkor engedélyezi a hozzáférést, ha olyan mobilalkalmazást használ, amely támogatja az Intune Alkalmazásvédelem
- Csak akkor engedélyezi a hozzáférést, ha a felhasználó rendelkezik mobilalkalmazáshoz kézbesített Intune alkalmazásvédelmi szabályzatokkal

A feltételes hozzáférési szabályzatok alkalmazásvédelemre való beállítását itt [olvashatja el.](../conditional-access/app-protection-based-conditional-access.md)
 
---

### <a name="general-availability---email-one-time-passcode"></a>Általánosan elérhető – Pin-One-Time e-mailben

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 
Az e-mailes egyszeri program lehetővé teszi, hogy a szervezetek világszerte együttműködjönnek bárkivel egy e-mailben küldött hivatkozással vagy meghívóval. A meghívott felhasználók az e-mailben küldött, a partner erőforrásaihoz való hozzáféréshez küldött egyszeres PIN-kóddal ellenőrizhetik identitásukat. [További információ](../external-identities/one-time-passcode.md). 
 
---

 ### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2021"></a>Új kiépítési összekötők az Azure AD Alkalmazáskatatárban – 2021. január

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Külső fél integrációja
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:
- [Fortes Change Cloud](../saas-apps/fortes-change-cloud-provisioning-tutorial.md)
- [Gtmhub](../saas-apps/gtmhub-provisioning-tutorial.md)
- [monday.com](../saas-apps/mondaycom-provisioning-tutorial.md)
- [Splashtop](../saas-apps/splashtop-provisioning-tutorial.md)
- [Templafy OpenID Connect](../saas-apps/templafy-openid-connect-provisioning-tutorial.md)
- [WEDO](../saas-apps/wedo-provisioning-tutorial.md)

További információkért lásd: Mi az az automatizált SaaS-alkalmazásfelhasználói jogosultságátépítés [az Azure AD-ban?](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---january-2021"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2021. január

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** külső fél integrációja

2021 januárjában a következő 29 új alkalmazást adtunk hozzá az alkalmazásgyűjteményünkbe összevonási támogatással:

[mySCView](https://dev.myscview.com/), [Talentech](https://talentech.com/contact/), [Bipsync](https://www.bipsync.com/), [OroTimesheet](https://app.orotimesheet.com/login.php), [Mio](https://app.m.io/auth/install/microsoft?scopetype=hub), [Sovelto Easy](https://login.soveltoeasy.fi/), [Supportbench,](https://account.supportbench.net/agent/login/)[Bienvenue Formáció,](https://formation.bienvenue.pro/login) [AUE Healthcare SSO,](https://aidaforparents.com/login/organizations) [International SOS Assistance Products](../saas-apps/international-sos-assistance-products-tutorial.md), [NAVEX One,](../saas-apps/navex-one-tutorial.md) [LabLog](../saas-apps/lablog-tutorial.md), [Oktopost SAML,](../saas-apps/oktopost-saml-tutorial.md) [EPHOTO DAM](../saas-apps/ephoto-dam-tutorial.md), [Notion,](../saas-apps/notion-tutorial.md) [Syndio](../saas-apps/syndio-tutorial.md) [,Phoo Enterprise](../saas-apps/yello-enterprise-tutorial.md), [Timeclock 365 SAML,](../saas-apps/timeclock-365-saml-tutorial.md) [Nalco E-data,](https://www.ecolab.com/) [Rendszerancy Filler,](https://app.vacancy-filler.co.uk/VFMVC/Account/Login) [Synerise AI Growth Ecosystem](../saas-apps/synerise-ai-growth-ecosystem-tutorial.md), [Imperva Data Security](../saas-apps/imperva-data-security-tutorial.md), [Illusive Networks](../saas-apps/illusive-networks-tutorial.md), [Proware](../saas-apps/proware-tutorial.md), [Splan Visitor](../saas-apps/splan-visitor-tutorial.md), [Aruba User Experience Insight](../saas-apps/aruba-user-experience-insight-tutorial.md), [Contentsquare SSO,](../saas-apps/contentsquare-sso-tutorial.md) [Perimeter 81](../saas-apps/perimeter-81-tutorial.md), [Burp Suite Enterprise kiadás](../saas-apps/burp-suite-enterprise-edition-tutorial.md)

Az összes alkalmazás dokumentációját itt is megtalálja https://aka.ms/AppsTutorial

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos részleteket itt olvashatja el https://aka.ms/AzureADAppRequest 

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Nyilvános előzetes verzió – A második szintű felettes alternatív jóváhagyóként is beállítható

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Felhasználói hozzáférések kezelése  
**Termékképesség:** Jogosultságkezelés
 
A jóváhagyók kiválasztásakor egy további lehetőség is elérhető a Jogosultságkezelésben. Ha az Első jóváhagyó mezőben a "Vezető jóváhagyóként" lehetőséget választja, egy másik lehetőség, a "Második szintű vezető másodlagos jóváhagyóként" lesz elérhető, amely a másodlagos jóváhagyó mezőben választható ki. Ha ezt a lehetőséget választja, hozzá kell adni egy tartalék jóváhagyót, aki továbbítja a kérést arra az esetre, ha a rendszer nem találja a második szintű felettest. [További információ](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)
 
---

### <a name="general-availability---navigate-to-teams-directly-from-my-access-portal"></a>Általánosan elérhető – Lépjen a Teamsbe közvetlenül a Saját hozzáférés portálról

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Felhasználói hozzáférések kezelése  
**Termékképesség:** Jogosultságkezelés
 
Most már elindíthatja a Teamst közvetlenül a Saját hozzáférés portálról. 

Jelentkezzen be a Saját hozzáférés ( ) szolgáltatásba, lépjen az "Access packages" (Hozzáférési csomagok) lapra, majd lépjen az "Active" (Aktív) lapra, és tekintse meg az összes hozzáférési csomagot, amelyekhez már https://myaccess.microsoft.com/) hozzáféréssel rendelkezik. Ha kibontja a kijelölt hozzáférési csomagot, és a Teamsre egeret használhatja, a Megnyitás gombra kattintva elindíthatja azt. [További információ](../governance/entitlement-management-request-access.md).
 
---

### <a name="improved-logging--end-user-prompts-for-risky-guest-users"></a>Továbbfejlesztett naplózási & End-User kérések kockázatos vendégfelhasználók számára

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Identity Protection  
**Termékképesség:** Identity Security & Protection
 

A Kockázatos vendégfelhasználókra vonatkozó naplózási és End-User kérések frissültek. További információ: [Identity Protection és B2B-felhasználók.](../identity-protection/concept-identity-protection-b2b.md)
 
---
 
## <a name="december-2020"></a>2020. december

### <a name="public-preview---azure-ad-b2c-phone-sign-up-and-sign-in-using-built-in-policy"></a>Nyilvános előzetes verzió – Azure AD B2C telefonos regisztráció és bejelentkezés beépített szabályzat használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** B2B/B2C
 
A B2C telefonos regisztráció és bejelentkezés beépített házirend használatával lehetővé teszi a szervezetek rendszergazdái és fejlesztői számára, hogy a felhasználói folyamatokban telefonszám használatával jelentkezzenek be és regisztrálnak a végfelhasználók számára. További információ: Telefonos regisztráció és bejelentkezés beállítása felhasználói [folyamatokhoz (előzetes](../../active-directory-b2c/phone-authentication-user-flows.md) verzió).

---

### <a name="general-availability---security-defaults-now-enabled-for-all-new-tenants-by-default"></a>Általános rendelkezésre állás – A biztonsági alapértelmezések alapértelmezés szerint engedélyezve vannak az összes új bérlőhöz

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Más  
**Termékképesség:** Identity Security & Protection
 
A felhasználói fiókok védelme érdekében a 2020. november 12-én vagy azt követően létrehozott összes új bérlőn engedélyezve lesz a biztonsági alapértelmezések. A biztonsági alapértelmezések több szabályzatot is kikényszeríteni, például:
- Megköveteli, hogy az összes felhasználó és rendszergazda regisztráljon az MFA-re az Microsoft Authenticator alkalmazással
- Kritikus rendszergazdai szerepkörökre van szükség az MFA minden egyes bejelentkezéskor való használatához. A rendszer minden más felhasználótól kérni fogja az MFA-t, ha szükséges. 
- Az örökölt hitelesítés a teljes bérlőre le lesz tiltva. 

További információ: Mik azok a [biztonsági alapértelmezések?](../fundamentals/concept-fundamentals-security-defaults.md)

---

### <a name="general-availability---support-for-groups-with-up-to-250k-members-in-aadconnect"></a>Általános rendelkezésre állás – A legfeljebb 250 000 tagot meg nem felő csoportok támogatása az AADConnectben

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** AD Connect  
**Termékképesség:** Identitás-életciklus kezelése
 
A Microsoft üzembe helyezett egy új végpontot (API- Azure AD Connect, amely javítja a szinkronizálási szolgáltatás műveleteinek teljesítményét a Azure Active Directory. Az új [V2](../hybrid/how-to-connect-sync-endpoint-api-v2.md)végpont használata során észrevehető teljesítménynövekedést tapasztalhat az Azure AD-be való exportálás és importálás során. Ez az új végpont a következő forgatókönyveket támogatja:

- Csoportok szinkronizálása legfeljebb 250 000 taggal
- Teljesítménynövekedés az Azure AD-be való exportálás és importálás során

---

### <a name="general-availability---entitlement-management-available-for-tenants-in-azure-china-cloud"></a>Általánosan elérhető – Jogosultságkezelés az Azure China-felhőben található bérlők számára

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Felhasználói hozzáférések kezelése  
**Termékképesség:** Jogosultságkezelés
 

A jogosultságkezelés funkciói mostantól az Azure China-felhő összes bérlője számára elérhetők. További információért látogasson el az [Identity Governance dokumentációs webhelyére.](https://docs.azure.cn/zh-cn/active-directory/governance/)

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---december-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – 2020. december

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** külső fél integrációja

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Bizagi Studio a digitális folyamatautomatizáláshoz](../saas-apps/bizagi-studio-for-digital-process-automation-provisioning-tutorial.md)
- [CybSafe](../saas-apps/cybsafe-provisioning-tutorial.md)
- [GroupTalk](../saas-apps/grouptalk-provisioning-tutorial.md)
- [PaperCut Cloud Print Management](../saas-apps/papercut-cloud-print-management-provisioning-tutorial.md)
- [Parsable](../saas-apps/parsable-provisioning-tutorial.md)
- [Shopify Plus](../saas-apps/shopify-plus-provisioning-tutorial.md)

A szervezet automatizált felhasználói fiókok automatikus kiépítése által történő biztonságossá tevésével kapcsolatos további információkért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val.](../app-provisioning/user-provisioning.md)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---december-2020"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2020. december

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** külső fél integrációja
 
2020 decemberében a következő 18 új alkalmazást adtunk hozzá az Alkalmazásgyűjteményhez összevonási támogatással:

[AwareGo](../saas-apps/awarego-tutorial.md), [HowNow SSO](https://gethownow.com/), [ZyLAB ONE Legal Hold](https://www.zylab.com/en/product/legal-hold), [Guider,](http://www.guider-ai.com/) [Softcrisis,](https://www.softcrisis.se/sv/) [Pims 365](http://www.omega365.com/pims), [InformaCast,](../saas-apps/informacast-tutorial.md) [RetrieverMediaDatabase](../saas-apps/retrievermediadatabase-tutorial.md), [vonage](../saas-apps/vonage-tutorial.md), [Count Me In - Operations Dashboard](../saas-apps/count-me-in-operations-dashboard-tutorial.md), [ProCrs Knowledge Base](../saas-apps/proprofs-knowledge-base-tutorial.md), [RightCrowd Workforce Management](../saas-apps/rightcrowd-workforce-management-tutorial.md), [JLL TRIRIGA](../saas-apps/jll-tririga-tutorial.md) [,Stock](../saas-apps/shutterstock-tutorial.md), [FortiWeb Web Application Firewall](../saas-apps/linkedin-talent-solutions-tutorial.md), [LinkedIn Talent Solutions](../saas-apps/linkedin-talent-solutions-tutorial.md), [Equinix Federation App](../saas-apps/equinix-federation-app-tutorial.md), [KFAdvance](../saas-apps/kfadvance-tutorial.md)

Az összes alkalmazás dokumentációját itt is megtalálja https://aka.ms/AppsTutorial

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos részleteket itt olvashatja el https://aka.ms/AzureADAppRequest

---

### <a name="navigate-to-teams-directly-from-my-access-portal"></a>Lépjen a Teamsbe közvetlenül a Saját hozzáférés portálról

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Felhasználói **hozzáférés-kezelési termékképesség: Jogosultságkezelés**

Mostantól közvetlenül a portálról is elindíthatja Saját hozzáférés Teamset. Jelentkezzen be a [Saját hozzáférés,](https://myaccess.microsoft.com/)lépjen a Hozzáférési csomagok lapra, majd lépjen az **Aktív** lapra, és tekintse meg az összes olyan hozzáférési csomagot, amelyekhez már rendelkezik hozzáféréssel. Ha kibontja a hozzáférési csomagot, és a Teams elemre kattint, a Megnyitás gombra kattintva **elindíthatja** azt. 

További információ a Saját hozzáférés portálról: Hozzáférés kérése hozzáférési csomaghoz [az Azure AD-jogosultságkezelésben.](../governance/entitlement-management-request-access.md#sign-in-to-the-my-access-portal)

---

### <a name="public-preview---second-level-manager-can-be-set-as-alternate-approver"></a>Nyilvános előzetes verzió – A második szintű felettes alternatív jóváhagyóként is beállítható

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Felhasználói hozzáférések kezelése  
**Termékképesség:** Jogosultságkezelés

A jogosultságkezelés jóváhagyási folyamatában egy további lehetőség is elérhető. Ha az Első jóváhagyó jóváhagyóként a Felettes lehetőséget választja, egy másik lehetőség is rendelkezésére áll: Második szintű vezető másodlagos jóváhagyóként, amely a másodlagos jóváhagyó mezőben választható. Ha ezt a lehetőséget választja, hozzá kell adni egy tartalék jóváhagyót, aki továbbítja a kérést arra az esetre, ha a rendszer nem találja a második szintű felettest.

További információ: Hozzáférési csomag jóváhagyási beállításainak módosítása [az Azure AD-jogosultságkezelésben.](../governance/entitlement-management-access-package-approval-policy.md#alternate-approvers)

--- 

## <a name="november-2020"></a>2020. november

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation"></a>Azure Active Directory TLS 1.0, TLS 1.1 és 3DES elavult

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Minden Azure AD-alkalmazás  
**Termékképesség:** Szabványok

Azure Active Directory 2021. június 30 Azure Active Directory kezdve a következő protokollok elavultak lesznek a globális régiókban:

- TLS 1.0
- TLS 1.1
- 3DES rejtjelcsomag (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Az érintett környezetek a következő:
- Azure kereskedelmi felhő
- Office 365 GCC és WW

Kapcsolódó közlemény Minden ügyfél-kiszolgáló és böngésző-kiszolgáló kombinációnak a TLS 1.2-t és a modern titkosítási csomagokat kell használnia, hogy biztonságos kapcsolatot tartsunk fenn az Azure Active Directory-hoz az Azure, az Office 365 és a Microsoft 365 szolgáltatásokhoz. Ez a változás a [Azure Active Directory TLS 1.0 1.0-s & 1.1-es](whats-new.md#azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud)és a 3DES-rejtjelcsomag elaprításával kapcsolatos a US Gov Cloudban.

Az elavult protokollfüggőségek eltávolításával kapcsolatos útmutatásért lásd: [Enable support for TLS 1.2 in your environment for Azure AD TLS 1.1 and 1.0 deprecation (A TLS 1.2](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)támogatásának engedélyezése a környezetben az Azure AD TLS 1.1 és 1.0 elavult).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---november-2020"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2020. november

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** külső fél integrációja

2020 novemberében a következő 52 új alkalmazást adtunk hozzá az Alkalmazásgyűjteményhez összevonási támogatással:

[Travel & Expense Management](https://app.expenseonce.com/Account/Login) [,Rmloo](../saas-apps/tribeloo-tutorial.md), [Itslearning File Picker,](https://pmteam.itslearning.com/) [Control,](../saas-apps/crises-control-tutorial.md) [CourtAlert](https://www.courtalert.com/), [StealthMail](https://stealthmail.com/), [Edmentum - Study Island](https://app.studyisland.com/cfw/login/), Virtual Risk [Manager,](../saas-apps/virtual-risk-manager-tutorial.md) [TIMU](../saas-apps/timu-tutorial.md), [Looker Analytics Platform](../saas-apps/looker-analytics-platform-tutorial.md), [Talview - Recruit,](https://recruit.talview.com/login)Real Time Translator, [Podbean](../saas-apps/podbean-tutorial.md), [zcal](https://zcal.co/signup), [expensemanager](https://api.expense-manager.com/), [Netsparker Enterprise](../saas-apps/netsparker-enterprise-tutorial.md), [En-trak Tenant Experience Platform](https://portal.en-trak.app/), [Appian](../saas-apps/appian-tutorial.md), [Panorays](../saas-apps/panorays-tutorial.md), [Builterra](https://portal.builterra.com/), EVA [Check-in](https://my.evacheckin.com/organization), [HowNow WebApp SSO](../saas-apps/hownow-webapp-sso-tutorial.md) [,Szervi-kockázat](../saas-apps/coupa-risk-assess-tutorial.md)felmérése , [Mindd (Minden termék),](../saas-apps/lucid-tutorial.md) [GoBright](https://portal.brightbooking.eu/), [SailPoint IdentityNow](../saas-apps/sailpoint-identitynow-tutorial.md), Resource[Central](../saas-apps/resource-central-tutorial.md), [UiPathStudioO365App](https://www.uipath.com/product/platform), [Jedox](../saas-apps/jedox-tutorial.md), [Cequence Application](../saas-apps/cequence-application-security-tutorial.md)Security , [PerimeterX,](../saas-apps/perimeterx-tutorial.md) [TrendMiner](../saas-apps/trendminer-tutorial.md), [Lexion,](../saas-apps/lexion-tutorial.md) [WorkWare,](../saas-apps/workware-tutorial.md) [ProdPad](../saas-apps/prodpad-tutorial.md), [AWS ClientVPN,](../saas-apps/aws-clientvpn-tutorial.md) [AppSec Flow SSO](../saas-apps/appsec-flow-sso-tutorial.md), [Luum,](../saas-apps/luum-tutorial.md)Freight [Measure,](https://www.gpcsl.com/freight.html) [Terraform Cloud](../saas-apps/terraform-cloud-tutorial.md), [Nature Research](../saas-apps/nature-research-tutorial.md), [Play Digital Signage](https://login.playsignage.com/login), [RemotePC](../saas-apps/remotepc-tutorial.md), [Prolorus,](../saas-apps/prolorus-tutorial.md) [Hirebridge ATS](../saas-apps/hirebridge-ats-tutorial.md), [Teamgage](https://www.teamgage.com/Account/ExternalLoginAzure), [Roadmunk](../saas-apps/roadmunk-tutorial.md), [Microsoft Software Relations CRM](https://cloud.relations-crm.com/), [Procware](../saas-apps/procaire-tutorial.md), Mentor® by [eDriving: Business](https://www.edriving.com/), [Gradle Enterprise](https://gradle.com/) [](https://access.klaxoon.com/login)

Az összes alkalmazás dokumentációját itt is megtalálja https://aka.ms/AppsTutorial

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos részleteket itt olvashatja el https://aka.ms/AzureADAppRequest

---

### <a name="public-preview---custom-roles-for-enterprise-apps"></a>Nyilvános előzetes verzió – Egyéni szerepkörök vállalati alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Rbac  
**Termékképesség:** Access Control
 
 [A delegált vállalati alkalmazáskezelés egyéni RBAC-szerepkörei](../roles/custom-available-permissions.md) nyilvános előzetes verzióban érhetőek el. Ezek az új engedélyek az alkalmazásregisztráció-kezelés egyéni szerepköreire épülnek, ami lehetővé teszi a rendszergazdák hozzáférésének finomhangolt szabályozását. Idővel további engedélyek szabadulnak fel az Azure AD felügyeletének delegálhatóra.

Néhány gyakori delegálás:
- SAML-alapú egyszeri bejelentkezési alkalmazásokhoz hozzáférő felhasználók és csoportok hozzárendelése
- Azure AD katalógusbeli alkalmazások létrehozása
- SAML-alapú egyszeri bejelentkezési alkalmazások alapszintű SAML-konfigurációjának frissítése és olvasása
- SAML-alapú egyszeri bejelentkezési alkalmazások aláíró tanúsítványának kezelése
- lejáró bejelentkezési tanúsítványok értesítési e-mail-címének frissítése SAML-alapú egyszeri bejelentkezési alkalmazásokhoz
- SAML-jogkivonat-aláírás és bejelentkezési algoritmus frissítése SAML-alapú egyszeri bejelentkezési alkalmazásokhoz
- SAML-alapú egyszeri bejelentkezési alkalmazások felhasználói attribútumainak és jogcímének létrehozása, törlése és frissítése
- a kiépítési feladatok be- és kikapcsolási és újraindítási képessége
- attribútumleképezés frissítései
- az objektumhoz társított kiépítési beállítások olvasásának képessége
- a szolgáltatásnévhez társított kiépítési beállítások olvasásának képessége
- alkalmazás-hozzáférés jogosultságának jogosultsága a kiépítéshez

---

### <a name="public-preview---azure-ad-application-proxy-natively-supports-single-sign-on-access-to-applications-that-use-headers-for-authentication"></a>Nyilvános előzetes verzió – Az Azure AD alkalmazásproxy natív módon támogatja az egyszeri bejelentkezéses hozzáférést az olyan alkalmazásokhoz, amelyek fejléceket használnak a hitelesítéshez

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazásproxy  
**Termékképesség:** Access Control
 
Azure Active Directory (Azure AD) alkalmazásproxy natív módon támogatja az egyszeri bejelentkezéses hozzáférést az olyan alkalmazásokhoz, amelyek fejléceket használnak a hitelesítéshez. Az alkalmazáshoz szükséges fejlécértékeket az Azure AD-ban konfigurálhatja. A fejlécértékeket a rendszer leküldi az alkalmazásnak a alkalmazásproxy. További információ: [Fejlécalapú](../manage-apps/application-proxy-configure-single-sign-on-with-headers.md) egyszeri bejelentkezés helyszíni alkalmazásokhoz Azure AD alkalmazás Proxyval
 
---

### <a name="general-availability---azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy"></a>Általánosan elérhető – Azure AD B2C telefonos regisztráció és bejelentkezés egyéni szabályzat használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** B2B/B2C

A telefonszám-regisztrációval és -bejelentkezéssel a fejlesztők és a nagyvállalatok lehetővé tehetik az ügyfeleik számára, hogy SMS-ben a felhasználó telefonszámára küldött egyszeres jelszóval regisztrálják és jelentkezzenek be. Ez a funkció azt is lehetővé teszi, hogy az ügyfél megváltoztasa a telefonszámát, ha elveszíti a telefonhoz való hozzáférését. Az egyéni szabályzatok segítségével a fejlesztők és a vállalatok az oldalak testreszabásával kommunikálhatnak a márkájukkal. Ismerje meg, hogyan állíthat be telefonos regisztrációt és bejelentkezést egyéni szabályzatokkal a [Azure AD B2C.](../../active-directory-b2c/phone-authentication-user-flows.md)
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---november-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – 2020. november

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Külső fél integrációja
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Adobe Identity Management](../saas-apps/adobe-identity-management-provisioning-tutorial.md)
- [Blogin](../saas-apps/blogin-provisioning-tutorial.md)
- [Clarizen One](../saas-apps/clarizen-one-provisioning-tutorial.md)
- [Contentful](../saas-apps/contentful-provisioning-tutorial.md)
- [GitHub AE](../saas-apps/github-ae-provisioning-tutorial.md)
- [Playvox](../saas-apps/playvox-provisioning-tutorial.md)
- [PrinterLogic SaaS](../saas-apps/printer-logic-saas-provisioning-tutorial.md)
- [Tic – Mobile](../saas-apps/tic-tac-mobile-provisioning-tutorial.md)
- [Visibly](../saas-apps/visibly-provisioning-tutorial.md)

További információ: [Felhasználókiépítés automatizálása SaaS-alkalmazásokban az Azure AD-val.](../app-provisioning/user-provisioning.md)
 
---

### <a name="public-preview---email-sign-in-with-proxyaddresses-now-deployable-via-staged-rollout"></a>Nyilvános előzetes verzió – A ProxyAddresses e-mailes Sign-In üzembe helyezhető szakaszos bevezetéssel

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Felhasználóhitelesítés
 
A bérlői rendszergazdák mostantól használhatnak szakaszos bevezetést az e-mail-Sign-In a ProxyAddresses használatával adott Azure AD-csoportokban. Ez segíthet a szolgáltatás kipróbálásában, mielőtt a teljes bérlőn üzembe helyeznék a Kezdőlapfelderítési szabályzat használatával. Az e-mail-Sign-In ProxyAddresses használatával, szakaszos bevezetéssel történő üzembe helyezésére vonatkozó utasítások a dokumentációban [találhatóak.](../authentication/howto-authentication-use-email-signin.md)
 
---

### <a name="limited-preview---sign-in-diagnostic"></a>Korlátozott előzetes verzió – Bejelentkezési diagnosztika

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Jelentési  
**Termékképesség:** Jelentéskészítés & figyelése
 
A bejelentkezési diagnosztika kezdeti előzetes kiadásával a rendszergazdák áttekinthetik a felhasználói bejelentkezéseket. A rendszergazdák környezetfüggő, konkrét és releváns részleteket és útmutatást kaphatnak arról, hogy mi történt a bejelentkezés során, és hogyan oldják meg a problémákat. A diagnosztika az Azure AD szintjén és a Feltételes hozzáférés diagnosztizálása és megoldása panelen is elérhető. Az ebben a kiadásban szereplő diagnosztikai forgatókönyvek a feltételes hozzáférés, a többtényezős hitelesítés és a sikeres bejelentkezés.

További információ: Mi az az [Azure AD-beli bejelentkezési diagnosztika?](../reports-monitoring/overview-sign-in-diagnostics.md).
 
---

### <a name="improved-unfamiliar-sign-in-properties"></a>Továbbfejlesztett ismeretlen bejelentkezési tulajdonságok

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Identity Protection  
**Termékképesség:** Identity Security & Protection

  szokatlan bejelentkezési tulajdonságok észlelések frissültek. Az ügyfelek nagyobb kockázatú, ismeretlen bejelentkezési tulajdonságok észlelését észlelhetnek. További információ: Mi [a kockázat?](../identity-protection/concept-identity-protection-risks.md)
 
---

### <a name="public-preview-refresh-of-cloud-provisioning-agent-now-available-version-112810"></a>Elérhetővé válik a Felhőalapú kiépítési ügynök nyilvános előzetes verziója (Verzió: 1.1.281.0)

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Azure AD Felhőalapú kiépítés  
**Termékképesség:** Identitás-életciklus kezelése
 
A felhőalapú kiépítési ügynök nyilvános előzetes verzióban érhető el, és mostantól elérhető a portálon keresztül. Ez a kiadás számos fejlesztést tartalmaz, beleértve a GMSA támogatását a tartományokhoz, amely nagyobb biztonságot, továbbfejlesztett kezdeti szinkronizálási ciklusokat és nagy csoportok támogatását biztosítja. További részletekért tekintse meg a [kiadási](../app-provisioning/provisioning-agent-release-version-history.md) verzióelőzményeket. 
 
---

### <a name="bitlocker-recovery-key-api-endpoint-now-under-informationprotection"></a>A BitLocker helyreállítási kulcs API-végpontja most az /informationProtection alatt található

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Eszköz hozzáférés-kezelés  
**Termékképesség:** Eszközök életciklus-kezelése
 
Korábban a /bitlocker végponton keresztül helyreállíthat BitLocker-kulcsokat. Idővel ki fogjuk használni ezt a végpontot, és az ügyfeleknek el kell kezdenie használni az /informationProtection alá eső API-t. 

A módosításoknak megfelelően a dokumentáció frissítéseit a [BitLocker helyreállítási API-val](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) kapcsolatos dokumentumban találhatja meg.

---

### <a name="general-availability-of-application-proxy-support-for-remote-desktop-services-html5-web-client"></a>A HTML5 alkalmazásproxy ügyfél általános Távoli asztali szolgáltatások támogatása

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Alkalmazásproxy  
**Termékképesség:** Access Control
 
Az Azure AD alkalmazásproxy (RDS) Távoli asztali szolgáltatások támogatása mostantól általánosan elérhető. Az RDS webes ügyféllel Távoli asztal felhasználók bármilyen HTLM5-kompatibilis böngészőn keresztül hozzáférhetnek az infrastruktúrához, például Microsoft Edge, Internet Explorer 11, Google Chrome stb. A felhasználók úgy kommunikálhatnak a távoli alkalmazásokkal vagy asztalokkal, mint egy helyi eszközzel, bárhonnan. 

Az Azure AD alkalmazásproxy használatával növelheti az RDS-környezet biztonságát, ha előhitelesítési és feltételes hozzáférési szabályzatokat kényszerít minden típusú gazdag ügyfélalkalmazáshoz. További információ: [Publish Távoli asztal with Azure AD alkalmazásproxy](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="new-enhanced-dynamic-group-service-is-in-public-preview"></a>Az új továbbfejlesztett dinamikuscsoport-szolgáltatás nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Csoportkezelés  
**Termékképesség:** Együttműködés
 
A továbbfejlesztett dinamikus csoportszolgáltatás mostantól nyilvános előzetes verzióban érhető el. Azok az új ügyfelek, akik dinamikus csoportokat hoznak létre a bérlőikben, az új szolgáltatást fogják használni. A dinamikus csoport létrehozásához szükséges idő arányos lesz a létrehozni kívánt csoport méretével a bérlő mérete helyett. Ez a frissítés jelentősen javítja a nagy bérlők teljesítményét, amikor az ügyfelek kisebb csoportokat hoznak létre. 

Az új szolgáltatás célja továbbá, hogy néhány percen belül befejezi a tagi összeadást és eltávolítást az attribútumok változásai miatt. Az egyetlen feldolgozási hiba sem blokkolja a bérlők feldolgozását. A dinamikus csoportok létrehozásával kapcsolatos további információkért tekintse meg a [dokumentációt.](../enterprise-users/groups-create-rule.md)
 
---
## <a name="october-2020"></a>2020. október

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Az Azure TLS-tanúsítvány változásai által érintett helyszíni Azure AD hibrid ügynökök

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** N/a  
**Termékképesség:** Platform

A Microsoft úgy frissíti az Azure-szolgáltatásokat, hogy más főtanúsítvány-készletből származó TLS-tanúsítványokat használjanak. Ennek a frissítésnek az az oka, hogy a jelenlegi HITELESÍTÉSszolgáltatói tanúsítványok nem felelnek meg a CA-/böngészőfórum alapkövetelményei egyikének. Ez a módosítás olyan helyszíni környezetekben telepített hibrid Azure AD-ügynököket fog befolyásolni, amelyek a főtanúsítványok rögzített listájával vannak megváltva, és frissíteni kell őket, hogy megbízzon az új tanúsítványkiállítókban.

Ez a változás szolgáltatáskimaradást eredményez, ha nem azonnal lép fel. Ezek az ügynökök alkalmazásproxy helyszíni távoli hozzáféréshez használható [](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) összekötőket tartalmaznak, az áttűnésalapú hitelesítési ügynököket, amelyek lehetővé teszik a felhasználók számára, hogy ugyanazokkal [a](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) jelszavakkal jelentkezzenek be az alkalmazásokba, valamint a [Cloud Provisioning Preview](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) ügynököket, amelyek AD-ről Azure AD-szinkronizálásra végeznek műveleteket. 

Ha olyan környezettel rendelkezik, amely olyan tűzfalszabályokkal rendelkezik, amelyek csak bizonyos visszavont tanúsítványok listájának (CRL) letöltésére engedélyezik a kimenő hívásokat, engedélyeznie kell a következő CRL- és OCSP-URL-címeket. A módosítással, valamint a hozzáférés engedélyezéséhez szükséges CRL- és OCSP URL-címekkel kapcsolatos részletekért lásd: [Az Azure TLS-tanúsítvány módosításai.](../../security/fundamentals/tls-certificate-changes.md)

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>A kiépítési események el lesznek távolítva az auditnaplókból, és kizárólag a kiépítési naplókban lesznek közzétéve

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Jelentési  
**Termékképesség:** Jelentéskészítés & monitorozása
 
Az SCIM [kiépítési](../app-provisioning/user-provisioning.md) szolgáltatása naplózza az auditnaplókat és a kiépítési naplókat is. Ide tartozik például egy felhasználó létrehozása a ServiceNow-ban, csoport létrehozása a GSuite-ban vagy egy szerepkör importálása az AWS-ről. A jövőben ezek az események csak a kiépítési naplókban lesznek közzétéve. Ez a módosítás azért van megvalósítva, hogy elkerülje a naplók közötti ismétlődő eseményeket, valamint a naplókat a Naplóelemzésben felhasználó ügyfelek által felmerülő további költségeket. 

Egy dátum befejezésekor egy frissítést is meg fogunk adni. Ez az elalasztás nem a 2020-as naptári évre van tervezve. 

> [!NOTE]
> Ez nem befolyásolja az auditnaplókban a kiépítési szolgáltatás által kibocsátott szinkronizálási eseményeken kívüli eseményeket. Az olyan események, mint az alkalmazások létrehozása, a feltételes hozzáférési szabályzatok, a címtárban található felhasználók stb. továbbra is ki lesznek bocsátva az auditnaplókban. [További információ](../reports-monitoring/concept-provisioning-logs.md?context=azure%2factive-directory%2fapp-provisioning%2fcontext%2fapp-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Azure AD helyszíni hibrid ügynökök, amelyekre hatással vannak az Azure Transport Layer Security (TLS) tanúsítványának változásai

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** N/a  
**Termékképesség:** Platform
 
A Microsoft úgy frissíti az Azure-szolgáltatásokat, hogy különböző legfelső szintű hitelesítésszolgáltatói (CA-k) TLS-tanúsítványait használják. A frissítés azért lesz, mert a jelenlegi hitelesítésszolgáltatói tanúsítványok nem követik a CA-/böngészőfórum alapkonfiguráció-követelményeinek valamelyikét. Ez a módosítás hatással lesz a helyszínen telepített hibrid Azure AD-ügynökökre, amelyek rögzített főtanúsítvány-listában rögzített rögzített környezetekkel vannak telepítve. Ezeket az ügynököket frissíteni kell, hogy megbízzon az új tanúsítványkiállítókban.

Ez a változás szolgáltatáskimaradást eredményez, ha nem azonnal lép fel. Ezek az ügynökök a következők: 
- [alkalmazásproxy összekötők](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) helyszíni táveléréshez 
- [A passthrough Authentication-ügynökök](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) lehetővé teszik, hogy a felhasználók ugyanazokkal a jelszavakkal jelentkezzenek be az alkalmazásokba
- [Felhőalapú kiépítési előzetes verziójú](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) ügynökök, amelyek AD-ről Azure AD-szinkronizálásra szolgálnak. 

Ha olyan környezettel rendelkezik, amely olyan tűzfalszabályokkal rendelkezik, amelyek csak bizonyos visszavont tanúsítványok listájának (CRL) kimenő hívásait engedélyezik, engedélyeznie kell a CRL- és OCSP URL-címeket. A módosítással, valamint a hozzáférés engedélyezéséhez szükséges CRL- és OCSP-URL-címekkel kapcsolatos részletekért lásd: [Az Azure TLS-tanúsítvány módosításai.](../../security/fundamentals/tls-certificate-changes.md)
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1.0, TLS 1.1 és 3DES elalasztás a US Gov Cloudban

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Minden Azure AD-alkalmazás  
**Termékképesség:** Szabványok
 
Azure Active Directory 2021. március 31-től a következő protokollok elavultak lesznek:
- TLS 1.0
- TLS 1.1
- 3DES rejtjelcsomag (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

A TLS 1.2 és a modern rejtjelcsomagok használatával minden ügyfél-kiszolgáló és böngésző-kiszolgáló kombinációnak biztonságos kapcsolatot kell fenntartania az Azure Active Directory-hoz az Azure, az Office 365 és a Microsoft 365 számára.

Az érintett környezetek a következő:
- Azure US Gov
- [Office 365 GCC High & DoD](/microsoft-365/compliance/tls-1-2-in-office-365-gcc)

Az elavult protokollfüggőségek eltávolításával kapcsolatos útmutatásért lásd: [Enable support for TLS 1.2 in your environment for Azure AD TLS 1.1 and 1.0 deprecation (A TLS 1.2](https://docs.microsoft.com/troubleshoot/azure/active-directory/enable-support-tls-environment)támogatásának engedélyezése a környezetben az Azure AD TLS 1.1-es és 1.0-s elavult).
 
---

### <a name="assign-applications-to-roles-on-administrative-unit-and-object-scope"></a>Alkalmazások hozzárendelése szerepkörökhöz felügyeleti egységben és objektumhatókörben

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Rbac  
**Termékképesség:** Access Control
 
Ez a funkció lehetővé teszi egy alkalmazás (SPN) rendszergazdai szerepkörhöz való hozzárendelését a felügyeleti egység hatókörében. További információ: Hatókörrel rendelkező szerepkörök [hozzárendelése felügyeleti egységhez.](../roles/admin-units-assign-roles.md)

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Mostantól letilthatja és törölheti a vendégfelhasználók hozzáférését egy erőforráshoz

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Hozzáférési felülvizsgálatok  
**Termékképesség:** Identity Governance
 
A Letiltás és törlés egy speciális vezérlő az Azure AD hozzáférési felülvizsgálatokban, amely segít a szervezeteknek a külső vendégek csoportokban és alkalmazásokban való jobb kezelésében. Ha egy hozzáférési felülvizsgálat megtagadja  a vendégek hozzáférését, a letiltás és törlés 30 napig automatikusan letiltja a bejelentkezést. 30 nap után teljesen el lesznek távolítva a bérlőből.

További információ erről a szolgáltatásról: Külső identitások letiltása és törlése [az Azure AD hozzáférési felülvizsgálatokkal.](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews)
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>A hozzáférés-áttekintés létrehozói egyéni üzeneteket adhatnak hozzá a felülvizsgálókhoz az e-mailekben

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Hozzáférési felülvizsgálatok  
**Termékképesség:** Identity Governance
 
Az Azure AD hozzáférési felülvizsgálatok során a felülvizsgálatokat létrehozó rendszergazdák mostantól egyéni üzenetet írhatnak a felülvizsgálóknak. A felülvizsgálók a kapott e-mailben megjelenik az üzenetben, amely felszólítja őket a felülvizsgálat befejezésére. A funkció használatával kapcsolatos további információkért lásd az Egy vagy több hozzáférési felülvizsgálat létrehozása szakasz 14. [lépését.](../governance/create-access-review.md#create-one-or-more-access-reviews)

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Új kiépítési összekötők az Azure AD Alkalmazáskatatárban – 2020. október

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Külső fél integrációja
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Globális Relay-identitás szinkronizálása](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

A szervezet automatizált felhasználói fiókok automatikus kiépítése által történő biztonságossá teről további információért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val.](../app-provisioning/user-provisioning.md)
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Integrációs asszisztens Azure AD B2C

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** B2B/B2C
 
Az Integrációs asszisztens (előzetes verzió) már elérhető a Azure AD B2C Alkalmazásregisztrációk. Ez a felhasználói élmény segítséget nyújt az alkalmazás gyakori forgatókönyvekhez való konfigurálásához. További információ a [Microsoft identitásplatformján ajánlott eljárásokról és javaslatokról.](../develop/identity-platform-integration-checklist.md)
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Szerepkörsablon azonosítójának megtekintése a Azure Portal felhasználói felületén

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Azure-szerepkörök  
**Termékképesség:** Access Control
 

Most már megtekintheti az egyes Azure AD-szerepkör sablonazonosítóját a Azure Portal. Az Azure AD-ban válassza  **ki a kiválasztott** szerepkör leírását. 

Javasoljuk, hogy az ügyfelek a megjelenített név helyett a szerepkörsablon-kódot használják a PowerShell-szkriptben és a kódban. A szerepkörsablon-azonosítók a [directoryRoles](/graph/api/resources/directoryrole) és [roleDefinition](/graph/api/resources/unifiedroledefinition?view=graph-rest-beta&preserve-view=true) objektumokhoz használhatók. További információ a szerepkörsablon-sablonokhoz: [Az Azure AD beépített szerepkörei.](../roles/permissions-reference.md)

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>A regisztrációhoz Azure AD B2C felhasználói folyamatok API-összekötői most már nyilvános előzetes verzióban megjelenik

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** B2B/B2C
 

Az API-összekötők mostantól használhatók a Azure Active Directory B2C. Az API-összekötők lehetővé teszik a webes API-k használatát a regisztráció felhasználói folyamatának testreszabásához és a külső felhőrendszerekkel való integrációhoz. Az API-összekötők a következőre használhatók:

- Integrálás egyéni jóváhagyási munkafolyamatokkal
- Felhasználói beviteli adatok ellenőrzése
- Felhasználói attribútumok felülírása 
- Egyéni üzleti logika futtatása 

 További információért látogasson el az [API-összekötők](../../active-directory-b2c/api-connectors-overview.md) használata a regisztráció dokumentációjának testreszabásához és kiterjesztéséhez.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>A csatlakoztatott szervezetek Állapot tulajdonsága a jogosultságkezelésben

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Címtárkezelési **termékképesség: Jogosultságkezelés**
 

 Mostantól minden csatlakoztatott szervezethez egy "State" nevű további tulajdonság is hozzá lesz csatlakoztatható. Az állapot azt fogja szabályozni, hogy a rendszer hogyan használja a csatlakoztatott szervezetet az "összes konfigurált csatlakoztatott szervezetre" hivatkozó szabályzatban. Az érték lehet "konfigurált" (azaz a szervezet az "összes" záradékot használó szabályzatok hatókörében van) vagy "javasolt" (azaz a szervezet nem része a hatókörnek).  

A manuálisan létrehozott csatlakoztatott szervezetek alapértelmezett beállítása "konfigurálva" lesz. Eközben az automatikusan létrehozottak (olyan szabályzatokkal létrehozva, amelyek lehetővé teszik, hogy az internet bármely felhasználója hozzáférést kérjen) alapértelmezés szerint "javasolt" lesz.  A 2020. szeptember 9. előtt létrehozott csatlakoztatott szervezetek beállítása "konfigurálva" lesz. A rendszergazdák szükség szerint frissítheti ezt a tulajdonságot. [További információ](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory External Identities prémium szintű speciális biztonsági beállításokkal rendelkezik a B2C-hez

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** B2B/B2C
 
Az Identity Protection kockázatalapú feltételes hozzáférési és kockázatészlelési funkciói már elérhetők a [Azure AD B2C.](../..//active-directory-b2c/conditional-access-identity-protection-overview.md) Ezekkel a speciális biztonsági funkciókkal az ügyfelek a következő lehetőségeket kínálják:
- Intelligens elemzésekkel mérheti fel a kockázatokat B2C-alkalmazásokkal és végfelhasználói fiókokkal. Az észlelések közé tartozik az atipikus utazás, a névtelen IP-címek, a kártevők által hivatkozott IP-címek és Azure AD Intelligens veszélyforrás-felderítés. Portál- és API-alapú jelentések is elérhetők.
- A kockázatok automatikus kezelése adaptív hitelesítési házirendek konfigurálásával a B2C-felhasználók számára. Az alkalmazásfejlesztők és rendszergazdák csökkenthetik a valós idejű kockázatokat azáltal, hogy többtényezős hitelesítést (MFA) követelnek meg, vagy letiltják a hozzáférést az észlelt felhasználói kockázati szinttől függően, további, hely, csoport és alkalmazás alapján elérhető vezérlőkkel.
- Integráció a Azure AD B2C folyamatokkal és egyéni szabályzatokkal. A feltételek aktiválhatóak a beépített felhasználói folyamatokból a Azure AD B2C vagy beépíthetőek az egyéni B2C-házirendekbe. A B2C felhasználói folyamat más aspektusainak megfelelően a végfelhasználói élmény üzenetkezelése is testre szabható. A testreszabás a szervezet hang-, márka- és kockázatcsökkentési alternatívái alapján lehetséges.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2020. október

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Külső fél integrációja
 
2020 októberében a következő 27 új alkalmazást adtunk hozzá az alkalmazásgyűjteményünkbe összevonási támogatással:

[Sentry](../saas-apps/sentry-tutorial.md) [,Bleblebee - Productivity Superapp](https://app.yellowmessenger.com/user/login), [ABBYY Rugalmascapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Cloud Integration for Azure](https://apps.mypurecloud.com/msteams-integration/), Zone Technologies [Portal](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [Check Vállalati](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [Találkozó](https://app.appointmentreminder.co.nz/account/login)emlékeztető , [Cloud.Market](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [Üdvözöljük](https://app.greetly.com/), [OrgVitality SSO,](../saas-apps/orgvitality-sso-tutorial.md) [Web Cargo Air](../saas-apps/web-cargo-air-tutorial.md), Loop Flow [CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem,](https://hrm.workstem.com/login) [Retail Zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [MEVISIO](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure virtuális Traffic Manager](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

Az összes alkalmazás dokumentációját itt is megtalálja https://aka.ms/AppsTutorial

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos részleteket itt olvashatja el https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>A kiépítési naplók mostantól továbbíthatóak a Log Analyticsbe

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Jelentési  
**Termékképesség:** Jelentéskészítés & monitorozása
 

Tegye közzé a kiépítési naplókat a Log Analyticsben a következő szolgáltatások érdekében:
- Kiépítési naplók tárolása több mint 30 napig
- Egyéni riasztások és értesítések meghatározása
- Irányítópultok létrehozása a naplók megjelenítéséhez
- Összetett lekérdezések végrehajtása a naplók elemzéséhez 

A funkció használatával kapcsolatos további információkért lásd: A kiépítés és a Azure Monitor [integrálása.](../app-provisioning/application-provisioning-log-analytics.md)
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>A kiépítési naplókat mostantól megtekinthetik az alkalmazás tulajdonosai

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Jelentési  
**Termékképesség:** Jelentéskészítés & monitorozása
 
Mostantól engedélyezheti az alkalmazástulajdonosok számára, hogy a kiépítési szolgáltatás által figyeljék a tevékenységeket, és elhárítsa a problémákat anélkül, hogy kiemelt szerepkört adna nekik, vagy szűk keresztmetszetet hozna az it-számukra. [További információ](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>10 szerepkör Azure Active Directory át

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Azure-szerepkörök  
**Termékképesség:** Access Control
 
Egyes Azure Active Directory (AD) beépített szerepkörei a Microsoft 365 Felügyeleti központ, az Azure AD portál és a Microsoft Graph. Ez az inkonzisztencia problémákat okozhat az automatizált folyamatokban. Ezzel a frissítéssel 10 szerepkörnevet fogunk átnevezésre, hogy konzisztensek legyenek. Az alábbi táblázat tartalmazza az új szerepkörneveket:

![Az MS Graph API és Azure Portal szerepkörneveket, valamint az M365 Felügyeleti központban javasolt új szerepkörnevet, az Azure Portal és az API-t bemutató táblázat.](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C MSAL JS 2.x-et használó spA-k hitelesítési kódfolyamának támogatása

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** B2B/B2C
 
MSAL.js 2.x verzió már támogatja az egyoldalas webalkalmazások (SPA-k) engedélyezési kódfolyamát. Azure AD B2C alkalmazás mostantól támogatja az SPA-alkalmazástípus használatát az Azure Portal-ban, valamint az MSAL.js-hitelesítési kódfolyam PKCE-val való használatát egyoldalas alkalmazásokhoz. Ez lehetővé teszi, hogy a Azure AD B2C az SSO-t újabb böngészőkben tartsák fenn, és betartsák az újabb hitelesítési protokollra vonatkozó javaslatokat. Első lépések: Egyoldalas alkalmazás [regisztrálása (SPA) az Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) oktatóanyagban.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>Frissítések a többtényezős hitelesítés (MFA) megbízható eszközbeállításon való jegyezéséhez

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Mfa  
**Termékképesség:** Identity Security & Protection
 

Nemrég frissítettük a többtényezős hitelesítés [(MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) megbízható eszközfunkción való használatát, amely akár 365 napra is kiterjeszti a hitelesítést. Azure Active Directory (Azure AD) Premium-licencek a Feltételes hozzáférés [–](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) Bejelentkezési gyakoriság szabályzatot is használják, amely nagyobb rugalmasságot biztosít az újrahitelesítő beállításokhoz.

Az optimális felhasználói élmény érdekében azt javasoljuk, hogy a megbízható eszközökön, helyeken vagy alacsony kockázatú munkameneteken a munkamenetek élettartamának meghosszabbításához használja a feltételes hozzáférési gyakoriságot, amely a megbízható eszközbeállításon található MFA-használat használatának alternatívájaként használható. Első lépésekként tekintse át az újrahitelesítő élmény optimalizálásával kapcsolatos [legújabb útmutatót.](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)

---
