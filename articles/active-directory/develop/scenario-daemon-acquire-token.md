---
title: Jogkivonatok beszerzése webes API-k meghívásához (Daemon-alkalmazás) – a Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre olyan Daemon-alkalmazást, amely webes API-kat hív meg (tokenek beszerzése)
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
ms.openlocfilehash: 19ead7fe063992e95588641f7fd739081cf54a2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578413"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Webes API-kat meghívó Daemon-alkalmazás – jogkivonat beszerzése

A bizalmas ügyfélalkalmazás létrehozása után megkeresheti az alkalmazás jogkivonatát, ha meghívja, átadja `AcquireTokenForClient` a hatókört, és igény szerint kényszeríti a token frissítését.

## <a name="scopes-to-request"></a>Kérelmekre vonatkozó hatókörök

Az ügyfél-hitelesítő adatokra vonatkozó kérelem hatóköre az erőforrás neve, majd a `/.default` . Ez a jelölés azt mutatja Azure Active Directory (Azure AD) számára, hogy az alkalmazás regisztrálása során statikusan deklarált *alkalmazási szintű engedélyeket* használjon. Emellett a bérlői rendszergazdának is meg kell adni ezeket az API-engedélyeket.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```JavaScript
const tokenRequest = {
    scopes: [process.env.GRAPH_ENDPOINT + '.default'], // e.g. 'https://graph.microsoft.com/.default'
};
```

# <a name="python"></a>[Python](#tab/python)

A MSAL Pythonban a konfigurációs fájl az alábbi kódrészlethez hasonlít:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

---

### <a name="azure-ad-v10-resources"></a>Azure AD-erőforrások (v 1.0)

Az ügyfél hitelesítő adataihoz használt hatókörnek mindig az erőforrás-AZONOSÍTÓnak kell lennie, amelyet követően `/.default` .

> [!IMPORTANT]
> Ha a MSAL hozzáférési jogkivonatot kér egy olyan erőforráshoz, amely 1,0 hozzáférési tokent fogad el, akkor az Azure AD a kért hatókörből elemezi a kívánt célközönséget, ha az utolsó perjel előtt mindent megtesz, és erőforrás-azonosítóként használja azt.
> Tehát ha például Azure SQL Database (**https: \/ /Database.Windows.net**), az erőforrás egy olyan célközönséget vár, amely perjeltel végződik (Azure SQL Database esetén `https://database.windows.net/` ), akkor a hatókörét kell kérnie `https://database.windows.net//.default` . (Jegyezze fel a dupla perjelet.) Lásd még: MSAL.NET probléma [#747: az erőforrás URL-címének záró perjele ki van hagyva, ami SQL-hitelesítési hibát okozott](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Az alkalmazáshoz tartozó token beszerzéséhez `AcquireTokenForClient` a platformtól függően a vagy annak megfelelőt kell használnia.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

### <a name="acquiretokenforclient-uses-the-application-token-cache"></a>A AcquireTokenForClient az alkalmazás-jogkivonat gyorsítótárát használja

A MSAL.NET-ben `AcquireTokenForClient` az alkalmazás-jogkivonat gyorsítótárát használja. (Az összes többi AcquireToken *XX* -módszer a felhasználói jogkivonat gyorsítótárát használja.) `AcquireTokenSilent` A hívás előtt ne telefonáljon `AcquireTokenForClient` , mert `AcquireTokenSilent` a a *felhasználói* jogkivonat gyorsítótárát használja. `AcquireTokenForClient` ellenőrzi az *alkalmazás* -jogkivonat gyorsítótárát, és frissíti azt.

# <a name="java"></a>[Java](#tab/java)

Ez a kód a [MSAL Java dev-mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/)lett kinyerve.

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Az alábbi kódrészlet szemlélteti a tokenek beszerzését egy MSAL-csomópont bizalmas ügyfélalkalmazás:

```JavaScript
try {
    const authResponse = await cca.acquireTokenByClientCredential(tokenRequest);
    console.log(authResponse.accessToken) // display access token
} catch (error) {
    console.log(error);
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

---

### <a name="protocol"></a>Protokoll

Ha még nem rendelkezik a választott nyelvhez tartozó könyvtárral, érdemes lehet közvetlenül a protokollt használni:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>Első eset: hozzáférés a jogkivonat-kérelemhez közös titok használatával

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Második eset: hozzáférés a jogkivonat-kérelemhez tanúsítvány használatával

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

További információkért tekintse meg a protokoll dokumentációját: [Microsoft Identity platform és a OAuth 2,0 Client hitelesítő adatok folyamata](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="did-you-use-the-resourcedefault-scope"></a>Használta az erőforrás/. alapértelmezett hatókört?

Ha hibaüzenet jelenik meg arról, hogy érvénytelen hatókört használt, akkor valószínűleg nem használta a `resource/.default` hatókört.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Elfelejtette, hogy rendszergazdai engedélyt adjon? Daemon-alkalmazások szükségesek!

Ha az API meghívásakor nem **rendelkezik megfelelő jogosultsággal a művelet elvégzéséhez** , a bérlői rendszergazdának engedélyeket kell adnia az alkalmazás számára. Lásd a fenti ügyfélalkalmazás regisztrálásának 6. lépését.
Általában a következőhöz hasonló hibaüzenet jelenik meg:

```json
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

### <a name="are-you-calling-your-own-api"></a>Saját API-t hív meg?

Ha saját webes API-t hív meg, és nem tudott alkalmazás-engedélyt adni az alkalmazás regisztrálásához a Daemon-alkalmazáshoz, a webes API-ban közzétett egy alkalmazás-szerepkört?

Részletekért lásd: [alkalmazás engedélyeinek kimutatása (alkalmazás-szerepkörök)](scenario-protected-web-api-app-registration.md#exposing-application-permissions-app-roles) , és különösen [annak biztosítása, hogy az Azure ad a webes API-hoz tartozó jogkivonatokat csak az ügyfelek számára engedélyezzék](scenario-protected-web-api-app-registration.md#ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients).

## <a name="next-steps"></a>Következő lépések

# <a name="net"></a>[.NET](#tab/dotnet)

A forgatókönyvben a következő cikkre léphet, amely [egy webes API](./scenario-daemon-call-api.md?tabs=dotnet)-t hív meg.

# <a name="java"></a>[Java](#tab/java)

A forgatókönyvben a következő cikkre léphet, amely [egy webes API](./scenario-daemon-call-api.md?tabs=java)-t hív meg.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A forgatókönyvben a következő cikkre léphet, amely [egy webes API](./scenario-daemon-call-api.md?tabs=nodejs)-t hív meg.

# <a name="python"></a>[Python](#tab/python)

A forgatókönyvben a következő cikkre léphet, amely [egy webes API](./scenario-daemon-call-api.md?tabs=python)-t hív meg.

---
