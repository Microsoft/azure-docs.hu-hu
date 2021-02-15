---
title: MSAL.js ügyfélalkalmazások inicializálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerkedjen meg az ügyfélalkalmazások inicializálásával a JavaScripthez készült Microsoft Authentication Library (MSAL.js) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/17/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: a6d7b760ffd1931fa5dcdb3a67dd02f2798957a9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365837"
---
# <a name="initialize-client-applications-using-msaljs"></a>Ügyfélalkalmazások inicializálása MSAL.js használatával

Ez a cikk a Microsoft hitelesítési függvénytárának a JavaScripthez (MSAL.js) való inicializálását ismerteti egy felhasználói ügynök alkalmazás egy példányával.

A User-Agent alkalmazás a nyilvános ügyfélalkalmazás olyan formája, amelyben az ügyfél kódja egy felhasználói ügynökön, például egy böngészőben fut. Az ilyen ügyfelek nem tárolják a titkos kulcsokat, mert a böngésző környezete nyíltan elérhető.

További információ az ügyfélalkalmazások típusairól és az alkalmazás konfigurációs lehetőségeiről: [nyilvános és bizalmas ügyfélalkalmazások a MSAL-ben](msal-client-applications.md).

## <a name="prerequisites"></a>Előfeltételek

Az alkalmazás inicializálása előtt először [regisztrálnia kell a Azure Portalba](scenario-spa-app-registration.md), és létre kell hoznia egy megbízhatósági kapcsolatot az alkalmazás és a Microsoft Identity platform között.

Az alkalmazás regisztrálását követően az alábbi értékek közül egy vagy többre lesz szüksége, amelyek a Azure Portal találhatók.

| Érték | Kötelező | Leírás |
|:----- | :------: | :---------- |
| Alkalmazás (ügyfél) azonosítója | Kötelező | Egy GUID, amely egyedileg azonosítja az alkalmazást a Microsoft Identity platformon belül. |
| Authority | Választható | Az identitás-szolgáltató URL-címe (a *példány*) és a *bejelentkezési célközönség* az alkalmazáshoz. A példány és a bejelentkezési célközönség összefűzéskor a *szolgáltatót* hozza létre. |
| Címtár (bérlő) azonosítója | Választható | Akkor adja meg ezt a lehetőséget, ha csak a szervezete számára készít üzletági alkalmazást, amelyet gyakran *egyetlen bérlős alkalmazásnak* nevezünk. |
| Átirányítási URI | Választható | Ha webalkalmazást hoz létre, akkor a `redirectUri` meghatározza, hogy a rendszer hol adja vissza az identitás-szolgáltatót (a Microsoft Identity platform) a kiállított biztonsági jogkivonatokat. |

## <a name="initialize-msaljs-2x-apps"></a>MSAL.js 2. x alkalmazások inicializálása

Inicializálja a MSAL hitelesítési környezetét egy [PublicClientApplication][msal-js-publicclientapplication] [konfigurációs][msal-js-configuration] objektummal való létrehozásával. A minimálisan szükséges konfigurációs tulajdonság a (z) alkalmazásban az alkalmazás `clientID` regisztrálásának **Áttekintés** lapján **(ügyfél-azonosító)** látható az Azure Portalban.

Íme egy példa a konfigurációs objektumra és a következő példányára `PublicClientApplication` :

```javascript
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111",
        authority: "https://login.microsoftonline.com/common",
        knownAuthorities: [],
        redirectUri: "https://localhost:3001",
        postLogoutRedirectUri: "https://localhost:3001/logout",
        navigateToLoginRequestUrl: true
    },
    cache: {
        cacheLocation: "sessionStorage",
        storeAuthStateInCookie: false
    },
    system: {
        loggerOptions: {
            loggerCallback: (level: LogLevel, message: string, containsPii: boolean): void => {
                if (containsPii) {
                    return;
                }
                switch (level) {
                    case LogLevel.Error:
                        console.error(message);
                        return;
                    case LogLevel.Info:
                        console.info(message);
                        return;
                    case LogLevel.Verbose:
                        console.debug(message);
                        return;
                    case LogLevel.Warning:
                        console.warn(message);
                        return;
                }
            },
            piiLoggingEnabled: false
        },
        windowHashTimeout: 60000,
        iframeHashTimeout: 6000,
        loadFrameTimeout: 0
    }
};

// Create an instance of PublicClientApplication
const msalInstance = new PublicClientApplication(msalConfig);

// Handle the redirect flows
msalInstance.handleRedirectPromise().then((tokenResponse) => {
    // Handle redirect response
}).catch((error) => {
    // Handle redirect error
});
```

