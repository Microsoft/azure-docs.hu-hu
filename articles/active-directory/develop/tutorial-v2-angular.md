---
title: Egyoldalas alkalmazás-oktatóanyag – Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogy a szögletes SPA-alkalmazások hogyan hívhatnak meg olyan API-t, amelyhez hozzáférési jogkivonatok szükségesek a Microsoft Identity platform végpontján.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, devx-track-javascript
ms.openlocfilehash: a58da8b11876d662173ae83de43d8ed74ab43e93
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118292"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Oktatóanyag: bejelentkezés a felhasználókba és a Microsoft Graph API meghívása egy szögletes egyoldalas alkalmazásból

Ez az oktatóanyag azt mutatja be, hogyan használható a szögletes egyoldalas alkalmazás (SPA):
- Jelentkezzen be személyes fiókjaiba, munkahelyi fiókjaiba vagy iskolai fiókjaiba.
- Hozzáférési jogkivonat beszerzése.
- Hívja meg az Microsoft Graph API-t vagy más API-kat, amelyek hozzáférési jogkivonatokat igényelnek a *Microsoft Identity platform végpontján*.

>[!NOTE]
>Ez az oktatóanyag bemutatja, hogyan hozhat létre új szögletes SPA-t a Microsoft Authentication Library (MSAL) használatával. Ha egy minta alkalmazást szeretne letölteni, tekintse [meg a rövid](quickstart-v2-angular.md)útmutatót.

## <a name="how-the-sample-app-works"></a>A minta alkalmazás működése

![Diagram, amely bemutatja, hogyan működik az oktatóanyagban létrehozott minta alkalmazás](./media/tutorial-v2-angular/diagram-auth-flow-spa-angular.svg)

### <a name="more-information"></a>További információ

Az ebben az oktatóanyagban létrehozott minta alkalmazás lehetővé teszi, hogy egy szögletes fürdő lekérdezze a Microsoft Graph API-t vagy egy webes API-t, amely a Microsoft Identity platform végpontjának jogkivonatait fogadja el. A szögletes könyvtár MSAL az alapszintű MSAL.js könyvtár burkolója. Lehetővé teszi a szögletes (6 +) alkalmazások számára a vállalati felhasználók hitelesítését Microsoft Azure Active Directory, Microsoft-fiók felhasználók és a közösségi identitást használó felhasználók (például a Facebook, a Google és a LinkedIn) használatával. A kódtár azt is lehetővé teszi, hogy az alkalmazások hozzáférjenek a Microsoft Cloud Serviceshez vagy Microsoft Graphhoz.

Ebben az esetben a felhasználó bejelentkezése után hozzáférési jogkivonatot kér a rendszer, és hozzáadja a HTTP-kérésekhez az engedélyezési fejlécen keresztül. A token beszerzését és megújítását a MSAL kezeli.

### <a name="libraries"></a>Kódtárak

Ez az oktatóanyag a következő könyvtárat használja:

|Kódtár|Leírás|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Authentication Library JavaScript szögletes Burkolóhoz|

A MSAL.js könyvtár forráskódját az [AzureAD/Microsoft-Authentication-Library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) adattáron találja a githubon.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag futtatásához a következőkre lesz szüksége:

