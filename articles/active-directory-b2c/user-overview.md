---
title: A felhasználói fiókok áttekintése a Azure Active Directory B2C
description: Ismerje meg, hogy milyen típusú felhasználói fiókok használhatók a Azure Active Directory B2C.
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
ms.openlocfilehash: 4b35cfeded13a50e5e27c240b0826f1d108ff7eb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529450"
---
# <a name="overview-of-user-accounts-in-azure-active-directory-b2c"></a>A felhasználói fiókok áttekintése a Azure Active Directory B2C

A Azure Active Directory B2C (Azure AD B2C) számos fióktípust lehet létrehozni. Azure Active Directory, Active Directory B2B- és Active Directory B2C megoszthatja a használható felhasználói fiókok típusaiban.

A következő típusú fiókok érhetők el:

- **Munkahelyi fiók** – A munkahelyi fiókok hozzáférhetnek a bérlők erőforrásaihoz, rendszergazdai szerepkörük pedig kezelheti a bérlőket.
- **Vendégfiók** – A vendégfiókok csak Microsoft-fiók vagy Azure Active Directory, amelyek alkalmazások elérésére vagy bérlők kezelésére használhatók.
- **Fogyasztói fiók** – A felhasználói fiókot a fiókkal regisztrált alkalmazások felhasználója Azure AD B2C. A fogyasztói fiókokat a következővel lehet létrehozni:
  - A felhasználó egy bejelentkezési felhasználói folyamaton megy keresztül egy Azure AD B2C alkalmazásban
  - A Microsoft Graph API használata
  - Az Azure Portal használata

## <a name="work-account"></a>Munkahelyi fiók

A munkahelyi fiók létrehozása az Azure AD-n alapuló összes bérlő számára ugyanúgy megegyezik. Munkahelyi fiók létrehozásához használja az Új felhasználók hozzáadása az alkalmazáshoz – rövid útmutatóban [Azure Active Directory.](../active-directory/fundamentals/add-users-azure-active-directory.md) Létrejön egy munkahelyi fiók az Új **felhasználó lehetőség használatával** a Azure Portal.

Új munkahelyi fiók hozzáadásakor figyelembe kell vennie a következő konfigurációs beállításokat:

- **Név** és **felhasználónév –** A **Név** tulajdonság tartalmazza a felhasználó megadott és vezetéknevét. A **Felhasználónév** az az azonosító, amit a felhasználó a bejelentkezéshez beír. A felhasználónév a teljes tartományt tartalmazza. A felhasználónév tartománynév részének vagy a kezdeti alapértelmezett tartománynévnek *(your-domain.onmicrosoft.com)* kell lennie, vagy [](../active-directory/fundamentals/add-custom-domain.md) egy ellenőrzött, nem összevont egyéni tartománynévnek, például az *contoso.com.* 
- **E-mail** – Az új felhasználó e-mail-címmel is bejelentkezhet. Az e-mailekben nem támogatjuk a speciális karaktereket és a többbájtos karaktereket, például a japán karaktereket.
- **Profil** – A fiók felhasználói adatok profiljával van beállítva. Megadhatja a vezetéknevet, a vezetéknevet, a beosztást és a részleg nevét. A profilt a fiók létrehozása után szerkesztheti.
- **Csoportok** – Csoportokkal olyan felügyeleti feladatokat hajthat végre, mint például licencek vagy engedélyek egyszerre több felhasználóhoz vagy eszközhez való hozzárendelése. Az új fiókot a bérlő egy [meglévő](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) csoportjába is be lehet tenni.
- **Címtár-szerepkör** – Meg kell adnia, hogy a felhasználói fiók milyen szintű hozzáféréssel rendelkezik a bérlő erőforrásaihoz. A következő engedélyszintek érhetők el:

    - **Felhasználó** – A felhasználók hozzáférhetnek a hozzárendelt erőforrásokhoz, de a legtöbb bérlői erőforrást nem kezelhetik.
    - **globális rendszergazda –** A globális rendszergazdák teljes körűen szabályozhatják az összes bérlői erőforrást.
    - **Korlátozott rendszergazda** – Válassza ki a felhasználó rendszergazdai szerepkörét vagy szerepköreit. További információ a kiválasztható szerepkörökről: Rendszergazdai szerepkörök hozzárendelése a [Azure Active Directory.](../active-directory/roles/permissions-reference.md)

