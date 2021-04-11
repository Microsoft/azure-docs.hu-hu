---
title: Webes API-kat meghívó webalkalmazás konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan konfigurálhatja a webes API-kat meghívó webalkalmazások kódját
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: aa377547f7f4961e199ec8d62bf0f1435296f983
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669304"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Webes API-kat meghívó webalkalmazás: kód konfigurálása

Ahogy [azt a webalkalmazásban](scenario-web-app-sign-user-overview.md) is láthatja, amely a felhasználók forgatókönyvét jelzi, a webalkalmazás a [OAuth 2,0 engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md) használja a felhasználó aláírásához a alkalmazásban. A folyamat két lépésből áll:

1. Kérjen egy engedélyezési kódot. Ez a rész privát párbeszédet delegál a felhasználóval a Microsoft Identity platformon. A párbeszéd során a felhasználó bejelentkezik, és hozzájárul a webes API-k használatához. A privát párbeszéd sikeres befejezése után a webalkalmazás egy engedélyezési kódot kap az átirányítási URI-n.
1. Igényeljen hozzáférési jogkivonatot az API-hoz az engedélyezési kód beváltásával.

A [felhasználói forgatókönyvekben bejelentkező webalkalmazás](scenario-web-app-sign-user-overview.md) csak az első lépést tárgyalja. Itt megtudhatja, hogyan módosíthatja a webalkalmazást úgy, hogy ne csak a felhasználókat írja alá, hanem a webes API-kat is meghívja.

## <a name="microsoft-libraries-supporting-web-apps"></a>Web Apps-t támogató Microsoft-kódtárak

A következő Microsoft-kódtárak támogatják a Web Apps szolgáltatást:

[!INCLUDE [active-directory-develop-libraries-webapp](../../../includes/active-directory-develop-libraries-webapp.md)]

Válassza ki az Önt érdeklő platform lapját:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Ügyfél-titkok vagy Ügyféltanúsítványok

Mivel a webalkalmazás most meghívja az alárendelt webes API-t, adjon meg egy ügyfél-titkos vagy ügyféltanúsítványt a fájl *appsettings.js* . Hozzáadhat egy szakaszt is, amely meghatározza a következőket:

- Az alárendelt webes API URL-címe
- Az API meghívásához szükséges hatókörök

