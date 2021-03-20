---
title: 'Oktatóanyag: bejelentkezési felhasználók Node.js & Express Web App-ben | Azure'
titleSuffix: Microsoft identity platform
description: Ebben az oktatóanyagban egy webalkalmazás bejelentkezési felhasználóinak támogatását adja hozzá.
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 3f1f26acbba0f5830421e760d6a68a11f618fa85
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648990"
---
# <a name="tutorial-sign-in-users-in-a-nodejs--express-web-app"></a>Oktatóanyag: bejelentkezési felhasználók Node.js & Express-webalkalmazásban

Ebben az oktatóanyagban egy webalkalmazást hoz létre, amely bejelentkezik a felhasználók számára. A létrehozott webalkalmazás a [Microsoft hitelesítési függvénytárát (MSAL) használja a csomóponthoz](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node).

Kövesse az oktatóanyag lépéseit:

> [!div class="checklist"]
> - Az alkalmazás regisztrálása a Azure Portalban
> - Express Web App-projekt létrehozása
> - A hitelesítési függvénytárak csomagjainak telepítése
> - Alkalmazás-regisztráció részleteinek hozzáadása
> - Kód hozzáadása a felhasználói bejelentkezéshez
> - Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek

- [Node.js](https://nodejs.org/en/download/)
- [Visual Studio Code](https://code.visualstudio.com/download) vagy más Kódszerkesztő

## <a name="register-the-application"></a>Az alkalmazás regisztrálása

Először végezze el az alkalmazás regisztrálása [a Microsoft Identity platformon](quickstart-register-app.md) című témakör lépéseit az alkalmazás regisztrálásához.

Az alkalmazás regisztrálásához használja az alábbi beállításokat:

- Név: `ExpressWebApp` (javasolt)
- Támogatott fióktípus: **bármely szervezeti címtárban (bármely Azure ad-címtár – több-bérlős) és személyes Microsoft-fiókokban (például Skype, Xbox) lévő fiókok**
- Platform típusa: **web**
- Átirányítási URI: `http://localhost:3000/redirect`
- Ügyfél titka: `*********` (ezt az értéket egy későbbi lépésben való használatra rögzíti – csak egyszer jelenik meg)

## <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy mappát az alkalmazás üzemeltetéséhez, például *ExpressWebApp*.

1. Először váltson át a projekt könyvtárába a terminálon, majd futtassa a következő `npm` parancsokat:

```console
    npm init -y
    npm install --save express
```

2. Ezután hozzon létre egy *index.js* nevű fájlt, és adja hozzá a következő kódot:

```JavaScript
    const express = require("express");
    const msal = require('@azure/msal-node');

    const SERVER_PORT = process.env.PORT || 3000;

    // Create Express App and Routes
    const app = express();

    app.listen(SERVER_PORT, () => console.log(`Msal Node Auth Code Sample app listening on port ${SERVER_PORT}!`))
```

Most már rendelkezik egy egyszerű webkiszolgálóval, amely a 3000-es porton fut. A projekt fájl-és mappa-struktúrájának a következőhöz hasonlóan kell kinéznie:

```
ExpressWebApp/
├── index.js
└── package.json
```

## <a name="install-the-auth-library"></a>Az Auth Library telepítése

Keresse meg a projekt gyökérkönyvtárának gyökerét egy terminálon, és telepítse a MSAL csomópont-csomagot a NPM használatával.

```console
    npm install --save @azure/msal-node
```

## <a name="add-app-registration-details"></a>Alkalmazás-regisztráció részleteinek hozzáadása

A korábban létrehozott *index.js* fájlban adja hozzá a következő kódot:

```JavaScript
    // Before running the sample, you will need to replace the values in the config,
    // including the clientSecret
    const config = {
        auth: {
            clientId: "Enter_the_Application_Id",
            authority: "Enter_the_Cloud_Instance_Id_Here/Enter_the_Tenant_Id_here",
            clientSecret: "Enter_the_Client_secret"
        },
        system: {
            loggerOptions: {
                loggerCallback(loglevel, message, containsPii) {
                    console.log(message);
                },
                piiLoggingEnabled: false,
                logLevel: msal.LogLevel.Verbose,
            }
        }
    };
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
- `Enter_the_Client_secret`: Cserélje le ezt az értéket a korábban létrehozott ügyfél-titkos kulcsra. Új kulcs létrehozásához használja a **tanúsítványok & Secrets** lehetőséget az Azure Portal alkalmazás regisztrációs beállításaiban.

> [!WARNING]
> A forráskódban található bármilyen szöveges titok nagyobb biztonsági kockázatot jelent. Ez a cikk egyszerű szöveges ügyfél-titkos kulcsot használ az egyszerűség kedvéért. [Hitelesítő adatok](active-directory-certificate-credentials.md) használata a bizalmas ügyfélalkalmazások ügyfeleinek titkos kulcsa helyett, különösen azokat az alkalmazásokat, amelyeket az éles környezetben kíván üzembe helyezni.

## <a name="add-code-for-user-login"></a>Kód hozzáadása a felhasználói bejelentkezéshez

A korábban létrehozott *index.js* fájlban adja hozzá a következő kódot:

```JavaScript
    // Create msal application object
    const cca = new msal.ConfidentialClientApplication(config);

    app.get('/', (req, res) => {
        const authCodeUrlParameters = {
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        // get url to sign user in and consent to scopes needed for application
        cca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            res.redirect(response);
        }).catch((error) => console.log(JSON.stringify(error)));
    });

    app.get('/redirect', (req, res) => {
        const tokenRequest = {
            code: req.query.code,
            scopes: ["user.read"],
            redirectUri: "http://localhost:3000/redirect",
        };

        cca.acquireTokenByCode(tokenRequest).then((response) => {
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => {
            console.log(error);
            res.status(500).send(error);
        });
    });
```

## <a name="test-sign-in"></a>Bejelentkezés tesztelése

Elkészült az alkalmazás létrehozásával, és most már készen áll az alkalmazás működésének tesztelésére.

1. Indítsa el a Node.js Console alkalmazást a következő parancs futtatásával a projekt mappájának gyökeréből:

```console
   node index.js
```

2. Nyisson meg egy böngészőablakot, és navigáljon a `http://localhost:3000` címre. Ekkor megjelenik a bejelentkezési képernyő:

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/sign-in-screen.png" alt-text="Az Azure AD bejelentkezési képernyőjének megjelenítése":::

3. A hitelesítő adatok megadása után meg kell jelennie egy beleegyezési képernyőnek, amely arra kéri, hogy hagyja jóvá az alkalmazás engedélyeit.

:::image type="content" source="media/tutorial-v2-nodejs-webapp-msal/consent-screen.png" alt-text="Az Azure AD-beli beleegyezési képernyő megjelenítése":::

## <a name="how-the-application-works"></a>Az alkalmazás működése

Ebben az oktatóanyagban egy MSAL Node [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md) objektumot inicializált egy olyan konfigurációs objektum (*msalConfig*) megadásával, amely az Azure ad-alkalmazás regisztrálásakor kapott paramétereket tartalmazza Azure Portal. A létrehozott webalkalmazás a [OAuth 2,0 engedélyezési kódot](./v2-oauth2-auth-code-flow.md) használja a bejelentkezési felhasználók számára, és beolvassa az azonosító és a hozzáférési tokeneket.

## <a name="next-steps"></a>Következő lépések

Ha szeretné mélyebben bemutatni Node.js & Express webalkalmazások fejlesztését a Microsoft Identity platformon, tekintse meg a több részből álló forgatókönyvek sorozatát:

> [!div class="nextstepaction"]
> [Forgatókönyv: a felhasználók által bejelentkezett webalkalmazás](scenario-web-app-sign-user-overview.md)