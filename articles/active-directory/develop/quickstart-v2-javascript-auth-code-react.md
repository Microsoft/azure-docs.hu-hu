---
title: 'Gyors útmutató: felhasználói bejelentkezés a JavaScriptben az egyoldalas alkalmazások (SPA) hitelesítés-kóddal való meghívása és a Call Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogy a JavaScript hogyan reagáljon egy egyoldalas alkalmazásra (SPA) a személyes fiókok, a munkahelyi fiókok és az iskolai fiókok felhasználói számára az engedélyezési kód folyamata és a hívási Microsoft Graph használatával.
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
ms.openlocfilehash: 0bf08c45e82dc6f36d4e179e95e1b58e655b14db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224367"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-a-react-spa-using-the-auth-code-flow"></a>Gyors útmutató: bejelentkezés és hozzáférési jogkivonat beszerzése egy reagáló SPA-ban az Auth Code flow használatával

Ebben a rövid útmutatóban egy olyan kódrészletet tölt le és futtat, amely bemutatja, hogy a JavaScript hogyan reagáljon egy egyoldalas alkalmazásra (SPA) a felhasználók számára, és hívja meg a Microsoft Grapht az engedélyezési kód folyamatának használatával. A kód minta bemutatja, hogyan kérhet hozzáférési tokent a Microsoft Graph API vagy bármely webes API meghívásához. 

