---
title: A funkciók lépcsőzetes bevezetésének engedélyezése a megcélzott célközönségek számára
titleSuffix: Azure App Configuration
description: Megtudhatja, hogyan engedélyezheti a funkciók lépcsőzetes bevezetését a célközönségek számára
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 11/20/2020
ms.openlocfilehash: c415eaeab2edd0a1b324bba4266266201cb50cbf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96929684"
---
# <a name="enable-staged-rollout-of-features-for-targeted-audiences"></a>A funkciók lépcsőzetes bevezetésének engedélyezése a megcélzott célközönségek számára

A funkciók jelzői lehetővé teszik az alkalmazás funkcióinak dinamikus aktiválását vagy inaktiválását. A szolgáltatási szűrők határozzák meg a szolgáltatás jelölője állapotát minden egyes kiértékeléskor. A `Microsoft.FeatureManagement` függvénytár tartalmazza a `TargetingFilter` szolgáltatást, amely lehetővé teszi a szolgáltatások jelölőjét a felhasználók és csoportok megadott listájához, vagy a felhasználók megadott százalékában. `TargetingFilter` "Sticky". Ez azt jelenti, hogy ha egy adott felhasználó megkapja a szolgáltatást, akkor az összes jövőbeli kérelem esetében továbbra is látni fogja ezt a szolgáltatást. A segítségével egy `TargetingFilter` bemutatóban engedélyezheti egy adott fiók funkcióját, hogy fokozatosan új szolgáltatásokat lehessen kibővíteni a különböző csoportokban vagy gyűrűkben lévő felhasználók számára, és még sok más.

Ebből a cikkből megtudhatja, hogyan hozhatja ki az új szolgáltatást egy ASP.NET Core webalkalmazásban a megadott felhasználók és csoportok számára az `TargetingFilter` Azure app Configuration használatával.

## <a name="create-a-web-application-with-feature-flags-and-authentication"></a>Webalkalmazás létrehozása szolgáltatás-jelzővel és-hitelesítéssel

A felhasználók és csoportok alapján történő szolgáltatások kifejlesztéséhez szüksége lesz egy webalkalmazásra, amely lehetővé teszi a felhasználóknak a bejelentkezést.

1. Hozzon létre egy webalkalmazást, amely a következő parancs használatával hitelesít egy helyi adatbázison:

   ```dotnetcli
   dotnet new mvc --auth Individual -o TestFeatureFlags
   ```

1. Hozzon létre és futtasson, majd kattintson a jobb felső sarokban található **regisztráció** hivatkozásra egy új felhasználói fiók létrehozásához. Használjon e-mail-címet `test@contoso.com` . A **regisztráció megerősítése** képernyőn válassza a **kattintson ide a fiók megerősítéséhez**.

1. Kövesse a gyors üzembe helyezési útmutató [: szolgáltatás-jelzők hozzáadása egy ASP.net Core alkalmazáshoz](./quickstart-feature-flag-aspnet-core.md) , és adja hozzá a szolgáltatás jelölőjét az új webalkalmazáshoz.

1. Kapcsolja be a funkció jelzőjét az alkalmazás konfigurációjában. Annak ellenőrzése, hogy ez a művelet szabályozza-e a **bétaverzió** láthatóságát a navigációs sávon.

## <a name="update-the-web-application-code-to-use-targetingfilter"></a>A webalkalmazás kódjának frissítése a TargetingFilter használatára

Ezen a ponton a szolgáltatás jelzője segítségével engedélyezheti vagy letilthatja az `Beta` összes felhasználó szolgáltatását. Ha a szolgáltatás jelölőjét egyes felhasználók számára szeretné engedélyezni, miközben mások számára letiltja, frissítse a használni kívánt kódot `TargetingFilter` . Ebben a példában a bejelentkezett felhasználó e-mail-címét fogja használni felhasználói AZONOSÍTÓként, valamint az e-mail-cím tartománynév részét a csoportként. Adja hozzá a felhasználót és a csoportot a következőhöz: `TargetingContext` . A `TargetingFilter` ezt a környezetet használja az egyes kérésekhez tartozó szolgáltatás jelző állapotának meghatározásához.

1. Frissítsen a csomag legújabb verziójára `Microsoft.FeatureManagement.AspNetCore` .

   ```dotnetcli
   dotnet add package Microsoft.FeatureManagement.AspNetCore
   ```

1. *TestTargetingContextAccessor. cs* fájl hozzáadása:

    ```csharp
    using Microsoft.AspNetCore.Http;
    using Microsoft.FeatureManagement.FeatureFilters;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace TestFeatureFlags
    {
        public class TestTargetingContextAccessor : ITargetingContextAccessor
        {
            private const string TargetingContextLookup = "TestTargetingContextAccessor.TargetingContext";
            private readonly IHttpContextAccessor _httpContextAccessor;

            public TestTargetingContextAccessor(IHttpContextAccessor httpContextAccessor)
            {
                _httpContextAccessor = httpContextAccessor ?? throw new ArgumentNullException(nameof(httpContextAccessor));
            }

            public ValueTask<TargetingContext> GetContextAsync()
            {
                HttpContext httpContext = _httpContextAccessor.HttpContext;
                if (httpContext.Items.TryGetValue(TargetingContextLookup, out object value))
                {
                    return new ValueTask<TargetingContext>((TargetingContext)value);
                }
                List<string> groups = new List<string>();
                if (httpContext.User.Identity.Name != null)
                {
                    groups.Add(httpContext.User.Identity.Name.Split("@", StringSplitOptions.None)[1]);
                }
                TargetingContext targetingContext = new TargetingContext
                {
                    UserId = httpContext.User.Identity.Name,
                    Groups = groups
                };
                httpContext.Items[TargetingContextLookup] = targetingContext;
                return new ValueTask<TargetingContext>(targetingContext);
            }
        }
    }
    ```

