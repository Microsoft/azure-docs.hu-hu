---
title: Trusona és Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan adhat hozzá Trusona identitás-szolgáltatóként Azure AD B2C a jelszó nélküli hitelesítés engedélyezéséhez.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99368da8b4018e93ad537e4722ffefd476e61291
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99573687"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>A Trusona integrálása Azure Active Directory B2C

A Trusona egy független szoftvergyártó (ISV) szolgáltató, amely a jelszó nélküli hitelesítés, a többtényezős hitelesítés és a digitális licencek vizsgálatának engedélyezésével segíti a bejelentkezést. Ebből a cikkből megtudhatja, hogyan adhat hozzá Trusona identitás-szolgáltatóként Azure AD B2C a jelszó nélküli hitelesítés engedélyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* [Egy Azure ad B2C bérlő](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.
* [Próbaverziós fiók](https://www.trusona.com/) a Trusona-on

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az esetben a Trusona identitás-szolgáltatóként viselkedik a Azure AD B2C a jelszó nélküli hitelesítés engedélyezéséhez. A következő összetevők alkotják a megoldást:

* Azure AD B2C kombinált bejelentkezési és regisztrációs szabályzat
* Trusona hozzáadva a Azure AD B2C identitás-szolgáltatóként
* A letölthető Trusona alkalmazás

![Trusona architektúra diagram](media/partner-trusona/trusona-architecture-diagram.png)

| Lépés | Leírás |
|------|------|
|1     | A felhasználó megpróbál bejelentkezni az alkalmazásba, vagy regisztrálja az alkalmazást. A felhasználó hitelesítése a Azure AD B2C regisztrációs és bejelentkezési szabályzaton keresztül történik. A regisztráció során a rendszer a felhasználó korábban ellenőrzött e-mail-címét használja a Trusona alkalmazásban.     |
|2     | Az Azure B2C az implicit folyamat használatával átirányítja a felhasználót a Trusona OpenID Connect (OIDC) identitás-szolgáltatóhoz.     |
|3     | Asztali PC-alapú bejelentkezés esetén a Trusona egy egyedi, állapot nélküli, animált és dinamikus QR-kódot jelenít meg a Trusona alkalmazással való vizsgálathoz. A Mobile-alapú bejelentkezések esetében a Trusona egy "mély hivatkozást" használ a Trusona alkalmazás megnyitásához. Ez a két módszer az eszközhöz és végső soron a felhasználók felderítéséhez használatos.     |
|4     | A felhasználó megkeresi a megjelenő QR-kódot a Trusona alkalmazással.     |
|5     | A felhasználó fiókja megtalálható a Trusona Cloud Service-ben, és a hitelesítés elkészült.     |
|6     | A Trusona Cloud Service a Trusona alkalmazásnak küldött leküldéses értesítés útján kibocsátja a felhasználót a hitelesítési kihívásra:<br>a. A rendszer a hitelesítési kihívással kéri a felhasználót. <br> b. A felhasználó úgy dönt, hogy elfogadja vagy elutasítja a problémát. <br> c. A felhasználónak az operációs rendszer biztonsága (például biometrikus, PIN-kód, PIN-kód vagy minta) használatával kell meggyőződnie a kérdés megerősítéséhez és aláírásához egy titkos kulccsal a biztonságos enklávé/megbízható végrehajtási környezetben. <br> d. A Trusona alkalmazás a hitelesítés paramétereinek alapján generál dinamikus, Visszajátszási adattartalmat a valós időben. <br> e. A teljes válasz a biztonságos enklávé/megbízható végrehajtási környezet titkos kulcsa alapján (második alkalommal) van aláírva, és a rendszer visszaküldi a Trusona Cloud Service-nek ellenőrzés céljából.      |
|7     |  A Trusona Cloud Service átirányítja a felhasználót a kezdeményező alkalmazásnak egy id_token. Azure AD B2C ellenőrzi, hogy az Trusona közzétett OpenID-konfigurációját használja-e a id_token az Identitáskezelés beállítása során.    |
|  |  |

## <a name="onboard-with-trusona"></a>Trusona

1. Töltse ki az [űrlapot](https://www.trusona.com/) egy Trusona-fiók létrehozásához és az első lépésekhez.

2. Töltse le a Trusona Mobile alkalmazást az App Store áruházból. Telepítse az alkalmazást, és regisztrálja az e-mail-címét.

3. A szoftver által küldött biztonságos "mágikus kapcsolaton" keresztül ellenőrizheti az e-mailjeit.  

4. Nyissa [meg a Trusona fejlesztői irányítópultját](https://dashboard.trusona.com) az önkiszolgáló szolgáltatáshoz.

5. Válassza a **kész** lehetőséget, és végezze el a hitelesítést a Trusona alkalmazással.

6. A bal oldali navigációs panelen válassza a **OIDC-integrációk** lehetőséget.

7. Válassza az **OpenID Connect-integráció létrehozása** lehetőséget.

8. Adja meg a kívánt **nevet** , és használja a korábban megadott tartományi információkat (például contoso) az **Ügyfél-átirányítási gazdagép mezőben**.  

   > [!NOTE]
   > A rendszer a Azure Active Directory kezdeti tartománynevét használja ügyfél-átirányítási gazdagépként.

9. Kövesse a [Trusona-integrációs útmutató](https://docs.trusona.com/integrations/aad-b2c-integration/)utasításait. Ha a rendszer kéri, használja az előző lépésben említett kezdeti tartománynevet (például contoso).  

## <a name="integrate-with-azure-ad-b2c"></a>Integrálás az Azure AD B2C szolgáltatással

### <a name="add-a-new-identity-provider"></a>Új identitás-szolgáltató hozzáadása

> [!NOTE]
> Ha még nem rendelkezik ilyennel, [hozzon létre egy Azure ad B2C bérlőt](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.

2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.

4. Navigáljon az **irányítópult**  >  **Azure Active Directory B2C**  >  **identitás-szolgáltatók** elemre.

3. Válassza az **identitás-szolgáltatók** elemet.

4. Válassza a **Hozzáadás** lehetőséget.

### <a name="configure-an-identity-provider"></a>Identitás-szolgáltató konfigurálása  

1. Válassza az **Identitáskezelés típusú**  >  **OpenID Connect (előzetes verzió)** lehetőséget.

2. Az identitás-szolgáltató beállításához töltse ki az űrlapot:  

   | Tulajdonság | Érték  |
   | :--- | :--- |
   | Metaadatok URL-címe | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | Ügyfél-azonosító | E-mailben elküldi Önt a Trusona |
   | Hatókör | OpenID-profil e-mail címe |
   | Válasz típusa | Id_token |
   | Válasz mód  | Form_post |

3. Válassza az **OK** lehetőséget.  

4. Válassza ki **az identitás-szolgáltató jogcímeinek leképezése** elemet.  

5. Töltse ki az űrlapot az identitás-szolgáltató leképezéséhez:

   | Tulajdonság | Érték  |
   | :--- | :--- |
   | UserID | Sub  |
   | Megjelenített név | Becenév |
   | Utónév | given_name |
   | vezetéknév; | Family_name |
   | Válasz mód | e-mail |

6. Az új OIDC-azonosító beállításának befejezéséhez kattintson **az OK gombra** .

### <a name="create-a-user-flow-policy"></a>Felhasználói folyamat szabályzatának létrehozása

A Trusona-t most már a B2C-szolgáltatók listáján szereplő **új OpenID Connect-identitás-szolgáltatóként** kell látni.

1. A Azure AD B2C-bérlő **házirend** területén válassza a **felhasználói folyamatok** elemet.

1. Válassza az **új felhasználói folyamat** elemet.

1. Válassza a **regisztráció és bejelentkezés** lehetőséget, válassza ki a kívánt verziót, majd kattintson a **Létrehozás** gombra.

1. Adja meg a szabályzat **nevét** .

1. Az **identitás-szolgáltatók** szakaszban válassza ki az újonnan létrehozott **Trusona-identitás-szolgáltatót**.

   > [!NOTE]
   > Mivel a Trusona természeténél fogva többtényezős, érdemes letiltani a többtényezős hitelesítést.

1. Válassza a **Létrehozás** lehetőséget.

1. A **felhasználói attribútumok és jogcímek** területen válassza a **továbbiak megjelenítése** lehetőséget. Az űrlapon válasszon ki legalább egy olyan attribútumot, amelyet a korábbi szakaszban a személyazonosság-szolgáltató beállítása során adott meg.

1. Válassza az **OK** lehetőséget.  

### <a name="test-the-policy"></a>A szabályzat tesztelése

1. Válassza ki az újonnan létrehozott házirendet.

2. Válassza a **felhasználói folyamat futtatása** lehetőséget.

3. Az űrlapon adja meg a válasz URL-címét.

4. Válassza a **felhasználói folyamat futtatása** lehetőséget. A rendszer átirányítja a Trusona OIDC-átjáróra. A Trusona-átjárón ellenőrizze a biztonságos QR-kódot a Trusona alkalmazással, vagy egy egyéni alkalmazással a Trusona Mobile SDK használatával.

5. A biztonságos QR-kód vizsgálatát követően a 3. lépésben megadott válasz URL-címre kell átirányítani.

## <a name="next-steps"></a>Következő lépések  

További információkért tekintse át a következő cikkeket:

- [Egyéni szabályzatok az Azure AD B2C-ben](custom-policy-overview.md)

- [Ismerkedés az egyéni szabályzatokkal AAD B2C](custom-policy-get-started.md?tabs=applications)
