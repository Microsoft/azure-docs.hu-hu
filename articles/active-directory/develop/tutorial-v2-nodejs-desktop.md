---
title: 'Oktatóanyag: bejelentkezés a felhasználókba és a Microsoft Graph API meghívása egy Electron asztali alkalmazásban | Azure'
titleSuffix: Microsoft identity platform
description: Ebben az oktatóanyagban egy olyan Electron Desktop-alkalmazást hoz létre, amely bejelentkezhet a felhasználókba, és az Auth Code flow használatával beszerezhet egy hozzáférési jogkivonatot a Microsoft Identity platformból, és meghívhatja a Microsoft Graph API-t.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: e9e36a3ed2cab05eb63168452bc8472656ab5efb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644289"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-in-an-electron-desktop-app"></a>Oktatóanyag: bejelentkezés a felhasználókba és a Microsoft Graph API meghívása egy elektron asztali alkalmazásban

Ebben az oktatóanyagban egy olyan Electron Desktop-alkalmazást hoz létre, amely a felhasználók és a PKCE-vel való engedélyezési kód használatával meghívja a Microsoft Graph. Az Ön által felépített asztali alkalmazás a [Microsoft hitelesítési függvénytárát (MSAL) használja Node.jshoz ](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Kövesse az oktatóanyag lépéseit:

> [!div class="checklist"]
> - Az alkalmazás regisztrálása a Azure Portalban
> - Electron asztali alkalmazás projekt létrehozása
> - Hitelesítési logika hozzáadása az alkalmazáshoz
> - Metódus hozzáadása webes API meghívásához
> - Alkalmazás-regisztráció részleteinek hozzáadása
> - Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek

- [Node.js](https://nodejs.org/en/download/)
- [Electron](https://www.electronjs.org/)
- [Visual Studio Code](https://code.visualstudio.com/download) vagy más Kódszerkesztő

## <a name="register-the-application"></a>Az alkalmazás regisztrálása

Először végezze el az alkalmazás regisztrálása [a Microsoft Identity platformon](quickstart-register-app.md) című témakör lépéseit az alkalmazás regisztrálásához.

Az alkalmazás regisztrálásához használja az alábbi beállításokat:

- Név: `ElectronDesktopApp` (javasolt)
- Támogatott fióktípus: **bármely szervezeti címtárban (bármely Azure ad-címtár – több-bérlős) és személyes Microsoft-fiókokban (például Skype, Xbox) lévő fiókok**
- Platform típusa: **mobil-és asztali alkalmazások**
- Átirányítási URI: `msal://redirect`

## <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy mappát az alkalmazás üzemeltetéséhez, például *ElectronDesktopApp*.

1. Először váltson át a projekt könyvtárába a terminálon, majd futtassa a következő `npm` parancsokat:

    ```console
    npm init -y
    npm install --save @azure/msal-node axios bootstrap dotenv jquery popper.js
    npm install --save-dev babel electron@10.1.6 webpack
    ```

2. Ezután hozzon létre egy *app* nevű mappát. A mappában hozzon létre egy *index.html* nevű fájlt, amely felhasználói felületként fog szolgálni. Adja hozzá a következő kódot:

    ```html
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <meta http-equiv="Content-Security-Policy" content="script-src 'self' 'unsafe-inline';" />
        <title>MSAL Node Electron Sample App</title>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.min.css">

        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
    </head>

    <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
            <a class="navbar-brand">Microsoft identity platform</a>
            <div class="btn-group ml-auto dropleft">
                <button type="button" id="signIn" class="btn btn-secondary" aria-expanded="false">
                    Sign in
                </button>
                <button type="button" id="signOut" class="btn btn-success" hidden aria-expanded="false">
                    Sign out
                </button>
            </div>
        </nav>
        <br>
        <h5 class="card-header text-center">Electron sample app calling MS Graph API using MSAL Node</h5>
        <br>
        <div class="row" style="margin:auto">
            <div id="cardDiv" class="col-md-3" style="display:none">
                <div class="card text-center">
                    <div class="card-body">
                        <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails
                        </h5>
                        <div id="profileDiv"></div>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="seeProfile">See Profile</button>
                        <br>
                        <br>
                        <button class="btn btn-primary" id="readMail">Read Mails</button>
                    </div>
                </div>
            </div>
            <br>
            <br>
            <div class="col-md-4">
                <div class="list-group" id="list-tab" role="tablist">
                </div>
            </div>
            <div class="col-md-5">
                <div class="tab-content" id="nav-tabContent">
                </div>
            </div>
        </div>
        <br>
        <br>

        <script>
            window.jQuery = window.$ = require('jquery');
            require("./renderer.js");
        </script>

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="../node_modules/jquery/dist/jquery.js"></script>
        <script src="../node_modules/popper.js/dist/umd/popper.js"></script>
        <script src="../node_modules/bootstrap/dist/js/bootstrap.js"></script>
    </body>

    </html>
    ```

3. Ezután hozzon létre egy *main.js* nevű fájlt, és adja hozzá a következő kódot:

    ```JavaScript
    require('dotenv').config()

    const path = require('path');
    const { app, ipcMain, BrowserWindow } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    const { callEndpointWithToken } = require('./fetch');
    const AuthProvider = require('./AuthProvider');

    const authProvider = new AuthProvider();
    let mainWindow;

    function createWindow () {
        mainWindow = new BrowserWindow({
            width: 800,
            height: 600,
            webPreferences: {
            nodeIntegration: true
            }
        });

        mainWindow.loadFile(path.join(__dirname, './index.html'));
        };

    app.on('ready', () => {
        createWindow();
    });

    app.on('window-all-closed', () => {
        app.quit();
    });


    // Event handlers
    ipcMain.on(IPC_MESSAGES.LOGIN, async() => {
        const account = await authProvider.login(mainWindow);

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
    });

    ipcMain.on(IPC_MESSAGES.LOGOUT, async() => {
        await authProvider.logout();
        await mainWindow.loadFile(path.join(__dirname, './index.html'));
    });

    ipcMain.on(IPC_MESSAGES.GET_PROFILE, async() => {

        const tokenRequest = {
            scopes: ['User.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_PROFILE, graphResponse);
    });

    ipcMain.on(IPC_MESSAGES.GET_MAIL, async() => {

        const tokenRequest = {
            scopes: ['Mail.Read'],
        };

        const token = await authProvider.getToken(mainWindow, tokenRequest);
        const account = authProvider.account;

        await mainWindow.loadFile(path.join(__dirname, './index.html'));

        const graphResponse = await callEndpointWithToken(`${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`, token);

        mainWindow.webContents.send(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, account);
        mainWindow.webContents.send(IPC_MESSAGES.SET_MAIL, graphResponse);
    });
    ```

A fenti kódrészletben inicializálunk egy elektron főablakos objektumot, és létrehozunk néhány eseménykezelőt az elektron ablakmal való interakcióhoz. Emellett konfigurációs paramétereket is importálunk a bejelentkezéshez, a kijelentkezéshez és a tokenek beszerzéséhez, valamint a Microsoft Graph API meghívásához a *authProvider* osztály példányának létrehozásához.

4. Ugyanebben a mappában (*alkalmazásban*) hozzon létre egy *renderer.js* nevű fájlt, és adja hozzá a következő kódot:

    ```JavaScript
    const { ipcRenderer } = require('electron');
    const { IPC_MESSAGES } = require('./constants');

    // UI event handlers
    document.querySelector('#signIn').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGIN);
    });

    document.querySelector('#signOut').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.LOGOUT);
    });

    document.querySelector('#seeProfile').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_PROFILE);
    });

    document.querySelector('#readMail').addEventListener('click', () => {
        ipcRenderer.send(IPC_MESSAGES.GET_MAIL);
    });

    // Main process message subscribers
    ipcRenderer.on(IPC_MESSAGES.SHOW_WELCOME_MESSAGE, (event, account) => {
        showWelcomeMessage(account);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_PROFILE, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`);
    });

    ipcRenderer.on(IPC_MESSAGES.SET_MAIL, (event, graphResponse) => {
        updateUI(graphResponse, `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`);
    });

    // DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("signIn");
    const signOutButton = document.getElementById("signOut");
    const cardDiv = document.getElementById("cardDiv");
    const profileDiv = document.getElementById("profileDiv");
    const tabList = document.getElementById("list-tab");
    const tabContent = document.getElementById("nav-tabContent");

    function showWelcomeMessage(account) {
        cardDiv.style.display = "initial";
        welcomeDiv.innerHTML = `Welcome ${account.name}`;
        signInButton.hidden = true;
        signOutButton.hidden = false;
    }

    function clearTabs() {
        tabList.innerHTML = "";
        tabContent.innerHTML = "";
    }

    function updateUI(data, endpoint) {

        console.log(`Graph API responded at: ${new Date().toString()}`);

        if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_ME_ENDPOINT}`) {
            setProfile(data);
        } else if (endpoint === `${process.env.GRAPH_ENDPOINT_HOST}${process.env.GRAPH_MAIL_ENDPOINT}`) {
            setMail(data);
        }
    }

    function setProfile(data) {
        profileDiv.innerHTML = ''

        const title = document.createElement('p');
        const email = document.createElement('p');
        const phone = document.createElement('p');
        const address = document.createElement('p');

        title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
        email.innerHTML = "<strong>Mail: </strong>" + data.mail;
        phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
        address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;

        profileDiv.appendChild(title);
        profileDiv.appendChild(email);
        profileDiv.appendChild(phone);
        profileDiv.appendChild(address);
    }

    function setMail(data) {
        const mailInfo = data;
        if (mailInfo.value.length < 1) {
            alert("Your mailbox is empty!")
        } else {
            clearTabs();
            mailInfo.value.slice(0, 10).forEach((d, i) => {
                    createAndAppendListItem(d, i);
                    createAndAppendContentItem(d, i);
            });
        }
    }

    function createAndAppendListItem(d, i) {
        const listItem = document.createElement("a");
        listItem.setAttribute("class", "list-group-item list-group-item-action")
        listItem.setAttribute("id", "list" + i + "list")
        listItem.setAttribute("data-toggle", "list")
        listItem.setAttribute("href", "#list" + i)
        listItem.setAttribute("role", "tab")
        listItem.setAttribute("aria-controls", i)
        listItem.innerHTML = d.subject;
        tabList.appendChild(listItem);
    }

    function createAndAppendContentItem(d, i) {
        const contentItem = document.createElement("div");
        contentItem.setAttribute("class", "tab-pane fade")
        contentItem.setAttribute("id", "list" + i)
        contentItem.setAttribute("role", "tabpanel")
        contentItem.setAttribute("aria-labelledby", "list" + i + "list")

        if (d.from) {
            contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
            tabContent.appendChild(contentItem);
        }
    }
    ```

5. Végül hozzon létre egy *constants.js* nevű fájlt, amely az alkalmazás **eseményeinek** leírásához a karakterlánc-konstansokat fogja tárolni:

    ```JavaScript
    const IPC_MESSAGES = {
        SHOW_WELCOME_MESSAGE: 'SHOW_WELCOME_MESSAGE',
        LOGIN: 'LOGIN',
        LOGOUT: 'LOGOUT',
        GET_PROFILE: 'GET_PROFILE',
        SET_PROFILE: 'SET_PROFILE',
        GET_MAIL: 'GET_MAIL',
        SET_MAIL: 'SET_MAIL'
    }

    module.exports = {
        IPC_MESSAGES: IPC_MESSAGES,
    }
    ```

Most már rendelkezik egy egyszerű grafikus felhasználói felülettel és interakciókkal az Electron-alkalmazáshoz. Az oktatóanyag további részének befejezése után a projekt fájl-és mappa-struktúrájának a következőhöz hasonlóan kell kinéznie:

```
ElectronDesktopApp/
├── App
│   ├── authProvider.js
│   ├── constants.js
│   ├── fetch.js
│   ├── main.js
│   ├── renderer.js
│   ├── index.html
├── package.json
└── .env
```

## <a name="add-authentication-logic-to-your-app"></a>Hitelesítési logika hozzáadása az alkalmazáshoz

Az *alkalmazás* mappájában hozzon létre egy *AuthProvider.js* nevű fájlt. Ez egy olyan hitelesítési szolgáltatói osztályt fog tartalmazni, amely a MSAL csomópont használatával kezeli a bejelentkezés, a kijelentkezés, a jogkivonat-beszerzés, a fiók kiválasztása és a kapcsolódó hitelesítési feladatok Adja hozzá a következő kódot:

```JavaScript
const { PublicClientApplication, LogLevel, CryptoProvider } = require('@azure/msal-node');
const { protocol } = require('electron');
const path = require('path');
const url = require('url');

/**
 * To demonstrate best security practices, this Electron sample application makes use of
 * a custom file protocol instead of a regular web (https://) redirect URI in order to
 * handle the redirection step of the authorization flow, as suggested in the OAuth2.0 specification for Native Apps.
 */
const CUSTOM_FILE_PROTOCOL_NAME = process.env.REDIRECT_URI.split(':')[0]; // e.g. msal://redirect

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const MSAL_CONFIG = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: `${process.env.AAD_ENDPOINT_HOST}${process.env.TENANT_ID}`,
        redirectUri: process.env.REDIRECT_URI,
    },
    system: {
        loggerOptions: {
            loggerCallback(loglevel, message, containsPii) {
                console.log(message);
            },
            piiLoggingEnabled: false,
            logLevel: LogLevel.Verbose,
        }
    }
};

class AuthProvider {

    clientApplication;
    cryptoProvider;
    authCodeUrlParams;
    authCodeRequest;
    pkceCodes;
    account;

    constructor() {
        /**
         * Initialize a public client application. For more information, visit:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-public-client-application.md
         */
        this.clientApplication = new PublicClientApplication(MSAL_CONFIG);
        this.account = null;

        // Initialize CryptoProvider instance
        this.cryptoProvider = new CryptoProvider();

        this.setRequestObjects();
    }

    /**
     * Initialize request objects used by this AuthModule.
     */
    setRequestObjects() {
        const requestScopes =  ['openid', 'profile', 'User.Read'];
        const redirectUri = process.env.REDIRECT_URI;

        this.authCodeUrlParams = {
            scopes: requestScopes,
            redirectUri: redirectUri
        };

        this.authCodeRequest = {
            scopes: requestScopes,
            redirectUri: redirectUri,
            code: null
        }

        this.pkceCodes = {
            challengeMethod: "S256", // Use SHA256 Algorithm
            verifier: "", // Generate a code verifier for the Auth Code Request first
            challenge: "" // Generate a code challenge from the previously generated code verifier
        };
    }

    async login(authWindow) {
        const authResult = await this.getTokenInteractive(authWindow, this.authCodeUrlParams);
        return this.handleResponse(authResult);
    }

    async logout() {
        if (this.account) {
            await this.clientApplication.getTokenCache().removeAccount(this.account);
            this.account = null;
        }
    }

    async getToken(authWindow, tokenRequest) {
        let authResponse;

        authResponse = await this.getTokenInteractive(authWindow, tokenRequest);

        return authResponse.accessToken || null;
    }

    // This method contains an implementation of access token acquisition in authorization code flow
    async getTokenInteractive(authWindow, tokenRequest) {

        /**
         * Proof Key for Code Exchange (PKCE) Setup
         *
         * MSAL enables PKCE in the Authorization Code Grant Flow by including the codeChallenge and codeChallengeMethod parameters
         * in the request passed into getAuthCodeUrl() API, as well as the codeVerifier parameter in the
         * second leg (acquireTokenByCode() API).
         *
         * MSAL Node provides PKCE Generation tools through the CryptoProvider class, which exposes
         * the generatePkceCodes() asynchronous API. As illustrated in the example below, the verifier
         * and challenge values should be generated previous to the authorization flow initiation.
         *
         * For details on PKCE code generation logic, consult the
         * PKCE specification https://tools.ietf.org/html/rfc7636#section-4
         */

        const {verifier, challenge} = await this.cryptoProvider.generatePkceCodes();

        this.pkceCodes.verifier = verifier;
        this.pkceCodes.challenge = challenge;

        const authCodeUrlParams = {
            ...this.authCodeUrlParams,
            scopes: tokenRequest.scopes,
            codeChallenge: this.pkceCodes.challenge, // PKCE Code Challenge
            codeChallengeMethod: this.pkceCodes.challengeMethod // PKCE Code Challenge Method
        };

        const authCodeUrl = await this.clientApplication.getAuthCodeUrl(authCodeUrlParams);

        protocol.registerFileProtocol(CUSTOM_FILE_PROTOCOL_NAME, (req, callback) => {
            const requestUrl = url.parse(req.url, true);
            callback(path.normalize(`${__dirname}/${requestUrl.path}`));
        });

        const authCode = await this.listenForAuthCode(authCodeUrl, authWindow);

        const authResponse = await this.clientApplication.acquireTokenByCode({
            ...this.authCodeRequest,
            scopes: tokenRequest.scopes,
            code: authCode,
            codeVerifier: this.pkceCodes.verifier // PKCE Code Verifier
        });

        return authResponse;
    }

    // Listen for authorization code response from Azure AD
    async listenForAuthCode(navigateUrl, authWindow) {

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

    /**
     * Handles the response from a popup or redirect. If response is null, will check if we have any accounts and attempt to sign in.
     * @param response
     */
    async handleResponse(response) {
        if (response !== null) {
            this.account = response.account;
        } else {
            this.account = await this.getAccount();
        }

        return this.account;
    }

    /**
     * Calls getAllAccounts and determines the correct account to sign into, currently defaults to first account found in cache.
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    async getAccount() {
        const cache = this.clientApplication.getTokenCache();
        const currentAccounts = await cache.getAllAccounts();

        if (currentAccounts === null) {
            console.log('No accounts detected');
            return null;
        }

        if (currentAccounts.length > 1) {
            // Add choose account code here
            console.log('Multiple accounts detected, need to add choose account code.');
            return currentAccounts[0];
        } else if (currentAccounts.length === 1) {
            return currentAccounts[0];
        } else {
            return null;
        }
    }
}

module.exports = AuthProvider;
```

A fenti kódrészletben először inicializálta a MSAL csomópontot `PublicClientApplication` egy konfigurációs objektum () átadásával `msalConfig` . Ezt követően `login` `logout` `getToken` a fő modul (*main.js*) által meghívott metódusok. `login`És `getToken` rendszer esetén az azonosító-és hozzáférési jogkivonatokat először egy engedélyezési kód megadásával, majd az MSAL Node nyilvános API-val történő cseréjével, a-ben és a-ben is megszerezzük `acquireTokenByCode` .

## <a name="add-a-method-to-call-a-web-api"></a>Metódus hozzáadása webes API meghívásához

Hozzon létre egy másik, *fetch.js* nevű fájlt. Ez a fájl tartalmaz egy Axios HTTP-ügyfelet, amely REST-hívásokat tesz a Microsoft Graph API-nak.

```JavaScript
const axios = require('axios');

/**
 * Makes an Authorization 'Bearer' request with the given accessToken to the given endpoint.
 * @param endpoint
 * @param accessToken
 */
async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

module.exports = {
    callEndpointWithToken: callEndpointWithToken,
};
```

## <a name="add-app-registration-details"></a>Alkalmazás-regisztráció részleteinek hozzáadása

Végül hozzon létre egy környezeti fájlt, amely a jogkivonatok beszerzéséhez használt alkalmazás-regisztrációs adatokat tárolja. Ehhez hozzon létre egy *. env* nevű fájlt a minta gyökérkönyvtárában (*ElectronDesktopApp*), és adja hozzá a következő kódot:

```
# Credentials
CLIENT_ID=Enter_the_Application_Id_Here
TENANT_ID=Enter_the_Tenant_Id_Here

# Configuration
REDIRECT_URI=msal://redirect

# Endpoints
AAD_ENDPOINT_HOST=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT_HOST=Enter_the_Graph_Endpoint_Here

# RESOURCES
GRAPH_ME_ENDPOINT=v1.0/me
GRAPH_MAIL_ENDPOINT=v1.0/me/messages

# SCOPES
GRAPH_SCOPES=User.Read Mail.Read
```

Adja meg ezeket az adatokat az Azure-alkalmazás regisztrációs portálján beszerzett értékekkel:

- `Enter_the_Tenant_Id_here` a következők egyikének kell lennie:
  - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére**. Például: `contoso.microsoft.com`.
  - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `organizations` .
  - Ha az alkalmazás a *szervezeti címtárban és a személyes Microsoft-fiókokban is támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `common` .
  - A *személyes Microsoft-fiókok* támogatásának korlátozásához cserélje le ezt az értéket a következőre: `consumers` .
- `Enter_the_Application_Id_Here`: A regisztrált alkalmazás **alkalmazás-(ügyfél-) azonosítója** .
- `Enter_the_Cloud_Instance_Id_Here`: Az Azure Cloud-példány, amelyben az alkalmazás regisztrálva van.
  - A fő (vagy *globális*) Azure-felhőhöz írja be a következőt: `https://login.microsoftonline.com/` .
  - Az **országos** felhők esetében (például Kína) a megfelelő értékeket találja a [nemzeti felhőkben](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` a Microsoft Graph API azon példánya, amellyel az alkalmazásnak kommunikálnia kell.
  - A **globális** Microsoft Graph API-végpont esetében cserélje le a sztring mindkét példányát a következőre: `https://graph.microsoft.com/` .
  - Az **országos** Felhőbeli üzemelő példányok esetében a Microsoft Graph dokumentációjában tekintse meg a [nemzeti Felhőbeli központi telepítések](/graph/deployments) című témakört.

## <a name="test-the-app"></a>Az alkalmazás tesztelése

Elkészült az alkalmazás létrehozásával, és most már készen áll az Electron asztali alkalmazás elindítására és az alkalmazás működésének tesztelésére.

1. Indítsa el az alkalmazást a következő parancs futtatásával a projekt mappájának gyökeréből:

```console
electron App/main.js
```

2. Az alkalmazás főablakában látnia kell a *index.html* -fájl tartalmát és a **Bejelentkezés** gombot.

## <a name="test-sign-in-and-sign-out"></a>Bejelentkezés tesztelése és kijelentkezés

A *index.html* -fájl betöltése után válassza a **Bejelentkezés** lehetőséget. A rendszer felszólítja, hogy jelentkezzen be a Microsoft Identity platformmal:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-01-signin-prompt.png" alt-text="bejelentkezési kérés":::

Ha beleegyezik a kért engedélyekkel, a webalkalmazások megjelenítik a felhasználónevet, és sikeres bejelentkezést jeleznek:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-03-after-signin.png" alt-text="sikeres bejelentkezés":::

## <a name="test-web-api-call"></a>Webes API-hívás tesztelése

A bejelentkezést követően válassza a **Profil megtekintése** lehetőséget a Microsoft Graph API-hívásra adott válaszban visszaadott felhasználói profil adatainak megtekintéséhez:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-04-profile.png" alt-text="profil adatai a Microsoft Graphból":::

Válassza a **levelek olvasása** lehetőséget a felhasználói fiókban lévő üzenetek megtekintéséhez. Egy beleegyezési képernyő jelenik meg:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-05-consent-mail.png" alt-text="beleegyezési képernyő a Read. mail engedélyhez":::

A beleegyezikés után megtekintheti a válaszban visszaadott üzeneteket a Microsoft Graph API-nak küldött hívástól:

:::image type="content" source="media/tutorial-v2-nodejs-desktop/desktop-06-mails.png" alt-text="Microsoft Graph levelezési adatai":::

## <a name="how-the-application-works"></a>Az alkalmazás működése

Amikor a felhasználó első alkalommal kiválasztja a **Bejelentkezés** gombot, a rendszer a Get `getTokenInteractive` metódust hívja meg *AuthProvider.js* . Ezzel a módszerrel a rendszer átirányítja a felhasználót a *Microsoft Identity platform végpontjának* bejelentkezni, és érvényesíti a felhasználó hitelesítő adatait, majd beolvassa az **engedélyezési kódot**. Ezt a kódot a rendszer az `acquireTokenByCode` MSAL csomópont nyilvános API használatával cseréli ki egy hozzáférési tokenre.

Ekkor a rendszer elküld egy PKCE-védelemmel ellátott hitelesítési kódot a CORS által védett jogkivonat-végpontba, és kicseréli a jogkivonatokat. Az alkalmazás fogadja az azonosító jogkivonatot, a hozzáférési jogkivonatot és a frissítési jogkivonatot, és a MSAL-csomópont dolgozza fel, a jogkivonatokban tárolt információk pedig gyorsítótárazva lesznek.

Az azonosító jogkivonat a felhasználóra vonatkozó alapszintű információkat tartalmaz, például a megjelenítendő nevüket. A hozzáférési jogkivonat korlátozott élettartammal rendelkezik, és 24 óra elteltével lejár. Ha azt tervezi, hogy ezeket a jogkivonatokat a védett erőforrásokhoz való hozzáféréshez szeretné használni, a háttér-kiszolgálónak *ellenőriznie kell* , hogy a tokent egy érvényes felhasználónak adta-e ki az alkalmazás számára.

Az oktatóanyagban létrehozott asztali alkalmazás REST-hívást kezdeményez a Microsoft Graph API-hoz egy hozzáférési jogkivonat használatával, mint tulajdonosi jogkivonat a kérelem fejlécében ([RFC 6750](https://tools.ietf.org/html/rfc6750)).

A Microsoft Graph API-nak a felhasználónak *. Read* hatókörrel kell rendelkeznie a felhasználói profil olvasásához. Alapértelmezés szerint a rendszer automatikusan hozzáadja ezt a hatókört a Azure Portalban regisztrált összes alkalmazáshoz. A Microsoft Graph egyéb API-jai, valamint a háttér-kiszolgálóhoz tartozó egyéni API-k további hatóköröket is igényelhetnek. A Microsoft Graph API-nak például a *mail. Read* hatókört kell megadnia a felhasználó e-mail-címének listázásához.

Hatókörök hozzáadásakor a rendszer kérni fogja a felhasználókat, hogy adjon meg további hozzájárulásukat a hozzáadott hatókörökhöz.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

Ha részletesebben szeretne tájékozódni a Microsoft Identity platformon a Node.js és az Electron asztali alkalmazások fejlesztéséről, tekintse meg a többrészes forgatókönyvek sorozatát:

> [!div class="nextstepaction"]
> [Forgatókönyv: webes API-kat meghívó asztali alkalmazás](scenario-desktop-overview.md)
