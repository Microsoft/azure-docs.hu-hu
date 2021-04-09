---
title: 'Gyors útmutató: Microsoft Graph hívása egy Node.js asztali alkalmazásból | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan jelentkezhet be egy Node.js Electron asztali alkalmazás a felhasználók számára, és hogyan kérhet hozzáférési jogkivonatot egy Microsoft Identity platform-végpont által védett API meghívásához
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: beef869b891fe6e3f0ea2f667763cb310008b2fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653269"
---
# <a name="quickstart-acquire-an-access-token-and-call-the-microsoft-graph-api-from-an-electron-desktop-app"></a>Gyors útmutató: hozzáférési jogkivonat beszerzése és a Microsoft Graph API meghívása egy Electron asztali alkalmazásból

Ebben a rövid útmutatóban egy kódrészletet tölt le és futtat, amely bemutatja, hogyan jelentkezhet be az Electron Desktop-alkalmazás a felhasználókba, és hogyan kérhet hozzáférési jogkivonatokat a Microsoft Graph API meghívásához.

Ez a rövid útmutató a [Microsoft hitelesítési függvénytárát használja a Node.jshoz (MSAL node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) az [engedélyezési kód folyamatával a PKCE](v2-oauth2-auth-code-flow.md)használatával.

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) vagy más Kódszerkesztő

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>A minta alkalmazás regisztrálása és letöltése
>
> Az első lépésekhez kövesse az alábbi lépéseket.
>
> #### <a name="step-1-register-the-application"></a>1. lépés: az alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** , például: `msal-node-desktop` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
> 1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
> 1. Válassza **a platform**  >  **mobil-és asztali alkalmazások** hozzáadása lehetőséget.
> 1. Az **átirányítási URI** -k szakaszban adja meg a értéket `msal://redirect` .
> 1. Válassza a **Konfigurálás** lehetőséget.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>1. lépés: az alkalmazás konfigurálása Azure Portalban
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, hozzá kell adnia egy válasz URL-címet a **msal://redirect**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-windows-desktop/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-electron-sample-project"></a>2. lépés: az elektron-minta projekt letöltése

