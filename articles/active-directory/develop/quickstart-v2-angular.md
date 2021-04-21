---
title: 'Rövid útmutató: Felhasználók bejelentkezése egyoldalas Angular-alkalmazásokba – Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan hívhat meg egy Angular-alkalmazás egy olyan API-t, amely a Microsoft identitásplatformja által kiadott hozzáférési jogkivonatokat igényel.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: d53ce97c4af302801098d9abaa633ced98c93f3a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814027"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Rövid útmutató: Felhasználók bejelentkeztetése és hozzáférési jogkivonat beszerzése egy egyoldalas Angular-alkalmazásban

Ebben a rövid útmutatóban letölt és futtat egy kódmintát, amely bemutatja, hogyan tud egy Angular egyoldalas alkalmazás (SPA) bejelentkezni a felhasználókba, és hogyan lehet Microsoft Graph. A kódminta bemutatja, hogyan lehet hozzáférési jogkivonatot szerezni a Microsoft Graph API vagy bármely webes API hívásához.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. [Hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js: ](https://nodejs.org/en/download/).
* [Visual Studio Code-ban](https://code.visualstudio.com/download) szerkessze a projektfájlokat, vagy Visual Studio [2019-es](https://visualstudio.microsoft.com/downloads/) kódot a projekt futtatásához.

> [!div renderon="docs"]
>
> ## <a name="register-and-download-the-quickstart-app"></a>A gyorsindítási alkalmazás regisztrálása és letöltése
>
> A gyors üzembe helyezési alkalmazás az alábbi lehetőségek egyikével indítja el.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>1. lehetőség (expressz): Regisztrálja és automatikusan konfigurálja az alkalmazást, majd töltse le a kódmintát
>
> 1. Ugrás a <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal – Alkalmazásregisztrációk</a> útmutatóra.
> 1. Adja meg az alkalmazás nevét, majd válassza a Regisztráció **lehetőséget.**
> 1. A gyorsindítási panelen keresse meg az Angular gyorsútmutatót. Kövesse az utasításokat az új alkalmazás letöltéséhez és automatikus konfigurálásához.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>2. lehetőség (manuális): Az alkalmazás és a kódminta regisztrálása és manuális konfigurálása
>
> #### <a name="step-1-register-the-application"></a>1. lépés: Az alkalmazás regisztrálása
>
> 1. Kövesse az utasításokat [egy egyoldalas](./scenario-spa-app-registration.md) alkalmazás regisztrálásához a Azure Portal.
> 1. Adjon hozzá egy  új platformot az alkalmazásregisztráció Hitelesítés paneljéhez, és regisztrálja az átirányítási URI-t: `http://localhost:4200/` .
> 1. Ez a rövid útmutató az [implicit engedélyfolyamot használja.](v2-oauth2-implicit-grant-flow.md) Az **Implicit engedély és hibrid folyamatok szakaszban válassza** az Azonosító **jogkivonatok és** a Hozzáférési **jogkivonatok lehetőséget.** Azonosító jogkivonatokra és hozzáférési jogkivonatokra azért van szükség, mert ez az alkalmazás bejelentkezteti a felhasználókat, és api-t hív meg.

> [!div class="sxs-lookup" renderon="portal"]
>
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása a Azure Portal
>
> Ahhoz, hogy a rövid útmutatóban a kódminta működjön, hozzá kell adni egy átirányítási URI-t a-hoz, és engedélyeznie `http://localhost:4200/` kell az **Implicit engedélyezést.**
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Ezeket a módosításokat a számomra kell eltenem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-code-sample"></a>2. lépés: A kódminta letöltése
>[!div renderon="docs"]
>Ha webkiszolgálóval futtatja a projektet a Node.js, [](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) klónozza a mintaadattárat, vagy töltse le az alapvető [projektfájlokat.](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip) Nyissa meg a fájlokat egy szerkesztőben, például a Visual Studio Code-ban.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [A kódminta letöltése](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>3. lépés: A JavaScript-alkalmazás konfigurálása
>
> Az *src/app mappában* szerkessze az *app.module.ts* fájlt, és állítsa be a `clientId` és a értéket a `authority` `MsalModule.forRoot` alatt.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> Cserélje le ezeket az értékeket:
>
>|Érték neve|Description|
>|---------|---------|
>|Enter_the_Application_Id_Here|Az **alkalmazásregisztráció** Áttekintés lapján ez az **alkalmazásazonosító (ügyfél) értéke.** |
>|Enter_the_Cloud_Instance_Id_Here|Ez az Azure-felhőpéldány. A fő vagy globális Azure-felhőhöz írja be a következőt: `https://login.microsoftonline.com` . Országos felhők (például Kína) esetében lásd: [Országos felhők.](./authentication-national-cloud.md)|
>|Enter_the_Tenant_Info_Here| Állítsa be a következő beállítások egyikét: Ha az alkalmazás támogatja az ebben a szervezeti címtárban található fiókokat, cserélje le ezt az értéket a címtár (bérlő) azonosítójára vagy a bérlő nevére `contoso.microsoft.com` (például). Ha az alkalmazás bármely szervezeti címtárban támogatja *a fiókokat,* cserélje le ezt az értéket a következőre: `organizations` . Ha az alkalmazás támogatja *a szervezeti* címtárban és a személyes Microsoft-fiókokban található fiókokat, cserélje le ezt az értéket a következőre: `common` . Ha csak a személyes *Microsoft-fiókokra korlátozza a* támogatást, cserélje le ezt az értéket a következőre: `consumers` . |
>|Enter_the_Redirect_Uri_Here|Cserélje le a helyére `http://localhost:4200` a következőt: .|
>|cacheLocation  | (Nem kötelező) Állítsa be a böngésző tárterületét a hitelesítési állapothoz. A mező alapértelmezett értéke: `sessionStorage`.   |
>|storeAuthStateInCookie  | (Nem kötelező) Azonosítsa a hitelesítési kérelem állapotát tároló kódtárat. Ez az állapot a böngésző cookie-kban való hitelesítési folyamatának érvényesítéséhez szükséges. Ez a cookie a két Internet Explorer Microsoft Edge beállítására van beállítva. További részletekért tekintse meg az ismert [problémákat.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues) |
>
> Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.

> További információ az elérhető konfigurálható beállításokról: [Ügyfélalkalmazások inicializálása.](msal-js-initializing-client-applications.md)

> A MSAL.js kódtár forráskódját a GitHub [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) adattárában találja.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: Az alkalmazás konfigurálva van, és készen áll a futtatásra
> Konfiguráljuk a projektet az alkalmazás tulajdonságainak értékeivel.

> [!div renderon="docs"]
>
> Görgessen le ugyanabban a fájlban, és frissítse a `graphMeEndpoint` fájlt. 
> - Cserélje le a `Enter_the_Graph_Endpoint_Herev1.0/me` sztringet a következőre: `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` A az a végpont, amelyről API-hívásokat fog kezdeményezni. Az API-szolgáltatás fő (globális) Microsoft Graph (a záró `https://graph.microsoft.com/` perjellel együtt) adja meg a következőt: . További információért lásd a [dokumentációt](https://docs.microsoft.com/graph/deployments).
>
>
> ```javascript
>      protectedResourceMap: [
>        ['Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']]
>      ],
> ```
>
>

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>4. lépés: A projekt futtatása

Ha a következőt használjaNode.js:

1. Indítsa el a kiszolgálót az alábbi parancsok a projektkönyvtárból való futtatásával:

   ```console
   npm install
   npm start
   ```

1. Nyissa meg a következőt: `http://localhost:4200/`.
1. Válassza a **Bejelentkezés lehetőséget.** Amikor első alkalommal jelentkezik be, a rendszer arra kéri, hogy engedélyezze az alkalmazásnak a profilhoz való hozzáférést, és automatikusan jelentkezzen be.
1. Válassza **a Profil** lehetőséget a Microsoft Graph. A felhasználói profil adatai megjelennek az oldalon.

## <a name="how-the-sample-works"></a>A minta működése

![Az ebben a rövid útmutatóban látható mintaalkalmazás működését bemutató ábra.](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)

## <a name="next-steps"></a>Következő lépések

Az Angular-oktatóanyagból megtudhatja, hogyan jelentkeztet be egy felhasználót és szerezhet be jogkivonatokat:

> [!div class="nextstepaction"]
> [Angular-oktatóanyag](./tutorial-v2-angular.md)
