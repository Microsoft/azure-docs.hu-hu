---
title: 'Gyors útmutató: felhasználói bejelentkezés a szögletes egyoldalas alkalmazásokban – Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan hívhat egy szögletes alkalmazás olyan API-t, amely a Microsoft Identity platform által kiadott hozzáférési jogkivonatokat igényel.
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
ms.openlocfilehash: 5ac6a19fc569d60f75f9da788629331a70b0a4c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225047"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Gyors útmutató: bejelentkezés a felhasználókba és hozzáférési jogkivonat beszerzése egy szögletes egyoldalas alkalmazásban

Ebben a rövid útmutatóban egy mintakód-mintát tölt le és futtat, amely bemutatja, hogyan jelentkezhet be egy szögletes egyoldalas alkalmazás (SPA) a felhasználókba, és hogyan hívhat Microsoft Graph. A kód minta bemutatja, hogyan kérhet hozzáférési tokent a Microsoft Graph API vagy bármely webes API meghívásához.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node.js](https://nodejs.org/en/download/).
* A Project Files vagy a [Visual studio 2019](https://visualstudio.microsoft.com/downloads/) a projekt futtatására szolgáló [Visual Studio Code](https://code.visualstudio.com/download) .

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>A Gyorsindítás alkalmazás regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás indításához használja az alábbi lehetőségek egyikét.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>1. lehetőség (expressz): regisztrálja és automatikusan konfigurálja az alkalmazást, majd töltse le a kód mintáját.
>
> 1. Lépjen a <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Alkalmazásregisztrációk</a> gyors üzembe helyezési élményhez.
> 1. Adja meg az alkalmazás nevét, majd válassza a **regisztráció** lehetőséget.
> 1. Lépjen a gyors üzembe helyezés panelre, és tekintse meg a szögletes rövid útmutatót. Az új alkalmazás letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>2. lehetőség (manuális): az alkalmazás-és a kód-minta regisztrálása és manuális konfigurálása
>
> #### <a name="step-1-register-the-application"></a>1. lépés: az alkalmazás regisztrálása
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Kövesse az utasításokat [egy egyoldalas alkalmazás regisztrálásához](./scenario-spa-app-registration.md) a Azure Portal.
> 1. Vegyen fel egy új platformot az alkalmazás regisztrációjának **hitelesítés** ablaktábláján, és regisztrálja az átirányítási URI-t: `http://localhost:4200/` .
> 1. Ez a rövid útmutató az [implicit engedélyezési folyamatot](v2-oauth2-implicit-grant-flow.md)használja. Az **implicit engedélyezés és a hibrid folyamatok** szakaszban válassza az **azonosító tokenek** és a **hozzáférési tokenek** elemet. Az azonosító jogkivonatok és hozzáférési tokenek megadása kötelező, mert az alkalmazás bejelentkezik a felhasználókba, és meghívja az API-t.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy az ebben a rövid útmutatóban szereplő kód minta működjön, hozzá kell adnia egy átirányítási URI-t, **http://localhost:4200/** és engedélyeznie kell az **implicit engedélyezést**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-code-sample"></a>2. lépés: a mintakód letöltése
>[!div renderon="docs"]
>Ha Node.js használatával szeretné futtatni a projektet egy webkiszolgálóval, [klónozott a minta-tárházat](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) , vagy [le kell töltenie az alapprojekt fájljait](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Nyissa meg a fájlokat egy szerkesztővel, például a Visual Studio Code használatával.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>3. lépés: a JavaScript-alkalmazás konfigurálása
>
> A *src/app* mappában szerkessze az *app. Module. TS* fájlt, és állítsa be a `clientId` és a `authority` értékeket `MsalModule.forRoot` .
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
>|Érték neve|Leírás|
>|---------|---------|
>|Enter_the_Application_Id_Here|Az alkalmazás regisztrációjának **Áttekintés** lapján ez az **alkalmazás (ügyfél) azonosítójának** értéke. |
>|Enter_the_Cloud_Instance_Id_Here|Ez az Azure-felhő példánya. A fő vagy a globális Azure-felhőhöz írja be a következőt: **https://login.microsoftonline.com** . Az országos felhők (például Kína) esetében lásd: [nemzeti felhők](./authentication-national-cloud.md).|
>|Enter_the_Tenant_Info_Here| Állítsa be a következő lehetőségek egyikét: Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a címtár (bérlő) vagy a bérlő nevére (például **contoso.microsoft.com**). Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket **szervezetekkel**. Ha az alkalmazás *minden szervezeti címtárban és személyes Microsoft-fiókban is támogatja a fiókokat*, cserélje le ezt az értéket **közösre**. Ha *csak a személyes Microsoft-fiókok* támogatását szeretné korlátozni, cserélje le ezt az értéket a **fogyasztókkal**. |
>|Enter_the_Redirect_Uri_Here|Cserélje le a-t **http://localhost:4200** .|
>|cacheLocation  | Választható Állítsa be a böngésző tárterületét a hitelesítési állapothoz. Az alapértelmezett érték a **sessionStorage**.   |
>|storeAuthStateInCookie  | Választható Azonosítsa a hitelesítési kérelem állapotát tároló könyvtárat. Ez az állapot szükséges a böngésző cookie-jai hitelesítési folyamatainak ellenőrzéséhez. Ez a cookie be van állítva az Internet Explorer és a Edge számára a két böngésző számára. További részletekért tekintse meg az [ismert problémákat](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
>
> Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.

További információ az elérhető konfigurálható lehetőségekről: [ügyfélalkalmazások inicializálása](msal-js-initializing-client-applications.md).

A MSAL.js könyvtár forráskódját az [AzureAD/Microsoft-Authentication-Library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) adattáron találja a githubon.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>3. lépés: a projekt futtatása

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>4. lépés: a projekt futtatása

Ha Node.jst használ:

1. Indítsa el a kiszolgálót a következő parancsok futtatásával a projekt könyvtárából:

   ```console
   npm install
   npm start
   ```

1. Lépjen a **http://localhost:4200/** lapra.
1. Válassza a **Bejelentkezés** lehetőséget.
1. Válassza ki a **profilt** a Microsoft Graph meghívásához.

Ha a böngésző betölti az alkalmazást, válassza a **Bejelentkezés** lehetőséget. Amikor először jelentkezik be, a rendszer arra kéri, hogy adja meg az engedélyt, hogy az alkalmazás hozzáférhessen a profiljához, és jelentkezzen be. Miután sikeresen bejelentkezett, válassza a **profil** lehetőséget, és a felhasználói profil adatai megjelennek az oldalon.

## <a name="how-the-sample-works"></a>A minta működése

![Diagram, amely bemutatja, hogyan működik a jelen rövid útmutatóban szereplő minta alkalmazás](./media/quickstart-v2-angular/diagram-auth-flow-spa-angular.svg)


## <a name="next-steps"></a>Következő lépések

Következő lépésként megtudhatja, hogyan jelentkezhet be a felhasználóba, és hogyan szerezheti be a tokeneket a szögletes oktatóanyagban:

> [!div class="nextstepaction"]
> [Szögletes oktatóanyag](./tutorial-v2-angular.md)