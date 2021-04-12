---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a WhoIAM használatával
titleSuffix: Azure AD B2C
description: Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja Azure AD B2C hitelesítését a WhoIAM használatával a felhasználók ellenőrzéséhez.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 32c288476c8491747cc80f0115daa72919aa05a8
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257738"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Oktatóanyag a WhoIAM konfigurálásához a Azure Active Directory B2C

Ebben a példában útmutatást adunk arról, hogyan konfigurálhatja a [WhoIAM](https://www.whoiam.ai/brims/) márkás Identitáskezelés rendszerét (BRIMS) a környezetében, és integrálhatja azt Active Directory B2C (Azure ad B2C) használatával.

A BRIMS az Ön környezetében üzembe helyezett alkalmazások és szolgáltatások összessége. Ez biztosítja a felhasználói bázis hang-, SMS-és e-mail-ellenőrzését. A BRIMS a meglévő identitás-és hozzáférés-kezelési megoldással együtt működik, és a platform agnosztikus.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Egy Azure ad B2C bérlő](./tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

- Egy WhoIAM [próbaverziós fiók](https://www.whoiam.ai/contact-us/).

## <a name="scenario-description"></a>Forgatókönyv leírása

A WhoIAM-integráció a következő összetevőket tartalmazza:

- Azure AD B2C bérlő. Ez az engedélyezési kiszolgáló, amely a felhasználó hitelesítő adatait ellenőrzi a megadott egyéni szabályzatok alapján. Más néven identitás-szolgáltató.

- Felügyeleti portál az ügyfelek és azok konfigurációinak kezeléséhez.

- Egy API-szolgáltatás, amely a különböző szolgáltatásokat a végpontokon keresztül teszi elérhetővé.  

- Azure Cosmos DB, amely a BRIMS felügyeleti portál és az API szolgáltatás hátterében is működik.

A következő architektúra-diagram a megvalósítást mutatja be.

![A Azure AD B2C integráció architektúrájának ábrája a WhoIAM-mel.](media/partner-whoiam/whoiam-architecture-diagram.png)

|Lépés | Description |
|:-----| :-----------|
| 1. | A felhasználó egy lapon megérkezik a regisztrációs vagy bejelentkezési kérés elindítására olyan alkalmazásra, amely a Azure AD B2C identitás-szolgáltatóként használja.
| 2. | A hitelesítés részeként a felhasználó a saját e-mail-címe vagy telefonja tulajdonjogának ellenőrzésére, illetve a hangjuk biometrikus ellenőrzési tényezőként való felhasználására kéri.  
| 3. | Azure AD B2C kezdeményezi a BRIMS API szolgáltatás hívását, és továbbítja a felhasználó e-mail-címét, telefonszámát és hangfelvételét.
| 4. | A BRIMS előre definiált konfigurációkat használ, például teljes mértékben testreszabható e-maileket és SMS-sablonokat, hogy az alkalmazás stílusával konzisztens módon kommunikáljon a felhasználóval a saját nyelvén.
| 5. | A felhasználó személyazonosságának ellenőrzése után a BRIMS egy tokent ad vissza Azure AD B2C az ellenőrzés eredményének jelzéséhez. Azure AD B2C ezután engedélyezi a felhasználónak az alkalmazáshoz való hozzáférést, vagy a hitelesítési kísérletük meghiúsul.  

## <a name="sign-up-with-whoiam"></a>Regisztráció a WhoIAM

1. Forduljon a [WhoIAM](https://www.whoiam.ai/contact-us/) -hez, és hozzon létre egy BRIMS-fiókot.

2. Használja az Ön számára elérhető regisztrációs irányelveket, és konfigurálja a következő Azure-szolgáltatásokat:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): a jelszavak biztonságos tárolásához használatos, például a levelezési szolgáltatás jelszavai.

    - [Azure app Service](https://azure.microsoft.com/services/app-service/): a BRIMS API és a felügyeleti portál szolgáltatásainak üzemeltetésére szolgál.

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/): a rendszergazda felhasználók hitelesítésére szolgál a felügyeleti portálon.

    - [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/): a beállítások tárolására és lekérésére szolgál.

    - [Application Insights](../azure-monitor/app/app-insights-overview.md) (nem kötelező): az API-ra és a felügyeleti portálra való bejelentkezéshez használatos.

3. Telepítse a BRIMS API-t és a BRIMS felügyeleti portált az Azure-környezetben.

4. A BRIMS regisztrációs dokumentációjában Azure AD B2C egyéni házirend-minták érhetők el. Az alkalmazás konfigurálásához és a BRIMS platform felhasználói identitás-ellenőrzéshez való használatához kövesse a dokumentációt.  

A WhoIAM BRIMS kapcsolatos további információkért tekintse meg a [termék dokumentációját](https://www.whoiam.ai/brims/).

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt. A **szabályzatok** területen válassza az **identitási élmény keretrendszere** elemet.

2. Válassza ki a korábban létrehozott **SignUpSignIn**.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, majd:

   a. **Alkalmazás** esetén válassza ki a regisztrált alkalmazást (a minta a JWT).

   b. A **Válasz URL-cím** mezőben válassza ki az **átirányítási URL-címet**.

   c. Válassza a **felhasználói folyamat futtatása** lehetőséget.

4. Ugorjon végig a regisztrációs folyamaton, és hozzon létre egy fiókot.

5. A rendszer a BRIMS szolgáltatást a folyamat során hívja meg a felhasználói attribútum létrehozása után. Ha a folyamat hiányos, ellenőrizze, hogy a felhasználó nincs-e mentve a címtárban.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)