---
title: Oktatóanyag – bejelentkezési felhasználók és egy védett API meghívása egy Blazer webszerelvény-alkalmazásból
titleSuffix: Microsoft identity platform
description: Ebben az oktatóanyagban jelentkezzen be a felhasználókba, és hívjon fel egy védett API-t a Microsoft Identity platform használatával a Blazer webassembly (WASM) alkalmazásban.
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 47b4f36aec9a906317a9704a7d73bf66385d9e88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552116"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>Oktatóanyag: bejelentkezés a felhasználókba és a védett API meghívása egy Blazer webszerelvény-alkalmazásból

Ebben az oktatóanyagban egy olyan Blazer webszerelvény-alkalmazást hoz létre, amely bejelentkezik a felhasználók számára, és beolvassa a Microsoft Graph adatait a Microsoft Identity platform használatával, és regisztrálja az alkalmazást a Azure Active Directoryban (Azure AD). 

Ebben az oktatóanyagban:

> [!div class="checklist"]
>
> * Hozzon létre egy új, az Azure Active Directory (Azure AD) használatára konfigurált, a Microsoft Identity platformot használó, [hitelesítésre és engedélyezésre](authentication-vs-authorization.md) konfigurált Blazer webszerelvény-alkalmazást
> * Adatok beolvasása egy védett webes API-ból, ebben az esetben [Microsoft Graph](/graph/overview)

Ez az oktatóanyag a .NET Core 3,1-et használja. A .NET docs a ASP.NET Core 5,0 [-et használó Blazer webszerelvény-alkalmazás biztonságossá tételére](/aspnet/core/blazor/security/webassembly/graph-api) vonatkozó utasításokat tartalmaz. 

A [Blazer-kiszolgáló oktatóanyaga](tutorial-blazor-server.md)is rendelkezésre áll. 

## <a name="prerequisites"></a>Előfeltételek

