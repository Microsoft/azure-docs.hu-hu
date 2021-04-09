---
title: Felhasználói bejelentkezést bejelentkező webalkalmazás írása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre vagy ki felhasználókat használó webalkalmazásokat
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
ms.openlocfilehash: 10ddee404de21c5bc04672fdb6dd32c30f481ba3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578243"
---
# <a name="web-app-that-signs-in-users-sign-in-and-sign-out"></a>Felhasználók számára bejelentkező webes alkalmazás: bejelentkezés és kijelentkezés

Megtudhatja, hogyan veheti fel a bejelentkezést a webalkalmazáshoz a felhasználókba bejelentkező programkódba. Ezután megtudhatja, hogyan lehet kijelentkezni.

## <a name="sign-in"></a>Bejelentkezés

A bejelentkezés két részből áll:

- A HTML-lap bejelentkezési gombja
- A bejelentkezési művelet a vezérlő mögötti kódban

### <a name="sign-in-button"></a>Bejelentkezés gomb

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core a Microsoft Identity platform alkalmazásai esetében a **Bejelentkezés** gomb `Views\Shared\_LoginPartial.cshtml` (MVC-alkalmazás esetén) vagy `Pages\Shared\_LoginPartial.cshtm` (Razor-alkalmazás esetén) elérhető. Csak akkor jelenik meg, ha a felhasználó nincs hitelesítve. Ez azt jelzi, hogy akkor jelenik meg, ha a felhasználó még nincs bejelentkezve vagy kijelentkezett. Ellenkező esetben a **kijelentkezés** gomb akkor jelenik meg, ha a felhasználó már be van jelentkezve. Vegye figyelembe, hogy a fiók vezérlője a **Microsoft. Identity. Web. UI** NuGet csomagban van definiálva a **MicrosoftIdentity** nevű területen.

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET MVC-ben a kijelentkezési gomb elérhető a-ben `Views\Shared\_LoginPartial.cshtml` . Ez csak akkor jelenik meg, ha hitelesített fiók van. Ez azt jelzi, hogy a felhasználó korábban már bejelentkezett.

```html
@if (Request.IsAuthenticated)
{
 // Code omitted code for clarity
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

A Java gyors útmutatóban a bejelentkezési gomb a [Main/Resources/templates/index.html](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/msal-java-webapp-sample/src/main/resources/templates/index.html) fájlban található.

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>HomePage</title>
</head>
<body>
<h3>Home Page</h3>

<form action="/msal4jsample/secure/aad">
    <input type="submit" value="Login">
</form>

</body>
</html>
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A Node.js rövid útmutatóban nincs bejelentkezési gomb. A kód mögött automatikusan bekéri a felhasználót, hogy jelentkezzen be, amikor eléri a webalkalmazás gyökerét.

```javascript
app.get('/', (req, res) => {
    // authentication logic
});
```

# <a name="python"></a>[Python](#tab/python)

A Python rövid útmutatójában nincs bejelentkezési gomb. A kód mögött automatikusan bekéri a felhasználót, hogy jelentkezzen be, amikor eléri a webalkalmazás gyökerét. Lásd: [app. a # L14-L18](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app.py#L14-L18).

```Python
@app.route("/")
def index():
    if not session.get("user"):
        return redirect(url_for("login"))
    return render_template('index.html', user=session["user"])
```

---

### <a name="signin-action-of-the-controller"></a>`SignIn` a vezérlő művelete

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET-ben a webalkalmazás **bejelentkezési** gombjára kattintva elindítja a műveletet a `SignIn` `AccountController` vezérlőn. A ASP.NET Core sablonok korábbi verzióiban a `Account` vezérlőt a webalkalmazásba ágyazták be. Ez már nem így van, mert a vezérlő már része a **Microsoft. Identity. Web. UI** NuGet csomagnak. További részletekért lásd: [AccountController. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

Ez a vezérlő a Azure AD B2C-alkalmazásokat is kezeli.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET-ben a kijelentkezés a `SignOut()` vezérlő metódusa alapján történik (például [AccountController. cs # L16-L23](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L16-L23)). Ez a metódus nem része a ASP.NET-keretrendszernek (ellentétben a ASP.NET Core). Az átirányítási URI-t javasolva egy OpenID bejelentkezési kihívást küld.

```csharp
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