A következő példában a `GraphBeta` szakasz ezeket a beállításokat adja meg.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
    }
}
```

Az ügyfél titkos kulcsa helyett megadhat egy ügyféltanúsítványt. A következő kódrészlet a Azure Key Vaultban tárolt tanúsítvány használatát mutatja be.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-app-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"

   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
   ]
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

A *Microsoft. Identity. Web* számos módszert kínál a tanúsítványok leírására, a konfiguráció vagy a kód alapján. Részletekért lásd: [Microsoft. Identity. Web – tanúsítványok használata](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) a githubon.

## <a name="startupcs"></a>Startup.cs

A webalkalmazásnak meg kell adnia egy jogkivonatot az alsóbb rétegbeli API-hoz. Ezt úgy adhatja meg, hogy a sort a következő után adja hozzá `.EnableTokenAcquisitionToCallDownstreamApi()` `.AddMicrosoftIdentityWebApi(Configuration)` . Ez a sor teszi elérhetővé a `ITokenAcquisition` vezérlő és az oldal műveleteiben használható szolgáltatást. A következő két lehetőség esetében azonban egyszerűen megteheti. Ki kell választania egy jogkivonat-gyorsítótár implementációját is, például `.AddInMemoryTokenCaches()` az *indításkor. cs*:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

Az átadott hatókörök `EnableTokenAcquisitionToCallDownstreamApi` nem kötelezőek, és lehetővé teszik, hogy a webalkalmazás a hatóköröket és a felhasználó beleegyezik a hatókörökbe a bejelentkezéskor. Ha nem adja meg a hatóköröket, a *Microsoft. Identity. Web* lehetővé teszi a növekményes beleegyező felhasználói élmény meglétét.

Ha nem szeretné saját magának megkapni a jogkivonatot, a *Microsoft. Identity. Web* két mechanizmust biztosít a webes API-k egy webalkalmazásból való meghívásához. A választott lehetőség attól függ, hogy Microsoft Graph vagy egy másik API-t szeretne meghívni.

### <a name="option-1-call-microsoft-graph"></a>1. lehetőség: hívás Microsoft Graph

Ha meg szeretné hívni Microsoft Graph, a *Microsoft. Identity. Web* lehetővé teszi, hogy közvetlenül használja a `GraphServiceClient` (az Microsoft Graph SDK által közzétett) API-műveleteket. Microsoft Graph közzététele:

1. Adja hozzá a [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) NuGet-csomagot a projekthez.
1. Adja `.AddMicrosoftGraph()` hozzá `.EnableTokenAcquisitionToCallDownstreamApi()` a következőt a *Startup. cs* fájlban. `.AddMicrosoftGraph()` több felülbírálással rendelkezik. A konfigurációs szakaszt paraméterként tartalmazó felülbírálás használatával a kód a következőképpen fog megjelenni:

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, Configuration.GetSection("AzureAd"))
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>2. lehetőség: a Microsoft Graphtól eltérő alárendelt webes API meghívása

Ha a Microsoft Graphtól eltérő webes API-t szeretne meghívni, a *Microsoft. Identity. Web* biztosítja `.AddDownstreamWebApi()` , amely jogkivonatokat kér le, és meghívja az alárendelt webes API-t.

   ```csharp
   using Microsoft.Identity.Web;

   public class Startup
   {
     // ...
     public void ConfigureServices(IServiceCollection services)
     {
     // ...
     services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
             .AddMicrosoftIdentityWebApp(Configuration, "AzureAd")
               .EnableTokenAcquisitionToCallDownstreamApi(new string[]{"user.read" })
                  .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
               .AddInMemoryTokenCaches();
      // ...
     }
     // ...
   }
   ```

### <a name="summary"></a>Összefoglalás

A webes API-khoz hasonlóan különböző jogkivonat-gyorsítótár-implementációkat is választhat. Részletekért lásd: [Microsoft. Identity. Web-token cache szerializálás](https://aka.ms/ms-id-web/token-cache-serialization) a githubon.

Az alábbi ábrán a *Microsoft. Identity. Web* különböző lehetőségei láthatók, valamint a *Startup. cs* fájlra gyakorolt hatásuk:

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="A webes API-k meghívásához és a jogkivonat-gyorsítótár implementációjának megadásához szükséges, a szolgáltatás konfigurációs beállításait bemutató diagram letiltása":::

> [!NOTE]
> A példákban szereplő kódrészletek teljes megértéséhez Ismerkedjen meg [ASP.net Core alapjaival](/aspnet/core/fundamentals), és különösen a [függőségek befecskendezésével](/aspnet/core/fundamentals/dependency-injection) és [lehetőségeivel](/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Mivel a felhasználói bejelentkezés az OpenID Connect (OIDC) middleware-hez van delegálva, a OIDC folyamattal kell kommunikálnia. Az interakció módja a használt keretrendszertől függ.

A ASP.NET esetében előfizethet a köztes OIDC eseményekre:

- Lehetővé teszi, hogy ASP.NET Core igényeljen egy engedélyezési kódot az Open ID-kapcsolat közbenső hálózatának segítségével. A ASP.NET vagy a ASP.NET Core lehetővé teszi a felhasználó számára a bejelentkezést és a beleegyezik.
- Előfizet a webalkalmazásnak az engedélyezési kód fogadásához. Ezt az előfizetést C#-delegált használatával végezheti el.
- Az engedélyezési kód fogadásakor a MSAL-kódtárakat fogja használni a beváltáshoz. Az eredményül kapott hozzáférési jogkivonatok és frissítési tokenek a jogkivonat-gyorsítótárban tárolódnak. A gyorsítótár az alkalmazás más részeiben, például a vezérlőkben is használható, hogy a többi tokent csendesen szerezzen.

A cikkben szereplő példák a [ASP.net Web App mintából](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)származnak. A teljes körű megvalósítási részletekért érdemes lehet erre a példára hivatkozni.

# <a name="java"></a>[Java](#tab/java)

A cikkben szereplő példákat és a következőt a Java-webalkalmazásból nyeri ki, [amely meghívja a Microsoft Grapht](https://github.com/Azure-Samples/ms-identity-java-webapp), a MSAL for Java-t használó webalkalmazási mintát.
A minta jelenleg lehetővé teszi, hogy a Java MSAL az engedélyezési kód URL-címét, és kezeli a navigációt a Microsoft Identity platform engedélyezési végpontján. A Sprint Security használatával is aláírhatja a felhasználót a alkalmazásban. Előfordulhat, hogy a teljes megvalósítás részleteit a mintában érdemes megtekinteni.

# <a name="python"></a>[Python](#tab/python)

A cikkben szereplő példákat és az alábbi kódot a [Python-webalkalmazás hívja le Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), egy webalkalmazási mintát, amely a MSAL-t használja. Python.
A minta jelenleg lehetővé teszi a MSAL. A Python előkészíti az engedélyezési kód URL-címét, és kezeli a navigációt a Microsoft Identity platform engedélyezési végpontján. Előfordulhat, hogy a teljes megvalósítás részleteit a mintában érdemes megtekinteni.

---

## <a name="code-that-redeems-the-authorization-code"></a>Az engedélyezési kódot beváltó kód

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A Microsoft. Identity. Web leegyszerűsíti a kódot úgy, hogy beállítja a megfelelő OpenID Connect-beállításokat, előfizet a kóddal fogadott eseményre, és beváltja a kódot. Az engedélyezési kód beváltásához nem szükséges további kód. Ennek működéséről a [Microsoft. Identity. Web forráskódjában](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L140) talál további információt.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET a ASP.NET Core hasonlóan kezeli a dolgokat, azzal a különbséggel, hogy az OpenID Connect konfigurációja és az eseményre való előfizetés az `OnAuthorizationCodeReceived` [App_Start \startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) -fájlban történik. A fogalmak a ASP.NET Corehoz hasonlóan is hasonlóak, de a ASP.NET-ben meg kell adnia `RedirectUri` a [Web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Ez a konfiguráció egy kicsit kevésbé robusztus, mint ASP.NET Core, mert az alkalmazás telepítésekor módosítania kell azt.

Itt látható az indítási. auth. CS kód:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Tekintse [meg a felhasználók által használt webalkalmazást: a kód konfigurációjában](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) megismerheti, hogy a Java-minta hogyan kapja meg az engedélyezési kódot. Miután az alkalmazás megkapja a kódot, a [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delegálja a `AuthHelper.processAuthenticationCodeRedirect` metódusnak a [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Hívások `getAuthResultByAuthCode` .

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

A `getAuthResultByAuthCode` metódus definiálva van a [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176). Létrehoz egy MSAL `ConfidentialClientApplication` , majd `acquireToken()` `AuthorizationCodeParameters` az engedélyezési kódból létrehozott hívásokat hívja meg.

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

A rendszer az engedélyezési kód folyamatát kéri a [webalkalmazásban, amely aláírja a felhasználókat: kód konfigurálása](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). A kód ezután a `authorized` függvényen érkezik, amely a lombik útvonalait az `/getAToken` URL-címről továbbítja. Lásd: [app. a # L30 – L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) a kód teljes kontextusához:

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Az ügyfél titkos kulcsa helyett a bizalmas ügyfélalkalmazás is igazolhatja személyazonosságát egy ügyféltanúsítvány vagy egy ügyfél-tanúsítvány használatával.
Az ügyfél-kijelentések használata egy speciális forgatókönyv, amely részletesen szerepel az [ügyfél-kijelentésekben](msal-net-client-assertions.md).

## <a name="token-cache"></a>Jogkivonat-gyorsítótár

> [!IMPORTANT]
> A webalkalmazások vagy webes API-k jogkivonat-gyorsítótárazási implementációja eltér az asztali alkalmazások megvalósításán, ami gyakran [fájl alapú](scenario-desktop-acquire-token.md#file-based-token-cache).
> Biztonsági és teljesítménybeli okokból fontos, hogy a webalkalmazások és a webes API-k esetében a felhasználói fiókok esetében egy jogkivonat-gyorsítótár legyen. Minden fiókhoz szerializálnia kell a jogkivonat-gyorsítótárat.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core oktatóanyag függőségi befecskendezést használ, hogy eldöntse a jogkivonat-gyorsítótár megvalósítását az alkalmazás Startup. cs fájljában. A Microsoft. Identity. Web előre elkészített jogkivonat-gyorsítótárazási szerializálók szerepelnek a [jogkivonat-gyorsítótár szerializálása](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)című témakörben. Érdekes lehetőség a ASP.NET Core [elosztott memória-gyorsítótárak](/aspnet/core/performance/caching/distributed#distributed-memory-cache)kiválasztása:

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi(
                initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache();

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

A jogkivonat-gyorsítótár szolgáltatókkal kapcsolatos részletekért lásd még: Microsoft. Identity. Web [token cache szerializálási](https://aka.ms/ms-id-web/token-cache-serialization) cikk, valamint a [ASP.net Core Web App oktatóanyagok | ](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) A Web Apps-oktatóanyag token-gyorsítótárai szakasza.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A webalkalmazások vagy webes API-k jogkivonat-gyorsítótárazási implementációja eltér az asztali alkalmazások megvalósításán, ami gyakran [fájl alapú](scenario-desktop-acquire-token.md#file-based-token-cache).

A webes alkalmazás implementációja a ASP.NET-munkamenetet vagy a kiszolgáló memóriáját használhatja. Tekintse meg például a gyorsítótár implementációját a MSAL.NET alkalmazás létrehozása után a [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

A MSAL Java a jogkivonat-gyorsítótár szerializálására és deszerializálására szolgáló metódusokat biztosít. A Java-minta kezeli a szerializálást a munkamenetből, ahogy az az `getAuthResultBySilentFlow` [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)metódusban is látható:

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Az osztály részletes adatai `SessionManagementHelper` a [Java MSAL-mintájában](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)vannak megadva.

# <a name="python"></a>[Python](#tab/python)

A Python-mintában egy fiókon belül egy gyorsítótárat biztosítunk, amely minden kérelemhez létrehoz egy bizalmas ügyfélalkalmazás-alkalmazást, majd a lombik-munkamenet gyorsítótárában szerializálja azt:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>Következő lépések

Ezen a ponton, amikor a felhasználó bejelentkezik, a rendszer a jogkivonat-gyorsítótárban tárolja a tokent. Lássuk, hogyan használják majd a webalkalmazás más részeiben.

[Fiókok eltávolítása a gyorsítótárból a globális kijelentkezéskor](scenario-web-app-call-api-sign-in.md)