* [.NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* Egy Azure AD-bérlő, ahol regisztrálhat egy alkalmazást. Ha nincs hozzáférése egy Azure AD-bérlőhöz, beszerezhet egyet a [Microsoft 365 fejlesztői programhoz](https://developer.microsoft.com/microsoft-365/dev-program) való regisztrációval, vagy egy [ingyenes Azure-fiók](https://azure.microsoft.com/free)létrehozásával.

## <a name="register-the-app-in-the-azure-portal"></a>Az alkalmazás regisztrálása a Azure Portalban

Minden Azure Active Directory (Azure AD) hitelesítést használó alkalmazást regisztrálni kell az Azure AD-ben. Kövesse az [alkalmazás regisztrálása](quickstart-register-app.md) a következő specifikációkkal című témakör utasításait:

- **Támogatott fióktípus** esetén **csak a szervezeti címtárban** válassza a fiókok lehetőséget.
- Hagyja meg az **átirányítási URI** legördülő listát a web értékre, és írja be a **következőt** : `https://localhost:5001/authentication/login-callback` . A vércse-on futó alkalmazások alapértelmezett portja 5001. Ha az alkalmazás egy másik porton érhető el, a portszámot a helyett kell megadnia `5001` .

Regisztráció után a **kezelés** területen válassza a **hitelesítés**  >  **implicit engedélyezés és a hibrid folyamatok** lehetőséget. Válassza a **hozzáférési jogkivonatok** és **azonosító tokenek** lehetőséget, majd kattintson a **Mentés** gombra.

## <a name="create-the-app-using-the-net-core-cli"></a>Az alkalmazás létrehozása a a .NET Core parancssori felülete használatával

Az alkalmazás létrehozásához szüksége lesz a legújabb Blazer-sablonokra. Ezeket a a .NET Core parancssori felülete a következő paranccsal telepítheti:

```dotnetcli
dotnet new -i Microsoft.Identity.Web.ProjectTemplates::1.6.0
```

Ezután futtassa a következő parancsot az alkalmazás létrehozásához. Cserélje le a parancsban található helyőrzőket az alkalmazás áttekintés lapjáról, és hajtsa végre a parancsot egy parancs-rendszerhéjban. A beállítással megadott kimeneti hely `-o|--output` létrehoz egy projekt mappát, ha az nem létezik, és az alkalmazás nevének részévé válik.

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| Helyőrző   | Azure Portal neve       | Példa                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | Alkalmazás (ügyfél) azonosítója | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | Címtár (bérlő) azonosítója   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>Az alkalmazás tesztelése

Most már létrehozhatja és futtathatja az alkalmazást. Ha futtatja ezt a sablon alkalmazást, meg kell adnia a keretrendszert, amelyet a-keretrendszer használatával kell futtatni. Ez az oktatóanyag a .NET Standard 2,1-as szabványt használja, de a sablon más keretrendszereket is támogat.

```dotnetcli
dotnet run --framework netstandard2.1
```

A böngészőben nyissa meg a alkalmazást `https://localhost:5001` , és jelentkezzen be egy Azure ad-felhasználói fiókkal, és tekintse meg az alkalmazást futtató és a Microsoft Identity platformon a felhasználók naplózása című témakört.

A sablon azon összetevői, amelyek engedélyezik az Azure AD-be való bejelentkezést a Microsoft Identity platform használatával, a [jelen témakör ASP.net dokumentációjában olvashatók](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package).

## <a name="retrieving-data-from-a-protected-api-microsoft-graph"></a>Adatok beolvasása védett API-ból (Microsoft Graph)

A [Microsoft Graph](/graph/overview) olyan API-kat tartalmaz, amelyek hozzáférést biztosítanak a felhasználók Microsoft 365 adataihoz, és támogatja a Microsoft Identity platform által kiállított jogkivonatokat, ami a megfelelő védett API-t használja példaként. Ebben a szakaszban kódot adhat hozzá a Microsoft Graph hívásához, és megjeleníti a felhasználó e-mail-címeit az alkalmazás "beolvasása" lapján.

Ez a szakasz a védett API-k névvel ellátott ügyfél használatával történő meghívására szolgáló közös megközelítéssel íródott. Ugyanezt a módszert használhatja más védett API-k meghívásához is. Ha azonban az alkalmazásból Microsoft Graph meghívását tervezi, a Graph SDK használatával csökkentheti a kihelyezhető elemet. A .NET docs útmutatást tartalmaz a [Graph SDK használatáról](/aspnet/core/blazor/security/webassembly/graph-api?view=aspnetcore-5.0&preserve-view=true).

Mielőtt elkezdené, jelentkezzen ki az alkalmazásból, mert a szükséges engedélyeket módosítja, és az aktuális jogkivonat nem fog működni. Ha még nem tette meg, futtassa újra az alkalmazást, és válassza a **kijelentkezés** lehetőséget az alábbi kód frissítése előtt.

Most frissítenie kell az alkalmazás regisztrációját és kódját, hogy lekérje a felhasználó e-mailjeit, és megjeleníti az üzeneteket az alkalmazáson belül.

Először adja hozzá az `Mail.Read` API-engedélyt az alkalmazás regisztrálásához, hogy az Azure ad tisztában legyen azzal, hogy az alkalmazás kérni fogja a felhasználók e-mail-címének elérését.

1. A Azure Portal válassza ki az alkalmazást **Alkalmazásregisztrációkban**.
1. A **kezelés** területen válassza az **API-engedélyek** lehetőséget.
1. Válassza **az engedély hozzáadása**  >  **Microsoft Graph** elemet.
1. Válassza a **delegált engedélyek** elemet, majd keresse meg és válassza ki a **mail. Read** engedélyt.
1. Válassza az **engedélyek hozzáadása** lehetőséget.

Ezután adja hozzá a következőt a projekt *. csproj* fájljához a Netstandard 2.1 **ItemGroup**. Ez lehetővé teszi az egyéni HttpClient létrehozását a következő lépésben.

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

Ezután módosítsa a kódot a következő néhány lépésben megadott módon. Ezek a módosítások [hozzáférési jogkivonatokat](access-tokens.md) fognak hozzáadni a Microsoft Graph API-nak küldött kimenő kérelmekhez. Ezt a mintát részletesebben a [ASP.net Core Blazer webszerelvény további biztonsági forgatókönyvei](/aspnet/core/blazor/security/webassembly/additional-scenarios)tárgyalják.

Először hozzon létre egy *GraphAPIAuthorizationMessageHandler. cs* nevű új fájlt a következő kóddal. Ez a kezelő a felhasználó számára hozzáférési tokent ad hozzá a `User.Read` és `Mail.Read` hatókörökhöz a Microsoft Graph API felé irányuló kimenő kérésekhez.

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

Ezután cserélje le a metódus tartalmát a `Main` *program. cs fájl* tartalmára a következő kóddal. Ez a kód az új és az `GraphAPIAuthorizationMessageHandler` `User.Read` `Mail.Read` alapértelmezett hatóköröket használja, amelyeket az alkalmazás akkor kér, amikor a felhasználó először bejelentkezik.

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

Végül cserélje le a *FetchData. Razor* oldal tartalmát a következő kódra. Ez a kód beolvassa a felhasználó e-mail-adatait a Microsoft Graph API-ból, és listaként jeleníti meg őket. A-ben `OnInitializedAsync` a `HttpClient` megfelelő hozzáférési jogkivonatot használó új létrehozás és a kérésnek a Microsoft Graph API-hoz való elvégzésére szolgál.

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

Most indítsa újra az alkalmazást. Megfigyelheti, hogy a rendszer felszólítja, hogy adjon hozzáférést az alkalmazásnak az e-mailek olvasásához. Ez akkor várható, ha egy alkalmazás kéri a `Mail.Read` hatókört.

A jóváhagyás megadása után navigáljon az "adat lekérése" lapra, és olvasson el néhány e-mailt.

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="Képernyőkép a végleges alkalmazásról. Van egy fejléce, amely szerint a Hello Nicholas, és megjeleníti a Nicholas-hoz tartozó e-mailek listáját.":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Microsoft Identity platform – ajánlott eljárások és javaslatok](./identity-platform-integration-checklist.md)
