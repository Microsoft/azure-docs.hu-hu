---
title: 'Gyors útmutató: bejelentkezés felvétele a Microsofttal egy ASP.NET webalkalmazásba | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan implementálhat Microsoft-bejelentkezést egy ASP.NET-webalkalmazásban az OpenID Connect használatával.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET, contperf-fy21q1
ms.openlocfilehash: 87948ed04f7b50820d94993d4c4fbcf2dfd94b31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578685"
---
# <a name="quickstart-add-microsoft-identity-platform-sign-in-to-an-aspnet-web-app"></a>Gyors útmutató: Microsoft Identity platform-bejelentkezés hozzáadása egy ASP.NET-webalkalmazáshoz

Ebben a rövid útmutatóban egy kódrészletet tölt le és futtat, amely bemutatja, hogy egy ASP.NET-webalkalmazás hogyan tud bejelentkezni a felhasználók bármelyik Azure Active Directory (Azure AD) szervezetből. 

> [!div renderon="docs"]
> Az alábbi ábrán a minta alkalmazás működése látható:
>
> ![A webböngésző, a webalkalmazás és a Microsoft Identity platform közötti interakció diagramja a minta alkalmazásban.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Előfeltételek
>
> * Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
> * [.NET-keretrendszer 4.7.2 +](https://dotnet.microsoft.com/download/visual-studio-sdks)
>
> ## <a name="register-and-download-the-app"></a>Az alkalmazás regisztrálása és letöltése
> Az alkalmazás összeállításának megkezdéséhez két lehetőség közül választhat: automatikus vagy manuális konfiguráció.
>
> ### <a name="automatic-configuration"></a>Automatikus konfiguráció
> Ha szeretné automatikusan konfigurálni az alkalmazást, majd töltse le a kód mintát, kövesse az alábbi lépéseket:
>
> 1. Lépjen a <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs" target="_blank">Azure Portal lapra az alkalmazás regisztrálásához</a>.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Az új alkalmazás egyetlen kattintással való letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
>
> ### <a name="manual-configuration"></a>Manuális konfigurálás
> Ha manuálisan szeretné konfigurálni az alkalmazás és a kód mintát, kövesse az alábbi eljárásokat.
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kívánja az alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. A **név** mezőben adja meg az alkalmazás nevét. Adja meg például az **ASPNET-Gyorsindítás** értéket. Az alkalmazás felhasználói ezt a nevet fogják látni, és később is megváltoztathatók.
> 1. Adja hozzá **https://localhost:44368/** az **ÁTirányítási URI**-t, majd válassza a **regisztráció** lehetőséget.
> 1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
> 1. Az **implicit engedélyezés és a hibrid folyamatok** szakaszban válassza az **azonosító tokenek** elemet.
> 1. Kattintson a **Mentés** gombra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy az ebben a rövid útmutatóban szereplő kód minta működjön, adja meg **https://localhost:44368/** az **átirányítási URI**-t.
>
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Az alkalmazás már konfigurálva ](media/quickstart-v2-aspnet-webapp/green-check.png) van ezzel az attribútummal.

#### <a name="step-2-download-the-project"></a>2. lépés: A projekt letöltése

> [!div renderon="docs"]
> [A Visual Studio 2019-megoldás letöltése](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Futtassa a projektet a Visual Studio 2019 használatával.
> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [A mintakód letöltése](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projekt az alkalmazás tulajdonságainak értékeivel lett konfigurálva.

> [!div renderon="docs"]
> #### <a name="step-3-run-your-visual-studio-project"></a>3. lépés: a Visual Studio-projekt futtatása

1. Bontsa ki a. zip fájlt egy helyi mappába, amely a gyökérmappa közelébe van. Bontsa ki például a következőt: *C:\Azure-Samples*.
   
   Azt javasoljuk, hogy az archívumot a meghajtó gyökerének közelében egy könyvtárba csomagolja, hogy elkerülje a Windowson a Path Length korlátozásai által okozott hibákat.
2. Nyissa meg a megoldást a Visual Studióban (*AppModelv2-WebApp-OpenIDConnect-DotNet. SLN*).
3. A Visual Studio verziójától függően előfordulhat, hogy a jobb gombbal a **AppModelv2-WebApp-OpenIDConnect-DotNet** projektre kell kattintania, majd válassza a **NuGet-csomagok visszaállítása** lehetőséget.
4. Nyissa meg a csomagkezelő konzolt a   >  **más Windows**  >  **Package Manager-konzol** megtekintése lehetőség kiválasztásával. Ez után futtassa a `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r` parancsot.

> [!div renderon="docs"]
> 5. Szerkessze *Web.config* , és cserélje le a paramétereket, a és a paramétert `ClientId` `Tenant` `redirectUri` :
>    ```xml
>    <add key="ClientId" value="Enter_the_Application_Id_here" />
>    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
>    <add key="redirectUri" value="https://localhost:44368/" />
>    ```
>    Ebben a kódban:
>
>    - `Enter_the_Application_Id_here` a korábban létrehozott alkalmazás-regisztráció (ügyfél) azonosítója. Keresse meg az alkalmazás (ügyfél) AZONOSÍTÓját az Azure Portal **Alkalmazásregisztrációk** alkalmazás **Áttekintés** lapján.
>    - `Enter_the_Tenant_Info_Here` a következő lehetőségek egyike:
>      - Ha az alkalmazás **csak a saját szervezetet** támogatja, cserélje le ezt az értéket a könyvtár (bérlő) vagy a bérlő nevére (például: `contoso.onmicrosoft.com` ). Keresse meg a könyvtár (bérlő) AZONOSÍTÓját az alkalmazás **Áttekintés** lapján **Alkalmazásregisztrációk** a Azure Portalban.
>      - Ha az alkalmazás **minden szervezeti címtárban támogatja a fiókokat**, cserélje le ezt az értéket a következőre: `organizations` .
>      - Ha az alkalmazás támogatja az **összes Microsoft-fiók felhasználót**, cserélje le ezt az értéket a következőre: `common` .
>    - `redirectUri` az **átirányítási URI** , amelyet korábban adott meg a Azure Portal **Alkalmazásregisztrációkban** .
>

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

## <a name="more-information"></a>További információ

Ez a szakasz áttekintést nyújt a felhasználók bejelentkezéséhez szükséges kódokról. Ez az Áttekintés hasznos lehet a kód működésének megértéséhez, a fő argumentumok megismeréséhez és a bejelentkezés meglévő ASP.NET-alkalmazáshoz való hozzáadásához.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>A minta működése
>
> ![A webböngésző, a webalkalmazás és a Microsoft Identity platform közötti interakció diagramja a minta alkalmazásban.](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

### <a name="owin-middleware-nuget-packages"></a>OWIN közbenső NuGet-csomagok

A hitelesítési folyamatot cookie-alapú hitelesítéssel is beállíthatja, ha OpenID connectet használ a ASP.NET-ben a OWIN middleware-csomagokkal. Ezeket a csomagokat a Visual Studióban a következő parancsok futtatásával telepítheti a Package Manager konzolon:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="owin-startup-class"></a>OWIN indítási osztálya

A OWIN middleware egy *indítási osztályt* használ, amely a üzemeltetési folyamat indításakor fut. Ebben a rövid útmutatóban a *Startup. cs* fájl a gyökérkönyvtárban található. A következő kód a rövid útmutató által használt paramétereket mutatja be:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the client ID, authority, and redirect URI as obtained from Web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it's using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the code id_token, which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.CodeIdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organization, set ValidateIssuer to true and the 'tenant' setting in Web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use the ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to the OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Ahol  | Leírás |
> |---------|---------|
> | `ClientId`     | Az alkalmazás azonosítója a Azure Portalban regisztrált alkalmazásban. |
> | `Authority`    | A hitelesítéshez a felhasználóhoz tartozó biztonságijogkivonat-szolgáltatás (STS) végpontja. Általában `https://login.microsoftonline.com/{tenant}/v2.0` a nyilvános felhőben. Ebben az URL-címben a (z) *{bérlő}* a bérlő neve, a bérlő azonosítója vagy a `common` közös végpontra mutató hivatkozás. (A közös végpont a több-bérlős alkalmazásokhoz használatos.) |
> | `RedirectUri`  | Az URL-cím, amelyben a felhasználók a Microsoft Identity platform hitelesítése után továbbítódnak. |
> | `PostLogoutRedirectUri`     | Az URL-cím, amelyben a felhasználók a kijelentkezés után továbbítódnak. |
> | `Scope`     | A kért hatókörök listája szóközökkel elválasztva. |
> | `ResponseType`     | A hitelesítéstől kapott válasz egy engedélyezési kódot és egy azonosító jogkivonatot tartalmaz. |
> | `TokenValidationParameters`     | A jogkivonatok érvényesítéséhez használatos paraméterek listája. Ebben az esetben úgy `ValidateIssuer` van beállítva, hogy `false` jelezze, hogy fogadja-e a bejelentkezéseket a személyes, munkahelyi vagy iskolai fiók típusától. |
> | `Notifications`     | Az üzeneteken futtatható Delegálók listája `OpenIdConnect` . |


> [!NOTE]
> A beállítás a rövid útmutató `ValidateIssuer = false` egyszerűsítése. A valós alkalmazásokban ellenőrizze a kiállítót. Ennek megértéséhez tekintse meg a mintákat.

### <a name="authentication-challenge"></a>Hitelesítési kérdés

Előírhatja a felhasználóknak a bejelentkezést, ha a vezérlőben egy hitelesítő kérdést kezdeményez:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> Ha ezt a módszert használja, a hitelesítési kérdés megadása nem kötelező. Általában akkor érdemes használni, ha azt szeretné, hogy a nézet elérhető legyen a hitelesített és a nem hitelesített felhasználóktól. Másik megoldásként a vezérlők is védhetők a következő szakaszban leírt módon.

### <a name="attribute-for-protecting-a-controller-or-a-controller-actions"></a>Vezérlő vagy vezérlő műveleteinek védelmére szolgáló attribútum

Az attribútum használatával védelemmel láthat el egy vezérlőt vagy vezérlő műveleteket `[Authorize]` . Ez az attribútum korlátozza a vezérlőhöz vagy műveletekhez való hozzáférést úgy, hogy csak a hitelesített felhasználók férhetnek hozzá a vezérlő műveleteihez. A hitelesítési kihívás ezután automatikusan megtörténik, amikor egy nem hitelesített felhasználó megpróbál hozzáférni az attribútum által díszített egyik művelethez vagy vezérlőhöz `[Authorize]` .

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

Az alkalmazások és az új funkciók létrehozásával kapcsolatos részletes útmutatót, beleértve a gyors útmutató részletes ismertetését, próbálja ki a ASP.NET-oktatóanyagot.

> [!div class="nextstepaction"]
> [Bejelentkezés hozzáadása egy ASP.NET-webalkalmazáshoz](tutorial-v2-asp-webapp.md)
