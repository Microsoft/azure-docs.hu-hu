---
title: Útmutató a Kulcsnélkül konfigurálásához a Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Oktatóanyag a kulcsnélküli Azure Active Directory B2C a jelszó nélküli hitelesítéshez való konfigurálásához
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 006bcc6c0cffe6f7c08b16057325a499e44f06be
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257908"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>Oktatóanyag: a kulcsnélküli konfiguráció konfigurálása Azure Active Directory B2C

Ebben a példában útmutatást adunk a Azure Active Directory (AD) B2C és a [kulcsnélküli](https://keyless.io/)beállítások konfigurálásához. Ha a Azure AD B2C identitás-szolgáltatóként, a felhasználók számára valódi jelszó nélküli hitelesítést biztosíthat az ügyfelek alkalmazásaival.

A kulcsnélküli megoldás **kulcsnélküli Zero-Knowledge biometrikus (ZKB™)** olyan jelszó nélküli többtényezős hitelesítést biztosít, amely kiküszöböli a csalások, az adathalászat és a hitelesítő adatok újrafelhasználását – mindezt a felhasználói élmény fokozása és az adatok védelme érdekében.

## <a name="pre-requisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

- Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

- [Azure ad B2C bérlő](./tutorial-create-tenant.md). A bérlőt az Azure-előfizetéshez kell kapcsolni.

- Egy kulcsnélküli Felhőbeli bérlő, ingyenes [próbaverziós fiók](https://keyless.io/go)beszerzése.

- A felhasználó eszközére telepített kulcsnélküli hitelesítő alkalmazás.

## <a name="scenario-description"></a>Forgatókönyv leírása

A kulcsnélküli integráció a következő összetevőket tartalmazza:

- Azure AD B2C – az engedélyezési kiszolgáló, amely a felhasználó hitelesítő adatainak, más néven az identitás-szolgáltatónak a ellenőrzéséért felelős.

- Web-és Mobile-alkalmazások – a mobil-vagy webalkalmazások, amelyeket Ön a kulcsnélküli és a Azure AD B2C elleni védelemhez választ.

- A kulcsnélküli mobil alkalmazás – a kulcsnélküli mobil alkalmazás a Azure AD B2C-kompatibilis alkalmazásokhoz való hitelesítéshez lesz használva.

A következő architektúra-diagram a megvalósítást mutatja be.

![A kép a kulcsnélküli architektúra diagramját mutatja](./media/partner-keyless/keyless-architecture-diagram.png)

|Lépés | Description |
|:-----| :-----------|
| 1. | A felhasználó egy bejelentkezési oldalon érkezik. A felhasználók kiválaszthatják a bejelentkezést és a regisztrációt, és beírja a felhasználónevet
| 2. | Az alkalmazás elküldi a felhasználói attribútumokat a Azure AD B2C identitás-ellenőrzéshez.
| 3. | Azure AD B2C gyűjti a felhasználói attribútumokat, és az attribútumokat a kulcsnélküli értékre küldi, hogy hitelesítse a felhasználót a kulcsnélküli mobil alkalmazáson keresztül.
| 4. | A kulcsnélküli értesítés küldése a regisztrált felhasználó mobileszközön az adatvédelem érdekében – a hitelesítés megőrzése arc biometrikus vizsgálat formájában.
| 5. | Miután a felhasználó válaszol a leküldéses értesítésre, a felhasználó az ellenőrzési eredmények alapján engedélyezheti vagy megtagadhatja a hozzáférést az ügyfélalkalmazás számára.

## <a name="integrate-with-azure-ad-b2c"></a>Integrálás az Azure AD B2C szolgáltatással

### <a name="add-a-new-identity-provider"></a>Új identitás-szolgáltató hozzáadása

Új identitás-Szolgáltató hozzáadásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a **[Azure Portalba](https://portal.azure.com/#home)** a Azure ad B2C bérlő globális rendszergazdájaként.

2. Győződjön meg arról, hogy az Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.

4. Navigáljon az **irányítópultra**  >  **Azure Active Directory B2C**  >   **Identity Providers**

5. Válassza az **identitás-szolgáltatók** elemet.

6. Válassza a **Hozzáadás** lehetőséget.

### <a name="configure-an-identity-provider"></a>Identitás-szolgáltató konfigurálása

Az identitás-szolgáltató konfigurálásához kövesse az alábbi lépéseket:

1. Válasszon ki egy  >  **OpenID Connect (előzetes verzió)** típusú identitás-szolgáltatót
2. Az identitás-szolgáltató beállításához töltse ki az űrlapot:

   |Tulajdonság | Érték |
   |:-----| :-----------|
   | Név   | Kulcsnélküli |
   | Metaadatok URL-címe | Szúrja be az üzemeltetett kulcsnélküli hitelesítési alkalmazás URI-JÁT, majd a megadott elérési utat, például: " https://keyless.auth/.well-known/openid-configuration " |
   | Titkos ügyfélkulcs | A kulcsnélküli hitelesítési példányhoz társított titok – nem ugyanaz, mint a korábban konfigurált. Helyezzen be egy tetszőleges összetett karakterláncot. Ezt a titkot később a kulcsnélküli tároló konfigurációjában fogjuk használni.|
   | Ügyfél-azonosító | Az ügyfél azonosítója. Ezt az azonosítót később a kulcsnélküli tároló konfigurációjában fogjuk használni.|
   | Hatókör | OpenID |
   | Válasz típusa | id_token |
   | Válasz mód | form_post|

3. Válassza az **OK** lehetőséget.

4. Válassza ki **az identitás-szolgáltató jogcímeinek leképezése** elemet.

5. Töltse ki az űrlapot az identitás-szolgáltató leképezéséhez:

   |Tulajdonság | Érték |
   |:-----| :-----------|
   | UserID    | Előfizetésből |
   | Megjelenített név | Előfizetésből |
   | Válasz mód | Előfizetésből |

6. Válassza a **Save (Mentés** ) lehetőséget az új Open ID kapcsolat (OIDC) identitás-szolgáltató beállításának befejezéséhez.

### <a name="create-a-user-flow-policy"></a>Felhasználói folyamat szabályzatának létrehozása

A B2C-azonosítók listáján szereplő új OIDC-azonosítónak kell megjelennie.

1. A Azure AD B2C-bérlő **házirend** területén válassza a **felhasználói folyamatok** elemet.

2. Válassza az **új** felhasználói folyamat elemet.

3. Válassza a **regisztráció és bejelentkezés** lehetőséget, válassza ki a kívánt **verziót**, majd kattintson a **Létrehozás** gombra.

4. Adja meg a szabályzat **nevét** .

5. Az identitás-szolgáltatók szakaszban válassza ki az újonnan létrehozott kulcsnélküli szolgáltatót.

6. Állítsa be a felhasználói folyamat paramétereit. Szúrjon be egy nevet, és válassza ki a létrehozott identitás-szolgáltatót. Emellett e-mail címet is hozzáadhat. Ebben az esetben az Azure nem irányítja át közvetlenül a kulcsnélküli eljárást, hanem megjeleníti azt a képernyőt, ahol a felhasználó kiválaszthatja a használni kívánt beállítást.

7. Hagyja meg a **multi-Factor Authentication** mezőt a következőképpen:.

8. **Feltételes hozzáférési szabályzatok kikényszerítés** kiválasztása

9. A **felhasználói attribútumok és a jogkivonat-jogcímek** területen válassza az **e-mail-cím** lehetőséget az attribútum összegyűjtése lehetőségnél. Felvehet minden olyan attribútumot, amelyet a Azure Active Directory gyűjthet a felhasználóval azon jogcímek mellett, amelyeket Azure AD B2C tud visszaadni az ügyfélalkalmazás számára.

10. Válassza a **Létrehozás** lehetőséget.

11. Sikeres létrehozás után válassza ki az új **felhasználói folyamatot**.

12. A bal oldali panelen válassza az **alkalmazás jogcímek** lehetőséget. A beállítások területen jelölje be az **e-mail** jelölőnégyzetet, majd kattintson a **Mentés** gombra.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. Nyissa meg a Azure AD B2C bérlőt, és a házirendek területen válassza az identitási élmény keretrendszere elemet.

2. Válassza ki a korábban létrehozott SignUpSignIn.

3. Válassza a felhasználói folyamat futtatása lehetőséget, és válassza ki a beállításokat:

   a. Alkalmazás: válassza ki a regisztrált alkalmazást (minta a JWT)

   b. Válasz URL-címe: válassza ki az átirányítási URL-címet

   c. Válassza a felhasználói folyamat futtatása lehetőséget.

4. Ugorjon a regisztrációs folyamatra, és hozzon létre egy fiókot

5. A kulcsnélküli hívás a folyamat során, a felhasználói attribútum létrehozása után lesz meghívva. Ha a folyamat hiányos, ellenőrizze, hogy a felhasználó nincs-e mentve a címtárban.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](./custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)