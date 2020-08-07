---
title: Felhasználói bejelentkezést használó webalkalmazás konfigurálása – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre egy webalkalmazást, amely a felhasználók számára jelentkezik (kód konfigurálása)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 7352fe220e302730f2683d335de5d32ca13d9add
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87845170"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Felhasználók számára bejelentkező webalkalmazás: kód konfigurálása

Megtudhatja, hogyan konfigurálhatja a webalkalmazáshoz tartozó kódot, amely a felhasználóknál jelentkezik.

## <a name="libraries-for-protecting-web-apps"></a>A webalkalmazások védelméhez használható kódtárak

<!-- This section can be in an include for web app and web APIs -->
A webalkalmazások (és webes API-k) elleni védelemhez használt kódtárak a következők:

| Platform | Kódtár | Leírás |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_NET.png) | [A .NET-hez készült Identity Model-bővítmények](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | A ASP.NET és a ASP.NET Core által közvetlenül használt Microsoft Identity Model Extensions for .NET azt javasolja, hogy a .net-keretrendszerben és a .NET Core-ban is fusson a DLL-fájlok összessége. Egy ASP.NET vagy ASP.NET Core webalkalmazásból a jogkivonat-érvényesítést a **TokenValidationParameters** osztály használatával (különösen bizonyos partneri forgatókönyvekben) lehet szabályozni. A gyakorlatban a bonyolultság a [Microsoft. Identity. Web](https://aka.ms/ms-identity-web) könyvtárban van beágyazva. |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Java-webalkalmazások támogatása |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Python-webalkalmazások támogatása |

Válassza ki az Önt érdeklő platformhoz tartozó lapot:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A cikkben szereplő kódrészletek és a következők a [ASP.net Core Web App növekményes oktatóanyagból, 1. fejezetből](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)származnak.

Ebben az oktatóanyagban érdemes megtekinteni a teljes megvalósítás részleteit.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A cikkben szereplő kódrészletek és a következők a [ASP.net Web App mintából](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)származnak.

Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

# <a name="java"></a>[Java](#tab/java)

Ebben a cikkben a kódrészleteket és a következő, a Java- [webalkalmazásban a Microsoft Graph mintát hívó Java-webalkalmazásból](https://github.com/Azure-Samples/ms-identity-java-webapp) kinyert MSAL.

Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

# <a name="python"></a>[Python](#tab/python)

A cikkben szereplő kódrészletek és a következők a Python [webalkalmazásból](https://github.com/Azure-Samples/ms-identity-python-webapp) származnak, amely a MSAL Pythonban hívja meg a Microsoft Graph mintát.

Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

---

## <a name="configuration-files"></a>Konfigurációs fájlok

A felhasználókat a Microsoft Identity platform használatával bejelentkező webalkalmazások konfigurációs fájlokon keresztül konfigurálhatók. A kitöltendő beállítások a következők:

- A Cloud instance ( `Instance` ), ha azt szeretné, hogy az alkalmazás az országos felhőkben fusson, például:
- A bérlő AZONOSÍTÓjának () célközönsége `TenantId`
- Az alkalmazáshoz tartozó ügyfél-azonosító ( `ClientId` ), amelyet a Azure Portal másolt

Előfordulhat, hogy az alkalmazások a parametrized `Authority` , amely a és a összefűzése `Instance` `TenantId` .

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core ezek a beállítások a fájl [appsettings.js](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) a "AzureAd" szakaszban találhatók.

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-oidc"
  }
}
```

ASP.NET Core egy másik fájl ([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) tartalmazza az `applicationUrl` `sslPort` alkalmazáshoz és a különböző profilokhoz tartozó URL-címet () és a TLS/SSL-portot ().

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

A Azure Portal az alkalmazás **hitelesítési** lapján regisztrálni kívánt válasz URI-azonosítóknak meg kell egyezniük ezekkel az URL-címekkel. A két előző konfigurációs fájl esetében a következő lesz: `https://localhost:44321/signin-oidc` . Ennek az az oka `applicationUrl` , hogy az `http://localhost:3110` , de meg `sslPort` van adva (44321). `CallbackPath`a ( `/signin-oidc` ) a ben definiált `appsettings.json` .

Ugyanígy a kijelentkezési URI is a következőre lesz beállítva: `https://localhost:44321/signout-oidc` .

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET-ben az alkalmazás a [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) fájlon keresztül, a 12 – 15. sorban van konfigurálva.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

A Azure Portal az alkalmazás **hitelesítési** lapján regisztrálni kívánt válasz URI-azonosítóknak meg kell egyezniük ezekkel az URL-címekkel. Tehát a szükséges `https://localhost:44326/` .

# <a name="java"></a>[Java](#tab/java)

A Java-ban a konfiguráció a alatt található [Application. properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) fájlban található `src/main/resources` .

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

A Azure Portal az alkalmazás **hitelesítési** lapján regisztrálni kívánt válasz URI-azonosítóknak meg kell egyezniük az `redirectUri` alkalmazás által definiált példányokkal. Tehát a `http://localhost:8080/msal4jsample/secure/aad` és a `http://localhost:8080/msal4jsample/graph/me` .

# <a name="python"></a>[Python](#tab/python)

Itt látható a Python konfigurációs fájlja [app_config.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)a következőben:

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Ez a rövid útmutató azt javasolja, hogy az egyszerűség kedvéért a konfigurációs fájlban tárolja az ügyfél titkos kulcsát. Az éles alkalmazásban más módszerekkel is elvégezheti a titkos kulcs (például kulcstartó vagy környezeti változó) tárolását a [lombik dokumentációjában](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)leírtak szerint.
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Inicializálási kód

Az inicializálási kód a platformtól függően eltérő. ASP.NET Core és ASP.NET esetében a bejelentkezést a felhasználók az OpenID Connect middleware-be delegálják. A ASP.NET vagy a ASP.NET Core sablon webalkalmazásokat hoz létre a Azure Active Directory (Azure AD) 1.0-s verziójának végpontján. Néhány konfigurációra van szükség a Microsoft Identity platform (v 2.0) végponthoz való alkalmazkodáshoz. Java esetén a rugó az alkalmazás együttműködésével van kezelve.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core Web Apps (és webes API-k) esetében az alkalmazás védett, mert a `[Authorize]` vezérlőn vagy a vezérlő műveletein van egy attribútuma. Ez az attribútum ellenőrzi, hogy a felhasználó hitelesítése megtörtént-e. Az alkalmazást inicializáló kód a *Startup.cs* fájlban található.

A Microsoft Identity platform (korábbi nevén Azure AD v 2.0) használatával történő hitelesítés hozzáadásához hozzá kell adnia a következő kódot. A kódban szereplő megjegyzéseknek magától értetődőnek kell lenniük.

> [!NOTE]
> Ha közvetlenül a Microsoft Identity platformhoz tartozó új ASP.NET Core sablonokkal szeretné elindítani a Microsoft. Identity. Web-t, letöltheti a .NET Core 3,1 és a .NET 5,0 rendszerhez készült Project templates (előzetes verziójú) NuGet-csomagot. Ezután a telepítés után közvetlenül hozhat létre ASP.NET Core webalkalmazásokat (MVC vagy Blazer). Részletekért tekintse meg a [Microsoft. Identity. Web Web App Project-sablonokat](https://aka.ms/ms-id-web/webapp-project-templates) . Ez a legegyszerűbb megközelítés, mivel az alábbi lépéseket fogja elvégezni Önnek.
>
> Ha úgy szeretné elindítani a projektet, hogy az aktuális alapértelmezett ASP.NET Core webes projekt megjelenjen a Visual Studióban, vagy a vagy a használatával `dotnet new mvc --auth SingleAuth` `dotnet new webapp --auth SingleAuth` , a következőhöz hasonló kódot fog látni:
>
>```c#
>  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
>          .AddAzureAD(options => Configuration.Bind("AzureAd", options));
> ```
>
> Ez a kód az örökölt **Microsoft. AspNetCore. Authentication. AzureAD. UI** NuGet-csomagot használja, amely egy Azure ad v 1.0-alkalmazás létrehozásához használatos. Ez a cikk azt ismerteti, hogyan lehet létrehozni egy Microsoft Identity platform (Azure AD v 2.0) alkalmazást, amely felváltja ezt a kódot.
>

1. Adja hozzá a [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) és a [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) NuGet-csomagokat a projekthez. Ha megtalálható, távolítsa el a Microsoft. AspNetCore. Authentication. AzureAD. UI NuGet-csomagot.

2. Frissítse a kódot `ConfigureServices` úgy, hogy az és a `AddMicrosoftWebAppAuthentication` `AddMicrosoftIdentityUI` metódusokat használja.

   ```c#
   public class Startup
   {
    ...
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
     services.AddMicrosoftWebAppAuthentication(Configuration, "AzureAd");

     services.AddRazorPages().AddMvcOptions(options =>
     {
      var policy = new AuthorizationPolicyBuilder()
                    .RequireAuthenticatedUser()
                    .Build();
      options.Filters.Add(new AuthorizeFilter(policy));
     }).AddMicrosoftIdentityUI();
    ```

3. A `Configure` *Startup.cs*metódusában engedélyezze a hitelesítést a következő hívásával:`app.UseAuthentication();`

   ```c#
   // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
    // more code here
    app.UseAuthentication();
    app.UseAuthorization();
    // more code here
   }
   ```

A fenti kódban:
- A `AddMicrosoftWebAppAuthentication` kiterjesztési módszer a **Microsoft. Identity. webban**van definiálva. Ez
  - Hozzáadja a hitelesítési szolgáltatást.
  - A konfigurációs fájl olvasásához szükséges beállításokat konfigurálja (itt az "AzureAD" szakaszból)
  - Az OpenID Connect beállításainak konfigurálása, hogy a szolgáltató a Microsoft Identity platform végpontja legyen.
  - Ellenőrzi a jogkivonat kiállítóját.
  - Biztosítja, hogy a névnek megfelelő jogcímeket a rendszer az `preferred_username` azonosító jogkivonatban lévő jogcímből rendeli le.

- A konfigurációs objektum mellett megadhatja a konfigurációs szakasz nevét is a híváskor `AddMicrosoftWebAppAuthentication` . Alapértelmezés szerint ez a érték `AzureAd` .

- `AddMicrosoftWebAppAuthentication`más paraméterekkel rendelkezik a speciális forgatókönyvekhez. Az OpenID Connect middleware-események nyomon követése például segíthet a webalkalmazások hibakeresésében, ha a hitelesítés nem működik. Ha a nem kötelező paramétert állítja be, akkor `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` `true` megmutathatja, hogyan dolgozza fel az információkat a ASP.net Core köztes middleware-készlet, mivel a http-válaszból a felhasználó identitására kerül `HttpContext.User` .

- A `AddMicrosoftIdentityUI` kiterjesztési módszer a **Microsoft. Identity. Web. UI fájlon**belül van definiálva. Egy alapértelmezett vezérlőt biztosít a bejelentkezés és a kijelentkezés kezeléséhez.

További információ arról, hogy a Microsoft. Identity. Web segítségével hogyan hozhat létre webalkalmazásokat a következőben:<https://aka.ms/ms-id-web/webapp>

> [!WARNING]
> Jelenleg a Microsoft. Identity. Web nem támogatja az **egyéni felhasználói fiókok** (a felhasználói fiókok alkalmazásban való tárolása) esetét az Azure ad és a külső bejelentkezési szolgáltató használata esetén. Részletekért lásd: [AzureAD/Microsoft-Identity-web # 133](https://github.com/AzureAD/microsoft-identity-web/issues/133)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET webalkalmazás és webes API-k hitelesítéséhez kapcsolódó kód a [App_Start/Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) fájlban található.

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

A Java-minta a Spring Framework-t használja. Az alkalmazás védett, mert egy szűrőt valósít meg, amely elfogja az egyes HTTP-válaszokat. A Java-webalkalmazások rövid útmutatójában ez a szűrő található `AuthFilter` `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java` .

A szűrő feldolgozza a OAuth 2,0 engedélyezési kód folyamatát, és ellenőrzi, hogy a felhasználó hitelesítve van-e ( `isAuthenticated()` metódus). Ha a felhasználó nincs hitelesítve, kiszámítja az Azure AD-engedélyezési végpontok URL-címét, és átirányítja a böngészőt erre az URI-ra.

Ha a válasz érkezik, amely az engedélyezési kódot tartalmazza, a MSAL Java használatával szerzi be a jogkivonatot. Amikor végül megkapja a tokent a jogkivonat-végponttól (az átirányítási URI-n), a felhasználó bejelentkezett.

További részletekért tekintse `doFilter()` meg a [AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)metódusát.

> [!NOTE]
> A kódja `doFilter()` némileg eltérő sorrendben van megírva, de a folyamat az egyik leírása.

Az ezzel a módszerrel aktiválható engedélyezési kód folyamatával kapcsolatos részleteket lásd: [Microsoft Identity platform és OAuth 2,0 engedélyezési kód folyamata](v2-oauth2-auth-code-flow.md).

# <a name="python"></a>[Python](#tab/python)

A Python-minta a lombikot használja. A lombik és a MSAL Python inicializálása az [app. # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>Következő lépések

A következő cikkben megtudhatja, hogyan aktiválhatja a bejelentkezést és a kijelentkezést.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