# <a name="java"></a>[Java](#tab/java)

A Java-ban a kijelentkezést a Microsoft Identity platform `logout` végpontjának közvetlen meghívásával és az érték megadásával kezeljük `post_logout_redirect_uri` . Részletekért lásd: [AuthPageController. Java # L30-L48](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L30-L48).

```Java
@Controller
public class AuthPageController {

    @Autowired
    AuthHelper authHelper;

    @RequestMapping("/msal4jsample")
    public String homepage(){
        return "index";
    }

    @RequestMapping("/msal4jsample/secure/aad")
    public ModelAndView securePage(HttpServletRequest httpRequest) throws ParseException {
        ModelAndView mav = new ModelAndView("auth_page");

        setAccountInfo(mav, httpRequest);

        return mav;
    }

    // More code omitted for simplicity
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A többi platformtól eltérően a MSAL-csomópont gondoskodik arról, hogy a felhasználó bejelentkezzen a bejelentkezési oldalról.

```javascript

// 1st leg of auth code flow: acquire a code
app.get('/', (req, res) => {
    const authCodeUrlParameters = {
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    // get url to sign user in and consent to scopes needed for application
    pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
        res.redirect(response);
    }).catch((error) => console.log(JSON.stringify(error)));
});

// 2nd leg of auth code flow: exchange code for token
app.get('/redirect', (req, res) => {
    const tokenRequest = {
        code: req.query.code,
        scopes: ["user.read"],
        redirectUri: REDIRECT_URI,
    };

    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
        res.sendStatus(200);
    }).catch((error) => {
        console.log(error);
        res.status(500).send(error);
    });
});
```

# <a name="python"></a>[Python](#tab/python)

A többi platformtól eltérően a MSAL Python gondoskodik arról, hogy a felhasználó bejelentkezzen a bejelentkezési oldalról. Lásd: [app. a # L20-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L20-L28).

```Python
@app.route("/login")
def login():
    session["state"] = str(uuid.uuid4())
    auth_url = _build_msal_app().get_authorization_request_url(
        app_config.SCOPE,  # Technically we can use an empty list [] to just sign in
                           # Here we choose to also collect user consent up front
        state=session["state"],
        redirect_uri=url_for("authorized", _external=True))
    return "<a href='%s'>Login with Microsoft Identity</a>" % auth_url
```

A `_build_msal_app()` metódus a következő módon van definiálva az [app. a # L81-L88](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L81-L88) :

```Python
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

def _get_token_from_cache(scope=None):
    cache = _load_cache()  # This web app maintains one cache per session
    cca = _build_msal_app(cache)
    accounts = cca.get_accounts()
    if accounts:  # So all accounts belong to the current signed-in user
        result = cca.acquire_token_silent(scope, account=accounts[0])
        _save_cache(cache)
        return result

```

---

Miután a felhasználó bejelentkezett az alkalmazásba, engedélyeznie kell a kijelentkezést.

## <a name="sign-out"></a>Kijelentkezés

A webalkalmazásból való kijelentkezés több, mint a webalkalmazás állapotában lévő bejelentkezett fiók adatainak eltávolítása.
A webalkalmazásnak is át kell irányítani a felhasználót a Microsoft Identity platform `logout` -végpontra a kijelentkezéshez.

Ha a webalkalmazás átirányítja a felhasználót a `logout` végpontra, ez a végpont törli a felhasználó munkamenetét a böngészőből. Ha az alkalmazás nem a `logout` végpontra mutat, a felhasználó újra hitelesíteni fogja magát az alkalmazásban anélkül, hogy újra be kellene írnia a hitelesítő adatait. Ennek az az oka, hogy egy érvényes egyszeri bejelentkezési munkamenettel rendelkeznek a Microsoft Identity platformmal.

További információért lásd a [kijelentkezési kérelem küldése](v2-protocols-oidc.md#send-a-sign-out-request) szakaszt a [Microsoft Identity platform és az OpenID Connect Protocol](v2-protocols-oidc.md) dokumentációjában.

### <a name="application-registration"></a>Alkalmazásregisztráció

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Az alkalmazás regisztrálása során regisztrálnia kell egy előtérben kijelentkezési URL-címet. Az oktatóanyagban a `https://localhost:44321/signout-oidc` **hitelesítés** oldalon a **kijelentkezési URL-cím** mezőben regisztrálta a regisztrációt. Részletekért lásd: [a webApp alkalmazás regisztrálása](scenario-web-app-sign-user-app-registration.md#register-an-app-by-using-the-azure-portal).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Az alkalmazás regisztrálása során nem kell regisztrálnia egy extra előtérben kijelentkezési URL-címet. A rendszer visszahívja az alkalmazást a fő URL-címére. 

