---
title: Archívum a Azure Active Directory? | Microsoft Docs
description: A tartalomkészlet Áttekintés szakaszában található Újdonságok kibocsátási megjegyzések 6 hónapos tevékenységet tartalmaznak. 6 hónap után a rendszer eltávolítja az elemeket a fő cikkből, és ebbe az archívumcikkbe kerül.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 3/31/2021
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca7fda6345356568d512b396c412603cf7d837f7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532397"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archívum a Azure Active Directory?

Az [Újdonságok a Azure Active Directory?](whats-new.md) kibocsátási megjegyzések elsődleges cikke az elmúlt hat hónap frissítéseit tartalmazza, míg ez a cikk az összes régebbi információt tartalmazza.

Az Újdonságok a Azure Active Directory? A kibocsátási megjegyzések a következő információkkal kapcsolatos információkat tartalmaznak:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Terv a változásokhoz

---

## <a name="september-2020"></a>2020. szeptember

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Új kiépítési összekötők az Azure AD Alkalmazáskatatárban – 2020. szeptember

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Külső fél integrációja
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Webroot Security Awareness](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

A szervezet automatizált felhasználói fiókok automatikus kiépítése által történő biztonságossá tevésével kapcsolatos további információkért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val.](../app-provisioning/user-provisioning.md)
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>A felhőalapú kiépítés nyilvános előzetes verzió frissítése

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Az Azure AD Cloud Provisioning **Product képessége:** Identitás-életciklus kezelése
 
Azure AD Connect cloud provisioning public preview refresh (Felhőalapú kiépítés nyilvános előzetes verzió frissítése) funkció két jelentős fejlesztést tartalmaz, amelyek az ügyfelek visszajelzéseiből fejlődtek: 

- Attribútumleképezési élmény Azure Portal

    Ezzel a funkcióval a rendszergazdák az AD-ről az Azure AD-be leképezhetnek felhasználó-, csoport- vagy kapcsolatattribútumokat a jelenleg használt különböző leképezési típusok használatával. Az attribútumleképezés egy olyan szolgáltatás, amely az attribútumok és az Active Directory között Azure Active Directory. Meghatározhatja, hogy az attribútumérték közvetlenül legyen leképezve az AD-ről az Azure AD-be, vagy kifejezések használatával alakítsa át az attribútumértékeket a felhasználók kiépítésekor. [További információ](../cloud-sync/how-to-attribute-mapping.md)

- Igény szerinti kiépítés vagy tesztfelhasználói élmény

    A konfiguráció beállítása után érdemes lehet tesztelni, hogy a felhasználói átalakítás a várt módon működik-e, mielőtt az összes hatókörbe tartozó felhasználóra alkalmazva lenne. Az igény szerinti kiépítéssel a rendszergazdák megadhatjak az AD-felhasználók megkülönböztető nevét (DN), és láthatják, hogy a várt módon szinkronizálják-e őket. Az igény szerinti kiépítés nagyszerű lehetőséget kínál annak biztosítására, hogy az attribútumleképezések korábban a várt módon működjön. [További információ](../cloud-sync/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Auditált BitLocker-helyreállítás az Azure AD-ban – Nyilvános előzetes verzió

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Eszköz hozzáférés-kezelés  
**Termékképesség:** Eszközök életciklus-kezelése
 
Amikor a rendszergazdák vagy végfelhasználók a BitLocker helyreállítási kulcs(oknak) a hozzáférését olvassák, a Azure Active Directory létrehoz egy auditnaplót, amely rögzíti, hogy ki fért hozzá a helyreállítási kulcshoz. Ugyanez a naplózás tartalmazza annak az eszköznek a részleteit, amelyhez a BitLocker-kulcs társítva lett.

A végfelhasználók a Saját fiókon keresztül férhetnek hozzá a [helyreállítási kulcsukhoz.](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key) A rendszergazdák bétaverzióban vagy az Azure AD portálon keresztül férhetnek hozzá a helyreállítási kulcsokhoz a [BitLocker helyreállításikulcs-API-n](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta&preserve-view=true) keresztül. További információ: BitLocker-kulcsok megtekintése vagy másolása [az Azure AD portálon.](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys)

---

### <a name="teams-devices-administrator-built-in-role"></a>Teams-eszközök rendszergazdai beépített szerepköre

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Rbac  
**Termékképesség:** Access Control
 
A [Teams-eszközök rendszergazdája](../roles/permissions-reference.md#teams-devices-administrator) szerepkörű felhasználók a Teams felügyeleti központban kezelhetik a [Teams-tanúsítvánnyal](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) rendelkező eszközöket. 

Ez a szerepkör lehetővé teszi, hogy a felhasználó egyetlen pillantással megtekintsen minden eszközt, és lehetővé teszi az eszközök keresését és szűrését. A felhasználó az egyes eszközök adatait is ellenőrizheti, beleértve a bejelentkezett fiókot, valamint az eszköz modelljét és modelljét. A felhasználó módosíthatja az eszköz beállításait, és frissítheti a szoftververziókat. Ez a szerepkör nem ad engedélyeket a Teams-tevékenységek ellenőrzéséhez és az eszköz minőségének ellenőrzéséhez.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Speciális lekérdezési képességek címtárobjektumok számára

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** MS Graph  
**Termékképesség:** Fejlesztői élmény
 
Az Azure AD API-k címtárobjektumaihoz bevezetett összes új lekérdezési képesség elérhető az 1.0-s végponton, és készen áll az éles használatra. A fejlesztők a szabványos OData-operátorok használatával megszámolnak, keresnek, szűrnek és rendeznek címtárobjektumokat és kapcsolódó hivatkozásokat.

További tudnivalókért tekintse meg az itt [található dokumentációt,](https://aka.ms/BlogPostMezzoGA)és ezzel a rövid felméréssel [visszajelzést is küldhet.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u)
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Nyilvános előzetes verzió: a feltételes hozzáférési szabályzatokat konfiguráló bérlők folyamatos hozzáférés-kiértékelése

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Identity Security & Protection
 
A folyamatos hozzáférés-kiértékelés (CAE) nyilvános előzetes verzióban érhető el feltételes hozzáférési szabályzatokkal az Azure AD-bérlők számára. A CAE-ben a kritikus fontosságú biztonsági események és szabályzatok valós időben vannak kiértékelve. Ide tartozik a fiók letiltása, az új jelszó létrehozása és a hely módosítása. További információ: Folyamatos [hozzáférés-kiértékelés.](../conditional-access/concept-continuous-access-evaluation.md)

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Nyilvános előzetes verzió: további kérdések kérése a hozzáférési csomagot kérelmező felhasználóktól a jóváhagyási döntések javítása érdekében

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Felhasználói hozzáférések kezelése  
**Termékképesség:** Jogosultságkezelés
 
A rendszergazdák mostantól megkövetelhetik, hogy a hozzáférési csomagot igénylő felhasználók az Üzleti indokláson túl további kérdéseket is megválaszolnak az Azure AD jogosultságkezelési Saját hozzáférés portálján. Ezután megjelennek a felhasználók válaszai a jóváhagyók számára, hogy pontosabb döntést hoznak a hozzáférés-jóváhagyási döntésekről. További információ: [További kérelmezői információk gyűjtése jóváhagyáshoz (előzetes verzió).](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)
 
---

### <a name="public-preview-enhanced-user-management"></a>Nyilvános előzetes verzió: Továbbfejlesztett felhasználókezelés

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Felhasználókezelés  
**Termékképesség:** Felhasználókezelés
 

Az Azure AD portált frissítettük, hogy könnyebb legyen megtalálni a felhasználókat a Minden felhasználó és a Törölt felhasználók oldalon. Az előzetes verzió változásai többek között a következők: 
- Láthatóbb felhasználói tulajdonságok, beleértve az objektumazonosítót, a címtár-szinkronizálás állapotát, a létrehozás típusát és az identitáskiállítót.
- A keresés mostantól lehetővé teszi a nevek, e-mailek és objektum-objektumok együttes keresését.
- Továbbfejlesztett szűrés felhasználótípus (tag, vendég és nincs), címtár-szinkronizálás állapota, létrehozási típus, vállalatnév és tartománynév szerint.
- Új rendezési képességek olyan tulajdonságokon, mint a név, az egyszerű felhasználónév és a törlés dátuma.
- Az új összes felhasználó megszámol, hogy a keresések vagy szűrők frissülnek.

További információ: A felhasználókezelés fejlesztései [(előzetes verzió) a Azure Active Directory.](../enterprise-users/users-search-enhanced.md)

---

### <a name="new-notes-field-for-enterprise-applications"></a>Új megjegyzések mező vállalati alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Nagyvállalati **alkalmazások termékképessége:** SSO

A vállalati alkalmazásokhoz szabad szöveges megjegyzéseket adhat hozzá. Bármilyen olyan releváns információt hozzáadhat, amely segítséget nyújt az alkalmazások felügyeletében a Vállalati alkalmazások alatt. További információ: Rövid útmutató: Tulajdonságok konfigurálása egy alkalmazáshoz a Azure Active Directory [(Azure AD) bérlőben.](../manage-apps/add-application-portal-configure.md) 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2020. szeptember

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Külső fél integrációja

2020 szeptemberében a következő 34 új alkalmazást adtunk hozzá az alkalmazásgyűjteményünkbe összevonási támogatással:

[VMware Horizon]()– Unified Access Gateway , Pulse Secure [PCS,](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md) [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_Server](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASE;/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILÉ](../saas-apps/cybersolutions-cybermail-tutorial.md) [,MitleCMMS ,CleleCMMS](https://auth.limblecmms.com/) [,Cleint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), Gender [Fitness](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [Grammarly](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Ku rendelkezésre](../saas-apps/kumolus-tutorial.md)álló , [RSA Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR,](https://marketplace.bizmerlin.net/bmone/signup)Mobile Suite , [Zengine](../saas-apps/zengine-tutorial.md), [CloudCADI,](https://app.cloudcadi.com/login) [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [Priva Identity & Access Management](https://my.priva.com/), [Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md), [Secured Signing Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), Secured Signing [Enterprise Portal AAD](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal) [Setup](../saas-apps/mobile-locker-tutorial.md), [Wistec Online](https://wisteconline.com/auth/oidc), Oracle PeopleSoft – Az [F5 BIG-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md) által védett

Az összes alkalmazás dokumentációját itt is megtalálja: https://aka.ms/AppsTutorial .

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásához olvassa el a részleteket itt: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Új delegálás szerepkör az Azure AD jogosultságkezelésében: Hozzáférési csomag-hozzárendelés kezelője

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Felhasználói hozzáférések kezelése  
**Termékképesség:** Jogosultságkezelés
 
Az Azure AD jogosultságkezelése új hozzáférésicsomag-hozzárendelés-kezelői szerepkörrel bővült, amely részletes engedélyeket biztosít a hozzárendelések kezeléséhez. Most már delegálhat feladatokat a szerepkör egy felhasználójának, aki a hozzáférési csomag hozzárendelés-kezelését egy üzleti tulajdonosnak delegálhatja. A hozzáférésicsomag-hozzárendelés kezelője azonban nem módosíthatja a hozzáférésicsomag-házirendeket és a rendszergazdák által beállított egyéb tulajdonságokat. 

Ezzel az új szerepkörvel élvezheti a hozzárendelések felügyeletének delegálához és az összes többi hozzáférésicsomag-konfiguráció felügyeleti felügyeletének fenntartásához szükséges legkevesebb jogosultságot. További információ: [Jogosultságkezelési szerepkörök.](../governance/entitlement-management-delegate.md#entitlement-management-roles)
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>A Privileged Identity Management folyamatának változásai

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Privileged Identity Management  
**Termékképesség:** Privileged Identity Management
 
Korábban a Privileged Identity Management (PIM) regisztrálásához felhasználói jóváhagyásra és egy regisztrációs folyamatra volt szükség a PIM panelen, amely a regisztrációt is Azure AD MFA. A PIM használatának az Azure AD-szerepkörök és -rendszergazdák panelre való legutóbbi integrációjával megszüntetjük ezt a felhasználói élményt. Az érvényes P2 licenccel rendelkező bérlők automatikusan fel lesznek automatizálva a PIM-be.

A PIM-be való be- és be történő be- és be- vagy le- vagy felvezétezés nincs közvetlen kedvezőtlen hatással a bérlőre. A következő változások várhatók:
- További hozzárendelési lehetőségek, például aktív vagy jogosult a kezdési és a záró időpontra, amikor a PIM vagy az Azure AD-szerepkörök és rendszergazdák panelen hoz hozzárendelést. 
- További hatókör-hatókörkezelést szolgáló mechanizmusok, például a felügyeleti egységek és az egyéni szerepkörök közvetlenül a hozzárendelési folyamatba vezetnek be. 
- Ha Ön globális rendszergazda vagy kiemelt szerepkörú rendszergazda, elkezdhet néhány további e-mailt kapni, például a PIM heti kivonatát. 
- A szerepkör-hozzárendeléssel kapcsolatos auditnaplóban az ms-pim szolgáltatásnév is látható. Ez a várt változás nem befolyásolja a normál munkafolyamatot.

 További információ: [A](../privileged-identity-management/pim-getting-started.md)Privileged Identity Management.

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD-jogosultságkezelés: A hozzáférésicsomag-erőforrások Kiválasztás panele alapértelmezés szerint a kiválasztott katalógusban jelenleg található erőforrásokat jeleníti meg

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Felhasználói hozzáférések kezelése  
**Termékképesség:** Jogosultságkezelés
 

A hozzáférésicsomag-létrehozási folyamat Erőforrás-szerepkörök lapján a Kijelölés panel viselkedése változik. Jelenleg az alapértelmezett viselkedés az összes olyan erőforrás megjelenítése, amely a felhasználó tulajdonában van, valamint a kiválasztott katalógushoz hozzáadott erőforrások. 

Ez a felhasználói élmény úgy módosul, hogy csak a katalógusban aktuálisan hozzáadott erőforrásokat jelenítse meg alapértelmezés szerint, így a felhasználók könnyedén választhatnak erőforrásokat a katalógusból. A frissítés segít felderíteni a hozzáférési csomagokhoz hozzáadható erőforrásokat, és csökkenti annak kockázatát, hogy véletlenül olyan erőforrásokat adjon hozzá a felhasználóhoz, akik nem részei a katalógusnak. További információ: Új hozzáférési csomag létrehozása az [Azure AD-jogosultságkezelésben.](../governance/entitlement-management-access-package-create.md#resource-roles)
 
---

## <a name="august-2020"></a>2020. augusztus 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Az Azure Multi-Factor Authentication-kiszolgáló tűzfalkövetelmények frissítései

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Mfa  
**Termékképesség:** Identity Security & Protection
 
2020. október 1-től Azure MFA-kiszolgáló tűzfalkövetelmények további IP-címtartományokat igényelnek.

Ha a szervezetben kimenő tűzfalszabályok vannak, frissítse a szabályokat, hogy az MFA-kiszolgálók kommunikáljanak az összes szükséges IP-tartománysal. Az IP-címtartományokat az [Azure tűzfalkövetelményei Multi-Factor Authentication-kiszolgáló dokumentálja.](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements)

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>A felhasználói élmény közelgő változásai az identitás-biztonsági pontszámban

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Identity Protection **termékképesség:** Identity Security & Protection

Frissítjük az Identity Secure Score portált, hogy igazodjon a Microsoft Secure Score új kiadásában bevezetett [változásokhoz.](/microsoft-365/security/mtp/microsoft-secure-score-whats-new) 

A módosításokat bemutató előzetes verzió szeptember elején lesz elérhető. Az előzetes verzió változásai a következők:
- Az "Identity Secure Score" új neve "Secure Score for Identity" (Identitás biztonsági pontszáma) a márka és a Microsoft Secure Score igazítása érdekében
- A standard szintre normalizált és a pontok helyett százalékban jelentett pontok

Ebben az előzetes verzióban az ügyfelek válthatnak a meglévő és az új felhasználói élmény között. Ez az előzetes verzió 2020. november végéig lesz elérhető. Az előzetes verzió után a rendszer automatikusan az új felhasználói felületre irányítja az ügyfeleket.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Új korlátozott vendégelérési engedélyek az Azure AD-ban – Nyilvános előzetes verzió

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Access Control   
**Termékképesség:** Felhasználókezelés

Frissítettük a vendégfelhasználók címtárszintű engedélyét. Ezekkel az engedélyekkel a rendszergazdák további korlátozásokat és korlátozásokat követelnek meg a külső vendégfelhasználók hozzáférésére. A rendszergazdák mostantól további korlátozásokat adhatnak hozzá a külső vendégek felhasználói és csoportprofilhoz és tagsági információkhoz való hozzáférése számára. Ezzel a nyilvános előzetes verziójú funkcióval az ügyfelek nagy méretekben kezelhetik a külső felhasználói hozzáférést a csoporttagságok eltolásával, beleértve annak korlátozását, hogy a vendégfelhasználók ne látják a csoport(nak) tagságát.

További információ: Korlátozott [vendégelérési](../enterprise-users/users-restrict-guest-permissions.md) engedélyek és Alapértelmezett felhasználói [engedélyek.](./users-default-permissions.md)
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Szolgáltatásnév változáslekérdezések általános rendelkezésre állása

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** MS Graph  
**Termékképesség:** Fejlesztői élmény
 
Microsoft Graph Delta Query mostantól támogatja az erőforrástípust az 1.0-s v.0-ban:
- Szolgáltatásnév

Az ügyfelek mostantól hatékonyan követhetik az erőforrások módosításait, és a legjobb megoldást kínálják az erőforrások módosításainak helyi adattáraval való szinkronizálására. Az erőforrások lekérdezésben való konfigurálásával kapcsolatos további információkért lásd: Változáslekérdezés használata a változásadatok [változásainak Microsoft Graph követéséhez.](/graph/delta-query-overview)
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Az oAuth2PermissionGrant változáslekérdezéseinek általános rendelkezésre állása

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** MS Graph  
**Termékképesség:** Fejlesztői élmény

Microsoft Graph Delta Query mostantól támogatja az 1.0-s erőforrástípust:
- OAuth2PermissionGrant

Az ügyfelek mostantól hatékonyan nyomon követhetik ezen erőforrások változásait, és a legjobb megoldást kínálják az erőforrások módosításainak helyi adattáraval való szinkronizálására. Az erőforrások lekérdezésben való konfigurálásával kapcsolatos további információkért lásd: Változáslekérdezés használata az adatok [változásainak Microsoft Graph követésére.](/graph/delta-query-overview)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2020. augusztus

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** külső fél integrációja

2020 augusztusában a következő 25 új alkalmazást adtunk hozzá az Alkalmazásgyűjteményhez összevonási támogatással:

[Backup365,](https://portal.backup365.io/login) [Soapbox,](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2) [Alma SIS,](https://almau.getalma.com/) [Enlyft Dynamics 365-összekötő,](http://enlyft.com/) [Serraview Space Utilization Software Solutions](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [Uniq,](https://web.uniq.app/) [Visibly](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum - Courseware Assessments Exact Path](https://auth.edmentum.com/elf/login), [CyberLAB,](https://cyberlab.evolvesecurity.com/#/welcome) [Altamira HRM,](../saas-apps/altamira-hrm-tutorial.md) [WireWheel,](../saas-apps/wirewheel-tutorial.md) [Zix Compliance and Capture](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [Greenlight Enterprise Business Controls Platform,](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md) [Genetec Compliance](https://www.clearance.network/), iSAMS , [VeraSMART,](../saas-apps/verasmart-tutorial.md) [Amiko,](https://amiko.web.rivero.app/) [Twingate,](https://auth.twingate.com/signup) [Funnel Leasing](https://nestiolistings.com/sso/oidc/azure/authorize/), Scale [skálázó](https://scalefusion.com/users/sign_in/), [Bpandia](https://goto.bpanda.com/login), [Andun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect), [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md), [Egy Végfelhasználó](https://www.wandera.com/) [](../saas-apps/isams-tutorial.md)

Az összes alkalmazás dokumentációját itt is megtalálja https://aka.ms/AppsTutorial

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos részleteket itt olvashatja el https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Az erőforráserdők már elérhetők a Azure AD DS 

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Azure AD Domain Services   
**Termékképesség:** Azure AD Domain Services
 
Mostantól általánosan elérhető a Azure AD Domain Services erdőinek képessége. Mostantól engedélyezheti a jelszó kivonatszinkronizálása nélküli engedélyezést, Azure AD Domain Services az intelligens kártya hitelesítését is. További információ: Replikakészletek alapfogalmai és funkciói a [Azure Active Directory Domain Services (előzetes verzió)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>A regionális replika támogatása már Azure AD DS felügyelt tartományokhoz

**Írja be a következőt:** Új funkció   
**Szolgáltatáskategória:** Azure AD Domain Services  
**Termékképesség:** Azure AD Domain Services
 
A felügyelt tartományokat kiterjesztheti úgy, hogy Azure AD-bérlőnként több replikakészletet használjanak. A replikakészletek bármely virtuális társhálózathoz hozzáadhatók bármely olyan Azure-régióban, amely támogatja a Azure AD Domain Services. A különböző Azure-régiókban található további replikakészletek földrajzi vészhelyreállítást biztosítanak az örökölt alkalmazások számára, ha egy Azure-régió offline állapotba kerül. További információ: Replikakészletek alapfogalmai és funkciói Azure Active Directory Domain Services [(előzetes verzió)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Az Azure AD My Sign-Ins

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Végfelhasználói élmények
 
Az Azure AD My Sign-Ins egy új funkció, amely lehetővé teszi a vállalati felhasználók számára a bejelentkezési előzmények áttekintését a szokatlan tevékenységek ellenőrzéséhez. Ez a funkció lehetővé teszi a végfelhasználók számára, hogy "Ez nem én vagyok" vagy "Ez én vagyok" jelentést jelentsen a gyanús tevékenységekről. A funkció használatával kapcsolatos további információkért lásd a Legutóbbi bejelentkezési tevékenység megtekintése és keresése a Saját Sign-Ins [oldalon.](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Általánosan elérhető az SAP SuccessFactors HR-alapú felhasználóátépítés az Azure AD-be

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése
 
Most már integrálhatja az SAP SuccessFactorst mint mérvadó identitásforrást az Azure AD-be, és automatizálhatja a végpontok között elvégző identitás-életciklust a HR-események ( például új bérlések és leállítások) használatával a fiókok Azure AD-beli kiépítése és megszüntetése érdekében. 

Az SAP SuccessFactors Azure AD-be irányuló bejövő kiépítésének konfigurálásról további információt az SAP SuccessFactors konfigurálása a felhasználók Active Directory [konfiguráláshoz oktatóanyagban olvashat.](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Egyéni Open ID Connect MS Graph API támogatás a Azure AD B2C

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** B2B/B2C
 
Korábban az egyéni open ID Connect szolgáltatók csak az alkalmazáson keresztül voltak hozzáadva vagy Azure Portal. Az ügyfelek Azure AD B2C API-k bétaverziója segítségével Microsoft Graph és kezelhetik őket. Az erőforrás API-okkal való konfigurálásával kapcsolatban lásd: [identityProvider erőforrástípus.](/graph/api/resources/identityprovider?view=graph-rest-beta&preserve-view=true)
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Beépített Azure AD-szerepkörök hozzárendelése felhőcsoportokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Azure AD-szerepkörök  
**Termékképesség:** Access Control

Ezzel az új funkcióval mostantól beépített Azure AD-szerepköröket rendelhet felhőcsoportokhoz. Hozzárendelheti például a SharePoint-rendszergazda szerepkört egy Contoso_SharePoint_Admins csoporthoz. A PIM-et arra is használhatja, hogy állandó hozzáférés megadása helyett jogosult tagtá tegye a csoportot. A funkció konfigurálásának elsajátításért lásd: Szerepkör-hozzárendelések kezelése felhőcsoportok használatával a [Azure Active Directory (előzetes verzió)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Az Insights Business Leader beépített szerepköre már elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Azure AD-szerepkörök  
**Termékképesség:** Access Control
 
Az Elemzések üzleti vezető szerepkörének felhasználói az [M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)alkalmazással férhetnek hozzá irányítópultokhoz és elemzésekhez. Ez magában foglalja az összes irányítópult teljes hozzáférését, valamint a bemutatott elemzéseket és adatfeltárási funkciókat. Az ebben a szerepkörben található felhasználók azonban nem férhetnek hozzá a termékkonfigurációs beállításokhoz, ami az Insights-rendszergazdai szerepkör feladata. További információ erről a szerepkörről: Rendszergazdai [szerepkör engedélyei a Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Az Insights-rendszergazda beépített szerepköre már elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Azure AD-szerepkörök  
**Termékképesség:** Access Control
 
Az Insights-rendszergazda szerepkörben a felhasználók az [M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)alkalmazás összes felügyeleti képességét elérhetik. Az ebben a szerepkörben található felhasználók olvashatják a címtáradatokat, figyelheti a szolgáltatás állapotát, fájltámogatási jegyeket kaphatnak, és hozzáférhetnek az Insights rendszergazdai beállításaihoz. További információ erről a szerepkörről: Rendszergazdai [szerepkör engedélyei a Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Az alkalmazás-rendszergazda és a felhőalkalmazás-rendszergazda kezelheti az alkalmazások bővítménytulajdonságait

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Azure AD-szerepkörök  
**Termékképesség:** Access Control
 
Korábban csak a globális rendszergazda kezelheti a [bővítménytulajdonságokat.](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http&preserve-view=true) Most már az alkalmazás-rendszergazda és a felhőalkalmazás-rendszergazda számára is engedélyezjük ezt a képességet.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>A MIM 2016 SP2 4.6.263.0-s gyorsjavítása és az 1.1.1301.0-s összekötők

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Microsoft Identity Manager  
**Termékképesség:** Identitás-életciklus kezelése

A Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) csomaghoz elérhető egy gyorsjavítási összesítő csomag [(build: 4.6.263.0).](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) Ez az összesítő csomag a MIM CM, a MIM Synchronization Manager és a PAM összetevők frissítéseit tartalmazza. Emellett az általános MIM-összekötők 1.1.1301.0-s buildet is tartalmaznak a Graph-összekötő frissítéseit.

---

## <a name="july-2020"></a>2020. július

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Rendszergazdaként feltételes hozzáféréssel szeretnék ügyfélalkalmazásokat célozni

**Írja be a következőt:** A változás megterve   
**Szolgáltatáskategória:** Feltételes hozzáférés  
**Termékképesség:** Identity Security & Protection
 
A feltételes hozzáférés ügyfélalkalmazás-feltételének általánosan elérhető kiadásával az új szabályzatok alapértelmezés szerint minden ügyfélalkalmazásra érvényesek. Ide tartoznak az örökölt hitelesítési ügyfelek is. A meglévő szabályzatok változatlanok maradnak, de a Configure *Yes/No (Igen/Nem)* váltógomb el lesz távolítva a meglévő szabályzatból, így könnyen látható, hogy mely ügyfélalkalmazásokat alkalmazza a szabályzat. 

Új szabályzat létrehozásakor mindenképpen zárja ki azokat a felhasználókat és szolgáltatásfiókokat, amelyek továbbra is régi hitelesítést használnak; Ha nem, a rendszer letiltja őket. [További információ](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>SciM-megfelelőségi javítások várhatók

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése
 
Az Azure AD kiépítési szolgáltatás az SCIM szabványt használja az alkalmazásokkal való integrációhoz. Az SCIM-szabvány implementációja folyamatosan fejlődik, és arra számítunk, hogy módosítjuk a PATCH-műveletek végrehajtásának viselkedését, valamint az "aktív" tulajdonságot az erőforráson. [További információ](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Az Azure felügyeleti portál csoporttulajdonosi beállítása módosul

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Csoportkezelés  
**Termékképesség:** Együttműködés

A Csoportok általános beállítási oldalán a Tulajdonosi beállítások konfigurálható úgy, hogy a tulajdonos-hozzárendelési jogosultságokat a felhasználók egy korlátozott csoportjára korlátozzák az Azure felügyeleti portálján és hozzáférési panel. Hamarosan nemcsak a két felhasználói felület ezen a két portálján lehet csoporttulajdonosi jogosultságot hozzárendelni, hanem kényszeríteni is a szabályzatot a háttéren, hogy konzisztens viselkedést biztosítson a végpontok, például a PowerShell és a Microsoft Graph. 

Elkezdjük letiltani az aktuális beállítást a nem használó ügyfelek számára, és a következő néhány hónapban lehetőséget biztosítunk a felhasználók csoporttulajdonosi jogosultságra való hatókörének beállításához. A csoportbeállítások frissítésével kapcsolatos útmutatásért lásd: Csoportinformációk szerkesztése a [Azure Active Directory.](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory regisztrációs szolgáltatás befejezi a TLS 1.0 és 1.1 támogatását

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Eszközregisztráció és -kezelés  
**Termékképesség:** Platform

A Transport Layer Security (TLS) 1.2 és a frissítési kiszolgálók és ügyfelek hamarosan kommunikálni fognak a Azure Active Directory Eszközregisztráció szolgáltatással. A TLS 1.0 és 1.1 támogatása az Azure AD eszközregisztrációs szolgáltatással való kommunikációhoz kivezetve lesz:
- 2020. augusztus 31-én minden szuverén felhőben (GCC High, DoD stb.)
- 2020. október 30-án minden kereskedelmi felhőben

[További információ a](../devices/reference-device-registration-tls-1-2.md) TLS 1.2-ről az Azure AD regisztrációs szolgáltatáshoz.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Vállalati Windows Hello Azure AD bejelentkezési naplókban látható bejelentkezések megtekintése

**Írja be a következőt:** Rögzített  
**Szolgáltatáskategória:** Jelentési  
**Termékképesség:** Jelentéskészítés & monitorozása
 
Vállalati Windows Hello lehetővé teszi a végfelhasználók számára, hogy kézmozdulatokkal (például PIN-kódot vagy biometriát) jelentkezzenek be a Windows rendszerű gépekre. Előfordulhat, hogy az Azure AD-rendszergazdák meg szeretnék különböztetni Vállalati Windows Hello bejelentkezéseket a többi Windows-bejelentkezéstől a szervezet jelszó nélküli hitelesítésre való folyamatának részeként. 

A rendszergazdák mostantól az Azure AD Sign-Ins panelen, a Windows-bejelentkezési esemény Hitelesítési adatok lapján Vállalati Windows Hello-e a Windows-hitelesítés Azure Portal. Vállalati Windows Hello a "WindowsHelloForBusiness" szerepel a Hitelesítési módszer mezőben. A bejelentkezési naplók értelmezésével Sign-In a bejelentkezési [naplók dokumentációjában talál további információt.](../reports-monitoring/concept-sign-ins.md)
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>A csoporttörlési viselkedés és a teljesítmény javításai javításai

**Írja be a következőt:** Rögzített  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése
 
Korábban, amikor egy csoport "hatókören belülről hatókören kívül" lett, és egy rendszergazda a módosítás befejezése előtt az újraindításra kattintott, a csoportobjektum nem lett törölve. A csoportobjektum most már törlődik a célalkalmazásból, amikor az már nem tartozik a hatókörbe (letiltva, törölve, nincs hozzárendelve, vagy nem adott át hatókörszűrőn). [További információ](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Nyilvános előzetes verzió: A rendszergazdák mostantól egyéni tartalmakat adhatnak hozzá az e-mailhez a felülvizsgálókhoz hozzáférési felülvizsgálat létrehozásakor

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Hozzáférési felülvizsgálatok  
**Termékképesség:** Identity Governance
 
Új hozzáférési felülvizsgálat létrehozásakor a felülvizsgáló kap egy e-mailt, amely a hozzáférési felülvizsgálat befejezését kéri. Számos ügyfelünk azt kérte, hogy az e-mailhez egyéni tartalmat, például kapcsolattartási adatokat vagy egyéb kiegészítő tartalmat adjon hozzá a felülvizsgálónak. 

A nyilvános előzetes verzióban elérhető rendszergazdák egyéni tartalmakat adhatnak meg a felülvizsgálóknak küldött e-mailben, ha hozzáadnak tartalmat az Azure AD hozzáférési felülvizsgálatok "speciális" szakaszában. A hozzáférési felülvizsgálatok létrehozásával kapcsolatos útmutatásért lásd: Csoportok és alkalmazások hozzáférési felülvizsgálatának létrehozása az [Azure AD hozzáférési felülvizsgálatok során.](../governance/create-access-review.md)
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Az egyoldalas alkalmazások engedélyezési kódfolyama elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Fejlesztői élmény
 
A böngészők modern, harmadik féltől származó cookie-kra vonatkozó korlátozásai, például a Safari ITP miatt az SPA-knak az engedélyezési kódfolyamot kell használniuk az implicit folyamat helyett az SSO karbantartásához, és az MSAL.js 2.x-es verziója mostantól támogatni fogja az engedélyezési kódfolyamot. 

Az alkalmazáshoz megfelelő frissítések Azure Portal hogy az SPA-t "spa" típusúra frissítse, és használja a hitelesítési kódfolyamot. További [útmutatásért lásd: Felhasználók bejelentkezése és hozzáférési jogkivonat](../develop/quickstart-v2-javascript-auth-code.md) beszerzése JavaScript SPA-ban a hitelesítési kódfolyam használatával.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Az Azure AD alkalmazásproxy mostantól támogatja a Távoli asztali szolgáltatások webes ügyfelet

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazásproxy  
**Termékképesség:** Access Control

Az Azure AD alkalmazásproxy támogatja az Távoli asztali szolgáltatások (RDS) webes ügyfelet. Az RDS webes ügyféllel Távoli asztal felhasználók bármilyen HTLM5-kompatibilis böngészővel elérhetik az infrastruktúrát, például Microsoft Edge, Internet Explorer 11, Google Chrome stb. A felhasználók úgy kommunikálhatnak a távoli alkalmazásokkal vagy asztalokkal, mint egy helyi eszközzel, bárhonnan. Az Azure AD alkalmazásproxy az RDS üzembe helyezésének biztonságát azáltal, hogy előhitelesítési és feltételes hozzáférési szabályzatokat kényszerít minden típusú gazdag ügyfélalkalmazáshoz. Útmutatásért lásd: [Távoli asztal közzététele az Azure AD alkalmazásproxy.](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md)
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>A felhasználói Azure AD B2C új generációja nyilvános előzetes verzióban

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** B2B/B2C
 
Az egyszerűsített felhasználói folyamat funkcióparitást kínál az előzetes verziójú funkciókkal, és minden új funkciónak ad kezdőlapot. A felhasználók új funkciókat engedélyeznek ugyanazon felhasználói folyamaton belül, így minden új funkció kiadásával több verzió létrehozására lesz szükség. Végül az új, felhasználóbarát felhasználói felület leegyszerűsíti a felhasználói folyamatok kiválasztását és létrehozását. Próbálja ki most egy [felhasználói folyamat létrehozásával.](../../active-directory-b2c/tutorial-create-user-flows.md) 

További információ a felhasználói folyamatokról: Felhasználói folyamatok verziói [a Azure Active Directory B2C.](../../active-directory-b2c/user-flow-versions.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2020. július

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Külső fél integrációja
 
2020 júliusában a következő 55 új alkalmazást adtunk hozzá az alkalmazásgyűjteményünkbe összevonási támogatással:

[Clap Your Hands](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beekast,](https://my.beekast.com/) [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects](https://msteams.peterconnects.com/)fogadós , [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom,](https://start.cocoom.com/) [A COCO Construction Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [Reflekt,](https://reflekt.konsolute.com/login) [Rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Rendszerképfájl,](../saas-apps/titanfile-tutorial.md)Könyvtár, [SolarWinds Orion,](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US) [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [Datasite,](../saas-apps/datasite-tutorial.md) [BlogIn](../saas-apps/blogin-tutorial.md), [IntSights](../saas-apps/intsights-tutorial.md), [kpifire](../saas-apps/kpifire-tutorial.md), [Textline](../saas-apps/textline-tutorial.md), [Cloud Academy - SSO,](../saas-apps/cloud-academy-sso-tutorial.md)Community [Spark,](../saas-apps/community-spark-tutorial.md) [Chatwork](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Hand Data Hub Single Sign-On,](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md) [Egress,](../saas-apps/egress-tutorial.md) [SendSafely,](../saas-apps/sendsafely-tutorial.md) [Eletive,](https://app.eletive.com/) [Right-Hand Cybersecurity ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [Verme](../saas-apps/verme-tutorial.md), [Lenses.io](../saas-apps/lensesio-tutorial.md), [Momenta](../saas-apps/momenta-tutorial.md), [Uprise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [TellMe Bot](https://tellme365liteweb.azurewebsites.net/), [Inspire,](https://app.inspiresoftware.com/) [Maverics Identity Orchestrator SAML Connector,](https://www.strata.io/identity-fabric/)Smart [school management system,](https://smartschoolz.com/login) [Zepto –](https://user.zepto-ai.com/signin)Intelligens időmérő , [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md) [](../saas-apps/wootric-tutorial.md)

Az összes alkalmazás dokumentációját itt is megtalálja https://aka.ms/AppsTutorial

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos részleteket itt olvashatja el https://aka.ms/AzureADAppRequest

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Szerepkör-hozzárendelések megtekintése az összes hatókörben és azok csv-fájlba való letöltésének képessége

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Azure AD-szerepkörök  
**Termékképesség:** Access Control
 
Mostantól megtekintheti a szerepkör-hozzárendeléseket a szerepkör összes hatókörében az Azure AD portál "Szerepkörök és rendszergazdák" lapján. Az egyes szerepkör-hozzárendeléseket egy CSV-fájlba is letöltheti. A szerepkör-hozzárendelések megtekintésével és hozzáadásával kapcsolatos útmutatásért lásd: Rendszergazdai szerepkörök megtekintése és hozzárendelése a [Azure Active Directory.](../roles/manage-roles-portal.md)
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Az Azure Multi-Factor Authentication szoftverfejlesztés (Azure MFA SDK) elavult

**Írja be a következőt:** Elavult  
**Szolgáltatáskategória:** Mfa  
**Termékképesség:** Identity Security & Protection
 
Az Azure Multi-Factor Authentication szoftverfejlesztés (Azure MFA SDK) 2018. november 14-én érte el az életciklus végét, ahogy azt először 2017 novemberében bejelentettük. A Microsoft 2020. szeptember 30-án leállítja az SDK szolgáltatást. Az SDK-ra tett összes hívás sikertelen lesz.

Ha a szervezete az Azure MFA SDK-t használja, 2020. szeptember 30-ig kell áttérnie:
- MiM-hez készült Azure MFA SDK: Ha az SDK-t a MIM-mel együtt használja, miben kell áttérni az Azure MFA-kiszolgáló- és Privileged Access Management (PAM) aktiválásához a következő utasítások [szerint.](/microsoft-identity-manager/working-with-mfaserver-for-mim)   
- Azure MFA SDK testreszabott alkalmazásokhoz: Érdemes lehet integrálni az alkalmazást az Azure AD-be, és feltételes hozzáféréssel kikényszeríteni az MFA-t. Első lépésekért tekintse át ezt az [oldalt.](../manage-apps/plan-an-application-integration.md) 

---

## <a name="june-2020"></a>2020. június 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Felhasználói kockázati feltétel a feltételes hozzáférési szabályzatban

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Feltételes hozzáférés  
**Termékképesség:** Identity Security & Protection
 

A felhasználói kockázat támogatása az Azure AD feltételes hozzáférési szabályzatában lehetővé teszi, hogy több felhasználói kockázatalapú szabályzatot hozzon létre. A különböző felhasználók és alkalmazások esetében különböző minimális felhasználói kockázati szintekre lehet szükség. A felhasználói kockázat alapján létrehozhat olyan szabályzatokat, amelyek letiltják a hozzáférést, megkövetelik a többtényezős hitelesítést, biztonságos jelszóváltozást igényelnek, vagy átirányítják a Microsoft Cloud App Security-be a munkamenet-szabályzat kényszerítése érdekében, például további naplózást.

A felhasználói kockázati feltételhez prémium szintű Azure AD P2 szükséges, mivel az Azure Identity Protectiont használja, amely egy P2 ajánlat. A feltételes hozzáféréssel kapcsolatos további információkért tekintse meg az [Azure AD feltételes hozzáférés dokumentációját.](../conditional-access/index.yml)

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>Az SAML SSO mostantól támogatja azokat az alkalmazásokat, amelyek megkövetelik az SPNameQualifier beállítását, amikor a rendszer kéri

**Írja be a következőt:** Rögzített  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Sso
 
Egyes SAML-alkalmazások esetében az SPNameQualifier paramétert kell visszaadni a helyességi feltétel tárgyában, amikor a kérelemre szükség van. Az Azure AD most megfelelően válaszol, ha a kérelem NameID szabályzatában SPNameQualifier kérést kér. Ez az SP által kezdeményezett bejelentkezéshez is működik, és az idP által kezdeményezett bejelentkezés követi.  Az SAML-protokollról a Azure Active Directory saml protokoll Sign-On [olvashat bővebben.](../develop/single-sign-on-saml-protocol.md)

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Az Azure AD B2B-együttműködés támogatja MSA- és Google-felhasználók meghívását Azure Government bérlőkben

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 

Azure Government B2B együttműködési funkciókat használó bérlők mostantól microsoftos vagy Google-fiókkal is meghívhat felhasználókat. Annak kiderítéhez, hogy a bérlő használhatja-e ezeket a képességeket, kövesse a Honnan tudom, hogy a B2B-együttműködés elérhető-e az Usa-beli [Azure Government-bérlőmben?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Az MS Graph v1 felhasználói objektuma mostantól externalUserState és externalUserStateChangedDateTime tulajdonságokat is tartalmaz

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 

Az externalUserState és az externalUserStateChangedDateTime tulajdonsággal megkeresheti azokat a meghívott B2B-vendégfelhasználókat, akik még nem fogadták el a meghívásukat, valamint automatizálást is építhet, például törölhet olyan felhasználókat, akik néhány nap után nem fogadták el a meghívásukat. Ezek a tulajdonságok már elérhetők az MS Graph v1-ben. A tulajdonságok használatával kapcsolatos útmutatásért tekintse meg a Felhasználói [erőforrástípust.](/graph/api/resources/user)
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Általánosan elérhető a hitelesítési munkamenetek kezelése az Azure AD feltételes hozzáférésben

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Feltételes hozzáférés  
**Termékképesség:** Identity Security & Protection
 
A hitelesítési munkamenet-kezelési képességek lehetővé teszik annak beállítását, hogy a felhasználóknak milyen gyakran kell megadniuk a bejelentkezési hitelesítő adatokat, és hogy meg kell-e adniuk a hitelesítő adatokat a böngészők bezárása és újbóli megnyitása után, hogy nagyobb biztonságot és rugalmasságot nyújtson a környezetben.
 
Emellett a hitelesítési munkamenetek kezelése csak az Azure AD-hez csatlakozott, a hibrid Azure AD-hez csatlakozott és az Azure AD-ben regisztrált eszközök first Factor Authentication-hitelesítésére vonatkozik. A hitelesítési munkamenet kezelése mostantól az MFA-hoz is érvényes lesz. További információ: [Hitelesítési munkamenet-kezelés konfigurálása feltételes hozzáféréssel.](../conditional-access/howto-conditional-access-session-lifetime.md)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2020. június

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Külső fél integrációja
 
2020 júniusában a következő 29 új alkalmazást adva az alkalmazásgyűjteményünkbe összevonási támogatással:

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [Market](../saas-apps/bullseyetdp-tutorial.md)Automation, [Raketa](../saas-apps/raketa-tutorial.md), [Segment,](../saas-apps/segment-tutorial.md) [Ai Auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [Proto.io](../saas-apps/proto.io-tutorial.md), [Gatekeeper,](https://www.gatekeeperhq.com/)Hub [Planner](../saas-apps/hub-planner-tutorial.md), [Ansira-Partner Go-to-Market Toolbox](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation on Cloud](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [Kisi Physical Security](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics,](../saas-apps/pymetrics-tutorial.md) [Zero,](https://www.teamzero.com/) [InStation,](https://instation.invillia.com/) [edX for Business SAML 2.0 Integration,](../saas-apps/edx-for-business-saml-integration-tutorial.md) [MOOC Office 365,](https://mooc.office365-training.com/en/) [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning platform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [Field iD](../saas-apps/field-id-tutorial.md), [Curricula SAML](../saas-apps/curricula-saml-tutorial.md), [Perforce Helix Core - Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md), [MyCompliance Cloud](https://cloud.metacompliance.com/), [Smallstep SSH](https://smallstep.com/sso-ssh/)  

Az összes alkalmazás dokumentációját itt https://aka.ms/AppsTutorial találja: . Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásához olvassa el a részleteket itt: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>Az önkiszolgáló External Identities API-összekötők nyilvános előzetes verzióban megjelenik

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 
External Identities API-összekötők lehetővé teszik a webes API-k használatát az önkiszolgáló regisztráció külső felhőrendszerekkel való integrálásához. Ez azt jelenti, hogy mostantól meghívhat webes API-kat a bejelentkezési folyamat adott lépéseiként, hogy elindítsa a felhőalapú egyéni munkafolyamatokat. Az API-összekötők például a következőre használhatók:

- Integrálás egyéni jóváhagyási munkafolyamatokkal.
- Identitás-ellenőrzés végrehajtása
- Felhasználói beviteli adatok ellenőrzése
- Felhasználói attribútumok felülírása
- Egyéni üzleti logika futtatása

További információ az API-összekötők által elérhető összes felületről: [API-összekötők](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)használata az önkiszolgáló regisztráció testreszabásához és kiterjesztéséhez, vagy Az External Identities önkiszolgáló regisztráció testreszabása webes API-integrációkkal. [](../external-identities/api-connectors-overview.md)
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Igény szerinti kiépítés és a felhasználók másodpercek alatt az alkalmazásokba való beépítése

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése
 
Az Azure AD kiépítési szolgáltatás jelenleg ciklikusan működik. A szolgáltatás 40 perc alatt fut le. Az [igény szerinti kiépítési](https://aka.ms/provisionondemand) képesség lehetővé teszi, hogy másodpercek alatt kiválasztsa a felhasználót, és kiépítse őket. Ez a képesség lehetővé teszi a kiépítési problémák gyors elhárítását anélkül, hogy újraindítást kell indítania a kiépítési ciklus újraindításának kényszerítés érdekében. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Új engedély az Azure AD-jogosultságkezeléshez a Graphban

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Más  
**Termékképesség:** Jogosultságkezelés
 
Az EntitlementManagement.Read.All új delegált engedély mostantól használható a bétaverzió Alkalmazáskezelés API jogosultsági Microsoft Graph számára. További információ az elérhető API-król: [Az Azure AD](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta&preserve-view=true)jogosultságkezelési API használata.

---

### <a name="identity-protection-apis-available-in-v10"></a>Az Identity Protection API-k elérhetők az 1.0-s

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Identity Protection  
**Termékképesség:** Identity Security & Protection
 
Általánosan elérhetők a riskyUsers és a riskDetections Microsoft Graph API-k. Most, hogy elérhetők az 1.0-s végponton, meghívjuk, hogy éles környezetben használja őket. További információt a következő dokumentumokban [Microsoft Graph meg:](/graph/api/resources/identityprotectionroot).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Mostantól általánosan elérhetők a bizalmasság címkék Microsoft 365 a szabályzatok alkalmazásához

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Csoportkezelés  
**Termékképesség:** Együttműködés
 

Mostantól bizalmasság címkéket hozhat létre, és a címkebeállítások használatával szabályzatokat alkalmazhat a Microsoft 365 csoportokra, beleértve az adatvédelmi (nyilvános vagy privát) és a külső felhasználói hozzáférési szabályzatokat. Létrehozhat egy privát adatvédelmi szabályzatot és külső felhasználói hozzáférési szabályzatot, amely nem engedélyezi a vendégfelhasználók hozzáadását. Amikor egy felhasználó alkalmazza ezt a címkét egy csoportra, a csoport privát lesz, és nem lehet vendégfelhasználót hozzáadni a csoporthoz. 

A bizalmasság címkék fontos szerepet játszik az üzletileg kritikus adatok védelmében, valamint a csoportok nagy léptékű, megfelelő és biztonságos kezelésében. A bizalmasság címkék használatával kapcsolatos útmutatásért lásd: Bizalmasság címkék hozzárendelése Microsoft 365 csoportokhoz a [Azure Active Directory (előzetes verzió) .](../enterprise-users/groups-assign-sensitivity-labels.md)
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Frissítések az ügyfelek Microsoft Identity Manager prémium szintű Azure AD támogatásához

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Microsoft Identity Manager  
**Termékképesség:** Identitás-életciklus kezelése
 
Azure ügyfélszolgálata a 2016-os Microsoft Identity Manager Azure AD-integrációs összetevőihez a 2016-os kibővített támogatás Microsoft Identity Manager érhető el. További információ: [Támogatási frissítés prémium szintű Azure AD ügyfelek számára a Microsoft Identity Manager.](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>Növekedett a csoporttagsági feltételek használata az SSO-jogcímek konfigurációjában

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Sso
 
Korábban a jogcímek csoporttagság alapján bármely alkalmazáskonfiguráción belüli feltételes módosításakor használható csoportok száma 10-re volt korlátozva. A csoporttagsági feltételek használata az SSO-jogcímek konfigurációjában mostantól legfeljebb 50 csoportra nőtt. További információ a jogcímek konfigurálásról: Vállalati alkalmazások [SSO-jogcímkonfigurációja.](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions) 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Alapszintű formázás engedélyezése a Sign In Page Text (Bejelentkezési oldal szövege) összetevőn a Vállalati védjegyezésben.

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Felhasználóhitelesítés
 
Az Azure AD/Microsoft 365 bejelentkezési élmény vállalati védjegyezés funkciójának frissítése lehetővé teszi, hogy az ügyfél hivatkozásokat és egyszerű formázást adjon hozzá, beleértve a félkövér betűtípust, az aláhúzást és a dőlt betűket. A funkció használatával kapcsolatos útmutatásért lásd: Védjegyezés hozzáadása a Azure Active Directory [bejelentkezési oldalhoz.](./customize-branding.md)

---

### <a name="provisioning-performance-improvements"></a>A kiépítési teljesítmény fejlesztései

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése
 
A kiépítési szolgáltatás frissítve lett, hogy csökkentse [a](../app-provisioning/how-provisioning-works.md#incremental-cycles) növekményes ciklus befejezéséhez szükséges időt. Ez azt jelenti, hogy a felhasználók és csoportok gyorsabban lesznek kiépítve az alkalmazásokban, mint korábban. A 2020. 06. 10. után létrehozott összes új kiépítési feladat automatikusan kihasználja a teljesítménybeli fejlesztéseket. A 2020. 06. 10. előtt kiépítésre konfigurált alkalmazásokat 2020. 06. 10. után újra kell indítani a teljesítménybeli fejlesztések kihasználása érdekében. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Az ADAL és az MS Graph Parity elalasztásának bejelentése

**Írja be a következőt:** Elavult  
**Szolgáltatáskategória:** N/a  
**Termékképesség:** Eszközök életciklus-kezelése

Most, hogy a Microsoft hitelesítési kódtárak (MSAL) elérhetők, nem adjuk hozzá az új szolgáltatásokat az Azure Active Directory Authentication Libraries (ADAL) szolgáltatáshoz, és 2022. június 30-án véget érnek a biztonsági javítások. További információ az MSAL-be való áttelepítésről: Alkalmazások áttelepítése [a Microsoft Authentication Librarybe (MSAL).](../develop/msal-migration.md)

Emellett befejeztük az Azure AD Graph összes funkcióját elérhetővé tenni az MS Graph-ban. Így az Azure AD Graph API-k csak hibajavításokat és biztonsági javításokat kapnak 2022. június 30-ig. További információ: [Az](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363) alkalmazások frissítése a Microsoft Authentication Library és a Microsoft Graph API használatára
 
---
## <a name="may-2020"></a>2020. május

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Tulajdonságok elajánlása a signIns, a riskyUsers és a riskDetections API-kban

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Identity Protection  
**Termékképesség:** Identity Security & Protection

Jelenleg az enumerált típusok a riskType tulajdonságot képviselik a riskDetections API-ban és a riskyUserHistoryItem (előzetes verzióban). Az enumerált típusok a signIns API riskEventTypes tulajdonságához is használhatók. Ezeket a tulajdonságokat sztringekként fogjuk ábrázolni. 

Az ügyfeleknek 2020. szeptember 9-ig át kell állnunk a riskEventType tulajdonságra a béta riskDetections és a riskyUserHistoryItem API-ban, valamint a beta signIns API riskEventTypes_v2 tulajdonságára. Ezen a napon az aktuális riskType és riskEventTypes tulajdonságokat fogjuk kivenni. További információ: A kockázati események tulajdonságainak módosítása [és Az Identity Protection API-k a Microsoft Graph.](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>A riskEventTypes tulajdonság elalasztása a signIns 1.0-s verziós API-ban a Microsoft Graph

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Jelentési  
**Termékképesség:** Identity Security & Protection

Az enumerált típusok sztringtípusokra váltnak, amikor 2020 szeptemberében a kockázati események tulajdonságait Microsoft Graph képviselik. Az előzetes verziójú API-k befolyásolása mellett ez a módosítás az éles környezetben való signIns API-ra is hatással lesz.

Bevezettünk egy új riskEventsTypes_v2 (sztring) tulajdonságot a signIns v1.0 API-hoz. Az aktuális riskEventTypes (enum) tulajdonságot 2022. június 11-én kivezetjük az Microsoft Graph szabályzatnak megfelelően. Az ügyfeleknek 2022. június 11-ig át kell riskEventTypes_v2 a signIns API 1.0-s verzióban található riskEventTypes_v2 tulajdonságra. További információért tekintse meg a riskEventTypes tulajdonság elalasztása a következőn: [signIns v1.0 API a Microsoft Graph.](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Az MFA e-mail-értesítések várható változásai

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Mfa  
**Termékképesség:** Identity Security & Protection
 

A felhőbeli MFA e-mail-értesítései a következő módosításokat végrehajtották:

Az e-mail-értesítések a következő címről lesznek azure-noreply@microsoft.com elküldve: és msonlineservicesteam@microsoftonline.com . Frissítjük a csalási riasztások e-mailjeinek tartalmát, hogy jobban jelezve legyen a használat letiltásának feloldásához szükséges lépések.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Új önkiszolgáló regisztráció az összevont tartományokban található olyan felhasználók számára, akik nem tudnak hozzáférni a Microsoft Teamshez, mert nincsenek szinkronizálva az Azure Active Directory.

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Felhasználóhitelesítés
 

Jelenleg az Azure AD-ban összevont tartományokban lévő, de a bérlővel nem szinkronizált felhasználók nem férhetnek hozzá a Teamshez. Ez az új funkció június végével lehetővé teszi, hogy ezt a meglévő, e-mailben ellenőrzött regisztráció funkció kiterjesztésével tegye meg. Ez lehetővé teszi, hogy azok a felhasználók, akik bejelentkeznek egy összevont identitásszolgáltatóba, de még nincs felhasználói objektumuk az Azure ID-ban, automatikusan létrehoznak egy felhasználói objektumot, és hitelesítsék magukat a Teamsben. A felhasználói objektum "önkiszolgáló regisztrációként" lesz megjelölve. Ez annak a meglévő képességnek a kiterjesztése, amely lehetővé teszi az e-mailben ellenőrzött önkiszolgáló regisztrációt, hogy a felügyelt tartományok felhasználói képesek és vezérelhetőek ugyanazokkal a jelzővel. Ez a változás a következő két hónap során fog befejeződni. A dokumentáció frissítéseit itt [találhatja meg.](../enterprise-users/directory-self-service-signup.md)
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Közelgő javítás: Folyamatban van az Azure Government-felhő OIDC-felderítési dokumentumának frissítése a megfelelő Graph-végpontokra való hivatkozáshoz.

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Szuverén felhők  
**Termékképesség:** Felhasználóhitelesítés
 
Júniustól az [OIDC](../develop/authentication-national-cloud.md) felderítési dokumentuma a [Microsoft](../develop/v2-protocols-oidc.md) identitásplatformját és OpenID Connect protokollt a Azure Government felhővégponton (login.microsoftonline.us) kezdi visszaadni a megfelelő [Országos](/graph/deployments) felhőgráf végpontot ( vagy ) a megadott bérlő https://graph.microsoft.us https://dod-graph.microsoft.us) alapján.  Jelenleg a helytelen Graph-végpontot (graph.microsoft.com) "msgraph_host" mezőt tartalmazza.  

Ezt a hibajavítást fokozatosan, körülbelül 2 hónapig vezetjük be.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government felhasználók nem fognak tudni bejelentkezni a login.microsoftonline.com

**Írja be a következőt:** A módosítás megterve  
**Szolgáltatáskategória:** Szuverén felhők  
**Termékképesség:** Felhasználóhitelesítés
 
2018. június 1-től a hivatalos Azure Active Directory (Azure AD) authority for Azure Government -ről -re https://login-us.microsoftonline.com https://login.microsoftonline.us változott. Ha egy saját alkalmazással rendelkezik egy Azure Government-bérlőn belül, frissítenie kell az alkalmazást, hogy bejelentkeztetje a felhasználókat az .us végponton.

Május 5-től kezdődően az Azure AD megkezdi a végpontváltás kényszerítét, ami letiltja, hogy Azure Government felhasználók Azure Government bérlőkben üzemeltetett alkalmazásokba a nyilvános végpont (microsoftonline.com) használatával bejelentkeznek. Az érintett alkalmazásoknál az AADSTS900439 – USGClientNotSupportedOnPublicEndpoint hibaüzenet jelenik meg. 

Ezt a változást fokozatosan vezetjük be, és a kényszerítés várhatóan minden alkalmazásra 2020 júniusában befejeződik. További részletekért tekintse meg az Azure Government [blogbejegyzését.](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>Az SAML single Logout kérés mostantól a megfelelő formátumban küldi el a NameID-t

**Írja be a következőt:** Rögzített  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Felhasználóhitelesítés
 
Amikor egy felhasználó a kijelentkezésre kattint (például a MyApps portálon), az Azure AD SAML egyszeri kijelentkezés üzenetet küld minden olyan alkalmazásnak, amely aktív a felhasználói munkamenetben, és konfigurálva van egy kijelentkezés URL-címe. Ezek az üzenetek egy névazonosítót tartalmaznak állandó formátumban.

Ha az eredeti SAML bejelentkezési jogkivonat más formátumot használt a NameID azonosítóhoz (pl. e-mail/UPN), akkor az SAML-alkalmazás nem tudja korrelálni a névazonosítót a kijelentkezés üzenetében egy meglévő munkamenettel (mivel a két üzenetben használt névazonosítók eltérőek), ami miatt az SAML-alkalmazás elvette a kijelentkezés üzenetét, és a felhasználónak be kell jelentkeznie. Ez a javítás konzisztenssé teszi a kijelentkező üzenetet az alkalmazáshoz konfigurált NameID-val.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>A hibrid identitás-rendszergazdai szerepkör mostantól elérhető a felhőalapú jogosultságlétrehozásban

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Azure AD Felhőalapú kiépítés  
**Termékképesség:** Identitás-életciklus kezelése
 
A rendszergazdák az új "Hibrid rendszergazda" szerepkört kezdhetik a felhőalapú jogosultságok üzembe Azure AD Connect szerepkörként. Ezzel az új szerepkörsel már nem kell globális rendszergazdai szerepkört használnia a felhőalapú jogosultságok építésének beállításához és konfigurálásához. [További információ](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2020. május

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Külső fél integrációja
 
2020 májusában a következő 36 új alkalmazást adva az alkalmazásgyűjteményünkbe összevonási támogatással:

[Moula,](https://moula.com.au/pay/merchants) [Surveypal,](https://www.surveypal.com/app) [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox,](http://www.tacklebox.app/) [Teams,](https://powell-software.com/en/powell-teams-en/) [Talentsoft Assistant,](https://msteams.talent-soft.com/) [ASC Recording Insights,](https://teams.asc-recording.app/product) [GO1,](https://www.go1.com/) [B-Engaged,](https://b-engaged.se/) [Competella Contact Center Workgroup](http://www.competella.com/), [Asite,](http://www.asite.com/) [ImageSoft Identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [insuite](../saas-apps/insuite-tutorial.md), change [process management](../saas-apps/change-process-management-tutorial.md), [Cyara CX Assurance Platform,](../saas-apps/cyara-cx-assurance-platform-tutorial.md) [Smart Global Governance,](../saas-apps/smart-global-governance-tutorial.md) [Prezi](../saas-apps/prezi-tutorial.md), [Mapbox,](../saas-apps/mapbox-tutorial.md) [Datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Whimsical](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO for Confluence](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO for BitBucket, EasySSO](../saas-apps/easysso-for-bitbucket-tutorial.md) [for Lehet](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii](../saas-apps/torii-tutorial.md), [Aldd Cloud](../saas-apps/axiad-cloud-tutorial.md), [Humanage](../saas-apps/humanage-tutorial.md), [ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md), [CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md), [ShareVault](../saas-apps/sharevault-tutorial.md), [Vyond](../saas-apps/vyond-tutorial.md), [TextExpdent](../saas-apps/textexpander-tutorial.md), [Anyone Home CRM](../saas-apps/anyone-home-crm-tutorial.md), [askSpoke](../saas-apps/askspoke-tutorial.md), [ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

Az összes alkalmazás dokumentációját itt https://aka.ms/AppsTutorial találja: .

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos részleteket itt olvashatja https://aka.ms/AzureADAppRequest el.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Általánosan elérhető a feltételes hozzáférés csak jelentési módja

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Feltételes hozzáférés  
**Termékképesség:** Identity Security & Protection

[Az Azure AD](../conditional-access/concept-conditional-access-report-only.md) feltételes hozzáférés csak jelentési módja lehetővé teszi a szabályzatok eredményének kiértékelését a hozzáférés-vezérlés kényszerítése nélkül. Tesztelheti a csak jelentéskészítési szabályzatokat a szervezeten belül, és még azelőtt megértheti azok hatását, hogy engedélyezné őket, így biztonságosabbá és egyszerűbbé teszi az üzembe helyezést. Az elmúlt néhány hónapban a csak jelentési mód erős bevezetésével láttuk el, hogy több mint 26 millió felhasználó már egy csak jelentésre vonatkozó szabályzat hatókörében van. A mai bejelentéssel az új Azure AD feltételes hozzáférési szabályzatok alapértelmezés szerint csak jelentési módban lesznek létrehozva. Ez azt jelenti, hogy a szabályzatok hatását a létrehozás pillanatában monitorhatja. Azok számára pedig, akik az MS Graph [API-kat](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) használják, programozott módon kezelhetik a csak jelentésre vonatkozó szabályzatokat is. 

---

### <a name="self-service-sign-up-for-guest-users"></a>Önkiszolgáló regisztráció vendégfelhasználók számára

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 
Az External Identities Azure AD-ban lehetővé teheti a szervezeten kívüli személyek számára, hogy hozzáférjenek az alkalmazásokhoz és az erőforrásokhoz, miközben bármilyen identitással bejelentkeznek. Ha külső felhasználókkal oszt meg egy alkalmazást, előfordulhat, hogy nem mindig tudja előre, hogy kinek lesz szüksége az alkalmazáshoz való hozzáférésre. Az [önkiszolgáló regisztrációval](../external-identities/self-service-sign-up-overview.md)engedélyezheti a vendégfelhasználóknak, hogy regisztrálnak és vendégfiókot szerezzenek az üzletági (LOB) alkalmazásokhoz. A regisztrációt az Azure AD és a közösségi identitások támogatása szerint lehet létrehozni és testre szabni. A regisztráció során további információkat is gyűjthet a felhasználóról.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Általánosan elérhető a feltételes hozzáférési elemzések és a jelentéskészítési munkafüzet

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Feltételes hozzáférés  
**Termékképesség:** Identity Security & Protection

Az [elemzési és jelentéskészítési munkafüzet](../conditional-access/howto-conditional-access-insights-reporting.md) összefoglaló nézetet biztosít a rendszergazdák számára az Azure AD feltételes hozzáférésről a bérlőjükben. Az egyes szabályzatok kiválasztásának lehetőségével a rendszergazdák jobban megértheti az egyes szabályzatok funkcióját, és valós időben figyelheti a változásokat. A munkafüzet a következő utasítások Azure Monitor streamel adatokat, amelyeket néhány perc alatt [beállíthat.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) Az irányítópultot az Azure AD feltételes hozzáférés menüjének új elemzési és jelentéskészítési lapjára költöztetjük, hogy jobban látható legyen.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A feltételes hozzáférés Szabályzat részletei panelje nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Feltételes hozzáférés  
**Termékképesség:** Identity Security & Protection

Az új [szabályzat részletei panelen](../conditional-access/troubleshoot-conditional-access.md) megjelenik a feltételes hozzáférési szabályzat kiértékelése során teljesült hozzárendelések, feltételek és vezérlők. A panel eléréséhez válasszon ki egy sort a Feltételes hozzáférés vagy a Csak jelentés lapon a Bejelentkezési adatok között.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>A címtárobjektumok új lekérdezési lehetőségei Microsoft Graph nyilvános előzetes verzióban biztosítanak

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** AZ MS Graph **termékképessége:** Fejlesztői élmény

Új képességeket vezetünk be a Microsoft Graph Objektumok API-khoz, amelyek lehetővé teszik a Darabszám, a Keresés, a Szűrés és a Rendezési műveleteket. Ez lehetővé teszi a fejlesztők számára a címtárobjektumok gyors lekérdezését olyan megkerülő megoldások nélkül, mint a memóriában való szűrés és rendezés. További információt ebben a [blogbejegyzésben talál.](https://aka.ms/CountFilterMSGraphAAD)

Jelenleg nyilvános előzetes verzióban vagyunk, és visszajelzést keresünk. Kérjük, küldje el megjegyzéseit ezzel a rövid [felméréssel.](https://aka.ms/MsGraphAADSurveyDocs)

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>SAML-alapú egyszeri bejelentkezés konfigurálása az Microsoft Graph API-val (bétaverzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Sso
 
Mostantól támogatott az alkalmazások létrehozása és konfigurálása az Azure AD-katalógusból az MS Graph API-k használatával a bétaverzióban. Ha SAML-alapú egyszeri bejelentkezést kell beállítania egy alkalmazás több példányához, időt takaríthat meg, ha a Microsoft Graph API-okkal automatizálja az [SAML-alapú egyszeri bejelentkezés konfigurációját.](/graph/application-saml-sso-configure-api)
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – 2020. május

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Külső fél integrációja
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic a szervezettől](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infrastructure-console-provisioning-tutorial.md)

A szervezet automatizált felhasználói fiókok automatikus kiépítése által történő biztonságossá teről további információért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val.](../app-provisioning/user-provisioning.md)

---

### <a name="saml-token-encryption-is-generally-available"></a>Az SAML-jogkivonat titkosítása általánosan elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Sso
 
[Az SAML-jogkivonat titkosítása](../manage-apps/howto-saml-token-encryption.md) lehetővé teszi, hogy az alkalmazások titkosított SAML-helyességi adatokat fogadjanak. A funkció mostantól általánosan elérhető az összes felhőben.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Az alkalmazás-jogkivonatok csoportnév-jogcímei általánosan elérhetők

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Sso
 
A jogkivonatban kiadott csoportos jogcímek mostantól korlátozhatók az alkalmazáshoz rendelt csoportokra.  Ez különösen akkor fontos, ha a felhasználók nagy számú csoport tagjai, és fennállt a tokenméretkorlátok túllépése kockázata. Ezzel az új képességgel általánosan elérhető a csoportnevek [jogkivonathoz](../hybrid/how-to-connect-fed-group-claims.md) való hozzáadása.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>A Workday Writeback mostantól támogatja a munkahelyi telefonszám attribútumainak beállítását

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése
 
Továbbfejlesztjük a Workday Writeback kiépítési alkalmazást, hogy mostantól támogassa a munkahelyi telefonszámok és mobilszám-attribútumok visszaírását. Mostantól az e-mail-cím és a felhasználónév mellett a Workday Writeback kiépítési alkalmazást is konfigurálhatja úgy, hogy telefonszámértékeket küldjön az Azure AD-ről a Workdayre. A telefonszám-visszaírás konfigurálási részleteiért tekintse meg a [Workday Writeback alkalmazás oktatóanyagát.](../saas-apps/workday-writeback-tutorial.md) 

---

### <a name="publisher-verification-preview"></a>Közzétevő ellenőrzése (előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Más  
**Termékképesség:** Fejlesztői élmény
 
A közzétevő ellenőrzése (előzetes verzió) segít a rendszergazdáknak és a végfelhasználóknak megérteni a Microsoft identitásplatformjával integrált alkalmazásfejlesztők hitelességét. Részletekért lásd: [Közzétevő ellenőrzése (előzetes verzió).](../develop/publisher-verification-overview.md)
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Engedélyezési kódfolyam egyoldalas alkalmazásokhoz

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Hitelesítési **termékképesség:** Fejlesztői élmény

A böngészők külső [cookie-kra](../develop/reference-third-party-cookies-spas.md)vonatkozó modern korlátozásai, például a Safari ITP miatt az SPA-knak az engedélyezési kódfolyamot kell használniuk az implicit folyamat helyett az SSO karbantartásához; MSAL.js 2.x-es virtuális gép mostantól támogatja az engedélyezési kódfolyamot. Az alkalmazás megfelelő frissítései Azure Portal hogy az SPA-t "spa" típusúra frissítse, és használja a hitelesítési kódfolyamot. Útmutatásért lásd: Rövid útmutató: Felhasználók bejelentkezése és hozzáférési jogkivonat beszerzése [JavaScript SPA-ban a hitelesítési kódfolyam használatával.](../develop/quickstart-v2-javascript-auth-code.md)

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Továbbfejlesztett eszközszűrés nyilvános előzetes verzióban

**Írja be a következőt:** Módosított funkció   
**Szolgáltatáskategória:** Eszközkezelés **termékképesség: Eszközök** életciklus-kezelése
 
Korábban csak az "Engedélyezve" és a "Tevékenység dátuma" szűrőt lehetett használni. Mostantól további [tulajdonságokra](../devices/device-management-azure-portal.md#device-list-filtering-preview)is szűrheti az eszközök listáját, beleértve az operációs rendszer típusát, az illesztés típusát, a megfelelőséget stb. Ezeknek a kiegészítésnek egyszerűsítenie kell egy adott eszköz megkeresét.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Általánosan elérhető Alkalmazásregisztrációk új Azure AD B2C felhasználói élmény

**Írja be a következőt:** Módosított funkció   
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** Identitás-életciklus kezelése
 
Az új Alkalmazásregisztrációk felhasználói élmény Azure AD B2C általánosan elérhető. 

Korábban a B2C felhasználói alkalmazásait a többi alkalmazástól elkülönítve kellett kezelnie az örökölt "Alkalmazások" felhasználói élmény használatával. Ez különböző alkalmazás-létrehozási élményt jelentett az Azure különböző helyei között.

Az új funkció egy helyen jeleníti meg az összes B2C-alkalmazásregisztrációt és az Azure AD-alkalmazásregisztrációt, és egységes módon kezeli őket. Akár ügyfélalkalmazást, akár Microsoft Graph Azure AD B2C-erőforrások programozott kezeléséhez hozzáférő alkalmazást kell kezelnie, csak egy módszerre van szüksége.

Az új felhasználói élmény eléréséhez navigálhat a Azure AD B2C szolgáltatásban, és válassza a Alkalmazásregisztrációk panelt. A felhasználói élmény az Azure Active Directory is elérhető.

A Azure AD B2C Alkalmazásregisztrációk az Azure AD-bérlők általános alkalmazásregisztrációs élményére épül, de az alkalmazások Azure AD B2C. [](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) Az örökölt "Alkalmazások" felhasználói élmény a jövőben elavult lesz.

További információért látogasson el az [Új alkalmazásregisztrációs élmény a Azure AD B2C.](../../active-directory-b2c/app-registrations-training-guide.md)

---
## <a name="april-2020"></a>2020. április

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Általánosan elérhető a kombinált biztonsági adatok regisztrálási élménye

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)

**Termékképesség:** Identity Security & Protection

Általánosan elérhető a Multi-Factor Authentication (MFA) és a Self-Service jelszó-visszaállítás (SSPR) kombinált regisztrációs élménye. Ez az új regisztrációs folyamat lehetővé teszi, hogy a felhasználók egyetlen, lépésenként regisztrálnak az MFA-re és az SSPR-re. Ha az új felhasználói élményt a szervezet számára telepíti, a felhasználók kevesebb idő alatt és kevesebb gond nélkül regisztrálnak. Tekintse meg a [blogbejegyzést itt.](https://bit.ly/3etiRyQ)

---

### <a name="continuous-access-evaluation"></a>folyamatos hozzáférés-kiértékelés

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)

**Termékképesség:** Identity Security & Protection

folyamatos hozzáférés-kiértékelés egy új biztonsági funkció, amely lehetővé teszi a szabályzatok közel valós idejű érvényesítését az Azure AD hozzáférési jogkivonatokat használó függő felek számára, amikor események történnek az Azure AD-ban (például a felhasználói fiókok törlésekor). Ezt a funkciót először a Teams- és Outlook-ügyfelek számára vezetjük be. További részletekért olvassa el blogunkat [és](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) [dokumentációunkat.](../conditional-access/concept-continuous-access-evaluation.md)

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS-bejelentkezés: Az első vonalbeli dolgozók a telefonszámuk és jelszavuk nélkül tudnak bejelentkezni az Azure AD-alapú alkalmazásokba

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)

**Termékképesség:** Felhasználóhitelesítés

Az Office olyan mobilos üzleti alkalmazások sorozatát bocsátja ki, amelyek nem hagyományos szervezeteknek és nagyvállalatok alkalmazottainak szolgálnak ki olyan üzleti alkalmazásokat, amelyek nem e-maileket használják elsődleges kommunikációs módszerként. Ezek az alkalmazások olyan frontline-alkalmazottakat, asztali dolgozókat, helyszíni ügynököket vagy kereskedelmi alkalmazottakat céloznak meg, akik esetleg nem kapják meg a munkáltatójuk e-mail-címét, hozzáférhetnek egy számítógéphez vagy az IT-hoz. Ezzel a projekttel ezek az alkalmazottak egy telefonszám megadásával és egy kód kerekítésének megadásával bejelentkeznek az üzleti alkalmazásokba. További részletekért tekintse meg a [rendszergazdai dokumentációt](../authentication/howto-authentication-sms-signin.md) és [a végfelhasználói dokumentációt.](../user-help/sms-sign-in-explainer.md)

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Belső felhasználók meghívása B2B-együttműködés használatára

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** B2b

**Termékképesség:**

Bővítjük a B2B meghívási képességet, hogy lehetővé tegye a meglévő belső fiókok meghívását a B2B együttműködési hitelesítő adatok további használatára. Ehhez át kell küldeni a felhasználói objektumot a Meghívás API-nak az olyan jellemző paraméterek mellett, mint a meghívott e-mail-cím. A felhasználó objektumazonosítója, UPN-e, csoporttagsága, alkalmazás-hozzárendelése stb. érintetlen marad, de a 2B-t a meghívó előtt használt belső hitelesítő adatok helyett a B2B használatával fogja hitelesíteni az otthoni bérlői hitelesítő adataival. Részletekért tekintse meg a [dokumentációt.](../external-identities/invite-internal-users.md)

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>Általánosan elérhető a feltételes hozzáférés csak jelentési módja

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Feltételes hozzáférés

**Termékképesség:** Identity Security & Protection

[Az Azure AD](../conditional-access/concept-conditional-access-report-only.md) feltételes hozzáférés csak jelentési módja lehetővé teszi a szabályzatok eredményének kiértékelését a hozzáférés-vezérlés kényszerítése nélkül. Tesztelheti a csak jelentéskészítési szabályzatokat a szervezeten belül, és még azelőtt megértheti azok hatását, hogy engedélyezné őket, így biztonságosabbá és egyszerűbbé teszi az üzembe helyezést. Az elmúlt néhány hónapban a csak jelentési mód erős bevezetésével láttuk el, hogy több mint 26 millió felhasználó már a csak jelentésre vonatkozó szabályzat hatókörében van. Ezzel a bejelentéssel az új Azure AD feltételes hozzáférési szabályzatok alapértelmezés szerint csak jelentési módban lesznek létrehozva. Ez azt jelenti, hogy a szabályzatok hatását a létrehozás pillanatában figyelheti. Azok számára pedig, akik az MS Graph API-kat használják, programozott módon kezelhetik a csak jelentésre vonatkozó [szabályzatokat.](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta&preserve-view=true) 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>A feltételes hozzáféréssel kapcsolatos elemzések és jelentéskészítési munkafüzet általánosan elérhető

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Feltételes hozzáférés

**Termékképesség:** Identity Security & Protection

A feltételes [hozzáféréssel kapcsolatos elemzések és jelentéskészítési munkafüzet](../conditional-access/howto-conditional-access-insights-reporting.md) összefoglaló nézetet biztosít a rendszergazdák számára az Azure AD feltételes hozzáférésről a bérlőjükben. Az egyes szabályzatok kiválasztásának képessége lehetővé teszi, hogy a rendszergazdák jobban megértsék, mire valók az egyes szabályzatok, és valós időben figyelik a változásokat. A munkafüzet a következő utasítások Azure Monitor streamel adatokat, amelyeket néhány perc alatt [beállíthat.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) Annak érdekében, hogy az irányítópult felderíthetőbb legyen, az Azure AD feltételes hozzáférés menüjének új elemzési és jelentéskészítési lapjára költöztetjük.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A feltételes hozzáférés Szabályzat részletei panelje nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Feltételes hozzáférés

**Termékképesség:** Identity Security & Protection

Az új [szabályzat részletei panelen](../conditional-access/troubleshoot-conditional-access.md) látható, hogy mely hozzárendelések, feltételek és vezérlők teljesültek a feltételes hozzáférési szabályzat kiértékelése során. A panel eléréséhez válasszon ki egy  sort  a Feltételes hozzáférés vagy a Csak jelentés lapon a Bejelentkezési adatok között.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Új összevont alkalmazások, amelyek a Azure AD alkalmazás 2020. áprilisában érhetők el

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Vállalati alkalmazások

**Termékképesség:** külső fél integrációja

2020 áprilisában az alábbi 31, összevonási támogatást támogató új alkalmazást adtunk hozzá az alkalmazásgyűjteményhez: 

[SincroPool Apps,](https://www.sincropool.com/) [SmartDB,](https://hibiki.dreamarts.co.jp/smartdb/trial/) [Float,](../saas-apps/float-tutorial.md) [LMS365,](https://lms.365.systems/) [IWT Procurement Suite](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni,](https://lunni.fi/) [EasySSO for Jira,](../saas-apps/easysso-for-jira-tutorial.md) [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki Dashboard](../saas-apps/meraki-dashboard-tutorial.md), Microsoft 365 [Mover,](https://app.mover.io/login) [Speaker Engage](https://speakerengage.com/login.php), [Monly](../saas-apps/honestly-tutorial.md), [Ally,Flow,](../saas-apps/ally-tutorial.md) [AlertMedia](../saas-apps/alertmedia-tutorial.md), [gr8 People](../saas-apps/gr8-people-tutorial.md), [Pendo,](../saas-apps/pendo-tutorial.md) [HighGround,](../saas-apps/highground-tutorial.md) [](https://app.dutyflow.nl/) [Sync,](../saas-apps/harmony-tutorial.md) [Timetabling Solutions](../saas-apps/timetabling-solutions-tutorial.md), [SynchroNet CLICK](../saas-apps/synchronet-click-tutorial.md), [empower](https://www.made-in-office.com/en/), [fortes Change Cloud](../saas-apps/fortes-change-cloud-tutorial.md), [Litmus,](../saas-apps/litmus-tutorial.md) [GroupTalk](https://recorder.grouptalk.com/), [Frontify](../saas-apps/frontify-tutorial.md), [MongoDB Cloud](../saas-apps/mongodb-cloud-tutorial.md), [TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md), [COCO](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Productivity Suite,](../saas-apps/nitro-productivity-suite-tutorial.md) [Trend Micro Web Security (TMWS)](../saas-apps/trend-micro-tutorial.md)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Microsoft Graph előzetes verzióhoz elérhető az oAuth2PermissionGrant Delta-lekérdezés támogatása

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** MS Graph

**Termékképesség:** Fejlesztői élmény

Az oAuth2PermissionGrant Delta-lekérdezése nyilvános előzetes verzióban érhető el! Most már nyomon követheti a módosításokat anélkül, hogy folyamatosan lekérdezné Microsoft Graph. [Részletek](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Microsoft Graph általánosan elérhető a változáslekérdezés támogatása a szervezeti kapcsolattartók számára

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** MS Graph

**Termékképesség:** Fejlesztői élmény

Általánosan elérhető a szervezeti kapcsolattartók delta-lekérdezése! Mostantól nyomon követheti az éles alkalmazások változásait anélkül, hogy folyamatosan lekérdezné Microsoft Graph. Cserélje le az összes meglévő kódot, amely folyamatosan lekérdezi az orgContact data by delta lekérdezést a teljesítmény jelentős javítása érdekében. [Részletek](/graph/api/orgcontact-delta?tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Microsoft Graph általánosan elérhető delta-lekérdezések támogatása

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** MS Graph

**Termékképesség:** Fejlesztői élmény

Általánosan elérhető az alkalmazások Delta-lekérdezése! Mostantól nyomon követheti az éles alkalmazások változásait anélkül, hogy folyamatosan lekérdezné Microsoft Graph. Cserélje le a meglévő kódot, amely folyamatosan lekérdezi az alkalmazásadatokat változáslekérdezésekkel a teljesítmény jelentős javítása érdekében. [Részletek](/graph/api/application-delta)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Microsoft Graph előzetes verzióhoz elérhető felügyeleti egységek delta-lekérdezési támogatása

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** MS Graph

**Termékképesség:** A Fejlesztői élmény Delta felügyeleti egységekre vonatkozó lekérdezése nyilvános előzetes verzióban érhető el! Most már nyomon követheti a módosításokat anélkül, hogy folyamatosan lekérdezné Microsoft Graph. [Részletek](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta&preserve-view=true)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Hitelesítési telefonszámok és egyéb funkciók kezelése az új Microsoft Graph API-kban

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** MS Graph

**Termékképesség:** Fejlesztői élmény

Ezek az API-k kulcsfontosságú eszközök a felhasználók hitelesítési módszereinek kezeléséhez. Most már programozott módon előre regisztrálhatja és kezelheti az MFA-hez és az önkiszolgáló jelszó-visszaállításhoz (SSPR) használt hitelesítőket. Ez volt az egyik leggyakrabban kért szolgáltatás a Azure AD MFA, SSPR és Microsoft Graph helyeken. Az ebben a hullámban kiadott új API-k a következő lehetőségeket adták meg:

- Felhasználó hitelesítési telefonjainak olvasása, hozzáadása, frissítése és eltávolítása
- Felhasználó jelszavának alaphelyzetbe állítása
- SMS-bejelentkezés be- és kikapcsolása

További információ: [Az Azure AD](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true)hitelesítési módszerek API áttekintése.

---

### <a name="administrative-units-public-preview"></a>Felügyeleti egységek nyilvános előzetes verziója

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Azure AD-szerepkörök

**Termékképesség:** Access Control

A felügyeleti egységek lehetővé teszik olyan rendszergazdai engedélyek megadását, amelyek a szervezet egy részlegére, régiójára vagy más, Ön által meghatározott szegmensére korlátozódnak. A felügyeleti egységek használatával engedélyeket delegálhat a regionális rendszergazdáknak, vagy szabályzatokat állíthat be részletesen. Egy felhasználói fiók rendszergazdája például frissítheti a profiladatokat, új jelszavakat állíthat vissza, és licenceket rendelhet a felhasználókhoz csak a felügyeleti egységben.

A felügyeleti egységek használatával a központi rendszergazda:

- Felügyeleti egység létrehozása az erőforrások decentralizált kezeléséhez
- Rendszergazdai engedélyekkel rendelkező szerepkör hozzárendelése csak az Azure AD-felhasználókhoz egy felügyeleti egységben
- A felügyeleti egységek feltöltése szükség szerint felhasználókkal és csoportokkal

További információ: Felügyeleti egységek kezelése a [Azure Active Directory (előzetes verzió) .](../roles/administrative-units.md)

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>A nyomtató-rendszergazda és a nyomtató-technikus beépített szerepkörei

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Azure AD-szerepkörök

**Termékképesség:** Access Control

**Nyomtató-rendszergazda:** Az ezzel a szerepkörsel rendelkező felhasználók nyomtatókat regisztrálnak, és a Microsoft Univerzális nyomtatás megoldásban az összes nyomtatókonfiguráció minden aspektusát kezelhetik, beleértve a Univerzális nyomtatás-összekötő beállításait. Az összes delegált nyomtatásra vonatkozó engedélykéréshez engedélyt kaphatnak. A nyomtató-rendszergazdák a jelentések nyomtatásához is hozzáférhetnek. 

**Nyomtatókezelő:** Az ezzel a szerepkörrel rendelkeznek felhasználók regisztrálják a nyomtatókat, és kezelhetik a nyomtatók állapotát a Microsoft Univerzális nyomtatás megoldásban. Emellett az összekötők összes adatát is olvashatják. A nyomtatókezelő által nem elérhető legfontosabb feladatok a nyomtatókra és a nyomtatók megosztására vonatkozó felhasználói engedélyek beállítása. [Részletek](../roles/permissions-reference.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Beépített hibrididentitás-rendszergazdai szerepkör

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Azure AD-szerepkörök

**Termékképesség:** Access Control

Az ebben a szerepkörben álló felhasználók engedélyezhetik, konfigurálhatja és kezelhetik a hibrid identitás Azure AD-beli engedélyezésével kapcsolatos szolgáltatásokat és beállításokat. Ez a szerepkör lehetővé teszi, hogy konfigurálja az Azure AD-t a három támogatott hitelesítési módszer (&#8212;jelszó-kivonatszinkronizálás (PHS), átmenő hitelesítés (PTA) vagy összevonás (AD FS vagy külső összevonási szolgáltató)&#8212;egyikére, valamint a kapcsolódó helyszíni infrastruktúra üzembe helyezésére azok engedélyezéséhez. A helyszíni infrastruktúra magában foglalja a kiépítési és PTA-ügynököket. Ez a szerepkör lehetővé teszi a közvetlen egykiszolgálós Sign-On (S-SSO) engedélyezését a nem Windows 10 eszközökön vagy a nem Windows Server 2016 rendszerű számítógépeken. Emellett ez a szerepkör lehetővé teszi a bejelentkezési naplók megtekintését, valamint az állapot és az elemzések monitorozási és hibaelhárítási célú hozzáférését. [Részletek](../roles/permissions-reference.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Beépített hálózati rendszergazdai szerepkör

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Azure AD-szerepkörök

**Termékképesség:** Access Control

Az ezzel a szerepkörű felhasználók áttekinthetik a Microsofttól származó, a felhasználói helyekről származó hálózati telemetriai adatokon alapuló, a szegélyhálózati architektúrára vonatkozó javaslatokat. A hálózati teljesítmény Microsoft 365 nagyvállalati ügyfélhálózati peremhálózati architektúrára támaszkodik, amely általában a felhasználók helyspecifikus architektúrája. Ez a szerepkör lehetővé teszi a felderített felhasználói helyek szerkesztését és ezen helyek hálózati paramétereinek konfigurációját a továbbfejlesztett telemetriai mérések és tervezési javaslatok elősegítése érdekében. [Részletek](../roles/permissions-reference.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Tömeges tevékenység és letöltések az Azure AD felügyeleti portálon

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Felhasználókezelés

**Termékképesség:** Taglista

Mostantól tömeges tevékenységeket végezhet a felhasználókon és csoportokon az Azure AD-ban, ha feltölt egy CSV-fájlt az Azure AD felügyeleti portál felületéről. Felhasználókat hozhat létre, felhasználókat törölhet és vendégfelhasználókat hívhat meg. Csoporttagokat is hozzáadhat, illetve eltávolíthat csoportból.

Az Azure AD-erőforrások listáját az Azure AD felügyeleti portál felületről is letöltheti. Letöltheti a címtárban található felhasználók listáját, a címtárban található csoportok listáját és egy adott csoport tagjait.

További információért tekintse meg a következőket:

- [Felhasználók létrehozása](../enterprise-users/users-bulk-add.md) vagy [vendégfelhasználók meghívása](../external-identities/tutorial-bulk-invite.md)
- [Felhasználók törlése](../enterprise-users/users-bulk-delete.md) vagy [törölt felhasználók visszaállítása](../enterprise-users/users-bulk-restore.md)
- [Felhasználók listájának letöltése vagy](../enterprise-users/users-bulk-download.md) [Csoportok listájának letöltése](../enterprise-users/groups-bulk-download.md)
- [Tagok hozzáadása (importálása) vagy](../enterprise-users/groups-bulk-import-members.md) [tagok eltávolítása](../enterprise-users/groups-bulk-remove-members.md) vagy Tagok [listájának letöltése](../enterprise-users/groups-bulk-download-members.md) egy csoporthoz

---

### <a name="my-staff-delegated-user-management"></a>Munkatársak delegált felhasználókezelés

**Írja be a következőt:** Új funkció

**Szolgáltatáskategória:** Felhasználókezelés

**Termékképesség:**

Munkatársak lehetővé teszi a Firstline Managers, például az áruházvezetők számára, hogy az alkalmazottak hozzáférjenek az Azure AD-fiókjukhoz. A központi segélykulára való támaszkodás helyett a szervezetek delegálhatnak gyakori feladatokat, például a jelszavak alaphelyzetbe állítását vagy a telefonszámok megváltoztatását a Firstline Managernek. A Munkatársak az olyan felhasználók, akik nem férnek hozzá a fiókjukhoz, néhány kattintással újra hozzáférhetnek, és nincs szükség a segítségre vagy az it-csoport tagjaira. További információ: Felhasználók kezelése a Munkatársak [(előzetes verzió)](../roles/my-staff-configure.md) és [Delegált felhasználókezelés a Munkatársak (előzetes verzió) gombra.](../user-help/my-staff-team-manager.md)

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Frissített végfelhasználói élmény a hozzáférési felülvizsgálatok során

**Írja be a következőt:** Módosított funkció

**Szolgáltatáskategória:** Hozzáférési felülvizsgálatok

**Termékképesség:** Identity Governance

Frissítettük az Azure AD hozzáférési felülvizsgálatok felülvizsgálói élményét a Saját alkalmazások portálon. Április végén az Azure AD hozzáférési felülvizsgálati szolgáltatásba bejelentkezett felülvizsgálók egy szalagcímet fognak látni, amely lehetővé teszi számukra a frissített felhasználói élmény Saját hozzáférés. Vegye figyelembe, hogy a hozzáférési felülvizsgálatok frissített felülete ugyanazt a funkciót kínálja, mint a jelenlegi élmény, de az új képességeken felül egy továbbfejlesztett felhasználói felülettel, amely lehetővé teszi a felhasználók hatékony működését. [A frissített felhasználói élményről itt olvashat bővebben.](../governance/perform-access-review.md) Ez a nyilvános előzetes verzió 2020 júliusának végéig tart. Július végén azok a felülvizsgálók, akik nem döntöttek az előzetes verzió mellett, automatikusan a Saját hozzáférés hozzáférési felülvizsgálatok elvégzésére lesznek irányítva. Ha azt szeretné, hogy a felülvizsgálók véglegesen át tudjanak váltani a Saját hozzáférés előzetes verzióra, kérjük, itt nyújtson [be kérelmet.](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>A Workday bejövő felhasználóátépítési és -visszaírási alkalmazásai mostantól támogatják a Workday Web Services API legújabb verzióit

**Írja be a következőt:** Módosított funkció

**Szolgáltatáskategória:** Alkalmazás kiépítése

**Termékképesség:** 

Az ügyfelek visszajelzései alapján frissítettük a Workday bejövő felhasználóátépítési és -visszaírási alkalmazásait a vállalati alkalmazáskatatárban, hogy támogassák a Workday Web Services (WWS) API legújabb verzióit. Ezzel a módosítással az ügyfelek megadhatják a KAPCSOLATi sztringben használni kívánt WWS API-verziót. Így az ügyfelek több, a Workday kiadásában elérhető HR-attribútumot is lekérnek. A Workday Writeback alkalmazás mostantól az ajánlott Change_Work_Contact_Info Workday webszolgáltatást használja a munkahelyi Maintain_Contact_Info.

Ha a kapcsolati sztringben nincs megadva verzió, alapértelmezés szerint a Workday bejövő kiépítési alkalmazásai továbbra is a WWS v21.1-et használják a [](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) legújabb Workday API-kra való váltáshoz a bejövő felhasználók kiépítéséhez, az ügyfeleknek frissíteni kell a kapcsolati sztringet az oktatóanyagban dokumentáltak szerint, valamint frissíteni kell a Workday attribútumok esetében használt XPATH-kat a Workday attribútumok referencia-útmutatója [alapján.](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) 

Az új API visszaíráshoz való használatához nincs szükség módosításra a Workday Writeback kiépítési alkalmazásban. A Workday oldalán győződjön meg arról, hogy a Workday integration system user (ISU) fiók rendelkezik a Change_Work_Contact üzleti folyamat meghívásához szükséges engedélyekkel az üzletifolyamat-biztonsági házirend engedélyeinek konfigurálása című oktatóanyagban dokumentált [módon.](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions) 

Frissítettük az [oktatóanyag útmutatóját, hogy](../saas-apps/workday-inbound-tutorial.md) tükrözze az új API-verzió támogatását.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Az alapértelmezett hozzáférési szerepkörrel rendelkezik felhasználók mostantól a kiépítés hatókörében vannak

**Írja be a következőt:** Módosított funkció

**Szolgáltatáskategória:** Alkalmazás kiépítése

**Termékképesség:** Identitás-életciklus kezelése

Korábban az alapértelmezett hozzáférési szerepkörrel rendelkezik a felhasználók kiépítési hatóköre. Visszajelzést kaptunk arról, hogy az ügyfelek azt szeretnék, hogy az ezzel a szerepkörrel kapcsolatos felhasználók a kiépítés hatókörében legyen. 2020. április 16-tól minden új kiépítési konfiguráció lehetővé teszi az alapértelmezett hozzáférési szerepkörű felhasználók számára. Fokozatosan módosítjuk a meglévő kiépítési konfigurációk viselkedését, hogy támogassa az ezzel a szerepkörhöz szükséges felhasználók kiépítését. [Részletek](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>A kiépítési felhasználói felület frissítése

**Írja be a következőt:** Módosított funkció

**Szolgáltatáskategória:** Alkalmazás kiépítése

**Termékképesség:** Identitás-életciklus kezelése

Frissítettünk a kiépítési élményt, hogy egy célzottabb felügyeleti nézetet hozzunk létre. Ha egy már konfigurált vállalati alkalmazás kiépítési paneljére lép, könnyedén nyomon követheti az üzembehelyezés előrehaladását, és kezelheti az olyan műveleteket, mint az üzembehelyezés indítása, leállítása és újraindítása. [Részletek](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>A dinamikus csoport szabályérvényesítése már elérhető a nyilvános előzetes verzióban

**Írja be a következőt:** Módosított funkció

**Szolgáltatáskategória:** Csoportkezelés

**Termékképesség:** Együttműködés

Azure Active Directory (Azure AD) lehetővé teszi a dinamikus csoportszabályok érvényesítését. A Szabályok **érvényesítése lapon** ellenőrizheti a dinamikus szabályt a mintacsoporttagok alapján, így ellenőrizheti, hogy a szabály a várt módon működik-e. Dinamikus csoport szabályainak létrehozásakor vagy frissítésekkor a rendszergazdák tudni szeretnék, hogy egy felhasználó vagy eszköz tagja-e a csoportnak. Ez segít kiértékelni, hogy egy felhasználó vagy eszköz megfelel-e a szabály feltételeinek, és segítséget nyújt a hibaelhárításban, ha tagság nem várható.

További információ: Dinamikus csoporttagsági szabály [ellenőrzése (előzetes verzió)](../enterprise-users/groups-dynamic-rule-validation.md).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Identitásbiztonsági pontszám – Biztonsági alapértékek és MFA-fejlesztések műveletfrissítései

**Írja be a következőt:** Módosított funkció

**Szolgáltatáskategória:** N/a

**Termékképesség:** Identity Security & Protection

**Biztonsági alapértelmezések támogatása az Azure AD fejlesztési műveletekhez:** A Microsoft biztonsági pontszáma frissíteni fogja a fejlesztési műveleteket az [Azure AD](./concept-fundamentals-security-defaults.md)alapértelmezett biztonsági beállításainak támogatása érdekében, ami megkönnyíti a szervezet védelmét a gyakori támadások előre konfigurált biztonsági beállításaival. Ez a következő fejlesztési műveletekre lesz hatással:

- Győződjön meg arról, hogy a biztonságos hozzáférés érdekében minden felhasználó el tudja végrehajtani a többtényezős hitelesítést
- MFA megkövetelve a rendszergazdai szerepkörökhöz
- Szabályzat engedélyezése az örökölt hitelesítés letiltás érdekében
 
**Az MFA fejlesztése műveletfrissítések:** Annak érdekében, hogy tükrözze, hogy a vállalatoknak a lehető legnagyobb biztonságot kell biztosítaniuk a vállalattal használható szabályzatok alkalmazása során, a Microsoft biztonsági pontszáma eltávolított három fejlesztési műveletet, amelyek a többtényezős hitelesítésre vannak központosodva, és kettővel bővült.

A fejlesztési műveletek eltávolítva:

- Az összes felhasználó regisztrálása többtényezős hitelesítéshez
- MFA megkövetelése minden felhasználótól
- MFA megkövetelve az Azure AD kiemelt szerepkörei esetében

Hozzáadott fejlesztési műveletek:

- Annak biztosítása, hogy a biztonságos hozzáférés érdekében minden felhasználó el tudja végrehajtani a többtényezős hitelesítést
- MFA megkövetelve a rendszergazdai szerepkörökhöz

Ezekhez az új fejlesztési műveletekhez regisztrálni kell a felhasználókat vagy rendszergazdákat a címtár többtényezős hitelesítéséhez (MFA), és meg kell állítani a szervezeti igényeknek megfelelő szabályzatokat. A fő cél a rugalmasság biztosítása, ugyanakkor annak biztosítása, hogy minden felhasználó és rendszergazda több tényezővel vagy kockázatalapú identitás-ellenőrzési kéréssel is hitelesíthető legyen. Ez több, hatókörön belül meghozott döntéseket használó szabályzat vagy a biztonsági alapértelmezések (március 16-tól) beállításának formájában is jelen lehet, amelyek lehetővé teszi, hogy a Microsoft döntsön a felhasználók MFA-jával kapcsolatos kérdésről. További információ a Microsoft biztonsági [pontszámának újdonságairól.](/microsoft-365/security/mtp/microsoft-secure-score#whats-new)

---

## <a name="march-2020"></a>2020. március

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march--2021"></a>Nem Azure Active Directory fiókok 2021. márciusi B2B-frissítésében

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 
**2021. március 31-től** kezdve a Microsoft nem fogja támogatni a meghívók érvényesítését nem Azure Active Directory -fiókok és bérlők létrehozásával a B2B együttműködési forgatókönyvekhez. Ennek előkészítéséhez javasoljuk, hogy küldjön [e-mailt e-mailben az egyszer használható PIN-kódos hitelesítéshez.](../external-identities/one-time-passcode.md)

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Az alapértelmezett hozzáférési szerepkörrel rendelkezik a felhasználók kiépítési hatóköre

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése
 
Korábban az alapértelmezett hozzáférési szerepkörrel nem lehetett kiépítni a felhasználókat. Visszajelzést kaptunk arról, hogy az ügyfelek azt szeretnék, ha az ezzel a szerepkörrel kapcsolatos felhasználók a kiépítés hatókörében lenne. Dolgozunk egy módosítás üzembe helyezésén, hogy az összes új kiépítési konfiguráció lehetővé tegye az alapértelmezett hozzáférési szerepkörű felhasználók üzembe helyezését. Fokozatosan módosítjuk a meglévő kiépítési konfigurációk viselkedését, hogy támogassák az ezzel a szerepkörhöz szükséges felhasználók kiépítését. Nincs szükség felhasználói beavatkozásra. Amint ez a változás [](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md) meg van útjára áll, közzétenünk egy frissítést a dokumentációban.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Az Azure AD B2B-együttműködés a 21Vianet Microsoft Azure (Azure China 21Vianet) bérlők által üzemeltetett Azure China 21Vianet lesz elérhető

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 
Az Azure AD B2B együttműködési képességek a 21Vianet- (Azure China 21Vianet-) bérlők által üzemeltetett Microsoft Azure-ben lesznek elérhetők, így az Azure China 21Vianet-bérlő felhasználói zökkenőmentesen működhetnek együtt más Azure China 21Vianet-bérlők felhasználóival. [További információ az Azure AD B2B-együttműködésről.](/azure/active-directory/b2b/)

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B-együttműködés meghívó e-mail újratervezése

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 
Az [](../external-identities/invitation-email-elements.md) Azure AD B2B együttműködési meghívó szolgáltatása által a felhasználók címtárba való meghívására küldött e-mailek újra lesznek tervezve, hogy a meghívó adatait és a felhasználó következő lépéseit egyértelműbb legyen.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>A HomeRealmDiscovery szabályzat módosításai megjelennek az auditnaplókban

**Írja be a következőt:** Rögzített  
**Szolgáltatáskategória:** Ellenőrzési  
**Termékképesség:** Jelentéskészítés & figyelése
 
Kijavítottunk egy hibát, amely miatt a [HomeRealmDiscovery](../manage-apps/configure-authentication-for-federated-users-portal.md) szabályzat módosításai nem szerepeltek az auditnaplókban. Most már láthatja, hogy mikor és hogyan változott a szabályzat, és ki és ki módosította. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás katalógusban – 2020. március

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Külső fél integrációja
 
2020 márciusában hozzáadtunk ezt az 51 új, összevonási támogatást támogató alkalmazást az alkalmazásgyűjteményhez: 

[Cisco AnyConnect,](../saas-apps/cisco-anyconnect.md) [Zoho One China,](../saas-apps/zoho-one-china-tutorial.md) [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [Profit.co SAML-alkalmazás,](../saas-apps/profitco-saml-app-tutorial.md) [iPoint-szolgáltató,](../saas-apps/ipoint-service-provider-tutorial.md) [contexxt.ai SPHERE,](https://contexxt-sphere.com/login) [Az Invictus,](../saas-apps/wisdom-by-invictus-tutorial.md) [Flare Digital Signage](https://spark-dev.pixelnebula.com/login), Logz.io – Felhő [megfigyelhetősége](../saas-apps/logzio-cloud-observability-for-engineers-tutorial.md)mérnökök számára, [SpectrumU](../saas-apps/spectrumu-tutorial.md), [BizzContact](https://bizzcontact.app/), [Elqano SSO,](../saas-apps/elqano-sso-tutorial.md) [MarketSignShare,](http://www.signshare.com/) [CrossKnowledge Learning Suite,](../saas-apps/crossknowledge-learning-suite-tutorial.md) [Netvision Compas,](../saas-apps/netvision-compas-tutorial.md) [FCM HUB,](../saas-apps/fcm-hub-tutorial.md) [ILLETVE A/S Byggeweb Mobile](https://apps.apple.com/us/app/docia/id529058757), [GoLinks,](../saas-apps/golinks-tutorial.md) [Datadog](../saas-apps/datadog-tutorial.md), [Zscaler B2B User Portal](../saas-apps/zscaler-b2b-user-portal-tutorial.md), [LIFT](../saas-apps/lift-tutorial.md), [Planview Enterprise One](../saas-apps/planview-enterprise-one-tutorial.md), [WatchTeams](https://www.devfinition.com/), [Aster,](https://demo.asterapp.io/login) [Skills Workflow,](../saas-apps/skills-workflow-tutorial.md) [Node Insight,](https://admin.nodeinsight.com/AADLogin.aspx) [IP Platform](../saas-apps/ip-platform-tutorial.md), [InVision,](../saas-apps/invision-tutorial.md) [Pipedrive,](../saas-apps/pipedrive-tutorial.md) [Showcase Workshop](https://app.showcaseworkshop.com/), [Greenlight Integration Platform, Greenlight](../saas-apps/greenlight-integration-platform-tutorial.md) [Compliant Access Management](../saas-apps/greenlight-compliant-access-management-tutorial.md), [Grok Learning](../saas-apps/grok-learning-tutorial.md), [Miradore Online](https://login.online.miradore.com/), [Khoros Care](../saas-apps/khoros-care-tutorial.md), [AskYourTeam](../saas-apps/askyourteam-tutorial.md), [TruNarrative](../saas-apps/trunarrative-tutorial.md), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [Bizagi Studio a digitális](../saas-apps/bizagi-studio-for-digital-process-automation-tutorial.md)folyamatautomatizáláshoz, [insuiteX,](https://www.insuite.jp/) [sybo](https://www.systexsoftware.com.tw/), [Britive](../saas-apps/britive-tutorial.md), [WhosOffice](../saas-apps/whosoffice-tutorial.md), [E-days](../saas-apps/e-days-tutorial.md), [Kollective SDN](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/), [Playvox](https://my.playvox.com/login), [KornOffice 360](../saas-apps/korn-ferry-360-tutorial.md), [Campus Campus](../saas-apps/campus-cafe-tutorial.md), [Catchpoint,](../saas-apps/catchpoint-tutorial.md) [Code42](../saas-apps/code42-tutorial.md)

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Az Azure AD B2B-együttműködés elérhető Azure Government bérlőkben

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** B2B/B2C
 
Az Azure AD B2B együttműködési funkciók már elérhetők néhány Azure Government között.  Ha meg kell tudni, hogy a bérlő fel tudja-e használni ezeket a képességeket, kövesse a Honnan tudom, hogy elérhető-e [a B2B-együttműködés az Azure US Government-bérlőmben?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor Azure-naplók integrációja mostantól elérhető a Azure Government

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Jelentési  
**Termékképesség:** Jelentéskészítés & monitorozása
 
Azure Monitor Azure AD-naplók integrációja már elérhető a Azure Government. Az Azure AD-naplókat (audit- és bejelentkezési naplókat) egy tárfiókba, az Event Hubba és a Log Analyticsbe irányíthatja. Tekintse meg a részletes [dokumentációt,](../reports-monitoring/concept-activity-logs-azure-monitor.md) valamint az Azure AD-forgatókönyvek jelentéskészítési és monitorozási üzembe helyezési terveit. [](../reports-monitoring/plan-monitoring-and-reporting.md)

---

### <a name="identity-protection-refresh-in-azure-government"></a>Az Identity Protection frissítése a Azure Government

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Identity Protection  
**Termékképesség:** Identity Security & Protection

Izgatottan várjuk, hogy a frissített felhasználói [](../identity-protection/overview-identity-protection.md)élményt Azure AD Identity Protection a Microsoft Azure Government   [portálon.](https://portal.azure.us/) További információért tekintse meg bejelentési [blogbejegyzésünket.](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Vészhelyreállítás: A kiépítési konfiguráció letöltése és tárolása

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése
 
Az Azure AD kiépítési szolgáltatás konfigurációs képességek gazdag készletét biztosítja. Az ügyfeleknek menteniük kell a konfigurációjukat, hogy később hivatkozni tudjanak rá, vagy vissza tudjanak menni egy ismert jó verzióra. Lehetőség van a kiépítési konfiguráció JSON-fájlként való letöltésére és szükség esetén feltöltésére. [További információ](../app-provisioning/export-import-provisioning-configuration.md).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>Az SSPR (új jelszó önkiszolgáló visszaállítása) mostantól két kaput igényel a 21Vianet (Microsoft Azure) által üzemeltetett Azure China 21Vianet 

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Self-Service új jelszó kérése  
**Termékképesség:** Identity Security & Protection
 
Korábban a 21Vianet (Azure China 21Vianet) által üzemeltetett Microsoft Azure-ben az önkiszolgáló jelszó-visszaállítást (SSPR) használó rendszergazdáknak csak egyetlen "kapura" (feladatra) volt szükségük az identitásuk bizonyítására. A nyilvános és más országos felhőkben a rendszergazdáknak általában két kaput kell használniuk az identitásuk bizonyítására az SSPR használata esetén. Mivel azonban nem támogatjuk az SMS-eket vagy telefonhívásokat a Azure China 21Vianet, a rendszergazdák egy kapus jelszó-visszaállítást is engedélyezettek.

Szolgáltatásparitást hozunk létre az SSPR-Azure China 21Vianet és a nyilvános felhő között. A rendszergazdáknak két kaput kell használniuk az SSPR használata esetén. Az SMS- és telefonhívások, valamint az Authenticator-alkalmazások értesítései és kódjai támogatottak lesznek. [További információ](../authentication/concept-sspr-policy.md#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>A jelszó hossza legfeljebb 256 karakter lehet

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Felhasználóhitelesítés
 
Az Azure AD szolgáltatás megbízhatóságának biztosítása érdekében a felhasználói jelszavak hossza mostantól legfeljebb 256 karakter lehet. Az ennél hosszabb jelszavakat használó felhasználóknak a későbbi bejelentkezések során módosítaniuk kell a jelszavukat. Ehhez vegye fel a kapcsolatot a rendszergazdával vagy az új jelszó önkiszolgáló visszaállítási funkcióját használva.

Ez a módosítás 2020. március 13-án, 10:00-kor (UTC) engedélyezve volt, és a hiba AADSTS 50052, InvalidPasswordExceedsMaxLength. További [részleteket a feltörésről szóló változásról](../develop/reference-breaking-changes.md#user-passwords-will-be-restricted-to-256-characters) szóló értesítésben talál.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Az Azure AD bejelentkezési naplói mostantól minden ingyenes bérlő számára elérhetők a Azure Portal

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Jelentési  
**Termékképesség:** Jelentéskészítés & monitorozása
 
Az ingyenes bérlőkkel rendelkezik ügyfelek mostantól legfeljebb 7 napig férhetnek hozzá az [Azure AD](../reports-monitoring/concept-sign-ins.md) bejelentkezési Azure Portal a portálról. Korábban a bejelentkezési naplók csak olyan ügyfelek számára voltak elérhetők, akik prémium szintű Azure Active Directory licenccel. Ezzel a módosítással minden bérlő hozzáférhet ezekhez a naplókhoz a portálon keresztül.

> [!NOTE]
> Az ügyfeleknek továbbra is prémium szintű licencre (prémium szintű Azure Active Directory P1 vagy P2) van szükségük a bejelentkezési naplók eléréséhez a Microsoft Graph API-n és Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>A címtárra kiterjedő csoportok elavadása lehetőség a Csoportok általános beállítások a Azure Portal

**Írja be a következőt:** Elavult  
**Szolgáltatáskategória:** Csoportkezelés  
**Termékképesség:** Együttműködés

Annak érdekében, hogy az ügyfelek rugalmasabban hoznak létre az igényeiknek leginkább  megfelelő címtárra kiterjedő csoportokat, a Azure Portal Csoportokkal kapcsolatos általános beállításai között lecseréltünk egy, a dinamikus csoportdokumentációra mutató  >   hivatkozást. [](../enterprise-users/groups-dynamic-membership.md) Továbbfejlesztettük a dokumentációt, hogy további utasításokat tartalmaz, így a rendszergazdák vendégfelhasználókat is magában foglaló vagy kizáró teljes felhasználói csoportokat hozhatnak létre.

---

## <a name="february-2020"></a>2020. február

### <a name="upcoming-changes-to-custom-controls"></a>Az egyéni vezérlők közelgő módosításai

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Mfa  
**Termékképesség:** Identity Security & Protection
 
Tervezzük lecserélni a jelenlegi egyéni vezérlők előzetesét egy olyan megközelítésre, amely lehetővé teszi, hogy a partner által biztosított hitelesítési képességek zökkenőmentesen működjön együtt a Azure Active Directory rendszergazdai és végfelhasználói felülettel. Napjainkban a partner MFA-megoldásokra a következő korlátozások vonatkoznak: csak jelszó megadása után működnek; más kulcsfontosságú forgatókönyvekben nem MFA-ként szolgálnak a további hitelesítéshez; és nem integrálhatók a végfelhasználói vagy rendszergazdai hitelesítő adatok kezelési funkcióival. Az új implementáció lehetővé teszi, hogy a partner által biztosított hitelesítési tényezők működjön együtt a kulcsfontosságú forgatókönyvek beépített tényezőivel, beleértve a regisztrációt, a használatot, az MFA-jogcímeket, a hitelesítés, a jelentéskészítés és a naplózást. 

Az egyéni vezérlők az új kialakítással együtt előzetes verzióban is támogatottak maradnak, amíg az általánosan elérhető nem lesz. Ezen a ponton időt adunk az ügyfeleknek arra, hogy áttűnésnek az új tervre. A jelenlegi megközelítés korlátai miatt az új szolgáltatókat csak az új terv elérhetővé tévővé válik. Szorosan együttműködünk az ügyfelekkel és szolgáltatókkal, és a zárás után tájékoztatjuk az ütemtervet. [További információ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identitás-biztonsági pontszám – Az MFA fejlesztési műveletének frissítései

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Mfa  
**Termékképesség:** Identity Security & Protection
 
Annak érdekében, hogy a vállalatoknak a lehető legnagyobb biztonságot biztosítsák a vállalattal használható szabályzatok alkalmazása során, a Microsoft biztonsági pontszáma eltávolít három, a többtényezős hitelesítésre (MFA) központos fejlesztési műveletet, és kettő hozzáadásával.

A következő fejlesztési műveletek lesznek eltávolítva:

- Az összes felhasználó regisztrálása az MFA-hez
- MFA megkövetelése minden felhasználótól
- MFA megkövetelve az Azure AD kiemelt szerepkörei esetében

A következő fejlesztési műveletek lesznek hozzáadva:

- Győződjön meg arról, hogy az összes felhasználó el tudja végrehajtani az MFA-t a biztonságos hozzáférés érdekében
- MFA megkövetelve a rendszergazdai szerepkörökhöz

Ezekhez az új fejlesztési műveletekhez regisztrálni kell a felhasználókat vagy rendszergazdákat az MFA-hoz a címtárban, és létre kell hozni a szervezeti igényeknek megfelelő szabályzatokat. A fő cél a rugalmasság biztosítása, ugyanakkor annak biztosítása, hogy minden felhasználó és rendszergazda több tényezővel vagy kockázatalapú identitás-ellenőrzési kéréssel is hitelesíthető legyen. Ez olyan alapértelmezett biztonsági beállítások formájában is használhatja, amelyek lehetővé teszi, hogy a Microsoft döntsön arról, hogy mikor kell a felhasználóknak az MFA-t alkalmaznia, vagy több szabályzattal, amelyek hatókörön belül hozott döntéseket alkalmaznak. A fejlesztési művelet frissítéseinek részeként az alapkonfiguráció védelmi szabályzatai már nem szerepelnek a pontozási számításokban. [További információ a Microsoft biztonsági pontszámról.](/microsoft-365/security/mtp/microsoft-secure-score-whats-coming)

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services termékváltozat kiválasztása

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Azure AD Domain Services  
**Termékképesség:** Azure AD Domain Services
 
Visszajelzéseket kaptunk, Azure AD Domain Services az ügyfelek rugalmasabban választják ki a példányaik teljesítményszintjét. 2020. február 1-től átváltottunk egy dinamikus modellről (ahol az Azure AD az objektumok száma alapján határozza meg a teljesítményt és a tarifacsomagot) egy önkiválasztási modellre váltottunk. Mostantól az ügyfelek kiválaszthatják a környezetüknek megfelelő teljesítményszintet. Ez a változás lehetővé teszi az olyan új forgatókönyvek engedélyezését is, mint az erőforráserdők és a prémium szintű funkciók, például a napi biztonsági mentések. Az objektumok száma mostantól korlátlan az összes SKUs-hoz, de továbbra is javaslatokat fogunk kínálni az egyes szintek objektumszámára.

**Nincs szükség azonnali ügyfél beavatkozásra.** A meglévő ügyfelek esetében a 2020. február 1-jén használatban lévő dinamikus szint határozza meg az új alapértelmezett szintet. A változás nem befolyásolja a díjszabást vagy a teljesítményt. A Azure AD DS a címtárméret és a számítási feladatok jellemzőinek változásához a teljesítménykövetelményeket kell kiértékelniük. A szolgáltatásszintek közötti váltás továbbra is állásidő nélkül történik, és a címtár növekedése alapján nem fogjuk automatikusan új szintre áthelyezni az ügyfeleket. Emellett nem lesznek árnövekedések, és az új díjszabás az aktuális számlázási modellünknek megfelelően fog igazodni. További információért tekintse meg a Azure AD DS [termékcsomagok](../../active-directory-domain-services/administration-concepts.md#azure-ad-ds-skus) dokumentációját és a Azure AD Domain Services [oldalon.](https://azure.microsoft.com/pricing/details/active-directory-ds/)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Új összevont alkalmazások, amelyek a Azure AD alkalmazás 2020. februárban érhetők el

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Külső fél integrációja
 
2020 februárjában hozzáadtunk ezt a 31 új, összevonási támogatást támogató alkalmazást az alkalmazásgyűjteményhez: 

[IamIP-platform,](../saas-apps/iamip-patent-platform-tutorial.md)Experience [Cloud,](../saas-apps/experience-cloud-tutorial.md) [NS1 SSO for Azure](../saas-apps/ns1-sso-azure-tutorial.md), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), In Case of Crisis – Online [Portal](../saas-apps/in-case-of-crisis-online-portal-tutorial.md), [BIC Cloud Design](../saas-apps/bic-cloud-design-tutorial.md), [Beekeeper Azure AD Data Connector](../saas-apps/beekeeper-azure-ad-data-connector-tutorial.md), [Korn Qua Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](../saas-apps/verkada-command-tutorial.md), [Splashtop](../saas-apps/splashtop-tutorial.md), [Syxsense,](../saas-apps/syxsense-tutorial.md) [EAB Navigate](../saas-apps/eab-navigate-tutorial.md), New Relic Korlátozott [kiadás)](../saas-apps/new-relic-limited-release-tutorial.md), [Thulink](https://admin.thulium.com/login/instance), Ticket [Manager,](../saas-apps/ticketmanager-tutorial.md) [Template Chooser for Teams](https://links.officeatwork.com/templatechooser-download-teams), [Beesy](https://www.beesy.me/index.php/site/login), Health [Support System](../saas-apps/health-support-system-tutorial.md), [MURAL](https://app.mural.co/signup), [Hive](../saas-apps/hive-tutorial.md), [LavaDo,](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [Wakelet,](https://wakelet.com/login) [Firmex VDR,](../saas-apps/firmex-vdr-tutorial.md) [ThingLink for Teachers and Schools](https://www.thinglink.com/), [Coda](../saas-apps/coda-tutorial.md), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](../saas-apps/wedo-tutorial.md), [InvitePeople](https://invitepeople.com/login), [Reprints Desk - Article Galaxy,](../saas-apps/reprints-desk-article-galaxy-tutorial.md) [TeamViewer](../saas-apps/teamviewer-tutorial.md)

 
További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása a Azure Active Directory [alkalmazásgyűjteményben.](../develop/v2-howto-app-gallery-listing.md)

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Új kiépítési összekötők az Azure AD Alkalmazáskatatárban – 2020. február

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** külső fél integrációja
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Mixpanel](../saas-apps/mixpanel-provisioning-tutorial.md)
- [TeamViewer](../saas-apps/teamviewer-provisioning-tutorial.md)
- [Azure Databricks](/azure/databricks/administration-guide/users-groups/scim/aad)
- [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-provisioning-tutorial.md)
- [Zapier](../saas-apps/zapier-provisioning-tutorial.md)

A szervezet automatizált felhasználói fiókok automatikus kiépítése által történő biztonságossá tevésével kapcsolatos további információkért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val.](../app-provisioning/user-provisioning.md)

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-támogatás a FIDO2 biztonsági kulcsokhoz hibrid környezetekben

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Felhasználóhitelesítés
 
Bejelentjük, hogy az Azure AD nyilvános előzetes verziója támogatja a FIDO2 biztonsági kulcsokat hibrid környezetekben. A felhasználók mostantól FIDO2 biztonsági kulcsokkal bejelentkeznek a hibrid Azure AD-hez Windows 10-eszközeikre, és zökkenőmentesen bejelentkeznek helyszíni és felhőbeli erőforrásaikba. A hibrid környezetek támogatása volt a jelszó nélküli ügyfeleinktől leggyakrabban kért szolgáltatás, mivel kezdetben elindítottuk a FIDO2-támogatás nyilvános előzetes kiadását az Azure AD-hez csatlakozott eszközökön. Az olyan fejlett technológiákkal történő jelszó nélküli hitelesítés, mint a biometrikus adatok és a nyilvános/titkos kulcsok titkosítása kényelmes használatot és egyszerű használatot biztosít a biztonság érdekében. Ezzel a nyilvános előzetes verzióval mostantól modern hitelesítéssel, például FIDO2 biztonsági kulcsokkal férhet hozzá a hagyományos Active Directory erőforrásokhoz. További információ: [SSO to on-premises resources (SSO a helyszíni erőforrásokon).](../authentication/howto-authentication-passwordless-security-key-on-premises.md) 

Első lépésként olvassa el [a FIDO2](../authentication/howto-authentication-passwordless-security-key.md) biztonsági kulcsok bérlő számára való engedélyezésével kapcsolatos részletes útmutatót. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Általánosan elérhető az új Saját fiók felhasználói élmény

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Saját profil/fiók  
**Termékképesség:** Végfelhasználói élmények
 
Mostantól általánosan elérhető a Fiókom, amely az összes végfelhasználói fiókkezelési igény egyetlen állomása! A végfelhasználók URL-címen vagy az új felhasználói élmény fejlécében férhetnek hozzá Saját alkalmazások webhelyhez. Az új felhasználói élmény összes önkiszolgáló képességével kapcsolatos további információért olvassa el a [Fiókportál áttekintése témakört.](../user-help/my-account-portal-overview.md)

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>A fiók webhelyének URL-címe a következőre frissül myaccount.microsoft.com

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Saját profil/fiók  
**Termékképesség:** Végfelhasználói élmények
 
Az új Saját fiók végfelhasználói felület a következő hónapban a következő hónapra frissíti az `https://myaccount.microsoft.com` URL-címét. További információt a felhasználói élményről és a fiók önkiszolgáló funkcióiról a Saját fiókportálon elérhető súgóban [talál.](../user-help/my-account-portal-overview.md)

---

## <a name="january-2020"></a>2020. január
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Az új Saját alkalmazások mostantól általánosan elérhető

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Saját alkalmazások  
**Termékképesség:** Végfelhasználói élmények
 
Frissítse szervezetét az új Saját alkalmazások portálra, amely mostantól általánosan elérhető! További információ az új portálról és gyűjteményről: Gyűjtemények létrehozása a [Saját alkalmazások portálon.](../manage-apps/access-panel-collections.md)

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Az Azure AD-munkaterületek új neve gyűjtemények

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Saját alkalmazások   
**Termékképesség:** Végfelhasználói élmények
 
A munkaterületeket, a szűrőket a rendszergazdák a felhasználói alkalmazások rendszerezésére konfigurálva gyűjteménynek nevezzük. További információ a konfigurálásukról: [Gyűjtemények létrehozása a Saját alkalmazások portálon.](../manage-apps/access-panel-collections.md)

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C regisztráció és bejelentkezés egyéni szabályzat használatával (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** B2B/B2C
 
A telefonszám-regisztrációval és -bejelentkezéssel a fejlesztők és a nagyvállalatok lehetővé tehetik, hogy az ügyfeleik SMS-ben egy, a felhasználó telefonszámára küldött egyszeres jelszóval regisztrálják és jelentkezzenek be. Ez a funkció azt is lehetővé teszi, hogy az ügyfél módosítsa a telefonszámát, ha elveszíti a telefonhoz való hozzáférését. Az egyéni szabályzatok, a telefonos regisztráció és a bejelentkezés révén a fejlesztők és a vállalatok az oldalak testreszabásával kommunikálhatnak a márkájukkal. Ismerje meg, hogyan állíthat be telefonos regisztrációt és bejelentkezést egyéni szabályzatokkal [a Azure AD B2C.](../../active-directory-b2c/phone-authentication-user-flows.md)
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – 2020. január

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** külső fél integrációja
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Promapp]( ../saas-apps/promapp-provisioning-tutorial.md)
- [Zscaler Private Access](../saas-apps/zscaler-private-access-provisioning-tutorial.md)

A szervezet automatizált felhasználói fiókok automatikus kiépítése által történő biztonságossá tevésével kapcsolatos további információkért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val.](../app-provisioning/user-provisioning.md)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Új összevont alkalmazások a Azure AD alkalmazás – 2020. január

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** külső fél integrációja
 
2020 januárjában az alábbi 33 új, összevonási támogatást támogató alkalmazást adtunk hozzá az alkalmazásgyűjteményhez: 

[JOSA,](../saas-apps/josa-tutorial.md) [Fastly Edge Cloud,](../saas-apps/fastly-edge-cloud-tutorial.md) [Terraform Enterprise,](../saas-apps/terraform-enterprise-tutorial.md) [Spintr SSO,](../saas-apps/spintr-sso-tutorial.md) [Abibot Net oracleik,](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik) [Skykick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [Upshotly](../saas-apps/upshotly-tutorial.md), [LeaveBot,](https://appsource.microsoft.com/en-us/product/office/WA200001175) [DataCamp,](../saas-apps/datacamp-tutorial.md) [TripActions,](../saas-apps/tripactions-tutorial.md) [SmartWork](https://www.intumit.com/teams-smartwork/), [Dotcom-Monitor](../saas-apps/dotcom-monitor-tutorial.md), [SSALU – Azure AD SSO Gateway for Oracle E-Business Suite – EBS, PeopleSoft, and JDE](../saas-apps/ssogen-tutorial.md), [Hosted MyCirqa SSO](../saas-apps/hosted-mycirqa-sso-tutorial.md), [Yuhu Property Management Platform](../saas-apps/yuhu-property-management-platform-tutorial.md), [LumApps,](https://sites.lumapps.com/login) [Upwork Enterprise,](../saas-apps/upwork-enterprise-tutorial.md) [Talentsoft](../saas-apps/talentsoft-tutorial.md), [SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](../saas-apps/presspage-tutorial.md), [ContractSafe Saml2 SSO,](../saas-apps/contractsafe-saml2-sso-tutorial.md) [Maxient Conduct Manager Software](../saas-apps/maxient-conduct-manager-software-tutorial.md), [Helpshift](../saas-apps/helpshift-tutorial.md), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), Squelch Cloud Office365-összekötő, [PingFlow-hitelesítés,](https://app-staging.pingview.io/) [ PrinterLogic SaaS](../saas-apps/printerlogic-saas-tutorial.md), [Taskize Connect](../saas-apps/taskize-connect-tutorial.md), [Sandwai](https://app.sandwai.com/), [EZRentOut,](../saas-apps/ezrentout-tutorial.md) [AssetSonar](../saas-apps/assetsonar-tutorial.md), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="two-new-identity-protection-detections"></a>Két új Identity Protection-észlelés

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Identity Protection  
**Termékképesség:** Identity Security & Protection
 
Két új, összekapcsolt bejelentkezéses észlelési típust adtunk hozzá az Identity Protectionhez: a gyanús üzenetkezelési szabályok és Lehetetlen utazás. Ezeket az offline észleléseket az Microsoft Cloud App Security (MCAS) felderíti, és befolyásolja a felhasználót és a bejelentkezési kockázatokat az Identity Protectionben. Az észlelésekkel kapcsolatos további információkért tekintse meg a bejelentkezési [kockázattípusokat.](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Nem megfelelő változás: Az URI-töredékek nem lesznek áthozva a bejelentkezési átirányításon keresztül

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Felhasználóhitelesítés
 
2020. február 8-tól kezdve, amikor a login.microsoftonline.com-nek egy felhasználó bejelentkezési kérése van elküldve, a szolgáltatás egy üres töredéket fűz a kéréshez.  Ez megakadályozza az átirányítási támadások egy osztályát, mert biztosítja, hogy a böngésző törölje a kérésben lévő összes meglévő töredéket. Egy alkalmazásnak sem szabad függőségi viszonyban lennie ezzel a viselkedéssel. További információkért lásd a Microsoft [identitásplatformjának](../develop/reference-breaking-changes.md#february-2020) dokumentációjának Breaking changes (A microsoftos identitásplatformok változásainak feltörése) dokumentumát.

---

## <a name="december-2019"></a>2019. december

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Sap SuccessFactors-kiépítés integrálása az Azure AD-be és a helyszíni AD-be (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Alkalmazás kiépítése  
**Termékképesség:** Identitás-életciklus kezelése

Most már integrálhatja az SAP SuccessFactorst mérvadó identitásforrásként az Azure AD-be. Ezzel az integrációval automatizálhatja a teljes identitás-életciklust, beleértve a HR-alapú eseményeket, például az újonnan történt felvételeket vagy megszüntetéseket az Azure AD-fiókok kiépítése szabályozásához.

Az SAP SuccessFactors Azure AD-be irányuló bejövő kiépítésének beállítását az [SAP SuccessFactors](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md) automatikus kiépítésének konfigurálása oktatóanyagban tudja meg.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Testreszabott e-mailek támogatása a Azure AD B2C (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2C – Fogyasztói identitáskezelés  
**Termékképesség:** B2B/B2C

Mostantól testre szabott e-Azure AD B2C hozhat létre, amikor a felhasználók regisztrálnak az alkalmazásai használatára. A DisplayControls (jelenleg előzetes verzióban érhető el) és egy külső e-mail-szolgáltató (például [SendGrid,](https://sendgrid.com/) [SparkPost](https://sparkpost.com/)vagy egyéni REST API) használatával használhatja saját e-mail-sablonját,  a Címből és a tárgy szövegét, valamint támogathatja a honosítást és az egyéni egyszeri jelszó (OTP) beállításait.

További információ: Egyéni [e-mail-ellenőrzés a Azure Active Directory B2C.](../../active-directory-b2c/custom-email-sendgrid.md)

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Alapkonfiguráció-szabályzatok cseréje biztonsági alapértékekkel

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Más  
**Termékképesség:** Identitásbiztonság és -védelem

Egy alapértelmezett hitelesítési modell részeként minden bérlőből eltávolítjuk a meglévő alapkonfiguráció-védelmi szabályzatokat. Az eltávolítás célja február végi befejezés. Az alapkonfiguráció védelmi szabályzatai a biztonsági alapértelmezések. Ha alapkonfiguráció-védelmi szabályzatokat használt, át kell lépnie az új alapértelmezett biztonsági szabályzatra vagy a feltételes hozzáférésre. Ha még nem használta ezeket a szabályzatokat, nincs teendő.

Az új alapértelmezett biztonsági beállításokkal kapcsolatos további információkért lásd: [Mik azok a biztonsági alapértelmezések?](./concept-fundamentals-security-defaults.md) További információ a feltételes hozzáférési szabályzatokkal kapcsolatban: [Általános feltételes hozzáférési szabályzatok.](../conditional-access/concept-conditional-access-policy-common.md)

---

## <a name="november-2019"></a>2019. november

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>A SameSite attribútum és a Chrome 80 támogatása

**Írja be a következőt:** A változás megterve  
**Szolgáltatáskategória:** Hitelesítések (bejelentkezések)  
**Termékképesség:** Felhasználóhitelesítés

A cookie-k alapértelmezett biztonsági modelljének részeként a Chrome 80 böngésző módosítja a cookie-k attribútum nélküli `SameSite` kezelésének a mikéntjét. Minden olyan cookie, amely nem adja meg az attribútumot, úgy lesz kezelve, mintha az lenne, ami azt eredményezi, hogy a Chrome blokkol bizonyos tartományközi cookie-megosztási forgatókönyveket, amelyektől az alkalmazás `SameSite` `SameSite=Lax` függhet. A Chrome régebbi viselkedésének fenntartása érdekében használhatja az attribútumot, és hozzáadhat egy további attribútumot, így a webhelyek közötti `SameSite=None` `Secure` cookie-k csak HTTPS-kapcsolatokon keresztül érhetők el. A Chrome a tervek szerint 2020. február 4-ig befejezi ezt a változtatást.

Javasoljuk, hogy minden fejlesztőnk tesztelje az alkalmazásait ezzel az útmutatóval:

- Állítsa a Biztonságos cookie használata beállítás alapértelmezett **értékét** Igen **értékre.**

- A **SameSite** attribútum alapértelmezett értékét állítsa **Nincs értékre.**

- Adjon hozzá egy további `SameSite` Secure **attribútumot.**

További információ: [Upcoming SameSite Cookie Changes in ASP.NET and ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) (Az azonos webhely cookie-k várható változásai a ASP.NET és a ASP.NET Core-ban) és Az ügyfélwebhelyek és a Microsoft-termékek és -szolgáltatások esetleges megszakadása a Chrome [79-es](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)és újabb verzióiban.

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Új gyorsjavítás a Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) csomaghoz

**Írja be a következőt:** Rögzített  
**Szolgáltatáskategória:** Microsoft Identity Manager  
**Termékképesség:** Identitás-életciklus kezelése

A Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2) csomaghoz elérhető egy gyorsjavítási összesítő csomag (4.6.34.0-s build). Ez az összesítő csomag megoldja a problémákat, és fejlesztéseket ad hozzá "A frissítésben rögzített problémák és fejlesztések" című szakaszban leírtak szerint.

További információ és a gyorsjavítási csomag letöltése: [Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) Update Rollup is available (A 2016 2. szervizcsomag (4.6.34.0-s build)](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)frissítés érhető el).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Új AD FS alkalmazástevékenység-jelentés, amely segítséget ad az alkalmazások Azure AD-be való áttelepítéséhez (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Sso

A Active Directory összevonási szolgáltatások (AD FS) (AD FS Azure Portal) alkalmazástevékenység-jelentésével azonosíthatja, hogy mely alkalmazások migrálhatóak az Azure AD-be. A jelentés felméri az összes AD FS, hogy kompatibilisek-e az Azure AD-val, ellenőrzi a problémákat, és útmutatást nyújt az egyes alkalmazások migrálásra való előkészítéséhez.

További információ: Alkalmazások áttelepítése az [Azure AD AD FS](../manage-apps/migrate-adfs-application-activity.md)alkalmazástevékenységi jelentés használatával.

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Új munkafolyamat a felhasználók számára rendszergazdai jóváhagyás kérésére (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Access Control

Az új rendszergazdai hozzájárulási munkafolyamat lehetővé teszi a rendszergazdák számára, hogy hozzáférést adjanak a rendszergazdai jóváhagyást igénylő alkalmazásokhoz. Ha egy felhasználó megpróbál hozzáférni egy alkalmazáshoz, de nem tud hozzájárulást adni, mostantól rendszergazdai jóváhagyásra vonatkozó kérelmet küldhet. A kérést a szolgáltatás e-mailben küldi el, és egy, a Azure Portal-ból elérhető üzenetsorba helyezi a felülvizsgálóként kijelölt összes rendszergazda számára. Miután egy felülvizsgáló műveletet tett egy függőben lévő kérésen, a kérelmező felhasználók értesítést kapnak a műveletről.

További információ: [Configure the admin consent workflow (preview) (Rendszergazdai jóváhagyás munkafolyamatának konfigurálása (előzetes verzió)](../manage-apps/configure-admin-consent-workflow.md)).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Új Azure AD alkalmazás regisztrációk Tokenkonfigurációs élmény választható jogcímek kezeléséhez (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Más  
**Termékképesség:** Fejlesztői élmény

A Azure AD alkalmazás **új** Regisztrációs jogkivonatok konfigurációs panelje Azure Portal az alkalmazásfejlesztők számára dinamikus listát mutat az alkalmazásaikhoz választható jogcímek listájáról. Ez az új felület segít leegyszerűsíteni az Azure AD-alkalmazások migrálását, és minimalizálni a nem kötelező jogcímkonfigurációkat.

További információ: [Opcionális jogcímek megadása az Azure AD-alkalmazásnak.](../develop/active-directory-optional-claims.md)

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Új kétszakaszos jóváhagyási munkafolyamat az Azure AD-jogosultságkezelésben (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Más  
**Termékképesség:** Jogosultságkezelés

Bevezettünk egy új kétszakaszos jóváhagyási munkafolyamatot, amely lehetővé teszi, hogy két jóváhagyónak jóvá kell hagynia egy felhasználó hozzáférési csomagra vonatkozó kérését. Beállíthatja például úgy, hogy a kérelmező felhasználó felettesének jóvá kell hagynia azt, majd erőforrás-tulajdonost is megkövetelhet a jóváhagyáshoz. Ha az egyik jóváhagyó nem hagyja jóvá a jóváhagyást, a rendszer nem engedélyezi a hozzáférést.

További információ: Hozzáférési csomagra vonatkozó kérelem- és jóváhagyási beállítások módosítása [az Azure AD-jogosultságkezelésben.](../governance/entitlement-management-access-package-request-policy.md)

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Az új Saját alkalmazások frissítései (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Saját alkalmazások  
**Termékképesség:** külső fél integrációja

Mostantól testre szabhatja, hogy a szervezet felhasználói hogyan néznek meg és férnek hozzá a frissített Saját alkalmazások felhasználói élményhez. Az új felhasználói élmény része az új munkaterületek funkció is, amellyel a felhasználók könnyebben találhatják meg és rendszerezhetik az alkalmazásokat.

További információ az új munkaterületi Saját alkalmazások és a munkaterületek létrehozásáról: Munkaterületek létrehozása a [Saját alkalmazások portálon.](../manage-apps/access-panel-collections.md)

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Google közösségi azonosító támogatása az Azure AD B2B-együttműködéshez (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** B2b  
**Termékképesség:** Felhasználóhitelesítés

Az Azure AD-ben a Google közösségi adatok (Gmail-fiókok) használatának új támogatása segít egyszerűbbé tenni a felhasználók és partnerek együttműködését. A partnereknek többé már nincs szükségük új Microsoft-specifikus fiók létrehozására és kezelésére. A Microsoft Teams mostantól teljes körűen támogatja a Google-felhasználókat az összes ügyfélen, valamint a közös és bérlőkkel kapcsolatos hitelesítési végpontok esetében.

További információ: A Google hozzáadása [identitásszolgáltatóként B2B vendégfelhasználók számára.](../external-identities/google-federation.md)

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge mobiltámogatás a feltételes hozzáféréshez és az egyszeri bejelentkezéshez (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Feltételes hozzáférés  
**Termékképesség:** Identity Security & Protection

Az iOS-Microsoft Edge Android rendszerhez készült Azure AD mostantól támogatja az azure AD single Sign-On és a feltételes hozzáférést:

- **Microsoft Edge egyszeri bejelentkezés (SSO):** Az egyszeri bejelentkezés mostantól elérhető a natív ügyfelek (például a Microsoft Outlook és a Microsoft Edge) minden Azure AD-hez csatlakoztatott alkalmazáshoz.

- **Microsoft Edge feltételes hozzáférés:** Az alkalmazásalapú feltételes hozzáférési szabályzatokkal a felhasználóknak olyan védett Microsoft Intune kell használniuk, mint például a Microsoft Edge.

A feltételes hozzáféréssel és az egyszeri bejelentkezéssel kapcsolatos további Microsoft Edge lásd a [Microsoft Edge Mobile Support for Conditional Access and Single Sign-on Now Generally Available (A feltételes](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) hozzáférés mobiltámogatása és az egyszeri bejelentkezés mostantól általánosan elérhető) blogbejegyzést. Az ügyfélalkalmazások alkalmazásalapú feltételes hozzáféréssel vagy eszközalapú [](../conditional-access/require-managed-devices.md)feltételes hozzáféréssel való beállításával kapcsolatos további információkért lásd: Webes hozzáférés kezelése Microsoft Intune szabályzat által védett [böngészővel.](/intune/apps/app-configuration-managed-browser) [](../conditional-access/app-based-conditional-access.md)

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD-jogosultságkezelés (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Más  
**Termékképesség:** Jogosultságkezelés

Az Azure AD-jogosultságkezelés egy új identitáskezelési funkció, amellyel a szervezetek nagy léptékben kezelhetik az identitás- és hozzáférési életciklust. Ez az új funkció automatizálja a hozzáférési kérelmek munkafolyamatait, hozzáférés-hozzárendeléseit, felülvizsgálatait és lejáratát a csoportok, alkalmazások és SharePoint Online-webhelyek között.

Az Azure AD-jogosultságok kezelésével hatékonyabban kezelheti az alkalmazottak és a szervezeten kívüli felhasználók hozzáférését is, akiknek hozzá kell férni ezekhez az erőforrásokhoz.

További információ: [Mi az az Azure AD-jogosultságkezelés?](../governance/entitlement-management-overview.md#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Külső fél integrációja  

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

[SAP Cloud Platform Identity Authentication,](../saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial.md) [RingCentral,](../saas-apps/ringcentral-provisioning-tutorial.md) [SpaceIQ,](../saas-apps/spaceiq-provisioning-tutorial.md) [Miro,](../saas-apps/miro-provisioning-tutorial.md) [Cloudgate](../saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial.md), [Infor CloudSuite,](../saas-apps/infor-cloudsuite-provisioning-tutorial.md) [OfficeSpace Software,](../saas-apps/officespace-software-provisioning-tutorial.md) [Priority Matrix](../saas-apps/priority-matrix-provisioning-tutorial.md)

A szervezet automatizált felhasználói fiókok automatikus kiépítése által történő biztonságossá tevésével kapcsolatos további információkért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val.](../app-provisioning/user-provisioning.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás katalógusban – 2019. november

**Írja be a következőt:** Új funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** külső fél integrációja

2019 novemberében az alábbi 21, összevonási támogatással bővült új alkalmazás az alkalmazásgyűjteményben:

[Airtable](../saas-apps/airtable-tutorial.md), [Hootsuite,](../saas-apps/hootsuite-tutorial.md) [Blue Access for Members (BAM)](../saas-apps/blue-access-for-members-tutorial.md), [Bitly](../saas-apps/bitly-tutorial.md), [Riva,](../saas-apps/riva-tutorial.md) [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](../saas-apps/negometrixportal-tutorial.md), TeamsChamp , Motus , [MyAryaka](../saas-apps/myaryaka-tutorial.md), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](../saas-apps/visma-tutorial.md), [OneDesk](../saas-apps/onedesk-tutorial.md), [Foko Retail](../saas-apps/foko-retail-tutorial.md), [Qmarkets Idea & Innovation Management](../saas-apps/qmarkets-idea-innovation-management-tutorial.md), [Netskope User Authentication](../saas-apps/netskope-user-authentication-tutorial.md), [uniFLOW Online](../saas-apps/uniflow-online-tutorial.md), [Claromentis](../saas-apps/claromentis-tutorial.md), [Jisc Student Regisztrációs](../saas-apps/jisc-student-voter-registration-tutorial.md), [e4enable](https://portal.e4enable.com/) [](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279) [](../saas-apps/motus-tutorial.md)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Új és továbbfejlesztett Azure AD-alkalmazásgyűjtemény

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Sso

Frissítettük az Azure AD-alkalmazásgyűjteményt, hogy könnyebben talál olyan előre integrált alkalmazásokat, amelyek támogatják a kiépítést, a OpenID Connect és az SAML-t az Azure Active Directory bérlőn.

További információ: Alkalmazás hozzáadása a Azure Active Directory [bérlőhöz.](../manage-apps/add-application-portal.md)

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Az alkalmazás-szerepkör definíciójának hosszkorlátja 120 karakterről 240 karakterre nőtt

**Írja be a következőt:** Módosított funkció  
**Szolgáltatáskategória:** Vállalati alkalmazások  
**Termékképesség:** Sso

Az ügyfelektől úgy tudjuk, hogy egyes alkalmazásokban és szolgáltatásokban túl rövid, 120 karakternél rövidebb az alkalmazásszerepkre vonatkozó definícióérték. Válaszként megnövelte a szerepkörérték-definíció maximális hosszát 240 karakterre.

Az alkalmazásspecifikus szerepkör-definíciók használatával kapcsolatos további információkért lásd: Alkalmazás-szerepkörök hozzáadása az alkalmazásban, és fogadásuk a [jogkivonatban.](../develop/howto-add-app-roles-in-azure-ad-apps.md)

---

## <a name="october-2019"></a>2019. október

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Az identityRiskEvent API elalasztása a Azure AD Identity Protection észleléséhez

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Identity Protection **termékképesség:** Identity Security & Protection

A fejlesztői visszajelzések prémium szintű Azure AD P2-előfizetők mostantól összetett lekérdezéseket végezhetnek a Azure AD Identity Protection kockázatészlelési adatain az új riskDetection API használatával a Microsoft Graph. A meglévő [identityRiskEvent](/graph/api/resources/identityriskevent?view=graph-rest-beta&preserve-view=true) API bétaverziója **2020. január 10-hez** nem ad vissza adatokat. Ha a szervezete az identityRiskEvent API-t használja, akkor az új riskDetection API-t kell használnia.

Az új riskDetection API-val kapcsolatos további információkért tekintse meg a [Kockázatészlelési API referenciadokumentációját.](/graph/api/resources/riskdetection)

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>alkalmazásproxy SameSite Attribute és a Chrome 80 támogatása

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Alkalmazásproxy **termékképessége:** Access Control

Néhány héttel a Chrome 80 böngésző kiadása előtt tervezzük frissíteni, hogy a alkalmazásproxy cookie-k hogyan kezelik a **SameSite** attribútumot. A Chrome 80 megjelenésében minden olyan cookie, amely nem adja meg a **SameSite** attribútumot, a rendszer úgy kezeli, mintha a következőre lett volna állítva: `SameSite=Lax` .

A változás potenciálisan negatív hatásainak elkerülése érdekében a következő alkalmazásproxy a munkamenet-cookie-kat:

- A Biztonságos cookie használata beállítás alapértelmezett értékének **beállítása** Igen **értékre.**

- A **SameSite** attribútum alapértelmezett értékének Beállítása **Nincs értékre.**

    >[!NOTE]
    > alkalmazásproxy hozzáférési cookie-kat mindig kizárólag biztonságos csatornákon keresztül átvitelre került. Ezek a módosítások csak a munkamenet-cookie-kra vonatkoznak.

További információ a cookie alkalmazásproxy beállításairól: [Cookie-beállítások](../manage-apps/application-proxy-configure-cookie-settings.md)a helyszíni alkalmazások eléréséhez a Azure Active Directory.

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>Alkalmazásregisztrációk (örökölt) és alkalmazáskezelés az alkalmazásregisztrációs portálon (apps.dev.microsoft.com) már nem érhető el

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** N/A **termékképesség:** Fejlesztői élmény

Az Azure AD-fiókkal rendelkező felhasználók többé nem regisztrálnak vagy kezelnek alkalmazásokat az alkalmazásregisztrációs portálon (apps.dev.microsoft.com), és nem regisztrálnak és kezelnek alkalmazásokat a Alkalmazásregisztrációk (örökölt) felhasználói Azure Portal.

Az új felhasználói élményről a Alkalmazásregisztrációk útmutató Alkalmazásregisztrációk útmutatóban Azure Portal [további információt.](../develop/quickstart-register-app.md)

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>A felhasználóknak többé nem kell újra regisztrálniuk a felhasználónkénti MFA-ból a feltételes hozzáférésen alapuló MFA-be való migrálás során

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** MFA **termékképesség:** Identity Security & Protection

Kijavítottunk egy ismert problémát, amely miatt a felhasználóknak újra kellett regisztrálniuk őket, ha a felhasználónkénti többtényezős hitelesítés (MFA) le lett tiltva, majd egy feltételes hozzáférési szabályzaton keresztül engedélyezték az MFA-t.

Ha meg kell követelni a felhasználóktól az újra regisztrációt, válassza az **MFA szükséges** újra regisztrálása lehetőséget a felhasználó hitelesítési módszerei közül az Azure AD portálon. A felhasználók felhasználónkénti MFA-ból feltételes hozzáférésen alapuló MFA-re való áttelepítésére vonatkozó további információkért lásd: Felhasználók átalakítása felhasználónkénti MFA-ról feltételes hozzáférésen alapuló [MFA-re.](../authentication/howto-mfa-getstarted.md#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Új képességek jogcímek átalakítására és elküldére az SAML-jogkivonatban

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Enterprise Apps **Termékképesség:** SSO

További képességeket adtunk hozzá, amelyek segítségével testreszabhatja és elküldhatja a jogcímeket az SAML-jogkivonatban. Ezek az új képességek a következők:

- További jogcím-átalakítási függvények, amelyek segítenek a jogcímben küldött érték módosításában.

- Több átalakítás alkalmazása egyetlen jogcímre.

- Lehetőség a jogcímforrás megadására annak a felhasználónak a típusa és a csoport alapján, amelyhez a felhasználó tartozik.

Az új képességekkel kapcsolatos részletes információkért, többek között a használatukról lásd: Az SAML-jogkivonatban kibocsátott jogcímek testreszabása [vállalati alkalmazásokhoz.](../develop/active-directory-saml-claims-customization.md)

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Új Saját bejelentkezések oldal végfelhasználók számára az Azure AD-ban

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** A hitelesítések (bejelentkezések) **termékképessége:** A jelentéskészítés & monitorozása

Hozzáadtunk egy új  Bejelentkezési oldal () et, amely lehetővé teszi, hogy a szervezet felhasználói megtekintsék a legutóbbi bejelentkezési előzményeiket, és szokatlan tevékenységeket https://mysignins.microsoft.com) keressnek. Ez az új oldal lehetővé teszi, hogy a felhasználók a következőt látják:

- Ha valaki megpróbálja kitalálni a jelszavát.

- Ha egy támadó sikeresen bejelentkezett a fiókjába és melyik helyről.

- Mely alkalmazásokat próbálta elérni a támadó.

További információkért lásd: A felhasználók most már ellenőrizhetik a bejelentkezési előzményeikben a szokatlan [tevékenységek blogját.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Virtuális Azure AD Domain Services (Azure AD DS) áttelepítése Azure Resource Manager virtuális hálózatokra

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Azure AD Domain Services **termékképesség:** Azure AD Domain Services

A klasszikus virtuális hálózatokon elakadt ügyfeleinknek nagyszerű híreink vannak! Most már végrehajthat egy egyszeres migrálást egy klasszikus virtuális hálózatról egy meglévő Resource Manager virtuális hálózatra. Az Resource Manager virtuális hálózatra való áthelyezés után kihasználhatja a további és frissített funkciókat, például a jelszó-szabályzatok, az e-mail-értesítések és az auditnaplók előnyeit.

További információ: [Preview - Migrate Azure AD Domain Services from the Classic virtual network model to Resource Manager.](../../active-directory-domain-services/migrate-from-classic-vnet.md)

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>A Azure AD B2C oldal szerződéselrendezésének frissítései

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** B2C – Fogyasztói identitáskezelési **termékképesség:** B2B/B2C

Bevezettünk néhány új módosítást az oldalszerződés 1.2.0-s verziójában a Azure AD B2C. Ebben a frissített verzióban most már vezérelheti az elemek betöltési sorrendjét, ami segít leállítani a stíluslap (CSS) betöltésekor történik.

Az oldalszerződésen végrehajtott módosítások teljes listáját a Verzióváltozási [naplóban láthatja.](../../active-directory-b2c/page-layout.md#other-pages-providerselection-claimsconsent-unifiedssd)

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Frissítés a Saját alkalmazások lapra az új munkaterületekkel együtt (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Saját alkalmazások **termékképesség:** Access Control

Mostantól testre szabhatja, hogy a cég felhasználói hogyan találják meg és hogyan férhetnek hozzá a vadonatúj Saját alkalmazások felhasználói élményhez, beleértve az új munkaterületek funkcióját is, így megkönnyítheti számukra az alkalmazások keresését. Az új munkaterületek funkció szűrőként működik a szervezet felhasználói számára már elérhető alkalmazásokhoz.

További információ az új felhasználói élmény Saját alkalmazások és a munkaterületek létrehozásáról: Munkaterületek létrehozása a Saját alkalmazások [(előzetes verzió) portálon.](../manage-apps/access-panel-collections.md)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>A havi aktív felhasználóalapú számlázási modell támogatása (általánosan elérhető)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2C – Fogyasztói identitáskezelési **termékképesség:** B2B/B2C

Azure AD B2C már támogatja a havi aktív felhasználók (MAU) számlázását. A MAU számlázása azon egyedi felhasználók számán alapul, akik hitelesítési tevékenységet folytatnak egy naptári hónapban. A meglévő ügyfelek bármikor válthatnak erre az új számlázási módszerre.

2019. november 1-től kezdve minden új ügyfélnek automatikusan ezzel a módszerrel kell számlázva lennie. Ez a számlázási módszer előnyös az ügyfelek számára a költségmegtakarítások és az előre tervezés lehetősége révén.

További információ: [Frissítés havi aktív felhasználók számlázási modelljére.](../../active-directory-b2c/billing.md#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Új összevont alkalmazások, amelyek a Azure AD alkalmazás 2019. októberében érhetők el

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2019 októberében hozzáadtunk ezt a 35 új, összevonási támogatást támogató alkalmazást az alkalmazásgyűjteményhez:

[In Case of Crisis – Mobile](../saas-apps/in-case-of-crisis-mobile-tutorial.md), [Juno Journey](../saas-apps/juno-journey-tutorial.md), [ExponentHR](../saas-apps/exponenthr-tutorial.md), [Tact](https://www.tact.ai/products/tact-assistant), [OpusCapita Cash Management,](https://appsource.microsoft.com/product/web-apps/opuscapitagroupoy-1036255.opuscapita-cm) [Salestim](https://www.salestim.com/), [Learnster](../saas-apps/learnster-tutorial.md), [Dynatrace,](../saas-apps/dynatrace-tutorial.md) [HunchBuzz,](https://login.hunchbuzz.com/integrations/azure/process) [Freshworks](../saas-apps/freshworks-tutorial.md), [eCornell](../saas-apps/ecornell-tutorial.md), [ShipHazmat](../saas-apps/shiphazmat-tutorial.md), [Netskope Cloud Security](../saas-apps/netskope-cloud-security-tutorial.md), [Contentful](../saas-apps/contentful-tutorial.md), [Bindtuning,](https://bindtuning.com/login) [HireVue Coordinate – Európa](https://www.hirevue.com/), [HireVue Koordináták – USOnly](https://www.hirevue.com/), [HireVue](https://www.hirevue.com/)Koordináták – US , [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](../saas-apps/cloudmore-tutorial.md), [Visit.org](../saas-apps/visitorg-tutorial.md), [Cambium Xirrus EasyPass portál](https://login.xirrus.com/azure-signup), [Paylocity,](../saas-apps/paylocity-tutorial.md) [Mail Luck!](../saas-apps/mail-luck-tutorial.md), [Teamie,](https://theteamie.com/)Velocity for Teams , [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL,](../saas-apps/eab-navigate-impl-tutorial.md) [ScreenMeet,](https://console.screenmeet.com/) [Mert Point,](https://pi.ompnt.com/) [Speaking Email for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Beszélő](https://velocity.peakup.org/teams/login)e-mail az [Office 365 Directhez (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [Exact Exact Exact SSO](../saas-apps/exactcare-sso-tutorial.md), [iHealthHome Care Navigation System](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása a Azure Active Directory [alkalmazásgyűjteményben.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Az Összevont biztonság menüelem az Azure AD portálon

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Identity Protection **termékképesség:** Identity Security & Protection

Most már elérheti az összes elérhető Azure AD  biztonsági funkciót az  új Biztonság menüelemből, valamint a keresősávból a Azure Portal. Emellett a Biztonság  – Első lépések nevű új Biztonsági kezdőlap a nyilvános dokumentációra, a biztonsági útmutatásra és az üzembe helyezési útmutatókra mutató hivatkozásokat is tartalmaz.

Az új **Security (Biztonság)** menü a következőket tartalmazza:

- Feltételes hozzáférés
- Identity Protection
- Security Center
- Identitás biztonsági pontszáma
- Hitelesítési módszerek
- MFA
- Kockázati jelentések – Kockázatos felhasználók, Kockázatos bejelentkezések, Kockázatészlelések
- És még sok más...

További információ: [Biztonság – Első lépések.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Az Office 365-csoportok elévülési szabályzata automatikusan új

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Csoportkezelési **termékképesség:** Identitás-életciklus kezelése

Az Office 365-csoportok elévülési szabályzata úgy lett továbbfejlesztve, hogy automatikusan megújítsa a tagok által aktívan használt csoportokat. A csoportok automatikusan újak lesznek az összes Office 365-alkalmazás, köztük az Outlook, a SharePoint és a Teams felhasználói tevékenysége alapján.

Ez a fejlesztés segít csökkenteni a csoport elévülési értesítését, és segít abban, hogy az aktív csoportok továbbra is elérhetők maradnak. Ha már rendelkezik aktív elévülési szabályzattal az Office 365-csoportokhoz, nem kell semmit megtennie az új funkció bekapcsolás érdekében.

További információ: Elévülési szabályzat [konfigurálása Office 365-csoportokhoz.](../enterprise-users/groups-lifecycle.md)

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Frissített Azure AD Domain Services (Azure AD DS) létrehozása

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Azure AD Domain Services **termékképesség:** Azure AD Domain Services

Frissítettük a Azure AD Domain Services (Azure AD DS), hogy új és továbbfejlesztett létrehozási élményt biztosítsunk, így csupán három kattintással létrehozhat egy felügyelt tartományt. Emellett most már feltöltheti és üzembe helyezheti Azure AD DS sablonból.

További információ: [Oktatóanyag: Új](../../active-directory-domain-services/tutorial-create-instance.md)Azure Active Directory Domain Services létrehozása és konfigurálása.

---

## <a name="september-2019"></a>2019. szeptember

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>A változás megtervezése: A Power BI elalasztása

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** **Jelentéskészítési termék képessége:** A jelentéskészítés & monitorozása

2019. október 1-től a Power BI elalasztja az összes tartalomcsomagot, beleértve az Azure AD Power BI tartalomcsomagot is. A tartalomcsomag alternatívájaként az Azure AD-munkafüzetek használatával betekintést nyerhet az Azure AD-hez kapcsolódó szolgáltatásokba. További munkafüzetek is elérhetőek, például a feltételes hozzáférési szabályzatokkal kapcsolatos munkafüzetek csak jelentési módban, alkalmazás-hozzájáruláson alapuló elemzések stb.

A munkafüzetekkel kapcsolatos további információkért lásd: How to use Azure Monitor workbooks for Azure Active Directory reports (Munkafüzetek használata [Azure Active Directory jelentésekhez).](../reports-monitoring/howto-use-azure-monitor-workbooks.md) A tartalomcsomagok elalasztásról a Sablonalkalmazások általános elérhetőségének bejelentése Power BI [blogbejegyzésben](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) talál további információt.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>A profilom a fiók Microsoft Office és integrálása

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Saját profil/fiók **termékképessége:** Együttműködés

Októbertől a Saját profil felhasználói élmény lesz a Saját fiók. Ennek a változásnak a részeként mindenhol, ahol jelenleg ez áll, a **Saját profil** a **Következőre változik: Saját fiók.** Az elnevezési módosítás és néhány kialakítási fejlesztés mellett a frissített felhasználói élmény további integrációt biztosít a Microsoft Office-fiók oldalával. Pontosabban, az Office-telepítéseket és -előfizetéseket  az Áttekintési fiók oldalról, valamint az Office-hoz kapcsolódó kapcsolattartási beállításokhoz férhet hozzá az Adatvédelem **lapon.**

További információ a Saját profil (előzetes verzió) felhasználói élményről: Saját [profil (előzetes verzió) portál áttekintése.](../user-help/my-account-portal-overview.md)

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Csoportok és tagok tömeges kezelése CSV-fájlokkal az Azure AD portálon (nyilvános előzetes verzió)

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Csoportkezelési **termékképesség:** Együttműködés

Örömmel jelentjük be, hogy az Azure AD portálon elérhető a csoportos csoportkezelési funkciók nyilvános előzetes verziója. Mostantól csv-fájllal és az Azure AD-portállal kezelheti a csoportokat és taglistákat, beleértve a következőket:

- Tagok hozzáadása vagy eltávolítása egy csoportból.

- A csoportok listájának letöltése a könyvtárból.

- Egy adott csoport csoporttagok listájának letöltése.

További információ: Tagok tömeges [hozzáadása,](../enterprise-users/groups-bulk-import-members.md)Tagok tömeges [eltávolítása,](../enterprise-users/groups-bulk-remove-members.md)Tagok tömeges letöltése [lista](../enterprise-users/groups-bulk-download-members.md)és Csoportok tömeges [letöltése lista.](../enterprise-users/groups-bulk-download.md)

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>A dinamikus hozzájárulás mostantól támogatott egy új rendszergazdai hozzájárulási végponton keresztül

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Hitelesítések (bejelentkezések) **Termékképesség:** Felhasználói hitelesítés

Létrehoztunk egy új rendszergazdai hozzájárulási végpontot a dinamikus hozzájárulás támogatásához, ami hasznos lehet az olyan alkalmazások számára, amelyek a Microsoft Identity platformon szeretnék használni a dinamikus hozzájárulási modellt.

Az új végpont használatával kapcsolatos további információkért lásd: [A rendszergazdai hozzájárulási végpont használata.](../develop/v2-admin-consent.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Új összevont alkalmazások a Azure AD alkalmazás – 2019. szeptember

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Enterprise Apps **Termékképesség:** külső féltől származó integráció

2019 szeptemberében az alábbi 29, összevonási támogatással bővült új alkalmazás az alkalmazásgyűjteményben:

[ScheduleLook,](https://schedulelook.bbsonlineservices.net/) [MS Azure SSO Access for Ethidex Compliance Office &trade; –](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md)Egyszeri bejelentkezés, [iServer Portal,](../saas-apps/iserver-portal-tutorial.md) [SKYSITE,](../saas-apps/skysite-tutorial.md) [Concur Travel and Expense](../saas-apps/concur-travel-and-expense-tutorial.md), [WorkBoard](../saas-apps/workboard-tutorial.md), `https://apps.yeeflow.com/` , [ARC-létesítmények,](../saas-apps/arc-facilities-tutorial.md) [LuwareListus csapat,](https://stratus.emea.luware.cloud/login) [Wide Ideas](https://wideideas.online/wideideas/), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), [JDLT Client Hub](https://clients.jdlt.co.uk/login), [RENRAKU,](../saas-apps/renraku-tutorial.md) [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma Cloud](../saas-apps/prisma-cloud-tutorial.md), `https://app.penneo.com/` `https://app.testhtm.com/settings/email-integration` [Cintoo Cloud,](https://aec.cintoo.com/login) [Whitesource,](../saas-apps/whitesource-tutorial.md) [Hosted Heritage Online SSO,](../saas-apps/hosted-heritage-online-sso-tutorial.md) [IDC,](../saas-apps/idc-tutorial.md) [Tohr,CIL](../saas-apps/cakehr-tutorial.md), [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube,](../saas-apps/sonarqube-tutorial.md) [Adobe Identity Management,](../saas-apps/tutorial-list.md)Discovery Benefits [SSO,](../saas-apps/discovery-benefits-sso-tutorial.md) [Amelio](https://app.amelio.co/), [](../saas-apps/bis-tutorial.md)`https://itask.yipinapp.com/`

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az alkalmazás [Azure Active Directory katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-azure-ad-global-reader-role"></a>Új Globális Azure AD-olvasó szerepkör

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Azure AD-szerepkörök **Termékképesség:** Access Control

2019. szeptember 24-től kezdve egy új, globális olvasónak nevezett Azure Active Directory (AD) szerepkört vezetünk be. Ez a bevezetés az éles környezetben és a globális felhő ügyfeleinél (GCC) kezdődik, és októberben világszerte be lesz fejezve.

A globális olvasó szerepkör a globális rendszergazda csak olvasási megfelelője. Az ebben a szerepkörben lévő felhasználók olvashatják a beállításokat és a rendszergazdai információkat Microsoft 365 szolgáltatások között, de nem tudnak felügyeleti műveleteket. Létrehoztuk a Globális olvasó szerepkört, hogy csökkentsük a szervezet globális rendszergazdáinak számát. Mivel a globális rendszergazdai fiókok hatékonyak és ki vannak téve a támadásoknak, javasoljuk, hogy ötnél kevesebb globális rendszergazdával legyen. A tervezéshez, auditáláshoz és vizsgálatokhoz a globális olvasó szerepkör használatát javasoljuk. Javasoljuk továbbá, hogy használja a globális olvasó szerepkört más korlátozott rendszergazdai szerepkörökkel, például az Exchange-rendszergazda szerepkörsel együtt, hogy a globális rendszergazdai szerepkör nélkül is el tud munkához látni.

A Globális olvasó szerepkör az új Microsoft 365 Felügyeleti központtal, az Exchange felügyeleti központtal, a Teams felügyeleti központtal, az Security Center-val, a Megfelelőségi központtal, az Azure AD felügyeleti központtal és a Eszközkezelés felügyeleti központtal működik.

>[!NOTE]
> A nyilvános előzetes verzió elején a globális olvasói szerepkör nem működik a következővel: SharePoint, Privileged Access Management, Ügyfélszéf, bizalmasság címkék, Teams-életciklus, Teams Reporting & Call Analytics, Teams IP Phone Eszközkezelés és Teams alkalmazáskatalógus.

További információ: [Rendszergazdai szerepkör engedélyei](../roles/permissions-reference.md)a Azure Active Directory.

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Helyszíni jelentéskiszolgáló elérése a Power BI Mobile alkalmazásból az Azure Active Directory alkalmazásproxy

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Alkalmazásproxy-termékképesség:** Access Control

Az Power BI mobilalkalmazás és az Azure AD alkalmazásproxy közötti új integráció lehetővé teszi, hogy biztonságosan jelentkezzen be az Power BI-mobilalkalmazásba, és megtekintse a szervezet helyszíni alkalmazásokban tárolt Power BI jelentéskészítő kiszolgáló.

Az alkalmazással kapcsolatos Power BI Mobile, beleértve az alkalmazás letöltési helyével kapcsolatos információkat a Power BI [oldalán.](https://powerbi.microsoft.com/mobile/) A Power BI mobilalkalmazás Azure AD alkalmazásproxy-val való beállításával kapcsolatos további információkért lásd: Távoli hozzáférés engedélyezése Power BI Mobile az [Azure AD alkalmazásproxy.](../manage-apps/application-proxy-integrate-with-power-bi.md)

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>Elérhető az AzureADPreview PowerShell-modul új verziója

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Egyéb **termékképesség:** Címtár

Az AzureADPreview modul új parancsmagokkal bővült, amelyek segítségével egyéni szerepköröket definiálhat és rendelhet hozzá az Azure AD-ben, többek között a következőket:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>A Azure AD Connect

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Egyéb **termékképesség:** Címtár

Az automatikus frissítéssel Azure AD Connect frissített verzióját adták ki. Ez az új verzió számos új funkciót, fejlesztést és hibajavítást tartalmaz. További információ erről az új verzióról: [Azure AD Connect: Verzió kiadási előzményei.](../hybrid/reference-connect-version-history.md#14250)

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Az Azure Multi-Factor Authentication-kiszolgáló (MFA) 8.0.2-es verziója már elérhető

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** MFA **termékképesség:** Identity Security & Protection

Ha Ön már ügyfél, és 2019. július 1. előtt aktiválta az MFA-kiszolgálót, letöltheti az MFA-kiszolgáló legújabb verzióját (8.0.2-es verzió). Ebben az új verzióban:

- Kijavítottunk egy hibát, így amikor az Azure AD-szinkronizálás letiltottról Engedélyezettre módosít egy felhasználót, a rendszer e-mailt küld a felhasználónak.

- Kijavítottunk egy hibát, hogy az ügyfelek sikeresen frissítsen, miközben továbbra is használják a Címkék funkciót.

- Hozzá van adva a Egyesült Államok (+383) országkódja.

- A MultiFactorAuthSvc.log naplóhoz hozzáadta az egyszeres mellőzéses naplózást.

- Jobb teljesítmény a Web Service SDK-hoz.

- Egyéb kisebb hibák kijavítva.

2019. július 1-től kezdve a Microsoft nem kínál MFA-kiszolgálót az új üzembe helyezéshez. A többtényezős hitelesítést igénylő új ügyfeleknek felhőalapú Azure AD Multi-Factor Authenticationt kell használniuk. További információ: [Planning a cloud-based Azure AD Multi-Factor Authentication deployment (Felhőalapú Azure AD Multi-Factor Authentication üzembe helyezésének megtervezése).](../authentication/howto-mfa-getstarted.md)

---

## <a name="august-2019"></a>2019. augusztus

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>A csoportok bővített keresése, szűrése és rendezése az Azure AD portálon érhető el (nyilvános előzetes verzió)

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Csoportkezelési **termékképesség:** Együttműködés

Örömmel jelentjük be, hogy az Azure AD portálon elérhető a továbbfejlesztett csoportokhoz kapcsolódó funkciók nyilvános előzetes verziója. Ezek a fejlesztések a következő funkciók biztosításával segítik a csoportok és taglisták jobb kezelését:

- Speciális keresési képességek, például csoportlistákon való részsztring-keresés.
- Speciális szűrési és rendezési beállítások a tag- és tulajdonoslistákon.
- Új keresési képességek a tag- és tulajdonoslistákhoz.
- Pontosabb csoportszám nagy csoportok esetében.

További információ: [Csoportok kezelése a Azure Portal.](./active-directory-groups-members-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Új egyéni szerepkörök érhetők el az alkalmazásregisztráció kezeléséhez (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Azure AD-szerepkörök **Termékképesség:** Access Control

Az egyéni szerepkörök (amelyek Azure AD P1 vagy P2 előfizetéssel érhetők el) mostantól részletes hozzáférést nyújtanak azáltal, hogy adott engedélyekkel rendelkező szerepkör-definíciókat hozhat létre, majd adott erőforrásokhoz rendelheti őket. Jelenleg egyéni szerepköröket hozhat létre az alkalmazásregisztrációk kezeléséhez szükséges engedélyekkel, majd hozzárendelheti a szerepkört egy adott alkalmazáshoz. További információ az egyéni szerepkörökről: Egyéni rendszergazdai szerepkörök a [Azure Active Directory (előzetes verzió)](../roles/custom-overview.md).

Ha további, jelenleg nem látható engedélyekre vagy támogatott erőforrásokra van szüksége, küldjön visszajelzést az [Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) visszajelzési webhelyére, és kérését hozzáadjuk a frissítési térképhez.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Az új üzembehelyezési naplók segíthetnek az alkalmazás üzembe helyezésének figyelésében és hibaelhárításában (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** App Provisioning **Product képesség:** Identitás-életciklus kezelése

Új kiépítési naplók érhetők el, amelyek segítenek a felhasználók és csoportok üzembe helyezésének figyelésében és hibaelhárításában. Ezek az új naplófájlok a következőkről tartalmaznak információkat:

- A [ServiceNow-ban](../saas-apps/servicenow-provisioning-tutorial.md) sikeresen létrehozott csoportok
- Milyen szerepköröket importáltak az [AWS Single-Account Accessből](../saas-apps/amazon-web-service-tutorial.md#configure-and-test-azure-ad-sso-for-aws-single-account-access)
- Milyen alkalmazottakat nem importáltak a [Workdayből?](../saas-apps/workday-inbound-tutorial.md)

További információ: [Kiépítési jelentések a Azure Active Directory portálon (előzetes verzió).](../reports-monitoring/concept-provisioning-logs.md)

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Új biztonsági jelentések az összes Azure AD-rendszergazda számára (általánosan elérhető)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Identity Protection **termékképesség:** Identity Security & Protection

Alapértelmezés szerint minden Azure AD-rendszergazda hamarosan hozzáférhet a modern biztonsági jelentésekhez az Azure AD-ban. Szeptember végéig használhatja a modern biztonsági jelentések tetején található szalagcímet, hogy visszatérjen a régi jelentésekhez.

A modern biztonsági jelentések további képességeket biztosítanak a régebbi verziókból, például:

- Speciális szűrés és rendezés
- Tömeges műveletek, például a felhasználói kockázatok elvetését
- Sérült vagy biztonságos entitások megerősítése
- Kockázati állapot, amely a következő kockázatokat fedi le: Veszélyeztetett, Elvetve, Szervizelt és Megerősített biztonságú
- Új kockázatokkal kapcsolatos észlelések (az előfizetők prémium szintű Azure AD számára)

További információ: [Kockázatos](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users)felhasználók, [Kockázatos bejelentkezések](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)és [Kockázatészlelések.](../identity-protection/howto-identity-protection-investigate-risk.md#risk-detections)

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>A felhasználó által hozzárendelt felügyelt identitás elérhető Virtual Machines és Virtual Machine Scale Sets (általánosan elérhető)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Az Azure-erőforrások felügyelt identitása **Termékképesség:** Fejlesztői élmény

A felhasználó által hozzárendelt felügyelt identitások mostantól általánosan elérhetők a Virtual Machines és Virtual Machine Scale Sets. Ennek részeként az Azure létrehozhat egy olyan identitást az Azure AD-bérlőben, amelyet a használt előfizetés megbízhatónak használ, és hozzárendelhető egy vagy több Azure-szolgáltatáspéldányhoz. A felhasználó által hozzárendelt felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../managed-identities-azure-resources/overview.md).

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>A felhasználók mobilalkalmazással vagy hardvertoken használatával állíthatják vissza jelszavukat (általánosan elérhető)

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Önkiszolgáló jelszó-visszaállítási **termékképesség:** Felhasználói hitelesítés

Azok a felhasználók, akik regisztráltak egy mobilalkalmazást a szervezetnél, mostantól új jelszót állíthatnak vissza a Microsoft Authenticator-alkalmazás értesítésének jóváhagyásával, vagy egy kód megadásával a mobilalkalmazásból vagy hardvertokenből.

További információ: [How it works: Azure AD self-service password reset (Az Azure AD önkiszolgáló jelszó-visszaállítás).](../authentication/concept-sspr-howitworks.md) További információ a felhasználói élményről: A saját munkahelyi vagy iskolai jelszó alaphelyzetbe [állítása – áttekintés.](../user-help/active-directory-passwords-reset-register.md)

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET nem MSAL.NET a megosztott gyorsítótárat a több nevében történő használatra

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** A hitelesítések (bejelentkezések) **termékképessége:** Felhasználóhitelesítés

Az Azure AD hitelesítési kódtár (ADAL.NET) 5.0.0-s előzetes verziójától kezdve az alkalmazásfejlesztőknek fiókonként egy gyorsítótárat kell szerializálni a webalkalmazások és webes [API-k esetében.](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api) Ellenkező esetben egyes forgatókönyvek, amelyek a Java-hoz való, nevében történő folyamatot használják, valamint a bizonyos alkalmazási helyzeteket, a jogosultsági szint emelését [](../develop/scenario-web-api-call-api-app-configuration.md?tabs=java) `UserAssertion` eredményezhetik. A biztonsági rés elkerülése érdekében a ADAL.NET mostantól figyelmen kívül hagyja a Microsoft Authentication Library for dotnet (MSAL.NET) megosztott gyorsítótárat a saját nevében történő használatra.

További információ erről a problémáról: A [Azure Active Directory jogosultságszint-emelési biztonsági résének létrehozása a hitelesítési könyvtárban.](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Új összevont alkalmazások a Azure AD alkalmazás – 2019. augusztus

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Enterprise Apps **Termékképesség:** külső féltől származó integráció

2019 augusztusában az alábbi 26 új, összevonási támogatást támogató alkalmazást adtunk hozzá az alkalmazásgyűjteményhez:

[Platform](../saas-apps/civic-platform-tutorial.md), [Amazon Business](../saas-apps/amazon-business-tutorial.md), [ProNovos Ops Manager](../saas-apps/pronovos-ops-manager-tutorial.md), [Cognidox](../saas-apps/cognidox-tutorial.md), [Viareport's Inativ Portal (Európa)](../saas-apps/viareports-inativ-portal-europe-tutorial.md), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](../saas-apps/robin-tutorial.md), [Academy Matrix,](../saas-apps/academy-attendance-tutorial.md) [Priority Matrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace,](https://cousto.platformers.be/account/login) [Uploadtric](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](../saas-apps/carbonite-endpoint-backup-tutorial.md), CP [Cincom](../saas-apps/cpqsync-by-cincom-tutorial.md), [Chargebee](../saas-apps/chargebee-tutorial.md), [deliver.media &trade; portál,](https://portal.deliver.media) [Frontline Education,](../saas-apps/frontline-education-tutorial.md) [F5](https://www.f5.com/products/security/access-policy-manager), [törlő AD connect,](https://www.stashcat.com) [Villogás,](../saas-apps/blink-tutorial.md) [Vocoli,](../saas-apps/vocoli-tutorial.md) [ProNovos Analytics](../saas-apps/pronovos-analytics-tutorial.md), [Sigstr,](../saas-apps/sigstr-tutorial.md) [Watch](../saas-apps/darwinbox-tutorial.md) [by Colors](../saas-apps/watch-by-colors-tutorial.md), [Harness](../saas-apps/harness-tutorial.md), [EAB Navigate Strategic Care](../saas-apps/eab-navigate-strategic-care-tutorial.md)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>Az AzureAD PowerShell és az AzureADPreview PowerShell-modulok új verziói érhetők el

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Egyéb **termékképesség:** Címtár

Az AzureAD és az AzureAD előzetes verziójú PowerShell-moduljainak új frissítései érhetők el:

- Az AzureAD-modulban egy új paraméter lett `-Filter` `Get-AzureADDirectoryRole` hozzáadva a paraméterhez. Ez a paraméter segít a parancsmag által visszaadott címtár-szerepkörök szűrésében.
- Az AzureADPreview modul új parancsmagokkal bővült, amelyek segítségével egyéni szerepköröket definiálhat és rendelhet hozzá az Azure AD-ben, például:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>A dinamikuscsoport-szabályszerkesztő felhasználói felületének fejlesztései a Azure Portal

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Csoportkezelési **termékképesség:** Együttműködés

A dinamikus csoport szabályszerkesztője a Azure Portal-ban elérhetővé tett néhány felhasználói felületi fejlesztést, így könnyebben állíthat be új szabályokat, vagy módosíthatja a meglévő szabályokat. Ez a fejlesztés lehetővé teszi, hogy csak egy helyett legfeljebb öt kifejezéssel hozzon létre szabályokat. Frissítettük az eszköztulajdonságok listáját is, hogy eltávolítsuk az elavult eszköztulajdonságokat.

További információ: [Dinamikus tagsági szabályok kezelése.](../enterprise-users/groups-dynamic-membership.md)

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>Új Microsoft Graph hozzáférési felülvizsgálatokkal használható alkalmazásengedély

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Hozzáférési felülvizsgálatok **termékképesség:** Identitásirányítás

Bevezettünk egy új alkalmazásengedélyt Microsoft Graph, amellyel az alkalmazások automatikusan hozhatnak létre és kérnek le hozzáférési felülvizsgálatokat csoporttagságokkal és `AccessReview.ReadWrite.Membership` alkalmazás-hozzárendelésekkel. Ezt az engedélyt használhatja az ütemezett feladatok vagy az automatizálás részeként, anélkül, hogy be kellene jelentkeznie a felhasználói környezetbe.

További információkért tekintse meg az [Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)hozzáférési felülvizsgálatok PowerShell-alkalmazásengedélyekkel való Microsoft Graph példát.

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Az Azure AD-tevékenységnaplók mostantól elérhetők a kormányzati felhőpéldányokhoz a Azure Monitor

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termékképesség:** A jelentéskészítés & figyelése

Izgatottan jelentjük, hogy az Azure AD-tevékenységnaplók már elérhetők a kormányzati felhőpéldányokhoz a Azure Monitor. Mostantól elküldheti az Azure AD-naplókat a tárfiókba vagy egy eseményközpontba az olyan SIEM-eszközökkel való integrációhoz, mint a [Sumologic,](../reports-monitoring/howto-integrate-activity-logs-with-sumologic.md)a [Splunk](../reports-monitoring/howto-integrate-activity-logs-with-splunk.md)és az [ArcSight.](../reports-monitoring/howto-integrate-activity-logs-with-arcsight.md)

A szolgáltatás beállításával kapcsolatos további Azure Monitor az [Azure AD-tevékenységnaplók a Azure Monitor.](../reports-monitoring/concept-activity-logs-azure-monitor.md#cost-considerations)

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>A felhasználók frissítése az új, továbbfejlesztett biztonsági információkra

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:**  A hitelesítések (bejelentkezések) **termékképessége:** Felhasználói hitelesítés

2019. szeptember 25-én kikapcsoljuk a régi, nem továbbfejlesztett biztonsági információkat a felhasználói biztonsági adatok regisztrálásával és kezelésével, valamint az [új,](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)továbbfejlesztett verzió bekapcsolásával. Ez azt jelenti, hogy a felhasználók többé nem fogják tudni használni a régi élményt.

A továbbfejlesztett biztonsági információkkal kapcsolatos további [](../authentication/concept-registration-mfa-sspr-combined.md) információkért tekintse meg a rendszergazdai dokumentációt és a felhasználói [dokumentációt.](../user-help/security-info-setup-signin.md)

#### <a name="to-turn-on-this-new-experience-you-must"></a>Az új felhasználói élmény bekapcsolás érdekében a következőt kell:

1. Jelentkezzen be a Azure Portal globális rendszergazdaként vagy felhasználói rendszergazdaként.

2. A Felhasználói Azure Active Directory > panel előzetes > beállításainak kezelése **lehetőséget.**

3. A Users can use preview features for registering **and managing security info - enhanced** (A  felhasználók a biztonsági adatok regisztrálása és kezelése – bővített területen) területen válassza a Selected (Kiválasztva) lehetőséget, majd válasszon ki egy felhasználói csoportot, vagy válassza az All (Mind) lehetőséget, hogy a bérlő összes felhasználója számára bekapcsolja ezt a funkciót. 

4. A **A felhasználók előzetes verziójú funkciókat használhatnak a biztonsági **információ** regisztráláshoz és kezeléséhez területen válassza a **Nincs lehetőséget.**

5. Mentse a beállításokat.

    A beállítások mentése után már nem lesz hozzáférése a régi biztonsági adatokhoz.

>[!Important]
>Ha 2019. szeptember 25. előtt nem tudja végrehajtani ezeket a lépéseket, Azure Active Directory bérlője automatikusan engedélyezve lesz a továbbfejlesztett élmény érdekében. Ha kérdése van, lépjen kapcsolatba velünk a következő elérhetőségen: registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>A POST-bejelentkezéseket használó hitelesítési kérelmeket a rendszer szigorúan ellenőrzi

**Írja be a következőt:** Módosított szolgáltatás **szolgáltatáskategória:** A hitelesítések (bejelentkezések) **termékképessége:** Szabványok

2019. szeptember 2-től kezdődően a POST metódust használó hitelesítési kérelmek szigorúan a HTTP-szabványoknak megfelelően lesznek ellenőrizve. Pontosabban a szóközök és a dupla idézőjelek (") a továbbiakban nem lesznek eltávolítva a kéreleműrlap értékeiből. Ezek a módosítások várhatóan nem szakítják meg a meglévő ügyfeleket, és segítenek abban, hogy az Azure AD-nek küldött kérések minden alkalommal megbízhatóan kezelhetők.

További információkért tekintse meg az Azure AD breaking changes notices (Az Azure AD feltörést [nem hozó változások értesítései) adatokat.](../develop/reference-breaking-changes.md#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)

---

## <a name="july-2019"></a>2019. július

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>A módosítás megterve: alkalmazásproxy szolgáltatásfrissítés csak a TLS 1.2-t támogatja

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Alkalmazásproxy **termékképessége:** Access Control

A legerősebb titkosítás érdekében elkezdjük korlátozni a alkalmazásproxy hozzáférését a TLS 1.2 protokollokra. Ez a korlátozás kezdetben csak a TLS 1.2 protokollt használó ügyfelek számára lesz elérhető, így nem fogja látni a hatást. A TLS 1.0 és a TLS 1.1 protokoll teljes elalasztása 2019. augusztus 31-én befejeződik. A TLS 1.0-t és a TLS 1.1-et használó ügyfelek speciális értesítést kapnak a változásra való felkészülésről.

Annak érdekében, hogy a változás során fenntartsa a kapcsolatot az alkalmazásproxy szolgáltatással, azt javasoljuk, hogy győződjön meg arról, hogy az ügyfél-kiszolgáló és a böngésző-kiszolgáló kombinációi a TLS 1.2 használatára frissültek. Javasoljuk továbbá, hogy ügyeljen arra, hogy az alkalmazottak által az alkalmazásszolgáltatáson keresztül közzétett alkalmazásokhoz való hozzáféréshez használt alkalmazásproxy is.

További információkért lásd: Helyszíni alkalmazás hozzáadása táveléréshez távoli hozzáféréshez alkalmazásproxy [a Azure Active Directory.](../manage-apps/application-proxy-add-on-premises-application.md)

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>A változás megtervezése: Tervfrissítések íme az alkalmazásgyűjteményhez

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Nagyvállalati alkalmazások **termékképessége:** SSO

Az Új felhasználói felület módosításai az Add **(Hozzáadás)** felülethez közelednek az Add (Hozzáadás) terület katalógusából az **Add an application (Alkalmazás hozzáadása) panelen.** Ezek a módosítások segítenek könnyebben megtalálni az automatikus kiépítést, a OpenID Connect, Security Assertion Markup Language (SAML) és a jelszavas egyszeri bejelentkezést (SSO) támogató alkalmazásokat.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Terv a módosításra: Az MFA-kiszolgáló IP-címének eltávolítása az Office 365 IP-címéről

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** MFA **termékképesség:** Identity Security & Protection

Eltávolítjuk az MFA-kiszolgáló IP-címét az [Office 365 IP-cím- és URL-webszolgáltatásból.](/office365/enterprise/office-365-ip-web-service) Ha jelenleg ezekre [Multi-Factor Authentication-kiszolgáló](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements) az oldalakra támaszkodik a tűzfal beállításainak frissítéséhez, meg kell győződnie arról, hogy az Azure Multi-Factor Authentication-kiszolgáló-tűzfalkövetelmények című cikk Azure Multi-Factor Authentication-kiszolgáló-tűzfalkövetelmények szakaszában dokumentált IP-címek listáját is tartalmazza. 

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>A csak alkalmazásra vonatkozó jogkivonatok esetében az ügyfélalkalmazásnak már léteznie kell az erőforrás-bérlőben

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** A hitelesítések (bejelentkezések) **termékképessége:** Felhasználóhitelesítés

2019. július 26-án módosítottuk, hogyan biztosítunk csak alkalmazásra vonatkozó jogkivonatokat az ügyfél hitelesítő [adatain keresztül.](../azuread-dev/v1-oauth2-client-creds-grant-flow.md) Korábban az alkalmazások olyan jogkivonatokat is be lehetett szerezni, amelyek más alkalmazásokat hívhatnak meg, függetlenül attól, hogy az ügyfélalkalmazás a bérlőben volt-e. Frissítettük ezt a viselkedést, így az egybérlős erőforrásokat( más néven webes API-kat) csak az erőforrás-bérlőben található ügyfélalkalmazások hívhatja meg.

Ha az alkalmazás nem az erőforrás-bérlőben található, a következő hibaüzenet jelenik meg: A probléma megoldásához létre kell hoznia az ügyfélalkalmazás egyszerű szolgáltatását a bérlőben a rendszergazdai hozzájárulási végpont vagy a PowerShell használatával, amely biztosítja, hogy a bérlő engedélyt adott az alkalmazásnak a bérlőn belüli `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` működésre. [](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint) [](../develop/howto-authenticate-service-principal-powershell.md)

További információ: [Újdonságok a hitelesítéshez.](../develop/reference-breaking-changes.md#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)

> [!NOTE]
> Az ügyfél és az API közötti meglévő jóváhagyásra továbbra sem lesz szükség. Az alkalmazásoknak továbbra is saját engedélyezési ellenőrzéseket kell végezniük.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Új jelszó nélküli bejelentkezés az Azure AD-be FIDO2 biztonsági kulcsokkal

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Hitelesítések (bejelentkezések) **termékképesség:** Felhasználói hitelesítés

Az Azure AD-ügyfelek mostantól szabályzatokat állíthatnak be a FIDO2 biztonsági kulcsának kezeléséhez a szervezet felhasználói és csoportjai számára. A végfelhasználók saját maga is regisztrálják a biztonsági kulcsukat, a kulcsokkal bejelentkeznek a Microsoft-fiókjukba a webhelyeken FIDO-kompatibilis eszközökön, valamint bejelentkeznek az Azure AD-hez Windows 10 eszközökre.

További információ: Enable [passwordless sign in for Azure AD (preview)](../authentication/concept-authentication-passwordless.md) for administrator related information (Jelszó nélküli bejelentkezés engedélyezése az Azure AD-be (előzetes verzió) rendszergazdai információkért és Biztonsági adatok beállítása biztonsági kulcs [(előzetes verzió)](../user-help/security-info-setup-security-key.md) használatára a végfelhasználóval kapcsolatos információkhoz.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Új összevont alkalmazások a Azure AD alkalmazás – 2019. július

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Enterprise Apps **Termékképesség:** külső féltől származó integráció

2019 júliusában hozzáadtunk ezt a 18 új alkalmazást összevonási támogatással az alkalmazásgyűjteményhez:

[Ungerboeck Software](../saas-apps/ungerboeck-software-tutorial.md), Bright [Pattern Omnichannel Contact Center](../saas-apps/bright-pattern-omnichannel-contact-center-tutorial.md), Clever [Necial](../saas-apps/clever-nelly-tutorial.md), [AcquireIO](../saas-apps/acquireio-tutorial.md), [Toop](https://www.looop.co/schedule-a-demo/), [productboard](../saas-apps/productboard-tutorial.md), [MS Azure SSO Access for Ethidex Compliance Office &trade; ](../saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial.md), [Hype](../saas-apps/hype-tutorial.md), [Abstract](../saas-apps/abstract-tutorial.md), [Ascentis](../saas-apps/ascentis-tutorial.md), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Patterna](../saas-apps/wandera-tutorial.md), [TwineSocial](https://twinesocial.com/), [Kallidus](../saas-apps/kallidus-tutorial.md), [HyperAnna](../saas-apps/hyperanna-tutorial.md), [PaluID WasteWitness](https://pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](../saas-apps/jfrog-artifactory-tutorial.md)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe építésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Enterprise Apps **Termékképesség:** A jelentéskészítés & monitorozása

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Összevont címtár](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

A szervezet automatizált felhasználói fiókok automatikus kiépítése által történő biztonságossá tevésével kapcsolatos további információkért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val](../app-provisioning/user-provisioning.md)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Új Azure AD Domain Services szolgáltatáscímke a hálózati biztonsági csoporthoz

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Azure AD Domain Services **termékképesség:** Azure AD Domain Services

Ha unja már az IP-címek és tartományok hosszú listáinak felügyeletét, használhatja az **Új AzureActiveDirectoryDomainServices** hálózati szolgáltatáscímkét az Azure-beli hálózati biztonsági csoportban az Azure AD Domain Services virtuális hálózat alhálózatára irányuló bejövő forgalom biztonságossá Azure AD Domain Services érdekében.

További információ erről az új szolgáltatáscímkéről: Hálózati biztonsági [csoportok a Azure AD Domain Services.](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Új biztonsági naplózási Azure AD Domain Services (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Azure AD Domain Services **termékképesség:** Azure AD Domain Services

Örömmel jelentjük be, hogy az Azure AD tartományi szolgáltatás biztonsági naplózása nyilvános előzetes verzióban jelent meg. A biztonsági naplózás az Azure AD tartományi szolgáltatás portáljának használatával kritikus betekintést nyújt a hitelesítési szolgáltatásokba azáltal, hogy streameli a biztonsági naplózási eseményeket a megcélzott erőforrásokhoz, például az Azure Storage-hoz, az Azure Log Analytics-munkaterülethez és az Azure Event Hubhoz.

További információ: [Enable Security Audits for Azure AD Domain Services (Preview) (Biztonsági](../../active-directory-domain-services/security-audit-events.md)naplózás engedélyezése Azure AD Domain Services (előzetes verzió) ).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Új hitelesítési módszerek használata & elemzésekhez (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Az önkiszolgáló jelszó-visszaállítási **termék képessége:** A & monitorozása

Az új hitelesítési módszerek használati & Insights-jelentései segítségével megértheti, hogyan regisztrálják és használják a szervezetben az olyan funkciókat, mint az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás, beleértve az egyes funkciók regisztrált felhasználóinak számát, azt, hogy milyen gyakran használják az új jelszó önkiszolgáló alaphelyzetbe állítását a jelszavak alaphelyzetbe állításához, és milyen módon történik az alaphelyzetbe állítás.

További információ: Hitelesítési módszerek használata és [& (előzetes verzió)](../authentication/howto-authentication-methods-activity.md).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Új biztonsági jelentések érhetők el az összes Azure AD-rendszergazda számára (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Identity Protection **termékképesség:** Identity Security & Protection

Mostantól minden Azure  AD-rendszergazda kiválaszthatja a meglévő biztonsági jelentések tetején található szalagcímet , például a Kockázatosként  megjelölt felhasználók jelentést, hogy elkezdje használni az új biztonsági élményt, ahogy az a Kockázatos felhasználók és a **Kockázatos** bejelentkezési jelentésekben is látható. Idővel az összes biztonsági jelentés át fog lépni a régebbi verziókról az új verziókra, az új jelentések pedig a következő funkciókat biztosítják:

- Speciális szűrés és rendezés

- Tömeges műveletek, például a felhasználói kockázatok elvetését

- Sérült vagy biztonságos entitások megerősítése

- Kockázati állapot, amely a következő kockázatokat fedi le: Veszélyeztetett, Elvetve, Szervizelt és Megerősített biztonságú

További információ: [Kockázatos](../identity-protection/howto-identity-protection-investigate-risk.md#risky-users) felhasználók jelentése és [Kockázatos bejelentkezések jelentés.](../identity-protection/howto-identity-protection-investigate-risk.md#risky-sign-ins)

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Új biztonsági naplózási Azure AD Domain Services (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Azure AD Domain Services **termékképesség:** Azure AD Domain Services

Örömmel jelentjük be, hogy az Azure AD tartományi szolgáltatások biztonsági naplózása nyilvános előzetes verzióban jelent meg. A biztonsági naplózás kritikus betekintést nyújt a hitelesítési szolgáltatásokba azáltal, hogy streameli a biztonsági naplózási eseményeket a megcélzott erőforrásokhoz, például az Azure Storage-hoz, az Azure Log Analytics-munkaterülethez és az Azure Event Hubhoz az Azure AD tartományi szolgáltatás portálján.

További információ: [Enable Security Audits for Azure AD Domain Services (Preview)](../../active-directory-domain-services/security-audit-events.md)(Biztonsági naplózás engedélyezése Azure AD Domain Services (előzetes verzió) ).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Új közvetlen B2B-összevonás SAML/WS-Fed használatával (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2B **termékképesség:** B2B/B2C

A közvetlen összevonással könnyebben dolgozhat olyan partnerekkel, akiknek az IT által felügyelt identitásmegoldása nem Azure AD, mert az SAML- vagy WS-Fed identitásrendszerekkel dolgozik. Miután közvetlen összevonási kapcsolatot hoz létre egy partnerrel, az ebből a tartományból meghívott új vendégfelhasználók együttműködhetnek a meglévő szervezeti fiókjukkal, így zökkenőmentesebb lesz a vendégfelhasználói élmény.

További információ: Közvetlen összevonás [vendégfelhasználók AD FS külső szolgáltatókkal (előzetes verzió)](../external-identities/direct-federation.md).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** Figyelés & jelentéskészítéshez

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Dialpad](../saas-apps/dialpad-provisioning-tutorial.md)

- [Összevont címtár](../saas-apps/federated-directory-provisioning-tutorial.md)

- [Figma](../saas-apps/figma-provisioning-tutorial.md)

- [Leapsome](../saas-apps/leapsome-provisioning-tutorial.md)

- [Peakon](../saas-apps/peakon-provisioning-tutorial.md)

- [Smartsheet](../saas-apps/smartsheet-provisioning-tutorial.md)

A szervezet automatizált felhasználói fiókok automatikus kiépítése által történő biztonságossá teről további információért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val.](../app-provisioning/user-provisioning.md)

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Új csoportnevek ellenőrzése az Azure AD-portálon

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Csoportkezelési **termékképesség:** Együttműködés

Most, amikor létrehoz vagy frissít egy csoportnevet az Azure AD-portálon, egy ellenőrzéssel ellenőrizzük, hogy az erőforrásban meg van-e példányosítva egy meglévő csoportnév. Ha azt határozzák meg, hogy a nevet már egy másik csoport használja, a rendszer kérni fogja, hogy módosítsa a nevét.

További információ: [Csoportok kezelése az Azure AD portálon.](./active-directory-groups-create-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Az Azure AD mostantól támogatja a statikus lekérdezési paramétereket a válasz (átirányítás) URI-kban

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Hitelesítések (bejelentkezések) **termékképesség:** Felhasználói hitelesítés

Az Azure AD-alkalmazások mostantól statikus lekérdezési paraméterekkel (például ) is regisztrálnak és használhatnak válasz(átirányítási) URI-ket az `https://contoso.com/oauth2?idp=microsoft` OAuth 2.0-kérések esetében. A statikus lekérdezési paraméterre a válasz URI-k sztringegyeztetése vonatkozik, csakúgy, mint a válasz URI bármely más része. Ha nincs az URL-dekódolt redirect-uri-nak megfelelő regisztrált sztring, a rendszer elutasítja a kérelmet. Ha a válasz URI megtalálható, a rendszer a teljes sztringet használja a felhasználó átirányítására, beleértve a statikus lekérdezési paramétert is.

A dinamikus válasz URI-k továbbra is tiltottak, mert biztonsági kockázatot jelentenek, és nem használhatók az állapotinformációk megőrzésére a hitelesítési kérelmekben. Erre a célra használja a `state` paramétert.

Az alkalmazásregisztrációs képernyők jelenleg Azure Portal letiltják a lekérdezési paramétereket. Az alkalmazásjegyzék manuális szerkesztésével azonban lekérdezési paramétereket adhat hozzá és tesztelhet az alkalmazásban. További információ: [Újdonságok a hitelesítéshez.](../develop/reference-breaking-changes.md#redirect-uris-can-now-contain-query-string-parameters)

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Az Azure AD tevékenységnaplói (MS Graph API-k) mostantól PowerShell-parancsmagokkal érhetők el

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termék képesség:** Figyelés & jelentéskészítés

Izgatottan jelentjük, hogy az Azure AD-tevékenységnaplók (audit- és bejelentkezési jelentések) már elérhetők az Azure AD PowerShell-modulon keresztül. Korábban MS Graph API-végpontok használatával létrehozhatta a saját szkripteket, és ezt a képességet kiterjesztettük a PowerShell-parancsmagokra.

A parancsmagok használatával kapcsolatos további információkért lásd: [Azure AD PowerShell-parancsmagok jelentéskészítéshez.](../reports-monitoring/reference-powershell-reporting.md)

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Az Audit- és bejelentkezési naplók szűrővezérlői frissítve az Azure AD-ban

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termék képesség:** Figyelés & jelentéskészítés

Frissítettük a naplózási és bejelentkezési naplójelentéseket, így mostantól különböző szűrőket alkalmazhat anélkül, hogy oszlopként felvené azokat a jelentés képernyőjén. Emellett azt is eldöntheti, hogy hány szűrőt szeretne látni a képernyőn. Ezek a frissítések együttesen megkönnyítik a jelentések olvasását és az igényeinek megfelelő hatókört.

További információ ezekről a frissítésekről: [Auditnaplók szűrése](../reports-monitoring/concept-audit-logs.md#filtering-audit-logs) és [Bejelentkezési tevékenységek szűrése.](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)

---

## <a name="june-2019"></a>2019. június

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Új riskDetections API Microsoft Graph (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Identity Protection **termékképesség:** Identity Security & Protection

Örömmel jelentjük be, hogy a Microsoft Graph új riskDetections API nyilvános előzetes verzióban érhető el. Ezzel az új API-val megtekintheti a szervezet Identity Protectionrel kapcsolatos felhasználói és bejelentkezési kockázatészlelési listáját. Ezzel az API-val hatékonyabban is lekérdezheti a kockázatészlelési adatokat, többek között az észlelés típusát, állapotát, szintjét és egyéb adatait.

További információt a [Kockázatészlelés API referenciadokumentációjában talál.](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2019. június

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2019 júniusában hozzáadtunk ezt a 22 új, összevonási támogatást támogató alkalmazást az alkalmazásgyűjteményhez:

[Azure AD SAML Toolkit](../saas-apps/saml-toolkit-tutorial.md), [Otsuka Pandakai (塚商会)](../saas-apps/otsuka-shokai-tutorial.md), [ANAQUA](../saas-apps/anaqua-tutorial.md), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](../saas-apps/expensein-tutorial.md) [,](../saas-apps/helper-helper-tutorial.md)Segítő segítő , [Costpoint,](../saas-apps/costpoint-tutorial.md) [GlobalOne](../saas-apps/globalone-tutorial.md), [In-CarIn-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-tutorial.md), [CyberArk SAML Authentication](../saas-apps/cyberark-saml-authentication-tutorial.md), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](../saas-apps/pandadoc-tutorial.md), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/), [Vtiger CRM (SAML)](../saas-apps/vtiger-crm-saml-tutorial.md), Oracle Access Manager for Oracle Retail Merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edus

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása a Azure Active Directory [alkalmazásgyűjteményben.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** Figyelés & jelentéskészítéshez

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Zoom](../saas-apps/zoom-provisioning-tutorial.md)

- [Envoy](../saas-apps/envoy-provisioning-tutorial.md)

- [Proxyclick](../saas-apps/proxyclick-provisioning-tutorial.md)

- [4me](../saas-apps/4me-provisioning-tutorial.md)

A szervezet automatizált felhasználói fiókok építéssel történő biztonságosabb felhasználásával kapcsolatos további információkért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val](../app-provisioning/user-provisioning.md)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Az Azure AD kiépítési szolgáltatás valós idejű folyamatának megtekintése

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** App Provisioning **Product képesség:** Identitás-életciklus kezelése

Frissítettük az Azure AD kiépítési felületét, hogy tartalmazni tudja az új folyamatjelzőt, amely bemutatja, milyen messze van a felhasználóátépítési folyamatban. Ez a frissített funkció az aktuális ciklusban kiépített felhasználók számról, valamint az eddig kiépített felhasználók számról is tájékoztatást nyújt.

További információ: [A felhasználókiépítés állapotának ellenőrzése.](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>A vállalati arculat jelenik meg a kijelentkezés és a hiba képernyőjén

**Írja be a következőt:** Módosított szolgáltatás **szolgáltatáskategória:** Hitelesítések (bejelentkezések) **termékképesség:** Felhasználói hitelesítés

Frissítettük az Azure AD-t, hogy a vállalati arculat most megjelenik a kijelentkezés és a hibaképernyőn, valamint a bejelentkezési oldalon. A funkció bekapcsolásért semmit sem kell tenni, az Azure AD egyszerűen a vállalati  arculat területén már beállított eszközöket Azure Portal.

A vállalati arculat beállításával kapcsolatos további információkért lásd: Védjegyezés hozzáadása a szervezet saját Azure Active Directory [oldalhoz.](./customize-branding.md)

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Az Azure Multi-Factor Authentication- (MFA-) kiszolgáló már nem érhető el az új üzemelő példányok számára

**Írja be a következőt:** Elavult **szolgáltatáskategória:** MFA **termékképesség:** Identity Security & Protection

2019. július 1-től a Microsoft már nem nyújt MFA-kiszolgálót az új telepítésekhez. A többtényezős hitelesítést a szervezetben megkövetelni kívánó új ügyfeleknek mostantól a felhőalapú Azure AD Multi-Factor Authenticationt kell használniuk. Azok az ügyfelek, akik július 1. előtt aktiválták az MFA-kiszolgálót, nem látnak változást. Továbbra is letöltheti a legújabb verziót, letöltheti a jövőbeli frissítéseket, és aktiváló hitelesítő adatokat hozhat létre.

További információ: [Ismerkedés](../authentication/howto-mfaserver-deploy.md)az Azure Multi-Factor Authentication-kiszolgáló. További információ a felhőalapú Azure AD Multi-Factor [Authenticationről: Planning a cloud-based Azure AD Multi-Factor Authentication deployment (Felhőalapú Azure AD Multi-Factor Authentication üzembe helyezésének megtervezése).](../authentication/howto-mfa-getstarted.md)

---

## <a name="may-2019"></a>2019. május

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Szolgáltatásváltozás: A jövőben csak a TLS 1.2 protokollok támogatása lesz alkalmazásproxy szolgáltatásban

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Alkalmazásproxy **termékképessége:** Access Control

Annak érdekében, hogy az ügyfelek számára a legjobb titkosítást biztosítsuk, csak a TLS 1.2 protokollokra korlátozunk a alkalmazásproxy szolgáltatásban. Ezt a változást fokozatosan vezetjük be az olyan ügyfelek számára, akik már csak TLS 1.2 protokollokat használnak, így nem láthat módosításokat.

A TLS 1.0 és a TLS 1.1 elalasztása 2019. augusztus 31-én történik, de további speciális értesítést is biztosítanak, így lesz ideje felkészülni a változásra. A változásra való felkészüléshez győződjön meg arról, hogy az ügyfél-kiszolgáló és a böngészőkiszolgáló kombinációi, beleértve a felhasználók által az alkalmazásproxy-ban közzétett alkalmazások elérésére használt ügyfeleket is, frissítve vannak a TLS 1.2 protokoll használatára a alkalmazásproxy szolgáltatással való kapcsolat fenntartásához. További információ: Helyszíni alkalmazás hozzáadása táveléréshez távoli elérésre alkalmazásproxy [a Azure Active Directory.](../manage-apps/application-proxy-add-on-premises-application.md#prerequisites)

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>A használati és elemzési jelentés használata az alkalmazással kapcsolatos bejelentkezési adatok megtekintéséhez

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** Figyelés & jelentéskészítéshez

Mostantól a Azure Portal Vállalati alkalmazások területén található  használati és elemzési jelentéssel alkalmazásközpontú nézetet kaphat a bejelentkezési adatokról, többek között a következőkről:

- A szervezet által használt legnépszerűbb alkalmazások

- A legtöbb sikertelen bejelentkezéssel bejelentkező alkalmazások

- Az egyes alkalmazások legfontosabb bejelentkezési hibái

További információ erről a funkcióról: Használati és elemzési jelentés a [Azure Active Directory portálon](../reports-monitoring/concept-usage-insights-report.md)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Felhasználóátállítás automatizálása felhőalkalmazásokban az Azure AD használatával

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** Figyelés & jelentéskészítéshez

Kövesse ezeket az új oktatóanyagokat az Azure AD provisioning Service használatával a következő felhőalapú alkalmazások felhasználói fiókjai létrehozásának, törlésének és frissítésének automatizálására:

- [Comeet](../saas-apps/comeet-recruiting-software-provisioning-tutorial.md)

- [DynamicSignal (Dinamikus aláírás)](../saas-apps/dynamic-signal-provisioning-tutorial.md)

- [KeeperSecurity](../saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial.md)

Kövesse ezt az [](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)új Dropbox-oktatóanyagot is, amely a csoportobjektumok építési útmutatóját is bemutatja.

A szervezet automatizált felhasználóifiók-létesítésen keresztüli biztonságossá történő szervezésével kapcsolatos további információkért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val.](../app-provisioning/user-provisioning.md)

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Az identitások biztonsági pontszáma mostantól elérhető az Azure AD-ban (általánosan elérhető)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** N/A **termékképesség:** Identity Security & Protection

Mostantól az Azure AD identitásbiztonsági pontszám funkcióját használva figyelheti és javíthatja az identitásbiztonsági állapotokat. Az identitás-biztonsági pontszám funkció egyetlen irányítópultot használ a következő célokra:

- Az identitásbiztonsági rendszer objektív mérése egy 1 és 223 közötti pontszám alapján.

- Az identitásbiztonsági fejlesztések megterve

- A biztonsági fejlesztések sikerességének áttekintése

Az identitásbiztonsági pontszám funkcióval kapcsolatos további információkért lásd: Mi az identitásbiztonsági pontszám a [Azure Active Directory.](./identity-secure-score.md)

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Új Alkalmazásregisztrációk elérhetővé válik (általánosan elérhető)

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Hitelesítések (bejelentkezések) **termékképesség:** Fejlesztői élmény

Az új [Alkalmazásregisztrációk](https://aka.ms/appregistrations) mostantól általánosan elérhető. Ez az új felhasználói élmény tartalmazza az alkalmazásregisztrációs portálon és az alkalmazásregisztrációs portálon Azure Portal legfontosabb funkciókat, és a következőn keresztül fejleszti tovább őket:

- **Jobb alkalmazáskezelés.** Ahelyett, hogy az alkalmazásokat különböző portálok között nézi meg, mostantól egyetlen helyen láthatja az összes alkalmazást.

- **Egyszerűsített alkalmazásregisztráció.** A továbbfejlesztett navigációs élménytől az átalakított engedélyválasztási folyamaton át mostantól egyszerűbb az alkalmazások regisztrálása és kezelése.

- **Részletesebb információk.** További részleteket is megtudhat az alkalmazásról, többek között rövid útmutatókat is talál.

További információ: [A Microsoft identitásplatformja](../develop/index.yml) [és Alkalmazásregisztrációk általánosan elérhető szolgáltatás!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blogbemondás.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Az Identity Protection Risky Users API-ján elérhető új képességek

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Identity Protection **termékképesség:** Identity Security & Protection

Örömmel jelenthetjük be, hogy mostantól a Kockázatos felhasználók API-val lekérheti a felhasználók kockázati előzményeit, elvetheti a kockázatos felhasználókat, és megerősítheti, hogy a felhasználók sérültek. Ezzel a módosítással hatékonyabban frissítheti a felhasználók kockázati állapotát, és megértheti azok kockázati előzményeit.

További információt a [Risky Users API referenciadokumentációjában talál.](/graph/api/resources/riskyuser?view=graph-rest-beta&preserve-view=true)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2019. május

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2019 májusában hozzáadtunk ezt a 21 új, összevonási támogatást támogató alkalmazást az alkalmazásgyűjteményhez:

[Freedcamp,](../saas-apps/freedcamp-tutorial.md) [Real Links,](../saas-apps/real-links-tutorial.md) [Kiandr,](https://app.kianda.com/sso/OpenID/AzureAD/)Simple [Sign,](../saas-apps/simple-sign-tutorial.md) [Braze,](../saas-apps/braze-tutorial.md) [Displayr,](../saas-apps/displayr-tutorial.md) [Templafy,](../saas-apps/templafy-tutorial.md) [Marketo Sales Engage](https://toutapp.com/login), [ACLP,](../saas-apps/aclp-tutorial.md) [OutSystems,](../saas-apps/outsystems-tutorial.md) [Meta4 Global HR,](../saas-apps/meta4-global-hr-tutorial.md) [Quantum Workplace](../saas-apps/quantum-workplace-tutorial.md), [Cobalt](../saas-apps/cobalt-tutorial.md), [webMethods API Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](../saas-apps/whatfix-tutorial.md), [Control](../saas-apps/control-tutorial.md), [JOBHUB,](../saas-apps/jobhub-tutorial.md) [NEOGOV,](../saas-apps/neogov-tutorial.md) [Foodee](../saas-apps/foodee-tutorial.md), [MyVR](../saas-apps/myvr-tutorial.md)

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az alkalmazás [Azure Active Directory katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Továbbfejlesztett csoportok létrehozása és kezelése az Azure AD portálon

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Csoportkezelési **termékképesség:** Együttműködés

Továbbfejlesztjük a csoportokkal kapcsolatos felhasználói élményt az Azure AD portálon. Ezek a fejlesztések lehetővé teszik a rendszergazdák számára a csoportlisták és tagok listái jobb kezelését, valamint további létrehozási lehetőségeket biztosítanak.

A fejlesztések közé tartoznak a következők:

- Alapszintű szűrés tagságtípus és csoporttípus szerint.

- Új oszlopok, például a Forrás és az E-mail-cím hozzáadása.

- Lehetőség több csoport, tag és tulajdonoslista kiválasztására az egyszerű törlés érdekében.

- Lehetőség az e-mail-cím választásra és a tulajdonosok hozzáadására a csoport létrehozása során.

További információ: [Alapszintű](./active-directory-groups-create-azure-portal.md)csoport létrehozása és tagok hozzáadása a Azure Active Directory.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Elnevezési szabályzat konfigurálása Office 365-csoportokhoz az Azure AD portálon (általánosan elérhető)

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Csoportkezelési **termékképesség:** Együttműködés

A rendszergazdák mostantól az Azure AD portál használatával konfigurálhatják az Office 365-csoportok elnevezési szabályzatát. Ezzel a módosítással konzisztens elnevezési konvenciókat érvényesíthet a szervezet felhasználói által létrehozott vagy szerkesztett Office 365-csoportok esetében.

Az Office 365-csoportok elnevezési szabályzatát kétféleképpen konfigurálhatja:

- Definiálja a csoportnévhez automatikusan hozzáadott előtagokat vagy utótagokat.

- Feltölthet egy testreszabott, letiltott szavakat a szervezet számára, amelyek nem engedélyezettek a csoportnevekben (például "VEZÉRIGAZGATÓ, Bérszámfejtési, HR").

További információ: [Elnevezési szabályzat kényszerítása Office 365-csoportokhoz.](../enterprise-users/groups-naming-policy.md)

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API-végpontok már elérhetők az Azure AD-tevékenységnaplókhoz (általánosan elérhető)

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termék képesség:** Figyelés & jelentéskészítés

Örömmel jelentjük be, hogy általánosan elérhető a Microsoft Graph API-végpontok támogatása az Azure AD-tevékenységnaplókhoz. Ebben a kiadásban mostantól az Azure AD auditnaplók 1.0-s verzióját, valamint a bejelentkezési naplók API-ját is használhatja.

További információ: [Az Azure AD auditnapló API áttekintése.](/graph/api/resources/azure-ad-auditlog-overview)

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>A rendszergazdák mostantól feltételes hozzáférést használhatnak a kombinált regisztrációs folyamathoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Feltételes hozzáférési **termékképesség:** Identity Security & Protection

A rendszergazdák mostantól feltételes hozzáférési szabályzatokat hozhatnak létre a kombinált regisztrációs oldalon való használatra. Ez magában foglalja a szabályzatok alkalmazásával a regisztrációt, ha:

- A felhasználók megbízható hálózaton vannak.

- A felhasználók alacsony bejelentkezési kockázatot jelent.

- A felhasználók felügyelt eszközön vannak.

- A felhasználók beleegyeznek a szervezet használati feltételeibe.

A feltételes hozzáféréssel és a jelszó-visszaállítással kapcsolatos további információkért tekintse meg a feltételes hozzáférést az Azure AD kombinált MFA- és jelszó-visszaállítási regisztrációs folyamatához [blogbejegyzésben.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348) A kombinált regisztrációs folyamat feltételes hozzáférési szabályzatával kapcsolatos további információkért lásd: Feltételes hozzáférési [szabályzatok kombinált regisztrációhoz.](../authentication/howto-registration-mfa-sspr-combined.md#conditional-access-policies-for-combined-registration) Az Azure AD használati feltételek funkcióval kapcsolatos további információkért lásd a Azure Active Directory [szolgáltatási](../conditional-access/terms-of-use.md)feltételeket.

---

## <a name="april-2019"></a>2019. április

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Az Azure AD Intelligens veszélyforrás-felderítés új észlelése már elérhető a Azure AD Identity Protection

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Azure AD Identity Protection **termékképesség:** Identity Security & Protection

Azure AD Intelligens veszélyforrás-felderítés észlelés mostantól elérhető a frissített Azure AD Identity Protection részeként. Ez az új funkció segít egy adott felhasználó vagy tevékenység szokatlan felhasználói tevékenységének jelzésében, amely a Microsoft belső és külső fenyegetés-felderítési forrásain alapuló ismert támadási mintákkal konzisztens.

További információ a Azure AD Identity Protection frissített verziójáról: [Négy](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) fő Azure AD Identity Protection-fejlesztés érhető el a nyilvános előzetes verzió blogján és a [What is Azure Active Directory Identity Protection (refreshed)?](../identity-protection/overview-identity-protection.md) cikket. A kockázatészlelésről Azure AD Intelligens veszélyforrás-felderítés lásd a Azure Active Directory Identity Protection [kockázatészlelési cikkét.](../identity-protection/concept-identity-protection-risks.md)

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Elérhető az Azure AD-jogosultságkezelés (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Identitásirányítási termékképesség:** Identity Governance

A nyilvános előzetes verzióban elérhető Azure AD-jogosultságkezelés segítségével az ügyfelek delegálhatják a hozzáférési csomagok kezelését, amely meghatározza, hogy az alkalmazottak és az üzleti partnerek hogyan kérhetnek hozzáférést, kinek kell jóváhagynia, és mennyi ideig van hozzáférésük. A hozzáférési csomagok kezelhetik az Azure AD- és Office 365-csoportok tagságát, a vállalati alkalmazások szerepkör-hozzárendeléseit és a SharePoint Online-webhelyek szerepkör-hozzárendeléseit. A jogosultságkezelésről további információt [az Azure AD-jogosultságkezelés áttekintésében talál.](../governance/entitlement-management-overview.md) További információ a különböző funkciókról Azure AD Identity Governance beleértve a Privileged Identity Management, a hozzáférési felülvizsgálatokat és a használati feltételeket: Mi az a [Azure AD Identity Governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Elnevezési szabályzat konfigurálása Office 365-csoportokhoz az Azure AD portálon (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Csoportkezelési **termékképesség:** Együttműködés

A rendszergazdák mostantól az Azure AD portál használatával konfigurálhatják az Office 365-csoportok elnevezési szabályzatát. Ezzel a módosítással konzisztens elnevezési konvenciókat érvényesíthet a szervezet felhasználói által létrehozott vagy szerkesztett Office 365-csoportok esetében.

Az Office 365-csoportok elnevezési szabályzatát kétféleképpen konfigurálhatja:

- Definiálja a csoportnévhez automatikusan hozzáadott előtagokat vagy utótagokat.

- Feltölthet egy testreszabott letiltott szót a szervezet számára, amely nem engedélyezett a csoportnevekben (például "VEZÉRIGAZGATÓ, Bérszámfejtési, HR").

További információ: [Elnevezési szabályzat kényszerítása Office 365-csoportokhoz.](../enterprise-users/groups-naming-policy.md)

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Az Azure AD-tevékenységnaplók mostantól elérhetők Azure Monitor (általánosan elérhető)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termékképesség:** Figyelés & jelentéskészítéshez

Az Azure AD-tevékenységnaplók használatával a vizualizációkra vonatkozó visszajelzések kezelése érdekében bevezetjük a Log Analytics új Insights funkcióját. Ez a funkció a Munkafüzetek nevű interaktív sablonokkal segít az Azure AD-erőforrások elemzésében. Ezek az előre felépített munkafüzetek az alkalmazások vagy felhasználók adatait, valamint a következőket biztosítják:

- **Bejelentkezések.** Az alkalmazások és a felhasználók adatait tartalmazza, beleértve a bejelentkezés helyét, a használatban van az operációs rendszert vagy a böngészőt használó ügyfelet és verziót, valamint a sikeres vagy sikertelen bejelentkezések számát.

- **Régi hitelesítés és feltételes hozzáférés.** Részleteket nyújt az örökölt hitelesítést használó alkalmazásokról és felhasználókról, beleértve a feltételes hozzáférési szabályzatok által aktivált többtényezős hitelesítés használatát, a feltételes hozzáférési szabályzatokat használó alkalmazásokat stb.

- **Bejelentkezési hibák elemzése.** Segít megállapítani, hogy a bejelentkezési hibák felhasználói művelet, szabályzathibák vagy az infrastruktúra miatt fordulnak-e elő.

- **Egyéni jelentések.** Létrehozhat új munkafüzeteket, vagy szerkesztheti a meglévő munkafüzeteket, így testre szabhatja az Elemzések funkciót a szervezet számára.

További információ: [Azure Monitor munkafüzetek használata Azure Active Directory jelentésekhez.](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2019. április

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2019 áprilisában hozzáadtunk ezt a 21 új, összevonási támogatást támogató alkalmazást az alkalmazásgyűjteményhez:

[SAP Fiori](../saas-apps/sap-fiori-tutorial.md), [HRworks single Sign-On](../saas-apps/hrworks-single-sign-on-tutorial.md), [Percolate](../saas-apps/percolate-tutorial.md), [MobiControl](../saas-apps/mobicontrol-tutorial.md), [Citrix NetScaler,](../saas-apps/citrix-netscaler-tutorial.md) [Shibumi](../saas-apps/shibumi-tutorial.md), Adattitkosítás, [KmIQ](https://mileiq.onelink.me/991934284/7e980085), [](../saas-apps/benchling-tutorial.md) [PageDNA](../saas-apps/pagedna-tutorial.md), [EduBrite LMS](../saas-apps/edubrite-lms-tutorial.md), [RStudio Connect](../saas-apps/rstudio-connect-tutorial.md), [AMMS](../saas-apps/amms-tutorial.md), [Mitel Connect](../saas-apps/mitel-connect-tutorial.md), [Alibaba Cloud (szerepköralapú SSO)](../saas-apps/alibaba-cloud-service-role-based-sso-tutorial.md), [Certent Management](../saas-apps/certent-equity-management-tutorial.md), [Sectigo Certificate Manager](../saas-apps/sectigo-certificate-manager-tutorial.md), [GreenOrbit](../saas-apps/greenorbit-tutorial.md), [Workgrid](../saas-apps/workgrid-tutorial.md), [monday.com](../saas-apps/mondaycom-tutorial.md), [SurveyMonkey Enterprise](../saas-apps/surveymonkey-enterprise-tutorial.md), [Indiggo](https://indiggolead.com/)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Új hozzáférési felülvizsgálatok gyakorisága beállítás és több szerepkör kiválasztása

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Hozzáférési felülvizsgálatok **termékképesség:** Identitásirányítás

Az Azure AD hozzáférési felülvizsgálatok új frissítései a következő lehetőségeket biztosítják:

- Módosítsa a hozzáférési felülvizsgálatok gyakoriságát félévesre, a korábban meglévő heti, havi, negyedéves és éves beállítások mellett.

- Több Azure AD- és Azure-erőforrásszerepk szerepkört is kiválaszthat egyetlen hozzáférési felülvizsgálat létrehozásakor. Ebben az esetben minden szerepkör ugyanazokkal a beállításokkal van beállítva, és minden felülvizsgáló egyszerre kap értesítést.

További információ a hozzáférési felülvizsgálatok létrehozásáról: Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure AD hozzáférési [felülvizsgálatok során.](../governance/create-access-review.md)

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-mail-riasztási rendszer(nek) áttűnnek, és új e-mail-feladói információkat küldenek egyes ügyfelek számára

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** AD-szinkronizáló **termékképesség:** Platform

Azure AD Connect folyamatban van az e-mail-riasztási rendszer(nek) váltása, amely potenciálisan új e-mail-küldőt mutat néhány ügyfélnek. Ennek kezeléséhez hozzá kell adni a listára a szervezet engedélyezési listáját, különben nem fog tudni továbbra is fontos riasztásokat kapni az Office 365-től, az Azure-tól vagy a szinkronizálási `azure-noreply@microsoft.com` szolgáltatásoktól.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Az UPN-utótag módosításai mostantól sikeresek az összevont tartományok között a Azure AD Connect

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** AD-szinkronizáló **termékképesség:** Platform

Most már sikeresen módosíthatja egy felhasználó UPN-utótagját egy összevont tartományról egy másik összevont tartományra a Azure AD Connect. Ez a javítás azt jelenti, hogy a szinkronizálási ciklus során többé nem jelenik meg a FederatedDomainChangeError hibaüzenet, vagy a következő értesítési e-mail jelenik meg: "Az objektum nem frissíthető az Azure Active Directory-ban, mert a(z) [FederatedUser.UserPrincipalName] attribútum érvénytelen. Frissítse az értéket a helyi címtárszolgáltatásokban".

További információ: [Hibaelhárítás a szinkronizálás során.](../hybrid/tshoot-connect-sync-errors.md#federateddomainchangeerror)

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Fokozott biztonság az alkalmazásvédelmi feltételes hozzáférési szabályzat használatával az Azure AD-ban (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Feltételes hozzáférési **termékképesség:** Identity Security & Protection

Alkalmazásvédelem-alapú feltételes hozzáférés mostantól elérhető az Alkalmazásvédelmi szabályzat **megkövetelve lehetőség** használatával. Ez az új szabályzat segít a szervezet biztonságának növelésében azáltal, hogy megakadályozza a következő problémákat:

- A felhasználók licenc nélkül férnek hozzá Microsoft Intune alkalmazásokhoz.

- Azok a felhasználók, akik nem Microsoft Intune alkalmazásvédelmi szabályzatot.

- A felhasználók nem konfigurált alkalmazásvédelmi szabályzat Microsoft Intune férnek hozzá az alkalmazásokhoz.

További információ: How to Require app protection policy for cloud app access with Conditional Access (Alkalmazásvédelmi szabályzat megkövetele a felhőalkalmazások feltételes [hozzáféréséhez).](../conditional-access/app-protection-based-conditional-access.md)

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Új támogatás az Azure AD egyszeri bejelentkezéshez és feltételes hozzáféréshez a Microsoft Edge (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Feltételes hozzáférési **termékképesség:** Identity Security & Protection

Továbbfejlesztjük az Azure AD-támogatást a Microsoft Edge, beleértve az Azure AD egyszeri bejelentkezés és a feltételes hozzáférés új támogatását. Ha korábban már használt Microsoft Intune Managed Browser, ehelyett használhatja a Microsoft Edge is.

Az eszközök és alkalmazások feltételes hozzáféréssel történő beállításával [](../conditional-access/require-managed-devices.md) és kezelésével kapcsolatos további információkért lásd: Felügyelt eszközök felhőalkalmazás-hozzáférés megkövetele feltételes hozzáféréssel és Jóváhagyott ügyfélalkalmazások megkövetelve a felhőalkalmazáshoz való feltételes [hozzáféréshez.](../conditional-access/app-based-conditional-access.md) További információ a hozzáférés házirendekkel való kezeléséhez Microsoft Edge Microsoft Intune: Internet-hozzáférés kezelése házirend által [Microsoft Intune böngészővel.](/intune/app-configuration-managed-browser)

---

## <a name="march-2019"></a>2019. március

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identity Experience Framework és egyéni szabályzattámogatás Azure Active Directory B2C elérhető (GA)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2C – Fogyasztói identitáskezelési **termékképesség:** B2B/B2C

Most már létrehozhat egyéni szabályzatokat a Azure AD B2C, beleértve a következő feladatokat, amelyeket nagy léptékben és az Azure SLA keretében támogatunk:

- Egyéni hitelesítési felhasználói utak létrehozása és feltöltése egyéni házirendek használatával.

- A felhasználói utak részletes leírására jogcímszolgáltatók közötti adatcserékként.

- Feltételes elágaztatás definiálása a felhasználói utakban.

- Jogcímek átalakítása és leképezés a valós idejű döntésekhez és kommunikációhoz.

- Az REST API szolgáltatásokat használhatja az egyéni hitelesítési felhasználói utakon. Például e-mail-szolgáltatókkal, CRM-ekkel és jogvédett engedélyezési rendszerekkel.

- Összefedi az OpenIDConnect protokollnak megfelelő identitásszolgáltatókat. Például több-bérlős Azure AD-val, közösségi fiókszolgáltatókkal vagy kéttényezős ellenőrzési szolgáltatókkal.

További információ az egyéni szabályzatok létrehozásáról: Fejlesztői megjegyzések a szabályzatok egyéni Azure Active Directory B2C és Alex Simon blogbejegyzése, beleértve az [esettanulmányokat is.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791) [](../../active-directory-b2c/custom-policy-developer-notes.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Az Azure AD-alkalmazásgyűjteményben elérhető új összevont alkalmazások – 2019. március

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2019 márciusában hozzáadtunk ezt a 14 új, összevonási támogatást támogató alkalmazást az alkalmazásgyűjteményhez:

[ISEC7 Mobile Exchange](https://www.isec7.com/english/)Delegate , [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](../saas-apps/eplatform-tutorial.md), [Fulcrum,](../saas-apps/fulcrum-tutorial.md) [ExcelityGlobal,](../saas-apps/excelityglobal-tutorial.md) [Explanation-Based Auditing System](../saas-apps/explanation-based-auditing-system-tutorial.md), [Lean](../saas-apps/lean-tutorial.md), [Powerschool Performance Matters](../saas-apps/powerschool-performance-matters-tutorial.md), [Cinode,](https://cinode.com/) [Iris Intranet](../saas-apps/iris-intranet-tutorial.md), [Empactis](../saas-apps/empactis-tutorial.md), [SmartDraw,](../saas-apps/smartdraw-tutorial.md) [Confirmit Horizons](../saas-apps/confirmit-horizons-tutorial.md), [TAS](../saas-apps/tas-tutorial.md)

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása a Azure Active Directory [alkalmazásgyűjteményben.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Új Zscaler- és Atlass-kiépítési összekötők az Azure AD-katalógusban – 2019. március

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** App Provisioning **Product képesség:** külső fél integrációja

Automatizálhatja a következő alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

[Zscaler](../saas-apps/zscaler-provisioning-tutorial.md), [Zscaler Beta](../saas-apps/zscaler-beta-provisioning-tutorial.md), [Zscaler One](../saas-apps/zscaler-one-provisioning-tutorial.md), [Zscaler Two](../saas-apps/zscaler-two-provisioning-tutorial.md), [Zscaler Three](../saas-apps/zscaler-three-provisioning-tutorial.md), [Zscaler ZSCloud](../saas-apps/zscaler-zscloud-provisioning-tutorial.md), [Atlassian Cloud](../saas-apps/atlassian-cloud-provisioning-tutorial.md)

A szervezet automatizált felhasználóifiók-létesítésen keresztüli hatékonyabb biztonságával kapcsolatos további információkért lásd: Felhasználókiépítés automatizálása SaaS-alkalmazásokban az [Azure AD-val.](../app-provisioning/user-provisioning.md)

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Törölt Office 365-csoportok visszaállítása és kezelése az Azure AD portálon

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Csoportkezelési **termékképesség:** Együttműködés

Most már megtekintheti és kezelheti a törölt Office 365-csoportokat az Azure AD portálon. Ezzel a módosítással láthatja, hogy mely csoportok állíthatók vissza, valamint véglegesen törölheti a szervezet számára nem szükséges csoportokat.

További információ: Lejárt vagy [törölt csoportok visszaállítása.](../enterprise-users/groups-restore-deleted.md#view-and-manage-the-deleted-microsoft-365-groups-that-are-available-to-restore)

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Az egyszeri bejelentkezés mostantól elérhető az Azure AD SAML által védett helyszíni alkalmazásokhoz alkalmazásproxy (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Alkalmazásproxy-termékképesség:** Access Control

Mostantól egyszeri bejelentkezést (SSO) is biztosíthat a helyszíni, SAML által hitelesített alkalmazásokhoz, valamint távoli hozzáférést az alkalmazásokhoz a alkalmazásproxy. Az SAML SSO helyszíni alkalmazásokkal való beállításával kapcsolatos további információkért lásd: SAML single [sign-on for on-premises applications with alkalmazásproxy (Preview) (SAML egyszeri](../manage-apps/application-proxy-configure-single-sign-on-on-premises-apps.md)bejelentkezés helyszíni alkalmazásokhoz alkalmazásproxy (előzetes verzió) ).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>A kérelemhurkokban az ügyfélalkalmazások megszakadnak a megbízhatóság és a felhasználói élmény javítása érdekében

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Hitelesítések (bejelentkezések) **Termékképesség:** Felhasználói hitelesítés

Az ügyfélalkalmazások helytelenül több száz azonos bejelentkezési kérést tudnak kiadva rövid idő alatt. Ezek a kérések , függetlenül attól, hogy sikeresek-e vagy sem, mind hozzájárulnak az idP gyenge felhasználói élményéhez és magas számítási feladataihoz, növelik az összes felhasználó késését és csökkentik az internetszolgáltató rendelkezésre állását.

Ez a frissítés hibaüzenetet küld: az olyan ügyfélalkalmazások számára, amelyek rövid időn keresztül többször, a normál működés hatókörét túllépve ismétlődő `invalid_grant` `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` kéréseket küldnek. A problémába ütköző ügyfélalkalmazások interaktív parancssort mutatnak, amely arra kéri a felhasználót, hogy jelentkezzen be újra. A változásról és arról, hogy hogyan javíthatja ki az alkalmazást, ha ezt a hibát látja, lásd: [What's new for authentication?](../develop/reference-breaking-changes.md#looping-clients-will-be-interrupted)(Újdonságok a hitelesítéshez? ).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Új auditnaplók felhasználói felület érhető el

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termék képesség:** Figyelés & jelentéskészítés

Létrehoztunk egy új Azure AD **auditnapló-oldalt,** amely segít az olvashatóság és az adatok keresésének javításában. Az új Auditnaplók **lap megtekintéséhez** válassza az **Auditnaplók** lehetőséget az Azure AD **Tevékenység** szakaszában.

![Új auditnaplók oldal mintaadatokkal](media/whats-new/audit-logs-page.png)

Az új Auditnaplók lapról a naplózási tevékenységre vonatkozó jelentések a Azure Active Directory [oldalán található.](../reports-monitoring/concept-audit-logs.md#audit-logs) 

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Új figyelmeztetések és útmutatás a helytelenül konfigurált feltételes hozzáférési szabályzatok véletlen rendszergazdai zárolásának megakadályozásához

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Feltételes hozzáférési **termékképesség:** Identity Security & Protection

Annak érdekében, hogy a rendszergazdák véletlenül zárolják magukat a saját bérlőjükből helytelenül konfigurált feltételes hozzáférési szabályzatokkal, új figyelmeztetéseket hoztunk létre, és frissítettük az Azure Portal. Az új útmutatóval kapcsolatos további információkért lásd: Mik azok a szolgáltatásfüggőségek a [Azure Active Directory hozzáférésben.](../conditional-access/service-dependencies.md)

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Továbbfejlesztett végfelhasználói használati feltételek mobileszközökön

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség: Irányítás**

Frissítettük a meglévő használati feltételeket, hogy javítsuk a használati feltételek áttekintésének és a használati feltételeknek a mobileszközön való beleegyezését. Most már nagyíthat és kicsinyíthet, visszamehet, letöltheti az információkat, és kiválaszthatja a hivatkozásokat. A frissített használati feltételekkel kapcsolatos további információkért lásd a Azure Active Directory [szolgáltatási feltételeket.](../conditional-access/terms-of-use.md#what-terms-of-use-looks-like-for-users)

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Új Azure AD-tevékenységnaplók letöltési élménye

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termékképesség:** A jelentéskészítés & figyelése

Mostantól nagy mennyiségű tevékenységnaplót tölthet le közvetlenül a Azure Portal. Ez a frissítés a következőt teszi lehetővé:

- Legfeljebb 250 000 sort tölthet le.

- Értesítést kap a letöltés befejezése után.

- A fájlnév testreszabása.

- Határozza meg a kimeneti formátumot, amely lehet JSON vagy CSV.

További információ erről a szolgáltatásról: [Rövid útmutató:](../reports-monitoring/quickstart-download-audit-report.md) Naplójelentés letöltése a Azure Portal

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Használatlan változás: A feltételértékelés frissítései az Exchange ActiveSync (EAS) által

**Írja be a következőt:** Terv a **szolgáltatáskategória változására:** Feltételes hozzáférési **termékképesség:** Access Control

Folyamatban van annak frissítése, hogy az Exchange ActiveSync (EAS) hogyan értékeli ki a következő feltételeket:

- Felhasználó helye ország, régió vagy IP-cím alapján

- Bejelentkezési kockázat

- Eszközplatform

Ha korábban már használta ezeket a feltételeket a feltételes hozzáférési szabályzatban, vegye figyelembe, hogy a feltétel viselkedése megváltozhat. Ha például korábban a felhasználó helyére vonatkozó feltételt használta egy szabályzatban, előfordulhat, hogy a rendszer a felhasználó helye alapján kihagyja a szabályzatot.

---

## <a name="february-2019"></a>2019. február

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurálható Azure AD SAML-jogkivonattitkosítás (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Enterprise Apps **Termékképesség:** SSO

Mostantól bármilyen támogatott SAML-alkalmazást konfigurálhat a titkosított SAML-jogkivonatok fogadására. Ha egy alkalmazással van konfigurálva és használva, az Azure AD egy, az Azure AD-ban tárolt tanúsítványból származó nyilvános kulccsal titkosítja a kibocsátott SAML helyességi feltételt.

Az SAML-jogkivonat titkosításának konfigurálásával kapcsolatos további információkért lásd: [Configure Azure AD SAML token encryption (Az Azure AD SAML-jogkivonat titkosításának konfigurálása).](../manage-apps/howto-saml-token-encryption.md)

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Hozzáférési felülvizsgálat létrehozása csoportokhoz vagy alkalmazásokhoz az Azure AD hozzáférési felülvizsgálatok használatával

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Hozzáférési felülvizsgálatok **termékképesség:** Irányítás

Mostantól több csoportot vagy alkalmazást is tartalmazhat egyetlen Azure AD hozzáférési felülvizsgálatba csoporttagság vagy alkalmazás-hozzárendelés esetén. A több csoporttal vagy alkalmazással rendelkező hozzáférési felülvizsgálatokat ugyanazokkal a beállításokkal lehet beállítani, és a benne foglalt felülvizsgálók is egyszerre értesítést kapnak.

További információ a hozzáférési felülvizsgálatok Azure AD hozzáférési felülvizsgálatokkal való létrehozásáról: Csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure AD hozzáférési [felülvizsgálatok között](../governance/create-access-review.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2019. február

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Enterprise Apps **Termékképesség:** külső féltől származó integráció

2019 februárjában az alábbi 27, összevonási támogatást támogató új alkalmazást adtunk hozzá az alkalmazásgyűjteményhez:

[Euromonitor Passport](../saas-apps/euromonitor-passport-tutorial.md), [MindTickle,](../saas-apps/mindtickle-tutorial.md) [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](../saas-apps/airstack-tutorial.md), [Oracle Fusion ERP](../saas-apps/oracle-fusion-erp-tutorial.md), [IDrive,](../saas-apps/idrive-tutorial.md) [Skyward Qmlativ,](../saas-apps/skyward-qmlativ-tutorial.md) [Brightidea,](../saas-apps/brightidea-tutorial.md) [AlertOps,](../saas-apps/alertops-tutorial.md) [Lehetinsight-CloudGate SSO,](../saas-apps/soloinsight-cloudgate-sso-tutorial.md)Engedélykattintás, [Brandfolder](../saas-apps/brandfolder-tutorial.md), [StoregateSmartFile,](../saas-apps/smartfile-tutorial.md) [Pexip,](../saas-apps/pexip-tutorial.md) [Stormboard,](../saas-apps/stormboard-tutorial.md) [Seismic](../saas-apps/seismic-tutorial.md), [Share A Dream,](https://www.shareadream.org/how-it-works) [Bugsnag,](../saas-apps/bugsnag-tutorial.md) [webMethods Integration Cloud](../saas-apps/webmethods-integration-cloud-tutorial.md), Knowledge Anywhere [LMS](../saas-apps/knowledge-anywhere-lms-tutorial.md), [OU Campus](../saas-apps/ou-campus-tutorial.md), [Periscope Data](../saas-apps/periscope-data-tutorial.md), [Netop Portal](../saas-apps/netop-portal-tutorial.md), [smartvid.io](../saas-apps/smartvid.io-tutorial.md), [PureCloud by Genesys](../saas-apps/purecloud-by-genesys-tutorial.md), [ClickUp Productivity Platform](../saas-apps/clickup-productivity-platform-tutorial.md)

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az alkalmazás [Azure Active Directory katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="enhanced-combined-mfasspr-registration"></a>Továbbfejlesztett kombinált MFA-/SSPR-regisztráció

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Önkiszolgáló jelszó-visszaállítási **termékképesség:** Felhasználói hitelesítés

Az ügyfelek visszajelzésére reagálva továbbfejlesztjük az MFA/SSPR regisztrációs előzetes verzió egyesített élményét, hogy a felhasználók gyorsabban regisztrálják biztonsági adataikat mind az MFA, mind az SSPR számára.

**A felhasználók mai élményének javítása érdekében kövesse az alábbi lépéseket:**

1. Globális rendszergazdaként vagy felhasználói rendszergazdaként jelentkezzen be az Azure Portal lapra, és Azure Active Directory > felhasználói beállításokat > a hozzáférési panel előzetes funkcióinak **beállításainak kezeléséhez.**

2. A Users who can use the preview **features for registering and managing security info – refresh** (A felhasználók, akik az előzetes verziójú funkciókat használhatja **a** biztonsági adatok regisztrálása és kezelése – frissítés) lehetőségben kapcsolja be a funkciókat a Kijelölt felhasználók csoport vagy a Minden felhasználó **számára.**

A következő hetekben megszüntetjük a régi kombinált MFA/SSPR regisztrációs előzetes verzió bekapcsolási képességét azon bérlőknél, amelyek még nem kapcsolták be.

**Annak ellenőrzéséhez, hogy a vezérlő el lesz-e távolítva a bérlőjéhez, kövesse az alábbi lépéseket:**

1. Globális rendszergazdaként vagy felhasználói rendszergazdaként jelentkezzen be a Azure Portal, és Azure Active Directory > a Felhasználói beállítások lap > beállítások kezelése a hozzáférési panel előzetes **funkcióihoz** lehetőséget.

2. Ha a Users who can use the preview **features for registering and managing security info** (A felhasználók, akik az előzetes verziójú funkciókat használhatja a biztonsági adatok regisztrálása és kezelése) beállítás Nincs beállításra van állítva, a rendszer eltávolítja a beállítást a bérlőből. 

Függetlenül attól, hogy korábban bekapcsolta-e a régi kombinált MFA/SSPR regisztrációs előzetes verziójú felhasználói élményt a felhasználók számára, a régi élmény egy későbbi időpontban ki lesz kapcsolva. Emiatt határozottan javasoljuk, hogy a lehető leghamarabb lépjen át az új, továbbfejlesztett felhasználói élményre.

További információ a továbbfejlesztett regisztrációs élményről: Az Azure AD kombinált MFA és jelszó-visszaállítás regisztrációs szolgáltatásának cool [fejlesztései.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)

---

### <a name="updated-policy-management-experience-for-user-flows"></a>A felhasználói folyamatok szabályzatkezelési felületének frissítése

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** B2C – Fogyasztói identitáskezelési **termékképesség:** B2B/B2C

Egyszerűbben frissítettük a felhasználói folyamatok (korábbi nevén beépített szabályzatok) szabályzat-létrehozási és -kezelési folyamatát. Ez az új felhasználói élmény mostantól az összes Azure AD-bérlő alapértelmezett beállítása.

További visszajelzéseket és javaslatokat adhat a portál képernyő  tetején található Visszajelzés küldése területen található mosolygó vagy rosszalló ikonokkal.

Az új szabályzatkezelési funkcióval kapcsolatos további információkért tekintse meg a Azure AD B2C már rendelkezik [JavaScript](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) testreszabással és számos további új funkcióval blogot.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Válassza ki a Azure AD B2C

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2C – Fogyasztói identitáskezelési **termékképesség:** B2B/B2C

Most már kiválaszthatja az oldalelemek egy adott verzióját, amelyet a Azure AD B2C. Egy adott verzió kiválasztásával tesztelheti a frissítéseket, mielőtt azok megjelennek egy oldalon, és kiszámítható viselkedést kaphat. Emellett engedélyezheti bizonyos oldalverziók kényszerítése a JavaScript-testreszabások engedélyezése érdekében. A funkció bekapcsoláshoz a felhasználói folyamatok **Tulajdonságok** lapjára kell fordulnia.

Az oldalelemek adott verzióinak kiválasztásával kapcsolatos további információkért tekintse meg a Azure AD B2C már rendelkezik [JavaScript](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) testreszabással és számos további új funkcióval blogot.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurálható végfelhasználói jelszókövetelmények B2C-hez (GA)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2C – Fogyasztói identitáskezelési **termékképesség:** B2B/B2C

Mostantól a natív Azure AD-jelszóhasználati szabályzat használata helyett beállíthatja a szervezet jelszó-összetettségét a végfelhasználók számára. A felhasználói folyamatok **Tulajdonságok** paneljére (korábbi nevén beépített szabályzatok) kiválaszthatja  az Egyszerű vagy az **Erős** jelszó összetettségét, vagy létrehozhat egyéni követelménykészletet. 

A jelszó összetettségére vonatkozó követelmények konfigurálásával kapcsolatos további információkért lásd: [Configure complexity requirements for passwords in Azure Active Directory B2C.](../../active-directory-b2c/password-complexity.md)

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Új alapértelmezett sablonok egyéni védjegyes hitelesítési élményhez

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2C – Fogyasztói identitáskezelési **termékképesség:** B2B/B2C

A felhasználói folyamatok Oldalelrendezések  paneljére (korábbi nevén beépített szabályzatok) használhatja az új alapértelmezett sablonokat, hogy egyéni, védjegyes hitelesítési élményt hozzon létre a felhasználók számára.

A sablonok használatával kapcsolatos további információkért lásd: Azure AD B2C már rendelkezik JavaScript testreszabással és számos [további új funkcióval.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)

---

## <a name="january-2019"></a>2019. január

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Active Directory B2B-együttműködés engedélyezése egyszeres PIN-kódos hitelesítéssel (nyilvános előzetes verzió)

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** B2B **termékképesség:** B2B/B2C

Bevezettük az egyszeri PIN-kódos hitelesítést (OTP) azon B2B vendégfelhasználók számára, akik nem hitelesíthetők más módon, például az Azure AD-n, Microsoft-fiók-n (MSA) vagy Google-összevonáson keresztül. Ez az új hitelesítési módszer azt jelenti, hogy a vendégfelhasználóknak nem kell új Microsoft-fiók. Ehelyett egy meghívó beváltása vagy egy megosztott erőforrás elérése közben a vendégfelhasználó kérheti egy ideiglenes kód e-mail-címre való elküldését. Ezzel az ideiglenes kóddal a vendégfelhasználó továbbra is bejelentkezhet.

További információkért lásd: Egyszeri pin-kódos hitelesítés küldése [e-mailben (előzetes verzió)](../external-identities/one-time-passcode.md) és a blog: [Az Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)zökkenőmentesen teszi a megosztást és az együttműködést bármely fiókkal használó felhasználó számára.

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Új Azure AD alkalmazásproxy cookie-beállítások

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Alkalmazásproxy **termékképessége:** Access Control

Három új cookie-beállítást vezettünk be, amelyek a következőn keresztül közzétett alkalmazásokhoz alkalmazásproxy:

- **Használja HTTP-Only cookie-t.** Beállítja a **HTTPOnly** jelzőt a alkalmazásproxy a hozzáférési és munkamenet-cookie-khoz. A beállítás bekapcsolása további biztonsági előnyökkel jár, például segít megelőzni a cookie-k másolását vagy módosítását az ügyféloldali parancsfájlok használatával. Javasoljuk, hogy a további előnyökért kapcsolja be ezt a jelzőt **(válassza** az Igen lehetőséget).

- **Használjon biztonságos cookie-t.** Beállítja **a Biztonságos jelzőt** a alkalmazásproxy és munkamenet-cookie-khoz. A beállítás bekapcsolása további biztonsági előnyökkel jár, mivel biztosítja, hogy a cookie-k csak TLS biztonságos csatornákon, például HTTPS-kapcsolaton keresztül továbbíthatók. Javasoljuk, hogy a további előnyökért kapcsolja be ezt a jelzőt **(válassza** az Igen lehetőséget).

- **Használjon állandó cookie-t.** Megakadályozza a cookie-k elérésének lejáratát a webböngésző bezárása után. Ezek a cookie-k a hozzáférési jogkivonat teljes élettartama alatt elérhetőek. A cookie-k azonban alaphelyzetbe állnak, ha elérik a lejárati időt, vagy ha a felhasználó manuálisan törli a cookie-t. Azt javasoljuk, hogy tartsa meg az alapértelmezett **No**(Nem) beállítást, csak olyan régebbi alkalmazásoknál kapcsolja be a beállítást, amelyek nem osztanak meg cookie-kat a folyamatok között.

További információ az új [cookie-król: Cookie-beállítások](../manage-apps/application-proxy-configure-cookie-settings.md)a helyszíni alkalmazások eléréséhez a Azure Active Directory.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2019. január

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2019 januárjában hozzáadtunk ezt a 35 új, összevonási támogatást támogató alkalmazást az alkalmazásgyűjteményhez:

[Firstbird](../saas-apps/firstbird-tutorial.md), [Folloze](../saas-apps/folloze-tutorial.md), [Talent Palette](../saas-apps/talent-palette-tutorial.md), [Infor CloudSuite](../saas-apps/infor-cloud-suite-tutorial.md), [Cisco Umbrella](../saas-apps/cisco-umbrella-tutorial.md), [Zscaler Internet Access Administrator,](../saas-apps/zscaler-internet-access-administrator-tutorial.md) [Expiration Reminder](../saas-apps/expiration-reminder-tutorial.md), [InstaVR Viewer,](../saas-apps/instavr-viewer-tutorial.md) [CorpTax](../saas-apps/corptax-tutorial.md), [Verb](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital Close](../saas-apps/pavaso-digital-close-tutorial.md), [GoodPractice Toolkit](../saas-apps/goodpractice-toolkit-tutorial.md), [Cloud Service PICCO,](../saas-apps/cloud-service-picco-tutorial.md) [AuditBoard](../saas-apps/auditboard-tutorial.md), [iProva](../saas-apps/iprova-tutorial.md), [Workable](../saas-apps/workable-tutorial.md), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO System](../saas-apps/gtnexus-sso-module-tutorial.md), [CBRE ServiceInsight](../saas-apps/cbre-serviceinsight-tutorial.md), [Deskradar](../saas-apps/deskradar-tutorial.md), [Deskogixv](../saas-apps/coralogix-tutorial.md), [Signagelive](../saas-apps/signagelive-tutorial.md), [ARES for Enterprise](../saas-apps/ares-for-enterprise-tutorial.md), [K2 for Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](../saas-apps/idid-manager-tutorial.md), [HighGear](../saas-apps/highgear-tutorial.md), [Visitly](../saas-apps/visitly-tutorial.md), [KornLp ALP](../saas-apps/korn-ferry-alp-tutorial.md), [Acadia](../saas-apps/acadia-tutorial.md), [Adoddle cSaas Platform](../saas-apps/adoddle-csaas-platform-tutorial.md)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az alkalmazás [Azure Active Directory katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Az Azure AD Identity Protection fejlesztései (nyilvános előzetes verzió)

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Identity Protection **termékképesség:** Identity Security & Protection

Nagy izgatottan jelentjük be, hogy a nyilvános előzetes verziók ajánlata a következő Azure AD Identity Protection bővült, többek között:

- Frissített és integráltabb felhasználói felület

- További API-k

- Továbbfejlesztett kockázatfelmérés gépi tanulással

- Az egész termékre kiterjedő igazítás a kockázatos felhasználók és a kockázatos bejelentkezések között

A fejlesztésekkel kapcsolatos további információkért lásd: Mi a Azure Active Directory Identity Protection [(frissítve)?](../identity-protection/overview-identity-protection.md) ha többet szeretne megtudni, és a terméken keresztül megosztja a gondolatát.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Az alkalmazászárolás új Microsoft Authenticator iOS- és Android-eszközökön

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Microsoft Authenticator App **Product képesség:** Identity Security & Protection

Az egyszer használható PIN-kód, alkalmazásinformációk és alkalmazásbeállítások biztonságának biztosítása érdekében az alkalmazás alkalmazászárolás funkcióját Microsoft Authenticator be. A alkalmazászárolás bekapcsolása azt jelenti, hogy a rendszer minden alkalommal kérni fogja a PIN-kód vagy biometrika használatával történő hitelesítést, amikor megnyitja Microsoft Authenticator alkalmazást.

További információért tekintse meg a gyakori [Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdéseket.](../user-help/user-help-auth-app-faq.md)

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Továbbfejlesztett Azure AD Privileged Identity Management (PIM) exportálási képességei

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Privileged Identity Management **termékképesség:** Privileged Identity Management

Privileged Identity Management (PIM) rendszergazdái mostantól exportálhatják egy adott erőforrás összes aktív és jogosult szerepkör-hozzárendelését, beleértve az összes gyermekerőforrás szerepkör-hozzárendelését is. Korábban a rendszergazdáknak nehéz volt az előfizetés szerepkör-hozzárendelései teljes listáját lekértük, és minden egyes erőforráshoz exportálni kellett a szerepkör-hozzárendeléseket.

További információ: [View activity and audit history for Azure resource roles in PIM (Az Azure-erőforrás-szerepkörök tevékenység- és naplózási előzményeinek megtekintése a PIM-ban).](../privileged-identity-management/azure-pim-resource-rbac.md)

---

## <a name="novemberdecember-2018"></a>2018. november/december

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>A szinkronizálási hatókörből eltávolított felhasználók már nem csak felhőalapú fiókokra váltnak

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** Felhasználókezelési **termékképesség:** Címtár

>[!Important]
>Ennek a javításnak az oka, hogy ön frusztrációt okoz, és megértjük a frusztrációt. Ezért ezt a változtatást mindaddig visszaállítjuk, amíg a javítást könnyebben megvalósíthatja a szervezetben.

Kijavítottunk egy hibát, amely miatt egy felhasználó DirSyncEnabled jelzője tévesen  Hamisra vált, amikor a Active Directory Domain Services(AD DS) objektum ki lett zárva a szinkronizálási hatókörből, majd a következő szinkronizálási ciklusban átkerült az Azure AD Lomtár-beli jelölőjébe. A javítás eredményeként, ha a felhasználó ki van zárva a szinkronizálási hatókörből, majd később visszaállítja az Azure AD Lomtár-ból, a felhasználói fiók a várt módon szinkronizálva marad a helyszíni AD-ről, és nem kezelhető a felhőben, mivel a hitelesítés forrása (SoA) továbbra is helyszíni AD marad.

A javítás előtt hiba történt, amikor a DirSyncEnabled jelzőt Hamisra váltotta. Helytelenül adta azt a megjelenést, hogy ezeket a fiókokat csak felhőbeli objektumokká konvertálták, és hogy a fiókok a felhőben kezelhetők. A fiókok azonban továbbra is a helyszíni AD-ről származó soA-t és az összes szinkronizált tulajdonságot (árnyékattribútumot) megőrzik. Ez az állapot több olyan problémát okozott az Azure AD-ban és más felhőbeli számítási feladatokban (például az Exchange Online-ban), amelyek ezeket a fiókokat az AD-ból szinkronizáltként kezelték, de jelenleg csak felhőalapú fiókokként működnek.

Jelenleg az AD-ről szinkronizált fiók kizárólag felhőalapú fiókká való valódi konvertálásának egyetlen módja a DirSync letiltása a bérlő szintjén, amely elindít egy háttérműveletet az soA átviteléhez. Az ilyen típusú SoA-változáshoz (de nem kizárólagosan) meg kell tisztítani az összes helyszíni kapcsolódó attribútumot (például a LastDirSyncTime és az árnyékattribútumokat), és jelet kell küldeni más felhőalapú számítási feladatoknak, hogy a megfelelő objektumát is csak felhőalapú fiókra konvertálják.

Ez a javítás ezért megakadályozza az AD-ről szinkronizált felhasználó ImmutableID attribútumának közvetlen frissítését, ami bizonyos múltbeli forgatókönyvekben kötelező volt. Kialakításából adódóan az Azure AD-ben egy objektum ImmutableID-nek , ahogyan a neve is mutatja, nem módosíthatónak kell lennie. A szinkronizálási ügyfélprogramban Azure AD Connect Health és Azure AD Connect új funkciók érhetők el az ilyen helyzetek megoldásához:

- **Nagy léptékű ImmutableID frissítés sok felhasználó számára szakaszos megközelítéssel**

  Például hosszadalmas áttelepítést kell AD DS erdők közötti áttelepítéshez. Megoldás: A Azure AD Connect használatával  konfigurálhatja a Forráshorgonyt, és a felhasználó migrálta a meglévő ImmutableID értékeket az Azure AD-ből az új erdő helyi AD DS-ms-DS-Consistency-Guid attribútumába. További információ: [Using ms-DS-ConsistencyGuid as sourceAnchor (Az ms-DS-ConsistencyGuid használata sourceAnchorként).](../hybrid/plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor)

- **Nagy léptékű ImmutableID-frissítések számos felhasználó számára egyetlen léptékű frissítésben**

  Például a Azure AD Connect hibát vét, és most módosítania kell a SourceAnchor attribútumot. Megoldás: Tiltsa le a DirSyncet a bérlő szintjén, és törölje az összes érvénytelen ImmutableID értéket. További információ: [Az Office 365 címtár-szinkronizálásának kikapcsolása.](/office365/enterprise/turn-off-directory-synchronization)

- Helyszíni felhasználó és meglévő Azure **AD-felhasználó újra egyeződése** Például egy, a AD DS-ban újra létrehozott felhasználó duplikáltat hoz létre az Azure AD-fiókban ahelyett, hogy egy meglévő Azure AD-fiókkal (árva objektummal) újra egyezést hoz létre. Megoldás: A Azure AD Connect Health a Azure Portal a Source Anchor/ImmutableID újra leképezésében. További információ: [Árva objektumok forgatókönyve.](../hybrid/how-to-connect-health-diagnose-sync-errors.md#orphaned-object-scenario)

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Nem megfelelő változás: Az audit- és bejelentkezési naplók sémáját frissíti a Azure Monitor

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termék képesség:** Figyelés & jelentéskészítés

Jelenleg a naplózási és bejelentkezési naplóstreameket is közzétenjük az Azure Monitor-ban, így zökkenőmentesen integrálhatja a naplófájlokat az SIEM-eszközökkel vagy a Log Analytics szolgáltatással. A visszajelzése alapján és a funkció általánosan elérhető bejelentésének előkészítéseként a sémát a következő módosításokat juk végre. Ezek a sémaváltozások és a kapcsolódó dokumentációfrissítések január első hetében történnek meg.

#### <a name="new-fields-in-the-audit-schema"></a>Új mezők a naplózási sémában
Hozzáadunk egy új **Művelettípus** mezőt, amely az erőforráson végrehajtott művelet típusát adja meg. Például adja hozzá a **következőt: Add**(Hozzáadás) , **Update**(Frissítés) vagy **Delete (Törlés).**

#### <a name="changed-fields-in-the-audit-schema"></a>Módosított mezők az Audit sémában
Az auditsémában a következő mezők változnak:

|Mező neve|Mi változott?|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|Kategória|Ez volt a **Szolgáltatásnév** mező. Ez most a **Naplózási kategóriák** mező. **A szolgáltatás neve** a **loggedByService** mezőre lett átnevezve.|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Önkiszolgáló jelszó-visszaállítás</li></ul>|<ul><li>Felhasználókezelés</li><li>Csoportkezelés</li><li>Alkalmazáskezelés</li></ul>|
|targetResources|A felső szinten tartalmazza **a TargetResourceType** típust.|&nbsp;|<ul><li>Szabályzat</li><li>Alkalmazás</li><li>Felhasználó</li><li>Group</li></ul>|
|loggedByService|Az auditnaplót generáló szolgáltatás nevét biztosítja.|Null|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Új jelszó önkiszolgáló kérése</li></ul>|
|Eredmény|Az auditnaplók eredményét biztosítja. Ezt korábban enumeráltuk, de most a tényleges értéket mutatjuk be.|<ul><li>0</li><li>1</li></ul>|<ul><li>Siker</li><li>Hiba</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Módosított mezők a bejelentkezési sémában
A bejelentkezési sémában a következő mezők változnak:

|Mező neve|Mi változott?|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Ez volt a **conditionalaccessPolicies** mező. Ez most már **az appliedConditionalAccessPolicies** mező.|Nincs változás besorolás|Nincs változás besorolás|
|conditionalAccessStatus|A feltételes hozzáférési szabályzat állapotának eredményét biztosítja a bejelentkezésnél. Ezt korábban enumeráltuk, de most a tényleges értéket mutatjuk be.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Siker</li><li>Hiba</li><li>Nincs alkalmazva</li><li>Disabled (Letiltva)</li></ul>|
|appliedConditionalAccessPolicies: eredmény|Az egyéni feltételes hozzáférési szabályzat állapotának eredményét biztosítja a bejelentkezésnél. Ezt korábban enumeráltuk, de most a tényleges értéket mutatjuk be.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Siker</li><li>Hiba</li><li>Nincs alkalmazva</li><li>Disabled (Letiltva)</li></ul>|

További információ a sémáról: [Az Azure AD auditnaplók](../reports-monitoring/reference-azure-monitor-audit-log-schema.md) sémája értelmezése a Azure Monitor (előzetes verzió)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Az Identity Protection fejlesztései a felügyelt gépi tanulási modellben és a kockázati pontszám motorban

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Identity Protection **termékképesség:** Kockázati pontszámok

Az Identity Protectionrel kapcsolatos felhasználói és bejelentkezési kockázatfelmérési motor fejlesztései segíthetnek a felhasználói kockázat pontosságának és lefedettségének javításában. A rendszergazdák észrevehetnek, hogy a felhasználói kockázati szint már nem kapcsolódik közvetlenül az adott észlelések kockázati szintjéhez, és a kockázatos bejelentkezési események számának és szintjének növekedése is növekszik.

A kockázatészleléseket a felügyelt gépi tanulási modell értékeli, amely a felhasználói kockázatokat a felhasználói bejelentkezések további funkcióinak és az észlelési mintáknak a használatával számítja ki. A modell alapján előfordulhat, hogy a rendszergazda magas kockázati pontszámmal rendelkező felhasználókat talál, még akkor is, ha az adott felhasználóhoz társított észlelések alacsony vagy közepes kockázatúak.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>A rendszergazdák a saját jelszavukat a Microsoft Authenticator (nyilvános előzetes verzió) használatával állíthatják vissza

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Az önkiszolgáló jelszó-visszaállítási **termék képessége:** Felhasználói hitelesítés

Az Azure AD-rendszergazdák mostantól új jelszót állíthatnak be a Microsoft Authenticator alkalmazásértesítések vagy bármely mobilhitelesítési alkalmazás vagy hardvertoken kódjának használatával. A saját jelszavuk visszaállításához a rendszergazdák az alábbi módszerek közül két módszert használhatnak:

- Microsoft Authenticator alkalmazásértesítés

- Egyéb mobilhitelesítő alkalmazás / Hardveres jogkivonat kódja

- E-mail

- Telefonhívás

- Szöveges üzenet

További információ a jelszó-visszaállításhoz Microsoft Authenticator alkalmazás használatával kapcsolatban: Azure AD önkiszolgáló jelszó-visszaállítás – Mobilalkalmazás és [SSPR (előzetes verzió)](../authentication/concept-sspr-howitworks.md#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Új Azure AD felhőalapú eszköz-rendszergazdai szerepkör (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Eszközregisztráció és -felügyeleti **termékképesség:** Hozzáférés-vezérlés

A rendszergazdák felhasználókat rendelhetnek az új felhőalapúeszköz-rendszergazdai szerepkörhöz a felhőalapú eszközök rendszergazdai feladatainak elvégzéséhez. A Felhőeszköz-rendszergazdák szerepkörhöz rendelt felhasználók engedélyezhetik, letilthatják és törölhetik az eszközöket az Azure AD-ban, valamint olvashatják Windows 10 BitLocker-kulcsokat (ha vannak) az Azure Portal.

További információ a szerepkörökről és engedélyekről: [Rendszergazdai szerepkörök hozzárendelése a Azure Active Directory](../roles/permissions-reference.md)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Eszközök kezelése az új tevékenység-időbélyegzővel az Azure AD-ban (nyilvános előzetes verzió)

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Eszközregisztráció és -felügyeleti **termékképesség:** Eszköz életciklus-kezelése

Tisztában vagyunk vele, hogy idővel frissítenie és ki kell mondania a szervezet eszközeit az Azure AD-ban, hogy ne legyen elavult eszköz a környezetében. A folyamat segítése érdekében az Azure AD új tevékenység-időbélyeggel frissíti az eszközöket, így segít az eszköz életciklusának kezelésében.

Az időbélyeg le- és használatának további információiért lásd: How To: Manage the elavult devices in Azure AD (Az elavult eszközök kezelése az [Azure AD-ban)](../devices/manage-stale-devices.md)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>A rendszergazdák megkövetelhetik a felhasználóktól, hogy minden eszközön elfogadják a használati feltételeket

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség: Irányítás**

A rendszergazdák mostantól bekapcsolhatják a **Require users to consent on every** device (Felhasználói jóváhagyás megkövetelését minden eszközhöz) beállítást, hogy megkövetelje a felhasználóktól a használati feltételek elfogadását a bérlőn használt összes eszközön.

További információért tekintse meg a használati feltételek funkció Azure Active Directory [eszközökre vonatkozó használati feltételek szakaszát.](../conditional-access/terms-of-use.md#per-device-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>A rendszergazdák ismétlődő ütemezés alapján konfigurálhatják a használati feltételek lejáratát

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség: Irányítás**


A rendszergazdák mostantól  bekapcsolhatják a Jóváhagyások lejárata beállítást, hogy a használati feltételek az összes felhasználóra érvényesek leévülnek a megadott ismétlődő ütemezés alapján. Az ütemezés lehet évente, kétévente, negyedévente vagy havonta. A használati feltételek lejárta után a felhasználóknak újra el kell érvénybe járniuk.

További információkért lásd a használati feltételekkel kapcsolatos Azure Active Directory [szakaszát.](../conditional-access/terms-of-use.md#add-terms-of-use)

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>A rendszergazdák úgy konfigurálhatják a használati feltételeket, hogy az egyes felhasználók ütemezése alapján lejárjon

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség: Irányítás**

A rendszergazdák mostantól megadhatnak egy időtartamot, amely alatt a felhasználónak újra el kell használhatja a használati feltételeket. A rendszergazdák meghatározhatják például, hogy a felhasználóknak 90 naponta újra el kell acacednie a használati feltételeket.

További információkért lásd a használati feltételek hozzáadását Azure Active Directory [szakaszát.](../conditional-access/terms-of-use.md#add-terms-of-use)

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Új Azure AD Privileged Identity Management (PIM) e-mailek Azure Active Directory szerepkörökhöz

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Privileged Identity Management **termékképesség:** Privileged Identity Management

A Azure AD Privileged Identity Management (PIM) használó ügyfelek mostantól heti kivonatoló e-mailt kaphatnak, amely az alábbi információkat tartalmazza az elmúlt hét napra:

- A legfontosabb jogosult és állandó szerepkör-hozzárendelések áttekintése

- Szerepköröket aktiváló felhasználók száma

- Szerepkörökhöz rendelt felhasználók száma a PIM-ban

- A PIM-en kívüli szerepkörökhöz rendelt felhasználók száma

- A PIM-hez "véglegesen" kötött felhasználók száma

További információ a PIM-ről és az elérhető e-mail-értesítésekről: E-mail-értesítések [a PIM-ben.](../privileged-identity-management/pim-email-notifications.md)

---

### <a name="group-based-licensing-is-now-generally-available"></a>Általánosan elérhető a csoportalapú licencelés

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Egyéb **termékképesség:** Címtár

A csoportalapú licencelés a nyilvános előzetes verzióból érhető el, és mostantól általánosan elérhető. Ennek az általános kiadásnak a részeként skálázhatóbb lett ez a funkció, és lehetővé vált a csoportalapú licenc-hozzárendelések újrafeldolgozása egyetlen felhasználó számára, valamint a csoportalapú licencelés office 365 E3/A3-licencekkel való használata.

További információ a csoportalapú licencelésről: Mi az a csoportalapú licencelés a [Azure Active Directory?](./active-directory-licensing-whatis-azure-portal.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2018. november

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2018 novemberében hozzáadtunk ezt a 26 új, összevonási támogatást támogató alkalmazást az alkalmazásgyűjteményhez:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](../saas-apps/hubspot-tutorial.md), [GetThere](../saas-apps/getthere-tutorial.md), [Gra-Pe](../saas-apps/grape-tutorial.md), [eHour,](https://getehour.com/try-now) [Consent2Go,](../saas-apps/consent2go-tutorial.md) [Appinux,](../saas-apps/appinux-tutorial.md) [DriveLar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall,](../saas-apps/useall-tutorial.md) [Infinite Campus](../saas-apps/infinitecampus-tutorial.md), [Alhy](https://alayagood.com), [HeyBuddy](../saas-apps/heybuddy-tutorial.md), [Wrike SAML](../saas-apps/wrike-tutorial.md), [Drift](../saas-apps/drift-tutorial.md), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](../saas-apps/everbridge-tutorial.md), [IDEO,](https://profile.ideo.com/users/sign_up) [Service Manager (ISM)](../saas-apps/ivanti-service-manager-tutorial.md), [Peakon,](../saas-apps/peakon-tutorial.md) [Allbound SSO,](../saas-apps/allbound-sso-tutorial.md) [Plex Apps -](https://test.plexonline.com/signon)Classic Test , [Plex Apps – Classic](https://www.plexonline.com/signon), Plex Apps - UX Test , Plex Apps – UX , [Plex Apps – UX](https://cloud.plex.com/sso), [Plex Apps – IAM](https://accounts.plex.com/), [CRAFTS - Child child records, Plex, & Pénzügyi követő rendszer](https://getcrafts.ca/craftsregistration) [](https://test.cloud.plex.com/sso)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

## <a name="october-2018"></a>2018. október

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Az Azure AD-naplók mostantól működnek az Azure Log Analytics szolgáltatással (nyilvános előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termék képesség:** Figyelés & jelentéskészítés

Izgatottan várjuk, hogy mostantól továbbíthatja Azure AD-naplóit az Azure Log Analyticsbe! Ez a kért funkció még jobb hozzáférést biztosít az üzleti, üzemeltetési és biztonsági elemzésekhez, valamint segít az infrastruktúra figyelésében. További információt az Azure [Log Analytics Azure Active Directory tevékenységnaplókban](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) elérhető blogban talál.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2018. október

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Enterprise Apps **Termékképesség:** külső féltől származó integráció

2018 októberében hozzáadtunk ezt a 14 új alkalmazást összevonási támogatással az alkalmazásgyűjteményhez:

My [Award Points](../saas-apps/myawardpoints-tutorial.md), [Vibe HCM](../saas-apps/vibehcm-tutorial.md), ambyint, [MyWorkDrive,](../saas-apps/myworkdrive-tutorial.md) [BorrowBox,](../saas-apps/borrowbox-tutorial.md)Dialpad, [ON24 Virtual Environment](../saas-apps/on24-tutorial.md), [RingCentral](../saas-apps/ringcentral-tutorial.md), [Zscaler Three](../saas-apps/zscaler-three-tutorial.md), [Phraseanet](../saas-apps/phraseanet-tutorial.md), [Appraisd](../saas-apps/appraisd-tutorial.md), [Workspot Control](../saas-apps/workspotcontrol-tutorial.md), [Shuccho Navi](../saas-apps/shucchonavi-tutorial.md), [Glassfrog](../saas-apps/glassfrog-tutorial.md)

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása a Azure Active Directory [alkalmazásgyűjteményben.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services e-mail-értesítések küldése

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Azure AD Domain Services **termékképesség:** Azure AD Domain Services

Azure AD Domain Services riasztásokat ad a Azure Portal tartomány helytelen konfigurációiról vagy problémáiról. Ezek a riasztások részletes útmutatókat tartalmaznak, így anélkül próbálhatja megoldani a problémákat, hogy kapcsolatba kell lépnie az ügyfélszolgálattal.

Októbertől testre szabhatja a felügyelt tartomány értesítési beállításait, így új riasztások esetén e-mailt küld a kijelölt személyek csoportjának, így nem kell folyamatosan ellenőrizni a portálon a frissítéseket.

További információ: [Értesítési beállítások a Azure AD Domain Services.](../../active-directory-domain-services/notifications.md)

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Az Azure AD-portál támogatja a ForceDelete tartományi API használatát egyéni tartományok törléséhez

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Címtárkezelési **termék képesség:** Címtár

Örömmel jelentjük be, hogy mostantól használhatja a ForceDelete tartományi API-t az egyéni tartománynevek aszinkron módon történő törléséhez, például felhasználók, csoportok és alkalmazások egyéni tartománynévből (contoso.com) az eredeti alapértelmezett tartománynévre (contoso.onmicrosoft.com).

Ezzel a módosítással gyorsabban törölheti az egyéni tartományneveket, ha a szervezet már nem használja a nevet, vagy ha a tartománynevet egy másik Azure AD-val kell használnia.

További információ: [Egyéni tartománynév törlése.](../enterprise-users/domains-manage.md#delete-a-custom-domain-name)

---

## <a name="september-2018"></a>2018. szeptember

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Frissített rendszergazdai szerepköri engedélyek dinamikus csoportokhoz

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** Csoportkezelési **termékképesség:** Együttműködés

Kijavítottunk egy problémát, így az egyes rendszergazdai szerepkörök mostantól dinamikus tagsági szabályokat hozhatnak létre és frissíthetnek anélkül, hogy a csoport tulajdonosának kellene lennie.

A szerepkörök a következőek:

- Globális rendszergazda

- Intune-rendszergazda

- Felhasználói rendszergazda

További információ: [Dinamikus](../enterprise-users/groups-create-rule.md) csoport létrehozása és állapot ellenőrzése

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Egyszerűsített egyalkalmazásos Sign-On (SSO) konfigurációs beállításai külső alkalmazásokhoz

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Enterprise Apps **Termékképesség:** SSO

Tisztában vagyunk vele, hogy az Sign-On (SSO) beállítása saaS-alkalmazásokhoz kihívást jelenthet az egyes alkalmazáskonfigurációk egyedi jellege miatt. Egyszerűsített konfigurációt hoztunk létre, amely automatikusan kitölti az SSO konfigurációs beállításait a következő külső SaaS-alkalmazásokhoz:

- Zendesk

- ArcGis Online

- Jamf Pro

Az egykattintásos élmény használatának elkezdéséhez kattintson az **Azure Portal**  >  **SSO konfigurációs** oldalára. További információ: [SaaS-alkalmazás integrációja a Azure Active Directory](../saas-apps/tutorial-list.md)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – Hol találhatók az adatok? Oldalala

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Egyéb **termékképesség:** GoLocal

Válassza ki a vállalat régióját a Azure Active Directory – Itt **található** az adatok lapja, ahol megtekintheti, hogy melyik Azure-adatközpontban találhatók az összes Azure AD-szolgáltatáshoz az Azure AD-adatok. Az adatokat szűrheti a vállalat régiójához megadott Azure AD-szolgáltatások alapján.

A funkció eléréséhez és további információért lásd: [Azure Active Directory – Hol találhatók az adatai.](https://aka.ms/AADDataMap)

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Új üzembe helyezési terv érhető el a Saját alkalmazások Hozzáférési panelhez

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Saját alkalmazások **termékképesség:** SSO

Tekintse meg a Hozzáférési panel () Saját alkalmazások elérhető új üzembe helyezési https://aka.ms/deploymentplans) tervet.
A Saját alkalmazások Hozzáférési panelen a felhasználók egyetlen helyen találhatják meg és férhetnek hozzá az alkalmazásaikhoz. Ez a portál önkiszolgáló lehetőségeket is kínál a felhasználóknak, például hozzáférést kér az alkalmazásokhoz és csoportokhoz, vagy kezeli az erőforrásokhoz való hozzáférést mások nevében.

További információ: Mi az Saját alkalmazások [portál?](../user-help/my-apps-portal-end-user-access.md)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Az új Hibaelhárítás és támogatás lap a bejelentkezési naplók lapján Azure Portal

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termékképesség:** Figyelés & jelentéskészítéshez

A **Azure Portal** bejelentkezések oldalának  új Hibaelhárítás és támogatás lapján segítséget nyújt a rendszergazdáknak és a támogatási mérnököknek az Azure AD-bejelentkezésekkel kapcsolatos problémák elhárításában. Ez az új lap tartalmazza a hibakódot, a hibaüzenetet és a megoldási javaslatokat (ha vannak) a probléma megoldásához. Ha nem tudja megoldani a problémát, új módon is létrehozhat egy támogatási  jegyet a Másolás a vágólapra használatával, amely kitölti a kérésazonosító és a dátum **(UTC)** mezőket a naplófájlhoz a támogatási jegyben. 

![Az új lapot megjelenítő bejelentkezési naplók](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>A dinamikus tagsági szabályok létrehozásához használt egyéni bővítménytulajdonságok továbbfejlesztett támogatása

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Csoportkezelési **termékképesség:** Együttműködés

Ezzel a frissítéssel az  Egyéni bővítménytulajdonságok lekért hivatkozására kattinthat a dinamikus felhasználóicsoport-szabályszerkesztőből, megadhatja az egyedi alkalmazásazonosítót, és megadhatja az egyéni bővítménytulajdonságok teljes listáját, amelyek a dinamikus tagsági szabályok felhasználók számára való létrehozásakor használhatók. A lista frissítve is lekért új egyéni bővítménytulajdonságokat az adott alkalmazáshoz.

További információ az egyéni bővítménytulajdonságok dinamikus tagsági szabályokhoz való használatával kapcsolatban: [Bővítménytulajdonságok és egyéni bővítménytulajdonságok](../enterprise-users/groups-dynamic-membership.md#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazásalapú feltételes hozzáféréshez

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Feltételes hozzáférési **termékképesség:** Identitásbiztonság és -védelem

A jóváhagyott ügyfélalkalmazások listájában a következő alkalmazások [vannak felsorolva:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- Microsoft To-Do

- Microsoft Stream

További információkért lásd:

- [Azure AD alkalmazásalapú feltételes hozzáférés](../conditional-access/app-based-conditional-access.md)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Új jelszó-Self-Service a Windows 7/8/8.1 zárolási képernyőről

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** SSPR **termékképesség:** Felhasználói hitelesítés

Miután beállította ezt az új funkciót, a felhasználók egy  hivatkozást látnak, amely a Windows 7, Windows 8 vagy Windows 8.1 rendszerű eszközök zárolási képernyőjén visszaállítja a jelszavukat. A hivatkozásra kattintva a felhasználó ugyanazon jelszó-visszaállítási folyamaton fog végigmenni, mint a webböngészőben.

További információ: Jelszó-visszaállítás engedélyezése [Windows 7, 8 és 8.1 rendszerről](../authentication/howto-sspr-windows.md)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Módosítási értesítés: Az engedélyezési kódok a továbbiakban nem lesznek újra felhasználhatók

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Hitelesítések (bejelentkezések) **termékképesség:** Felhasználói hitelesítés

2018. november 15-től kezdve az Azure AD nem fogadja el az alkalmazások korábban használt hitelesítési kódjait. Ez a biztonsági változás segít, hogy az Azure AD összhangban legyen az OAuth-specifikációval, és a v1 és a v2 végponton is kényszerítve lesz.

Ha az alkalmazás újra felhasználja az engedélyezési kódokat, hogy több erőforrás jogkivonatát is le tudja kapni, javasoljuk, hogy a kóddal szerezzen be egy frissítési jogkivonatot, majd ezzel a frissítési jogkivonattal szerezzen be további jogkivonatokat más erőforrásokhoz. Az engedélyezési kódok csak egyszer használhatók, de a frissítési jogkivonatok többször is felhasználhatók több erőforráson. Az OAuth-kódfolyam során egy hitelesítési kódot újra felhasználni próbáló alkalmazás hibaüzenetet invalid_grant kap.

A protokollokkal kapcsolatos változásokért tekintse meg a hitelesítéssel kapcsolatos újdonságok [teljes listáját.](../develop/reference-breaking-changes.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2018. szeptember

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2018 szeptemberében az alábbi 16, összevonási támogatást támogató új alkalmazást adtunk hozzá az alkalmazásgyűjteményhez:

[Uberflip](../saas-apps/uberflip-tutorial.md), [Comeet Recruiting Software](../saas-apps/comeetrecruitingsoftware-tutorial.md), [Workteam](../saas-apps/workteam-tutorial.md), [ArcGIS Enterprise](../saas-apps/arcgisenterprise-tutorial.md), [Nuclino,](../saas-apps/nuclino-tutorial.md) [JDA Cloud](../saas-apps/jdacloud-tutorial.md), [Snowflake](../saas-apps/snowflake-tutorial.md), NavigoCloud, [Gemma](../saas-apps/figma-tutorial.md), join.me, [ZephyrSSO](../saas-apps/zephyrsso-tutorial.md), [Silverback](../saas-apps/silverback-tutorial.md), Riverbed Xirrus EasyPass, [Rackspace SSO](../saas-apps/rackspacesso-tutorial.md), Enlyft SSO for Azure, [](../saas-apps/dmarcian-tutorial.md) SurveyMonkey, [](../saas-apps/convene-tutorial.md)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="support-for-additional-claims-transformations-methods"></a>További jogcím-transzformációs módszerek támogatása

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Enterprise Apps **Termékképesség:** SSO

Új jogcím-átalakítási metódusokat vezettünk be, a ToLower() és a ToUpper() metódusokat, amelyek saml-jogkivonatra alkalmazhatók az SAML-alapú single Sign-On Configuration (SamL-alapú egyoldalas konfiguráció) lapon. 

További információ: Az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz az [Azure AD-ban](../develop/active-directory-saml-claims-customization.md)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Frissített SAML-alapú alkalmazáskonfigurációs felhasználói felület (előzetes verzió)

**Írja be a következőt:** Módosított szolgáltatás **szolgáltatáskategória:** Enterprise Apps **Termékképesség:** SSO

A frissített SAML-alapú alkalmazáskonfigurációs felhasználói felület részeként a következőt fogja kapni:

- Frissített útmutató az SAML-alapú alkalmazások konfigurálásához.

- A konfiguráció hiányzó vagy helytelen beállításainak jobb láthatósága.

- Több e-mail-cím hozzáadása elévülési tanúsítványértesítéshez.

- Új jogcím-átalakítási metódusok, ToLower() és ToUpper() stb.

- A saját jogkivonat-aláíró tanúsítvány feltöltésének módja a vállalati alkalmazásokhoz.

- Az SAML-alkalmazások NameID formátumának beállítására, valamint a NameID értékének címtárbővítményekként való beállítására.

A frissített nézet bekapcsoláshoz  kattintson a Próbálja ki az új felhasználói élményt hivatkozásra az Egyszeri **bejelentkezés oldal tetején.** További információ: [Oktatóanyag: SAML-alapú](../manage-apps/view-applications-portal.md)egyszeri bejelentkezés konfigurálása egy alkalmazáshoz az Azure Active Directory.

---

## <a name="august-2018"></a>2018. augusztus

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Ip-Azure Active Directory ip-címtartományok változásai

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Egyéb **termékképesség:** Platform

Nagyobb IP-címtartományokat vezetünk be az Azure AD-hez, ami azt jelenti, hogy ha konfigurálta az Azure AD IP-címtartományokat a tűzfalakhoz, útválasztókhoz vagy hálózati biztonsági csoportokhoz, frissítenie kell őket. Ezt a frissítést azért fogjuk frissíteni, hogy ne módosítsa a tűzfal, útválasztó vagy hálózati biztonsági csoportok IP-címtartomány-konfigurációit, amikor az Azure AD új végpontokat ad hozzá.

A következő két hónapban a hálózati forgalom ezekre az új tartományokra költözik. A folyamatos szolgáltatás folytatásához 2018. szeptember 10. előtt hozzá kell adni ezeket a frissített értékeket az IP-címekhez:

- 20.190.128.0/18

- 40.126.0.0/18

Határozottan javasoljuk, hogy csak akkor távolítsa el a régi IP-címtartományokat, ha az összes hálózati forgalom át nem került az új tartományokra. Az áthelyezésről és a régi tartományok eltávolításának lépéseit az [Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)URL-címeit és IP-címtartományokat használhatja.

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Módosítási értesítés: Az engedélyezési kódok a továbbiakban nem lesznek újra felhasználhatók

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Hitelesítések (bejelentkezések) **termékképesség:** Felhasználói hitelesítés

2018. november 15-től kezdve az Azure AD nem fogad el korábban használt hitelesítési kódokat az alkalmazásokhoz. Ez a biztonsági módosítás segít, hogy az Azure AD összhangban legyen az OAuth-specifikációval, és a v1 és v2 végponton is kényszerítve lesz.

Ha az alkalmazás újra felhasználja az engedélyezési kódokat, hogy több erőforrás jogkivonatát is le tudja kapni, javasoljuk, hogy a kóddal szerezzen be egy frissítési jogkivonatot, majd ezzel a frissítési jogkivonattal szerezzen be további jogkivonatokat más erőforrásokhoz. Az engedélyezési kódok csak egyszer használhatók, de a frissítési jogkivonatok többször is felhasználhatók több erőforráson. Egy alkalmazás, amely megpróbál újra felhasználni egy hitelesítési kódot az OAuth-kódfolyam során, hibaüzenetet invalid_grant kap.

Ezen és a protokollokkal kapcsolatos egyéb módosításokkal kapcsolatban tekintse meg a hitelesítéssel kapcsolatos újdonságok [teljes listáját.](../develop/reference-breaking-changes.md)

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Konvergens biztonságiinformáció-kezelés önkiszolgáló jelszóhoz (SSPR) és többtényezős hitelesítéshez (MFA)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** SSPR **termékképesség:** Felhasználói hitelesítés

Az új funkció segítségével a felhasználók egyetlen helyen és felhasználói élményben kezelhetik az SSPR-hez és az MFA-hoz szükséges biztonsági adatokat (például telefonszámokat, mobilalkalmazásokat és hasonlókat); a korábbiakhoz képest, ahol ez két különböző helyen történt.

Ez az átkonvergens élmény az SSPR-t vagy MFA-t használó személyek számára is működik. Továbbá, ha a szervezet nem kényszerítI ki az MFA- vagy SSPR-regisztrációt, a személyek továbbra is regisztrálhatnak bármilyen MFA- vagy SSPR-biztonsági információt, amelyet a szervezet a Saját alkalmazások portálon.

Ez egy nyilvános előzetes verzióra való feliratkozás. A rendszergazdák (ha szükséges) bekapcsolhatják az új felhasználói élményt egy kiválasztott csoport vagy a bérlő összes felhasználója számára. Az átkonvergens felhasználói élményről további információt a ["Konvergens élmény" blogban talál.](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Új HTTP-Only cookie-k beállítása az Azure AD-alkalmazásproxy-alkalmazásokban

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Alkalmazásproxy-termékképesség:** Access Control

Az alkalmazásában található egy új, **csak HTTP-alapú cookie-k** alkalmazásproxy beállítás. Ez a beállítás további biztonságot nyújt azáltal, hogy a HTTPOnly jelzőt a HTTP-válaszfejlécben tartalmazza a alkalmazásproxy-hozzáféréshez és a munkamenet-cookie-khoz, leállítja a cookie-hoz való hozzáférést egy ügyféloldali szkriptből, és további meggátolja az olyan műveleteket, mint a cookie másolása vagy módosítása. Bár ez a jelző korábban még nem volt használatban, a cookie-kat a rendszer mindig TLS-kapcsolaton keresztül titkosította és továbbította, így védve magát a nem megfelelő módosításoktól.

Ez a beállítás nem kompatibilis az ActiveX-vezérlőket, például a Távoli asztal. Ha ilyen helyzetben van, javasoljuk, hogy kapcsolja ki ezt a beállítást.

A cookie-k használatának beállításával kapcsolatos HTTP-Only lásd: Alkalmazások közzététele [az Azure AD alkalmazásproxy.](../manage-apps/application-proxy-add-on-premises-application.md)

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) az Azure-erőforrásokhoz támogatja a felügyeleti csoportok erőforrástípusokat

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Privileged Identity Management **termékképesség:** Privileged Identity Management

Az előfizetések, erőforráscsoportok és erőforrások (például virtuális gépek, App Services és egyéb) esetében is alkalmazhatóak az azonnal használható aktiválási és hozzárendelési beállítások. Emellett bárki, aki rendszergazdai hozzáférést biztosít egy felügyeleti csoporthoz, a PIM-ban is felderítheti és kezelheti az adott erőforrást.

További információ a PIM-ről és az Azure-erőforrásokról: Azure-erőforrások felderítése és kezelése [a Privileged Identity Management](../privileged-identity-management/pim-resource-roles-discover-resources.md)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Az alkalmazás-hozzáférés (előzetes verzió) gyorsabb hozzáférést biztosít az Azure AD portálhoz

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Privileged Identity Management **termékképesség:** Privileged Identity Management

A szerepkör PIM használatával való aktiválása jelenleg több mint 10 percet is igénybe vehet, amíg az engedélyek életbe lépnek. Ha az alkalmazás-hozzáférés használatát választja, amely jelenleg nyilvános előzetes verzióban érhető el, a rendszergazdák az aktiválási kérelem befejezése után gyorsan hozzáférhetnek az Azure AD portálhoz.

Az alkalmazás-hozzáférés jelenleg csak az Azure AD-portált és az Azure-erőforrásokat támogatja. A PIM-ről és az alkalmazás-hozzáférésről a Mi az a [Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2018. augusztus

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2018 augusztusában hozzáadtunk ezt a 16 új alkalmazást összevonási támogatással az alkalmazásgyűjteményhez:

[Metbill](../saas-apps/hornbill-tutorial.md), [Bridgeline Unbound](../saas-apps/bridgelineunbound-tutorial.md), Testing Labs - Mobile [and Web Testing,](../saas-apps/saucelabs-mobileandwebtesting-tutorial.md) [Meta Networks Connector](../saas-apps/metanetworksconnector-tutorial.md), Way We [Do,](../saas-apps/waywedo-tutorial.md) [Spotinst,](../saas-apps/spotinst-tutorial.md) [ProMaster (by Inlogik)](../saas-apps/promaster-tutorial.md), SchoolBooking, [4me](../saas-apps/4me-tutorial.md), [Illetve Az](../saas-apps/dossier-tutorial.md), [N2F - Expense reports](../saas-apps/n2f-expensereports-tutorial.md), [Comm100 Live Chat](../saas-apps/comm100livechat-tutorial.md), [SafeConnect](../saas-apps/safeconnect-tutorial.md), [ZenQMS](../saas-apps/zenqms-tutorial.md), [eLuminate](../saas-apps/eluminate-tutorial.md), [Dovetale](../saas-apps/dovetale-tutorial.md).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>A Natív Tableau-támogatás mostantól elérhető az Azure AD-alkalmazásproxy

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Alkalmazásproxy **termékképessége:** Access Control

Az előhitelesítési protokollhoz OpenID Connect OAuth 2.0 Code Grant protokollra való frissítésnek megfelelően nem kell további konfigurációt használnia a Tableau és a alkalmazásproxy. Ez a protokollváltozás alkalmazásproxy támogatja a modernebb alkalmazásokat csak HTTP-átirányítások használatával, amelyeket a JavaScript- és HTML-címkék gyakran támogatnak.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Új támogatás a Google identitásszolgáltatóként való hozzáadásához a B2B-vendégfelhasználók számára a Azure Active Directory (előzetes verzió)

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2B **termékképesség:** B2B/B2C

A Google-ral való céges összevonás beállításával lehetővé teszi a meghívott Gmail-felhasználók számára, hogy meglévő Google-fiókjukkal jelentkezzenek be a megosztott alkalmazásokba és erőforrásokba anélkül, hogy személyes Microsoft-fiókot (MSA-t) vagy Azure AD-fiókot hoznak létre.

Ez egy nyilvános előzetes verzióra való feliratkozás. További információ a Google-összevonásról: A Google hozzáadása [identitásszolgáltatóként B2B vendégfelhasználók számára.](../external-identities/google-federation.md)

---

## <a name="july-2018"></a>2018. július

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Az e-Azure Active Directory kapcsolatos értesítések fejlesztései

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Egyéb **termékképességek: Identitás** életciklus-kezelése

Azure Active Directory (Azure AD) e-mailjei mostantól frissített kialakítást, valamint a küldő e-mail-címének és a feladó megjelenített nevének változásait is megjelenítik a következő szolgáltatásokból:

- Azure AD hozzáférési felülvizsgálatok
- Azure AD Connect Health
- Azure AD Identity Protection
- Azure AD Privileged Identity Management
- Vállalati alkalmazás lejáró tanúsítványával kapcsolatos értesítések
- Vállalati App Provisioning Service-értesítések

Az e-mail-értesítéseket a következő e-mail-címről és megjelenített névről küldjük el:

- E-mail-cím: azure-noreply@microsoft.com
- Megjelenítendő név: Microsoft Azure

Néhány új e-mail-kialakításra és további információra az E-mail-értesítések az [Azure AD PIM-ben.](../privileged-identity-management/pim-email-notifications.md)

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Az Azure AD-tevékenységnaplók mostantól a Azure Monitor

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termékképesség:** Figyelés & jelentéskészítéshez

Az Azure AD-tevékenységnaplók nyilvános előzetes verzióban érhetők el a Azure Monitor (az Azure platformra kiterjedő monitorozási szolgáltatása). Azure Monitor hosszú távú adatmegőrzést és zökkenőmentes integrációt biztosít a következő fejlesztések mellett:

- Hosszú távú megőrzés a naplófájlok saját Azure Storage-fiókba való átirányítása által.

- Zökkenőmentes SIEM-integráció, anélkül, hogy egyéni szkripteket kellene írnia vagy karbantartani.

- Zökkenőmentes integráció saját egyéni megoldásokkal, elemzőeszközökkel vagy incidenskezelési megoldásokkal.

Az új képességekkel kapcsolatos további információkért tekintse meg az Azure AD-tevékenységnaplókat az [Azure Monitor Diagnosticsban,](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) amely most már nyilvános előzetes verzióban érhető el, dokumentációnkban pedig a Azure Active Directory tevékenységnaplókat az [Azure Monitor (előzetes verzió)](../reports-monitoring/concept-activity-logs-azure-monitor.md)oldalon.

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Az Azure AD bejelentkezési jelentéséhez hozzáadott feltételes hozzáférési információk

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termékképesség:** Identity Security & Protection

Ez a frissítés lehetővé teszi, hogy lássa, mely szabályzatokat értékeli ki a rendszer, amikor egy felhasználó bejelentkezik a szabályzat eredményéhez. Emellett a jelentés már tartalmazza a felhasználó által használt ügyfélalkalmazás típusát, így azonosíthatja az örökölt protokollforgalmat. A jelentésbejegyzések most már egy korrelációs azonosítóra is rákeresnek, amely a felhasználó számára megjelenő hibaüzenetben található, és a megfelelő bejelentkezési kérés azonosítására és hibaelhárításra használható.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Régi hitelesítések megtekintése a bejelentkezési tevékenységnaplókban

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termékképesség:** Figyelés & jelentéskészítéshez

A bejelentkezési tevékenységnaplók Ügyfélalkalmazás mezőjének bevezetésével az ügyfelek láthatják a régi hitelesítéseket használó felhasználókat.  Az ügyfelek a Bejelentkezési Microsoft Graph API-val vagy az Azure AD portál bejelentkezési tevékenységnaplóiban keresztül férhetnek  hozzá ezekhez az információkhoz, ahol az ügyfélalkalmazás vezérlővel szűrheti az örökölt hitelesítéseket. További részletekért tekintse meg a dokumentációt.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2018. július

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2018 júliusában hozzáadtunk ezt a 16 új alkalmazást összevonási támogatással az alkalmazásgyűjteményhez:

Innovációs [központ,](../saas-apps/innovationhub-tutorial.md) [Leapsome](../saas-apps/leapsome-tutorial.md), Bizonyos rendszergazdai [SSO,](../saas-apps/certainadminsso-tutorial.md)PSUC Staging, [iPass SmartConnect,](../saas-apps/ipasssmartconnect-tutorial.md) [Screencast-O-Matic,](../saas-apps/screencast-tutorial.md)PowerStaging Unified Classroom, [Eli Onboarding](../saas-apps/elionboarding-tutorial.md), [Bomgar Remote Support](../saas-apps/bomgarremotesupport-tutorial.md), [Nimblex](../saas-apps/nimblex-tutorial.md), [Imagineer WebVision](../saas-apps/imagineerwebvision-tutorial.md), [Insight4GRC,](../saas-apps/insight4grc-tutorial.md) [SecureW2 JoinNow Connector](../saas-apps/securejoinnow-tutorial.md), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [Skills Base](../saas-apps/skillsbase-tutorial.md)

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása a Azure Active Directory [alkalmazásgyűjteményben.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>SaaS-alkalmazásintegrációk új felhasználók számára – 2018. július

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** App Provisioning **Product képesség:** külső féltől származó integráció

Az Azure AD segítségével automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását olyan SaaS-alkalmazásokban, mint a Dropbox, a Salesforce, a ServiceNow stb. 2018 júliusa óta a következő alkalmazásokhoz biztosítunk felhasználóátépítési támogatást az Azure AD alkalmazásgyűjteményében:

- [Cisco WebEx](../saas-apps/cisco-webex-provisioning-tutorial.md)

- [Bonusly](../saas-apps/bonusly-provisioning-tutorial.md)

Az Azure AD-katalógusban a felhasználóátépítést támogató összes alkalmazás listáját lásd: [SaaS-alkalmazásintegráció](../saas-apps/tutorial-list.md)a Azure Active Directory.

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync – Egyszerűbb megoldás az árva és duplikált attribútumok szinkronizálási hibáinak javítására

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Az AD Connect **termékképessége:** A & monitorozása

Azure AD Connect Health önkiszolgáló szervizelést vezet be, amely segít kiemelni és kijavítani a szinkronizálási hibákat. Ez a funkció elhárítja a duplikált attribútumok szinkronizálási hibáit, és kijavítja az Azure AD-ból árva objektumokat. Ez a diagnózis a következő előnyökkel jár:

- Leszűkíti a duplikált attribútumok szinkronizálási hibáit, és konkrét javításokat biztosít

- Javítást alkalmaz a dedikált Azure AD-forgatókönyvekhez, és egyetlen lépésben oldja fel a hibákat

- A funkció bekapcsolásához és használatához nincs szükség frissítésre vagy konfigurációra

További információ: Duplikált attribútumok szinkronizálási hibáinak [diagnosztizálása és javítása](../hybrid/how-to-connect-health-diagnose-sync-errors.md)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Az Azure AD és az MSA bejelentkezési élményének vizuális frissítései

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Azure **AD-termékképesség:** Felhasználói hitelesítés

Frissítettük a Microsoft felhasználói felületét a online szolgáltatások, például az Office 365 és az Azure felhasználói felületén. Ezzel a módosítással a képernyők kevésbé zsúfoltak és egyszerűbbek. A változással kapcsolatos további információkért tekintse meg az Azure AD bejelentkezési élmény közelgő [fejlesztései blogot.](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>A Azure AD Connect új kiadása – 2018. július

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** App Provisioning **Product képesség:** Identitás életciklus-kezelése

A legújabb kiadás a Azure AD Connect a következőket tartalmazza:

- Hibajavítások és támogatási frissítések

- Az integrációs Ping-Federate általános rendelkezésre állása

- A legújabb SQL 2012-ügyfél frissítései

További információ erről a frissítésről: [Azure AD Connect: Verzió kiadási előzményei](../hybrid/reference-connect-version-history.md)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>A használati feltételek végfelhasználói felhasználói felületének frissítései

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség:** Irányítás

A végfelhasználói felhasználói felületen frissítjük az elfogadási sztringet.

**Aktuális szöveg.** A ([tenantName] erőforrások eléréséhez el kell fogadnia a használati feltételeket.<br>**Új szöveg.** A ([tenantName] erőforrás eléréséhez el kell olvasnia a használati feltételeket.

**Aktuális szöveg:** Ha az elfogadás mellett dönt, azzal elfogadja a fenti használati feltételeket.<br>**Új szöveg:** Kattintson az Elfogadás gombra annak megerősítéséhez, hogy elolvasta és megértette a használati feltételeket.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Az átmenő hitelesítés támogatja a régi protokollokat és alkalmazásokat

**Írja be a következőt:** Módosított szolgáltatás **szolgáltatáskategória:** A hitelesítések (bejelentkezések) **termékképessége:** Felhasználói hitelesítés

Az átmenő hitelesítés mostantól támogatja a régi protokollokat és alkalmazásokat. A következő korlátozások mostantól teljes mértékben támogatottak:

- A régi Office-ügyfélalkalmazások, az Office 2010 és az Office 2013 felhasználói bejelentkezései modern hitelesítés nélkül.

- Hozzáférés a naptármegosztáshoz és az ingyenes/foglalt adatokhoz csak az Office 2010 hibrid Exchange-környezetekben.

- A felhasználók modern hitelesítés nélkül jelentkeznek be a Skype Vállalati verzió ügyfélalkalmazásaiba.

- Felhasználói bejelentkezések a PowerShell 1.0-s verziójába.

- Az Apple Készülékregisztrációs program (Apple DEP) iOS beállítási asszisztenssel.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Átkonvergens biztonságiinformáció-kezelés önkiszolgáló jelszóát állításhoz és többtényezős hitelesítéshez

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** SSPR **termékképesség:** Felhasználói hitelesítés

Ezzel az új funkcióval a felhasználók egyetlen felhasználói élményben kezelhetik az önkiszolgáló jelszó-visszaállítás (SSPR) és a többtényezős hitelesítés (MFA) biztonsági adatait (például telefonszámukat, e-mail-címüket, mobilalkalmazásukat stb.). A felhasználóknak többé nem kell ugyanazt a biztonsági információt regisztrálni az SSPR-hez és az MFA-hoz két különböző felhasználói élményben. Ez az új felhasználói élmény azokra a felhasználókra is vonatkozik, akik SSPR-hez vagy MFA-hoz is rendelkeznek.

Ha egy szervezet nem kényszerítI ki az MFA- vagy SSPR-regisztrációt, a felhasználók a portálon regisztrálják **Saját alkalmazások** adatait. Innen a felhasználók bármilyen MFA- vagy SSPR-módszert regisztrálnak.

Ez egy nyilvános előzetes verzióra való feliratkozás. A rendszergazdák (ha szükséges) bekapcsolják az új felhasználói élményt a felhasználók egy kiválasztott csoportja vagy egy bérlő összes felhasználója számára.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Az Microsoft Authenticator alkalmazással ellenőrizheti az identitását, amikor új jelszót kér

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** SSPR **termékképesség:** Felhasználói hitelesítés

Ez a funkció lehetővé teszi, hogy a nem rendszergazdák ellenőrizzék a személyazonosságukat, amikor új jelszót kérnek egy értesítés vagy egy Microsoft Authenticator (vagy bármely más hitelesítő alkalmazás) használatával. Miután a rendszergazdák bekapcsolják ezt az önkiszolgáló jelszó-visszaállítási módszert, azok a felhasználók, akik az aka.ms/mfasetup-on vagy aka.ms/setupsecurityinfo-on keresztül regisztráltak egy mobilalkalmazást, ellenőrzési módszerként használhatják a mobilalkalmazásukat a jelszavuk visszaállítása során.

A mobilalkalmazás-értesítéseket csak olyan szabályzat részeként lehet bekapcsolni, amely két módszert igényel a jelszó visszaállításához.

---

## <a name="june-2018"></a>2018. június

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Módosítási értesítés: Az Azure AD Activity Logs API-t használó alkalmazások delegált engedélyezési folyamatának biztonsági kijavítva

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** **Jelentéskészítési termék képessége:** A & jelentéskészítési szolgáltatás

Erősebb biztonsági kényszerítésünk miatt módosítanunk kellett az engedélyeket olyan alkalmazások esetében, amelyek delegált engedélyezési folyamatot használnak az [Azure AD-tevékenységnaplók API-k eléréséhez.](../reports-monitoring/concept-reporting-api.md) Ez a változás **2018. június 26-ig történik.**

Ha az alkalmazások bármelyike Azure AD-tevékenységnapló API-kat használ, kövesse az alábbi lépéseket, hogy az alkalmazás ne törjön el a módosítás után.

**Az alkalmazásengedélyek frissítése**

1. Jelentkezzen be a Azure Portal, válassza **Azure Active Directory** lehetőséget, majd válassza az **Alkalmazásregisztrációk lehetőséget.**
2. Válassza ki az Azure AD-tevékenységnaplók API-t használó alkalmazást, válassza a Beállítások **lehetőséget,** válassza a Szükséges engedélyek **lehetőséget,** majd válassza ki a **Windows Azure Active Directory** API-t.
3. A Hozzáférés engedélyezése **panel** Delegált engedélyek területén jelölje  be a Címtáradatok olvasása melletti jelölőnégyzetet, majd válassza a **Mentés lehetőséget.** 
4. Válassza **az Engedélyek megadása,** majd az **Igen lehetőséget.**

    >[!Note]
    >Az alkalmazásnak csak globális rendszergazda adhat engedélyeket.

További információt az [](../reports-monitoring/howto-configure-prerequisites-for-reporting-api.md#grant-permissions) Előfeltételek az Azure AD Reporting API eléréséhez cikk Engedélyek megadása területén talál.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>TLS-beállítások konfigurálása Azure AD-szolgáltatásokhoz való csatlakozáshoz a PCI DSS érdekében

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** N/A **termékképesség:** Platform

Transport Layer Security (TLS) egy olyan protokoll, amely adatvédelmet és adatintegritást biztosít két kommunikáló alkalmazás között, és jelenleg a legszélesebb körben alkalmazott biztonsági protokoll.

A [PCI biztonsági](https://www.pcisecuritystandards.org/) szabványügyi testülete megállapította, hogy a TLS és az SSL (SSL) korai verzióit le kell tiltani az új és biztonságosabb alkalmazás-protokollok engedélyezése érdekében, a megfelelőség **2018. június 30-tól kezdődően.** Ez a módosítás azt jelenti, hogy ha Azure AD-szolgáltatásokhoz csatlakozik, és PCI DSS szükséges, le kell tiltania a TLS 1.0-t. A TLS több verziója is elérhető, de a TLS 1.2 a legújabb verzió a Azure Active Directory számára. Javasoljuk, hogy az ügyfél/kiszolgáló és a böngésző/kiszolgáló kombinációk esetében is közvetlenül a TLS 1.2-re ássa át.

Előfordulhat, hogy az elavult böngészők nem támogatják az újabb TLS-verziókat, például a TLS 1.2-t. A böngésző által támogatott TLS-verziókért tekintse meg a [Qualys SSL Labs](https://www.ssllabs.com/) webhelyét, és kattintson **a Böngésző tesztelése elemre.** Javasoljuk, hogy frissítsen a webböngésző legújabb verziójára, és lehetőleg csak a TLS 1.2-t engedélyezze.

**A TLS 1.2 engedélyezése böngésző szerint**

- **Microsoft Edge és Internet Explorer (mindkettő az Internet Explorer)**

    1. Nyissa meg Internet Explorer, **majd válassza az Eszközök**  >  **Internetbeállítások Speciális**  >  **lehetőséget.**
    2. A Biztonság **területen** válassza a **TLS 1.2 használata** lehetőséget, majd kattintson az **OK gombra.**
    3. Zárja be az összes böngészőablakot, és indítsa Internet Explorer.

- **Google Chrome**

    1. Nyissa meg a Google *Chrome chrome://settings/, írja* be a címet a címsorba, majd nyomja le az **Enter billentyűt.**
    2. Bontsa **ki a Speciális** beállításokat, nyissa meg a Rendszer **területet,** és válassza a **Proxybeállítások megnyitása lehetőséget.**
    3. Az Internet **tulajdonságai mezőben** válassza a Speciális lapot, válassza a Biztonság **területet,** válassza a **TLS 1.2** használata lehetőséget, majd kattintson az **OK gombra.** 
    4. Zárja be az összes böngészőablakot, és indítsa újra a Google Chrome-ot.

- **Mozilla Firefox**

    1. Nyissa meg a Firefoxot, írja be az *about:config* címet a címsorba, majd nyomja le az **Enter billentyűt.**
    2. Keresse meg a *TLS* kifejezést, majd válassza ki a **security.tls.version.max bejegyzést.**
    3. Állítsa az értéket **3-ra,** hogy a böngésző a TLS 1.2-es verzióját használja, majd kattintson az **OK gombra.**

        >[!NOTE]
        >A Firefox 60.0-s verziója támogatja a TLS 1.3-as verzióját, így a security.tls.version.max értékét 4-re is **beállíthatja.**

    4. Zárja be az összes böngészőablakot, és indítsa újra a Mozilla Firefox böngészőt.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2018. június

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2018 júniusában hozzáadtunk ezt a 15 új alkalmazást összevonási támogatással az alkalmazásgyűjteményhez:

[Skytap](../saas-apps/skytap-tutorial.md) [,Inging music](../saas-apps/settlingmusic-tutorial.md), [SAML 1.1](../saas-apps/saml-tutorial.md)Token enabled LOB App , [Supermood,](../saas-apps/supermood-tutorial.md) [Autotask,](../saas-apps/autotaskendpointbackup-tutorial.md) [Endpoint Backup](../saas-apps/autotaskendpointbackup-tutorial.md), [Skyhigh Networks](../saas-apps/skyhighnetworks-tutorial.md), Smartway2, [TonicDM](../saas-apps/tonicdm-tutorial.md), [Moconavi](../saas-apps/moconavi-tutorial.md), [Zoho One](../saas-apps/zohoone-tutorial.md), [SharePoint on-premises](../saas-apps/sharepoint-on-premises-tutorial.md), [ForeSee CX Suite](../saas-apps/foreseecxsuite-tutorial.md), [Vidyard](../saas-apps/vidyard-tutorial.md) [,](../saas-apps/chronicx-tutorial.md)

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md) Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása a Azure Active Directory [alkalmazásgyűjteményben.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Az Azure AD jelszóvédelem nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Identity Protection **termékképesség:** Felhasználói hitelesítés

Az Azure AD jelszóvédelem használatával kiküszöbölheti a könnyen kitalálható jelszavakat a környezetből. Ezeknek a jelszavaknak a kiiktatása segít csökkenteni a szórásos jelszó szórásos támadással való feltörése kockázatát.

Az Azure AD jelszóvédelem a következő funkciókban nyújt segítséget:

- A szervezeti fiókok védelme az Azure AD-ban és a Windows Server Active Directory (AD) is.
- Megakadályozza, hogy a felhasználók jelszavakat használjanak a leggyakrabban használt jelszavak több mint 500 listájában, és több mint 1 millió karakter helyettesítési változatot.
- Az Azure AD jelszóvédelem felügyelete egyetlen helyről az Azure AD portálon az Azure AD-hez és a helyszíni Windows Server AD.

Az Azure AD jelszóvédelemmel kapcsolatos további információkért lásd: A rossz jelszavak kiküszöbölése [a szervezetben.](../authentication/concept-password-ban-bad.md)

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>A használati feltételek létrehozása során létrehozott új "minden vendég" feltételes hozzáférési szabályzatsablon

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség: Irányítás**

A használati feltételek létrehozása során egy új feltételes hozzáférési szabályzatsablon is létrejön a "minden vendég" és az "összes alkalmazás" számára. Ez az új szabályzatsablon alkalmazza az újonnan létrehozott toU-t, ami a vendégek létrehozási és kényszerítési folyamatát teszi egységessé.

További információ: Azure Active Directory Használati feltételek [funkció.](../conditional-access/terms-of-use.md)

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>A használati feltételek létrehozása során létrehozott új "egyéni" feltételes hozzáférési szabályzatsablon

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség: Irányítás**

A használati feltételek létrehozása során egy új "egyéni" feltételes hozzáférési szabályzatsablon is létrejön. Ezzel az új szabályzatsablonnal létrehozhatja a feltételeket, majd azonnal a Feltételes hozzáférési szabályzat létrehozása panelre lép, anélkül, hogy manuálisan kellene navigálnia a portálon.

További információ: Azure Active Directory Használati feltételek [funkció.](../conditional-access/terms-of-use.md)

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-ad-multi-factor-authentication"></a>Új és átfogó útmutató az Azure AD Multi-Factor Authentication üzembe helyezéséhez

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Egyéb **termékképesség:** Identity Security & Protection

Új, részletes útmutatót adtunk ki az Azure AD Multi-Factor Authentication (MFA) üzembe helyezéséhez a szervezetben.

Az MFA üzembe helyezési útmutatóját az Identitástelepítési útmutatók adattárban [találja](./active-directory-deployment-plans.md) a GitHubon. Ha visszajelzést szeretne küldeni az üzembe helyezési útmutatókról, használja a [Deployment Plan Feedback (Üzembe helyezési terv visszajelzése) űrlapot.](https://aka.ms/deploymentplanfeedback) Ha kérdése van az üzembe helyezési útmutatókkal kapcsolatban, lépjen kapcsolatba velünk az [IDGitDeploy elérhetőségén.](mailto:idgitdeploy@microsoft.com)

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Az Azure AD-beli delegált alkalmazáskezelési szerepkörök nyilvános előzetes verzióban állnak

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Enterprise Apps **Termékképesség:** Access Control

A rendszergazdák mostantól globális rendszergazdai szerepkör hozzárendelése nélkül delegálhat alkalmazáskezelési feladatokat. Az új szerepkörök és képességek a következőek:

- **Új szabványos Azure AD-rendszergazdai szerepkörök:**

    - **Alkalmazás-rendszergazda.** Lehetővé teszi az alkalmazások minden aspektusának kezelését, beleértve a regisztrációt, az SSO-beállításokat, az alkalmazás-hozzárendeléseket és a licencelést, az alkalmazásproxy-beállításokat és a jóváhagyást (kivéve az Azure AD-erőforrásokat).

    - **Felhőalkalmazás-rendszergazda.** Az alkalmazás-rendszergazdai képességek mindegyikét biztosítja, kivéve az alkalmazásproxyt, mivel nem biztosít helyszíni hozzáférést.

    - **Alkalmazásfejlesztő.** Akkor is lehetővé teszi alkalmazásregisztrációk  létrehozására, ha a felhasználók alkalmazásregisztrációt engedélyeznek beállítás ki van kapcsolva.

- **Tulajdonjog (alkalmazásonkénti regisztráció és vállalati alkalmazásonkénti beállítás, a csoport tulajdonosi folyamatához hasonlóan:**

    - **Alkalmazásregisztráció tulajdonosa.** Lehetővé teszi a saját tulajdonú alkalmazások regisztrációja minden aspektusának kezelését, beleértve az alkalmazásjegyzéket és a további tulajdonosok hozzáadását.

    - **Vállalati alkalmazás tulajdonosa.** Lehetővé teszi a saját vállalati alkalmazások számos aspektusának kezelését, beleértve az SSO-beállításokat, az alkalmazás-hozzárendeléseket és a hozzájárulásokat (kivéve az Azure AD-erőforrások esetében).

További információ a nyilvános előzetes verzióról: Az Azure AD-beli delegált alkalmazáskezelési szerepkörök [nyilvános előzetes verzióban vannak!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) Blog. További információ a szerepkörökről és engedélyekről: [Rendszergazdai szerepkörök hozzárendelése a Azure Active Directory.](../roles/permissions-reference.md)

---

## <a name="may-2018"></a>2018. május

### <a name="expressroute-support-changes"></a>Az ExpressRoute támogatási változásai

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Hitelesítések (bejelentkezések) **termékképessége:** Platform

A szolgáltatásként elérhető szoftverajánlatok, például a Azure Active Directory (Azure AD) úgy vannak kialakítva, hogy közvetlenül az interneten keresztül haladva, expressroute- vagy egyéb privát VPN-alagutak nélkül is működnek. Emiatt **2018.** augusztus 1-től nem támogatjuk az ExpressRoute-et az Azure AD-szolgáltatásokhoz az Azure nyilvános társviszony-létesítés és a Microsoft-társviszony-létesítés Azure-közösségei használatával. A változás által érintett szolgáltatásoknál előfordulhat, hogy az Azure AD-forgalom fokozatosan áttér az ExpressRoute-ból az internetre.

Bár módosítjuk a támogatást, azt is tudjuk, hogy bizonyos helyzetekben előfordulhat, hogy dedikált kapcsolatköröket kell használnia a hitelesítési forgalomhoz. Emiatt az Azure AD továbbra is támogatni fogja a bérlőnkénti IP-címtartományra vonatkozó korlátozásokat az ExpressRoute és az "Egyéb Office 365 Online-szolgáltatások" közösség microsoftos társviszony-létesítésben érintett szolgáltatásainak használatával. Ha a szolgáltatások hatással vannak rá, de ExpressRoute-ra van szüksége, tegye a következőket:

- **Ha Azure-beli nyilvános társviszony-létesítésen van.** Lépjen a Microsoft társviszony-létesítésbe, és regisztráljon az **Egyéb Office 365 Online-szolgáltatások (12076:5100) közösségre.** Az Azure-beli nyilvános társviszony-létesítésről a Microsoft társviszony-létesítésre való áthelyezésről további információt a Nyilvános társviszony-létesítés áthelyezése [Microsoft](../../expressroute/how-to-move-peering.md) társviszony-létesítésbe cikkben talál.

- **Ha Microsoft társviszony-létesítésen van.** Regisztráljon az **Egyéb Office 365 Online szolgáltatás (12076:5100) közösségre.** Az útválasztási követelményekkel kapcsolatos további információkért tekintse meg az ExpressRoute útválasztási követelményeivel kapcsolatos cikk [BGP-közösségek](../../expressroute/expressroute-routing.md#bgp) támogatása című szakaszát.

Ha továbbra is dedikált kapcsolatcsoportokat kell használnia, meg kell beszélnie a Microsoft-fiók csapatával, hogy hogyan szerez engedélyt az **Egyéb Office 365 Online szolgáltatás (12076:5100) közösség használatára.** Az MS Office által felügyelt felülvizsgálati tanács ellenőrzi, hogy szüksége van-e a kapcsolati körökre, és meggyőződik arról, hogy megértette azok technikai következményeit. Az Office 365-höz útvonalszűrőket létrehozni próbáló jogosulatlan előfizetések hibaüzenetet kapnak.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Microsoft Graph API-k felügyeleti forgatókönyvekhez a toU-khoz

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség:** Fejlesztői élmény

Api-kat adtunk Microsoft Graph Azure AD használati feltételek adminisztrációs műveleteihez. Létrehozhatja, frissítheti és törölheti a használati feltételek objektumát.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD több-bérlős végpont hozzáadása identitásszolgáltatóként a Azure AD B2C

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2C – Fogyasztói identitáskezelési **termékképesség:** B2B/B2C

Egyéni szabályzatok használatával mostantól hozzáadhatja az Azure AD közös végpontját identitásszolgáltatóként a Azure AD B2C. Ez lehetővé teszi, hogy az alkalmazásokba bejelentkező összes Azure AD-felhasználónak egyetlen belépési pontja legyen. További információ: Azure Active Directory B2C: Felhasználók bejelentkeztetnek egy [több-bérlős Azure AD-identitásszolgáltatóba egyéni szabályzatok használatával.](../../active-directory-b2c/identity-provider-azure-ad-multi-tenant.md)

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Belső URL-címekkel bárhonnan elérheti az alkalmazásokat Saját alkalmazások bejelentkezési bővítményünk és az Azure AD-alkalmazásproxy

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Saját alkalmazások **termékképesség:** SSO

A felhasználók a vállalati hálózaton kívüli belső URL-címeken keresztül is hozzáférhetnek az alkalmazásokhoz a Saját alkalmazások Azure AD biztonságos bejelentkezési bővítményével. Ez minden olyan alkalmazással működik, amely az Azure AD alkalmazásproxy használatával lett közzétéve minden olyan böngészőben, amely rendelkezik telepített hozzáférési panel böngészőbővítménysel. Az URL-átirányítási funkció automatikusan engedélyezve lesz, amint a felhasználó bejelentkezik a bővítménybe. A bővítmény letölthető a következő [böngészőkben: Microsoft Edge,](https://go.microsoft.com/fwlink/?linkid=845176) [Chrome,](https://go.microsoft.com/fwlink/?linkid=866367)és [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – Európai adatok európai ügyfelek számára

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Egyéb **termékképesség:** GoLocal

Az európai ügyfelek megkövetelik, hogy adataik Európában maradjanak, és ne replikálják őket az európai adatközponton kívül az adatvédelmi és az európai törvények betartása érdekében. Ez [a cikk](./active-directory-data-storage-eu.md) konkrét részleteket tartalmaz arról, hogy az európai adatközpontokban milyen identitásadatokat tárol a rendszer, valamint az európai adatközpontokban tárolt információkkal kapcsolatban.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>SaaS-alkalmazásintegrációk kiépítése új felhasználók számára – 2018. május

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** App Provisioning **Product képesség:** külső fél integrációja

Az Azure AD segítségével automatizálhatja a felhasználói identitások létrehozását, karbantartását és eltávolítását olyan SaaS-alkalmazásokban, mint a Dropbox, a Salesforce, a ServiceNow stb. 2018 májusában felhasználóátépítési támogatást adtunk hozzá a következő alkalmazásokhoz az Azure AD-alkalmazásgyűjteményben:

- [BlueJeans](../saas-apps/bluejeans-provisioning-tutorial.md)

- [Cornerstone OnDemand](../saas-apps/cornerstone-ondemand-provisioning-tutorial.md)

- [Zendesk](../saas-apps/zendesk-provisioning-tutorial.md)

Az Azure AD-katalógusban a felhasználóátépítést támogató összes alkalmazás listáját lásd: [https://aka.ms/appstutorial](../saas-apps/tutorial-list.md) .

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>A csoportok és az alkalmazás-hozzáférésEk Azure AD-hozzáférési felülvizsgálatai mostantól ismétlődő felülvizsgálatokat szolgáltatásokat kínálnak

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Hozzáférési felülvizsgálatok **termékképesség:** Irányítás

A csoportok és alkalmazások hozzáférési felülvizsgálata mostantól általánosan elérhető a P2 prémium szintű Azure AD részeként.  A rendszergazdák úgy konfigurálhatják a csoporttagságok és alkalmazás-hozzárendelések hozzáférési felülvizsgálatát, hogy rendszeres időközönként, például havonta vagy negyedévente automatikusan ismétlődnek.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Az Azure AD-tevékenységnaplók (bejelentkezések és auditálások) már elérhetők az MS Graphon keresztül

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termék képesség:** A jelentéskészítés & monitorozása

Az Azure AD-tevékenységnaplók, amelyek bejelentkezési és auditnaplókat is tartalmaznak, mostantól a Microsoft Graph API-n keresztül érhetők el. A naplók eléréséhez két végpontot elérhetővé Microsoft Graph API-n keresztül. Az első [lépésekhez tekintse](../reports-monitoring/concept-reporting-api.md) meg dokumentumainkban az Azure AD Reporting API-k programozott hozzáférését.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>A B2B beváltási élmény fejlesztései és elhagyják a szervezetet

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2B **termékképesség:** B2B/B2C

**Időben beváltható:** Miután megoszt egy erőforrást egy vendégfelhasználóval a B2B API használatával, nem kell különleges meghívó e-mailt küldenie. A legtöbb esetben a vendégfelhasználó hozzáférhet az erőforráshoz, és a rendszer az érvényesítési élményben végigveszi az adott időben. Nincs több hatása a kihagyott e-mailek miatt. Többé nem kell megkérdezni a vendégfelhasználókat, hogy "Kattintott arra az érvényesítési hivatkozásra, amit a rendszer küldött Önnek?". Ez azt jelenti, hogy ha az SPO a meghívókezelőt használja – a felhőalapú mellékletek az érvényesítés bármely állapotában ugyanaz a canonical URL-cím lehet az összes felhasználó (belső és külső) számára.

**Modern érvényesítési élmény:** Nincs több felosztási képernyő beváltási kezdőlapja. A felhasználók a meghívó szervezet adatvédelmi nyilatkozatában modern hozzájárulási élményt fognak látni, akárcsak a külső alkalmazások esetében.

**A vendégfelhasználók elhagyhatja a szervezetet:** Miután a felhasználó és a szervezet kapcsolata véget ért, önkiszolgálóan elhagyhatja a szervezetet. Többé nem kell meghívni a meghívó szervezet rendszergazdáját a "eltávolításra", és nem kell több támogatási jegyet kihívni.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Az Azure AD alkalmazásgyűjteményében elérhető új összevont alkalmazások – 2018. május

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2018 májusában hozzáadtunk ezt a 18 új alkalmazást összevonási támogatással az alkalmazásgyűjteményünkbe:

[AwardSpring](../saas-apps/awardspring-tutorial.md), Infogix Data3Sixty Govern, [Yodeck](../saas-apps/infogix-tutorial.md), [Jamf Pro,](../saas-apps/jamfprosamlconnector-tutorial.md) [KnowledgeOwl,](../saas-apps/knowledgeowl-tutorial.md) [Envi MMIS](../saas-apps/envimmis-tutorial.md), [LaunchDarkly](../saas-apps/launchdarkly-tutorial.md), [Adobe Captivate Prime,](../saas-apps/adobecaptivateprime-tutorial.md) [Montage Online](../saas-apps/montageonline-tutorial.md), まなび[következő: Online ,](../saas-apps/manabipocket-tutorial.md)OpenReel, [Arc Publishing - SSO](../saas-apps/arc-tutorial.md), [PlanGrid](../saas-apps/plangrid-tutorial.md), [iWellnessNow](../saas-apps/iwellnessnow-tutorial.md), [Proxyclick](../saas-apps/proxyclick-tutorial.md), [Riskware](../saas-apps/riskware-tutorial.md), [Reviewsnap](../saas-apps/reviewsnap-tutorial.md) [](../saas-apps/flock-tutorial.md)

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md)

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása a Azure Active Directory [alkalmazásgyűjteményben.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Új részletes üzembe helyezési útmutatók a Azure Active Directory

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Egyéb **termékképesség:** Címtár

Új, részletes útmutató az Azure Active Directory (Azure AD) üzembe helyezéséhez, beleértve az önkiszolgáló jelszóát állítást (SSPR), az egyszeri bejelentkezést (SSO), a feltételes hozzáférést, az alkalmazásproxyt, a felhasználóáttelepítést, az Active Directory összevonási szolgáltatások (AD FS)-t (ADFS) átmenő hitelesítésre (PTA) és az ADFS-ről jelszó kivonatszinkronizálásra (PHS).

Az üzembe helyezési útmutatók megtekintéséhez tekintse meg az [identitástelepítési útmutatók](./active-directory-deployment-plans.md) tárházát a GitHubon. Ha visszajelzést szeretne küldeni az üzembe helyezési útmutatókról, használja a [Deployment Plan Feedback (Üzembe helyezési terv visszajelzése) űrlapot.](https://aka.ms/deploymentplanfeedback) Ha kérdése van az üzembe helyezési útmutatókkal kapcsolatban, lépjen kapcsolatba velünk az [IDGitDeploy elérhetőségén.](mailto:idgitdeploy@microsoft.com)

---

### <a name="enterprise-applications-search---load-more-apps"></a>Vállalati alkalmazások keresése – További alkalmazások betöltése

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Enterprise Apps **Termékképesség:** SSO

Nem találja az alkalmazásait/szolgáltatásait? Lehetővé teszi, hogy több alkalmazást töltsön be a vállalati alkalmazásokba az összes alkalmazáslistán. Alapértelmezés szerint 20 alkalmazást mutatunk be. A további alkalmazások megtekintéséhez kattintson a További **adatok** betöltése elemre.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>Az AADConnect májusi kiadása tartalmazza a PingFederate-integráció nyilvános előzetesét, a fontos biztonsági frissítéseket, számos hibajavítást és új, nagyszerű új hibaelhárítási eszközöket.

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** AD Connect **termékképesség:** Identitás-életciklus kezelése

Az AADConnect májusi kiadása tartalmazza a PingFederate-integráció nyilvános előzetesét, a fontos biztonsági frissítéseket, számos hibajavítást és új, nagyszerű új hibaelhárítási eszközöket. A kibocsátási megjegyzéseket itt [találja:](../hybrid/reference-connect-version-history.md).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD hozzáférési felülvizsgálatok: automatikus alkalmazás

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Hozzáférési **felülvizsgálatok termékképessége:** Irányítás

A csoportok és alkalmazások hozzáférési felülvizsgálatai mostantól általánosan elérhetők a P2 prémium szintű Azure AD részeként. A rendszergazda beállíthatja, hogy automatikusan alkalmazza a felülvizsgáló módosításait az adott csoportra vagy alkalmazásra a hozzáférési felülvizsgálat befejezésekor. A rendszergazda azt is megadhatja, hogy mi történjen a felhasználó folyamatos hozzáférésével, ha a felülvizsgálók nem válaszoltak, nem távolodtak el a hozzáféréstől, nem tartják meg a hozzáférést, vagy rendszerjavaslatokat tehetnek.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Az azonosító jogkivonatok már nem lesznek visszaadva az új alkalmazások response_mode lekérdezési módszerének használatával.

**Írja be a következőt:** Módosított szolgáltatás **szolgáltatáskategória:** A hitelesítések (bejelentkezések) **termékképessége:** Felhasználói hitelesítés

A 2018. április 25-én vagy azt követően  létrehozott alkalmazások nem kérhetnek id_token **lekérdezési** response_mode.  Ez az Azure AD-t az OIDC-specifikációkkal együtt tartalmazza, és segít csökkenteni az alkalmazások támadási felületét.  A 2018. április 25. előtt létrehozott  alkalmazások számára nem lesz letiltva a response_mode lekérdezési response_type **id_token.**  Az Azure AD-től id_token visszaadott hiba az **AADSTS70007: A "query"** nem támogatott "response_mode" érték jogkivonat kérése esetén.

A **töredék** **és form_post** response_modes működnek – új alkalmazásobjektumok létrehozásakor (például alkalmazásproxy-használat esetén) az új alkalmazás létrehozása előtt győződjön meg arról, hogy az alábbi response_modes valamelyikét használja.

---

## <a name="april-2018"></a>2018. április

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C hozzáférési jogkivonat ga.

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2C – Fogyasztói identitáskezelési **termékképesség:** B2B/B2C

Most már hozzáférhet a webes API-khoz, Azure AD B2C hozzáférési jogkivonatokkal. A funkció nyilvános előzetes verzióról GA-verzióra költözik. Továbbfejlesztettük a felhasználói felület felhasználói felületét Azure AD B2C alkalmazások és webes API-k konfigurálásán, és egyéb kisebb fejlesztések is történtek.

További információ: [Azure AD B2C: Hozzáférési jogkivonatok kérése.](../../active-directory-b2c/access-tokens.md)

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Egyszeri bejelentkezés konfigurációjának tesztelése SAML-alapú alkalmazásokhoz

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Enterprise Apps **Termékképesség:** SSO

SAML-alapú SSO-alkalmazások konfigurálásakor tesztelni tudja az integrációt a konfigurációs oldalon. Ha hibát tapasztal a bejelentkezés során, a hibát a tesztelési élményben használhatja, az Azure AD pedig megoldási lépéseket biztosít az adott probléma megoldásához.

További információkért lásd:

- [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](../manage-apps/view-applications-portal.md)
- [SAML-alapú egyszeri bejelentkezés hibakeresése a Azure Active Directory](../manage-apps/debug-saml-sso-issues.md)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Az Azure AD használati feltételei mostantól felhasználónkénti jelentéskészítéssel is rendelkezik

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Használati feltételek **termékképesség:** Megfelelőség

A rendszergazdák mostantól kiválaszthatják az adott tou-t, és láthatják az összes felhasználót, aki hozzájárult ehhez, és hogy milyen dátum/idő volt az adott időpont.

További információkért lásd az [Azure AD használati feltételek funkcióját.](../conditional-access/terms-of-use.md)

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: Kockázatos IP-cím AD FS extranetes zárolás elleni védelemhez

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Egyéb **termékképesség: Figyelés** & jelentéskészítéshez

A Connect Health mostantól támogatja a sikertelen U/P-bejelentkezések küszöbértékét túllépő IP-címek észlelését óránként vagy naponta. A szolgáltatás a következő képességeket biztosítja:

- Átfogó jelentés, amely az IP-címet és a sikertelen bejelentkezések számát mutatja óránként/naponta, testre szabható küszöbértékekkel.
- E-mail-alapú riasztások, amelyek azt mutatják, ha egy adott IP-cím túllépte a sikertelen U/P-bejelentkezések küszöbértékét óránként/naponta.
- Letöltési lehetőség az adatok részletes elemzéséhez

További információ: [Kockázatos IP-jelentés.](../hybrid/how-to-connect-health-adfs.md)

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Egyszerű alkalmazás-konfiguráció metaadatfájllal vagy URL-címekkel

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Enterprise Apps **Termékképesség:** SSO

A Vállalati alkalmazások lapon a rendszergazdák SAML-metaadatfájlt tölthetnek fel, amely SAML-alapú bejelentkezést konfigurál az Azure AD-katalógushoz és a nem katalógusbeli alkalmazásokhoz.

Emellett az Azure AD alkalmazás-összevonási metaadatok URL-címével konfigurálhatja az SSO-t a megcélzott alkalmazással.

További információ: [Configuring single sign-on to applications](../manage-apps/view-applications-portal.md)that are not in the Azure Active Directory application gallery (Az alkalmazás-katalógusban nem Azure Active Directory alkalmazásokba való egyszeri bejelentkezés konfigurálása).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Általánosan elérhető Használati feltételek Azure AD-fiók

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség:** Megfelelőség


Az Azure AD használati feltételei átkerültek a nyilvános előzetes verzióról az általánosan elérhetőre.

További információkért lásd az [Azure AD használati feltételek funkcióját.](../conditional-access/terms-of-use.md)

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Adott szervezetek B2B-felhasználók felé irányuló meghívásainak engedélyezése vagy letiltása

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2B **termékképesség:** B2B/B2C


Mostantól megadhatja, hogy mely partnerszervezeteket szeretné megosztani és együttműködni az Azure AD-B2B-együttműködés. Ehhez létrehozhatja adott engedélyező vagy megtagadási tartományok listáját. Ha egy tartományt letilt ezekkel a képességekkel, az alkalmazottak többé nem küldhetnek meghívókat az adott tartományban található személyeknek.

Ez segít szabályozni az erőforrásokhoz való hozzáférést, miközben zökkenőmentes felhasználói élményt biztosít a jóváhagyott felhasználók számára.

Ez az B2B-együttműködés-funkció minden Azure Active Directory prémium szintű Azure AD-ügyfél számára elérhető, és olyan funkciókkal együtt használható, mint a feltételes hozzáférés és az identitásvédelem, így részletesebben szabályozható, hogy a külső üzleti felhasználók mikor és hogyan jelentkeznek be és kapnak hozzáférést.

További információ: [Adott szervezetektől származó B2B-felhasználók meghívásának engedélyezése vagy blokkolása.](../external-identities/allow-deny-list.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások, amelyek az Azure AD alkalmazásgyűjteményében érhetők el

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2018 áprilisában hozzáadtunk ezt a 13 új alkalmazást összevonási támogatással az alkalmazásgyűjteményünkbe:

Feltétel HCM, [FiscalNote](../saas-apps/fiscalnote-tutorial.md), [Secret Server (On-Premises)](../saas-apps/secretserver-on-premises-tutorial.md), dinamikus [jel](../saas-apps/dynamicsignal-tutorial.md), [mindWireless](../saas-apps/mindwireless-tutorial.md), [OrgChart Now](../saas-apps/orgchartnow-tutorial.md), [Ziflow](../saas-apps/ziflow-tutorial.md), [AppNeta Performance Monitor](../saas-apps/appneta-tutorial.md), [Elium,](../saas-apps/elium-tutorial.md) [Fluxx Labs,](../saas-apps/fluxxlabs-tutorial.md) [Cisco Cloud,](../saas-apps/ciscocloud-tutorial.md)Shelf, [SafetyNet](../saas-apps/safetynet-tutorial.md)

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md)

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az alkalmazás [Azure Active Directory katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>B2B-felhasználók hozzáférésének megadása az Azure AD-ben a helyszíni alkalmazásokhoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** B2B **termékképesség:** B2B/B2C

Olyan szervezetként, amely Azure Active Directory (Azure AD) B2B együttműködési képességeket használ a vendégfelhasználók partnerszervezetekből az Azure AD-be való meghívására, mostantól hozzáférést nyújthat ezeknek a B2B-felhasználóknak a helyszíni alkalmazásokhoz. Ezek a helyszíni alkalmazások SAML-alapú hitelesítést vagy integrált Windows-hitelesítés (IWA) kerberos által korlátozott delegálást (KCD) használnak.

További információ: Hozzáférés megadása a helyszíni alkalmazásokhoz [B2B-felhasználók számára az Azure AD-ben.](../external-identities/hybrid-cloud-to-on-premises.md)

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>SSO-integrációs oktatóanyagok le Azure Marketplace

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Egyéb **termékképesség:** külső féltől származó integráció

Ha a Azure Marketplace felsorolt [](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) alkalmazások támogatják az SAML-alapú egyszeri bejelentkezést, a Get **it now** (Lekért most) elemre kattintva megjelenik az alkalmazáshoz társított integrációs oktatóanyag.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Gyorsabb teljesítmény az Azure AD automatikus felhasználóátépítésében SaaS-alkalmazásokban

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** App Provisioning **Product képesség:** külső féltől származó integráció

Korábban az SaaS-alkalmazások Azure Active Directory-felhasználóátadási összekötőit (például Salesforce, ServiceNow és Box) használó ügyfelek lassú teljesítményt tapasztalhatnak, ha az Azure AD-bérlőjük több mint 100 000 összevont felhasználót és csoportot tartalmaz, és felhasználói és csoport-hozzárendelésekkel határozzák meg, hogy mely felhasználókat kell kiépítenünk.

2018. április 2-án jelentős teljesítménybeli fejlesztéseket helyezett üzembe az Azure AD kiépítési szolgáltatásban, amely jelentősen csökkenti az Azure Active Directory és a cél SaaS-alkalmazások közötti kezdeti szinkronizáláshoz szükséges időt.

Ennek eredményeképpen számos olyan ügyfél, aki kezdeti szinkronizálást tartott olyan alkalmazásokkal, amelyek sok napig vagy soha nem fejeződtek be, most percek vagy órák alatt befejeződnek.

További információ: Mi történik az [üzembe építés során?](../..//active-directory/app-provisioning/how-provisioning-works.md)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Új jelszó önkiszolgáló Windows 10 a hibrid Azure AD-hez csatlakozott gépek zárolási képernyőjén

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Önkiszolgáló jelszó-visszaállítási **termék képesség:** Felhasználói hitelesítés

Frissítettük a Windows 10 SSPR funkciót, hogy tartalmazza a hibrid Azure AD-hez csatlakozott gépek támogatását. Ez a funkció a Windows 10 RS4-ben lehetővé teszi, hogy a felhasználók visszaállítsa a jelszavukat a Windows 10 képernyőről. Az önkiszolgáló jelszóát állításhoz engedélyezett és regisztrált felhasználók is igénybe tudják venni ezt a funkciót.

További információkért lásd: [Új Azure AD-jelszó visszaállítása a bejelentkezési képernyőről.](../authentication/howto-sspr-windows.md)

---

## <a name="march-2018"></a>2018. március

### <a name="certificate-expire-notification"></a>Tanúsítvány lejárati értesítése

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** Enterprise Apps **Termékképesség:** SSO

Az Azure AD értesítést küld, ha egy katalógusbeli vagy nem katalógusbeli alkalmazás tanúsítványa hamarosan lejár.

Egyes felhasználók nem kapták meg az SAML-alapú egyszeri bejelentkezéshez konfigurált vállalati alkalmazások értesítését. A probléma megoldódott. Az Azure AD értesítést küld a 7, 30 és 60 nap múlva lejáró tanúsítványokról. Ezt az eseményt az auditnaplókban láthatja.

További információkért lásd:

- [Tanúsítványok kezelése összevont egyszeri bejelentkezéshez a Azure Active Directory](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon](../reports-monitoring/concept-audit-logs.md)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter- és GitHub-identitásszolgáltatók a Azure AD B2C

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** B2C – Fogyasztói identitáskezelési **termékképesség:** B2B/B2C

Most már hozzáadhatja a Twittert vagy a GitHubot identitásszolgáltatóként a Azure AD B2C. A Twitter nyilvános előzetes verzióról GA-kiadásra költözik. A GitHub nyilvános előzetes verzióban érhető el.

További információ: [Mi az az Azure AD B2B-együttműködés?](../external-identities/what-is-b2b.md).

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Böngésző-hozzáférés korlátozása Intune Managed Browser Azure AD alkalmazásalapú feltételes hozzáféréssel iOS és Android rendszeren

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Feltételes hozzáférési **termékképesség:** Identity Security & Protection

**Most már nyilvános előzetes verzióban!**

**Intune Managed Browser SSO:** Az alkalmazottak egyszeri bejelentkezést használhatnak a natív ügyfeleken (például a Microsoft Outlookon) és Intune Managed Browser Azure AD-hez csatlakoztatott összes alkalmazáshoz.

**Intune Managed Browser feltételes hozzáférés támogatása:** Mostantól alkalmazásalapú feltételes hozzáférési szabályzatok használatával megkövetelheti az alkalmazottaktól az Intune Managed Browser használatát.

Erről a blogbejegyzésünkben [olvashat bővebben.](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)

További információkért lásd:

- [Alkalmazásalapú feltételes hozzáférés beállítása](../conditional-access/app-based-conditional-access.md)

- [Felügyeltböngésző-szabályzatok konfigurálása](/mem/intune/apps/manage-microsoft-edge)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>Alkalmazásproxy-parancsmagok a PowerShell ga ga modulban

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Alkalmazásproxy **termékképessége:** Access Control

A alkalmazásproxy parancsmagok támogatása mostantól a PowerShell GA modulban található! Ehhez frissítenie kell a PowerShell-modulokat – ha egy évnél hosszabb időt fog használni, előfordulhat, hogy egyes parancsmagok nem működnek tovább.

További információ: [AzureAD.](/powershell/module/Azuread/)

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Az Office 365 natív ügyfeleit közvetlen egyszeri bejelentkezés támogatja nem interaktív protokoll használatával

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Hitelesítések (bejelentkezések) **Termékképesség:** Felhasználói hitelesítés

A natív Office 365-ügyfeleket (16.0.8730.xxxx és újabb verziók) használó felhasználó csendes bejelentkezési élményt kap a közvetlen egyszeri bejelentkezés használatával. Ezt a támogatást egy nem interaktív protokoll (WS-Trust) az Azure AD-hez való adása biztosítja.

További információkért lásd: Hogyan működik a közvetlen egyszeri bejelentkezést [(SSO)](../hybrid/how-to-connect-sso-how-it-works.md#how-does-sign-in-on-a-native-client-with-seamless-sso-work) is lehetővé tő natív ügyfélre való bejelentkezés?

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>A felhasználók zökkenőmentes egyszeri bejelentkezéssel csendes bejelentkezési élményt tapasztalnak, ha egy alkalmazás bejelentkezési kéréseket küld az Azure AD bérlői végpontjainak

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Hitelesítések (bejelentkezések) **Termékképesség:** Felhasználói hitelesítés

A felhasználók zökkenőmentes egyszeri bejelentkezéssel csendes bejelentkezési élményt tapasztalnak, ha egy alkalmazás (például ) bejelentkezési kéréseket küld az Azure AD bérlői végpontjainak – vagyis – az Azure AD közös végpontja `https://contoso.sharepoint.com` `https://login.microsoftonline.com/contoso.com/<..>` ( ) `https://login.microsoftonline.com/<tenant_ID>/<..>` `https://login.microsoftonline.com/common/<...>` helyett.

További információkért lásd a [Azure Active Directory egyszeri bejelentkezést.](../hybrid/how-to-connect-sso.md)

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>A felhasználók intranetes zónabeállításaihoz a korábbi két URL-cím helyett csak egy Azure AD URL-címet kell hozzáadnia a közvetlen egyszeri bejelentkezés (SSO) bevezetéshez

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Hitelesítések (bejelentkezések) **termékképesség:** Felhasználói hitelesítés

Ha zökkenőmentes egyszeri bejelentkezést szeretne üzembe adni a felhasználók számára, csak egy Azure AD URL-címet kell hozzáadnia a felhasználók intranetes zónabeállításához a következő csoportházirend használatával: `https://autologon.microsoftazuread-sso.com` Active Directory. Korábban az ügyfeleknek két URL-t kellett hozzáadniuk.

További információkért lásd a [Azure Active Directory egyszeri bejelentkezést.](../hybrid/how-to-connect-sso.md)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások, amelyek az Azure AD alkalmazásgyűjteményében érhetők el

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **termékképessége:** külső fél integrációja

2018 márciusában hozzáadtunk ezt a 15 új, összevonási támogatást támogató alkalmazást az alkalmazásgyűjteményünkbe:

[Boxcryptor](../saas-apps/boxcryptor-tutorial.md), [CylancePROTECT](../saas-apps/cylanceprotect-tutorial.md), Wrike, [SignalFx](../saas-apps/signalfx-tutorial.md), Assistant by FirstAgenda,OrdiniOne , Vtiger CRM, inwink, [Amplitude](../saas-apps/amplitude-tutorial.md), [Spacio](../saas-apps/spacio-tutorial.md), [ContractWorks](../saas-apps/contractworks-tutorial.md) [,Ordinsin](../saas-apps/bersin-tutorial.md), [Mercell](../saas-apps/mercell-tutorial.md), [Trisotech Digital Enterprise Server](../saas-apps/trisotechdigitalenterpriseserver-tutorial.md), [Qumu Cloud](../saas-apps/qumucloud-tutorial.md). [](../saas-apps/yardione-tutorial.md)

További információ az alkalmazásokról: [SaaS-alkalmazás integrálása a Azure Active Directory.](../saas-apps/tutorial-list.md)

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az alkalmazás [Azure Active Directory katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="pim-for-azure-resources-is-generally-available"></a>Általánosan elérhető a PIM az Azure-erőforrásokhoz

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Privileged Identity Management **termékképesség:** Privileged Identity Management

Ha a Azure AD Privileged Identity Management-t használja a címtárbeli szerepkörökhöz, mostantól használhatja a PIM időhöz kötött hozzáférési és hozzárendelési képességeit olyan Azure-erőforrás-szerepkörökhöz, mint az előfizetések, az erőforráscsoportok, az Virtual Machines és a Azure Resource Manager. Kényszerítsen többtényezős hitelesítést a szerepkörök időben történő aktiválása során, és az aktiválásokat a jóváhagyott változási időszakokkal koordinálva ütemezi. Emellett ez a kiadás olyan fejlesztéseket is tartalmaz, amelyek a nyilvános előzetes verzióban nem érhetők el, beleértve a frissített felhasználói felületet, a jóváhagyási munkafolyamatokat, valamint a hamarosan lejáró szerepkörök kiterjesztésének és a lejárt szerepkörök megújításának képességét.

További információ: [PIM for Azure resources (Preview) (PIM azure-erőforrásokhoz (előzetes verzió)](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Választható jogcímek hozzáadása az alkalmazás-jogkivonathoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Hitelesítések (bejelentkezések) **Termékképesség:** Felhasználói hitelesítés

Az Azure AD-alkalmazás mostantól egyéni vagy választható jogcímeket kérhet JWT-kben vagy SAML-jogkivonatban.  Ezek a felhasználóval vagy bérlővel kapcsolatos jogcímek, amelyek a méret vagy az alkalmazhatóság korlátai miatt alapértelmezés szerint nem szerepelnek a jogkivonatban.  Ez jelenleg nyilvános előzetes verzióban érhető el az 1.0-s és 2.0-s verziójú végponton lévő Azure AD-alkalmazásokhoz.  A dokumentációból tájékozódhat arról, hogy milyen jogcímeket lehet hozzáadni, és hogyan szerkesztheti az alkalmazásjegyzéket a kérésük érdekében.

További információ: Választható [jogcímek az Azure AD-ban.](../develop/active-directory-optional-claims.md)

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Az Azure AD támogatja a PKCE-t a biztonságosabb OAuth-folyamatokhoz

**Írja be a következőt:** Új szolgáltatás **szolgáltatáskategória:** Hitelesítések (bejelentkezések) **Termékképesség:** Felhasználói hitelesítés

Az Azure AD-dokumentumok frissítve vannak, hogy figyelembe vegye a PKCE támogatását, amely biztonságosabb kommunikációt tesz lehetővé az OAuth 2.0 engedélyezési kódengedélyezési folyamat során.  Az S256 és code_challenges is támogatott az 1.0-s és 2.0-s verziós végpontok esetén.

További információ: [Engedélyezési kód kérése.](../develop/v2-oauth2-auth-code-flow.md#request-an-authorization-code)

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>A Workday api-ban elérhető összes felhasználói attribútumérték üzembe Get_Workers támogatása

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** App Provisioning **Product képesség:** külső féltől származó integráció

A Workdayből a Active Directory-ba irányuló bejövő kiépítés nyilvános előzetes verziója és az Azure AD mostantól támogatja a Workday Get_Workers API-ban elérhető összes attribútumérték kinyerését és Get_Workers kinyerését. Ez több száz további standard és egyéni attribútumot támogat a Workday bejövő kiépítési összekötő kezdeti verzióján kívül.

További információ: A Workday felhasználói attribútumok [listájának testreszabása](../saas-apps/workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>Csoporttagság módosítása dinamikusról statikusra és fordítva

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Csoportkezelési **termékképesség:** Együttműködés

A csoporttagság kezelése megváltoztatható. Ez akkor hasznos, ha ugyanazt a csoportnevet és azonosítót szeretné megtartani a rendszerben, így a csoportra mutató meglévő hivatkozások továbbra is érvényesek; Ha új csoportot hozna létre, frissítenie kellene ezeket a hivatkozásokat.
Frissítettük az Azure AD felügyeleti központot, hogy támogassa ezt a funkciót. Az ügyfelek mostantól dinamikus tagságról hozzárendelt tagságra és fordítva is átalakítják a meglévő csoportokat. A meglévő PowerShell-parancsmagok is elérhetők.

További információ: Dinamikus [tagsági szabályok csoportokhoz](../enterprise-users/groups-dynamic-membership.md) a Azure Active Directory

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Továbbfejlesztett kijelentkező viselkedés közvetlen egyszeri bejelentkezéssel

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** A hitelesítések (bejelentkezések) **termékképessége:** Felhasználói hitelesítés

Korábban, még ha a felhasználók kifejezetten kijelentkeztek is az Azure AD által védett alkalmazásból, automatikusan vissza lesznek jelentkezve közvetlen egyszeri bejelentkezéssel, ha újra megpróbálnak hozzáférni egy Azure AD-alkalmazáshoz a vállalati hálózatukon belül a tartományhoz csatlakozott eszközeikről. Ezzel a módosítással a kijelentkezás támogatott.  Ez lehetővé teszi, hogy a felhasználók ugyanazt vagy eltérő Azure AD-fiókot választják, amelybe újra be kell jelentkezniük ahelyett, hogy a közvetlen egyszeri bejelentkezéssel automatikusan bejelentkeznek.

További információ: közvetlen [Azure Active Directory egyszeri bejelentkezés](../hybrid/how-to-connect-sso.md)

---

### <a name="application-proxy-connector-version-154020-released"></a>alkalmazásproxy Connector 1.5.402.0-s verziója kiadva

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** **Alkalmazásproxy-termékképesség:** Identity Security & Protection

Ezt az összekötőverziót fokozatosan vezetjük be novemberig. Az összekötő új verziója a következő módosításokat tartalmazza:

- Az összekötő most tartományszintű cookie-kat állít be az altartomány szintjén. Ez zökkenőmentesebb SSO-élményt biztosít, és elkerüli a redundáns hitelesítési kéréseket.
- Darabolásos kódolási kérések támogatása
- Továbbfejlesztett összekötő-állapotfigyelés
- Számos hibajavítás és stabilitási fejlesztés

További információ: [Az Azure AD alkalmazásproxy összekötői.](../manage-apps/application-proxy-connectors.md)

---

## <a name="february-2018"></a>2018. február

### <a name="improved-navigation-for-managing-users-and-groups"></a>Továbbfejlesztett navigáció a felhasználók és csoportok kezeléséhez

**Írja be a következőt:** Terv a **szolgáltatáskategória változására:** Címtárkezelési **termékképesség:** Címtár

A felhasználók és csoportok felügyeletének navigációs élménye zökkenőmentesen leegyszerúsult. Mostantól közvetlenül az összes felhasználó listájára navigálhat a címtár áttekintésében, és könnyebben hozzáférhet a törölt felhasználók listájához. A címtár áttekintésében közvetlenül az összes csoport listájára is navigálhat, így könnyebben hozzáférhet a csoportkezelési beállításokhoz. Emellett a címtáráttekintő oldalon felhasználóra, csoportra, vállalati alkalmazásra vagy alkalmazásregisztrációra is rákereshet.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>A 21Vianet (Microsoft Azure) által üzemeltetett Azure China 21Vianet rendelkezésre állása

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Azure Stack **termékképesség:** Jelentéskészítés & monitorozása

Az Azure AD-tevékenységnapló-jelentések mostantól Microsoft Azure 21Vianet- (Azure China 21Vianet)-példány által üzemeltetett alkalmazásokban. A következő naplókat tartalmazza:

- **Bejelentkezési tevékenységnaplók**  – A bérlőhöz társított összes bejelentkezési naplót tartalmazza.

- **Önkiszolgáló jelszónaplók** – Az összes SSPR-auditnaplót tartalmazza.

- **Címtárkezelési auditnaplók** – A címtárkezeléshez kapcsolódó összes auditnaplót tartalmazza, például a Felhasználókezelést, az Alkalmazáskezelést és egyebeket.

Ezekkel a naplókval betekintést nyerhet a környezete folyamatába. A megadott adatokkal a következőket teheti:

- Határozza meg, hogy a felhasználók hogyan használják az alkalmazásokat és a szolgáltatásokat.

- Elháríthatja a problémákat, amelyek megakadályozzák, hogy a felhasználók végezzék a munkájukat.

A jelentések használatával kapcsolatos további információkért lásd: Azure Active Directory [jelentéskészítés.](../reports-monitoring/overview-reports.md)

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>A "Jelentésolvasó" szerepkör (nem rendszergazdai szerepkör) használata az Azure AD-tevékenységjelentések megtekintéséhez

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Jelentéskészítési termékképesség:** Figyelés & jelentéskészítéshez

Az ügyfelek visszajelzései részeként, amelyek lehetővé teszik a nem rendszergazdai szerepkörök számára az Azure AD-tevékenységnaplókhoz való hozzáférést, lehetővé teszi a "Jelentésolvasó" szerepkörű felhasználók számára a bejelentkezési és naplózási tevékenységekhez való hozzáférést az Azure Portal-ban, valamint az Microsoft Graph API használatát.

A jelentések használatával kapcsolatos további információkért lásd: Azure Active Directory [jelentéskészítés.](../reports-monitoring/overview-reports.md)

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Az EmployeeID jogcím elérhető felhasználói attribútumként és felhasználói azonosítóként

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Enterprise Apps **Termékképesség:** SSO

Az **EmployeeID felhasználóazonosítóként** és Felhasználói attribútumként konfigurálható tagfelhasználók és B2B vendégek számára az SAML-alapú bejelentkezési alkalmazásokban a Vállalati alkalmazás felhasználói felületén.

További információ: Az SAML-jogkivonatban kiadott jogcímek testreszabása vállalati alkalmazásokhoz [a Azure Active Directory.](../develop/active-directory-saml-claims-customization.md)

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Egyszerűsített alkalmazáskezelés helyettesítő karakterek használatával az Azure AD-alkalmazásproxy

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Alkalmazásproxy-termékképesség:** Felhasználóhitelesítés

Az alkalmazások üzembe helyezésének egyszerűsítése és az adminisztratív terhelés csökkentése érdekében mostantól támogatjuk az alkalmazások helyettesítő karakterekkel való közzétételének képességét. Helyettesítő karakteres alkalmazások közzétételéhez kövesse a szabványos alkalmazás-közzétételi folyamatot, de használjon helyettesítő karaktereket a belső és külső URL-címeken.

További információ: Helyettesítő karakteres alkalmazások a Azure Active Directory [alkalmazásproxyban](../manage-apps/application-proxy-wildcard.md)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Új parancsmagok a parancsmagok konfigurációjának alkalmazásproxy

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Alkalmazásproxy-termékképesség:** Platform

Az AzureAD PowerShell előzetes modul legújabb kiadása új parancsmagokat tartalmaz, amelyek lehetővé teszik az ügyfelek számára, hogy alkalmazásproxy Alkalmazásokat a PowerShell használatával.

Az új parancsmagok a következőek:

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>Új parancsmagok a csoportok konfigurálásának támogatásához

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** **Alkalmazásproxy-termékképesség:** Platform

Az AzureAD PowerShell-modul legújabb kiadása parancsmagokat tartalmaz csoportok kezeléséhez az Azure AD-ben. Ezek a parancsmagok korábban az AzureADPreview modulban voltak elérhetők, és mostantól hozzáadva az AzureAD-modulhoz

Az általánosan elérhető csoport parancsmagok a következőek:

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Elérhető a Azure AD Connect új kiadása

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** AD-szinkronizáló **termékképesség:** Platform

Azure AD Connect az Azure AD és a helyszíni adatforrások közötti adatokat szinkronizáló előnyben részesített eszköz, beleértve a Windows Server Active Directory LDAP-t is.

>[!Important]
>Ez a build bevezeti a séma- és szinkronizálási szabályok módosításait. A Azure AD Connect szinkronizálási szolgáltatás a frissítés után elindítja a teljes importálás és a teljes szinkronizálás lépéseit. A viselkedés változásával kapcsolatos további információkért lásd: A teljes szinkronizálás elhalasztva [a frissítés után.](../hybrid/how-to-upgrade-previous-version.md#how-to-defer-full-synchronization-after-upgrade)

Ez a kiadás a következő frissítéseket és módosításokat tartalmazza:

**Kijavított problémák**

- A Partíciószűrés oldal háttérfeladatok időzítési ablakának kijavítása a következő oldalra való váltáskor.

- Kijavítottunk egy hibát, amely hozzáférés-megsértést okozott a ConfigDB egyéni művelete során.

- Kijavítottunk egy hibát, amely az SQL-kapcsolat időtúllépése után helyreállt.

- Kijavítottunk egy hibát, amely miatt a SAN helyettesítő karakterekkel nem sikerült a tanúsítványok előzetes ellenőrzése.

- Kijavítottunk egy hibát, amely miiserver.exe az Azure AD-összekötő exportálása során.

- Kijavítottunk egy hibát, amely miatt a tartományvezérlőn rossz jelszóra tett kísérlet történt a futtatáskor, ami miatt az Azure AD Connect varázsló módosítja a konfigurációt

**Új funkciók és fejlesztések**

- Alkalmazás-telemetria – A rendszergazdák be- és kikapcsolhatják ezt az adatosztályt.

- Azure AD Health-adatok – A rendszergazdáknak az állapotportálon kell szabályozni az állapotbeállításokat. A szolgáltatás-házirendet a változás után az ügynökök beolvasják és kikényszeríteni fogják.

- Hozzá van adva az eszközvisszaírás konfigurációs művelete és egy folyamatjelző sáv az oldal inicializálására.

- Továbbfejlesztett általános diagnosztika HTML-jelentésekkel és teljes adatgyűjtéssel egy ZIP-Text/HTML-jelentésben.

- Az automatikus frissítés nagyobb megbízhatósága és további telemetria hozzáadása a kiszolgáló állapotának meghatározása érdekében.

- Korlátozza az AD Connector-fiók kiemelt fiókjai számára elérhető engedélyeket. Új telepítések esetén a varázsló korlátozza a kiemelt jogosultságú fiókok MSOL-fiókra vonatkozó engedélyeit az MSOL-fiók létrehozása után. A módosítások az expressz telepítéseket és az automatikus fiók-létrehozási egyéni telepítéseket érintik.

- A telepítő úgy módosult, hogy az AADConnect tiszta telepítéséhez ne legyen szükség SA-jogosultságra.

- Új segédprogram egy adott objektum szinkronizálási hibáinak elhárításához. A segédprogram jelenleg a következőket ellenőrzi:

    - UserPrincipalName eltérés a szinkronizált felhasználói objektum és az Azure AD-bérlőben a felhasználói fiók között.

    - Ha az objektum tartományszűrés miatt szűrve van a szinkronizálásból

    - Ha az objektum a szervezeti egység (OU) szűrése miatt szűrve van a szinkronizálásból

- Új segédprogram az adott felhasználói fiók helyi Active Directory aktuális jelszó-kivonatának szinkronizálásához. A segédprogram nem igényel jelszóváltozást.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Az Azure AD Intune App Protection feltételes hozzáféréssel való használatra hozzáadott szabályzatokat támogató alkalmazások

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Feltételes hozzáférési **termékképesség:** Identity Security & Protection

További alkalmazásokat adtunk hozzá, amelyek támogatják az alkalmazásalapú feltételes hozzáférést. Mostantól a jóváhagyott ügyfélalkalmazásokkal hozzáférhet az Office 365-höz és más Azure AD-hez kapcsolódó felhőalkalmazáshoz.

A következő alkalmazások lesznek hozzáadva február végéig:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft-számlázás

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD alkalmazásalapú feltételes hozzáférés](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>Használati feltételek mobilélmény frissítése

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség:** Megfelelőség

Amikor megjelennek a használati feltételek, rákattinthat a **Megtekintési problémák elemre. Kattintson ide.** Erre a hivatkozásra kattintva natív módon megnyithatja a használati feltételeket az eszközön. A dokumentum betűméretétől vagy az eszköz képernyőméretétől függetlenül szükség szerint nagyíthatja és olvashatja a dokumentumot.

---

## <a name="january-2018"></a>2018. január

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Új összevont alkalmazások, amelyek az Azure AD alkalmazásgyűjteményében érhetők el

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Enterprise Apps **Termékképesség:** külső féltől származó integráció

2018 januárjában az alábbi összevonási támogatással a következő új alkalmazások kerültek be az alkalmazásgyűjteménybe:

[IBM OpenPages,](../saas-apps/ibmopenpages-tutorial.md) [OneTrust Privacy Management Software](../saas-apps/onetrust-tutorial.md), [Dealpath](../saas-apps/dealpath-tutorial.md), [IriusRisk Federated Directory és [Fidelity NetBenefits](../saas-apps/fidelitynetbenefits-tutorial.md).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md)

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="sign-in-with-additional-risk-detected"></a>Bejelentkezés további kockázat észlelve

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Identity Protection **termékképesség:** Identity Security & Protection

Az észlelt kockázatészlelésről nyert elemzés az Azure AD-előfizetéséhez kötődik. A prémium szintű Azure AD P2 kiadással a legrészletesebb információkat kap az összes mögöttes észlelésről.

A prémium szintű Azure AD P1 kiadásban a licenc által nem fedezett észlelések kockázatészlelési bejelentkezésként jelennek meg további kockázat észlelése mellett.

További információ: Azure Active Directory [észlelése.](../identity-protection/overview-identity-protection.md)

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365-alkalmazások elrejtése a végfelhasználók hozzáférési paneljeiről

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Saját alkalmazások **termékképesség:** SSO

Mostantól egy új felhasználói beállítással jobban kezelheti, hogy az Office 365-alkalmazások hogyan mutassanak a felhasználói hozzáférési paneleken. Ez a lehetőség akkor hasznos, ha csökkenteni szeretné a felhasználók hozzáférési paneljeinek alkalmazásait, ha csak az Office-alkalmazásokat szeretné az Office-portálon megmutatni. A beállítás a  Felhasználói beállítások között található, és a következő címkével van megcímkézve: A felhasználók csak az Office 365-alkalmazásokat láthatják **az Office 365 portálon.**

További információ: [Alkalmazás elrejtése a](../manage-apps/hide-application-from-user-portal.md)felhasználói élmény elől a Azure Active Directory.

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Közvetlen bejelentkezés a jelszó-egyszeri bejelentkezéshez közvetlenül az alkalmazás URL-címében engedélyezett alkalmazásokba

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Saját alkalmazások **termékképesség:** SSO

A Saját alkalmazások bővítmény mostantól elérhető egy kényelmes eszközzel, amely lehetővé teszi Saját alkalmazások egyszeri bejelentkezés funkciót parancsikonként a böngészőben. A telepítés után a felhasználók egy gofri ikont látnak a böngészőjükben, amely gyors hozzáférést biztosít az alkalmazásokhoz. A felhasználók mostantól a következő előnyöket is kihasználhatják:

- A jelszóalapú SSO-alapú alkalmazásokba való közvetlen bejelentkezés lehetősége az alkalmazás bejelentkezési oldalán
- Bármely alkalmazás elindítása a gyorskeresési funkcióval
- A bővítményből nemrég használt alkalmazások parancsikonjai
- A bővítmény a következő alkalmazásokhoz Microsoft Edge, Chrome és Firefox böngészőkhöz érhető el.

További információkért lásd a [Saját alkalmazások bejelentkezési bővítményt.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Az Azure AD felügyeleti élménye a klasszikus Azure portálon ki lettvezetve

**Írja be a következőt:** Elavult **szolgáltatáskategória:** Az Azure AD **termékképessége:** Címtár

2018. január 8-tól az Azure AD felügyeleti élménye a klasszikus Azure portálon már nem elérhető. Ez a klasszikus Azure portál kiesével együtt történt. A jövőben az Azure [AD](https://aad.portal.azure.com) felügyeleti központot kell használnia az Azure AD összes portálalapú felügyelethez.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>A PhoneFactor webportál ki lett vezetve

**Írja be a következőt:** Elavult **szolgáltatáskategória:** Az Azure AD **termékképessége:** Címtár

2018. január 8-tól a PhoneFactor webportál ki lett vezetve. Ezt a portált az MFA-kiszolgáló felügyeletére használták, de ezek a függvények a Azure Portal a portal.azure.com.

Az MFA konfigurációja a következő helyen található: **Azure Active Directory \> MFA-kiszolgáló**

---

### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések elavultak

**Írja be a következőt:** Elavult **szolgáltatáskategória:** **Jelentéskészítési termék képességei:** Identitás-életciklus kezelése


Az új Azure Active Directory Administration konzol és a tevékenység- és biztonsági jelentésekhez is elérhető új API-k általános elérhetőségével a "/reports" végpont alatt található jelentési API-kat 2017. december 31-től kivezettük.

**Mi érhető el?**

Az új felügyeleti konzolra való áttérés részeként két új API-t készítettünk az Azure AD-tevékenységnaplók leolvasására. Az új API-készlet a naplózási és bejelentkezési tevékenységek gazdagabb szűrési és rendezési funkciói mellett gazdagabb funkciókat biztosít. A biztonsági jelentésekben korábban elérhető adatok mostantól elérhetők az Identity Protection kockázatészlelési API-jának használatával a Microsoft Graph.

További információkért lásd:

- [Első lépések a Azure Active Directory Reporting API-val](../reports-monitoring/concept-reporting-api.md)

- [Első lépések a Azure Active Directory Identity Protection és Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md)

---

## <a name="december-2017"></a>2017. december

### <a name="terms-of-use-in-the-access-panel"></a>Használati feltételek a hozzáférési panel

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség:** Megfelelőség

Most már a hozzáférési panel megtekintheti a korábban elfogadott használati feltételeket.

Kövesse az alábbi lépéseket:

1. A [MyApps portálon jelentkezzen](https://myapps.microsoft.com)be.

2. A jobb felső sarokban válassza ki a nevét, majd válassza a **profilt** a listából.

3. A **profilban válassza** a **Használati feltételek áttekintése lehetőséget.**

4. Most áttekintheti az elfogadott használati feltételeket.

További információkért lásd az Azure AD használati [feltételek funkcióját (előzetes verzió).](../conditional-access/terms-of-use.md)

---

### <a name="new-azure-ad-sign-in-experience"></a>Új Azure AD-bejelentkezési élmény

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Az Azure AD **termékképessége:** Felhasználóhitelesítés

Az Azure AD és Microsoft-fiók identitásrendszer felhasználóinak újratervezése úgy történt, hogy egységes megjelenést és felhasználói megjelenést használjanak. Emellett az Azure AD bejelentkezési oldala először a felhasználónevet, majd a hitelesítő adatokat gyűjti össze egy második képernyőn.

További információ: [Az új Azure AD bejelentkezési](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)élmény nyilvános előzetes verzióban érhető el.

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Kevesebb bejelentkezési kérés: Új "bejelentkezve vagyok" az Azure AD-bejelentkezéshez

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Azure **AD-termékképesség:** Felhasználóhitelesítés

Az  Azure AD bejelentkezési oldalán a Bejelentkezve maradok jelölőnégyzetet egy új üzenet váltotta fel, amely a sikeres hitelesítés után jelenik meg.

Ha az Igen **választ adja** erre a parancssorra, a szolgáltatás egy állandó frissítési jogkivonatot ad. Ez ugyanúgy viselkedik, mint amikor  bejelölve a Régi élményben a Bejelentkezve maradok jelölőnégyzetet. Összevont bérlők esetén ez az üzenet jelenik meg, miután sikeresen hitelesítette magát az összevont szolgáltatással.

További információ: Kevesebb bejelentkezési kérés: Az Azure AD új "bejelentkezve tartás" [élménye előzetes verzióban érhető el.](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Konfiguráció hozzáadása, hogy a használati feltételeket az elfogadás előtt ki kell bontani

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség:** Megfelelőség

A rendszergazdák számára egy lehetőség megköveteli a felhasználóktól, hogy bővítsék a használati feltételeket a feltételek elfogadása előtt.

Válassza a **Be vagy** **a Ki** lehetőséget, hogy a felhasználóktól kötelezően ki kell bontani a használati feltételeket. A **Be beállításhoz** a felhasználóknak meg kell tekinteniük a használati feltételeket azok elfogadása előtt.

További információkért lásd az Azure AD használati [feltételek funkcióját (előzetes verzió).](../conditional-access/terms-of-use.md)

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>A jogosult szerepkör-hozzárendelések hatókörrel alá tartozó aktiválása

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Privileged Identity Management **termékképesség:** Privileged Identity Management

A hatókörrel rendelkező aktiválással aktiválhatja a jogosult Azure-erőforrásszerepkör-hozzárendeléseket, amelyek kevésbé autonómak, mint az eredeti hozzárendelések alapértelmezettek. Ilyen például, ha a bérlő egyik előfizetésének tulajdonosaként van hozzárendelve. A hatókörrel való aktiválással aktiválhatja a tulajdonosi szerepkört az előfizetésben található legfeljebb öt erőforráshoz (például erőforráscsoportokhoz és virtuális gépekhez). Az aktiválás hatókörének meghatározása csökkentheti a nem kívánt módosítások végrehajthatók kritikus Fontosságú Azure-erőforrásokon.

További információ: [Mi az a Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md).

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Új összevont alkalmazások az Azure AD-alkalmazásgyűjteményben

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Nagyvállalati alkalmazások **Termékképesség:** külső féltől származó integráció

2017 decemberében összevonási támogatással hozzáadtunk ezeket az új alkalmazásokat az alkalmazásgyűjteményhez:

[Accredible](../saas-apps/accredible-tutorial.md), Adobe Experience Manager, [EFI Digital StoreFront](../saas-apps/efidigitalstorefront-tutorial.md), [Communifire](../saas-apps/communifire-tutorial.md) CybSafe, [FactSet,](../saas-apps/factset-tutorial.md) [IMAGE WORKS](../saas-apps/imageworks-tutorial.md), [MOBI](../saas-apps/mobi-tutorial.md), [MobileIron Azure AD integration](../saas-apps/mobileiron-tutorial.md), [Reflektive](../saas-apps/reflektive-tutorial.md), [SAML SSO for GmbH,](../saas-apps/bamboo-tutorial.md) [SAML SSO for Bitbucket by resolution GmbH,](../saas-apps/bitbucket-tutorial.md) [Vodeclic](../saas-apps/vodeclic-tutorial.md), WebHR, Zenegy Azure AD Integration.

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazásintegráció a Azure Active Directory.](../saas-apps/tutorial-list.md)

Az alkalmazás Azure AD-alkalmazásgyűjteményben való listázásával kapcsolatos további információkért lásd: Az alkalmazás listázása az Azure Active Directory [katalógusban.](../develop/v2-howto-app-gallery-listing.md)

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Jóváhagyási munkafolyamatok az Azure AD-címtárbeli szerepkörökhöz

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Privileged Identity Management **termékképesség:** Privileged Identity Management

Általánosan elérhető az Azure AD-címtárbeli szerepkörök jóváhagyási munkafolyamata.

A jóváhagyási munkafolyamattal a kiemelt szerepkörű rendszergazdák megkövetelhetik a jogosult szerepkör tagjaitól a szerepkör aktiválását, mielőtt használhatják a kiemelt szerepkört. Több felhasználó és csoport is delegálható jóváhagyási feladatokra. A jogosult szerepkör tagjai értesítést kapnak, ha a jóváhagyás befejeződött, és a szerepkörük aktív.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Átmenő hitelesítés: Skype Vállalati verzió támogatása

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Hitelesítések (bejelentkezések) **Termékképesség:** Felhasználóhitelesítés

Az átmenő hitelesítés mostantól támogatja a modern hitelesítést támogató Skype Vállalati verzió ügyfélalkalmazásaiba való felhasználói bejelentkezéseket, beleértve az online és hibrid topológiákat is.

További információ: A modern hitelesítéssel támogatott [Skype Vállalati verzió topológiái.](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Az Azure RBAC Azure AD Privileged Identity Management frissítései (előzetes verzió)

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Privileged Identity Management **termékképesség:** Privileged Identity Management

Az Azure szerepköralapú hozzáférés-vezérléshez (Azure RBAC) Azure AD Privileged Identity Management nyilvános előzetes verziójú frissítéssel a következő funkciókat használhatja:

* Használja a Just Enough Administrationt.
* Jóváhagyás megkövetelése az erőforrás-szerepkörök aktiválásához.
* Ütemezz egy olyan szerepkör jövőbeli aktiválását, amelyhez jóváhagyás szükséges az Azure AD és az Azure-szerepkörök számára is.

További információ: [Azure-Privileged Identity Management (előzetes verzió)](../privileged-identity-management/azure-pim-resource-rbac.md).

---

## <a name="november-2017"></a>2017. november

### <a name="access-control-service-retirement"></a>Access Control szolgáltatás kieső

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Access Control **termékképesség:** Access Control szolgáltatás

Azure Active Directory Access Control (más néven Access Control szolgáltatás) 2018 végén lesz kivezetve. A következő hetekben további információt, például részletes ütemezést és magas szintű migrálási útmutatót tartalmazunk. Az oldalon megjegyzéseket fűzhet a Access Control szolgáltatással kapcsolatos kérdésekre, és egy csapattag meg fogja válaszolni őket.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Böngésző hozzáférésének korlátozása a Intune Managed Browser

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** Feltételes hozzáférési **termékképesség:** Identitásbiztonság és -védelem

Az Office 365-höz és más Azure AD-hez csatlakoztatott felhőalkalmazáshoz való böngésző-hozzáférést úgy korlátozhatja, hogy a Intune Managed Browser alkalmazásként használja.

Most már konfigurálhatja a következő feltételt az alkalmazásalapú feltételes hozzáféréshez:

**Ügyfélalkalmazások:** Böngésző

**Milyen hatással van a változás?**

A hozzáférés jelenleg le van tiltva, ha ezt a feltételt használja. Ha az előzetes verzió elérhető, minden hozzáféréshez a felügyelt böngészőalkalmazás használata szükséges.

Keresse meg ezt a képességet, és további információkat a soron következő blogokban és kibocsátási megjegyzésekben.

További információ: Feltételes [hozzáférés az Azure AD-ban.](../conditional-access/overview.md)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazásalapú feltételes hozzáféréshez

**Írja be a következőt:** Terv a **szolgáltatáskategória változására:** Feltételes hozzáférési **termékképesség:** Identitásbiztonság és -védelem

A jóváhagyott ügyfélalkalmazások listájában a következő alkalmazások [vannak felsorolva:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD alkalmazásalapú feltételes hozzáférés](../conditional-access/app-based-conditional-access.md)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Használati feltételek támogatása több nyelvhez

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség:** Megfelelőség

A rendszergazdák mostantól új használati feltételeket hozhatnak létre, amelyek több PDF-dokumentumot tartalmaznak. Ezeket a PDF-dokumentumokat címkézheti a megfelelő nyelvvel. A felhasználók a beállításoknak megfelelő nyelven jelennek meg a PDF-ben. Ha nincs egyezés, az alapértelmezett nyelv jelenik meg.

---

### <a name="real-time-password-writeback-client-status"></a>Valós idejű jelszóvisszaírási ügyfél állapota

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Önkiszolgáló jelszó-visszaállítás **termékképesség:** Felhasználóhitelesítés

Most már áttekintheti a helyszíni jelszóvisszaírási ügyfél állapotát. Ez a beállítás a Jelszó **visszaállítása** oldal Helyszíni integráció [szakaszában érhető](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) el.

Ha probléma van a helyszíni visszaírási ügyféllel való kapcsolattal, egy hibaüzenet jelenik meg, amely a következőt biztosítja:

- Információ arról, hogy miért nem tud csatlakozni a helyszíni visszaírási ügyfélhez.
- A probléma megoldását segítő dokumentációra mutató hivatkozás.

További információ: [Helyszíni integráció.](../authentication/concept-sspr-howitworks.md#on-premises-integration)

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD alkalmazásalapú feltételes hozzáférés

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Az Azure AD **termékképessége:** Identitásbiztonság és -védelem

Mostantól az Azure AD alkalmazásalapú feltételes hozzáféréssel az Intune [](../conditional-access/concept-conditional-access-conditions.md#client-apps) alkalmazásvédelmi szabályzatait támogató jóváhagyott ügyfélalkalmazások számára korlátozhatja az Office 365-höz és más Azure [AD-hez](../conditional-access/app-based-conditional-access.md)kapcsolódó felhőalkalmazások hozzáférését. Az Intune alkalmazásvédelmi szabályzatokkal konfigurálhatja és védheti a vállalati adatokat ezeken az ügyfélalkalmazásokon.

Az [alkalmazásalapú és](../conditional-access/app-based-conditional-access.md) az [eszközalapú](../conditional-access/require-managed-devices.md) feltételes hozzáférési szabályzatok kombinálásával rugalmasan védheti a személyes és a vállalati eszközök adatait.

A következő feltételek és vezérlők érhetők el az alkalmazásalapú feltételes hozzáféréssel való használathoz:

**Támogatott platform-feltétel**

- iOS
- Android

**Ügyfélalkalmazások feltétele**

- Mobilalkalmazások és asztali ügyfelek

**Hozzáférés-vezérlés**

- Jóváhagyott ügyfélalkalmazás megkövetelve

További információ: [Alkalmazásalapú Azure AD feltételes hozzáférés.](../conditional-access/app-based-conditional-access.md)

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure AD-eszközök kezelése a Azure Portal

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Eszközregisztráció és -kezelés **Termékképesség:** Identitásbiztonság és -védelem

Mostantól egyetlen helyen találja az Azure AD-hez csatlakoztatott összes eszközt és az eszközhöz kapcsolódó tevékenységeket. Új felügyeleti felhasználói élményben kezelheti az összes eszköz identitását és beállítását a Azure Portal. Ebben a kiadásban a következőt használhatja:

- Az összes megtekintése Azure AD-beli feltételes hozzáféréshez elérhető eszközeit.
- Megtekintheti a tulajdonságokat, például a hibrid Azure AD-hez csatlakozott eszközöket.
- BitLocker-kulcsokat találhat az Azure AD-hez csatlakozott eszközökhöz, kezelheti az eszközt az Intune-nal stb.
- Az Azure AD-hez kapcsolódó beállítások kezelése.

További információ: [Eszközök kezelése a](../devices/device-management-azure-portal.md)Azure Portal.

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>MacOS-támogatás eszközplatformként az Azure AD feltételes hozzáféréshez

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Feltételes hozzáférési **termékképesség:** Identitásbiztonság és -védelem

Mostantól a macOS-t eszközplatform-feltételként is felveheti (vagy kizárhatja) az Azure AD feltételes hozzáférési szabályzatában. A támogatott eszközplatformok macOS-sel való kiegészítésével a következőt teheti:

- **macOS-eszközök regisztrálása és kezelése az Intune-nal.** Más platformokhoz, például az iOS-hez és az Androidhoz hasonlóan a macOS-hez is elérhető egy céges portál alkalmazás az egységes regisztrációkhoz. A macOS-hez elérhető új Céges portál alkalmazással regisztrálhat egy eszközt az Intune-ban, és regisztrálhatja azt az Azure AD-ban.
- **Győződjön meg arról, hogy a macOS-eszközök megfelelnek a szervezet Intune-ban meghatározott megfelelőségi szabályzatának.** Az Intune-ban Azure Portal macOS-eszközökhöz már beállíthatja a megfelelőségi szabályzatokat.
- **Az Azure AD-alkalmazásokhoz való hozzáférés korlátozása csak a megfelelő macOS-eszközökre.** A feltételes hozzáférési szabályzatok szerzői funkcióját a macOS külön eszközplatform-beállításként használja. Most már macOS-specifikus feltételes hozzáférési szabályzatokat állíthat be az Azure-ban beállított célzott alkalmazáshoz.

További információkért lásd:

- [Megfelelőségi szabályzat létrehozása MacOS-eszközökhöz az Intune-nal](/mem/intune/protect/compliance-policy-create-mac-os)
- [Feltételes hozzáférés az Azure AD-ban](../conditional-access/overview.md)

---

### <a name="network-policy-server-extension-for-azure-ad-multi-factor-authentication"></a>Hálózati házirend-kiszolgálói bővítmény az Azure AD Multi-Factor Authentication szolgáltatáshoz

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:**  Többtényezős hitelesítés **termékképessége:** Felhasználóhitelesítés

Az Azure AD Multi-Factor Authentication hálózati házirend-kiszolgáló bővítménye felhőalapú Multi-Factor Authentication-képességeket ad hozzá a hitelesítési infrastruktúrához a meglévő kiszolgálók használatával. A Hálózati házirend-kiszolgáló bővítvével telefonhívást, SMS-t vagy telefonos alkalmazás-ellenőrzést adhat hozzá a meglévő hitelesítési folyamathoz. Nem kell telepítenie, konfigurálnia és karbantartani az új kiszolgálókat.

Ez a bővítmény olyan szervezetek számára lett létrehozva, amelyek meg szeretnék védeni a virtuális magánhálózati kapcsolatokat az Azure Multi-Factor Authentication-kiszolgáló. A hálózati házirend-kiszolgáló bővítmény adapterként működik a RADIUS és a felhőalapú Azure AD Multi-Factor Authentication között, hogy második hitelesítési tényezőt biztosítson az összevont vagy szinkronizált felhasználók számára.

További információ: Meglévő hálózati házirend-kiszolgálói infrastruktúra integrálása [az Azure AD Multi-Factor Authentication használatával.](../authentication/howto-mfa-nps-extension.md)

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Törölt felhasználók visszaállítása vagy végleges eltávolítása

**Írja be a következőt:** Új **funkciószolgáltatás-kategória:** Felhasználókezelési **termékképesség:** Címtár

Az Azure AD felügyeleti központban a következő funkciókat használhatja:

- Törölt felhasználó visszaállítása.
- Felhasználó végleges törlése.

**A kipróbálhoz:**

1. Az Azure AD felügyeleti központban válassza a [Minden felhasználó lehetőséget](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) a Kezelés **szakaszban.**

2. A Show **(Megjelenítése) listában** válassza **a Recently deleted users (Legutóbb törölt felhasználók) lehetőséget.**

3. Jelöljön ki egy vagy több nemrégiben törölt felhasználót, majd állítsa vissza őket, vagy véglegesen törölje őket.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazásalapú feltételes hozzáféréshez

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Feltételes hozzáférés **termékképesség:** Identitásbiztonság és -védelem

A jóváhagyott ügyfélalkalmazások listájához a következő alkalmazások [kerülnek be:](../conditional-access/concept-conditional-access-conditions.md#client-apps)

- Microsoft Planner
- Azure Information Protection

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](../conditional-access/concept-conditional-access-conditions.md#client-apps)
- [Azure AD alkalmazásalapú feltételes hozzáférés](../conditional-access/app-based-conditional-access.md)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>"OR" használata feltételes hozzáférési szabályzat vezérlői között

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Feltételes hozzáférési **termékképesség:** Identitásbiztonság és -védelem

Mostantól használhatja az "OR" (a kijelölt vezérlők egyikének megkövetelő) használatát a Feltételes hozzáférés vezérlőkhöz. Ezzel a funkcióval szabályzatokat hozhat létre az "OR" beállítással a hozzáférés-vezérlés között. Ezzel a funkcióval például olyan szabályzatot hozhat létre, amely megköveteli, hogy a felhasználó a Multi-Factor Authentication "OR" használatával jelentkezzen be egy megfelelő eszközön.

További információ: [Vezérlők az Azure AD feltételes hozzáférésében.](../conditional-access/controls.md)

---

### <a name="aggregation-of-real-time-risk-detections"></a>Valós idejű kockázatészlelések összesítése

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Identity Protection **Termékképesség:** Identitásbiztonság és -védelem

Ebben Azure AD Identity Protection az adott napon azonos IP-címről származó valós idejű kockázatészleléseket a rendszer mostantól minden kockázatészlelési típushoz összesíti. Ez a módosítás a felhasználói biztonság módosítása nélkül korlátozza a megjelenő kockázatészlelések mennyiségét.

A mögöttes valós idejű észlelés minden alkalommal működik, amikor a felhasználó bejelentkezik. Ha a bejelentkezési kockázat biztonsági szabályzata többtényezős hitelesítésre van beállítva, vagy letiltja a hozzáférést, az továbbra is aktiválódik az egyes kockázatos bejelentkezések során.

---

## <a name="october-2017"></a>2017. október

### <a name="deprecate-azure-ad-reports"></a>Az Azure AD-jelentések elavultak

**Írja be a következőt:** A szolgáltatáskategória **változásának megterve:** **Jelentéskészítési termék képessége:** Identitás-életciklus kezelése

A Azure Portal a következőt biztosítja:

- Új Azure AD felügyeleti konzol.
- Új API-k a tevékenység- és biztonsági jelentésekhez.

Ezen új képességek miatt a /reports végpont alatti jelentési API-kat 2017. december 10-én visszavonták.

---

### <a name="automatic-sign-in-field-detection"></a>Automatikus bejelentkezési mezőészlelés

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** Saját alkalmazások **termékképesség:** Egyszeri bejelentkezés

Az Azure AD támogatja az automatikus bejelentkezési mezők észlelését az olyan alkalmazásoknál, amelyek HTML-felhasználónevet és -jelszót renderelnek. Ezeket a lépéseket az Alkalmazás bejelentkezési [mezőinek](../manage-apps/troubleshoot-password-based-sso.md#manually-capture-sign-in-fields-for-an-app)automatikus rögzítése dokumentum tartalmazza. Ezt a képességet úgy találhatja meg,  ha *hozzáad* egy nem katalógusban található alkalmazást a vállalati alkalmazások oldalához a [Azure Portal.](https://aad.portal.azure.com) Ezen az új alkalmazáson konfigurálhatja az egyszeri bejelentkezési módot jelszóalapú egyszeri bejelentkezésre, beírhat egy webes **URL-címet,** majd mentheti az oldalt. 

Egy szolgáltatás problémája miatt ez a funkció ideiglenesen le lett tiltva. A probléma megoldódott, és az automatikus bejelentkezési mező észlelése ismét elérhetővé válik.

---

### <a name="new-multi-factor-authentication-features"></a>Új Multi-Factor Authentication-funkciók

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Többtényezős hitelesítés **termékképessége:** Identitásbiztonság és -védelem

A többtényezős hitelesítés (MFA) elengedhetetlen része a szervezet védelmének. A hitelesítő adatok adaptívabb és zökkenőmentesebb használata érdekében a következő funkciókkal bővült a szolgáltatás:

- A többtényezős feladat eredményei közvetlenül integrálva vannak az Azure AD bejelentkezési jelentésbe, amely szoftveres hozzáférést biztosít az MFA-eredményekhez.
- Az MFA konfigurációja mélyebben integrálva van az Azure AD konfigurációs élménybe a Azure Portal.

Ebben a nyilvános előzetes verzióban az MFA-kezelés és -jelentéskészítés az Azure AD alapvető konfigurációs szolgáltatásának integrált része. Most már kezelheti az MFA felügyeleti portál funkcióit az Azure AD-környezetben.

További információkért lásd az [MFA-jelentéskészítés referenciáját a Azure Portal.](../authentication/howto-mfa-reporting.md)

---

### <a name="terms-of-use"></a>Használati feltételek

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Használati feltételek **termékképesség:** Megfelelőség

Az Azure AD használati feltételeivel információkat, például jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatokat lehet bemutatni a felhasználóknak.

Az Azure AD használati feltételeit a következő esetekben használhatja:

- Általános használati feltételek a szervezet összes felhasználója számára
- A felhasználó attribútumai alapján meghatározott használati feltételek (például orvosok vagy orvosok vagy belföldi vagy nemzetközi alkalmazottak, dinamikus csoportok által végzett)
- A nagy hatással járó üzleti alkalmazások, például a Salesforce elérésére vonatkozó használati feltételek

További információ: [Az Azure AD használati feltételei.](../conditional-access/terms-of-use.md)

---

### <a name="enhancements-to-privileged-identity-management"></a>A Privileged Identity Management

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Privileged Identity Management **termékképesség:** Privileged Identity Management

A Azure AD Privileged Identity Management segítségével kezelheti, vezérelheti és figyelheti a szervezeten belüli Azure-erőforrásokhoz (előzetes verzió) való hozzáférést a következő szolgáltatások érdekében:

- Előfizetések
- Erőforráscsoportok
- Virtual machines (Virtuális gépek)

Az Azure RBAC Azure Portal funkciókat felhasználó összes erőforrás kihasználhatja az összes biztonsági és életciklus-kezelési képességet, Azure AD Privileged Identity Management kínálnak.

További információ: Privileged Identity Management [Azure-erőforrásokhoz.](../privileged-identity-management/azure-pim-resource-rbac.md)

---

### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

**Írja be a következőt:** Új **funkciószolgáltatás-kategória: Hozzáférési** felülvizsgálatok **Termékképesség:** Megfelelőség

A szervezetek a hozzáférési felülvizsgálatok (előzetes verzió) segítségével hatékonyan kezelhetik a csoporttagságokat és a vállalati alkalmazásokhoz való hozzáférést:

- Az alkalmazásokhoz való hozzáférések és a csoporttagságok felülvizsgálatával újrahitelesítheti a vendégfelhasználói hozzáférést. A felülvizsgálók hatékonyan dönthetik el, hogy engedélyezik-e a vendégeknek a folyamatos hozzáférést a hozzáférési felülvizsgálatok által biztosított elemzések alapján.
- A hozzáférési felülvizsgálatok segítségével az alkalmazottak alkalmazás-hozzáférését és csoporttagságait is újból hitelesítheti.

A hozzáférési felülvizsgálati vezérlőket cég- vagy szervezetspecifikus programokban is összegyűjtheti a megfelelőség vagy a kockázatérzékeny alkalmazások felülvizsgálatának nyomon követése céljából.

További információ: [Azure AD hozzáférési felülvizsgálatok.](../governance/access-reviews-overview.md)

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Külső alkalmazások elrejtése a Saját alkalmazások Office 365 alkalmazásindítójában

**Írja be a következőt:** Új **szolgáltatásszolgáltatás-kategória:** Saját alkalmazások **termékképesség:** Egyszeri bejelentkezés

Mostantól az új elrejtés alkalmazástulajdonságokkal jobban kezelheti a felhasználói portálon indító **alkalmazásokat.** Elrejtheti az alkalmazásokat, hogy segítsenek abban az esetben, ha az alkalmazáscsempék háttérszolgáltatásokhoz vagy ismétlődő csempékhez is megjelennek, és zsúfolttá teszi a felhasználók alkalmazásindítóit. A váltógomb a külső alkalmazás **Tulajdonságok** szakaszában található, és a felhasználó számára **látható címkével van megcímkézve.** Az alkalmazásokat programozott módon is elrejtheti a PowerShell használatával.

További információ: Harmadik féltől származó alkalmazás elrejtése a felhasználói élményből az [Azure AD-ban.](../manage-apps/hide-application-from-user-portal.md)


**Mi érhető el?**

 Az új felügyeleti konzolra való áttérés részeként két új API érhető el az Azure AD-tevékenységnaplók leolvasására. Az új API-k gazdagabb szűrési és rendezési funkciókat szolgáltatásokat kínálnak, valamint gazdagabb naplózási és bejelentkezési tevékenységeket szolgáltatásokat kínálnak. A biztonsági jelentésekben korábban elérhető adatok már elérhetők az Identity Protection kockázatészlelési API-jának használatával a Microsoft Graph.


## <a name="september-2017"></a>2017. szeptember

### <a name="hotfix-for-identity-manager"></a>Az Identity Manager gyorsjavítása

**Írja be a következőt:** Módosított **szolgáltatásszolgáltatás-kategória:** Identity Manager **termékképesség:** Identitás-életciklus kezelése

Az Identity Manager 2016 Service Pack 1-hez 2017. szeptember 25-től elérhető egy összesítő gyorsjavítási csomag (4.4.1642.0-s build). Ez az összesítő csomag:

- Megoldja a problémákat, és fejlesztéseket ad hozzá.
- Összegző frissítés, amely lecseréli az Identity Manager 2016 Service Pack 1 összes frissítését az Identity Manager 2016 4.4.1459.0-s buildjére.
- Ehhez az Identity Manager 2016 4.4.1302.0-s buildje szükséges.

További információ: Az Identity Manager 2016 Service Pack 1-hez elérhető a gyorsjavítások összesítő [csomagja (4.4.1642.0-s build).](https://support.microsoft.com/help/4021562)

---