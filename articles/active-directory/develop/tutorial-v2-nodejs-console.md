---
title: 'Oktatóanyag: Microsoft Graph hívása egy Node.js Console-alkalmazásban | Azure'
titleSuffix: Microsoft identity platform
description: Ebben az oktatóanyagban egy konzolos alkalmazást hoz létre, amely a Microsoft Grapht hívja meg egy Node.js Console-alkalmazáshoz.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 33d3712e25a06419e0ccc5914cdddfae7d85a371
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645789"
---
# <a name="tutorial-call-the-microsoft-graph-api-in-a-nodejs-console-app"></a>Oktatóanyag: a Microsoft Graph API meghívása egy Node.js Console-alkalmazásban

Ebben az oktatóanyagban olyan konzolszoftver-alkalmazást hoz létre, amely a saját identitásával hívja meg a Microsoft Graph API-t. Az Ön által létrehozott konzolablak a [Microsoft Authentication Library (MSAL) használatával Node.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Kövesse az oktatóanyag lépéseit:

> [!div class="checklist"]
> - Az alkalmazás regisztrálása a Azure Portalban
> - Node.js Console-alkalmazás projekt létrehozása
> - Hitelesítési logika hozzáadása az alkalmazáshoz
> - Alkalmazás-regisztráció részleteinek hozzáadása
> - Metódus hozzáadása webes API meghívásához
> - Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) vagy más Kódszerkesztő

## <a name="register-the-application"></a>Az alkalmazás regisztrálása

Először végezze el az alkalmazás regisztrálása [a Microsoft Identity platformon](quickstart-register-app.md) című témakör lépéseit az alkalmazás regisztrálásához.

Az alkalmazás regisztrálásához használja az alábbi beállításokat:

- Név: `NodeConsoleApp` (javasolt)
- Támogatott fióktípus: **csak a szervezeti könyvtárban lévő fiókok**
- API-engedélyek: **Microsoft API**-k  >  **Microsoft Graph**  >  **alkalmazás engedélyei** > `User.Read.All`
- Ügyfél titka: `*********` (ezt az értéket egy későbbi lépésben való használatra rögzíti – csak egyszer jelenik meg)

## <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy mappát az alkalmazás üzemeltetéséhez, például *NodeConsoleApp*.

1. Először váltson át a projekt könyvtárába a terminálon, majd futtassa a következő NPM parancsokat:

```console
    npm init -y
    npm install --save dotenv yargs axios @azure/msal-node
```

2. Ezután hozzon létre egy *bin* nevű mappát. Ezután a mappában belül hozzon létre egy *index.js* nevű fájlt, és adja hozzá a következő kódot:

```JavaScript
#!/usr/bin/env node

// read in env settings
require('dotenv').config();

const yargs = require('yargs');

const fetch = require('./fetch');
const auth = require('./auth');

const options = yargs
    .usage('Usage: --op <operation_name>')
    .option('op', { alias: 'operation', describe: 'operation name', type: 'string', demandOption: true })
    .argv;

async function main() {
    console.log(`You have selected: ${options.op}`);

    switch (yargs.argv['op']) {
        case 'getUsers':

            try {
                // here we get an access token
                const authResponse = await auth.getToken(auth.tokenRequest);

                // call the web API with the access token
                const users = await fetch.callApi(auth.apiConfig.uri, authResponse.accessToken);

                // display result
                console.log(users);
            } catch (error) {
                console.log(error);
            }

            break;
        default:
            console.log('Select a Graph operation first');
            break;
    }
};

main();
```

Ez a fájl két másik csomópont-modulra hivatkozik: *auth.js* , amely a MSAL-csomópont implementációját tartalmazza a hozzáférési tokenek beszerzéséhez, és *fetch.js* , amely egy olyan metódust tartalmaz, amely egy hozzáférési jogkivonattal rendelkező http-KÉRÉSt biztosít Microsoft Graph API-nak. Az oktatóanyag további részének befejezése után a projekt fájl-és mappa-struktúrájának a következőhöz hasonlóan kell kinéznie:

```
NodeConsoleApp/
├── bin
│   ├── auth.js
│   ├── fetch.js
│   ├── index.js
├── package.json
└── .env
```

## <a name="add-authentication-logic"></a>Hitelesítési logika hozzáadása

