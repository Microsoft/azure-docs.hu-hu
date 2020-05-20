---
title: Webes API-kat meghívó webalkalmazás konfigurálása – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan konfigurálhatja a webes API-kat meghívó webalkalmazások kódját
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: f20636bfaf8b1b1f7714a9cede63886deaf53e36
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83641196"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Webes API-kat meghívó webalkalmazás: kód konfigurálása

Ahogy [azt a webalkalmazásban](scenario-web-app-sign-user-overview.md) is láthatja, amely a felhasználók forgatókönyvét jelzi, a webalkalmazás a [OAuth 2,0 engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md) használja a felhasználó aláírásához a alkalmazásban. A folyamat két lépésből áll:

1. Kérjen egy engedélyezési kódot. Ez a rész privát párbeszédet delegál a felhasználóval a Microsoft Identity platformon. A párbeszéd során a felhasználó bejelentkezik, és hozzájárul a webes API-k használatához. A privát párbeszéd sikeres befejezése után a webalkalmazás egy engedélyezési kódot kap az átirányítási URI-n.
1. Igényeljen hozzáférési jogkivonatot az API-hoz az engedélyezési kód beváltásával.

A [felhasználói forgatókönyvekben bejelentkező webalkalmazás](scenario-web-app-sign-user-overview.md) csak az első lépést tárgyalja. Itt megtudhatja, hogyan módosíthatja a webalkalmazást úgy, hogy ne csak a felhasználókat írja alá, hanem a webes API-kat is meghívja.

## <a name="libraries-that-support-web-app-scenarios"></a>Webalkalmazás-forgatókönyveket támogató kódtárak

A Microsoft Authentication Library (MSAL) következő kódtárai támogatják a webes alkalmazások engedélyezési kódjának áramlását:

| MSAL-könyvtár | Description |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | A .NET-keretrendszer és a .NET Core platform támogatása. A Univerzális Windows-platform (UWP), a Xamarin. iOS és a Xamarin. Android nem támogatott, mivel ezek a platformok nyilvános ügyfélalkalmazások létrehozására használhatók. ASP.NET Core webalkalmazások és webes API-k esetében a MSAL.NET a Microsoft. Identity. Web nevű magasabb szintű könyvtárban van beágyazva.|
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> Pythonhoz készült MSAL | Python-webalkalmazások támogatása. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> Javához készült MSAL | Java-webalkalmazások támogatása. |

Válassza ki az Önt érdeklő platform lapját:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Ha engedélyezni szeretné a webalkalmazás számára a védett API-k meghívását a Microsoft. Identity. Web használatakor, csak hívnia kell, `AddWebAppCallsProtectedWebApi` és meg kell adnia a jogkivonat-gyorsítótár szerializálási formátumát (például memóriabeli jogkivonat-gyorsítótárban):

```C#
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
    // more code here

    services.AddSignIn(Configuration, "AzureAd")
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddInMemoryTokenCaches();

    // more code here
}
```

Ha szeretné jobban megismerni a jogkivonat-gyorsítótárat, tekintse meg a [jogkivonat-gyorsítótár szerializálási beállításai](#token-cache) című témakört.

> [!NOTE]
> Az itt található programkódok teljes megértéséhez ismernie kell [ASP.net Core alapjait](https://docs.microsoft.com/aspnet/core/fundamentals), és különösen a [függőségi befecskendezést](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) és a [beállításokat](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Mivel a felhasználói bejelentkezés delegálva van az Open ID kapcsolódási (OIDC) köztes, a OIDC folyamattal kell kommunikálni. Az interakció módja a használt keretrendszertől függ.

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

A Microsoft. Identity. Web leegyszerűsíti a kódot úgy, hogy beállítja a megfelelő OpenID Connect-beállításokat, előfizet a kóddal fogadott eseményre, és beváltja a kódot. Az engedélyezési kód beváltásához nem szükséges további kód.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET a ASP.NET Core hasonlóan kezeli a dolgokat, azzal a különbséggel, hogy az OpenID Connect konfigurációja és az eseményre való előfizetés az `OnAuthorizationCodeReceived` [App_Start \startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) -fájlban történik. A fogalmak a ASP.NET Corehoz hasonlóan is hasonlóak, de a ASP.NET a `RedirectUri` [web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15)kell megadnia. Ez a konfiguráció egy kicsit kevésbé robusztus, mint ASP.NET Core, mert az alkalmazás telepítésekor módosítania kell azt.

A Startup.Auth.cs kódja:

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

A ASP.NET Core oktatóanyag függőségi befecskendezést használ, hogy eldöntse a jogkivonat-gyorsítótár megvalósítását az alkalmazás Startup.cs-fájljában. A Microsoft. Identity. Web előre elkészített jogkivonat-gyorsítótárazási szerializálók szerepelnek a [jogkivonat-gyorsítótár szerializálása](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application)című témakörben. Érdekes lehetőség a ASP.NET Core [elosztott memória-gyorsítótárak](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)kiválasztása:

```csharp
// Use a distributed token cache by adding:
    services.AddSignIn(Configuration, "AzureAd");
            .AddWebAppCallsProtectedWebApi(Configuration,
                                           initialScopes: new string[] { "user.read" })
            .AddDistributedTokenCaches();

// Then, choose your implementation.
// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

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

További információ a jogkivonat-gyorsítótár szolgáltatókról: a [ASP.net Core Web App oktatóanyagok | Az oktatóanyag jogkivonat-gyorsítótárazási](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) fázisa.

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

## <a name="next-steps"></a>További lépések

Ezen a ponton, amikor a felhasználó bejelentkezik, a rendszer a jogkivonat-gyorsítótárban tárolja a tokent. Lássuk, hogyan használják majd a webalkalmazás más részeiben.

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webalkalmazás: fiókok eltávolítása a gyorsítótárból a globális kijelentkezéskor](scenario-web-app-call-api-sign-in.md)