### <a name="create-a-work-account"></a>Munkahelyi fiók létrehozása

Az alábbi információk alapján hozhat létre új munkahelyi fiókot:

- [Azure Portal](../active-directory/fundamentals/add-users-azure-active-directory.md)
- [Microsoft Graph](/graph/api/user-post-users)

### <a name="update-a-user-profile"></a>Felhasználói profil frissítése

A következő információk segítségével frissítheti egy felhasználó profilját:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

### <a name="reset-a-password-for-a-user"></a>Felhasználó jelszavának visszaállítása

A felhasználó jelszavának visszaállításához a következő információkat használhatja:

- [Azure Portal](../active-directory/fundamentals/active-directory-users-reset-password-azure-portal.md)
- [Microsoft Graph](/graph/api/user-update)

## <a name="guest-user"></a>Vendégfelhasználó

Vendégfelhasználóként meghívhat külső felhasználókat a bérlőjébe. Az adminisztrációs feladatok megosztása egy tipikus forgatókönyv, amikor vendégfelhasználót Azure AD B2C a bérlőjébe. A vendégfiókok használatának példájért lásd: Egy Azure Active Directory [B2B együttműködési felhasználó tulajdonságai.](../active-directory/external-identities/user-properties.md)

Amikor meghív egy vendégfelhasználót a bérlőjébe, meg kell adnia a címzett e-mail-címét és a meghívót leíró üzenetet. A meghívó hivatkozás a hozzájárulási oldalra viszi a felhasználót. Ha egy beérkezett üzenet nincs csatolva az e-mail-címhez, a felhasználó úgy navigálhat a hozzájárulási oldalra, hogy a meghívott hitelesítő adatokkal a Microsoft egyik oldalára navigál. A felhasználónak ezután ugyanúgy kell beváltani a meghívót, mint az e-mailben található hivatkozásra kattintva. Példa: `https://myapps.microsoft.com/B2CTENANTNAME`.

Vendégfelhasználó meghívásához Microsoft Graph [API-t](/graph/api/invitation-post) is használhatja.

## <a name="consumer-user"></a>Fogyasztói felhasználó

A fogyasztó felhasználó bejelentkezhet az Azure AD B2C által védett alkalmazásokba, de nem férhet hozzá az Olyan Azure-erőforrásokhoz, mint Azure Portal. A fogyasztó felhasználó használhat helyi fiókot vagy összevont fiókokat, például a Facebookot vagy a Twittert. A felhasználói fiók létrehozása egy bejelentkezési vagy bejelentkezési felhasználói folyamat, [a](user-flow-overview.md)Microsoft Graph API vagy a Azure Portal.

Megadhatja a felhasználói felhasználói fiók létrehozásakor gyűjtött adatokat. További információ: [Felhasználói attribútumok hozzáadása és felhasználói bemenet testreszabása.](configure-user-input.md)

A fogyasztói fiókok kezelésével kapcsolatos további információkért lásd: [Felhasználói Azure AD B2C kezelése a Microsoft Graph.](./microsoft-graph-operations.md)

### <a name="migrate-consumer-user-accounts"></a>Fogyasztói felhasználói fiókok áttelepítése

Előfordulhat, hogy meglévő felhasználói felhasználói fiókokat kell mi egyik identitásszolgáltatóból a Azure AD B2C. További információ: [Felhasználók migrálása az Azure AD B2C-be](user-migration.md).