A *bin* mappában hozzon létre egy *auth.js* nevű fájlt, és adja hozzá a következő kódot egy hozzáférési jogkivonat beszerzéséhez, amely a Microsoft Graph API meghívásakor jelenik meg.

```JavaScript
const msal = require('@azure/msal-node');

/**
 * Configuration object to be passed to MSAL instance on creation.
 * For a full list of MSAL Node configuration parameters, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md
 */
const msalConfig = {
    auth: {
        clientId: process.env.CLIENT_ID,
        authority: process.env.AAD_ENDPOINT + process.env.TENANT_ID,
        clientSecret: process.env.CLIENT_SECRET,
    }
};

/**
 * With client credentials flows permissions need to be granted in the portal by a tenant administrator.
 * The scope is always in the format '<resource>/.default'. For more, visit:
 * https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow
 */
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'],
};

const apiConfig = {
    uri: process.env.GRAPH_ENDPOINT + 'v1.0/users',
};

/**
 * Initialize a confidential client application. For more info, visit:
 * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md
 */
const cca = new msal.ConfidentialClientApplication(msalConfig);

/**
 * Acquires token with client credentials.
 * @param {object} tokenRequest
 */
async function getToken(tokenRequest) {
    return await cca.acquireTokenByClientCredential(tokenRequest);
}

module.exports = {
    apiConfig: apiConfig,
    tokenRequest: tokenRequest,
    getToken: getToken
};
```

A fenti kódrészletben először létre kell hozni egy konfigurációs objektumot (*msalConfig*), és át kell adnia egy MSAL- [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)inicializálásához. Ezután létrehozunk egy módszert a jogkivonatok ügyfél- **hitelesítő adatokkal** való beszerzéséhez, és végül elérhetővé tenni ezt a modult *main.js*. A modul konfigurációs paraméterei egy környezeti fájlból származnak, amelyet a következő lépésben fogunk létrehozni.

## <a name="add-app-registration-details"></a>Alkalmazás-regisztráció részleteinek hozzáadása

Hozzon létre egy környezeti fájlt, amely a jogkivonatok beszerzéséhez használt alkalmazás-regisztrációs adatokat tárolja. Ehhez hozzon létre egy *. env* nevű fájlt a minta gyökérkönyvtárában (*NodeConsoleApp*), és adja hozzá a következő kódot:

```
# Credentials
TENANT_ID=Enter_the_Tenant_Id_Here
CLIENT_ID=Enter_the_Application_Id_Here
CLIENT_SECRET=Enter_the_Client_Secret_Here

# Endpoints
AAD_ENDPOINT=Enter_the_Cloud_Instance_Id_Here
GRAPH_ENDPOINT=Enter_the_Graph_Endpoint_Here
```

Adja meg ezeket az adatokat az Azure-alkalmazás regisztrációs portálján beszerzett értékekkel:

- `Enter_the_Tenant_Id_here` a következők egyikének kell lennie:
  - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére**. Például: `contoso.microsoft.com`.
  - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `organizations` .
  - Ha az alkalmazás a *szervezeti címtárban és a személyes Microsoft-fiókokban is támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `common` .
  - A *személyes Microsoft-fiókok* támogatásának korlátozásához cserélje le ezt az értéket a következőre: `consumers` .
- `Enter_the_Application_Id_Here`: A regisztrált alkalmazás **alkalmazás-(ügyfél-) azonosítója** .
- `Enter_the_Cloud_Instance_Id_Here`: Az Azure Cloud-példány, amelyben az alkalmazás regisztrálva van.
  - A fő (vagy *globális*) Azure-felhőhöz írja be a következőt: `https://login.microsoftonline.com` .
  - Az **országos** felhők esetében (például Kína) a megfelelő értékeket találja a [nemzeti felhőkben](authentication-national-cloud.md).
- `Enter_the_Graph_Endpoint_Here` a Microsoft Graph API azon példánya, amellyel az alkalmazásnak kommunikálnia kell.
  - A **globális** Microsoft Graph API-végpont esetében cserélje le a sztring mindkét példányát a következőre: `https://graph.microsoft.com` .
  - Az **országos** Felhőbeli üzemelő példányok esetében a Microsoft Graph dokumentációjában tekintse meg a [nemzeti Felhőbeli központi telepítések](/graph/deployments) című témakört.