# <a name="java"></a>[Java](#tab/java)

Az alkalmazás regisztrálásához nem szükséges előtérben kijelentkezési URL-cím.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Az alkalmazás regisztrálásához nem szükséges előtérben kijelentkezési URL-cím.

# <a name="python"></a>[Python](#tab/python)

Az alkalmazás regisztrálása során nem kell regisztrálnia egy extra előtérben kijelentkezési URL-címet. A rendszer visszahívja az alkalmazást a fő URL-címére.

---

### <a name="sign-out-button"></a>Kijelentkezés gomb

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET-ben a webalkalmazás **kijelentkezés** gombjára kattintva elindítja a `SignOut` műveletet a `AccountController` vezérlőn (lásd alább).

```html
<ul class="navbar-nav">
  @if (User.Identity.IsAuthenticated)
  {
    <li class="nav-item">
        <span class="navbar-text text-dark">Hello @User.Identity.Name!</span>
    </li>
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignOut">Sign out</a>
    </li>
  }
  else
  {
    <li class="nav-item">
        <a class="nav-link text-dark" asp-area="MicrosoftIdentity" asp-controller="Account" asp-action="SignIn">Sign in</a>
    </li>
  }
</ul>
```

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET MVC-ben a kijelentkezési gomb elérhető a-ben `Views\Shared\_LoginPartial.cshtml` . Ez csak akkor jelenik meg, ha hitelesített fiók van. Ez azt jelzi, hogy a felhasználó korábban már bejelentkezett.

```html
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

# <a name="java"></a>[Java](#tab/java)

A Java gyors útmutatóban a kijelentkezés gomb a Main/Resources/templates/auth_page.html fájlban található.

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<body>

<form action="/msal4jsample/sign_out">
    <input type="submit" value="Sign out">
</form>
...
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ez a minta alkalmazás nem implementálja a kijelentkezést.

# <a name="python"></a>[Python](#tab/python)

A Python rövid útmutatójában a kijelentkezés gomb a [templates/index.html # L10](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/templates/index.html#L10) fájlban található.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
</head>
<body>
    <h1>Microsoft Identity Python web app</h1>
    Welcome {{ user.get("name") }}!
    <li><a href='/graphcall'>Call Microsoft Graph API</a></li>
    <li><a href="/logout">Logout</a></li>
</body>
</html>
```

---

### <a name="signout-action-of-the-controller"></a>`SignOut` a vezérlő művelete

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core sablonok korábbi verzióiban a `Account` vezérlőt a webalkalmazásba ágyazták be. Ez már nem így van, mert a vezérlő már része a **Microsoft. Identity. Web. UI** NuGet csomagnak. További részletekért lásd: [AccountController. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs) .

