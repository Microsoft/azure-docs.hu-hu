---
title: Oktatóanyag a Azure Active Directory B2C konfigurálásához a HYPR használatával
titleSuffix: Azure AD B2C
description: Oktatóanyag a Azure Active Directory B2C és a Hypr konfigurálásához a valódi jelszóval nem rendelkező erős ügyfél-hitelesítéshez
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b80b1a4b3f9bcde6cf01b0e0e59425c6783bd5d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953763"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Oktatóanyag a HYPR konfigurálásához a Azure Active Directory B2C

Ebben a példában a Azure AD B2C és a [HYPR](https://get.hypr.com)konfigurálását ismertető oktatóanyag nyújt útmutatást. A Azure AD B2C identitás-szolgáltatóként integrálhatja az HYPR-ket bármely ügyfél-alkalmazással, hogy valódi jelszó nélküli hitelesítést nyújtson a felhasználóknak. A HYPR a jelszavakat a nyilvános kulcsú titkosítással váltja fel, kiküszöbölve a csalást, az adathalászatot és a hitelesítő adatokat

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Azure ad B2C bérlő](./tutorial-create-tenant.md). A bérlő az Azure-előfizetéshez van csatolva.

- Egy HYPR Felhőbeli bérlő, ingyenes [próbaverziós fiók](https://get.hypr.com/free-trial)beszerzése.

- A HYPR REST API-kkal vagy a HYPR Eszközkezelő a HYPR-bérlőn keresztül regisztrált felhasználó mobil eszköze. A feladat elvégzéséhez például használhatja a [HYPR Java SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) -t.

## <a name="scenario-description"></a>Forgatókönyv leírása

A HYRP-integráció a következő összetevőket tartalmazza:

- Azure AD B2C – az engedélyezési kiszolgáló, amely a felhasználó hitelesítő adatainak (más néven identitás-szolgáltató) ellenőrzéséhez felelős.

- Webes és mobil alkalmazások – a HYPR és a Azure AD B2C védelemmel ellátott mobil-vagy webalkalmazások. A HYPR egy robusztus mobil SDK-t is biztosít, amely az iOS-és Android-platformokon használható valódi, jelszóval nem rendelkező hitelesítéshez.

- A HYPR Mobile App – a HYPR Mobile alkalmazás használható a minta végrehajtásához, ha inkább a saját mobil alkalmazásaiban szeretné használni a mobil SDK-kat.

- HYPR REST API-k – a HYPR API-k használatával mind a felhasználó-eszköz regisztrációját, mind a hitelesítést használhatja. Ezek az API-k [itt](https://apidocs.hypr.com)találhatók.

A következő architektúra-diagram a megvalósítást mutatja be.

![Képernyőkép a hypr-Architecture-diagramról](media/partner-hypr/hypr-architecture-diagram.png)

|Lépés | Leírás |
|:-----| :-----------|
| 1. | A felhasználó egy bejelentkezési oldalon érkezik. A felhasználók kiválaszthatják a bejelentkezést és a regisztrációt, és beírhatják a felhasználónevet az oldalra.
| 2. | Az alkalmazás elküldi a felhasználói attribútumokat Azure AD B2C az ellenőrzés azonosításához.
| 3. | Azure AD B2C gyűjti a felhasználói attribútumokat, és elküldi az attribútumokat a HYPR, hogy hitelesítse a felhasználót a HYPR Mobile alkalmazáson keresztül.
| 4. | A HYPR leküldéses értesítést küld a regisztrált felhasználói mobileszköz számára egy gyors identitású online (pont) hitelesített hitelesítéshez. Ez lehet egy felhasználói ujj nyomtatása, biometrikus vagy decentralizált PIN-kód.  
| 5. | Miután a felhasználó tudomásul veszi a leküldéses értesítést, a felhasználó az ellenőrzési eredmények alapján engedélyezi vagy megtagadja a hozzáférést az ügyfélalkalmazás számára.

## <a name="configure-the-azure-ad-b2c-policy"></a>A Azure AD B2C házirend konfigurálása

1. Nyissa meg a házirend mappában található [Azure ad B2C HYPR szabályzatot](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy) .

2. A [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) letöltéséhez kövesse ezt a [dokumentumot](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack)

3. Konfigurálja a Azure AD B2C bérlő házirendjét.

>[!NOTE]
>Frissítse a megadott szabályzatokat az adott bérlőhöz kapcsolódóan.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a házirendek területen válassza az **identitási élmény keretrendszere** elemet.

2. Válassza ki a korábban létrehozott **SignUpSignIn**.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget, és válassza ki a beállításokat:

   a. **Alkalmazás**: válassza ki a regisztrált alkalmazást (minta a JWT)

   b. **Válasz URL-címe**: válassza ki az **átirányítási URL-címet**

   c. Válassza a **felhasználói folyamat futtatása** lehetőséget.

4. Ugorjon a regisztrációs folyamatra, és hozzon létre egy fiókot

5. A HYPR a folyamat során hívja meg a rendszer a felhasználói attribútum létrehozása után. Ha a folyamat hiányos, ellenőrizze, hogy a felhasználó nincs-e mentve a címtárban.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](./custom-policy-get-started.md?tabs=applications)