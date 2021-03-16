---
title: Regisztráció és bejelentkezés beállítása Facebook-fiókkal
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C használatával az alkalmazásokban Facebook-fiókkal rendelkező felhasználók számára biztosíthatja a regisztrációt és a bejelentkezést.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 49abd2cc62ff7a2eab3d95265f3db8f5c894ebb6
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488941"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Facebook-fiókkal Azure Active Directory B2C használatával

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Facebook-alkalmazás létrehozása

Ha Azure Active Directory B2C (Azure AD B2C) Facebook-fiókkal rendelkező felhasználók számára szeretné engedélyezni a bejelentkezést, létre kell hoznia egy alkalmazást a [Facebook-alkalmazás irányítópultján](https://developers.facebook.com/). További információ: [alkalmazásfejlesztés](https://developers.facebook.com/docs/development). Ha még nem rendelkezik Facebook-fiókkal, regisztrálhat a következő címen: [https://www.facebook.com/](https://www.facebook.com/) .

1. A Facebook-fiók hitelesítő adataival jelentkezzen be a [facebookba a fejlesztők számára](https://developers.facebook.com/) .
1. Ha még nem tette meg, regisztrálnia kell Facebook-fejlesztőként. Ehhez válassza az első **lépések** lehetőséget az oldal jobb felső sarkában, fogadja el a Facebook szabályzatait, és végezze el a regisztráció lépéseit.
1. Válassza **a saját alkalmazások** lehetőséget, majd **hozza létre az alkalmazást**.
1. Válassza a **csatlakoztatott élmények létrehozása** lehetőséget.
1. Adjon meg egy **megjelenítendő nevet** és egy érvényes **kapcsolattartási e-mailt**.
1. Válassza az **alkalmazás-azonosító létrehozása** lehetőséget. Előfordulhat, hogy el kell fogadnia a Facebook-platform szabályzatait, és el kell végeznie egy online biztonsági ellenőrzését.
1. Válassza a **Beállítások**  >  **alapszintű** lehetőséget.
    1. Válasszon egy **kategóriát**, például: `Business and Pages` . Ez az érték a Facebook számára szükséges, de Azure AD B2C nem használható.
    1. Adja meg az URL-címet a **szolgáltatási feltételek URL**-címéhez, például: `http://www.contoso.com/tos` . A szabályzat URL-címe egy olyan oldal, amelyet fenntart az alkalmazás használati feltételeinek megadásához.
    1. Adja meg az **adatvédelmi szabályzat URL**-címét (például `http://www.contoso.com/privacy` ). A szabályzat URL-címe olyan oldal, amelyet az alkalmazásra vonatkozó adatvédelmi információk biztosítására tart fenn.
1. A lap alján válassza a **platform hozzáadása** lehetőséget, majd válassza a **webhely** lehetőséget.
1. A **webhely URL**-címe mezőben adja meg a webhely címét, például: `https://contoso.com` . 
1. Válassza a **módosítások mentése** lehetőséget.
1. Az oldal tetején másolja az **alkalmazás-azonosító** értékét.
1. Válassza az **alkalmazás titkos kulcsának** **megjelenítése** és másolása lehetőséget. Mindkettőt használja a Facebook identitás-szolgáltatóként való konfigurálásához a bérlőben. Az **alkalmazás titkos kulcsa** fontos biztonsági hitelesítő adat.
1. A menüben válassza ki a **termékek** melletti **plusz** jelet. A **termékek hozzáadása az alkalmazáshoz** területen válassza a **beállítás** a **Facebook-Bejelentkezés** alatt lehetőséget.
1. A menüben válassza a **Facebook-Bejelentkezés** lehetőséget, majd válassza a **Beállítások** lehetőséget.
1. Az **érvényes OAuth átirányítási URI**-k mezőben adja meg a értéket `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Ha [Egyéni tartományt](custom-domain.md)használ, írja be a értéket `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Cserélje le a `your-tenant-name` nevet a bérlő nevére, és az `your-domain-name` Egyéni tartományra. 
1. A lap alján kattintson a **módosítások mentése** gombra.
1. Ha szeretné, hogy a Facebook-alkalmazás elérhető legyen a Azure AD B2C számára, válassza ki a lap jobb felső sarkában található állapotjelzőt, és kapcsolja be **, hogy az** alkalmazás nyilvános legyen, majd válassza a **váltás mód** lehetőséget.  Ezen a ponton az állapotnak a **fejlesztéstől** az **élő** értékre kell váltania.

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>A Facebook konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **Facebook** lehetőséget.
1. Adjon meg egy **nevet**. Például: *Facebook*.
1. Az **ügyfél-azonosító** mezőben adja meg a korábban létrehozott Facebook-alkalmazás alkalmazás-azonosítóját.
1. Az **ügyfél titka** mezőben adja meg a rögzített alkalmazás titkát.
1. Kattintson a **Mentés** gombra.

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>Facebook-identitás szolgáltatójának hozzáadása felhasználói folyamathoz 

1. A Azure AD B2C-bérlőben válassza a **felhasználói folyamatok** lehetőséget.
1. Kattintson arra a felhasználói folyamatra, amelyhez hozzá szeretné adni a Facebook-identitás szolgáltatóját.
1. A **közösségi identitás-szolgáltatók** területen válassza a **Facebook** lehetőséget.
1. Kattintson a **Mentés** gombra.
1. A szabályzat teszteléséhez válassza a **felhasználói folyamat futtatása** lehetőséget.
1. Az **alkalmazás** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **felhasználói folyamat futtatása** gombra.
1. A regisztrációs vagy bejelentkezési oldalon válassza a **Facebook** lehetőséget a Facebook-fiókkal való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Házirend-kulcs létrehozása

A Azure AD B2C bérlőben korábban rögzített alkalmazás-titkos kulcsot kell tárolnia.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki a bérlőt tartalmazó könyvtárat.
3. Válassza ki az **összes szolgáltatást** a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
4. Az Áttekintés lapon válassza az **identitási élmény keretrendszert**.
5. Válassza a **szabályzat kulcsok** lehetőséget, majd kattintson a **Hozzáadás** gombra.
6. A **Beállítások** területen válassza a lehetőséget `Manual` .
7. Adja meg a szabályzat kulcsának **nevét** . Például: `FacebookSecret`. A rendszer automatikusan hozzáadja az előtagot a `B2C_1A_` kulcs nevéhez.
8. A **Secret (titkos** kód) mezőben adja meg az alkalmazás korábban rögzített titkát.
9. A **kulcshasználat** beállításnál válassza a elemet `Signature` .
10. Kattintson a **Létrehozás** lehetőségre.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Facebook-fiók konfigurálása identitás-szolgáltatóként

1. A `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** fájlban cserélje le a `client_id` Facebook-alkalmazás azonosítóját a (z) értékre:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>A szabályzat feltöltése és tesztelése

Frissítse a függő entitás (RP) fájlját, amely kezdeményezi a létrehozott felhasználói utat.

1. Töltse fel a *TrustFrameworkExtensions.xml* fájlt a bérlőbe.
1. Az **Egyéni házirendek** területen válassza a **B2C_1A_signup_signin** lehetőséget.
1. Az **alkalmazás kiválasztása** lapon válassza ki a korábban regisztrált *testapp1* nevű webalkalmazást. A **Válasz URL-címének** meg kell jelennie `https://jwt.ms` .
1. Kattintson a **Futtatás most** gombra.
1. A regisztrációs vagy bejelentkezési oldalon válassza a **Facebook** lehetőséget a Facebook-fiókkal való bejelentkezéshez.

Ha a bejelentkezési folyamat sikeres, a rendszer átirányítja a böngészőt `https://jwt.ms` , amely a Azure ad B2C által visszaadott jogkivonat tartalmát jeleníti meg.

::: zone-end

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [adhat hozzá Facebook-tokent az alkalmazásához](idp-pass-through-user-flow.md).
