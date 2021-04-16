---
title: Első lépések a Azure Active Directory integrálása alkalmazásokkal
description: Ez a cikk egy bevezető útmutató a Azure Active Directory (AD) helyszíni alkalmazásokkal és felhőalapú alkalmazásokkal való integrálásához.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 04/05/2021
ms.author: iangithinji
ms.reviewer: asteen
ms.openlocfilehash: 37916e5e356e7c5ad6e685ac0838363fe2579496
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374981"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Integrációs Azure Active Directory alkalmazásokkal – első lépések útmutató

Ez a témakör az alkalmazások és a Azure Active Directory (AD) integrálásának folyamatát foglalja össze. Az alábbi szakaszok egy-egy részletesebb témakör rövid összefoglalását tartalmazzák, így azonosíthatja, hogy az első lépéseket tartalmazó útmutató mely részei fontosak Önnek.

Részletes üzembe helyezési tervek letöltéséhez lásd: [További lépések.](#next-steps)

## <a name="take-inventory"></a>Leltározás
Mielőtt integrálja az alkalmazásokat az Azure AD-val, fontos tudni, hogy ön hol van és hol szeretne lenni.  Az alábbi kérdések az Azure AD alkalmazásintegrációs projekttel kapcsolatos kérdésekhez segítenek.

### <a name="application-inventory"></a>Alkalmazásleltár
* Hol van az összes alkalmazása? Ki a tulajdonuk?
* Milyen hitelesítésre van szüksége az alkalmazásoknak?
* Kinek van szüksége hozzáférésre mely alkalmazásokhoz?
* Új alkalmazást szeretne üzembe helyezni?
  * Házon keresztül építi fel és üzembe fogja helyezni egy Azure számítási példányon?
  * Olyan alkalmazást fog használni, amely elérhető az Azure-alkalmazásgyűjteményben?

### <a name="user-and-group-inventory"></a>Felhasználói és csoportleltár
* Hol találhatók a felhasználói fiókok?
  * Helyszíni Active Directory
  * Azure AD
  * Egy külön alkalmazás-adatbázison belül, amely az Ön tulajdonában van
  * Nem engedély nélküli alkalmazásokban
  * A fentiek mindegyike
* Jelenleg milyen engedélyekkel és szerepkör-hozzárendelésekkel rendelkezik az egyes felhasználók? Át kell vizsgálnia a hozzáférésüket, vagy biztos benne, hogy a felhasználói hozzáférés és a szerepkör-hozzárendelések megfelelőek?
* A csoportok már létrejöttek a helyi Active Directory?
  * Hogyan vannak rendszerezve a csoportok?
  * Kik a csoport tagjai?
  * Jelenleg milyen engedélyekkel/szerepkör-hozzárendelésekkel rendelkezik a csoport?
* Meg kell-e tisztítania a felhasználói/csoportadatbázisokat az integráció előtt?  (Ez egy fontos kérdés. Szemétgyűjtés, kigyűjtés.)

### <a name="access-management-inventory"></a>Hozzáférés-kezelési leltár
* Hogyan kezelheti jelenleg az alkalmazások felhasználói hozzáférését? Ennek változnia kell?  Megfontolt egyéb hozzáférési módszereket, például az [Azure RBAC-t?](../../role-based-access-control/role-assignments-portal.md)
* Kinek van szüksége hozzáférésre?

Lehet, hogy nem tudja előre megválaszolni ezeket a kérdéseket, de ez nem gond.  Ez az útmutató segíthet megválaszolni néhány kérdést, és megalapozott döntéseket hozni.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Nem engedély nélküli felhőalkalmazások Cloud Discovery

Ahogy korábban említettük, előfordulhat, hogy a szervezet eddig nem kezelt alkalmazásokat.  A leltározási folyamat részeként lehetőség van nem engedély nélküli felhőalkalmazások keresésére. Lásd: [Cloud Discovery.](/cloud-app-security/set-up-cloud-discovery)

## <a name="integrating-applications-with-azure-ad"></a>Alkalmazások integrálása az Azure AD segítségével
A következő cikkek az alkalmazások Azure AD-val való integrálásának különböző módjait ismertetik, és útmutatást nyújtanak.

* [A Active Directory meghatározása](../fundamentals/active-directory-whatis.md)
* [Alkalmazások használata az Azure-alkalmazásgyűjteményben](what-is-single-sign-on.md)
* [SaaS-alkalmazások integrálása – oktatóanyagok listája](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Az Azure AD-katalógusban nem szereplő alkalmazások képességei

Bármilyen, a szervezetben már létező alkalmazást hozzáadhat, vagy olyan szállítótól származó külső alkalmazást, amely még nem tagja az Azure AD-katalógusnak. A [licencszerződéstől függően a](https://azure.microsoft.com/pricing/details/active-directory/)következő képességek érhetők el:

- A Security Assertion Markup Language [(SAML) 2.0 identitásszolgáltatókat (SP](https://wikipedia.org/wiki/SAML_2.0) vagy identitásszolgáltató által kezdeményezett) támogató alkalmazások önkiszolgáló integrációja
- A jelszóalapú [SSO-t](sso-options.md#password-based-sso) használó, HTML-alapú bejelentkezési oldalú webalkalmazások önkiszolgáló integrációja
- A felhasználóátépítéshez a [System for Cross-Domain Identity Management (SCIM)](../app-provisioning/use-scim-to-provision-users-and-groups.md) protokollt használó alkalmazások önkiszolgáló kapcsolata
- Lehetőség bármely alkalmazás hivatkozásának hozzáadására az [Office 365 alkalmazásindítójában vagy](https://support.microsoft.com/office/meet-the-microsoft-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) [Saját alkalmazások](https://myapplications.microsoft.com/)

Ha fejlesztői útmutatásra van szüksége az egyéni alkalmazások Azure AD-be való integrálásához, tekintse meg a hitelesítési forgatókönyveket az [Azure AD-hez.](../develop/authentication-vs-authorization.md) Ha olyan alkalmazást fejleszt, amely az [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) protokollhoz hasonló modern protokollt használ [](../develop/quickstart-register-app.md) a felhasználók hitelesítéséhez, regisztrálhatja azt a Microsoft identitásplatformján a Alkalmazásregisztrációk felhasználói felületének Azure Portal.

### <a name="authentication-types"></a>Hitelesítési típusok
Az egyes alkalmazások különböző hitelesítési követelményekkel is lehetnek. Az Azure AD-ben az aláíró tanúsítványok saml 2.0-t, WS-Federationt vagy OpenID Connect protokollokat és jelszavas egyszeri bejelentkezést használnak. További információ az alkalmazáshitelesítési típusokról: [Managing Certificates for Federated Single Sign-On](manage-certificates-for-federated-single-sign-on.md) in Azure Active Directory and Password based single sign [on](what-is-single-sign-on.md)..

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Az SSO engedélyezése Azure AD alkalmazás proxyval
A Microsoft Azure AD alkalmazásproxy segítségével biztonságosan, bárhonnan és bármilyen eszközön biztosíthatja a hozzáférést a magánhálózaton belüli alkalmazásokhoz. Miután telepített egy alkalmazásproxy-összekötőt a környezetében, az könnyedén konfigurálható az Azure AD-val.

### <a name="integrating-custom-applications"></a>Egyéni alkalmazások integrálása
Ha egyéni alkalmazását hozzá szeretné adni az Azure Alkalmazásgyűjteményhez, tekintse meg az Alkalmazás közzététele az [Azure AD-alkalmazásgyűjteményben cikkeket.](../develop/v2-howto-app-gallery-listing.md)

## <a name="managing-access-to-applications"></a>Alkalmazásokhoz való hozzáférés kezelése
A következő cikkek ismertetik, hogyan kezelheti az alkalmazásokhoz való hozzáférést, miután az Azure AD-összekötők és az Azure AD használatával integrálta őket az Azure AD-be.

* [Alkalmazásokhoz való hozzáférés kezelése az Azure AD használatával](what-is-access-management.md)
* [Automatizálás Azure AD-összekötők használatával](../app-provisioning/user-provisioning.md)
* [Felhasználók hozzárendelése egy alkalmazáshoz](./assign-user-or-group-access-portal.md)
* [Csoportok hozzárendelése egy alkalmazáshoz](./assign-user-or-group-access-portal.md)
* [Fiókok megosztása](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Következő lépések
Részletes információkért töltse le az üzembehely Azure Active Directory a [GitHubról.](../fundamentals/active-directory-deployment-plans.md) Katalógusalkalmazások esetén az egyszeri bejelentkezéshez, a feltételes hozzáféréshez és a felhasználókiépítéshez szükséges telepítési terveket a következő [Azure Portal.](https://portal.azure.com)

Telepítési terv letöltése a Azure Portal:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza **a Vállalati alkalmazások**  |  **lehetőséget, válasszon egy alkalmazástelepítési**  |  **tervet.**
