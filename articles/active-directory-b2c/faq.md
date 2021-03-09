---
title: Gyakori kérdések (GYIK) Azure Active Directory B2C
description: Válaszok a Azure Active Directory B2Cekkel kapcsolatos gyakori kérdésekre.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cef2636aeecf78a12c48b31b384af2cf76903879
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102498829"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: gyakori kérdések (GYIK)

Ez az oldal válaszol a Azure Active Directory B2C (Azure AD B2C) kapcsolatos gyakori kérdésekre. A frissítések ellenőrzésének megtartása.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Miért nem tudom elérni a Azure AD B2C bővítményt a Azure Portalban?

Két gyakori oka van annak, hogy az Azure AD-bővítmény miért nem működik. Azure AD B2C megköveteli, hogy a címtárban a globális rendszergazda legyen a felhasználói szerepköre. Vegye fel a kapcsolatot a rendszergazdával, ha úgy gondolja, hogy rendelkezik hozzáféréssel. Ha globális rendszergazdai jogosultságokkal rendelkezik, győződjön meg róla, hogy Azure AD B2C könyvtárban van, és nem Azure Active Directory könyvtárat. [Azure ad B2C bérlő létrehozásához](tutorial-create-tenant.md)szükséges utasításokat itt tekintheti meg.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Használhatom Azure AD B2C szolgáltatásokat a meglévő, alkalmazotti alapú Azure AD-bérlőn?

Az Azure AD és a Azure AD B2C külön termék-ajánlat, és nem létezhet egyszerre ugyanabban a bérlőben. Egy Azure AD-bérlő a szervezetet jelöli. Az Azure AD B2C bérlők a függő entitások alkalmazásaival használható identitások gyűjteményét jelölik. Ha **új OpenID Connect-szolgáltatót** ad hozzá a **Azure ad B2C >-identitás-szolgáltatóhoz** vagy egyéni szabályzatokhoz, a Azure ad B2C összevonása az Azure ad-be, amely lehetővé teszi a szervezet alkalmazottainak hitelesítését.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-microsoft-365"></a>Használhatom a Azure AD B2Ct a közösségi bejelentkezés (Facebook és a Google +) megadására a Microsoft 365ba?

Azure AD B2C nem használható a Microsoft 365 felhasználók hitelesítéséhez. Az Azure AD a Microsoft megoldás az SaaS-alkalmazásokhoz való alkalmazottak hozzáférésének kezelésére, és olyan funkciókkal rendelkezik, amelyek erre a célra lettek kialakítva, például licencelés és feltételes hozzáférés. A Azure AD B2C identitás-és hozzáférés-kezelési platformot biztosít a webes és mobil alkalmazások létrehozásához. Ha Azure AD B2C úgy van konfigurálva, hogy az Azure AD-bérlőre összevonása, az Azure AD-bérlő kezeli az alkalmazottak hozzáférését a Azure AD B2C-on alapuló alkalmazásokhoz.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Mik a Azure AD B2C helyi fiókjai? Miben különböznek a munkahelyi vagy iskolai fiókok az Azure AD-ben?

Egy Azure AD-bérlőben a bérlőhöz tartozó felhasználók az űrlap e-mail-címével rendelkeznek `<xyz>@<tenant domain>` . A a `<tenant domain>` bérlő vagy a kezdeti tartomány ellenőrzött tartományainak egyike `<...>.onmicrosoft.com` . Ez a fióktípus munkahelyi vagy iskolai fiók.

Egy Azure ad B2C bérlőben a legtöbb alkalmazás azt szeretné, hogy a felhasználó tetszőleges e-mail-címmel jelentkezzen be (például,,, joe@comcast.net bob@gmail.com sarah@contoso.com vagy jim@live.com ). Ez a fióktípus helyi fiók. A helyi fiókok (például Joe, Bob, Sarah vagy Jim) alapján is támogatjuk az tetszőleges felhasználóneveket. Ezt a két helyi fióktípus közül választhatja ki, ha az Azure Portal Azure AD B2C identitás-szolgáltatóját konfigurálja. A Azure AD B2C-bérlőben válassza az **identitás-szolgáltatók**, majd a **helyi fiók** lehetőséget, és válassza a **username (Felhasználónév**) lehetőséget.

