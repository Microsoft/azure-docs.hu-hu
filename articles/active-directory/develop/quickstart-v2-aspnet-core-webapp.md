---
title: 'Gyors útmutató: bejelentkezés felvétele a Microsofttal egy ASP.NET Core webalkalmazásba | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan valósítja meg egy alkalmazás a Microsoft-bejelentkezést egy ASP.NET Core webalkalmazásban az OpenID Connect használatával
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/11/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: d0d3154d123b5e073a4eadf976d5259d51972da8
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436481"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-core-web-app"></a>Gyors útmutató: bejelentkezés felvétele a Microsofttal ASP.NET Core webalkalmazásba

Ebben a rövid útmutatóban egy kódrészletet tölt le és futtat, amely bemutatja, hogyan jelentkezhet be ASP.NET Core webalkalmazás a felhasználók bármelyik Azure Active Directory (Azure AD) szervezetből.  

> [!div renderon="docs"]
> Az alábbi ábrán a minta alkalmazás működése látható:
>
> ![A webböngésző, a webalkalmazás és a Microsoft Identity platform közötti interakció diagramja a minta alkalmazásban.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)
>
> ## <a name="prerequisites"></a>Előfeltételek
>
> * [Visual studio 2019](https://visualstudio.microsoft.com/vs/) vagy [Visual Studio Code](https://code.visualstudio.com/)
> * [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/download)
>
> ## <a name="register-and-download-the-app"></a>Az alkalmazás regisztrálása és letöltése
> Az alkalmazás összeállításának megkezdéséhez két lehetőség közül választhat: automatikus vagy manuális konfiguráció.
>
> ### <a name="automatic-configuration"></a>Automatikus konfiguráció
> Ha szeretné automatikusan konfigurálni az alkalmazást, majd töltse le a kód mintát, kövesse az alábbi lépéseket:
>
> 1. Lépjen a <a href="https://aka.ms/aspnetcore2-1-aad-quickstart-v2/" target="_blank">Azure Portal lapra az alkalmazás regisztrálásához</a>.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Az új alkalmazás egyetlen kattintással való letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
>
> ### <a name="manual-configuration"></a>Manuális konfigurálás
> Ha manuálisan szeretné konfigurálni az alkalmazás és a kód mintát, kövesse az alábbi eljárásokat.
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kívánja az alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. A **név** mezőben adja meg az alkalmazás nevét. Adja meg például a **AspNetCore-Gyorsindítás** értéket. Az alkalmazás felhasználói ezt a nevet fogják látni, és később is megváltoztathatók.
> 1. Az **átirányítási URI** mezőbe írja be a következőt: **https://localhost:44321/signin-oidc** .
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
> 1. Az **előtérben kijelentkezési URL-cím** mezőbe írja be a következőt: **https://localhost:44321/signout-oidc** .
> 1. Az **implicit engedélyezés és a hibrid folyamatok** területen válassza az **azonosító tokenek** lehetőséget.
> 1. Kattintson a **Mentés** gombra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> A jelen rövid útmutatóban a következő működéshez:
> - **Átirányítási URI** esetén írja be a következőt: **https://localhost:44321/** és **https://localhost:44321/signin-oidc** .
> - Az **előtérben kijelentkezési URL-cím** mezőbe írja be a következőt: **https://localhost:44321/signout-oidc** . 
>
> Az engedélyezési végpont a kérelmek AZONOSÍTÓjának jogkivonatait fogja kibocsátani.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-aspnet-core-project"></a>2. lépés: a ASP.NET Core-projekt letöltése

> [!div renderon="docs"]
> [A ASP.NET Core-megoldás letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div renderon="portal" class="sxs-lookup"]
> Futtassa a projektet.

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/archive/aspnetcore3-1.zip)

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékével konfiguráltuk, és készen áll a futtatásra.
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
> [!div renderon="docs"]
> #### <a name="step-3-configure-your-aspnet-core-project"></a>3. lépés: a ASP.NET Core projekt konfigurálása
> 1. Bontsa ki a. zip-archívumot egy helyi mappába a meghajtó gyökerének közelében. Bontsa ki például a következőt: *C:\Azure-Samples*.
> 
>    Azt javasoljuk, hogy az archívumot a meghajtó gyökerének közelében egy könyvtárba csomagolja, hogy elkerülje a Windowson a Path Length korlátozásai által okozott hibákat.
> 1. Nyissa meg a megoldást a Visual Studio 2019-ben.
> 1. Nyissa meg a *appsettings.js* fájlt, és módosítsa a következő kódot:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "common",
>    ```
>
>    - A helyére írja `Enter_the_Application_Id_here` be a Azure Portalban regisztrált alkalmazás alkalmazás-(ügyfél-) azonosítóját. Az **alkalmazás (ügyfél) azonosítójának** értéke az alkalmazás **Áttekintés** lapján található.
>    - Cserélje le az értékét `common` a következők egyikére:
>       - Ha az alkalmazás **csak ebben a szervezeti könyvtárban támogatja a fiókokat**, cserélje le ezt az értéket a könyvtár (bérlő) azonosítójával (GUID) vagy a bérlő nevével (például `contoso.onmicrosoft.com` ). A **címtár (bérlő) azonosítójának** értéke az alkalmazás **Áttekintés** lapján található.
>       - Ha az alkalmazás **minden szervezeti címtárban támogatja a fiókokat**, cserélje le ezt az értéket a következőre: `organizations` .
>       - Ha az alkalmazás támogatja az **összes Microsoft-fiók felhasználót**, hagyja meg ezt az értéket `common` .
>
> Ebben a rövid útmutatóban ne módosítsa a fájl *appsettings.js* egyéb értékeit.
>
> #### <a name="step-4-build-and-run-the-application"></a>4. lépés: Az alkalmazás fordítása és futtatása
>
> Az alkalmazás a Visual Studióban történő létrehozásához és futtatásához válassza a **hibakeresés menüt >** a **hibakeresés indítása** lehetőséget, vagy nyomja le az F5 billentyűt.
>
> A rendszer kéri a hitelesítő adatait, majd megkéri, hogy adja meg az alkalmazás által igényelt engedélyeket. Válassza az **elfogadás** lehetőséget a beleegyezés kérése lapon.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-01-consent.png" alt-text="Képernyőkép a belefoglalási párbeszédpanelről, amelyen az alkalmazás által a felhasználótól kért engedélyek láthatók.":::
>
> Miután jóváhagyta a kért engedélyeket, az alkalmazás azt jeleníti meg, hogy sikeresen bejelentkezett a Azure Active Directory hitelesítő adataival.
>
> :::image type="content" source="media/quickstart-v2-aspnet-core-webapp/webapp-02-signed-in.png" alt-text="Képernyőkép a futó webalkalmazást és a bejelentkezett felhasználót bemutató böngészőről.":::

## <a name="more-information"></a>További információ

Ez a szakasz áttekintést nyújt a felhasználók bejelentkezéséhez szükséges kódokról. Ez az Áttekintés hasznos lehet a kód működésének megértéséhez, a fő argumentumok megismeréséhez és a bejelentkezés meglévő ASP.NET Core alkalmazáshoz való hozzáadásához.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>A minta működése
>
> ![A webböngésző, a webalkalmazás és a Microsoft Identity platform közötti interakció diagramja a minta alkalmazásban.](media/quickstart-v2-aspnet-core-webapp/aspnetcorewebapp-intro.svg)

### <a name="startup-class"></a>Indítási osztály

A *Microsoft. AspNetCore. Authentication* middleware a `Startup` üzemeltetési folyamat indításakor futtatott osztályt használ:

```csharp
  public void ConfigureServices(IServiceCollection services)
  {
      services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"));

      services.AddControllersWithViews(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
          options.Filters.Add(new AuthorizeFilter(policy));
      });
      services.AddRazorPages()
          .AddMicrosoftIdentityUI();
  }
