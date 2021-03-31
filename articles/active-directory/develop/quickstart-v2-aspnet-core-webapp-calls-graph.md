---
title: 'Gyors útmutató: ASP.NET Core webalkalmazás, amely bejelentkezik a felhasználók és a hívások között Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan használja az alkalmazás a Microsoft. Identity. Web eszközt, hogy a Microsoft-bejelentkezést az OpenID Connect és calls használatával ASP.NET Core webalkalmazásban implementálja Microsoft Graph
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/10/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: efa9465adc13b50e6ae12628d21347152c3fc2c0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578719"
---
# <a name="quickstart-aspnet-core-web-app-that-signs-in-users-and-calls-microsoft-graph-on-their-behalf"></a>Gyors útmutató: ASP.NET Core webalkalmazás, amely a felhasználók nevében jelentkezik be, és Microsoft Graph

Ebben a rövid útmutatóban egy olyan kódrészletet tölt le és futtat, amely bemutatja, hogyan jelentkezhet be ASP.NET Core webalkalmazás a felhasználók számára bármely Azure Active Directory (Azure AD) szervezetből, és meghívja Microsoft Graph.  

Nézze meg, [Hogyan működik a minta](#how-the-sample-works) egy ábrán.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Előfeltételek
>
> * [Visual studio 2019](https://visualstudio.microsoft.com/vs/) vagy [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-quickstart-app"></a>A Gyorsindítás alkalmazás regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Expressz] [1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Kézi] [2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Lépjen a <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetCoreWebAppQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Alkalmazásregisztrációk</a> gyors üzembe helyezési élményhez.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** , például: `AspNetCoreWebAppCallsGraph-Quickstart` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. Adja meg a **átirányítási URI-ját** `https://localhost:44321/signin-oidc` .
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
> 1. Adja meg az **előtérben kijelentkezési URL-címet** `https://localhost:44321/signout-oidc` .
> 1. Kattintson a **Mentés** gombra.
> 1. A **kezelés** területen válassza a **tanúsítványok & titkos kulcsok**  >  **új ügyfél titka** lehetőséget.
> 1. Adjon meg egy **leírást**, például: `clientsecret1` .
> 1. A titkos kulcs lejáratához válasszon **1 évet** .
> 1. Válassza a **Hozzáadás** lehetőséget, és azonnal jegyezze fel a titkos kulcs **értékét** egy későbbi lépésben való használatra. A titkos érték *soha nem jelenik meg újra* , és bármilyen más módon visszafordíthatatlan. Rögzítse biztonságos helyen, ahogy azt bármilyen jelszóval tenné.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy az ebben a rövid útmutatóban szereplő kód minta működjön, adjon hozzá egy **átirányítási URI** - `https://localhost:44321/signin-oidc` t és egy **előcsatornán belüli kijelentkezési URL-címet** az `https://localhost:44321/signout-oidc` alkalmazás regisztrálásához.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-aspnet-core-project"></a>2. lépés: a ASP.NET Core-projekt letöltése

> [!div renderon="docs"]
> [A ASP.NET Core-megoldás letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Futtassa a projektet.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1-callsgraph.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékével konfiguráltuk, és készen áll a futtatásra.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>3. lépés: a ASP.NET Core projekt konfigurálása
> 1. Bontsa ki a. zip-archívumot egy helyi mappába a meghajtó gyökerének közelében. Például a *C:\Azure-Samples*.
> 1. Nyissa meg a megoldást a Visual Studio 2019-ben.
> 1. Nyissa meg a *appsettings.js* fájlt, és módosítsa a következőket:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    "clientSecret": "Enter_the_Client_Secret_Here"
>    ```
>
>    - A helyére írja `Enter_the_Application_Id_here` be a Azure Portalban regisztrált alkalmazás **alkalmazás (ügyfél) azonosítóját** . Az **alkalmazás (ügyfél) azonosítóját** az alkalmazás **Áttekintés** oldalán találja.
>    - Cserélje le az értékét `common` a következők egyikére:
>       - Ha az alkalmazás **csak ebben a szervezeti könyvtárban támogatja a fiókokat**, cserélje le ezt az értéket a **könyvtár (bérlő) azonosítójával** (GUID) vagy a **bérlő nevével** (például `contoso.onmicrosoft.com` ). A **címtár (bérlő) azonosítója** az alkalmazás **Áttekintés** lapján található.
>       - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot** támogat, ezt az értéket az `organizations` értékre cserélje le
>       - Ha az alkalmazás támogatja az **összes Microsoft-fiók felhasználót**, hagyja ezt az értéket `common`
>    - Cserélje le `Enter_the_Client_Secret_Here` a helyére a létrehozott és egy korábbi lépésben rögzített **ügyfél-titkos kulcsot** .
> 
> Ebben a rövid útmutatóban ne módosítsa a fájl *appsettings.js* egyéb értékeit.
>
> #### <a name="step-4-build-and-run-the-application"></a>4. lépés: Az alkalmazás fordítása és futtatása
>
> Az alkalmazás a Visual Studióban történő létrehozásához és futtatásához válassza a **hibakeresés menüt >** **hibakeresés indítása** vagy a kulcs megnyomásával `F5` .
>
> A rendszer kéri a hitelesítő adatait, majd megkéri, hogy adja meg az alkalmazáshoz szükséges engedélyeket. Válassza az **elfogadás** lehetőséget a beleegyezés kérése lapon.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-01-consent.png" alt-text="Az alkalmazás által az > felhasználótól kért engedélyeket megjelenítő belefoglalási párbeszédpanel":::
>
> A kért engedélyek beleegyezése után az alkalmazás azt jeleníti meg, hogy sikeresen bejelentkezett a Azure Active Directory hitelesítő adataival, és megtekintheti az e-mail-címét az oldal "API-eredmény" szakaszában. Ezt Microsoft Graph használatával extraháljuk.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp-calls-graph/webapp-02-signed-in.png" alt-text="Webböngésző, amely megjeleníti a futó webalkalmazást és a bejelentkezett felhasználót":::

## <a name="about-the-code"></a>A kód ismertetése

Ez a szakasz áttekintést nyújt a felhasználóknak való bejelentkezéshez szükséges kódokról, és meghívja a Microsoft Graph API-t a nevükben. Ez az Áttekintés hasznos lehet a kód működésének, a fő argumentumok, valamint a meglévő ASP.NET Core alkalmazásba való bejelentkezéshez és a Microsoft Graph hívásához. A [Microsoft. Identity. Web](microsoft-identity-web.md)szolgáltatást használja, amely a [MSAL.net](msal-overview.md)körüli burkoló.

### <a name="how-the-sample-works"></a>A minta működése
![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-aspnet-core-webapp-calls-graph/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Indítási osztály

A *Microsoft. AspNetCore. Authentication* middleware olyan `Startup` osztályt használ, amely a üzemeltetési folyamat inicializálásakor fut:

```csharp
  // Get the scopes from the configuration (appsettings.json)
  var initialScopes = Configuration.GetValue<string>("DownstreamApi:Scopes")?.Split(' ');

  public void ConfigureServices(IServiceCollection services)
  {
      // Add sign-in with Microsoft
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))

            // Add the possibility of acquiring a token to call a protected web API
            .EnableTokenAcquisitionToCallDownstreamApi(initialScopes)

                // Enables controllers and pages to get GraphServiceClient by dependency injection
                // And use an in memory token cache
                .AddMicrosoftGraph(Configuration.GetSection("DownstreamApi"))
                .AddInMemoryTokenCaches();

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });

      // Enables a UI and controller for sign in and sign out.
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