1. A *Startup. cs*-ban adjon hozzá egy hivatkozást a *Microsoft. FeatureManagement. FeatureFilters* névtérhez:

    ```csharp
    using Microsoft.FeatureManagement.FeatureFilters;
    ```

1. Frissítse a *ConfigureServices* metódust a regisztráláshoz `TargetingFilter` , a következő hívásával `AddFeatureManagement()` :

    ```csharp
    services.AddFeatureManagement()
            .AddFeatureFilter<TargetingFilter>();
    ```

1. Frissítse a *ConfigureServices* metódust, hogy hozzáadja a `TestTargetingContextAccessor` Létrehozás a korábbi lépésben a szolgáltatás gyűjteményhez. A *TargetingFilter* azt használja, hogy a szolgáltatás jelölője minden alkalommal kiértékelje a kiértékelési környezetet.

    ```csharp
      services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    ```

A teljes *ConfigureServices* metódus így fog kinézni:

```csharp
    public void ConfigureServices(IServiceCollection services)
    {
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    services.AddDefaultIdentity<IdentityUser>(options => options.SignIn.RequireConfirmedAccount = true)
            .AddEntityFrameworkStores<ApplicationDbContext>();
    services.AddControllersWithViews();
    services.AddRazorPages();

    // Add feature management, targeting filter, and ITargetingContextAccessor to service collection
    services.AddFeatureManagement().AddFeatureFilter<TargetingFilter>();
    services.AddSingleton<ITargetingContextAccessor, TestTargetingContextAccessor>();
    }
```

## <a name="update-the-feature-flag-to-use-targetingfilter"></a>A szolgáltatás jelző frissítése a TargetingFilter használatára

1. A Azure Portal nyissa meg az alkalmazás konfigurációs tárolóját, és válassza a **Feature Manager** elemet.

1. Válassza ki a rövid útmutatóban létrehozott *béta* -szolgáltatás jelölőjét a helyi menüben. Válassza a **Szerkesztés** elemet.

    > [!div class="mx-imgBorder"]
    > ![Bétaverzió funkciójának szerkesztése](./media/edit-beta-feature-flag.png)

1. Ha még nincs bejelölve, a **Szerkesztés** képernyőn jelölje be a **szolgáltatás jelző engedélyezése** jelölőnégyzetet. Ezután jelölje be a **szolgáltatás szűrőjének használata** jelölőnégyzetet.

1. Válassza a **célzás** választógombot.

1. Válassza az alábbi lehetőségeket:

    - **Alapértelmezett százalék**: 0
    - **Csoportok**: adja meg a _contoso.com_ **nevét** és a _50_ **százalékos arányát**
    - **Felhasználók**: `test@contoso.com`

    A funkció szűrő képernyője a következőképpen fog kinézni:

    > [!div class="mx-imgBorder"]
    > ![Feltételes szolgáltatás jelzője](./media/feature-flag-filter-enabled.png)

    Ezek a beállítások a következő viselkedést eredményezik:

    - A szolgáltatás jelzője mindig engedélyezve van a felhasználó számára `test@contoso.com` , mert `test@contoso.com` a a _felhasználók_ szakaszban szerepel.
    - A szolgáltatás jelzője engedélyezve van a _contoso.com_ csoport többi felhasználójának 50%-ában, mert a _contoso.com_ a _groups (csoportok_ ) szakaszban szerepel a _50_-es _százalékos arányban_ .
    - A szolgáltatás mindig le van tiltva az összes többi felhasználónál, mert az _alapértelmezett százalékos_ érték _0_.

1. A beállítások mentéséhez és a **Feature Manager** képernyőhöz való visszatéréshez kattintson az **alkalmaz** gombra.

1. A **szolgáltatás** jelölője mostantól *célzásként* jelenik meg. Ez az állapot azt jelzi, hogy a szolgáltatás jelölője a *célzási* funkció szűrője által kényszerített feltételek alapján lesz engedélyezve vagy letiltva a kérelmek alapján.

## <a name="targetingfilter-in-action"></a>TargetingFilter működés közben

A szolgáltatás jelölő hatásának megtekintéséhez hozza létre és futtassa az alkalmazást. Kezdetben a *bétaverzió* nem jelenik meg az eszköztáron, mert az _alapértelmezett százalék_ beállítás értéke 0.

Most jelentkezzen be `test@contoso.com` , a regisztráláskor beállított jelszó használatával. A *bétaverzió* ekkor megjelenik az eszköztáron, mert `test@contoso.com` célként megadott felhasználóként van megadva.

A következő videó mutatja ezt a viselkedést működés közben.

> [!div class="mx-imgBorder"]
> ![TargetingFilter működés közben](./media/feature-flags-targetingfilter.gif)

`@contoso.com`E-mail-címmel további felhasználókat is létrehozhat, hogy megtekintse a csoport beállításainak viselkedését. a felhasználók 50%-a fogja látni a *bétaverziót* . A másik 50% nem jelenik meg a *bétaverzióban* .

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A szolgáltatások kezelése – áttekintés](./concept-feature-management.md)