## <a name="add-a-method-to-call-a-web-api"></a>Metódus hozzáadása webes API meghívásához

A *bin* mappában hozzon létre egy *fetch.js* nevű fájlt, és adja hozzá a következő kódot a REST-hívások Microsoft Graph API-hoz való átállításához:

```javascript
const axios = require('axios');

/**
 * Calls the endpoint with authorization bearer token.
 * @param {string} endpoint
 * @param {string} accessToken
 */
async function callApi(endpoint, accessToken) {

    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('request made to web API at: ' + new Date().toString());

    try {
        const response = await axios.default.get(endpoint, options);
        return response.data;
    } catch (error) {
        console.log(error)
        return error;
    }
};

module.exports = {
    callApi: callApi
};
```

Itt a `callApi` metódus használatával HTTP-kérést lehet végezni `GET` egy olyan védett erőforráson, amely hozzáférési jogkivonatot igényel. A kérés ezután visszaadja a tartalmat a hívónak. Ez a metódus hozzáadja a beszerzett jogkivonatot a *http-engedélyezési fejlécben*. A védett erőforrás itt a Microsoft Graph API [Users végpont](/graph/api/user-list) , amely a bérlő azon felhasználóit jeleníti meg, ahol az alkalmazás regisztrálva van.

## <a name="test-the-app"></a>Az alkalmazás tesztelése

Elkészült az alkalmazás létrehozásával, és most már készen áll az alkalmazás működésének tesztelésére.

Indítsa el a Node.js Console alkalmazást a következő parancs futtatásával a projekt mappájának gyökeréből:

```console
node . --op getUsers
```

Ennek a Microsoft Graph API-ból származó JSON-válaszokat kell eredményeznie, és a konzolon a felhasználói objektumok tömbjét kell megjelennie:

```console
You have selected: getUsers
request made to web API at: Fri Jan 22 2021 09:31:52 GMT-0800 (Pacific Standard Time)
{
    '@odata.context': 'https://graph.microsoft.com/v1.0/$metadata#users',
    value: [
        {
            displayName: 'Adele Vance'
            givenName: 'Adele',
            jobTitle: 'Retail Manager',
            mail: 'AdeleV@msaltestingjs.onmicrosoft.com',
            mobilePhone: null,
            officeLocation: '18/2111',
            preferredLanguage: 'en-US',
            surname: 'Vance',
            userPrincipalName: 'AdeleV@msaltestingjs.onmicrosoft.com',
            id: 'a6a218a5-f5ae-462a-acd3-581af4bcca00'
        }
    ]
}
```
:::image type="content" source="media/tutorial-v2-nodejs-console/screenshot.png" alt-text="Gráf-választ megjelenítő parancssori felület":::

## <a name="how-the-application-works"></a>Az alkalmazás működése

Ez az alkalmazás a [OAuth 2,0 ügyfél-hitelesítő adatok megadását](./v2-oauth2-client-creds-grant-flow.md)használja. Ez az engedélytípus általában olyan kiszolgálóközi interakciók esetén használatos, amelyeknek a felhasználóval való azonnali interakció nélkül, a háttérben kell futniuk. A hitelesítő adatok engedélyezési folyamata lehetővé teszi egy webszolgáltatás (bizalmas ügyfél) számára a saját hitelesítő adatainak használatát a felhasználó megszemélyesítése helyett a hitelesítéshez egy másik webszolgáltatás hívásakor. A hitelesítési modellel támogatott alkalmazások típusa általában **démonok** vagy **szolgáltatásfiókok**.

Az ügyfél-hitelesítő adatokra vonatkozó kérelem hatóköre az erőforrás neve, majd a `/.default` . Ez a jelölés azt mutatja Azure Active Directory (Azure AD) számára, hogy az alkalmazás regisztrálása során statikusan deklarált alkalmazási szintű engedélyeket használjon. Emellett a **bérlői rendszergazdának** is meg kell adni ezeket az API-engedélyeket.

## <a name="next-steps"></a>Következő lépések

Ha a Microsoft Identity platformon szeretné mélyebben bemutatni Node.js-konzolon futó alkalmazások fejlesztését, tekintse meg a több részből álló forgatókönyvek sorozatát:

> [!div class="nextstepaction"]
> [Forgatókönyv: Daemon-alkalmazás](scenario-daemon-overview.md)