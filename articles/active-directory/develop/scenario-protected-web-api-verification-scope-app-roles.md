---
title: Hatókörök és alkalmazás-szerepkörök által védett webes API ellenőrzése | Azure
titleSuffix: Microsoft identity platform
description: Győződjön meg arról, hogy az API-t csak olyan felhasználók nevében hívja meg, akik rendelkeznek a megfelelő hatókörrel és a megfelelő alkalmazás-szerepkörökkel rendelkező Daemon-alkalmazásokkal.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6d737f107e7a9b1476fdf86ac0320931ea137671
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94442905"
---
# <a name="protected-web-api-verify-scopes-and-app-roles"></a>Védett webes API: hatókörök és alkalmazás-szerepkörök ellenőrzése

Ez a cikk azt ismerteti, hogyan adhat hozzá engedélyt a webes API-hoz. Ez a védelem biztosítja, hogy az API-t csak a következő hívja meg:

- Alkalmazások a megfelelő hatókörökkel rendelkező felhasználók nevében.
- A megfelelő alkalmazás-szerepkörökkel rendelkező Daemon-alkalmazások.

> [!NOTE]
> A cikkben szereplő kódrészletek a GitHubon a következő mintakód-példákból származnak:
>
> - [ASP.NET Core webes API növekményes oktatóanyaga](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/master/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Controllers/TodoListController.cs)
> - [ASP.NET web API-minta](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof/blob/master/TodoListService/Controllers/TodoListController.cs)

A ASP.NET vagy ASP.NET Core webes API-k védetté tételéhez az `[Authorize]` attribútumot a következő elemek egyikéhez kell hozzáadnia:

- A vezérlőt, ha azt szeretné, hogy az összes vezérlő művelet védve legyen
- Az API-hoz tartozó egyéni vezérlő művelet

```csharp
    [Authorize]
    public class TodoListController : Controller
    {
     ...
    }
```

Ez a védelem azonban nem elég. Csak azt garantálja, hogy a ASP.NET és a ASP.NET Core érvényesítse a jogkivonatot. Az API-nak ellenőriznie kell, hogy az API meghívásához használt jogkivonat a várt jogcímeket kéri-e. A jogcímeknek különösen az ellenőrzésre van szükségük:

- A *hatókörök* , ha az API-t egy felhasználó nevében hívják meg.
- Az *alkalmazás szerepkörei* , ha az API-t egy Daemon-alkalmazásból lehet meghívni.

## <a name="verify-scopes-in-apis-called-on-behalf-of-users"></a>Az API-k hatókörének ellenőrzése felhasználók nevében

