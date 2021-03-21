---
title: Hibák és kivételek kezelése az MSAL.js-ben
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan kezelheti a hibákat és kivételeket, a feltételes hozzáférési jogcímeket, és próbálkozzon újra MSAL.js alkalmazásokban.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, hahamil
ms.custom: aaddev
ms.openlocfilehash: 20d276aba2ee3260911748cbee0a16020270059a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761336"
---
# <a name="handle-errors-and-exceptions-in-msaljs"></a>Hibák és kivételek kezelése az MSAL.js-ben

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msaljs"></a>Hibakezelés a MSAL.jsban

A MSAL.js olyan hibaüzeneteket biztosít, amelyek absztrakt és osztályozzák a gyakori hibák különböző típusait. Emellett egy felületet is biztosít a hibák konkrét részleteinek eléréséhez, például a hibaüzeneteket a megfelelő kezelés érdekében.

### <a name="error-object"></a>Hiba objektum

```javascript
export class AuthError extends Error {
    // This is a short code describing the error
    errorCode: string;
    // This is a descriptive string of the error,
    // and may also contain the mitigation strategy
    errorMessage: string;
    // Name of the error class
    this.name = "AuthError";
}
```

A hiba osztály kibővítésével a következő tulajdonságokat érheti el:
- `AuthError.message`: Ugyanaz, mint a `errorMessage` .
- `AuthError.stack`: Az eldobott hibák verem-nyomkövetése.

### <a name="error-types"></a>Hibák típusai

A következő típusú hibák érhetők el:

- `AuthError`: Az MSAL.js könyvtár alapszintű hibájának osztálya, amely váratlan hibákhoz is használatos.

- `ClientAuthError`: Error osztály, amely az ügyfél-hitelesítéssel kapcsolatos problémát jelöli. A könyvtárból érkező hibák többsége ClientAuthErrors lesz. Ezek a hibák olyan dolgokból származnak, mint például a bejelentkezési módszer meghívása, ha a bejelentkezés már folyamatban van, a felhasználó megszakítja a bejelentkezést, és így tovább.

- `ClientConfigurationError`: Hiba osztály, `ClientAuthError` a rendszer a kérelmek eldobása előtt kiterjeszti a megjelenő paramétereket, amikor a megadott felhasználói konfigurációs paraméterek helytelenek vagy hiányoznak.

- `ServerError`: A Error osztály a hitelesítési kiszolgáló által eljuttatott hibaüzeneteket jelöli. Ezek lehetnek olyan hibák, mint például az érvénytelen kérelmek formátuma vagy paraméterei, vagy bármilyen más hiba, amely megakadályozza, hogy a kiszolgáló hitelesítse vagy engedélyezze a felhasználót.

- `InteractionRequiredAuthError`: A hiba osztály az `ServerError` interaktív hívást igénylő kiszolgálói hibákra terjed ki. Ez a hiba akkor fordul elő, `acquireTokenSilent` Ha a felhasználónak a kiszolgálóval való interakcióra van szüksége a hitelesítő adatok vagy a hitelesítés/engedélyezés jóváhagyása érdekében. A hibakódok a következők:, `"interaction_required"` `"login_required"` és `"consent_required"` .

A hitelesítési folyamatokban a (z) átirányítási módszerekkel ( `loginRedirect` ,) való hibakezelés `acquireTokenRedirect` érdekében regisztrálnia kell a visszahívást, amely sikeres vagy sikertelen volt, miután az átirányítás a `handleRedirectCallback()` következő módszer használatával történik:

```javascript
function authCallback(error, response) {
    //handle redirect response
}

var myMSALObj = new Msal.UserAgentApplication(msalConfig);

// Register Callbacks for redirect flow
myMSALObj.handleRedirectCallback(authCallback);
myMSALObj.acquireTokenRedirect(request);
```

Az előugró élmény ( `loginPopup` ,) metódusok `acquireTokenPopup` visszaküldési lehetőséget biztosítanak, így a megígért minta (... és. Catch) segítségével a következőképpen kezelheti őket:

```javascript
myMSALObj.acquireTokenPopup(request).then(
    function (response) {
        // success response
    }).catch(function (error) {
        console.log(error);
    });
```

### <a name="errors-that-require-interaction"></a>Interakciót igénylő hibák

A rendszer hibát ad vissza, ha nem interaktív módszert használ a tokenek beszerzéséhez `acquireTokenSilent` , például MSAL, de nem tudta csendesen megtenni.

A lehetséges okok a következők:

- be kell jelentkeznie
- meg kell egyeznie
- egy többtényezős hitelesítési felülettel kell eljárnia.

A szervizelés olyan interaktív módszer meghívása, mint a `acquireTokenPopup` vagy `acquireTokenRedirect` :

```javascript
// Request for Access Token
myMSALObj.acquireTokenSilent(request).then(function (response) {
    // call API
}).catch( function (error) {
    // call acquireTokenPopup in case of acquireTokenSilent failure
    // due to consent or interaction required
    if (error.errorCode === "consent_required"
    || error.errorCode === "interaction_required"
    || error.errorCode === "login_required") {
        myMSALObj.acquireTokenPopup(request).then(
            function (response) {
                // call API
            }).catch(function (error) {
                console.log(error);
            });
    }
});
```

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

Ha a tokeneket csendes (a használatával `acquireTokenSilent` ) MSAL.js használatával kéri le, az alkalmazás hibákat kaphat, ha egy olyan API-nak szüksége van egy [feltételes hozzáférési jogcímek](../azuread-dev/conditional-access-dev-guide.md) megoldására, mint például az MFA-szabályzat.

A hiba kezelésére szolgáló minta egy interaktív hívást tesz lehetővé a token beszerzéséhez MSAL.js például `acquireTokenPopup` `acquireTokenRedirect` a következő példában vagy a-ben:

```javascript
myMSALObj.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // call API
}).catch(function(error) {
    if (error instanceof InteractionRequiredAuthError) {
    
        // extract, if exists, claims from error message
        if (error.ErrorMessage.claims) {
            accessTokenRequest.claimsRequest = JSON.stringify(error.ErrorMessage.claims);
        }
        
        // call acquireTokenPopup in case of InteractionRequiredAuthError failure
        myMSALObj.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // call API
        }).catch(function(error) {
            console.log(error);
        });
    }
});
```

A jogkivonat interaktív beszerzése kéri a felhasználót, és lehetőséget biztosít számukra a kötelező feltételes hozzáférési szabályzat kielégítésére.

Ha feltételes hozzáférést igénylő API-t hív meg, az API-val kapcsolatos hiba esetén a jogcímek kérdését is megkaphatja. Ebben az esetben átadhatja a hibában visszaadott jogcímeket az `claimsRequest` osztály mezőjébe, `AuthenticationParameters.ts` hogy az megfeleljen a megfelelő házirendnek. 

További részletekért lásd: [további jogcímek igénylése](active-directory-optional-claims.md) .


[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Következő lépések

A problémák diagnosztizálásához és hibakereséséhez érdemes megfontolni a [MSAL.jsnaplózásának ](msal-logging-js.md) engedélyezését.
