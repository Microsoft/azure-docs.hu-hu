---
title: 'Gyors útmutató: felhasználói bejelentkezés a JavaScript egyoldalas alkalmazásokban | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan hívhat meg egy JavaScript-alkalmazás olyan API-t, amely a Microsoft Identity platform által kiadott hozzáférési jogkivonatokat igényel.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 8e35342bd704f662d41f676f58e2cc14b54f29a8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023384"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Gyors útmutató: bejelentkezés a felhasználókba és hozzáférési jogkivonat beszerzése egy JavaScript SPA-ban

Ebben a rövid útmutatóban egy kódrészletet tölt le és futtat, amely bemutatja, hogyan jelentkezhet be a JavaScript egyoldalas alkalmazás (SPA) a felhasználókba, és hogyan hívhat Microsoft Graph. A kód minta azt is bemutatja, hogyan kérhető le hozzáférési jogkivonat a Microsoft Graph API vagy bármely webes API meghívásához.

Nézze meg, [Hogyan működik a minta](#how-the-sample-works) egy ábrán.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (projektfájlok szerkesztése)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>A gyors üzembe helyezési alkalmazás regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás indításához használja az alábbi lehetőségek egyikét.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség (expressz): regisztrálja és állítsa be automatikusan az alkalmazást, majd töltse le a kód mintáját
>
> 1. Lépjen a <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Alkalmazásregisztrációk</a> gyors üzembe helyezési élményhez.
> 1. Adja meg az alkalmazás nevét.
> 1. A **támogatott fiókok típusai** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. Az új alkalmazás letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség (manuális): az alkalmazás-és a kód-minta regisztrálása és manuális konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. A **támogatott fiókok típusai** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
> 1. Válassza a **Regisztráció** lehetőséget. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
> 1. Ez a rövid útmutató az [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezését igényli. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
> 1. A **platform konfigurációk** területen  >  **adjon hozzá egy platformot**. Válassza a **web** lehetőséget.
> 1. Állítsa be az **átirányítási URI** értékét a következőre: `http://localhost:3000/` . 
> 1. Válassza ki a **hozzáférési jogkivonatokat** és az **azonosító jogkivonatokat** az **implicit engedélyezési és a hibrid folyamatok**  alatt.
> 1. Válassza a **Konfigurálás** lehetőséget.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy az ebben a rövid útmutatóban szereplő kód minta működjön, adjon hozzá egy **átirányítási URI** -t, `http://localhost:3000/` és engedélyezze az **implicit engedélyezést**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-project"></a>2. lépés: A projekt letöltése

> [!div renderon="docs"]
> Ha Node.js használatával szeretné futtatni a projektet egy webkiszolgálóval, [töltse le az alapprojekt fájljait](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> A projekt futtatása webkiszolgálóval Node.js használatával

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>3. lépés: a JavaScript-alkalmazás konfigurálása
>
> A *JavaScriptSPA* mappában szerkessze *authConfig.js*, majd állítsa be a `clientID` és `authority` a `redirectUri` értékeket `msalConfig` .
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };
>
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Ahol:
> - `Enter_the_Application_Id_Here` a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** .
>
>    Az **alkalmazás (ügyfél) azonosítójának** megkereséséhez nyissa meg a Azure Portal alkalmazás **Áttekintés** lapját.
> - `Enter_the_Cloud_Instance_Id_Here` Az Azure-felhő példánya. A fő vagy a globális Azure-felhőhöz egyszerűen írja be a következőt: `https://login.microsoftonline.com/` . Az **országos** felhők (például Kína) esetében lásd: [nemzeti felhők](./authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` az a következő lehetőségek egyikére van beállítva:
>    - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére** (például: `contoso.microsoft.com` ).
>
>    A **címtár (bérlő) azonosító** értékének megkereséséhez nyissa meg az alkalmazás regisztrációjának **Áttekintés** lapját a Azure Portal.
>    - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `organizations` .
>    - Ha az alkalmazás a *szervezeti címtárban és a személyes Microsoft-fiókokban is támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `common` . A *személyes Microsoft-fiókok* támogatásának korlátozásához cserélje le ezt az értéket a következőre: `consumers` .
>
>    A **támogatott fióktípus** értékének megkereséséhez nyissa meg az alkalmazás regisztrációjának **Áttekintés** lapját a Azure Portal.
> - A `Enter_the_Redirect_Uri_Here` értéke `http://localhost:3000/`.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékeivel konfiguráltuk.

> [!div renderon="docs"]
>
> Ezután továbbra is ugyanabban a mappában szerkessze *graphConfig.js* fájlt a és az `graphMeEndpoint` `graphMeEndpoint` objektum beállításához `apiConfig` .
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Ahol:
> - *\<Enter_the_Graph_Endpoint_Here>* a végpont az API-hívásokat fogja elvégezni. A fő vagy a globális Microsoft Graph API szolgáltatáshoz egyszerűen írja be a következőt: `https://graph.microsoft.com/` . További információ: [belföldi Felhőbeli üzembe helyezés](/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>4. lépés: a projekt futtatása

A projekt futtatása webkiszolgálóval [Node.js](https://nodejs.org/en/download/)használatával:

1. A kiszolgáló elindításához futtassa a következő parancsot a projekt könyvtárából:
    ```cmd
    npm install
    npm start
    ```
1. Nyisson meg egy webböngészőt, és lépjen a következőre: `http://localhost:3000/` .

1. A bejelentkezés elindításához válassza a **Bejelentkezés** lehetőséget, majd hívja meg Microsoft Graph API-t.

Ha a böngésző betölti az alkalmazást, válassza a **Bejelentkezés** lehetőséget. Amikor először jelentkezik be, a rendszer arra kéri, hogy adja meg a beleegyezik, hogy engedélyezze az alkalmazásnak a profil elérését és a bejelentkezést. Miután sikeresen bejelentkezett, a felhasználói profil adatai megjelennek az oldalon.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése

![A minta JavaScript SPA működése: 1. A SPA kezdeményezi a bejelentkezést. 2. A fürdő azonosító jogkivonatot vásárol a Microsoft Identity platformon. 3. A SPA-hívások jogkivonat beszerzése. 4. A Microsoft Identity platform egy hozzáférési jogkivonatot ad vissza a SPA-nak. 5. A SPA teszi és a HTTP GET kérést az Aces Tokenrel a Microsoft Graph API-val. 6. A Graph API egy HTTP-választ ad vissza a SPA-nak.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

A MSAL-könyvtár bejelentkezik a felhasználók számára, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kéri. A gyors üzembe helyezési *index.html* fájl a könyvtárra mutató hivatkozást tartalmaz:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```

Az előző verziót lecserélheti a legújabb kiadott verzióra [MSAL.js kiadások](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)alatt.

Ha Node.js telepítve van, az Node.js csomagkezelő (NPM) használatával letöltheti a legújabb verziót:

```cmd
npm install msal
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

A gyors üzembe helyezési kód azt is bemutatja, hogyan inicializálható a MSAL-könyvtár:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
    }
  };

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

|Ahol  | Leírás |
|---------|---------|
|`clientId`     | A Azure Portalban regisztrált alkalmazás alkalmazás-azonosítója.|
|`authority`    | Választható A fiók típusát támogató szolgáltatói URL-cím, a konfiguráció szakaszban korábban leírtak szerint. Az alapértelmezett szolgáltató: `https://login.microsoftonline.com/common` . |
|`redirectUri`     | Az alkalmazás regisztrációja konfigurált válasz-/redirectUri. Ebben az esetben a `http://localhost:3000/` . |
|`cacheLocation`  | Választható Beállítja a böngésző tárolóját a hitelesítési állapothoz. Az alapértelmezett érték a sessionStorage.   |
|`storeAuthStateInCookie`  | Választható A böngésző cookie-jai hitelesítési folyamatainak érvényesítéséhez szükséges hitelesítési kérési állapotot tároló könyvtár. Ez a cookie az IE és a Edge böngészők számára van beállítva, hogy bizonyos [ismert problémákkal](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)enyhíthető legyen. |

További információ az elérhető konfigurálható lehetőségekről: [ügyfélalkalmazások inicializálása](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Bejelentkezési felhasználók

A következő kódrészlet bemutatja, hogyan jelentkezhet be a felhasználókba:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

|Ahol  | Leírás |
|---------|---------|
| `scopes`   | Választható Olyan hatóköröket tartalmaz, amelyeket a rendszer a bejelentkezési időben a felhasználói beleküldéshez kér. Például `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni webes API-khoz (azaz `api://<Application ID>/access_as_user` ). |

Azt is megteheti, hogy a `loginRedirect` metódus használatával átirányítja az aktuális oldalt a bejelentkezési lapra a felugró ablak helyett.

### <a name="request-tokens"></a>Kérelmek jogkivonatai

A MSAL három módszert használ a tokenek beszerzésére: `acquireTokenRedirect` , `acquireTokenPopup` és `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. A `loginRedirect` vagy a `loginPopup` metódus első futtatása után `acquireTokenSilent` a metódus általában a védett erőforrásokhoz való hozzáféréshez használt jogkivonatok beszerzésére szolgál a további hívásokhoz. A jogkivonatok kérése vagy megújítása csendesen történik.

```javascript

const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

|Ahol  | Leírás |
|---------|---------|
| `scopes`   | Az API hozzáférési jogkivonatában visszaadott hatóköröket tartalmaz. Például `[ "mail.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni webes API-khoz (azaz `api://<Application ID>/access_as_user` ).|

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Vannak olyan helyzetek, amikor a felhasználókat a Microsoft Identity platformmal való interakcióra kényszeríti. Például:
* Előfordulhat, hogy a felhasználóknak újra meg kell adniuk a hitelesítő adataikat, mert a jelszavuk lejárt.
* Az alkalmazása olyan további erőforrás-hatókörökhöz kér hozzáférést, amelyeknek a felhasználónak hozzá kell járulnia.
* Kétfaktoros hitelesítés szükséges.

A legtöbb alkalmazás esetében a szokásos ajánlott minta az első meghívása `acquireTokenSilent` , majd a kivétel elfogása, majd a `acquireTokenPopup` (vagy) hívása `acquireTokenRedirect` egy interaktív kérelem elindításához.

Az `acquireTokenPopup` eredmények felugró ablakban való meghívása a bejelentkezéshez. (Vagy a `acquireTokenRedirect` felhasználók a Microsoft Identity platformra történő átirányítását eredményezi). Ebben az ablakban a felhasználóknak kapcsolatba kell lépniük a hitelesítő adataik jóváhagyásával, a szükséges erőforrás megadásával vagy a kétfaktoros hitelesítés végrehajtásával.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> Ez a rövid útmutató a `loginRedirect` és a `acquireTokenRedirect` metódusokat használja a Microsoft Internet Explorerrel, mert az Internet Explorer felugró ablakok kezelésére vonatkozó [ismert probléma](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) miatt.

## <a name="next-steps"></a>Következő lépések

A rövid útmutatóhoz tartozó alkalmazás létrehozásával kapcsolatos részletes útmutatót a következő témakörben talál:

> [!div class="nextstepaction"]
> [Oktatóanyag: bejelentkezés a felhasználókba és a Microsoft Graph API meghívása egy JavaScript egyoldalas alkalmazásból (SPA)](tutorial-v2-javascript-spa.md)
