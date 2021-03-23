---
title: Ismerkedés a Azure Active Directory alkalmazásokkal való integrálásával
description: Ez a cikk egy első lépéseket ismertető útmutató a Azure Active Directory (AD) helyszíni alkalmazásokkal és felhőalapú alkalmazásokkal való integrálásához.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 03/19/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: de06bb4f97568eaa40b0b09e9bc2b50608424aa8
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775595"
---
# <a name="integrating-azure-active-directory-with-applications-getting-started-guide"></a>Azure Active Directory integrálása az első lépések útmutató alkalmazásokkal

Ez a témakör összefoglalja az alkalmazások Azure Active Directory (AD) szolgáltatással való integrálásának folyamatát. Az alábbi részek egy részletesebb témakört tartalmaznak, amelyből megtudhatja, hogy az első lépéseket ismertető útmutató mely részeire vonatkozik.

A részletes telepítési csomagok letöltéséhez tekintse meg a [következő lépéseket](#next-steps).

## <a name="take-inventory"></a>Leltár készítése
Az alkalmazások az Azure AD-vel való integrálása előtt fontos tudni, hogy hol van, és hová szeretne menni.  Az alábbi kérdések segítenek az Azure AD-alkalmazás-integrációs projekt meggondolása során.

### <a name="application-inventory"></a>Alkalmazásleltár
* Hol találhatók az összes alkalmazás? Kik tulajdonosai?
* Milyen típusú hitelesítésre van szükség az alkalmazásaiban?
* Kinek van hozzáférése az alkalmazásokhoz?
* Új alkalmazást kíván üzembe helyezni?
  * Létrehozza a házat, és üzembe helyezi egy Azure számítási példányon?
  * Ön az Azure Application Galleryben elérhetővé teszi az egyiket?

### <a name="user-and-group-inventory"></a>Felhasználó és csoport leltára
* Hol találhatók a felhasználói fiókjai?
  * Helyszíni Active Directory
  * Azure AD
  * Egy külön saját alkalmazás-adatbázison belül
  * A nem engedélyezett alkalmazásokban
  * A fentiek mindegyike
* Milyen engedélyek és szerepkör-hozzárendelések vannak jelenleg az egyes felhasználók számára? Ellenőriznie kell a hozzáférését, vagy biztos lehet benne, hogy a felhasználói hozzáférés és a szerepkör-hozzárendelések megfelelőek?
* A csoportok már a helyszíni Active Directoryban vannak kialakítva?
  * Hogyan vannak rendszerezve a csoportok?
  * Kik a csoporttagok?
  * Milyen engedélyek/szerepkör-hozzárendelések vannak jelenleg a csoportok számára?
* Az integrálás előtt törölnie kell a felhasználó/csoport adatbázisait?  (Ez egy fontos kérdés. Bekerülés a szemétbe.)

### <a name="access-management-inventory"></a>Hozzáférés-kezelési leltár
* Hogyan kezelheti jelenleg az alkalmazásokhoz való felhasználói hozzáférést? Ezt módosítani kell?  Más módszerekkel kezelheti a hozzáférést, például az [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) például?
* Kinek van hozzáférése a következőhöz?

Lehet, hogy nem rendelkezik a válaszokkal az összes kérdésre előre, de ez rendben van.  Ez az útmutató segítséget nyújt néhány kérdés megválaszolásához, és megalapozott döntéseket hozhat.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Nem engedélyezett felhőalapú alkalmazások keresése Cloud Discovery

A fentiekben leírtaknak megfelelően előfordulhat, hogy a szervezet eddig nem kezelt alkalmazásokat.  A leltározási folyamat részeként meg lehet találni a nem engedélyezett felhőalapú alkalmazásokat. Lásd: [Cloud Discovery beállítása](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Alkalmazások integrálása az Azure AD segítségével
A következő cikkek ismertetik az alkalmazások és az Azure AD integrálásának különböző módjait, és útmutatást nyújtanak.

* [A használni kívánt Active Directory meghatározása](../fundamentals/active-directory-whatis.md)
* [Alkalmazások használata az Azure Application Galleryben](what-is-single-sign-on.md)
* [SaaS-alkalmazások integrálása oktatóanyagok listája](../saas-apps/tutorial-list.md)

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Az Azure AD-katalógusban nem szereplő alkalmazások képességei

Hozzáadhat bármely olyan alkalmazást, amely már létezik a szervezetében, vagy bármely harmadik féltől származó alkalmazás olyan szállítótól, amely még nem része az Azure AD-katalógusnak. A [licencszerződéstől](https://azure.microsoft.com/pricing/details/active-directory/)függően a következő lehetőségek érhetők el:

- Bármely olyan alkalmazás önkiszolgáló integrációja, amely támogatja az [Security Assertion Markup Language (SAML) 2,0-](https://wikipedia.org/wiki/SAML_2.0) es identitás-szolgáltatót (SP által kezdeményezett vagy identitásszolgáltató)
- A HTML-alapú bejelentkezési oldallal rendelkező webalkalmazások önkiszolgáló integrációja [jelszó-alapú egyszeri bejelentkezés](sso-options.md#password-based-sso) használatával
- Az olyan alkalmazások önkiszolgáló kapcsolata, amelyek a [rendszert a tartományok közötti Identitáskezelés-felügyeleti (scim) protokoll használatával használják a felhasználók üzembe](../app-provisioning/use-scim-to-provision-users-and-groups.md) helyezéséhez
- Hivatkozás hozzáadása bármely alkalmazáshoz az [Office 365 app launcherben](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) vagy [a saját alkalmazásokban](sso-options.md#linked-sign-on)

Ha az egyéni alkalmazások Azure AD-val való integrálásával kapcsolatos fejlesztői útmutatást keres, tekintse meg az [Azure ad hitelesítési forgatókönyveit](../develop/authentication-vs-authorization.md). Ha olyan alkalmazást fejleszt ki, amely egy modern protokollt használ, például az [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) -t a felhasználók hitelesítéséhez, akkor a Azure Portal [Alkalmazásregisztrációk](../develop/quickstart-register-app.md) felületének használatával regisztrálhatja azt a Microsoft Identity platformon.

### <a name="authentication-types"></a>Hitelesítési típusok
Egyes alkalmazásai eltérő hitelesítési követelményekkel rendelkezhetnek. Az Azure AD-vel az aláírási tanúsítványok az SAML 2,0, a WS-Federation vagy az OpenID Connect protokollokat használó alkalmazásokkal és a jelszó egyszeri bejelentkezésével használhatók. Az alkalmazás hitelesítési típusaival kapcsolatos további információkért lásd: [tanúsítványok kezelése összevont egyszeri Sign-Onhoz Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) és [jelszó alapú egyszeri bejelentkezéssel](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Egyszeri bejelentkezés engedélyezése Azure AD alkalmazás proxyval
A Microsoft Azure AD alkalmazásproxy segítségével biztonságosan, bárhonnan és bármilyen eszközről biztosíthat hozzáférést a magánhálózaton belül található alkalmazásokhoz. Miután telepítette az alkalmazásproxy-összekötőt a környezetén belül, könnyen konfigurálható az Azure AD-vel.

### <a name="integrating-custom-applications"></a>Egyéni alkalmazások integrálása
Ha az egyéni alkalmazást az Azure-alkalmazás-katalógusban szeretné felvenni, tekintse [meg az alkalmazás közzététele az Azure ad alkalmazás-galériában](../develop/v2-howto-app-gallery-listing.md)című témakört.

## <a name="managing-access-to-applications"></a>Az alkalmazásokhoz való hozzáférés kezelése
A következő cikkek az Azure AD-összekötők és az Azure ad-vel való integráció során az alkalmazásokhoz való hozzáférés kezelését ismertetik.

* [Az alkalmazásokhoz való hozzáférés kezelése az Azure AD-vel](what-is-access-management.md)
* [Automatizálás az Azure AD-összekötők révén](../app-provisioning/user-provisioning.md)
* [Felhasználók hozzárendelése egy alkalmazáshoz](./assign-user-or-group-access-portal.md)
* [Csoportok hozzárendelése egy alkalmazáshoz](./assign-user-or-group-access-portal.md)
* [Fiókok megosztása](../enterprise-users/users-sharing-accounts.md)

## <a name="next-steps"></a>Következő lépések
Részletes információk a [githubról](../fundamentals/active-directory-deployment-plans.md)tölthetők le Azure Active Directory telepítési tervek. A Gallery-alkalmazások esetében az egyszeri bejelentkezéshez, a feltételes hozzáféréshez és a [Azure Portalon](https://portal.azure.com)keresztül történő felhasználói kiépítéshez is letölthetők a telepítési csomagok.

Telepítési terv letöltése a Azure Portalról:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **vállalati alkalmazások** válasszon  |  **egy alkalmazás**-  |  **telepítési csomagot**.
