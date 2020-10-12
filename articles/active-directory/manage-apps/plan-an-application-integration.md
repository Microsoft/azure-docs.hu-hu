---
title: Ismerkedés az Azure AD és az alkalmazások integrálásával | Microsoft Docs
description: Ez a cikk egy első lépéseket ismertető útmutató a Azure Active Directory (AD) helyszíni alkalmazásokkal és felhőalapú alkalmazásokkal való integrálásához.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc691148723e610f0885f7c20fd8684f11a81dc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89182326"
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
* Az integrálás előtt törölnie kell a felhasználó/csoport adatbázisait?  (Ez egy nagyon fontos kérdés. Bekerülés a szemétbe.)

### <a name="access-management-inventory"></a>Hozzáférés-kezelési leltár
* Hogyan kezelheti jelenleg az alkalmazásokhoz való felhasználói hozzáférést? Ezt módosítani kell?  Más módszerekkel kezelheti a hozzáférést, például az [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) például?
* Kinek van hozzáférése a következőhöz?

Lehet, hogy nem rendelkezik a válaszokkal az összes kérdésre előre, de ez rendben van.  Ez az útmutató segítséget nyújt néhány kérdés megválaszolásához, és megalapozott döntéseket hozhat.

### <a name="find-unsanctioned-cloud-applications-with-cloud-discovery"></a>Nem engedélyezett felhőalapú alkalmazások keresése Cloud Discovery

A fentiekben leírtaknak megfelelően előfordulhat, hogy a szervezet eddig nem kezelt alkalmazásokat.  A leltározási folyamat részeként meg lehet találni a nem engedélyezett felhőalapú alkalmazásokat. Lásd: [Cloud Discovery beállítása](/cloud-app-security/set-up-cloud-discovery).

## <a name="integrating-applications-with-azure-ad"></a>Alkalmazások integrálása az Azure AD segítségével
A következő cikkek ismertetik az alkalmazások és az Azure AD integrálásának különböző módjait, és útmutatást nyújtanak.

* [A használni kívánt Active Directory meghatározása](../fundamentals/active-directory-administer.md)
* [Alkalmazások használata az Azure Application Galleryben](what-is-single-sign-on.md)
* [SaaS-alkalmazások integrálása oktatóanyagok listája](../active-directory-saas-tutorial-list.md)

### <a name="authentication-types"></a>Hitelesítési típusok
Egyes alkalmazásai eltérő hitelesítési követelményekkel rendelkezhetnek. Az Azure AD-vel az aláírási tanúsítványok az SAML 2,0, a WS-Federation vagy az OpenID Connect protokollokat használó alkalmazásokkal, valamint a jelszó egyszeri bejelentkezéssel használhatók. Az Azure AD-vel használható alkalmazás-hitelesítési típusokkal kapcsolatos további információkért lásd: az [összevont egyszeri Sign-On tanúsítványainak kezelése Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) és [jelszó alapú egyszeri bejelentkezéssel](what-is-single-sign-on.md).

### <a name="enabling-sso-with-azure-ad-app-proxy"></a>Egyszeri bejelentkezés engedélyezése Azure AD alkalmazás proxyval
A Microsoft Azure AD alkalmazásproxy segítségével biztonságosan, bárhonnan és bármilyen eszközről biztosíthat hozzáférést a magánhálózaton belül található alkalmazásokhoz. Miután telepítette az alkalmazásproxy-összekötőt a környezetén belül, könnyen konfigurálható az Azure AD-vel.

### <a name="integrating-custom-applications"></a>Egyéni alkalmazások integrálása
Ha új alkalmazást ír, és szeretné segíteni a fejlesztőket az Azure AD hatékonyságának kihasználásában, tekintse meg a következő témakört: a [fejlesztők Guiding](../active-directory-applications-guiding-developers-for-lob-applications.md).

Ha egyéni alkalmazását szeretné felvenni az Azure-alkalmazás-katalógusba, tekintse meg a ["saját alkalmazás használata" kifejezést az Azure AD Self-Service SAML-konfigurációval](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).

## <a name="managing-access-to-applications"></a>Az alkalmazásokhoz való hozzáférés kezelése
A következő cikkek az Azure AD-összekötők és az Azure ad-vel való integráció során az alkalmazásokhoz való hozzáférés kezelését ismertetik.

* [Az alkalmazásokhoz való hozzáférés kezelése az Azure AD-vel](what-is-access-management.md)
* [Automatizálás az Azure AD-összekötők révén](../app-provisioning/user-provisioning.md)
* [Felhasználók hozzárendelése egy alkalmazáshoz](../active-directory-applications-guiding-developers-assigning-users.md)
* [Csoportok hozzárendelése egy alkalmazáshoz](../active-directory-applications-guiding-developers-assigning-groups.md)
* [Fiókok megosztása](../active-directory-sharing-accounts.md)

## <a name="next-steps"></a>Következő lépések
Részletes információk a [githubról](https://aka.ms/deploymentplans)tölthetők le Azure Active Directory telepítési tervek. A Gallery-alkalmazások esetében az egyszeri bejelentkezéshez, a feltételes hozzáféréshez és a [Azure Portalon](https://portal.azure.com)keresztül történő felhasználói kiépítéshez is letölthetők a telepítési csomagok. 

Telepítési terv letöltése a Azure Portalról:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **vállalati alkalmazások**válasszon  |  **egy alkalmazás**-  |  **telepítési csomagot**.

Kérjük, küldjön visszajelzést az üzembe helyezési tervekről az [üzembe helyezési terv felmérésének](https://aka.ms/DeploymentPlanFeedback)megadásával.