> [!div renderon="docs"]
> [A mintakód letöltése](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [A mintakód letöltése](https://github.com/azure-samples/ms-identity-javascript-nodejs-desktop/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-electron-sample-project"></a>3. lépés: az elektron-minta projekt konfigurálása
>
> 1. Bontsa ki a zip-fájlt egy helyi mappába a lemez gyökeréhez (például *C:/Azure-Samples*).
> 1. Szerkessze *. env* és cserélje le a mezők értékeit `TENANT_ID` és `CLIENT_ID` a következő kódrészletet:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here"
>    ```
>    Ahol:
>    - `Enter_the_Application_Id_Here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.
>    - `Enter_the_Tenant_Id_Here` – cserélje le ezt az értéket a **bérlői azonosító** vagy a **bérlő nevére** (például contoso.microsoft.com).
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítójának**, a **bérlői azonosító** értékének megkereséséhez lépjen az alkalmazás **Áttekintés** lapjára a Azure Portal.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4. lépés: az alkalmazás futtatása

> [!div renderon="docs"]
> #### <a name="step-4-run-the-application"></a>4. lépés: az alkalmazás futtatása

A minta függőségeit egyszer kell telepítenie:

```console
npm install
```

Ezután futtassa az alkalmazást a parancssorból vagy a konzolon keresztül:

```console
npm start
```

Ekkor meg kell jelennie az alkalmazás felhasználói felületének a **Bejelentkezés** gombbal.

## <a name="about-the-code"></a>A kód ismertetése

Az alábbiakban a minta alkalmazás néhány fontos aspektusát tárgyaljuk.

### <a name="msal-node"></a>MSAL csomópont

A [MSAL csomópont](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek. További információ a MSAL-csomópont asztali alkalmazásokkal való használatáról: [ebben a cikkben](scenario-desktop-overview.md).

A MSAL csomópontot a következő NPM-parancs futtatásával telepítheti.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

A MSAL csomóponthoz a következő kód hozzáadásával adhat hozzá hivatkozást:

```javascript
const { PublicClientApplication } = require('@azure/msal-node');
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```javascript
const MSAL_CONFIG = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
    },
};

const pca = new PublicClientApplication(MSAL_CONFIG);
```

> | Ahol: |Leírás |
> |---------|---------|
> | `clientId` | Az Azure Portalon regisztrált alkalmazás **alkalmazásazonosítója (ügyfél-azonosítója)**. Ezt az értéket az alkalmazás **Áttekintés** oldalán találja az Azure Portalon. |
> | `authority`    | A felhasználó által hitelesítendő STS-végpont. A nyilvános felhő esetében általában a (z) `https://login.microsoftonline.com/{tenant}` {bérlő} a bérlő vagy a bérlői azonosító neve.|

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Az engedélyezési kód első szakaszában a PKCE-mel a megfelelő paraméterekkel kell előkészíteni és elküldeni egy engedélyezési kód kérését. Ezután a folyamat második szakaszában figyelje az engedélyezési kód válaszát. A kód beszerzése után cserélje le a tokent.

```javascript
// The redirect URI you setup during app registration with a custom file protocol "msal"
const redirectUri = "msal://redirect";

const cryptoProvider = new CryptoProvider();

const pkceCodes = {
    challengeMethod: "S256", // Use SHA256 Algorithm
    verifier: "", // Generate a code verifier for the Auth Code Request first
    challenge: "" // Generate a code challenge from the previously generated code verifier
};

/**
 * Starts an interactive token request
 * @param {object} authWindow: Electron window object
 * @param {object} tokenRequest: token request object with scopes
 */
async function getTokenInteractive(authWindow, tokenRequest) {

    /**
     * Proof Key for Code Exchange (PKCE) Setup
     *
     * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod
     * parameters in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
     * second leg (acquireTokenByCode() API).
     */

    const {verifier, challenge} = await cryptoProvider.generatePkceCodes();

    pkceCodes.verifier = verifier;
    pkceCodes.challenge = challenge;

    const authCodeUrlParams = {
        redirectUri: redirectUri
        scopes: tokenRequest.scopes,
        codeChallenge: pkceCodes.challenge, // PKCE Code Challenge
        codeChallengeMethod: pkceCodes.challengeMethod // PKCE Code Challenge Method
    };

    const authCodeUrl = await pca.getAuthCodeUrl(authCodeUrlParams);

    // register the custom file protocol in redirect URI
    protocol.registerFileProtocol(redirectUri.split(":")[0], (req, callback) => {
        const requestUrl = url.parse(req.url, true);
        callback(path.normalize(`${__dirname}/${requestUrl.path}`));
    });

    const authCode = await listenForAuthCode(authCodeUrl, authWindow); // see below

    const authResponse = await pca.acquireTokenByCode({
        redirectUri: redirectUri,
        scopes: tokenRequest.scopes,
        code: authCode,
        codeVerifier: pkceCodes.verifier // PKCE Code Verifier
    });

    return authResponse;
}

/**
 * Listens for auth code response from Azure AD
 * @param {string} navigateUrl: URL where auth code response is parsed
 * @param {object} authWindow: Electron window object
 */
async function listenForAuthCode(navigateUrl, authWindow) {

    authWindow.loadURL(navigateUrl);

    return new Promise((resolve, reject) => {
        authWindow.webContents.on('will-redirect', (event, responseUrl) => {
            try {
                const parsedUrl = new URL(responseUrl);
                const authCode = parsedUrl.searchParams.get('code');
                resolve(authCode);
            } catch (err) {
                reject(err);
            }
        });
    });
}
```

> |Ahol:| Leírás |
> |---------|---------|
> | `authWindow` | Az aktuális elektron-ablak folyamatban. |
> | `tokenRequest` | A kért hatóköröket tartalmazza, például `"User.Read"` Microsoft Graph vagy `"api://<Application ID>/access_as_user"` egyéni webes API-k esetén. |

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az MSAL-csomóponttal rendelkező Electron desktop-alkalmazások fejlesztéséről, tekintse meg az oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag: bejelentkezés a felhasználókba és a Microsoft Graph API meghívása egy elektron asztali alkalmazásban](tutorial-v2-nodejs-desktop.md)