Nézze meg, [Hogyan működik a minta](#how-the-sample-works) egy ábrán.

Ez a rövid útmutató a MSAL reagál az engedélyezési kód folyamatára. Az implicit folyamattal MSAL.jst használó hasonló gyors útmutató: gyors üzembe helyezés a [felhasználók között JavaScript egyoldalas alkalmazásokban](./quickstart-v2-javascript.md).

> [!IMPORTANT]
> MSAL reagál [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [ingyenes Azure-előfizetés létrehozása](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) vagy más Kódszerkesztő

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>A gyors üzembe helyezési alkalmazás regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás indításához használja az alábbi lehetőségek egyikét.
>
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
> 1. Ha megjelenik az **Alkalmazás regisztrálása** oldal, adjon nevet az alkalmazásnak.
> 1. A **támogatott fiókok típusai** területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
> 1. Válassza a **Regisztráció** lehetőséget. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
> 1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
> 1. A **platform-konfigurációk** területen válassza **a platform hozzáadása** lehetőséget. A megnyíló panelen válassza az **egylapos alkalmazás** lehetőséget.
> 1. Állítsa be az **átirányítási URI** -k értékét `http://localhost:3000/` . Ez a NodeJS alapértelmezett portja a helyi gépen fog figyelni. A felhasználó sikeres hitelesítése után visszaküldjük a hitelesítési választ erre az URI-ra. 
> 1. A módosítások alkalmazásához válassza a **Konfigurálás** lehetőséget.
> 1. A **platform konfigurációi** területen bontsa ki az **egyoldalas alkalmazások** elemet.
> 1. Győződjön meg róla, hogy a megadott engedélyezési **típusoknál** ![ már konfigurálva ](media/quickstart-v2-javascript/green-check.png) van az átirányítási URI a PKCE.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy az ebben a rövid útmutatóban szereplő kód minta működjön, adjon hozzá egy **átirányítási URI** -t `http://localhost:3000/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-project"></a>2. lépés: A projekt letöltése

> [!div renderon="docs"]
> Ha Node.js használatával szeretné futtatni a projektet egy webkiszolgálóval, [töltse le az alapprojekt fájljait](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip).

> [!div renderon="portal" class="sxs-lookup"]
> A projekt futtatása webkiszolgálóval Node.js használatával

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>3. lépés: a JavaScript-alkalmazás konfigurálása
>
> Az *src* mappában Nyissa meg a *authConfig.js* fájlt, és frissítse a `clientID` , `authority` , és `redirectUri` értékeket az `msalConfig` objektumban.
>
> ```javascript
> /**
> * Configuration object to be passed to MSAL instance on creation. 
> * For a full list of MSAL.js configuration parameters, visit:
> * https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/configuration.md 
> */
> export const msalConfig = {
>    auth: {
>        clientId: "Enter_the_Application_Id_Here",
>        authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>        redirectUri: "Enter_the_Redirect_Uri_Here"
>    },
>    cache: {
>        cacheLocation: "sessionStorage", // This configures where your cache will be stored
>        storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    },
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Módosítsa a szakasz értékeit az `msalConfig` itt leírtak szerint:
>
> - `Enter_the_Application_Id_Here` a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** .
>
>    Az **alkalmazás (ügyfél) azonosító** értékének megkereséséhez nyissa meg a Azure Portal az alkalmazás regisztrációjának **Áttekintés** lapját.
> - `Enter_the_Cloud_Instance_Id_Here` Az Azure-felhő példánya. A fő vagy a globális Azure-felhőhöz írja be a következőt: `https://login.microsoftonline.com/` . Az **országos** felhők (például Kína) esetében lásd: [nemzeti felhők](authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here` értéke a következők egyike lehet:
>   - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére**. Például: `contoso.microsoft.com`.
>
>    A **címtár (bérlő) azonosító** értékének megkereséséhez nyissa meg az alkalmazás regisztrációjának **Áttekintés** lapját a Azure Portal.
>   - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `organizations` .
>   - Ha az alkalmazás a *szervezeti címtárban és a személyes Microsoft-fiókokban is támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `common` . Ehhez a rövid útmutatóhoz használja **a következőt:** `common` .
>   - A *személyes Microsoft-fiókok* támogatásának korlátozásához cserélje le ezt az értéket a következőre: `consumers` .
>
>    A **támogatott fióktípus** értékének megkereséséhez nyissa meg az alkalmazás regisztrációjának **Áttekintés** lapját a Azure Portal.
> - A `Enter_the_Redirect_Uri_Here` értéke `http://localhost:3000/`.
>
> `authority`Ha a fő (globális) Azure-felhőt használja, a *authConfig.jsban* a következőhöz hasonló értéknek kell megjelennie:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékeivel konfiguráltuk.

> [!div renderon="docs"]
>
> Görgessen le ugyanabban a fájlban, és frissítse a t `graphMeEndpoint` . 
> - Cserélje le a `Enter_the_Graph_Endpoint_Herev1.0/me` karakterláncot a `https://graph.microsoft.com/v1.0/me`
> - `Enter_the_Graph_Endpoint_Herev1.0/me` a végpont az API-hívásokat fogja elvégezni. A fő (globális) Microsoft Graph API szolgáltatásnál adja meg a `https://graph.microsoft.com/` következőt: (a záró továbbítási perjelet is beleértve). További információért lásd a [dokumentációt](/graph/deployments).
>
>
>
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>    export const graphConfig = {
>        graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me"
>    };
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
1. Nyissa meg a következő címet: `http://localhost:3000/`.

1. Válassza a **Bejelentkezés** lehetőséget a bejelentkezési folyamat elindításához, majd hívja meg a Microsoft Graph API-t.

    Amikor először jelentkezik be, a rendszer arra kéri, hogy adja meg az engedélyt, hogy az alkalmazás hozzáférhessen a profiljához, és jelentkezzen be. Miután sikeresen bejelentkezett, kattintson a profil **adatainak megkeresése** lehetőségre a profil adatainak a lapon való megjelenítéséhez.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése

![Egy egyoldalas alkalmazás engedélyezési kódjának folyamatát ábrázoló diagram.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

A MSAL.js kódtár bejelentkezik a felhasználók számára, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kéri.

Ha Node.js van telepítve, a legújabb verziót a Node.js Package Manager (NPM) segítségével töltheti le:

```console
npm install @azure/msal-browser @azure/msal-react
```

## <a name="next-steps"></a>Következő lépések

Az Auth Code flow alkalmazás Vanilla JavaScript használatával történő létrehozásával kapcsolatos részletes útmutatóért tekintse meg a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag a bejelentkezéshez és az MS Graph meghívásához](./tutorial-v2-javascript-auth-code.md)