* Helyi webkiszolgáló, például [Node.js](https://nodejs.org/en/download/). Az oktatóanyagban szereplő utasítások a Node.json alapulnak.
* Integrált fejlesztési környezet (IDE), például a [Visual Studio Code](https://code.visualstudio.com/download)a projektfájlok szerkesztéséhez.

## <a name="create-your-project"></a>Projekt létrehozása

Új szögletes alkalmazás létrehozása a következő NPM parancsok használatával:

```bash
npm install -g @angular/cli@8                    # Install the Angular CLI
ng new my-application --routing=true --style=css # Generate a new Angular app
cd my-application                                # Change to the app directory
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Az alkalmazás regisztrálása

Kövesse az [utasításokat egy egyoldalas alkalmazás regisztrálásához](./scenario-spa-app-registration.md) a Azure Portal.

A regisztráció alkalmazás **áttekintése** oldalán jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.

Regisztrálja az **átirányítási URI** -értéket, **http://localhost:4200/** és engedélyezze az implicit engedélyezési beállításokat.

## <a name="configure-the-application"></a>Az alkalmazás konfigurálása

1. A *src/app* mappában szerkessze az *app. Module. TS* fájlt, és adja hozzá a következőt `MSALModule` `imports` `isIE` :

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    Cserélje le ezeket az értékeket:

    |Érték neve|Névjegy|
    |---------|---------|
    |Enter_the_Application_Id_Here|Az alkalmazás regisztrációjának **Áttekintés** lapján ez az **alkalmazás (ügyfél) azonosítójának** értéke. |
    |Enter_the_Cloud_Instance_Id_Here|Ez az Azure-felhő példánya. A fő vagy a globális Azure-felhőhöz írja be a következőt: **https://login.microsoftonline.com** . Az országos felhők (például Kína) esetében lásd: [nemzeti felhők](./authentication-national-cloud.md).|
    |Enter_the_Tenant_Info_Here| Állítsa be a következő lehetőségek egyikét: Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a címtár (bérlő) vagy a bérlő nevére (például **contoso.microsoft.com**). Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket **szervezetekkel**. Ha az alkalmazás *minden szervezeti címtárban és személyes Microsoft-fiókban is támogatja a fiókokat*, cserélje le ezt az értéket **közösre**. Ha *csak a személyes Microsoft-fiókok*támogatását szeretné korlátozni, cserélje le ezt az értéket a **fogyasztókkal**. |
    |Enter_the_Redirect_Uri_Here|Cserélje le a-t **http://localhost:4200** .|

    További információ az elérhető konfigurálható lehetőségekről: [ügyfélalkalmazások inicializálása](msal-js-initializing-client-applications.md).

2. A fájl elején adja hozzá a következő importálási utasítást:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Adja hozzá a következő importálási utasításokat a felső részhez `src/app/app.component.ts` :

    ```javascript
    import { MsalService, BroadcastService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Bejelentkezés felhasználóként

Adja hozzá a következő kódot a `AppComponent` felhasználóhoz való bejelentkezéshez:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    ngOnInit() { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> Javasoljuk `loginRedirect` , hogy az Internet Explorer felhasználóit használja.

## <a name="acquire-a-token"></a>Jogkivonat beszerzése

### <a name="angular-interceptor"></a>Szögletes Interceptor

A MSAL olyan `Interceptor` osztályt biztosít, amely automatikusan lekéri azokat a kimenő kérelmeket, amelyek a szögletes `http` ügyfelet használják az ismert védett erőforrásokra.

Először is adja `Interceptor` meg az osztályt szolgáltatóként az alkalmazáshoz:

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

Ezután adja meg a védett erőforrások leképezését a következőre, és adja hozzá a `MsalModule.forRoot()` `protectedResourceMap` hatókört a következőhöz `consentScopes` :

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

Végül egy HTTP-kérelemmel rendelkező felhasználó profiljának beolvasása:

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent, acquireTokenPopup, acquireTokenRedirect
A MSAL három módszert használ a tokenek beszerzésére: `acquireTokenRedirect` , `acquireTokenPopup` és `acquireTokenSilent` . Azt javasoljuk azonban, hogy a `MsalInterceptor` szögletes alkalmazások helyett az osztályt használja, ahogy az az előző szakaszban is látható.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. A `loginRedirect` vagy a `loginPopup` metódus első futtatása után `acquireTokenSilent` általában a védett erőforrások a későbbi hívásokban való eléréséhez használt jogkivonatok beszerzésére szolgál. A jogkivonatok kérése vagy megújítása csendesen történik.

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

Ebben a kódban az `scopes` API hozzáférési jogkivonatában visszaadott hatóköröket tartalmaz.

Például:

* `["user.read"]`Microsoft Graph
* `["<Application ID URL>/scope"]`egyéni webes API-khoz (azaz `api://<Application ID>/access_as_user` )

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Előfordulhat, hogy a felhasználónak a Microsoft Identity platform-végponttal kell kommunikálnia. Például:

* Előfordulhat, hogy a felhasználóknak újra meg kell adniuk a hitelesítő adataikat, mert a jelszavuk lejárt.
* Az alkalmazása olyan további erőforrás-hatókörökhöz kér hozzáférést, amelyeknek a felhasználónak hozzá kell járulnia.
* Kétfaktoros hitelesítés szükséges.

A legtöbb alkalmazás javasolt mintája először hívja meg `acquireTokenSilent` a-t, majd a kivételt, majd a hívást `acquireTokenPopup` (vagy `acquireTokenRedirect` ) egy interaktív kérelem elindításához.

Az eredmények meghívása `acquireTokenPopup` egy előugró bejelentkezési ablakban. Azt is megteheti, `acquireTokenRedirect` hogy átirányítja a felhasználókat a Microsoft Identity platform-végpontra. Ebben az ablakban a felhasználóknak meg kell erősíteniük a hitelesítő adataikat, meg kell adniuk a szükséges erőforrást, vagy el kell végezniük a kétfaktoros hitelesítést.

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> Ez a rövid útmutató a `loginRedirect` és a `acquireTokenRedirect` metódusokat a Microsoft Internet Explorer használatával használja az előugró ablakok Internet Explorer általi kezelésére vonatkozó [ismert probléma](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) miatt.

## <a name="log-out"></a>Kijelentkezés

Adja hozzá a következő kódot a felhasználó kijelentkezéséhez:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Felhasználói felület hozzáadása
A következő témakörben talál példát arra, hogyan adhat hozzá felhasználói felületet a szögletes anyagminta-függvénytár használatával: [minta alkalmazás](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular).

## <a name="test-your-code"></a>A kód tesztelése

1.  Indítsa el a webkiszolgálót a port figyeléséhez a következő parancsok parancssori parancssorból történő futtatásával az alkalmazás mappájából:

    ```bash
    npm install
    npm start
    ```
1. A böngészőben adja meg a vagy a (z **http://localhost:4200** **http://localhost:{port}** ) értéket, ahol a *port* a webkiszolgáló által figyelt port.


### <a name="provide-consent-for-application-access"></a>Adja meg az alkalmazás-hozzáférés beleegyezikét

Amikor először kezdi bejelentkezni az alkalmazásba, a rendszer felszólítja, hogy adjon hozzáférést a profilhoz, és engedélyezze, hogy bejelentkezzen:

![A "kért engedélyek" ablak](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

## <a name="add-scopes-and-delegated-permissions"></a>Hatókörök és delegált engedélyek hozzáadása

A Microsoft Graph API-nak a felhasználónak *. Read* hatókörrel kell rendelkeznie a felhasználói profil olvasásához. Alapértelmezés szerint a rendszer automatikusan hozzáadja ezt a hatókört a regisztrációs portálon regisztrált összes alkalmazáshoz. A Microsoft Graph egyéb API-jai, valamint a háttér-kiszolgálóhoz tartozó egyéni API-k további hatóköröket is igényelhetnek. A Microsoft Graph API-nak például a *naptárak. Read* hatókört kell megadnia a felhasználók naptárának listázásához.

Ha egy alkalmazás kontextusában szeretné elérni a felhasználó naptárait, adja hozzá a *naptárakat. olvassa el* a delegált jogosultságokat az alkalmazás regisztrációs adataihoz. Ezután adja hozzá a *naptárak. Read* hatókört a `acquireTokenSilent` híváshoz.

>[!NOTE]
>A rendszer a hatókörök számának növelésével további hozzájárulásokat is kérhet a felhasználótól.

Ha egy háttérbeli API-nak nincs szüksége hatókörre (nem ajánlott), a *clientId* használhatja a jogkivonatok beszerzésére irányuló hívások hatókörét.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések

Ha most ismerkedik az identitás-és hozzáférés-kezeléssel, számos cikk segít a modern hitelesítési fogalmak megismerésében, a hitelesítés és az [Engedélyezés](authentication-vs-authorization.md)megkezdése előtt.

Ha a Microsoft Identity platformon mélyebbre szeretne ugrani egy egyoldalas alkalmazás-fejlesztésre, a többrészes [forgatókönyv: az egyoldalas alkalmazások](scenario-spa-overview.md) sorozata segítséget nyújt az első lépésekhez.