---
title: 'Gyors útmutató: bejelentkezés a felhasználókhoz JavaScript szögletes egyoldalas alkalmazásokban (SPA) hitelesítés-kóddal és Microsoft Graph hívása | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogy a JavaScript szögletes egyoldalas alkalmazás (SPA) hogyan jelentkezhet be a személyes fiókok, a munkahelyi fiókok és az iskolai fiókok felhasználói számára az engedélyezési kód folyamata és a hívási Microsoft Graph használatával.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 01/14/2021
ms.author: jamesmantu
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript, devx-track-js
ms.openlocfilehash: 0a4421ed548e6066338977260ed062e6ab14893f
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103702"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-an-angular-spa-using-the-auth-code-flow"></a>Gyors útmutató: bejelentkezés és hozzáférési jogkivonat beszerzése egy szögletes FÜRDŐben az Auth Code flow használatával

Ebben a rövid útmutatóban egy kód-mintát tölt le és futtat, amely bemutatja, hogyan jelentkezhet be a JavaScript szögletes egyoldalas alkalmazása (SPA) a felhasználókba, és meghívja a Microsoft Grapht az engedélyezési kód folyamatával. A kód minta bemutatja, hogyan kérhet hozzáférési tokent a Microsoft Graph API vagy bármely webes API meghívásához. 