A `AddAuthentication()` metódus úgy konfigurálja a szolgáltatást, hogy cookie-alapú hitelesítést adjon hozzá, amely a böngészőbeli forgatókönyvekben használatos, és az OpenID connectre vonatkozó kihívás beállítására szolgál.

A sort tartalmazó vonal `.AddMicrosoftIdentityWebApp` hozzáadja a Microsoft Identity platform hitelesítését az alkalmazáshoz. Ezt a [Microsoft. Identity. Web](microsoft-identity-web.md)biztosíthatja. Ezután be van állítva a Microsoft Identity platform használatával történő bejelentkezésre a `AzureAD` konfigurációs fájl *appsettings.js* szakaszában található információk alapján:

| *appsettings.jsa* kulcson | Leírás                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | A Azure Portalban regisztrált alkalmazás **alkalmazás-(ügyfél-) azonosítója** .                                                                                       |
| `Instance`             | A hitelesítéshez használt biztonságijogkivonat-szolgáltatás (STS) végpontja. Ez az érték általában `https://login.microsoftonline.com/` Az Azure nyilvános felhőre utal. |
| `TenantId`             | A bérlő vagy a bérlői azonosító (GUID) neve, *vagy a felhasználók* munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal való bejelentkezéséhez.                             |

A `EnableTokenAcquisitionToCallDownstreamApi` metódus lehetővé teszi, hogy az alkalmazás jogkivonatot szerezzen a védett webes API-k meghívásához. `AddMicrosoftGraph` lehetővé teszi, hogy a vezérlők vagy a borotva oldalai közvetlenül a `GraphServiceClient` (függőségi befecskendezés alapján) részesülhessenek, és a `AddInMemoryTokenCaches` metódusok lehetővé teszik az alkalmazás számára, hogy kihasználhassa a jogkivonat-gyorsítótárat.

