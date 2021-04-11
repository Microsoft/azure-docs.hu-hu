---
title: itsme OpenID-kapcsolat Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Ismerje meg, hogyan integrálhatja Azure AD B2C hitelesítését a itsme-OIDC client_secret felhasználói folyamat házirendjének használatával. a itsme egy digitális azonosító alkalmazás. Lehetővé teszi a biztonságos bejelentkezést a kártyaolvasók, jelszavak, kétfaktoros hitelesítés és több PIN-kód nélkül.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2560c294b63d0554708eda1f804bf279d16d7d74
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257959"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Az itsme OpenID Connect (OIDC) konfigurálása Azure Active Directory B2C

A itsme digitális azonosító alkalmazás lehetővé teszi, hogy a rendszer biztonságosan jelentkezzen be kártyaolvasók, jelszavak, kétfaktoros hitelesítés vagy több PIN-kód nélkül. A itsme alkalmazás erős ügyfél-hitelesítést biztosít ellenőrzött identitással. Ebből a cikkből megtudhatja, hogyan integrálhatja Azure AD B2C hitelesítését az itsme OpenID Connect (OIDC) használatával az ügyfél titkos felhasználói folyamatának házirendjével.

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következőkre lesz szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* [Egy Azure ad B2C bérlő](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.
* Az ügyfél-azonosító, más néven a itsme által biztosított partneri kód.
* A itsme által megadott szolgáltatási kód.
* A itsme-fiókhoz tartozó ügyfél-titkos kulcs.

## <a name="scenario-description"></a>Forgatókönyv leírása

![itsme architektúra diagram](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| Lépés | Leírás |
|------|------|
|1     | A webhelyen vagy az alkalmazásban vegye fel a **Bejelentkezés a itsme** gombbal a Azure ad B2C felhasználói folyamatba való alkalmazkodással. A kapcsolati folyamat elindul, amikor a felhasználó rákattint erre a gombra.  |
|2     | Azure AD B2C elindítja az OpenID Connect folyamatot egy engedélyezési kérelem küldésével a itsme ügyfél titkos API-nak. A végpontokkal kapcsolatos információkat tartalmazó, jól ismert/OpenID-konfigurációs végpont is elérhető.  |
|3     | A itsme-környezet átirányítja a felhasználót a itsme azonosítására szolgáló lapra, így a felhasználó kitöltheti a telefonszámát.  |
|4     | A itsme-környezet megkapja a telefonszámot a felhasználótól, és ellenőrzi a javítást.  |
|5     | Ha a telefonszám aktív itsme-felhasználóhoz tartozik, a rendszer létrehoz egy műveletet a itsme alkalmazáshoz.  |
|6     | A felhasználó megnyitja a itsme alkalmazást, ellenőrzi a kérést, és megerősíti a műveletet.  |
|7     |  Az alkalmazás tájékoztatja a itsme környezetet, a műveletet megerősítették. |
|8     |  A itsme-környezet visszaadja a OAuth hitelesítő kódot Azure AD B2C. |
|9     |  Az engedélyezés kód használatával a Azure AD B2C jogkivonat-kérelmet küld. |
| 10 | A itsme-környezet ellenőrzi a jogkivonat-kérelmet, és ha továbbra is érvényes, a OAuth hozzáférési tokent és a kért felhasználói adatokat tartalmazó azonosító tokent adja vissza. |
| 11 | Végül a rendszer átirányítja a felhasználót az átirányítási URL-címre hitelesített felhasználóként.  |
|   |   |

## <a name="onboard-with-itsme"></a>Itsme

1. A itsme-vel rendelkező fiók létrehozásához látogasson el az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace)elérhető itsme.

2. Aktiválja a itsme-fiókját e-mailben a címre onboarding@itsme.be . Ekkor megjelenik a B2C-beállításhoz szükséges **partneri kód** és **szolgáltatási kód** .

3. A itsme-fiók aktiválása után egy e-mailt fog kapni, amely egy egyszeri, az **ügyfél titkos kulcsára** mutató hivatkozást kap.

4. A konfigurálás befejezéséhez kövesse a [itsme](https://business.itsme.be/en) címen található utasításokat.

## <a name="integrate-with-azure-ad-b2c"></a>Integrálás az Azure AD B2C szolgáltatással

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Új identitás-szolgáltató beállítása Azure AD B2C

> [!NOTE]
> Ha még nem rendelkezik ilyennel, [hozzon létre egy Azure ad B2C bérlőt](tutorial-create-tenant.md) , amely az Azure-előfizetéshez van csatolva.

1. Győződjön meg arról, hogy a Azure AD B2C bérlőt tartalmazó könyvtárat használja. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, és válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.

2. Az **Azure-szolgáltatások** területen válassza a **Azure ad B2C** lehetőséget (vagy válassza a **További szolgáltatások** lehetőséget, és a **minden szolgáltatás** keresőmező használatával keresse meg a *Azure ad B2C*).

3. Válassza az **identitás-szolgáltatók**, majd az **új OpenID Connect Provider** lehetőséget.

4. Töltse ki az űrlapot a következő információkkal:

   |Tulajdonság | Érték |
   |------------ |------- |
   | Név | itsme |
   | Metaadatok URL-címe | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>hol `<environment>` van `e2e` vagy (tesztkörnyezet) vagy `prd` (éles környezet)  |
   | Ügyfélazonosító     | Az **ügyfél-azonosító**, más néven **partneri kód**  |
   | Titkos ügyfélkulcs | Az **client_secret** |
   | Hatókör  | OpenID szolgáltatás: YOURSERVICECODE profil e-mail-címe [Phone] [Cím]  |
   |Válasz típusa | code |
   |Válasz mód | lekérdezés |
   |Tartományi tipp | *Ezt üresen hagyhatja* |
   |UserID | Sub |
   |Megjelenítendő név | name |
   |Utónév | given_name |
   |vezetéknév; | family_name |
   |E-mail | e-mail|

5. Kattintson a **Mentés** gombra.

### <a name="configure-a-user-flow"></a>Felhasználói folyamat konfigurálása

1. A Azure AD B2C-bérlő **házirend** területén válassza a **felhasználói folyamatok** elemet.

2. Válassza az **új felhasználói folyamat** elemet.

3. Válassza a **regisztráció és bejelentkezés** lehetőséget, válassza ki a kívánt verziót, majd kattintson a **Létrehozás** gombra.

4. Adjon meg egy **nevet**.

5. Az **Identity Providers** szakaszban válassza a **itsme** lehetőséget.

6. Válassza a **Létrehozás** lehetőséget.

7. Nyissa meg az újonnan létrehozott felhasználói folyamatot a felhasználói folyamat nevének kiválasztásával.

8. Válassza a **Tulajdonságok** lehetőséget, és módosítsa a következő értékeket:

   * A **hozzáférés & azonosító jogkivonat élettartama (perc)** és **5** közötti értékre vált.
   * Módosítsa a **frissítési jogkivonat csúszó ablakának élettartamát** **lejárat nélkül**.

### <a name="register-an-application"></a>Egy alkalmazás regisztrálása

1. A B2C-bérlő **kezelés** területén válassza **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.

2. Adja meg az alkalmazás **nevét** , és adja meg az **átirányítási URI**-t. Tesztelési célból adja meg a következőt: `https://jwt.ms` .

3. Győződjön meg arról, hogy a többtényezős hitelesítés **le van tiltva**.

4. Válassza a **Regisztráció** lehetőséget.

   a. Tesztelési célból válassza a **hitelesítés** lehetőséget, majd az **implicit engedélyezés** területen jelölje be a **hozzáférési jogkivonatok** és **azonosító tokenek** jelölőnégyzetet.  

   b. Kattintson a **Mentés** gombra.

## <a name="test-the-user-flow"></a>A felhasználói folyamat tesztelése

1. A B2C-bérlő **házirend** területén válassza a **felhasználói folyamatok** lehetőséget.

2. Válassza ki a korábban létrehozott felhasználói folyamatot.

3. Válassza a **felhasználói folyamat futtatása** lehetőséget.

   a. **Alkalmazás**: *válassza ki a regisztrált alkalmazást*

   b. **Válasz URL-címe**: *válassza ki az átirányítási URL-címet*

4. Megjelenik a itsme- **azonosító** oldal.  

5. Adja meg a mobiltelefon számát, és válassza a **Küldés** lehetőséget.

6. Erősítse meg a műveletet a itsme alkalmazásban.

## <a name="next-steps"></a>Következő lépések

További információkért tekintse át a következő cikkeket:

* [Egyéni szabályzatok az Azure AD B2C-ben](custom-policy-overview.md)

* [Ismerkedés az egyéni szabályzatokkal Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)