Nézze meg, [Hogyan működik a minta](#how-the-sample-works) egy ábrán.

Ez a rövid útmutató a MSAL szögletes v2-t használja az engedélyezési kód folyamatával. Ahhoz, hogy egy hasonló gyors üzembe helyezési pont az 1. x MSAL használja az implicit folyamattal, tekintse meg a gyors útmutató [: felhasználói bejelentkezés JavaScript egyoldalas alkalmazásokban](./quickstart-v2-angular.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [ingyenes Azure-előfizetés létrehozása](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) vagy más Kódszerkesztő

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>A gyors üzembe helyezési alkalmazás regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás indításához használja az alábbi lehetőségek egyikét.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség (expressz): regisztrálja és állítsa be automatikusan az alkalmazást, majd töltse le a kód mintáját
>
> 1. Lépjen a <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Alkalmazásregisztrációk</a> gyors üzembe helyezési élményhez.
> 1. Adja meg az alkalmazás nevét.
> 1. A **támogatott fiókok típusai** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. Lépjen a gyors üzembe helyezés panelre, és kövesse az utasításokat az új alkalmazás letöltéséhez és automatikus konfigurálásához.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség (manuális): az alkalmazás-és a kód-minta regisztrálása és manuális konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. A **támogatott fiókok típusai** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
> 1. Válassza a **Regisztráció** lehetőséget. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
> 1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
> 1. A **platform-konfigurációk** területen válassza **a platform hozzáadása** lehetőséget. A megnyíló panelen válassza az **egylapos alkalmazás** lehetőséget.
> 1. Állítsa be az **átirányítási URI** -k értékét `http://localhost:4200/` . Ez a NodeJS alapértelmezett portja a helyi gépen fog figyelni. A felhasználó sikeres hitelesítése után visszaküldjük a hitelesítési választ erre az URI-ra. 
> 1. A módosítások alkalmazásához válassza a **Konfigurálás** lehetőséget.
> 1. A **platform konfigurációi** területen bontsa ki az **egyoldalas alkalmazások** elemet.
> 1. Győződjön meg róla, hogy a megadott engedélyezési **típusoknál** ![ már konfigurálva ](media/quickstart-v2-javascript/green-check.png) van az átirányítási URI a PKCE.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy az ebben a rövid útmutatóban szereplő kód minta működjön, adjon hozzá egy **átirányítási URI** -t `http://localhost:4200/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

 #### <a name="step-2-download-the-project"></a>2. lépés: A projekt letöltése

> [!div renderon="docs"]
> Ha Node.js használatával szeretné futtatni a projektet egy webkiszolgálóval, [töltse le az alapprojekt fájljait](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> A projekt futtatása webkiszolgálóval Node.js használatával

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>3. lépés: a JavaScript-alkalmazás konfigurálása
>
> Az *src* mappában Nyissa meg az *app* mappát, majd nyissa meg az *app. Module. TS* fájlt, és frissítse a `clientID` , a és az `authority` `redirectUri` értékeket az `auth` objektumban.
>
> ```javascript
> // MSAL instance to be passed to msal-angular
> export function MSALInstanceFactory(): IPublicClientApplication {
>   return new PublicClientApplication({
>     auth: {
>       clientId: 'Enter_the_Application_Id_Here',
>       authority: 'Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here',
>       redirectUri: 'Enter_the_Redirect_Uri_Here'
>     },
>     cache: {
>       cacheLocation: BrowserCacheLocation.LocalStorage,
>       storeAuthStateInCookie: isIE, // set to true for IE 11
>     },
>   });
> }
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Módosítsa a szakasz értékeit az `auth` itt leírtak szerint:
>
> - `Enter_the_Application_Id_Here` a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** .
> - `Enter_the_Cloud_Instance_Id_Here` Az Azure-felhő példánya. A fő vagy a globális Azure-felhőhöz írja be a következőt: `https://login.microsoftonline.com/` . Az **országos** felhők (például Kína) esetében lásd: [nemzeti felhők](authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` értéke a következők egyike lehet:
>   - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére**. Például: `contoso.microsoft.com`.
>   - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `organizations` .
>   - Ha az alkalmazás a *szervezeti címtárban és a személyes Microsoft-fiókokban is támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `common` . Ehhez a rövid útmutatóhoz használja **a következőt:** `common` .
>   - A *személyes Microsoft-fiókok* támogatásának korlátozásához cserélje le ezt az értéket a következőre: `consumers` .
> - A `Enter_the_Redirect_Uri_Here` értéke `http://localhost:4200/`.
>
> Az `authority` *app. Module. TS* értékének a következőhöz hasonlónak kell lennie, ha a fő (globális) Azure-felhőt használja:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítójának**, **könyvtárának (BÉRLŐi) azonosítójának** és a **támogatott fióktípus** értékének megkereséséhez nyissa meg az alkalmazás regisztrációjának **Áttekintés** lapját a Azure Portal.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékeivel konfiguráltuk.

> [!div renderon="docs"]
>
> Görgessen le ugyanabban a fájlban, és frissítse a t `graphMeEndpoint` . 
> - Cserélje le a `Enter_the_Graph_Endpoint_Herev1.0/me` karakterláncot a `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` a végpont az API-hívásokat fogja elvégezni. A fő (globális) Microsoft Graph API szolgáltatásnál adja meg a `https://graph.microsoft.com/` következőt: (a záró továbbítási perjelet is beleértve). További információért lásd a [dokumentációt](https://docs.microsoft.com/graph/deployments).
>
>
> ```javascript
> export function MSALInterceptorConfigFactory(): MsalInterceptorConfiguration {
>   const protectedResourceMap = new Map<string, Array<string>>();
>   protectedResourceMap.set('Enter_the_Graph_Endpoint_Herev1.0/me', ['user.read']);
>
>   return {
>     interactionType: InteractionType.Redirect,
>     protectedResourceMap
>   };
> }
> ```
>
>
 #### <a name="step-4-run-the-project"></a>4. lépés: a projekt futtatása

A projekt futtatása webkiszolgálóval Node.js használatával:

1. A kiszolgáló elindításához futtassa a következő parancsokat a projekt könyvtárából:
    ```console
    npm install
    npm start
    ```
1. Nyissa meg a következő címet: `http://localhost:4200/`.

1. A **bejelentkezési** folyamat elindításához és a Microsoft Graph API meghívásához válassza a bejelentkezés lehetőséget.

    Amikor először jelentkezik be, a rendszer arra kéri, hogy adja meg az engedélyt, hogy az alkalmazás hozzáférhessen a profiljához, és jelentkezzen be. Miután sikeresen bejelentkezett, kattintson a **profil** gombra a felhasználói adatok megjelenítéséhez az oldalon.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése

![Egy egyoldalas alkalmazás engedélyezési kódjának folyamatát ábrázoló diagram.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

A MSAL.js kódtár bejelentkezik a felhasználók számára, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kéri. 

Ha Node.js van telepítve, a legújabb verziót a Node.js Package Manager (NPM) segítségével töltheti le:

```console
npm install @azure/msal-browser @azure/msal-angular@2
```

## <a name="next-steps"></a>Következő lépések

Az Auth Code flow alkalmazás Vanilla JavaScript használatával történő létrehozásával kapcsolatos részletes útmutatóért tekintse meg a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag a bejelentkezéshez és az MS Graph meghívásához](./tutorial-v2-javascript-auth-code.md)
