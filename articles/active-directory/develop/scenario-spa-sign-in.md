---
title: Egyoldalas alkalmazás bejelentkezési & kijelentkezés
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (bejelentkezés)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bdfffbf8d2e416c87dd5abb8f6383c58ad270231
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584363"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Egyoldalas alkalmazás: bejelentkezés és kijelentkezés

Megtudhatja, hogyan adhat hozzá bejelentkezést az egyoldalas alkalmazás kódjához.

Ahhoz, hogy a tokenek hozzáférjenek az alkalmazás API-khoz, hitelesített felhasználói környezetre van szükség. A felhasználókat kétféleképpen lehet bejelentkezni az alkalmazásba MSAL.js módon:

* [Előugró ablak](#sign-in-with-a-pop-up-window)a `loginPopup` metódus használatával
* [Átirányítás](#sign-in-with-redirect)a `loginRedirect` metódus használatával

Igény szerint átadhatja azon API-k hatóköreit, amelyekhez a felhasználónak a bejelentkezéskor hozzá kell járulnia.

> [!NOTE]
> Ha az alkalmazásnak már van hozzáférése egy hitelesített felhasználói környezethez vagy azonosító jogkivonathoz, kihagyhatja a bejelentkezési lépést, és közvetlenül is beszerezheti a jogkivonatokat. Részletekért lásd: [egyszeri bejelentkezés MSAL.js bejelentkezés nélkül](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Egy előugró vagy átirányítási élmény közötti választás

Az alkalmazásban nem használhatók az előugró és az átirányítási módszerek. Az előugró vagy átirányítási élmény közötti választás az alkalmazási folyamattól függ:

* Ha nem szeretné, hogy a felhasználók a hitelesítés során elmozdulnak a fő alkalmazási lapról, javasoljuk, hogy az előugró metódust. Mivel a hitelesítés átirányítása egy előugró ablakban történik, a fő alkalmazás állapota megmarad.

* Ha a felhasználók olyan böngészőbeli korlátozásokkal vagy házirendekkel rendelkeznek, amelyekben az előugró ablakok le vannak tiltva, használhatja az átirányítási módszert. Használja az átirányítás módszert az Internet Explorer böngészővel, mert az [Internet Explorerben ismert problémák léptek fel az előugró ablakokban](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Bejelentkezés előugró ablakkal


# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success

        // In case multiple accounts exist, you can select
        const currentAccounts = myMsal.getAllAccounts();
    
        if (currentAccounts === null) {
            // no accounts detected
        } else if (currentAccounts.length > 1) {
            // Add choose account code here
        } else if (currentAccounts.length === 1) {
            username = currentAccounts[0].username;
        }
    
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular"></a>[Angular](#tab/angular)

A MSAL szögletes burkolója lehetővé teszi, hogy az alkalmazásban meghatározott útvonalakat biztosítson `MsalGuard` az útvonal-definícióhoz való hozzáadással. Ez az őr meghívja a metódust, hogy bejelentkezzen az útvonal elérésekor.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Az előugró ablak felhasználói felületén engedélyezze a `popUp` konfigurációs beállítást. A beleegyező hatóköröket a következőképpen adhatja át:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Bejelentkezés átirányítással

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

let username = "";

const myMsal = new PublicClientApplication(config);

function handleResponse(response) {
    //handle redirect response

    // In case multiple accounts exist, you can select
    const currentAccounts = myMsal.getAllAccounts();

    if (currentAccounts === null) {
        // no accounts detected
    } else if (currentAccounts.length > 1) {
        // Add choose account code here
    } else if (currentAccounts.length === 1) {
        username = currentAccounts[0].username;
    }
}

myMsal.handleRedirectPromise(handleResponse);

myMsal.loginRedirect(loginRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

Az átirányítási módszerek nem adnak vissza ígéretet a fő alkalmazásból való elmozdulás miatt. A visszaadott jogkivonatok feldolgozásához és eléréséhez regisztrálja az átirányítási módszerek meghívása előtt a sikeres és a visszahívási hibákat.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new UserAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

A kód itt ugyanaz, mint az előugró ablakba való bejelentkezéssel kapcsolatos szakasz korábbi részében leírtak szerint. Az alapértelmezett folyamat átirányítása.

---

## <a name="sign-out"></a>Kijelentkezés

A MSAL könyvtár olyan `logout` metódust biztosít, amely törli a gyorsítótárat a böngészőbeli tárolóban, és kijelentkezési kérést küld Azure Active Directory (Azure ad) számára. A kijelentkezés után a könyvtár alapértelmezés szerint átirányítja az alkalmazás kezdőlapját.

Beállíthatja azt az URI-t, amelyre a kijelentkezést követően át kell irányítani a beállítást `postLogoutRedirectUri` . Ezt az URI-t a kijelentkezési URI-ként is regisztrálni kell az alkalmazás regisztrálásakor.

# <a name="javascript-msaljs-2x"></a>[JavaScript (MSAL.js 2. x)](#tab/javascript2)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new PublicClientApplication(config);

// you can select which account application should sign out
const logoutRequest = {
    account: myMsal.getAccountByUsername(username)
}

myMsal.logout(logoutRequest);
```

# <a name="javascript-msaljs-1x"></a>[JavaScript (MSAL.js 1. x)](#tab/javascript1)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>Következő lépések

Ebben a forgatókönyvben a következő cikkre léphet be, amely [az alkalmazáshoz tartozó jogkivonatot szerez](scenario-spa-acquire-token.md)be.