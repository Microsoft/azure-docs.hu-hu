---
title: 'Oktatóanyag: hitelesítés engedélyezése egyoldalas alkalmazásokban'
titleSuffix: Azure AD B2C
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Active Directory B2Ct egy JavaScript-alapú egyoldalas alkalmazás (SPA) felhasználói bejelentkezésének biztosítására.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 04/04/2020
ms.custom: mvc, seo-javascript-september2019, devx-track-js
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 6a9f3b864bd8aba2140c7d32d4b5474ff7b95f88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96171228"
---
# <a name="tutorial-enable-authentication-in-a-single-page-application-with-azure-ad-b2c"></a>Oktatóanyag: hitelesítés engedélyezése egyoldalas alkalmazásban Azure AD B2C

Ez az oktatóanyag bemutatja, hogyan használható a Azure Active Directory B2C (Azure AD B2C) egy egyoldalas alkalmazásban (SPA) lévő felhasználók regisztrálására és bejelentkezésére a következők bármelyikével:
* [OAuth 2,0 engedélyezési kód folyamatábrája](./authorization-code-flow.md) ( [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)használatával)
* [OAuth 2,0 implicit engedélyezési folyamat](./implicit-flow-single-page-application.md) ( [MSAL.js 1. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)használatával)

Ebben az oktatóanyagban az első egy kétrészes sorozatban:

> [!div class="checklist"]
> * Válasz URL-címének hozzáadása a Azure AD B2C-bérlőben regisztrált alkalmazáshoz
> * Mintakód letöltése a GitHubról
> * A minta alkalmazás kódjának módosítása a Bérlővel való együttműködéshez
> * Regisztráció a regisztrációs/bejelentkezési felhasználói folyamat használatával

A sorozat [következő oktatóanyaga](tutorial-single-page-app-webapi.md) a kód mintájának webes API részét teszi lehetővé.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag lépéseinek folytatása előtt a következő Azure AD B2C erőforrásokra van szükség:

* [Azure AD B2C bérlő](tutorial-create-tenant.md)
* Az [alkalmazás regisztrálva](tutorial-register-spa.md) van a bérlőben
* A bérlőn [létrehozott felhasználói folyamatok](tutorial-create-user-flows.md)

Emellett a helyi fejlesztési környezetben a következőkre lesz szüksége:

