---
title: 'Oktatóanyag: auth-programkódot használó egyoldalas JavaScript-alkalmazás létrehozása | Azure'
titleSuffix: Microsoft identity platform
description: Ebben az oktatóanyagban létrehoz egy JavaScript SPA-t, amely bejelentkezhet a felhasználókba, és az Auth Code flow használatával beszerezhet egy hozzáférési jogkivonatot a Microsoft Identity platformból, és meghívhatja a Microsoft Graph API-t.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 1ec046ca6b42a5ca8f33b0347c562c85abd42684
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98756167"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-app-spa-using-auth-code-flow"></a>Oktatóanyag: bejelentkezés a felhasználókba és a Microsoft Graph API meghívása egy JavaScript-alapú egyoldalas alkalmazásból (SPA) az Auth Code flow használatával

Ebben az oktatóanyagban egy olyan JavaScript-alapú egyoldalas alkalmazást (SPA) hoz létre, amely bejelentkezik a felhasználókba, és meghívja a Microsoft Grapht a PKCE-vel való engedélyezési kód használatával. Az Ön által létrehozott SPA a Microsoft Authentication Library (MSAL) használata a JavaScript 2.0-s verzióhoz.

Ebben az oktatóanyagban:
> [!div class="checklist"]
> * A OAuth 2,0 engedélyezési kód folyamatának végrehajtása a PKCE
> * Személyes Microsoft-fiókok, valamint munkahelyi és iskolai fiókok aláírása
> * Hozzáférési jogkivonat beszerzése
> * Hívja meg Microsoft Graph vagy saját API-ját, amelyhez a Microsoft Identity platformból beszerzett hozzáférési jogkivonatok szükségesek