Az alkalmazásokhoz tartozó felhasználói fiókok létrehozhatók egy regisztrációs felhasználói folyamaton, a regisztrációs vagy bejelentkezési felhasználói folyamaton, a Microsoft Graph API-n vagy a Azure Portal.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Mely közösségi identitás-szolgáltatókat támogatja most? Melyeket tervezi a jövőben támogatni?

Jelenleg számos közösségi identitást támogatunk, többek között az Amazon, a Facebook, a GitHub (előzetes verzió), a Google, a LinkedIn, a Microsoft account (MSA), a QQ (előzetes verzió), a Twitter, a WeChat (előzetes verzió) és a WeiBo (előzetes verzió Az ügyfelek igénye alapján kiértékeljük, hogy további népszerű közösségi identitás-szolgáltatók támogatását vesszük fel.

A Azure AD B2C az [egyéni házirendeket](custom-policy-overview.md)is támogatja. Az egyéni házirendek lehetővé teszik saját szabályzat létrehozását bármely olyan identitás-szolgáltató számára, amely támogatja az [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) vagy az SAML használatát. Ismerkedjen meg az egyéni házirendekkel, és tekintse meg az egyéni szabályzatok [alapszintű csomagját](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Beállíthat hatóköröket, hogy további információkat gyűjtsön a különböző közösségi identitású szolgáltatók fogyasztóiról?

Nem. A közösségi identitás-szolgáltatók támogatott készletéhez használt alapértelmezett hatókörök a következők:

* Facebook: e-mail
* Google +: e-mail
* Microsoft-fiók: OpenID e-mail profil
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Az alkalmazásnak az Azure-ban kell futnia, hogy működjön a Azure AD B2C?

Nem, bárhol is üzemeltetheti az alkalmazást (a felhőben vagy a helyszínen). Ahhoz, hogy a Azure AD B2C kommunikáljon, képesnek kell lennie HTTP-kérések küldésére és fogadására nyilvánosan elérhető végpontokon.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Több Azure AD B2C Bérlővel rendelkezem. Hogyan kezelhetem ezeket a Azure Portal?

A Azure Portal bal oldali menüjében a "Azure AD B2C" megnyitása előtt be kell váltania a kezelni kívánt könyvtárba. A könyvtárakat a Azure Portal jobb felső sarkában található identitásra kattintva, majd a megjelenő legördülő listából választhatja ki.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>A Azure AD B2C által küldött ellenőrző e-mailek (a tartalom és a "Feladó:" mező) testreszabása Hogyan

Az ellenőrző e-mailek tartalmának testreszabásához használhatja a [vállalati arculati funkciót](../active-directory/fundamentals/customize-branding.md) . Az e-mailek ezen két eleme testreszabható:

* **Szalagcím emblémája**: a jobb alsó sarokban látható.
* **Háttérszín**: látható felül.

    ![Képernyőkép a testreszabott ellenőrző e-mailekről](./media/faq/company-branded-verification-email.png)

Az e-mail-aláírás tartalmazza azt a Azure AD B2C bérlő nevét, amelyet a Azure AD B2C bérlő első létrehozásakor adott meg. A nevet a következő utasítások használatával módosíthatja:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) globális rendszergazdaként.
1. Nyissa meg az **Azure Active Directory** panelt.
1. Válassza a **Tulajdonságok** lapot.
1. Módosítsa a **név** mezőt.
1. Kattintson az oldal tetején lévő **Mentés** elemre.

Jelenleg nem módosítható a "from:" mező az e-mailben.

> [!TIP]
> Azure AD B2C [Egyéni szabályzattal](custom-policy-overview.md)testreszabhatja az e-mailek küldését Azure ad B2C a felhasználók számára, beleértve az e-mailben szereplő "from:" mezőt is. Az egyéni e-mail-ellenőrzéshez egy külső gyártótól származó e-mail-szolgáltató (például [mailjet](custom-email-mailjet.md), [SendGrid](custom-email-sendgrid.md)vagy [SparkPost](https://sparkpost.com)) használata szükséges.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Hogyan telepíthetem át a meglévő felhasználóneveket, jelszavakat és profilokat az adatbázisból a Azure AD B2Cba?

Az áttelepítési eszköz írásához a Microsoft Graph API-t használhatja. A részletekért tekintse meg a [felhasználói áttelepítési útmutatót](user-migration.md) .

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Milyen jelszó-felhasználói folyamatot használ a Azure AD B2C helyi fiókjaihoz?

A helyi fiókok Azure AD B2C jelszavának felhasználói folyamata az Azure AD-szabályzaton alapul. Az Azure AD B2C's regisztrálása, regisztrálása, illetve bejelentkezési és jelszó-visszaállítási felhasználói folyamatai az "erős" jelszó erősségét használják, és nem jár le semmilyen jelszó. További részletekért tekintse [meg a jelszóházirend és a korlátozások a Azure Active Directoryban](../active-directory/authentication/concept-sspr-policy.md)című témakört.

A fiókok zárolásával és jelszavával kapcsolatos információkért lásd: a [fenyegetések kezelése az erőforrásokra és az adatokra Azure Active Directory B2Cban](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Használhatom Azure AD Connect a helyszíni Active Directory tárolt fogyasztói identitások áttelepíthetők a Azure AD B2Cre?

Nem, a Azure AD Connect nem a Azure AD B2C való működésre lett tervezve. Érdemes lehet a [Microsoft Graph API](microsoft-graph-operations.md) -t használni a felhasználók áttelepítéséhez. A részletekért tekintse meg a [felhasználói áttelepítési útmutatót](user-migration.md) .

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Megnyithatom az alkalmazásom Azure AD B2C oldalakat egy iFrame-kereten belül?

Nem, biztonsági okokból Azure AD B2C lapok nem nyithatók meg iFrame-kereten belül. A szolgáltatás kommunikál a böngészővel az iFrame-elemek tiltásához. A biztonsági Közösség általánosságban és a OAUTH2-specifikációban ajánlott, hogy a kattintások és a csatlakozás kockázata miatt a személyazonossági tapasztalatok iFrame-alapú használatát ajánljuk.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Működik Azure AD B2C olyan CRM-rendszerekkel, mint a Microsoft Dynamics?

Elérhető a Microsoft Dynamics 365 portál integrációja. Lásd: a [Dynamics 365-portál konfigurálása Azure ad B2C használatára a hitelesítéshez](/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Működik Azure AD B2C a SharePoint helyszíni 2016-es vagy korábbi verziójában?

Azure AD B2C nem a SharePoint külső partner-megosztási forgatókönyvének a célja; Lásd: [Azure ad B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) .

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>A külső identitások kezeléséhez Azure AD B2C vagy B2B használata szükséges?

Olvassa el a [B2B együttműködés és B2C összevetése az Azure ad-ben](../active-directory/external-identities/compare-with-b2c.md) című témakört, ahol többet tudhat meg a külső identitási forgatókönyvek megfelelő jellemzőinek alkalmazásáról.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Milyen jelentéskészítési és naplózási funkciókat biztosít a Azure AD B2C? Ugyanazok, mint a prémium szintű Azure ADban?

Nem, Azure AD B2C nem támogatja ugyanazokat a jelentéseket, mint a prémium szintű Azure AD. Számos közös vonás van azonban:

* A **bejelentkezési jelentések** a lecsökkentett részletekkel rendelkező bejelentkezéseket tartalmazzák.
* A **naplózási jelentések** magukban foglalják a rendszergazdai tevékenységeket és az alkalmazás tevékenységeit is.
* A **használati jelentések** közé tartozik a felhasználók száma, a bejelentkezések száma és az MFA mennyisége.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Honosítható a Azure AD B2C által kiszolgált lapok felhasználói felülete? Milyen nyelveket támogat a rendszer?

Igen, lásd: [nyelvi Testreszabás](language-customization.md). 36 nyelvű fordítást biztosítunk, és bármilyen karakterláncot felülbírálhat az igényeinek megfelelően.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Használhatom a saját URL-címeket az Azure AD B2C által kiszolgált regisztrációs és bejelentkezési lapokon? Megváltoztathatom például a contoso.b2clogin.com és a login.contoso.com URL-címét?

Jelenleg nincs. Ez a funkció az ütemterven található. A tartomány ellenőrzése a **tartományok** lapon, a Azure Portal nem hajtja végre ezt a célt. A b2clogin.com azonban [semleges legfelső szintű tartományt](b2clogin.md)kínálunk, így a külső megjelenés a Microsoft megemlítése nélkül is megvalósítható.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Hogyan törölni a Azure AD B2C bérlőt?

A Azure AD B2C bérlő törléséhez kövesse az alábbi lépéseket.

Használhatja az új, egységesített **Alkalmazásregisztrációk** élményt vagy az örökölt  **alkalmazások (örökölt)** felületét. [További információ az új felületről](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Alkalmazásregisztrációk](#tab/app-reg-ga/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) *előfizetés-rendszergazdaként*. Ugyanazt a munkahelyi vagy iskolai fiókot használja, vagy ugyanazt a Microsoft-fiók, amelyet az Azure-ra való regisztrációhoz használt.
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Törölje az összes **felhasználói folyamatot (szabályzatot)** a Azure ad B2C-bérlőben.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza a **minden alkalmazás** lapot.
1. Az összes regisztrált alkalmazás törlése.
1. A **B2C-Extensions-app** törlése.
1. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
1. Jelölje ki az egyes felhasználókat, és válassza ki azt az *előfizetés-rendszergazda* felhasználót, amelyhez jelenleg be van jelentkezve. Kattintson a lap alján található **Törlés** elemre, és válassza az **Igen** lehetőséget, ha a rendszer kéri.
1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.
1. A **kezelés** területen válassza a **felhasználói beállítások** lehetőséget.
1. A **kezelés** területen válassza a **Tulajdonságok** lehetőséget.
1. Az **Azure-erőforrásokhoz való hozzáférés kezelése** területen válassza az **Igen**, majd a **Mentés** lehetőséget.
1. Jelentkezzen ki a Azure Portal, majd jelentkezzen be újra a hozzáférésének frissítéséhez.
1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.
1. Az **Áttekintés** lapon válassza a **bérlő törlése** lehetőséget. A folyamat befejezéséhez kövesse a képernyőn megjelenő utasításokat.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) *előfizetés-rendszergazdaként*. Ugyanazt a munkahelyi vagy iskolai fiókot használja, vagy ugyanazt a Microsoft-fiók, amelyet az Azure-ra való regisztrációhoz használt.
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Törölje a Azure AD B2C bérlő összes **felhasználói folyamatát (szabályzatát)** .
1. Törölje az Azure AD B2C-bérlőben regisztrált összes **alkalmazást (örökölt)** .
1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.
1. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
1. Jelölje ki az egyes felhasználókat, és válassza ki azt az *előfizetés-rendszergazda* felhasználót, amelyhez jelenleg be van jelentkezve. Kattintson a lap alján található **Törlés** elemre, és válassza az **Igen** lehetőséget, ha a rendszer kéri.
1. A **Kezelés** területen válassza az **Alkalmazásregisztrációk** lehetőséget.
1. Válassza **az összes alkalmazás megtekintése** lehetőséget
1. Válassza ki a **B2C-Extensions-app** nevű alkalmazást, válassza a **Törlés** lehetőséget, majd kattintson az **Igen** gombra, amikor a rendszer kéri.
1. A **kezelés** területen válassza a **felhasználói beállítások** lehetőséget.
1. Ha van ilyen, a **LinkedIn-fiók kapcsolatai** területen válassza a **nem**, majd a **Mentés** lehetőséget.
1. A **kezelés** területen válassza a **Tulajdonságok** lehetőséget.
1. Az **Azure-erőforrásokhoz való hozzáférés kezelése** területen válassza az **Igen**, majd a **Mentés** lehetőséget.
1. Jelentkezzen ki a Azure Portal, majd jelentkezzen be újra a hozzáférésének frissítéséhez.
1. A bal oldali menüben válassza a **Azure Active Directory** lehetőséget.
1. Az **Áttekintés** lapon válassza a **könyvtár törlése** lehetőséget. A folyamat befejezéséhez kövesse a képernyőn megjelenő utasításokat.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Lekérhetek Azure AD B2C a nagyvállalati mobilitási csomag részeként?

Nem, Azure AD B2C az utólagos elszámolású Azure-szolgáltatás, amely nem része a nagyvállalati mobilitási csomagnak.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Hogyan a Azure AD B2Ckel kapcsolatos problémákat?

Lásd: [Azure Active Directory B2C fájlokra vonatkozó támogatási kérelmek](support-options.md).