A `Configure()` metódus két fontos módszert tartalmaz, `app.UseAuthentication()` `app.UseAuthorization()` amelyek lehetővé teszik a megnevezett funkciókat. Emellett a `Configure()` metódusban regisztrálnia kell a Microsoft Identity web útvonalait legalább egy hívással `endpoints.MapControllerRoute()` vagy egy hívással `endpoints.MapControllers()` .

```csharp
app.UseAuthentication();
app.UseAuthorization();

app.UseEndpoints(endpoints =>
{

    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
    endpoints.MapRazorPages();
});

// endpoints.MapControllers(); // REQUIRED if MapControllerRoute() isn't called.
```

### <a name="protect-a-controller-or-a-controllers-method"></a>Egy vezérlő vagy egy vezérlő metódusának védelme

A vezérlőt vagy annak metódusait a `[Authorize]` vezérlő osztályára, illetve egy vagy több metódusára alkalmazva is védelemmel láthatja el. Ez az `[Authorize]` attribútum csak hitelesített felhasználók engedélyezésével korlátozza a hozzáférést. Ha a felhasználó még nincs hitelesítve, megkezdheti a vezérlő elérését egy hitelesítési kérdéssel. Ebben a rövid útmutatóban a hatókörök a konfigurációs fájlból lesznek beolvasva:

```CSharp
[AuthorizeForScopes(ScopeKeySection = "DownstreamApi:Scopes")]
public async Task<IActionResult> Index()
{
    var user = await _graphServiceClient.Me.Request().GetAsync();
    ViewData["ApiResult"] = user.DisplayName;

    return View();
}
 ```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

Az ebben a rövid útmutatóban hivatkozott ASP.NET Core kódot tartalmazó GitHub-tárház útmutatást és további kódrészleteket tartalmaz, amelyek a következőket mutatják be:

- Hitelesítés hozzáadása új ASP.NET Core webalkalmazáshoz
- A Microsoft Graph, más Microsoft API-k vagy a saját webes API-k hívása
- Engedélyezés hozzáadása
- Bejelentkezés a felhasználók számára az országos felhőkben vagy közösségi identitásokkal

> [!div class="nextstepaction"]
> [Webalkalmazás-oktatóanyagok ASP.NET Core a GitHubon](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