* [Visual Studio Code](https://code.visualstudio.com/) vagy más Kódszerkesztő
* [Node.js](https://nodejs.org/en/download/)

## <a name="update-the-application"></a>Az alkalmazás frissítése

Az előfeltételek részeként elvégzett [második oktatóanyagban](./tutorial-register-spa.md) egy egyoldalas alkalmazást regisztrált Azure ad B2Cban. Ebben az oktatóanyagban a kód mintával való kommunikáció engedélyezéséhez adjon hozzá egy válasz URL-címet (más néven átirányítási URI-t) az alkalmazás regisztrálásához.

Ha frissíteni szeretne egy alkalmazást a Azure AD B2C-bérlőben, használhatja az új egyesített **Alkalmazásregisztrációk** -élményt vagy az örökölt  **alkalmazások (örökölt)** felületét. [További információ az új felületről](./app-registrations-training-guide.md).

#### <a name="app-registrations-auth-code-flow"></a>[Alkalmazásregisztrációk (Auth-kód folyamatábrája)](#tab/app-reg-auth/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, válassza a **birtokolt alkalmazások** fület, majd válassza ki a *spaapp1* alkalmazást.
1. Az **egyoldalas alkalmazás** területen válassza az **URI hozzáadása** hivatkozást, majd írja be a értéket `http://localhost:6420` .
1. Kattintson a **Mentés** gombra.
1. Válassza az **Áttekintés** lehetőséget.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra, amikor frissíti a kódot az egyoldalas webalkalmazásban.

#### <a name="app-registrations-implicit-flow"></a>[Alkalmazásregisztrációk (implicit folyamat)](#tab/app-reg-implicit/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. Válassza a **Alkalmazásregisztrációk** lehetőséget, válassza a **birtokolt alkalmazások** fület, majd válassza ki a *spaapp1* alkalmazást.
1. Az **egyoldalas alkalmazás** területen válassza az **URI hozzáadása** hivatkozást, majd írja be a értéket `http://localhost:6420` .
1. Az **implicit engedélyezés** területen jelölje be a **hozzáférési jogkivonatok** és **azonosító tokenek** jelölőnégyzetét, ha még nincs kiválasztva, majd válassza a **Mentés** lehetőséget.
1. Válassza az **Áttekintés** lehetőséget.
1. Jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra, amikor frissíti a kódot az egyoldalas webalkalmazásban.

#### <a name="applications-legacy"></a>[Alkalmazások (örökölt)](#tab/applications-legacy/)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az **összes szolgáltatás** elemet a Azure Portal bal felső sarkában, majd keresse meg és válassza ki a **Azure ad B2C**.
1. Válassza az **alkalmazások (örökölt)** lehetőséget, majd válassza ki a *spaapp1* alkalmazást.
1. A **Válasz URL-cím** területen adja hozzá a címet `http://localhost:6420` .
1. Kattintson a **Mentés** gombra.
1. A Tulajdonságok lapon jegyezze fel az **alkalmazás azonosítóját**. Az alkalmazás-azonosítót egy későbbi lépésben kell használni, amikor frissíti a kódot az egyoldalas webalkalmazásban.

* * *

## <a name="get-the-sample-code"></a>A mintakód letöltése

Ebben az oktatóanyagban egy, a GitHubról letöltött mintakód-mintát konfigurál a B2C-Bérlővel való együttműködéshez. A minta azt mutatja be, hogy egy egyoldalas alkalmazás hogyan használhatja a Azure AD B2C felhasználói regisztrációhoz és bejelentkezéshez, valamint egy védett webes API meghívásához (a sorozat következő oktatóanyagában engedélyezheti a webes API-t).

* MSAL.js 2. x engedélyezési kód folyamatábrája:

    [Zip-fájl letöltése](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa/archive/main.zip) vagy a minta klónozása a githubról:

    ```
    git clone https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa.git
    ```
* MSAL.js 1. x implicit folyamat mintája:

    [Zip-fájl letöltése](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) vagy a minta klónozása a githubról:

    ```
    git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
    ```

## <a name="update-the-sample"></a>A minta frissítése

Most, hogy beolvasta a mintát, frissítse a kódot a Azure AD B2C bérlői nevével és egy korábbi lépésben rögzített alkalmazás-AZONOSÍTÓval.

#### <a name="auth-code-flow-sample"></a>[Auth kód flow-minta](#tab/config-auth/)

1. Nyissa meg az *authConfig.js* fájlt az *alkalmazás* mappájában.
1. Az `msalConfig` objektumban keresse meg a hozzárendelést, `clientId` és cserélje le a korábbi lépésben rögzített **alkalmazás-(ügyfél-) azonosítóra** .
1. Nyissa meg az `policies.js` fájlt.
1. Keresse meg a bejegyzéseket a alatt, `names` és cserélje le a hozzárendelést a korábbi lépésben létrehozott felhasználói folyamatok nevére, például: `B2C_1_signupsignin1` .
1. Keresse meg a bejegyzéseket a `authorities` és a megfelelő módon cserélje le a korábbi lépésben létrehozott felhasználói folyamatok nevére, például: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` .
1. Keresse meg a hozzárendelést, és cserélje le a következőre: `authorityDomain` `<your-tenant-name>.b2clogin.com` .
1. Nyissa meg az `apiConfig.js` fájlt.
1. Keresse meg a hozzárendelést, `b2cScopes` és cserélje le az URL-címet a webes API-hoz létrehozott hatókör URL-címére, például: `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]` .
1. Keresse meg a hozzárendelést, `webApi` és cserélje le az aktuális URL-címet arra az URL-címre, ahol a webes API-t telepítette a 4. lépésben, például: `webApi: http://localhost:5000/hello` .

#### <a name="implicit-flow-sample"></a>[Implicit folyamat mintája](#tab/config-implicit/)

1. Nyissa meg a *authConfig.js* fájlt a *JavaScriptSPA* mappában.
1. Az `msalConfig` objektumban keresse meg a hozzárendelést, `clientId` és cserélje le a korábbi lépésben rögzített **alkalmazás-(ügyfél-) azonosítóra** .
1. Nyissa meg az `policies.js` fájlt.
1. Keresse meg a bejegyzéseket a alatt, `names` és cserélje le a hozzárendelést a korábbi lépésben létrehozott felhasználói folyamatok nevére, például: `B2C_1_signupsignin1` .
1. Keresse meg a bejegyzéseket a `authorities` és a megfelelő módon cserélje le a korábbi lépésben létrehozott felhasználói folyamatok nevére, például: `https://<your-tenant-name>.b2clogin.com/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>` .
1. Nyissa meg az `apiConfig.js` fájlt.
1. Keresse meg a hozzárendelést, `b2cScopes` és cserélje le az URL-címet a webes API-hoz létrehozott hatókör URL-címére, például: `b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/helloapi/demo.read"]` .
1. Keresse meg a hozzárendelést, `webApi` és cserélje le az aktuális URL-címet arra az URL-címre, ahol a webes API-t telepítette a 4. lépésben, például: `webApi: http://localhost:5000/hello` .

* * *

Az eredményül kapott kódnak az alábbihoz hasonlóan kell kinéznie:

#### <a name="auth-code-flow-sample"></a>[Auth kód flow-minta](#tab/review-auth/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    knownAuthorities: [b2cPolicies.authorityDomain],
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
    authorityDomain: "fabrikamb2c.b2clogin.com"
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

#### <a name="implicit-flow-sample"></a>[Implicit folyamat mintája](#tab/review-implicit/)

*authConfig.js*:

```javascript
const msalConfig = {
  auth: {
    clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
    authority: b2cPolicies.authorities.signUpSignIn.authority,
    validateAuthority: false
  },
  cache: {
    cacheLocation: "localStorage",
    storeAuthStateInCookie: true
  }
};

const loginRequest = {
  scopes: ["openid", "profile"],
};

const tokenRequest = {
  scopes: apiConfig.b2cScopes // i.e. ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"]
};
```

*policies.js*:

```javascript
const b2cPolicies = {
    names: {
        signUpSignIn: "b2c_1_susi",
        forgotPassword: "b2c_1_reset",
        editProfile: "b2c_1_edit_profile"
    },
    authorities: {
        signUpSignIn: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
        },
        forgotPassword: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
        },
        editProfile: {
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_edit_profile"
        }
    },
}
```

*apiConfig.js*:

```javascript
const apiConfig = {
  b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
  webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
};
```

* * *


## <a name="run-the-sample"></a>Minta futtatása

1. Nyisson meg egy konzolablak ablakot, és navigáljon a mintát tartalmazó könyvtárhoz. 

    - MSAL.js 2. x engedélyezési kód folyamatábrája:

        ```console
        cd ms-identity-b2c-javascript-spa
        ```
    - MSAL.js 1. x implicit flow minta esetén: 

        ```console
        cd active-directory-b2c-javascript-msal-singlepageapp
        ```

1. Futtassa az alábbi parancsot:

    ```console
    npm install && npm update
    npm start
    ```

    A konzol ablakban a helyileg futó Node.js kiszolgáló portszáma látható:

    ```console
    Listening on port 6420...
    ```
1. A gombra kattintva `http://localhost:6420` megtekintheti a helyi gépen futó webalkalmazást.

    :::image type="content" source="media/tutorial-single-page-app/web-app-spa-01-not-logged-in.png" alt-text="A helyileg futó egylapos alkalmazást megjelenítő webböngésző":::

### <a name="sign-up-using-an-email-address"></a>Regisztrálás e-mail-címmel

Ez a minta alkalmazás támogatja a regisztrációt, a bejelentkezést és a jelszó-visszaállítást. Ebben az oktatóanyagban egy e-mail-cím használatával regisztrálhat.

1. Válassza a **Bejelentkezés** lehetőséget a korábbi lépésben megadott *B2C_1_signupsignin1* felhasználói folyamat elindításához.
1. A Azure AD B2C egy regisztrációs hivatkozást tartalmazó bejelentkezési oldalt jelenít meg. Mivel még nem rendelkezik fiókkal, válassza a **regisztráció most** hivatkozást.
1. A regisztrációs munkafolyamat egy olyan oldalt jelenít meg, amely az e-mail-cím használatával gyűjti és ellenőrzi a felhasználó identitását. A regisztrációs munkafolyamat a felhasználó jelszavát és a felhasználói folyamatban definiált kért attribútumokat is gyűjti.

    Érvényes e-mail-címet használjon, és ellenőrizze az ellenőrző kód használatával. Állítson be egy jelszót. Adja meg a kért attribútumokhoz tartozó értékeket.

    :::image type="content" source="media/tutorial-single-page-app/user-flow-sign-up-workflow-01.png" alt-text="Azure AD B2C felhasználói folyamat által megjelenített regisztrációs oldal":::

1. A **Létrehozás** gombra kattintva helyi fiókot hozhat létre a Azure ad B2C könyvtárban.

A **Létrehozás** gombra kattintva az alkalmazás megjeleníti a bejelentkezett felhasználó nevét.

:::image type="content" source="media/tutorial-single-page-app/web-app-spa-02-logged-in.png" alt-text="Webböngésző, amely egy egyoldalas alkalmazást mutat be a bejelentkezett felhasználóval":::

Ha tesztelni szeretné a bejelentkezést, kattintson a **kijelentkezés** gombra, majd válassza a **Bejelentkezés** lehetőséget, és jelentkezzen be a regisztráció során megadott e-mail-címmel és jelszóval.

### <a name="what-about-calling-the-api"></a>Mi a helyzet az API meghívásával?

Ha a bejelentkezést követően az **API meghívása** gombot választja, a rendszer az API-hívás eredményei helyett a regisztrációs és bejelentkezési felhasználói folyamat lapot jeleníti meg. Ez azért várható, mert még nem konfigurálta az alkalmazás API-részét úgy, hogy kommunikáljon *a Azure ad B2C* bérlőben regisztrált webes API-alkalmazással.

Ezen a ponton az alkalmazás továbbra is megpróbál kommunikálni a bemutató bérlőben (fabrikamb2c.onmicrosoft.com) regisztrált API-val, és mivel Ön nem hitelesíti a bérlőt, megjelenik a regisztrációs/bejelentkezési oldal.

A védett API engedélyezéséhez lépjen a sorozat következő oktatóanyagára (lásd a [következő lépések](#next-steps) szakaszt).

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy egyoldalas alkalmazást konfigurált a Azure AD B2C-bérlő felhasználói folyamatával való együttműködéshez a regisztrálási és bejelentkezési képesség biztosításához. A következő lépéseket végezte el:

> [!div class="checklist"]
> * A Azure AD B2C-bérlőben regisztrált alkalmazáshoz tartozó válasz URL-cím hozzáadva
> * Mintakód letöltése a GitHubról
> * Módosította a minta alkalmazás kódját a Bérlővel való együttműködéshez
> * Regisztráció a regisztrációs/bejelentkezési felhasználói folyamat használatával

Most lépjen a sorozat következő oktatóanyagára, hogy hozzáférést biztosítson egy védett webes API-hoz a FÜRDŐből:

> [!div class="nextstepaction"]
> [Oktatóanyag: a webes API-hoz való hozzáférés biztosítása egy egyoldalas alkalmazásból >](tutorial-single-page-app-webapi.md)