```

A `AddAuthentication()` metódus egy cookie-alapú hitelesítés hozzáadására konfigurálja a szolgáltatást. Ez a hitelesítés a böngészőbeli forgatókönyvekben használható, és az OpenID Connect kihívásának beállítására szolgál.

A sor, amely tartalmazza a `.AddMicrosoftIdentityWebApp` Microsoft Identity platform hitelesítését az alkalmazáshoz. Ezután az alkalmazás a következő információk alapján lesz konfigurálva a felhasználók bejelentkezéséhez a `AzureAD` konfigurációs fájl *appsettings.js* szakaszában:

| *appsettings.jsa* kulcson | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | A Azure Portalban regisztrált alkalmazás alkalmazás-(ügyfél-) azonosítója.                                                                                       |
| `Instance`             | A hitelesítéshez használt biztonságijogkivonat-szolgáltatás (STS) végpontja. Ez az érték általában `https://login.microsoftonline.com/` Az Azure nyilvános felhőre utal. |
| `TenantId`             | A bérlő vagy a bérlői azonosító (GUID) neve, vagy `common` munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal rendelkező felhasználók bejelentkezni.                             |

A `Configure()` metódus két fontos módszert tartalmaz, `app.UseAuthentication()` `app.UseAuthorization()` amelyek lehetővé teszik a megnevezett funkciókat. Emellett a `Configure()` metódusban regisztrálnia kell a Microsoft Identity web Routes-t legalább egy hívással `endpoints.MapControllerRoute()` vagy a következő hívásával `endpoints.MapControllers()` :

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

### <a name="attribute-for-protecting-a-controller-or-methods"></a>Vezérlő vagy metódusok védelmét szolgáló attribútum

A vezérlőt vagy a vezérlő metódusait az attribútum használatával lehet védelemmel ellátni `[Authorize]` . Ez az attribútum csak a hitelesített felhasználók engedélyezésével korlátozza a vezérlőhöz vagy a metódusokhoz való hozzáférést. Ezután egy hitelesítési kérdés elindítható a vezérlő eléréséhez, ha a felhasználó nem hitelesítve van.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

A ASP.NET Core oktatóanyagot tartalmazó GitHub-tárház útmutatást és további kódrészleteket tartalmaz, amelyek az alábbiakat mutatják be:

- Hitelesítés hozzáadása új ASP.NET Core webalkalmazáshoz.
- Hívjon Microsoft Graph, más Microsoft API-kat vagy a saját webes API-kat.
- Adja hozzá az engedélyt.
- Jelentkezzen be a felhasználókba az országos felhőkben vagy a közösségi identitásokkal.

> [!div class="nextstepaction"]
> [Webalkalmazás-oktatóanyagok ASP.NET Core a GitHubon](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/)