### `handleRedirectPromise`

[HandleRedirectPromise][msal-js-handleredirectpromise] meghívása, ha az alkalmazás az átirányítási folyamatokat használja. Az átirányítási folyamatok használatakor `handleRedirectPromise` minden egyes lapon be kell futnia.

Az ígéretből három lehetséges eredmény érhető el:

- `.then` meghívása és `tokenResponse` igaz: az alkalmazás egy sikeres átirányítási műveletből tér vissza.
- `.then` meghívása és `tokenResponse` falsy ( `null` ): az alkalmazás nem ad vissza átirányítási műveletből.
- `.catch` meghívása: az alkalmazás egy átirányítási műveletből tér vissza, és hiba történt.

## <a name="initialize-msaljs-1x-apps"></a>MSAL.js 1. x alkalmazás inicializálása

Inicializálja a MSAL 1. x hitelesítési környezetét egy [UserAgentApplication][msal-js-useragentapplication] konfigurációs objektummal való létrehozásával. A minimálisan szükséges konfigurációs tulajdonság a (z) alkalmazásban az alkalmazás `clientID` regisztrálásának **Áttekintés** lapján **(ügyfél-azonosító)** látható az Azure Portalban.

Az átirányítási folyamatokkal ([loginRedirect][msal-js-loginredirect] és [acquireTokenRedirect][msal-js-acquiretokenredirect]) rendelkező hitelesítési módszerek esetében MSAL.js 1.2. x vagy korábbi verziókban explicit módon regisztrálnia kell egy visszahívást a sikerhez vagy a hibához a `handleRedirectCallback()` metóduson keresztül. A visszahívás explicit módon történő regisztrálása a MSAL.js 1.2. x és korábbi verziókban szükséges, mert az átirányítási folyamatok nem adnak vissza olyan ígéreteket, mint a metódusok előugró felülete. A visszahívás regisztrálása *opcionális* az MSAL.js 1.3. x vagy újabb verziójában.

```javascript
// Configuration object constructed
const msalConfig = {
    auth: {
        clientId: "11111111-1111-1111-111111111111"
    }
}

// Create UserAgentApplication instance
const msalInstance = new UserAgentApplication(msalConfig);

function authCallback(error, response) {
    // Handle redirect response
}

// Register a redirect callback for Success or Error (when using redirect methods)
// **REQUIRED** in MSAL.js 1.2.x and earlier
// **OPTIONAL** in MSAL.js 1.3.x and later
msalInstance.handleRedirectCallback(authCallback);
```

## <a name="single-instance-and-configuration"></a>Egyetlen példány és konfiguráció

A MSAL.js 1. x és 2. x egyaránt úgy lett kialakítva, hogy egyetlen példánnyal és konfigurációval rendelkezzenek a vagy a, illetve az `UserAgentApplication` `PublicClientApplication` egyetlen hitelesítési környezetnek.

A vagy a több példánya `UserAgentApplication` `PublicClientApplication` nem ajánlott, mert ütköző gyorsítótár-bejegyzéseket és viselkedést okoznak a böngészőben.

## <a name="next-steps"></a>Következő lépések

Ez a MSAL.js 2. x code minta a GitHubon azt mutatja be, hogyan hozható létre egy [PublicClientApplication][msal-js-publicclientapplication] egy [konfigurációs][msal-js-configuration] objektummal:

[Azure-Samples/MS-Identity-JavaScript-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2)

<!-- LINKS - External -->
[msal-browser]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/
[msal-core]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/
[msal-js-acquiretokenredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#acquiretokenredirect
[msal-js-configuration]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/modules/_azure_msal.html#configuration
[msal-js-handleredirectpromise]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html#handleredirectpromise
[msal-js-loginredirect]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html#loginredirect
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal_browser.publicclientapplication.html
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/classes/_azure_msal.useragentapplication.html
