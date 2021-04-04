---
title: MSAL.js használata a Azure AD B2C
titleSuffix: Microsoft identity platform
description: A JavaScripthez készült Microsoft Authentication Library (MSAL.js) lehetővé teszi, hogy az alkalmazások működjenek a Azure AD B2C és a jogkivonatokat szerezzenek be a biztonságos webes API-k meghívásához. Ezek a webes API-k lehetnek Microsoft Graph, más Microsoft API-k, a másoktól származó webes API-k vagy a saját webes API-k.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 53294a30b38bed7ab7516443277cac24e4fef4c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063722"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Használja a Microsoft hitelesítési függvénytárát a JavaScripthez, hogy működjön együtt Azure AD B2C

A [javascripthez készült Microsoft Authentication Library (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) lehetővé teszi a JavaScript-fejlesztők számára, hogy [Azure Active Directory B2C](../../active-directory-b2c/overview.md) (Azure ad B2C) használatával hitelesítsék a felhasználókat a közösségi és helyi identitásokkal.

A Azure AD B2C identitás-kezelési szolgáltatásként való használatával testreszabhatja és szabályozhatja, hogy az ügyfelek hogyan regisztrálhatnak, jelentkezhetnek be és kezelhetik a profiljaikat az alkalmazások használatakor. A Azure AD B2C lehetővé teszi az alkalmazás által a hitelesítési folyamat során megjelenített felhasználói felület kiírását és testreszabását is.

Az alábbi szakaszban a következőket mutatjuk be:

- Node.js webes API-k elleni védelem
- Egy egyoldalas alkalmazásban (SPA) való bejelentkezés támogatása és *a védett webes* API meghívása
- Jelszó-visszaállítási támogatás engedélyezése

## <a name="prerequisites"></a>Előfeltételek

Ha még nem tette meg, hozzon létre egy [Azure ad B2C bérlőt](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="nodejs-web-api"></a>NODE.js webes API

A következő lépések bemutatják, hogyan használhatják a **webes API** a Azure ad B2Ct saját maga elleni védelemre, és hogy a kijelölt hatókörök elérhetők legyenek egy ügyfélalkalmazás számára.

A csomópont MSAL.js jelenleg fejlesztés alatt áll. További információkért tekintse meg a GitHubon található [ütemtervet](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) . Jelenleg a [Passport-Azure-ad](https://github.com/AzureAD/passport-azure-ad)-t, a Microsoft által fejlesztett és támogatott Node.js hitelesítési kódtárat ajánljuk.

### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása

A webes API-k Azure AD B2C-mel való ellátásához először regisztrálnia kell. Lásd: [az alkalmazás regisztrálása](../../active-directory-b2c/add-web-api-application.md) a részletes lépésekhez.

### <a name="step-2-download-the-sample-application"></a>2. lépés: a minta alkalmazás letöltése

Töltse le a mintát zip-fájlként, vagy klónozást a GitHubról:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>3. lépés: a hitelesítés konfigurálása

1. Nyissa meg a *config.js* fájlt a mintában.

2. Konfigurálja a mintát az alkalmazás regisztrálásakor korábban beszerzett hitelesítő adatokkal. Módosítsa a kód következő sorait úgy, hogy lecseréli az értékeket a bérlő nevére, az ügyfél-AZONOSÍTÓra és a házirend nevére.

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

További információkért tekintse meg ezt a [Node.js B2C webes API-mintát](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

## <a name="javascript-spa"></a>JavaScript SPA

A következő lépések bemutatják, hogyan használható egy **egyoldalas alkalmazás** a Azure ad B2C a regisztrációhoz, a bejelentkezéshez és a védett webes API meghívásához.

### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása

A hitelesítés megvalósításához először regisztrálnia kell az alkalmazást. Lásd: [az alkalmazás regisztrálása](../../active-directory-b2c/tutorial-register-applications.md) a részletes lépésekhez.

### <a name="step-2-download-the-sample-application"></a>2. lépés: a minta alkalmazás letöltése

Töltse le a kód mintáját [. ZIP-archívum](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) vagy a GitHub-tárház klónozása:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>3. lépés: a hitelesítés konfigurálása

Az alkalmazás konfigurálása két fontos szempontot mutat be:

- API-végpont és elérhető hatókörök konfigurálása
- Hitelesítési paraméterek és jogkivonat-hatókörök konfigurálása

1. Nyissa meg a *apiConfig.js* fájlt a mintában.

2. Konfigurálja a mintát a webes API regisztrálása során korábban beszerzett paraméterekkel. Módosítsa a kód következő sorait úgy, hogy lecseréli az értékeket a webes API-ra és a feltehetően elérhető hatókörökre.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. Nyissa meg a *authConfig.js* fájlt a mintában.

1. Konfigurálja a mintát az egyoldalas alkalmazás regisztrálása során korábban beszerzett paraméterekkel. Módosítsa a kód következő sorait úgy, hogy lecseréli az értékeket a ClientId, a szolgáltatói metaadatok és a jogkivonat-kérelmek hatókörével.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

További információkért tekintse meg ezt a [JavaScript B2C egyoldalas alkalmazás mintát](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

## <a name="support-password-reset"></a>Támogatási jelszó alaphelyzetbe állítása

Ebben a szakaszban kiterjesztjük egy egyoldalas alkalmazást az Azure AD B2C jelszó-visszaállítási felhasználói folyamat használatára. Bár a MSAL.js jelenleg nem támogatja több felhasználói folyamat vagy egyéni házirend natív módon történő használatát, használhatja a könyvtárat a gyakori használati esetek, például a jelszó-visszaállítás kezelésére.

A következő lépések feltételezik, hogy már követte az előző [JavaScript Spa](#javascript-spa) szakasz lépéseit.

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>1. lépés: adja meg a szolgáltatói karakterláncot a jelszó-visszaállítási felhasználói folyamathoz

1. Először hozzon létre egy objektumot, amelyben tárolja a szolgáltatói URI-ket:

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. Ezután inicializálja a MSAL objektumot a `signInSignUp` szabályzattal alapértelmezettként (lásd a fenti kódrészletet). Amikor egy felhasználó megpróbál bejelentkezni, a következő képernyő jelenik meg:

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Azure AD B2C által megjelenített bejelentkezési képernyő":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>2. lépés: hitelesítési hibák elfogása és kezelése a bejelentkezési módszerben

Ha a felhasználó **elfelejtette a jelszót**, az alkalmazás olyan hibát jelez, amelyet a kódban fel kell fogni, majd a megfelelő felhasználói folyamat bemutatásával kell kezelnie. Ebben az esetben a `b2c_1_reset` jelszó-visszaállítási folyamat.

1. A bejelentkezési módszert a következőképpen bővítheti:

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. Az előző kódrészletből megtudhatja, hogyan jelenítheti meg a jelszó-visszaállítási képernyőt, miután megtekintette a hibát a kóddal `AADB2C90118` .

    A jelszó alaphelyzetbe állítása után a rendszer visszaadja a felhasználót az alkalmazásnak, hogy újra bejelentkezzen.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="A jelszó-visszaállítási folyamat képernyők Azure AD B2C" border="false":::

    További információ a hibakódokról és a kezelési kivételekről: [MSAL hiba és kivételek kódjai](msal-error-handling-js.md).

## <a name="next-steps"></a>Következő lépések

További információ ezekről a Azure AD B2C fogalmakról:

- [Felhasználói folyamatok](../../active-directory-b2c/tutorial-create-user-flows.md)
- [Egyéni szabályzatok](../../active-directory-b2c/custom-policy-get-started.md)
- [Felhasználói felület testreszabása](../../active-directory-b2c/configure-user-input.md)
