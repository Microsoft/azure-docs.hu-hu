---
title: Token beszerzése webes API-kat meghívó webalkalmazásban – Microsoft Identity platform | Azure
description: Útmutató a webes API-kat meghívó webalkalmazások jogkivonatának beszerzéséhez
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
ms.openlocfilehash: 40e788099a159e1f60c0af02deccd7e3bef82744
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82181732"
---
# <a name="a-web-app-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webes API-kat meghívó webalkalmazás: az alkalmazás jogkivonatának beszerzése

Létrehozott egy ügyfélalkalmazás-objektumot. Most ezt a lehetőséget fogja használni a webes API-t meghívó token beszerzéséhez. A ASP.NET vagy ASP.NET Core a webes API meghívása a vezérlőben történik:

- Szerezze be a webes API tokenjét a jogkivonat-gyorsítótár használatával. A jogkivonat beszerzéséhez hívja meg a MSAL `AcquireTokenSilent` metódust (vagy ennek megfelelőjét a Microsoft. Identity. Web-ben).
- Hívja meg a védett API-t, és adja át a hozzáférési jogkivonatot paraméterként.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A vezérlő módszereit olyan attribútum védi `[Authorize]` , amely arra kényszeríti a felhasználókat, hogy a webalkalmazás használatára legyenek hitelesítve. A Microsoft Graph meghívására szolgáló kód:

```csharp
[Authorize]
public class HomeController : Controller
{
 readonly ITokenAcquisition tokenAcquisition;

 public HomeController(ITokenAcquisition tokenAcquisition)
 {
  this.tokenAcquisition = tokenAcquisition;
 }

 // Code for the controller actions (see code below)

}
```

A `ITokenAcquisition` szolgáltatást a ASP.net a függőségi befecskendezés használatával fecskendezi.

Itt látható a művelethez tartozó egyszerűsített kód `HomeController` , amely a Microsoft Graph meghívására szolgáló tokent kap:

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
 string json = await client.GetStringAsync(url);
}
```

A forgatókönyvhöz szükséges kód jobb megismeréséhez tekintse meg a 2. fázis ([2-1-Web App calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) lépést az [MS-Identity-aspnetcore-WebApp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) oktatóanyagban.

Más összetett változatok is léteznek, például:

- Több API meghívása.
- Növekményes beleegyezett és feltételes hozzáférés feldolgozása.

Ezek a speciális lépések a [3 – WebApp-multi-API](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/3-WebApp-multi-APIs) -k oktatóanyag 3. fejezetében vannak lefoglalva.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET kódja hasonló a ASP.NET Corehoz megjelenített kódhoz:

- Egy [engedélyezés] attribútum által védett vezérlő művelet kibontja a vezérlő tagjának bérlői AZONOSÍTÓját és felhasználói AZONOSÍTÓját `ClaimsPrincipal` . (A ASP.NET használja `HttpContext.User` .)
- Innentől kezdve létrehoz egy MSAL.NET `IConfidentialClientApplication` objektumot.
- Végezetül meghívja a `AcquireTokenSilent` bizalmas ügyfélalkalmazás metódusát.

# <a name="java"></a>[Java](#tab/java)

A Java-mintában az API-t meghívó kód a [AuthPageController. Java # L62](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthPageController.java#L62)getUsersFromGraph metódusában található.

A metódus hívására tett kísérlet `getAuthResultBySilentFlow` . Ha a felhasználónak több hatókörhöz kell hozzájárulnia, a kód feldolgozza az `MsalInteractionRequiredException` objektumot, hogy megtámadja a felhasználót.

```java
@RequestMapping("/msal4jsample/graph/me")
public ModelAndView getUserFromGraph(HttpServletRequest httpRequest, HttpServletResponse response)
        throws Throwable {

    IAuthenticationResult result;
    ModelAndView mav;
    try {
        result = authHelper.getAuthResultBySilentFlow(httpRequest, response);
    } catch (ExecutionException e) {
        if (e.getCause() instanceof MsalInteractionRequiredException) {

            // If the silent call returns MsalInteractionRequired, redirect to authorization endpoint
            // so user can consent to new scopes.
            String state = UUID.randomUUID().toString();
            String nonce = UUID.randomUUID().toString();

            SessionManagementHelper.storeStateAndNonceInSession(httpRequest.getSession(), state, nonce);

            String authorizationCodeUrl = authHelper.getAuthorizationCodeUrl(
                    httpRequest.getParameter("claims"),
                    "User.Read",
                    authHelper.getRedirectUriGraph(),
                    state,
                    nonce);

            return new ModelAndView("redirect:" + authorizationCodeUrl);
        } else {

            mav = new ModelAndView("error");
            mav.addObject("error", e);
            return mav;
        }
    }

    if (result == null) {
        mav = new ModelAndView("error");
        mav.addObject("error", new Exception("AuthenticationResult not found in session."));
    } else {
        mav = new ModelAndView("auth_page");
        setAccountInfo(mav, httpRequest);

        try {
            mav.addObject("userInfo", getUserInfoFromGraph(result.accessToken()));

            return mav;
        } catch (Exception e) {
            mav = new ModelAndView("error");
            mav.addObject("error", e);
        }
    }
    return mav;
}
// Code omitted here
```

# <a name="python"></a>[Python](#tab/python)

A Python-mintában a Microsoft Graph meghívására szolgáló kód az [app. a # L53-L62](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/48637475ed7d7733795ebeac55c5d58663714c60/app.py#L53-L62).

A kód kísérletet tesz a jogkivonat-gyorsítótárból való lekérésére. Ezután az engedélyezési fejléc beállítása után meghívja a webes API-t. Ha nem tud jogkivonatot beolvasni, a rendszer ismét aláírja a felhasználót.

```python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-hívás](scenario-web-app-call-api-call-api.md)