Ha egy ügyfélalkalmazás egy felhasználó nevében hívja meg az API-t, az API-nak olyan tulajdonosi jogkivonatot kell igényelnie, amely meghatározott hatókörökkel rendelkezik az API-hoz. További információ: [kód konfigurálása | Tulajdonosi jogkivonat](scenario-protected-web-api-app-configuration.md#bearer-token).

### <a name="net-core"></a>.NET Core

#### <a name="verify-the-scopes-on-each-controller-action"></a>A hatókörök ellenőrzése az egyes vezérlő műveletekben

```csharp
[Authorize]
public class TodoListController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

    // GET: api/values
    [HttpGet]
    public IEnumerable<TodoItem> Get()
    {
         HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);
        // Do the work and return the result.
        // ...
    }
...
}
```

A `VerifyUserHasAnyAcceptedScope` metódus a következő lépésekhez hasonló:

- Ellenőrizze, hogy van-e a vagy a nevű jogcím `http://schemas.microsoft.com/identity/claims/scope` `scp` .
- Ellenőrizze, hogy a jogcím tartalmaz-e olyan értéket, amely tartalmazza az API által várt hatókört.


#### <a name="verify-the-scopes-more-globally"></a>A hatókörök ellenőrzése globálisan

Az ajánlott módszer a webes API részletes hatókörének meghatározása és a hatókörök ellenőrzése az egyes vezérlő műveletekben. A hatóköröket azonban az alkalmazás vagy a vezérlő szintjén is ellenőrizheti ASP.NET Core használatával. Részletekért lásd: [jogcím-alapú hitelesítés](/aspnet/core/security/authorization/claims) a ASP.net Core dokumentációjában.

### <a name="net-mvc"></a>.NET MVC

A ASP.NET esetében csak cserélje le a `HttpContext.User` `ClaimsPrincipal.Current` -t, és cserélje le a jogcím típusát a következőre: `"http://schemas.microsoft.com/identity/claims/scope"` `"scp"` . A kódrészletet a cikk későbbi részében is láthatja.

## <a name="verify-app-roles-in-apis-called-by-daemon-apps"></a>Az alkalmazás szerepköreinek ellenőrzése Daemon-alkalmazások által hívott API-kon

Ha a webes API-t egy [Daemon-alkalmazás](scenario-daemon-overview.md)hívja meg, akkor az alkalmazásnak a webes API-hoz szükséges alkalmazási engedéllyel kell rendelkeznie. Ahogy az az [alkalmazás engedélyeinek](./scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles)kifejtése című ábrán látható, az API elérhetővé teszi az ilyen engedélyeket. Az egyik példa az `access_as_application` alkalmazás szerepkör.

Most az API-nak ellenőriznie kell, hogy a kapott jogkivonat tartalmazza-e a `roles` jogcímet, és hogy a jogcím a várt értékkel rendelkezik-e. Az ellenőrző kód hasonló a delegált engedélyeket ellenőrző kódhoz, kivéve, hogy a vezérlő műveleti teszteket végez a hatókörök helyett a szerepkörökhöz:

### <a name="aspnet-core"></a>ASP.NET Core

```csharp
[Authorize]
public class TodoListController : ApiController
{
    public IEnumerable<TodoItem> Get()
    {
        HttpContext.ValidateAppRole("access_as_application");
        ...
    }
```

A `ValidateAppRole` metódus a Microsoft. Identity. Web [RolesRequiredHttpContextExtensions. cs](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RolesRequiredHttpContextExtensions.cs#L28)fájljában van definiálva.

### <a name="aspnet-mvc"></a>ASP.NET, MVC

```csharp
private void ValidateAppRole(string appRole)
{
    //
    // The `role` claim tells you what permissions the client application has in the service.
    // In this case, we look for a `role` value of `access_as_application`.
    //
    Claim roleClaim = ClaimsPrincipal.Current.FindFirst("roles");
    if (roleClaim == null || !roleClaim.Value.Split(' ').Contains(appRole))
    {
        throw new HttpResponseException(new HttpResponseMessage
        { StatusCode = HttpStatusCode.Unauthorized,
            ReasonPhrase = $"The 'roles' claim does not contain '{appRole}' or was not found"
        });
    }
}
}
```

### <a name="accepting-app-only-tokens-if-the-web-api-should-be-called-only-by-daemon-apps"></a>Csak alkalmazásbeli tokenek fogadása, ha a webes API-t csak Daemon-alkalmazások hívhatják meg

A felhasználók a felhasználói hozzárendelési mintákban is használhatják a szerepkörök jogcímeit, ahogy [azt az útmutató: alkalmazás-Szerepkörök hozzáadása az alkalmazásban című témakörben és a jogkivonatban](howto-add-app-roles-in-azure-ad-apps.md). Ha a szerepkörök mindkettőhöz hozzárendelhetők, a szerepkörök ellenőrzése lehetővé teszi, hogy az alkalmazások felhasználóként és felhasználóként jelentkezzenek be alkalmazásként. Javasoljuk, hogy a félreértések elkerülése érdekében különböző szerepköröket deklaráljon a felhasználók és az alkalmazások számára.

Ha azt szeretné, hogy a démon-alkalmazások meghívja a webes API-t, adja hozzá azt a feltételt, amely szerint a jogkivonat csak alkalmazási token, amikor érvényesíti az alkalmazás szerepkörét.

```csharp
string oid = ClaimsPrincipal.Current.FindFirst("oid")?.Value;
string sub = ClaimsPrincipal.Current.FindFirst("sub")?.Value;
bool isAppOnlyToken = oid == sub;
```

Az inverz feltétel ellenőrzése lehetővé teszi, hogy csak azok az alkalmazások jelentkezzenek be az API-ra, amelyek bejelentkeznek a felhasználókba.

## <a name="next-steps"></a>Következő lépések

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](scenario-protected-web-api-production.md).