- Beállítja az OpenID-átirányítási URI-t úgy, hogy `/Account/SignedOut` a vezérlőt vissza lehessen hívni, amikor az Azure ad befejezte a kijelentkezést.
- Meghívások `Signout()` , amelyek lehetővé teszi, hogy az OpenID Connect middleware kapcsolatba lépjen a Microsoft Identity platform- `logout` végponttal. A végpont ekkor:

  - Törli a munkamenet-cookie-t a böngészőből.
  - A kijelentkezés utáni átirányítási URI-t hívja vissza. Alapértelmezés szerint a kijelentkezés utáni átirányítási URI a [SignedOut. cshtml. cs](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Pages/Account/SignedOut.cshtml.cs)kijelentkezett nézet lapot jeleníti meg. Ez az oldal a Microsoft. Identity. Web részeként is elérhető.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET-ben a kijelentkezés a `SignOut()` vezérlő metódusa alapján történik (például [AccountController. cs # L25-L31](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Controllers/AccountController.cs#L25-L31)). Ez a metódus nem része a ASP.NET keretrendszernek, ellentétben azzal, ami a ASP.NET Core történik. Ez

- OpenID-kijelentkezési kihívást küld.
- Törli a gyorsítótárat.
- Átirányítja a kívánt oldalra.

```csharp
/// <summary>
/// Send an OpenID Connect sign-out request.
/// </summary>
public void SignOut()
{
 HttpContext.GetOwinContext()
            .Authentication
            .SignOut(CookieAuthenticationDefaults.AuthenticationType);
 Response.Redirect("/");
}
```

# <a name="java"></a>[Java](#tab/java)

A Java-ban a kijelentkezést a Microsoft Identity platform `logout` végpontjának közvetlen meghívásával és az érték megadásával kezeljük `post_logout_redirect_uri` . Részletekért lásd: [AuthPageController. Java # L50-L60](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L50-L60).

```Java
@RequestMapping("/msal4jsample/sign_out")
    public void signOut(HttpServletRequest httpRequest, HttpServletResponse response) throws IOException {

        httpRequest.getSession().invalidate();

        String endSessionEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/logout";

        String redirectUrl = "http://localhost:8080/msal4jsample/";
        response.sendRedirect(endSessionEndpoint + "?post_logout_redirect_uri=" +
                URLEncoder.encode(redirectUrl, "UTF-8"));
    }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ez a minta alkalmazás nem implementálja a kijelentkezést.

# <a name="python"></a>[Python](#tab/python)

A felhasználót kijelentkezési kód az [app. L46-L52](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L47-L48).

```Python
@app.route("/logout")
def logout():
    session.clear()  # Wipe out the user and the token cache from the session
    return redirect(  # Also need to log out from the Microsoft Identity platform
        "https://login.microsoftonline.com/common/oauth2/v2.0/logout"
        "?post_logout_redirect_uri=" + url_for("index", _external=True))
```

---

### <a name="intercepting-the-call-to-the-logout-endpoint"></a>A végpontra irányuló hívás elfogása `logout`

A kijelentkezés utáni URI lehetővé teszi, hogy az alkalmazások részt vegyenek a globális kijelentkezésben.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core OpenID Connect middleware lehetővé teszi, hogy az alkalmazás feltartóztatja a Microsoft Identity platform `logout` végpontjának hívását egy nevű OpenID Connect-esemény biztosításával `OnRedirectToIdentityProviderForSignOut` . Ezt a Microsoft. Identity. Web automatikusan kezeli (amely törli a fiókokat abban az esetben, amikor a webalkalmazás meghívja a webes API-kat)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET-ben delegálja a middleware-t, hogy végrehajtsa a kijelentkezést, törli a munkamenet cookie-jait:

```csharp
public class AccountController : Controller
{
 ...
 public void EndSession()
 {
  Request.GetOwinContext().Authentication.SignOut();
  Request.GetOwinContext().Authentication.SignOut(Microsoft.AspNet.Identity.DefaultAuthenticationTypes.ApplicationCookie);
  this.HttpContext.GetOwinContext().Authentication.SignOut(CookieAuthenticationDefaults.AuthenticationType);
 }
}
```

# <a name="java"></a>[Java](#tab/java)

A Java gyors útmutatóban a kijelentkezés utáni átirányítási URI csak a index.html oldalt jeleníti meg.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ez a minta alkalmazás nem implementálja a kijelentkezést.

# <a name="python"></a>[Python](#tab/python)

A Python rövid útmutatójában a kijelentkezés utáni átirányítási URI csak a index.html oldalt jeleníti meg.

---

## <a name="protocol"></a>Protokoll

Ha többet szeretne megtudni a kijelentkezésről, olvassa el az [Open ID csatlakozással](./v2-protocols-oidc.md)elérhető protokoll dokumentációját.

## <a name="next-steps"></a>Következő lépések

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](scenario-web-app-sign-user-production.md).