---
title: A Azure Active Directory B2C felhasználói fiókjainak áttekintése
description: Tudnivalók a Azure Active Directory B2C használható felhasználói fiókok típusairól.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: f2473955db5439911280360b169f469b8c93eb93
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105043597"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>A Azure Active Directory B2C felhasználói fiókjainak áttekintése

Azure Active Directory B2C (Azure AD B2C) számos különböző típusú fiókot hozhat létre. Azure Active Directory, Active Directory B2B és Active Directory B2C megosztás a használható felhasználói fiókok típusaiban.

A következő típusú fiókok érhetők el:

- **Munkahelyi fiók** – a munkahelyi fiók hozzáférhet a bérlő erőforrásaihoz, és rendszergazdai szerepkörrel is kezelheti a bérlőket.
- **Vendégfiók** – a vendég fiók csak olyan Microsoft-fiók vagy Azure Active Directory felhasználó lehet, amely használható az alkalmazások eléréséhez vagy a bérlők kezeléséhez.
- **Fogyasztói fiók** – a Azure ad B2C regisztrált alkalmazások felhasználója használja a fogyasztói fiókot. A felhasználói fiókokat a alábbiakkal hozhatja létre:
  - A felhasználó egy Azure AD B2C alkalmazásban egy regisztrációs felhasználói folyamaton keresztül zajlik
  - Microsoft Graph API használata
  - Az Azure Portal használata

## <a name="work-account"></a>Munkahelyi fiók

A munkahelyi fiókokat ugyanúgy hozza létre a rendszer az összes bérlő számára az Azure AD-on alapuló módon. Munkahelyi fiók létrehozásához használhatja a gyors útmutató [: új felhasználók hozzáadása Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md). A munkahelyi fiók az **új felhasználó** választása alapján jön létre a Azure Portal.

Új munkahelyi fiók hozzáadásakor figyelembe kell vennie a következő konfigurációs beállításokat:

- **Név** és **Felhasználónév** – a **Name (név** ) tulajdonság a felhasználó megadott és vezetéknevét tartalmazza. A **Felhasználónév** annak az azonosítónak a neve, amelyet a felhasználó be kell jelentkeznie. A Felhasználónév tartalmazza a teljes tartományt. A Felhasználónév tartománynév részének a kezdeti alapértelmezett tartománynév *Your-Domain.onmicrosoft.com* vagy egy ellenőrzött, nem összevont [Egyéni tartománynévnek](../active-directory/fundamentals/add-custom-domain.md) (például *contoso.com*) kell lennie. 
- **E-mail** – az új felhasználó egy e-mail-cím használatával is bejelentkezhet. Nem támogatunk speciális karaktereket vagy többbájtos karaktereket az e-mailekben, például Japán karaktereket.
- **Profil** – a fiók felhasználói adatprofillal van beállítva. Lehetősége van megadnia az utónév, a vezetéknév, a beosztás és a részleg nevét. A profilt a fiók létrehozása után módosíthatja.
- **Csoportok** – a csoportok használatával olyan felügyeleti feladatokat hajthat végre, mint például licencek vagy engedélyek kiosztása sok felhasználóhoz vagy eszközhöz. Az új fiókot egy meglévő [csoportba](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) helyezheti a bérlőben.
- **Directory-szerepkör** – meg kell adnia azt a hozzáférési szintet, amelyet a felhasználói fióknak a bérlő erőforrásaihoz kell használnia. A következő jogosultsági szintek érhetők el:

    -  A felhasználók hozzáférhetnek a hozzárendelt erőforrásokhoz, de nem kezelhetik a legtöbb bérlői erőforrást.
    - **Globális rendszergazda** – a globális rendszergazdák teljes hozzáféréssel rendelkeznek az összes bérlői erőforráshoz.
    - **Korlátozott rendszergazda** – válassza ki a felhasználó rendszergazdai szerepkörét vagy szerepköreit. További információ a kiválasztható szerepkörökről: [rendszergazdai szerepkörök kiosztása a Azure Active Directoryban](../active-directory/roles/permissions-reference.md).

### <a name="create-a-work-account"></a>Munkahelyi fiók létrehozása

Új munkahelyi fiók létrehozásához a következő információkat használhatja:

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users)

### <a name="update-a-user-profile"></a>Felhasználói profil frissítése

A következő információk segítségével frissítheti a felhasználó profilját:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

### <a name="reset-a-password-for-a-user"></a>Felhasználó jelszavának alaphelyzetbe állítása

A felhasználó jelszavának alaphelyzetbe állításához a következő információkat használhatja:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

## <a name="guest-user"></a>Vendég felhasználó

Meghívhatja a külső felhasználókat a bérlőnek vendég felhasználóként. Egy tipikus példa arra, hogy a vendég felhasználókat meghívja a Azure AD B2C bérlőre, hogy megossza az adminisztrációs feladatokat. A vendég fiók használatára példát a [Azure Active Directory B2B együttműködési felhasználó tulajdonságai](../active-directory/external-identities/user-properties.md)című témakörben talál.

Amikor meghívja a vendég felhasználóját a bérlőre, megadja a címzett e-mail-címét, valamint egy, a meghívót leíró üzenetet. A meghívó hivatkozás a felhasználót a belefoglaló oldalára viszi. Ha a beérkezett fájlok nem az e-mail-címre vannak csatolva, a felhasználó a meghívott hitelesítő adatokkal megnyitva egy Microsoft-oldalra navigálva megkeresheti a beleegyezik lapot. A felhasználónak ezután meg kell váltania a meghívót úgy, hogy az e-mailben szereplő hivatkozásra kattint. Példa: `https://myapps.microsoft.com/B2CTENANTNAME`.

Használhatja a [Microsoft Graph API](/graph/api/invitation-post?view=graph-rest-beta) -t is egy vendég felhasználó meghívásához.

## <a name="consumer-user"></a>Fogyasztói felhasználó

A felhasználó bejelentkezhet Azure AD B2C által védett alkalmazásokba, de nem fér hozzá az Azure-erőforrásokhoz, például a Azure Portalhoz. A fogyasztói felhasználó használhat helyi fiókot vagy összevont fiókot, például a Facebookot vagy a Twittert. A felhasználói fiók a Microsoft Graph API használatával vagy a Azure Portal használatával jön létre a [regisztrációs vagy bejelentkezési felhasználói folyamat](user-flow-overview.md)használatával.

Megadhatja a fogyasztói felhasználói fiók létrehozásakor gyűjtött adatokat. További információ: [felhasználói attribútumok hozzáadása és felhasználói bevitel testreszabása](configure-user-input.md).

A fogyasztói fiókok kezelésével kapcsolatos további információkért lásd: [Azure ad B2C felhasználói fiókok kezelése Microsoft Graphokkal](./microsoft-graph-operations.md).

### <a name="migrate-consumer-user-accounts"></a>Fogyasztói felhasználói fiókok migrálása

Előfordulhat, hogy a meglévő felhasználói fiókokat át kell telepítenie bármely identitás-szolgáltatótól a Azure AD B2Cba. További információ: [Felhasználók migrálása az Azure AD B2C-be](user-migration.md).