A MSAL.js 2,0 a MSAL.js 1,0-es verziójában javítja az engedélyezési kód folyamatát a böngészőben az implicit engedélyezési folyamat helyett. A MSAL.js 2,0 **nem támogatja az** implicit folyamatot.

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/download/) helyi webkiszolgáló futtatásához
* [Visual Studio Code](https://code.visualstudio.com/download) vagy más Kódszerkesztő

## <a name="how-the-tutorial-app-works"></a>Az oktatóanyag alkalmazás működése

:::image type="content" source="media/tutorial-v2-javascript-auth-code/diagram-01-auth-code-flow.png" alt-text="Egy egyoldalas alkalmazásban az engedélyezési kód folyamatát ábrázoló diagram":::

Az oktatóanyagban létrehozott alkalmazás lehetővé teszi a JavaScript SPA számára, hogy lekérdezze a Microsoft Graph API-t a biztonsági jogkivonatok beszerzésével a Microsoft Identity platformból. Ebben az esetben a felhasználó bejelentkezése után hozzáférési jogkivonatot kér, és hozzáadja a HTTP-kérelmekhez az engedélyezési fejlécben. A token beszerzését és megújítását a JavaScripthez készült Microsoft Authentication Library (MSAL.js) kezeli.

Ez az oktatóanyag a következő könyvtárat használja:

A Microsoft hitelesítési függvénytárának [msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) a JavaScript v 2.0 böngésző csomaghoz

## <a name="get-the-completed-code-sample"></a>Az elkészült mintakód beolvasása

Inkább az oktatóanyag befejezett projektjét szeretné letölteni? Ha a projektet helyi webkiszolgálón (például Node.js) szeretné futtatni, akkor az [MS-Identity-JavaScript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) tárházat klónozással:

`git clone https://github.com/Azure-Samples/ms-identity-javascript-v2`

Ezután a kód minta konfigurálásához a végrehajtás előtt ugorjon a [konfigurációs lépésre](#register-your-application).

Ha folytatni szeretné az oktatóanyagot, és saját maga is felépíti az alkalmazást, folytassa a következő szakasszal, [előfeltételekkel](#prerequisites).

## <a name="create-your-project"></a>Projekt létrehozása

A [Node.js](https://nodejs.org/en/download/) telepítése után hozzon létre egy mappát az alkalmazás üzemeltetéséhez, például: *msal-Spa-tutorial*.

Ezután hozzon létre egy kis [expressz](https://expressjs.com/) webkiszolgálót a *index.html* -fájl kiszolgálásához.

1. Először váltson át a projekt könyvtárába a terminálon, majd futtassa a következő `npm` parancsokat:
    ```console
    npm init -y
    npm install @azure/msal-browser
    npm install express
    npm install morgan
    npm install yargs
    ```
2. Ezután hozzon létre egy *server.js* nevű fájlt, és adja hozzá a következő kódot:

   ```JavaScript
   const express = require('express');
   const morgan = require('morgan');
   const path = require('path');
   const argv = require('yargs')
      .usage('Usage: $0 -p [PORT]')
      .alias('p', 'port')
      .describe('port', '(Optional) Port Number - default is 3000')
      .strict()
      .argv;

   const DEFAULT_PORT = 3000;

   //initialize express.
   const app = express();

   // Initialize variables.
   let port = DEFAULT_PORT; // -p {PORT} || 3000;
   if (argv.p) {
      port = argv.p;
   }

   // Configure morgan module to log all requests.
   app.use(morgan('dev'));

   // Set the front-end folder to serve public assets.
   app.use("/lib", express.static(path.join(__dirname, "../../lib/msal-browser/lib")));

   // Setup app folders
   app.use(express.static('app'));

   // Set up a route for index.html.
   app.get('*', function (req, res) {
      res.sendFile(path.join(__dirname + '/index.html'));
   });

   // Start the server.
   app.listen(port);
   console.log(`Listening on port ${port}...`);
    ```

Most már rendelkezik egy kis webkiszolgálóval a SPA kiszolgálásához. Az oktatóanyag további részének befejezése után a projekt fájl-és mappa-struktúrájának a következőhöz hasonlóan kell kinéznie:

```
msal-spa-tutorial/
├── app
│   ├── authConfig.js
│   ├── authPopup.js
│   ├── authRedirect.js
│   ├── graphConfig.js
│   ├── graph.js
│   ├── index.html
│   └── ui.js
└── server.js
```

## <a name="create-the-spa-ui"></a>A SPA felhasználói felületének létrehozása

1. Hozzon létre egy *alkalmazás* -mappát a projekt könyvtárában, és hozzon létre egy *index.html* -fájlt a JavaScript Spa számára. Ez a fájl a rendszerindítási **4 keretrendszerrel** létrehozott felhasználói felületet implementálja, és a konfigurációs, hitelesítési és API-hívások parancsfájl-fájljait importálja.

    A *index.html* fájlban adja hozzá a következő kódot:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no">
        <title>Tutorial | MSAL.js JavaScript SPA</title>

        <!-- IE support: add promises polyfill before msal.js  -->
        <script type="text/javascript" src="//cdn.jsdelivr.net/npm/bluebird@3.7.2/js/browser/bluebird.min.js"></script>
        <script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.4/js/msal-browser.js" integrity="sha384-7sxY2tN3GMVE5jXH2RL9AdbO6s46vUh9lUid4yNCHJMUzDoj+0N4ve6rLOmR88yN" crossorigin="anonymous"></script>

        <!-- adding Bootstrap 4 for UI components  -->
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh" crossorigin="anonymous">
        <link rel="SHORTCUT ICON" href="https://c.s-microsoft.com/favicon.ico?v2" type="image/x-icon">
      </head>
      <body>
        <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
          <a class="navbar-brand" href="/">Microsoft identity platform</a>
          <div class="btn-group ml-auto dropleft">
              <button type="button" id="SignIn" class="btn btn-secondary" onclick="signIn()">
                Sign In
              </button>
          </div>
        </nav>
        <br>
        <h5 class="card-header text-center">JavaScript SPA calling Microsoft Graph API with MSAL.js</h5>
        <br>
        <div class="row" style="margin:auto" >
        <div id="card-div" class="col-md-3" style="display:none">
        <div class="card text-center">
          <div class="card-body">
            <h5 class="card-title" id="WelcomeMessage">Please sign-in to see your profile and read your mails</h5>
            <div id="profile-div"></div>
            <br>
            <br>
            <button class="btn btn-primary" id="seeProfile" onclick="seeProfile()">See Profile</button>
            <br>
            <br>
            <button class="btn btn-primary" id="readMail" onclick="readMail()">Read Mail</button>
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

        <!-- importing bootstrap.js and supporting js libraries -->
        <script src="https://code.jquery.com/jquery-3.4.1.slim.min.js" integrity="sha384-J6qa4849blE2+poT4WnyKhv5vZF5SrPo0iEjwBvKU7imGFAV0wwj1yYfoRSJoZ+n" crossorigin="anonymous"></script>
        <script src="https://cdn.jsdelivr.net/npm/popper.js@1.16.0/dist/umd/popper.min.js" integrity="sha384-Q6E9RHvbIyZFJoft+2mJbHaEWldlvI9IOYy5n3zV9zzTtmI3UksdQRVvoxMfooAo" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js" integrity="sha384-wfSDF2E50Y2D1uUdj0O3uMBJnjuUD4Ih7YwaYd1iqfktj0Uod8GCExl3Og8ifwB6" crossorigin="anonymous"></script>

        <!-- importing app scripts (load order is important) -->
        <script type="text/javascript" src="./authConfig.js"></script>
        <script type="text/javascript" src="./graphConfig.js"></script>
        <script type="text/javascript" src="./ui.js"></script>

        <!-- <script type="text/javascript" src="./authRedirect.js"></script>   -->
        <!-- uncomment the above line and comment the line below if you would like to use the redirect flow -->
        <script type="text/javascript" src="./authPopup.js"></script>
        <script type="text/javascript" src="./graph.js"></script>
      </body>
    </html>
    ```

2. Ezután az *alkalmazás* mappájában is hozzon létre egy *ui.js* nevű fájlt, és adja hozzá a következő kódot. Ez a fájl a DOM-elemek elérését és frissítését fogja elérni.

    ```JavaScript
    // Select DOM elements to work with
    const welcomeDiv = document.getElementById("WelcomeMessage");
    const signInButton = document.getElementById("SignIn");
    const cardDiv = document.getElementById("card-div");
    const mailButton = document.getElementById("readMail");
    const profileButton = document.getElementById("seeProfile");
    const profileDiv = document.getElementById("profile-div");

    function showWelcomeMessage(account) {
        // Reconfiguring DOM elements
        cardDiv.style.display = 'initial';
        welcomeDiv.innerHTML = `Welcome ${account.username}`;
        signInButton.setAttribute("onclick", "signOut();");
        signInButton.setAttribute('class', "btn btn-success")
        signInButton.innerHTML = "Sign Out";
    }

    function updateUI(data, endpoint) {
        console.log('Graph API responded at: ' + new Date().toString());

        if (endpoint === graphConfig.graphMeEndpoint) {
            const title = document.createElement('p');
            title.innerHTML = "<strong>Title: </strong>" + data.jobTitle;
            const email = document.createElement('p');
            email.innerHTML = "<strong>Mail: </strong>" + data.mail;
            const phone = document.createElement('p');
            phone.innerHTML = "<strong>Phone: </strong>" + data.businessPhones[0];
            const address = document.createElement('p');
            address.innerHTML = "<strong>Location: </strong>" + data.officeLocation;
            profileDiv.appendChild(title);
            profileDiv.appendChild(email);
            profileDiv.appendChild(phone);
            profileDiv.appendChild(address);

        } else if (endpoint === graphConfig.graphMailEndpoint) {
            if (data.value.length < 1) {
                alert("Your mailbox is empty!")
            } else {
                const tabList = document.getElementById("list-tab");
                tabList.innerHTML = ''; // clear tabList at each readMail call
                const tabContent = document.getElementById("nav-tabContent");

                data.value.map((d, i) => {
                    // Keeping it simple
                    if (i < 10) {
                        const listItem = document.createElement("a");
                        listItem.setAttribute("class", "list-group-item list-group-item-action")
                        listItem.setAttribute("id", "list" + i + "list")
                        listItem.setAttribute("data-toggle", "list")
                        listItem.setAttribute("href", "#list" + i)
                        listItem.setAttribute("role", "tab")
                        listItem.setAttribute("aria-controls", i)
                        listItem.innerHTML = d.subject;
                        tabList.appendChild(listItem)

                        const contentItem = document.createElement("div");
                        contentItem.setAttribute("class", "tab-pane fade")
                        contentItem.setAttribute("id", "list" + i)
                        contentItem.setAttribute("role", "tabpanel")
                        contentItem.setAttribute("aria-labelledby", "list" + i + "list")
                        contentItem.innerHTML = "<strong> from: " + d.from.emailAddress.address + "</strong><br><br>" + d.bodyPreview + "...";
                        tabContent.appendChild(contentItem);
                    }
                });
            }
        }
    }
    ```

## <a name="register-your-application"></a>Az alkalmazás regisztrálása

Kövesse az [egyoldalas alkalmazás lépéseit: az alkalmazások regisztrálásával](scenario-spa-app-registration.md) hozzon létre egy alkalmazást az Ön gyógyfürdőjében.

Az [átirányítási URI: MSAL.js 2,0 és az Auth kód flow](scenario-spa-app-registration.md#redirect-uri-msaljs-20-with-auth-code-flow) lépésnél adja meg `http://localhost:3000` az alapértelmezett helyet, ahol az oktatóanyag alkalmazása fut.

Ha másik portot szeretne használni, írja be a (z `http://localhost:<port>` ) értéket, ahol a `<port>` az elsődleges TCP-portszáma. Ha más portszámot ad meg, akkor `3000` a *server.js* a kívánt portszámot is frissíti.

### <a name="configure-your-javascript-spa"></a>A JavaScript SPA konfigurálása

Hozzon létre egy *authConfig.js* nevű fájlt az *alkalmazás* mappájában, hogy tartalmazza a hitelesítés konfigurációs paramétereit, majd adja hozzá a következő kódot:

```javascript
const msalConfig = {
  auth: {
    clientId: "Enter_the_Application_Id_Here",
    authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Info_Here",
    redirectUri: "Enter_the_Redirect_Uri_Here",
  },
  cache: {
    cacheLocation: "sessionStorage", // This configures where your cache will be stored
    storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
  }
};

// Add scopes here for ID token to be used at Microsoft identity platform endpoints.
const loginRequest = {
 scopes: ["openid", "profile", "User.Read"]
};

// Add scopes here for access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
 scopes: ["User.Read", "Mail.Read"]
};
```

Módosítsa a szakasz értékeit az `msalConfig` itt leírtak szerint:

- `Enter_the_Application_Id_Here`: A regisztrált alkalmazás **alkalmazás-(ügyfél-) azonosítója** .
- `Enter_the_Cloud_Instance_Id_Here`: Az Azure Cloud-példány, amelyben az alkalmazás regisztrálva van.
  - A fő (vagy *globális*) Azure-felhőhöz írja be a következőt: `https://login.microsoftonline.com` .
  - Az **országos** felhők esetében (például Kína) a megfelelő értékeket találja a [nemzeti felhőkben](authentication-national-cloud.md).
- `Enter_the_Tenant_info_here` a következők egyikének kell lennie:
  - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére**. Például: `contoso.microsoft.com`.
  - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `organizations` .
  - Ha az alkalmazás a *szervezeti címtárban és a személyes Microsoft-fiókokban is támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `common` .
  - A *személyes Microsoft-fiókok* támogatásának korlátozásához cserélje le ezt az értéket a következőre: `consumers` .
- A `Enter_the_Redirect_Uri_Here` értéke `http://localhost:3000`.

`authority`Ha a globális Azure-felhőt használja, a *authConfig.js* az alábbihoz hasonló értéknek kell megjelennie:

```javascript
authority: "https://login.microsoftonline.com/common",
```

Még mindig az *alkalmazás* mappájában hozzon létre egy *graphConfig.js* nevű fájlt. Adja hozzá a következő kódot, amely megadja az alkalmazásnak a Microsoft Graph API meghívásához szükséges konfigurációs paramétereket:

```javascript
// Add the endpoints here for Microsoft Graph API services you'd like to use.
const graphConfig = {
    graphMeEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me",
    graphMailEndpoint: "Enter_the_Graph_Endpoint_Here/v1.0/me/messages"
};
```

Módosítsa a szakasz értékeit az `graphConfig` itt leírtak szerint:

- `Enter_the_Graph_Endpoint_Here` a Microsoft Graph API azon példánya, amellyel az alkalmazásnak kommunikálnia kell.
  - A **globális** Microsoft Graph API-végpont esetében cserélje le a sztring mindkét példányát a következőre: `https://graph.microsoft.com` .
  - Az **országos** Felhőbeli üzemelő példányok esetében a Microsoft Graph dokumentációjában tekintse meg a [nemzeti Felhőbeli központi telepítések](/graph/deployments) című témakört.

A `graphMeEndpoint` `graphMailEndpoint` globális végpont használata esetén a *graphConfig.js* a következőhöz hasonlónak kell lennie:

```javascript
graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
```

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-user"></a>Felhasználói bejelentkezés a Microsoft Authentication Library (MSAL) használatával

### <a name="pop-up"></a>Felugró ablak

Az *alkalmazás* mappájában hozzon létre egy *authPopup.js* nevű fájlt, és adja hozzá a következő hitelesítési és jogkivonat-beszerzési kódot a bejelentkezési előugró ablakhoz:

```JavaScript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let username = "";

function loadPage() {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    const currentAccounts = myMSALObj.getAllAccounts();
    if (currentAccounts === null) {
        return;
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
        console.warn("Multiple accounts detected.");
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
        showWelcomeMessage(currentAccounts[0]);
    }
}

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        loadPage();
    }
}

function signIn() {
    myMSALObj.loginPopup(loginRequest).then(handleResponse).catch(error => {
        console.error(error);
    });
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenPopup(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
        console.warn("silent token acquisition fails. acquiring token using redirect");
        if (error instanceof msal.InteractionRequiredAuthError) {
            // fallback to interaction when silent call fails
            return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
                console.log(tokenResponse);

                return tokenResponse;
            }).catch(error => {
                console.error(error);
            });
        } else {
            console.warn(error);
        }
    });
}

function seeProfile() {
    getTokenPopup(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenPopup(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}

loadPage();
```

### <a name="redirect"></a>Átirányítás

Hozzon létre egy *authRedirect.js* nevű fájlt az *app* mappában, és adja hozzá a következő hitelesítési és jogkivonat-beszerzési kódot a bejelentkezés átirányításához:

```javascript
// Create the main myMSALObj instance
// configuration parameters are located at authConfig.js
const myMSALObj = new msal.PublicClientApplication(msalConfig);

let accessToken;
let username = "";

// Redirect: once login is successful and redirects with tokens, call Graph API
myMSALObj.handleRedirectPromise().then(handleResponse).catch(err => {
    console.error(err);
});

function handleResponse(resp) {
    if (resp !== null) {
        username = resp.account.username;
        showWelcomeMessage(resp.account);
    } else {
        /**
         * See here for more info on account retrieval:
         * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
         */
        const currentAccounts = myMSALObj.getAllAccounts();
        if (currentAccounts === null) {
            return;
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
            console.warn("Multiple accounts detected.");
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
            showWelcomeMessage(currentAccounts[0]);
        }
    }
}

function signIn() {
    myMSALObj.loginRedirect(loginRequest);
}

function signOut() {
    const logoutRequest = {
        account: myMSALObj.getAccountByUsername(username)
    };

    myMSALObj.logout(logoutRequest);
}

function getTokenRedirect(request) {
    /**
     * See here for more info on account retrieval:
     * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-common/docs/Accounts.md
     */
    request.account = myMSALObj.getAccountByUsername(username);
    return myMSALObj.acquireTokenSilent(request).catch(error => {
            console.warn("silent token acquisition fails. acquiring token using redirect");
            if (error instanceof msal.InteractionRequiredAuthError) {
                // fallback to interaction when silent call fails
                return myMSALObj.acquireTokenRedirect(request);
            } else {
                console.warn(error);
            }
        });
}

function seeProfile() {
    getTokenRedirect(loginRequest).then(response => {
        callMSGraph(graphConfig.graphMeEndpoint, response.accessToken, updateUI);
        profileButton.classList.add('d-none');
        mailButton.classList.remove('d-none');
    }).catch(error => {
        console.error(error);
    });
}

function readMail() {
    getTokenRedirect(tokenRequest).then(response => {
        callMSGraph(graphConfig.graphMailEndpoint, response.accessToken, updateUI);
    }).catch(error => {
        console.error(error);
    });
}
```

### <a name="how-the-code-works"></a>A kód működése

Amikor a felhasználó első alkalommal kiválasztja a **Bejelentkezés** gombot, a `signIn` metódus meghívja a `loginPopup` felhasználót a bejelentkezéshez. A `loginPopup` metódus megnyit egy előugró ablakot a *Microsoft Identity platform végpontján* a felhasználó hitelesítő adatainak megadásához és érvényesítéséhez. Sikeres bejelentkezés után *msal.js* kezdeményezi az [engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md).

Ekkor a rendszer elküld egy PKCE-védelemmel ellátott hitelesítési kódot a CORS által védett jogkivonat-végpontba, és kicseréli a jogkivonatokat. Az alkalmazás egy azonosító jogkivonatot, hozzáférési jogkivonatot és frissítési jogkivonatot fogad el, és *msal.js* dolgozza fel, és a jogkivonatokban tárolt információk gyorsítótárazva vannak.

Az azonosító jogkivonat a felhasználóra vonatkozó alapszintű információkat tartalmaz, például a megjelenítendő nevüket. Ha azt tervezi, hogy az azonosító jogkivonat által megadott bármilyen adatforrást használ, a háttér-kiszolgálónak *ellenőriznie kell* , hogy a tokent egy érvényes felhasználónak adta-e ki az alkalmazás számára.

A hozzáférési jogkivonat korlátozott élettartammal rendelkezik, és 24 óra elteltével lejár. A frissítési token használatával csendesen szerezhetők be új hozzáférési tokenek.

Az oktatóanyagban létrehozott `acquireTokenSilent` és/vagy a `acquireTokenPopup` Microsoft Graph API felhasználói profil adatainak lekérdezéséhez használt *hozzáférési token* beszerzése. Ha szüksége van egy olyan mintára, amely érvényesíti az azonosító jogkivonatot, tekintse meg az [Active Directory-JavaScript-singlepageapp-DotNet-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) minta alkalmazást a githubon. A minta egy ASP.NET webes API-t használ a jogkivonat-ellenőrzéshez.

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

A kezdeti bejelentkezés után az alkalmazásnak nem kell megkérnie a felhasználókat, hogy minden alkalommal újra hitelesíteniük kell magukat a védett erőforrásokhoz való hozzáféréshez (azaz jogkivonat igényléséhez). Az ilyen újrahitelesítési kérelmek megtiltásához hívja a következőt: `acquireTokenSilent` . Bizonyos esetekben azonban előfordulhat, hogy a felhasználókat a Microsoft Identity platformmal való interakcióra kényszeríteni kell. Például:

- A felhasználóknak újra meg kell adniuk a hitelesítő adataikat, mert a jelszó lejárt.
- Az alkalmazás hozzáférést kér egy erőforráshoz, és szüksége van a felhasználó belefoglalására.
- Kétfaktoros hitelesítés szükséges.

A hívás `acquireTokenPopup` megnyit egy előugró ablakot (vagy `acquireTokenRedirect` átirányítja a felhasználókat a Microsoft Identity platformra). Ebben az ablakban a felhasználóknak kapcsolatba kell lépniük a hitelesítő adataik megerősítésével, a szükséges erőforrás jóváhagyásával, vagy a kétfaktoros hitelesítés végrehajtásával.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. A `loginPopup` (vagy `loginRedirect` ) első futtatása után `acquireTokenSilent` a metódus általában a védett erőforrásokhoz való hozzáféréshez használt jogkivonatok beszerzésére szolgál a további hívásokhoz. (A kérések és a megújítási hívások csendesen történnek.) `acquireTokenSilent` bizonyos esetekben sikertelen lehet. Előfordulhat például, hogy a felhasználó jelszava lejárt. Az alkalmazás két módon tudja kezelni ezt a kivételt:

1. A `acquireTokenPopup` felhasználó bejelentkezési kérésének elindításához hívjon fel azonnal egy hívást. Ezt a mintát gyakran használják online alkalmazásokban, ahol az alkalmazás nem hitelesített tartalmakat használ a felhasználó számára. Az irányított telepítő által generált minta ezt a mintát használja.
1. Vizuálisan jelezze a felhasználónak, hogy egy interaktív bejelentkezésre van szükség, hogy a felhasználó kiválassza a megfelelő időt a bejelentkezéshez, vagy az alkalmazás később is újra próbálkozik `acquireTokenSilent` . Ez a módszer általában akkor használható, ha a felhasználó az alkalmazás egyéb funkcióit nem zavarja. Előfordulhat például, hogy nem hitelesített tartalom érhető el az alkalmazásban. Ebben az esetben a felhasználó dönthet arról, hogy mikor szeretné bejelentkezni a védett erőforrás eléréséhez, vagy az elavult információk frissítéséhez.

> [!NOTE]
> Ez az oktatóanyag `loginPopup` `acquireTokenPopup` alapértelmezés szerint a és metódusokat használja. Ha az Internet Explorert használja, javasoljuk, hogy a `loginRedirect` és a `acquireTokenRedirect` metódust az Internet Explorer és az előugró ablakokkal kapcsolatos [ismert problémák](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) miatt használja. Ha például az átirányítási módszerek használatával kívánja elérni ugyanazt az eredményt, tekintse meg a [*authRedirect.js*](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/blob/quickstart/JavaScriptSPA/authRedirect.js) a githubon című témakört.

## <a name="call-the-microsoft-graph-api"></a>A Microsoft Graph API meghívása

Hozzon létre egy *graph.js* nevű fájlt az *app* mappában, és adja hozzá a következő kódot, amely Rest-hívásokat tesz elérhetővé a Microsoft Graph API számára:

```javascript
// Helper function to call Microsoft Graph API endpoint
// using authorization bearer token scheme
function callMSGraph(endpoint, token, callback) {
    const headers = new Headers();
    const bearer = `Bearer ${token}`;

    headers.append("Authorization", bearer);

    const options = {
        method: "GET",
        headers: headers
    };

    console.log('request made to Graph API at: ' + new Date().toString());

    fetch(endpoint, options)
        .then(response => response.json())
        .then(response => callback(response, endpoint))
        .catch(error => console.log(error));
}
```

Az oktatóanyagban létrehozott minta alkalmazásban a metódus használatával HTTP-kérelem hozható létre egy olyan `callMSGraph()` `GET` védett erőforráson, amely jogkivonatot igényel. A kérés ezután visszaadja a tartalmat a hívónak. Ez a metódus hozzáadja a beszerzett jogkivonatot a *http-engedélyezési fejlécben*. Az oktatóanyagban létrehozott minta alkalmazásban a védett erőforrás a Microsoft Graph API *Me* -végpontja, amely megjeleníti a bejelentkezett felhasználó profiljának adatait.

## <a name="test-your-application"></a>Az alkalmazás tesztelése

Elkészült az alkalmazás létrehozásával, és most már készen áll a Node.js webkiszolgáló elindítására és az alkalmazás működésének tesztelésére.

1. Indítsa el a Node.js webkiszolgálót a következő parancs futtatásával a projekt mappájának gyökeréből:

   ```console
   npm start
   ```
1. A böngészőben nyissa meg a `http://localhost:3000` vagy `http://localhost:<port>` a (z) lehetőséget, ahol az a `<port>` port, amelyet a webkiszolgáló figyel. Ekkor meg kell jelennie a *index.html* -fájl és a **Bejelentkezés** gomb tartalmának.

### <a name="sign-in-to-the-application"></a>Bejelentkezés az alkalmazásba

Miután a böngésző betölti a *index.html* fájlt, válassza a **Bejelentkezés** lehetőséget. A rendszer felszólítja, hogy jelentkezzen be a Microsoft Identity platformmal:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-01-signin-dialog.png" alt-text="Webböngésző – bejelentkezési párbeszédpanel megjelenítése":::

### <a name="provide-consent-for-application-access"></a>Adja meg az alkalmazás-hozzáférés beleegyezikét

Amikor először jelentkezik be az alkalmazásba, a rendszer felszólítja, hogy adjon hozzáférést a profilhoz, és jelentkezzen be:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-02-consent-dialog.png" alt-text="A böngészőben megjelenő tartalom párbeszédpanel":::

Ha beleegyezik a kért engedélyekkel, a webalkalmazások megjelenítik a felhasználónevet, és sikeres bejelentkezést jeleznek:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-03-signed-in.png" alt-text="A webböngésző sikeres bejelentkezésének eredményei":::

### <a name="call-the-graph-api"></a>A Graph API meghívása

A bejelentkezést követően válassza a **Profil megtekintése** lehetőséget a Microsoft Graph API-hívásra adott válaszban visszaadott felhasználói profil adatainak megtekintéséhez:

:::image type="content" source="media/tutorial-v2-javascript-auth-code/spa-04-see-profile.png" alt-text="A Microsoft Graph a böngészőben megjelenő profil adatai":::

### <a name="more-information-about-scopes-and-delegated-permissions"></a>További információ a hatókörökről és a delegált engedélyekről

A Microsoft Graph API-nak a felhasználónak *. Read* hatókörrel kell rendelkeznie a felhasználói profil olvasásához. Alapértelmezés szerint a rendszer automatikusan hozzáadja ezt a hatókört a Azure Portalban regisztrált összes alkalmazáshoz. A Microsoft Graph egyéb API-jai, valamint a háttér-kiszolgálóhoz tartozó egyéni API-k további hatóköröket is igényelhetnek. A Microsoft Graph API-nak például a *mail. Read* hatókört kell megadnia a felhasználó e-mail-címének listázásához.

Hatókörök hozzáadásakor a rendszer kérni fogja a felhasználókat, hogy adjon meg további hozzájárulásukat a hozzáadott hatókörökhöz.

Ha egy háttérrendszer API-nak nincs szüksége hatókörre, ami nem ajánlott, a a `clientId` hívásokban a jogkivonatok megszerzéséhez használható hatókörként használhatja.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

Ha szeretné mélyebben megismerni a JavaScript egyoldalas alkalmazás-fejlesztést a Microsoft Identity platformon, tekintse meg a több részből álló forgatókönyvek sorozatát:

> [!div class="nextstepaction"]
> [Forgatókönyv: egyoldalas alkalmazás](scenario-spa-overview.md)