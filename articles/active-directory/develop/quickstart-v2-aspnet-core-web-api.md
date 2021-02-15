---
title: 'Gyors útmutató: ASP.NET Core webes API-k biztosítása a Microsoft Identity platformmal | Azure'
titleSuffix: Microsoft identity platform
description: Ebben a rövid útmutatóban egy olyan mintakód letöltését és módosítását mutatja be, amely bemutatja, hogyan lehet védelemmel ellátni egy ASP.NET Core webes API-t az engedélyezéshez a Microsoft Identity platform használatával.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/22/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: da53d6bad790e6b204fa2a2b045e7bfdd83e0cc9
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102529"
---
# <a name="quickstart-protect-an-aspnet-core-web-api-with-microsoft-identity-platform"></a>Gyors útmutató: ASP.NET Core webes API-k védetté helyezése a Microsoft Identity platformmal

Ebben a rövid útmutatóban letölt egy ASP.NET Core webes API-kód mintát, és áttekinti a kódját, amely korlátozza az erőforrásokhoz való hozzáférést csak a jogosult fiókok számára. A minta támogatja a személyes Microsoft-fiókok és-fiókok engedélyezését bármely Azure Active Directory (Azure AD) szervezeten belül.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Előfeltételek
>
> - Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> - [Azure Active Directory bérlő](quickstart-create-new-tenant.md)
> - [.NET Core SDK 3.1 +](https://dotnet.microsoft.com/)
> - [Visual studio 2019](https://visualstudio.microsoft.com/vs/) vagy [Visual Studio Code](https://code.visualstudio.com/)
>
> ## <a name="step-1-register-the-application"></a>1. lépés: az alkalmazás regisztrálása
>
> Először regisztrálja a webes API-t az Azure AD-bérlőben, és adjon hozzá egy hatókört a következő lépésekkel:
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** , például: `AspNetCoreWebApi-Quickstart` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. A **kezelés** területen válassza **az API**  >  **hozzáadása hatókör** lehetőséget. Fogadja el az alapértelmezett **alkalmazás-azonosító URI** -t a **Mentés és folytatás** lehetőség kiválasztásával, és adja meg a következő adatokat:
>    - **Hatókör neve**: `access_as_user`
>    - **Kik tudnak beleegyezni?**: **rendszergazdák és felhasználók**
>    - **Rendszergazdai engedély megjelenítendő neve**: `Access AspNetCoreWebApi-Quickstart`
>    - **Rendszergazdai engedély leírása**: `Allows the app to access AspNetCoreWebApi-Quickstart as the signed-in user.`
>    - **Felhasználói beleegyező megjelenítendő név**: `Access AspNetCoreWebApi-Quickstart`
>    - **Felhasználói beleegyezett Leírás**: `Allow the application to access AspNetCoreWebApi-Quickstart on your behalf.`
>    - **Állapot**: **engedélyezve**
> 1. A hatókör hozzáadásának befejezéséhez válassza a **hatókör hozzáadása** elemet.

## <a name="step-2-download-the-aspnet-core-project"></a>2. lépés: a ASP.NET Core-projekt letöltése

> [!div renderon="docs"]
> [Töltse le a ASP.net Core megoldást](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/archive/aspnetcore3-1.zip) a githubról.

> [!div renderon="docs"]
> ## <a name="step-3-configure-the-aspnet-core-project"></a>3. lépés: a ASP.NET Core projekt konfigurálása
>
> Ebben a lépésben úgy konfigurálja a mintakód-t, hogy működjön a korábban létrehozott alkalmazás-regisztrációval.
>
> 1. Bontsa ki a. zip archívumot a meghajtó gyökeréhez közeli mappába. Például a *C:\Azure-Samples*.
> 1. Nyissa meg a megoldást a Kódszerkesztő *webapi* mappájában.
> 1. Nyissa meg a *appsettings.js* fájlt, és módosítsa a következőket:
>
>    ```json
>    "ClientId": "Enter_the_Application_Id_here",
>    "TenantId": "Enter_the_Tenant_Info_Here"
>    ```
>
>    - A helyére írja `Enter_the_Application_Id_here` be a Azure Portalban regisztrált alkalmazás **alkalmazás (ügyfél) azonosítóját** . Az **alkalmazás (ügyfél) azonosítóját** az alkalmazás **Áttekintés** oldalán találja.
>    - Cserélje le az értékét `Enter_the_Tenant_Info_Here` a következők egyikére:
>       - Ha az alkalmazás **csak ebben a szervezeti könyvtárban támogatja a fiókokat**, cserélje le ezt az értéket a **könyvtár (bérlő) azonosítójával** (GUID) vagy a **bérlő nevével** (például `contoso.onmicrosoft.com` ). A **címtár (bérlő) azonosítója** az alkalmazás **Áttekintés** lapján található.
>       - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot** támogat, ezt az értéket az `organizations` értékre cserélje le
>       - Ha az alkalmazás támogatja az **összes Microsoft-fiók felhasználót**, hagyja ezt az értéket `common`
>
> Ebben a rövid útmutatóban ne módosítsa a fájl *appsettings.js* egyéb értékeit.

## <a name="how-the-sample-works"></a>A minta működése

A webes API egy ügyfélalkalmazás tokenjét fogadja, és a webes API kódja érvényesíti a jogkivonatot. Ezt a forgatókönyvet a következő forgatókönyvben részletesebben ismertetjük [: védett webes API](scenario-protected-web-api-overview.md).

### <a name="startup-class"></a>Indítási osztály

A *Microsoft. AspNetCore. Authentication* middleware olyan `Startup` osztályt használ, amely a üzemeltetési folyamat inicializálásakor fut. A `ConfigureServices` metódusában a `AddMicrosoftIdentityWebApi` *Microsoft. Identity. Web* által biztosított bővítményi módszert nevezik.

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");
    }
```

A `AddAuthentication()` metódus konfigurálja a szolgáltatást JwtBearer-alapú hitelesítés hozzáadására.

A sort tartalmazó vonal `.AddMicrosoftIdentityWebApi` hozzáadja a Microsoft Identity platform engedélyezését a webes API-hoz. Ezután úgy van konfigurálva, hogy érvényesítse a Microsoft Identity platform által kiadott hozzáférési jogkivonatokat a `AzureAD` konfigurációs fájl *appsettings.js* szakaszában található információk alapján:

| *appsettings.jsa* kulcson | Description                                                                                                                                                          |
|------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ClientId`             | A Azure Portalban regisztrált alkalmazás **alkalmazás-(ügyfél-) azonosítója** .                                                                                       |
| `Instance`             | A hitelesítéshez használt biztonságijogkivonat-szolgáltatás (STS) végpontja. Ez az érték általában `https://login.microsoftonline.com/` Az Azure nyilvános felhőre utal. |
| `TenantId`             | A bérlő vagy a bérlői azonosító (GUID) neve, *vagy a felhasználók* munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal való bejelentkezéséhez.                             |

A `Configure()` metódus két fontos módszert tartalmaz, `app.UseAuthentication()` `app.UseAuthorization()` amelyek lehetővé teszik a megnevezett funkcióik használatát:

```csharp
// This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // more code
    app.UseAuthentication();
    app.UseAuthorization();
    // more code
}
```

### <a name="protect-a-controller-a-controllers-method-or-a-razor-page"></a>Vezérlő, vezérlő metódusa vagy borotva oldalának védetté

A vezérlő vagy vezérlő metódusait az attribútum használatával lehet védelemmel ellátni `[Authorize]` . Ez az attribútum csak a hitelesített felhasználók engedélyezésével korlátozza a vezérlőhöz vagy a metódusokhoz való hozzáférést, ami azt jelenti, hogy a hitelesítési kérdés elindítható a vezérlőhöz, ha a felhasználó nem hitelesítve van.

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
```

### <a name="validate-the-scope-in-the-controller"></a>A hatókör ellenőrzése a vezérlőben

A kód az API-ban azt ellenőrzi, hogy a szükséges hatókörök szerepelnek-e a jogkivonatban a használatával `HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);`

```csharp
namespace webapi.Controllers
{
    [Authorize]
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        // The Web API will only accept tokens 1) for users, and 2) having the "access_as_user" scope for this API
        static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

        [HttpGet]
        public IEnumerable<WeatherForecast> Get()
        {
            HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

            // some code here
        }
    }
}
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

A ASP.NET Core web API code minta részét képező GitHub-tárház útmutatást és további kódrészleteket tartalmaz, amelyek bemutatják a következőket:

- Hitelesítés hozzáadása új ASP.NET Core webes API-hoz
- A webes API meghívása asztali alkalmazásból
- Alsóbb rétegbeli API-k (például Microsoft Graph és más Microsoft API-k)

> [!div class="nextstepaction"]
> [Webes API-oktatóanyagok ASP.NET Core a GitHubon](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2)
