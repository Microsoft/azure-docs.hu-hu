---
title: Token beszerzése webes API meghívásához (asztali alkalmazás) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre olyan asztali alkalmazást, amely webes API-kat hív meg az alkalmazás jogkivonatának beszerzéséhez
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/06/2021
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: d45c40bb6878da80f68fff9642b55da68706743a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305837"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Webes API-kat meghívó asztali alkalmazás: token beszerzése

Miután létrehozta a nyilvános ügyfélalkalmazás egy példányát, felhasználhatja egy olyan token beszerzéséhez, amelyet később a webes API-k meghívásához fog használni.

## <a name="recommended-pattern"></a>Javasolt minta

A webes API-t a saját maga határozza meg `scopes` . Függetlenül attól, hogy milyen élményt nyújt az alkalmazásban, a használandó minta a következő:

- Szisztematikus kísérlet a jogkivonat-gyorsítótárból való beolvasására a hívásával `AcquireTokenSilent` .
- Ha a hívás sikertelen, használja a `AcquireToken` használni kívánt folyamatot, amelyet itt képvisel `AcquireTokenXX` .

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>A MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java

Set<IAccount> accountsInCache = pca.getAccounts().join();
// Take first account in the cache. In a production application, you would filter
// accountsInCache to get the right account for the user authenticating.
IAccount account = accountsInCache.iterator().next();

IAuthenticationResult result;
try {
    SilentParameters silentParameters =
            SilentParameters
                    .builder(SCOPE, account)
                    .build();

    // try to acquire token silently. This call will fail since the token cache
    // does not have any data for the user you are trying to acquire a token for
    result = pca.acquireTokenSilently(silentParameters).join();
} catch (Exception ex) {
    if (ex.getCause() instanceof MsalException) {

        InteractiveRequestParameters parameters = InteractiveRequestParameters
                .builder(new URI("http://localhost"))
                .scopes(SCOPE)
                .build();

        // Try to acquire a token interactively with system browser. If successful, you should see
        // the token and account information printed out to console
        result = pca.acquireToken(parameters).join();
    } else {
        // Handle other exceptions accordingly
        throw ex;
    }
}
return result;

```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Az iOS és a macOS rendszerhez készült MSAL

Objective-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```
Swift

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A MSAL csomópontban a tokeneket a Code Exchange (PKCE) igazolási kulcsával rendelkező engedélyezési kód használatával szerzi be. A MSAL-csomópont memóriabeli jogkivonat-gyorsítótárat használ annak megtekintéséhez, hogy van-e felhasználói fiók a gyorsítótárban. Ha van, a fiók objektum átadható a `acquireTokenSilent()` metódusnak a gyorsítótárazott hozzáférési jogkivonat lekéréséhez.

```JavaScript

const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const msalTokenCache = pca.getTokenCache();

let accounts = await msalTokenCache.getAllAccounts();

    if (accounts.length > 0) {

        const silentRequest = {
            account: accounts[0], // Index must match the account that is trying to acquire token silently
            scopes: ["user.read"],
        };
    
        pca.acquireTokenSilent(silentRequest).then((response) => {
            console.log("\nSuccessful silent token acquisition");
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => console.log(error));
    } else {
        const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

        const authCodeUrlParameters = {
            scopes: ["User.Read"],
            redirectUri: "your_redirect_uri",
            codeChallenge: challenge, // PKCE Code Challenge
            codeChallengeMethod: "S256" // PKCE Code Challenge Method 
        };
        
        // get url to sign user in and consent to scopes needed for application
        pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
            console.log(response);
        
            const tokenRequest = {
                code: response["authorization_code"],
                codeVerifier: verifier // PKCE Code Verifier 
                redirectUri: "your_redirect_uri",
                scopes: ["User.Read"],
            };
            
            // acquire a token by exchanging the code
            pca.acquireTokenByCode(tokenRequest).then((response) => {
                console.log("\nResponse: \n:", response);
            }).catch((error) => {
                console.log(error);
            });
        }).catch((error) => console.log(JSON.stringify(error)));
    }
```

# <a name="python"></a>[Python](#tab/python)

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```
---

Itt láthatja a jogkivonatok asztali alkalmazásokban való beszerzésének különböző módszereit.

## <a name="acquire-a-token-interactively"></a>Jogkivonat interaktív beszerzése

Az alábbi példa azt mutatja be, hogy a jogkivonat interaktív beolvasása a felhasználó profiljának Microsoft Graph használatával történő olvasásához szükséges minimális kód.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>A MSAL.NET

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>Kötelező paraméterek

`AcquireTokenInteractive` csak egy kötelező paraméterrel rendelkezik, ``scopes`` amely tartalmazza a karakterláncok enumerálását, amelyek meghatározzák azokat a hatóköröket, amelyekhez jogkivonat szükséges. Ha a jogkivonat Microsoft Graph, akkor a szükséges hatókörök az "engedélyek" szakaszban található egyes Microsoft Graph API-k API-referenciájában találhatók. A [felhasználó névjegyeinek listázásához](/graph/api/user-list-contacts)például a "felhasználó. Read", a "Contacts. Read" hatókört kell használni. További információ: [Microsoft Graph engedélyek referenciája](/graph/permissions-reference).

Androidon a szülő tevékenységet is meg kell adnia a használatával, amint azt a (z `.WithParentActivityOrWindow` ), hogy a jogkivonat vissza fog térni az adott fölérendelt tevékenységhez az interakció után. Ha nem ad meg, a rendszer kivételt okoz a hívásakor `.ExecuteAsync()` .

### <a name="specific-optional-parameters-in-msalnet"></a>Speciális választható paraméterek a MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

A felhasználói felület azért fontos, mert interaktív. `AcquireTokenInteractive` egy adott opcionális paraméterrel rendelkezik, amely az azt támogató platformok esetében megadhatja a szülő felhasználói felületet. Asztali alkalmazásokban való használat esetén `.WithParentActivityOrWindow` eltérő típusú, amely a platformtól függ. Azt is megteheti, hogy kihagyja a választható szülő ablak paramétert egy ablak létrehozásához, ha nem szeretné szabályozni, hogy a bejelentkezési párbeszédpanel megjelenjen-e a képernyőn. Ez a parancssoron alapuló, a más háttérrendszer-szolgáltatásokra irányuló hívások átadására, valamint a felhasználói beavatkozáshoz nem szükséges Windows-alapú alkalmazásokra alkalmazható.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .NET Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Megjegyzéseket tartalmazó

- A .NET Standard esetében a várt `object` `Activity` Android, `UIViewController` iOS, `NSWindow` Mac `IWin32Window` vagy Windows rendszeren `IntPr` .
- Windows rendszeren a felhasználói felületi szálból kell hívnia a hívást, `AcquireTokenInteractive` hogy a beágyazott böngésző megkapja a megfelelő felhasználói felületi szinkronizációs környezetet. A felhasználói felületi szálból való hívás nem okozhatja, hogy az üzenetek nem pumpálják megfelelően a felhasználói felületet. A Microsoft hitelesítési kódtárak (MSALs-EK) a felhasználói felületi szálból való meghívásának egyik módja, ha a felhasználói felületi szálon már nem használja a `Dispatcher` on WPF szolgáltatást.
- Ha WPF-t használ, az osztályt használhatja a WPF-vezérlőkből származó ablak beszerzéséhez `WindowInteropHelper.Handle` . Ezt követően a hívás egy WPF-vezérlőből ( `this` ) történik:

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` a a felhasználóval való interaktivitás vezérlésére szolgál egy kérdés megadásával.

![A prompt struktúrában található mezőket ábrázoló kép. Ezek az állandó értékek vezérlik a felhasználót a WithPrompt () metódus által megjelenített kérdés típusának meghatározásával.](https://user-images.githubusercontent.com/34331512/112267137-3f1c3a00-8c32-11eb-97fb-33604311329a.png)

Az osztály a következő konstansokat határozza meg:

- ``SelectAccount`` arra kényszeríti az STS-t, hogy bemutassa a fiók kiválasztása párbeszédpanelt, amely azokat a fiókokat tartalmazza, amelyekhez a felhasználó rendelkezik munkamenettel. Ez a beállítás akkor hasznos, ha az alkalmazás fejlesztői számára engedélyezni szeretné a felhasználók számára a különböző identitások közötti választást. Ezzel a beállítással a MSAL küldheti ``prompt=select_account`` az identitás-szolgáltatónak. Ez az alapértelmezett beállítás. Jó munkát biztosít a lehető legjobb élmény biztosításához a rendelkezésre álló információk, például a felhasználói munkamenetek fiókja és jelenléte alapján. Csak akkor módosítsa, ha nincs megfelelő oka.
- ``Consent`` lehetővé teszi, hogy az alkalmazás fejlesztője kényszerítse a felhasználót, hogy kérjen beleegyező engedélyt, még akkor is, ha az engedélyt megadták. Ebben az esetben a MSAL elküldi `prompt=consent` az identitás-szolgáltatónak. Ez a beállítás olyan biztonsági szempontokat használó alkalmazásokban használható, ahol a szervezet irányítása megköveteli, hogy a felhasználó az alkalmazás minden egyes használatakor a belefoglalt engedély párbeszédablakban legyen látható.
- ``ForceLogin`` lehetővé teszi, hogy az alkalmazás fejlesztője megkérje a felhasználótól a hitelesítő adatok megadását, még akkor is, ha ez a felhasználói kérdés esetleg nem szükséges. Ez a beállítás akkor lehet hasznos, ha a felhasználó újra bejelentkezik, ha a jogkivonat beszerzése meghiúsul. Ebben az esetben a MSAL elküldi `prompt=login` az identitás-szolgáltatónak. Időnként olyan biztonsági irányultságú alkalmazásokban használják, ahol a szervezet irányítása megköveteli, hogy a felhasználó minden alkalommal újra aláírja az alkalmazást, amikor az alkalmazás egyes részeihez hozzáférnek.
- ``Create`` elindít egy regisztrációs élményt, amelyet külső identitásokhoz használ `prompt=create` az identitás-szolgáltatónak való küldéssel. Ezt a kérést nem szabad elküldeni Azure AD B2C alkalmazásokhoz. További információ: önkiszolgáló [bejelentkezési felhasználói folyamat hozzáadása egy alkalmazáshoz](https://aka.ms/msal-net-prompt-create).
- ``Never`` (csak a .NET 4,5 és a WinRT esetében) nem fogja kérni a felhasználót, hanem a rejtett beágyazott webes nézetben tárolt cookie-t próbálja használni. További információ: webes nézetek a MSAL.NET-ben. Ez a beállítás sikertelen lehet. Ebben az esetben kivételt jelez, hogy `AcquireTokenInteractive` a felhasználói felületi interakcióra van szükség. Egy másik paramétert kell használnia `Prompt` .
- ``NoPrompt`` a rendszer nem küld figyelmeztetést az identitás-szolgáltatónak. Ez a beállítás csak Azure Active Directory (Azure AD) B2C-szerkesztési profil házirendjének esetében hasznos. További információ: [Azure ad B2C sajátosságok](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withuseembeddedwebview"></a>WithUseEmbeddedWebView

Ezzel a módszerrel megadhatja, hogy szeretné-e kényszeríteni egy beágyazott webnézet vagy a rendszer webnézetének használatát (ha elérhető). További információ: [böngészők használata](msal-net-web-browsers.md).

 ```csharp
 var result = await app.AcquireTokenInteractive(scopes)
                   .WithUseEmbeddedWebView(true)
                   .ExecuteAsync();
  ```

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Ezt a módosítót olyan speciális forgatókönyvekben használják, ahol a felhasználónak előre jóvá kell hagynia több erőforrást, és nem érdemes növekményes beleegyezést használni, amelyet általában a MSAL.NET/the Microsoft Identity platformmal használ. További információ: [a felhasználó beleegyezett több erőforrásra](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

A webes felhasználói felület egy böngésző meghívására szolgáló mechanizmus. Ez a mechanizmus lehet dedikált felhasználói felületi WebBrowser vezérlő vagy a böngésző megnyitásának delegálására szolgáló módszer.
A MSAL webes felhasználói felületi implementációkat biztosít a legtöbb platformhoz, de vannak olyan esetek, amikor saját maga is üzemeltetni szeretné a böngészőt:

- A MSAL által kifejezetten nem érintett platformok, például a Blazer, az Unity és a Mono az asztali gépeken.
- Azt szeretné, hogy a felhasználói felület tesztelje az alkalmazást, és használjon egy olyan automatizált böngészőt, amely a szelén segítségével használható.
- A böngésző és a MSAL futtató alkalmazás külön folyamatokban van.

##### <a name="at-a-glance"></a>Gyors áttekintés

Ennek eléréséhez meg kell adnia a MSAL `start Url` , amelyet egy tetszőleges böngészőben kell megjeleníteni, hogy a végfelhasználók olyan elemeket is megadhatnak, mint a felhasználónevek.
A hitelesítés befejeződése után az alkalmazásnak vissza kell térnie a MSAL `end Url` -re, amely az Azure ad által biztosított kódot tartalmaz.
A gazdagép `end Url` mindig `redirectUri` . Az elfogáshoz `end Url` tegye a következők egyikét:

- A böngésző-átirányítások figyelése `redirect Url` a találatok között.
- A böngésző átirányítja az URL-címre, amelyet figyel.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>A WithCustomWebUi egy bővíthetőségi pont

`WithCustomWebUi` a egy bővíthetőségi pont, amellyel saját felhasználói felületet biztosíthat a nyilvános ügyfélalkalmazások számára. Azt is megteheti, hogy a felhasználó áthalad az/Authorize végpontján, és lehetővé teszi, hogy jelentkezzen be és hagyja jóvá. A MSAL.NET ezután beválthatják a hitelesítési kódot, és megkapják a jogkivonatot. A Visual Studióban például az elektron-alkalmazások (például a Visual Studio-visszajelzések) biztosítják a webes interakciót, de a munka nagy része nem MSAL.NET el. Akkor is használhatja, ha a felhasználói felület automatizálását szeretné megadni. A nyilvános ügyfélalkalmazások esetében a MSAL.NET a Code Exchange (PKCE) szabványának igazolási kulcsát használja annak biztosítására, hogy a biztonság tiszteletben legyen. A kódot csak a MSAL.NET lehet beváltani. További információ: [RFC 7636-Proof kulcs a Code Exchange számára nyilvános ügyfelek OAuth](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>WithCustomWebUi használata

A használatához `.WithCustomWebUI` kövesse az alábbi lépéseket.

  1. Implementálja az `ICustomWebUi` felületet. További információkért tekintse meg [ezt a webhelyet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implementáljon egy `AcquireAuthorizationCodeAsync` metódust, és fogadja el a MSAL.net által kiszámított engedélyezési kód URL-címét. Ezután hagyja, hogy a felhasználó átugorja a interakciót az identitás-szolgáltatóval, és visszaadja az URL-címet, amellyel az identitás-szolgáltató a megvalósítást az engedélyezési kóddal együtt vissza kellett volna hívnia. Ha problémák merülnek fel, a megvalósításnak kivételt kell kidobnia, `MsalExtensionException` hogy szépen működjenek együtt a MSAL.
  2. A `AcquireTokenInteractive` hívásban használja a `.WithCustomUI()` módosítót az egyéni webes felhasználói felület példányának átadásával.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Példák a ICustomWebUi bevezetésére a test automationben: SeleniumWebUI

A MSAL.NET csapata újraírta a felhasználói felületi teszteket, hogy ezt a bővíthető mechanizmust használják. Ha érdekli, tekintse meg a [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) osztályt a MSAL.net forráskódjában.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Nagyszerű élményt nyújt a SystemWebViewOptions

A MSAL.NET 4,1-es verziójában [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions) megadhatja a következőket:

- A () `BrowserRedirectError` vagy a megjelenített HTML-kódrészlet () vagy a rendszerböngészőbe való bejelentkezés vagy a hozzájuk tartozó megadási `HtmlMessageError` hibák esetén használandó URI.
- A `BrowserRedirectSuccess` `HtmlMessageSuccess` sikeres bejelentkezés vagy beleegyezikés esetén a () vagy a megjelenítendő HTML-kódrészlet ().
- A rendszerböngésző indításához futtatandó művelet. A delegálás beállításával saját implementációt is megadhat `OpenBrowserAsync` . Az osztály egy alapértelmezett implementációt is biztosít két böngészőhöz: `OpenWithEdgeBrowserAsync` és `OpenWithChromeEdgeBrowserAsync` a Microsoft Edge és a [Microsoft Edge esetében a Chromium](https://www.windowscentral.com/faq-edge-chromium)-ben.

A struktúra használatához írjon a következő példához hasonló módon:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Egyéb választható paraméterek

Ha többet szeretne megtudni a további választható paraméterekről, tekintse meg a következőt `AcquireTokenInteractive` : [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>Az iOS és a macOS rendszerhez készült MSAL

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A MSAL csomópontban a tokeneket a Code Exchange (PKCE) igazolási kulcsával rendelkező engedélyezési kód használatával szerzi be. A folyamat két lépésből áll: először az alkalmazás egy URL-címet kap, amely egy engedélyezési kód létrehozásához használható. Ez az URL-cím megnyitható egy tetszőleges böngészőben, ahol a felhasználó megadhatja a hitelesítő adatait, és a rendszer visszairányítja a `redirectUri` (az alkalmazás regisztrálása során regisztrált) engedélyezési kóddal. Másodszor, az alkalmazás átadja az engedélyezési kódot a `acquireTokenByCode()` metódusnak, amely egy hozzáférési jogkivonatra cseréli azt.

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

const authCodeUrlParameters = {
    scopes: ["User.Read"],
    redirectUri: "your_redirect_uri",
    codeChallenge: challenge, // PKCE Code Challenge
    codeChallengeMethod: "S256" // PKCE Code Challenge Method 
};

// get url to sign user in and consent to scopes needed for application
pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
    console.log(response);

    const tokenRequest = {
        code: response["authorization_code"],
        codeVerifier: verifier // PKCE Code Verifier 
        redirectUri: "your_redirect_uri",
        scopes: ["User.Read"],
    };
    
    // acquire a token by exchanging the code
    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
    }).catch((error) => {
        console.log(error);
    });
}).catch((error) => console.log(JSON.stringify(error)));
```

# <a name="python"></a>[Python](#tab/python)

A MSAL Python nem biztosít közvetlenül egy interaktív beszerzési jogkivonat-metódust. Ehelyett az alkalmazásnak engedélyezési kérelmet kell küldenie a felhasználói interakciós folyamat megvalósításában egy engedélyezési kód beszerzéséhez. Ezt a kódot ezután átadhatja a `acquire_token_by_authorization_code` metódusnak a jogkivonat lekéréséhez.

```Python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_authorization_code(
         request.args['code'],
         scopes=config["scope"])

```
---

## <a name="integrated-windows-authentication"></a>Beépített Windows-hitelesítés

Ha tartományi felhasználót szeretne bejelentkezni egy tartományba vagy egy Azure AD-hez csatlakoztatott gépre, használja az integrált Windows-hitelesítést (IWA).

### <a name="constraints"></a>Korlátozások

- Az integrált Windows-hitelesítés csak *összevont +* felhasználók számára használható, azaz a Active Directoryban létrehozott és az Azure ad által támogatott felhasználók. A közvetlenül az Azure AD-ben létrehozott felhasználók Active Directory biztonsági mentés nélkül, azaz a *felügyelt* felhasználók nem használhatják ezt a hitelesítési folyamatot. Ez a korlátozás nem befolyásolja a Felhasználónév és a jelszó folyamatát.
- A IWA a .NET-keretrendszer, a .NET Core és a Univerzális Windows-platform (UWP) platformokhoz írt alkalmazások esetében használható.
- A IWA nem kerüli el a [többtényezős hitelesítést (MFA)](../authentication/concept-mfa-howitworks.md). Ha az MFA konfigurálva van, a IWA sikertelen lehet, ha MFA-kihívásra van szükség, mert az MFA felhasználói beavatkozást igényel.
  
    A IWA nem interaktív, az MFA azonban felhasználói interaktivitást igényel. Nem szabályozhatja, hogy az identitás-szolgáltató Mikor kér el MFA-t, a bérlői rendszergazda végzi. A megfigyelések során MFA szükséges, ha más országból/régióból jelentkezik be, ha nem VPN-kapcsolaton keresztül csatlakozik a vállalati hálózathoz, és néha még akkor is, ha VPN-en keresztül csatlakozik. Ne várja meg a determinisztikus szabályait. Az Azure AD a mesterséges intelligencia használatával folyamatosan tanul, ha MFA szükséges. Ha a IWA nem sikerül, térjen vissza egy olyan felhasználói kéréshez, mint az interaktív hitelesítés vagy az eszköz kódjának folyamata.

- Az átadott szolgáltatónak a következőnek `PublicClientApplicationBuilder` kell lennie:
  - Az űrlap bérlője `https://login.microsoftonline.com/{tenant}/` , ahol `tenant` vagy a bérlő azonosítóját, vagy a bérlőhöz társított tartományt jelképező GUID.
  - Bármilyen munkahelyi és iskolai fiókhoz: `https://login.microsoftonline.com/organizations/` .
  - A Microsoft személyes fiókjai nem támogatottak. /Gyakori hibák-vagy/consumers-bérlők nem használhatók.

- Mivel az integrált Windows-hitelesítés csendes folyamat:
  - Az alkalmazás felhasználójának előzőleg el kell juttatnia az alkalmazás használatát.
  - Vagy a bérlői rendszergazdának előzőleg meg kell adnia a bérlő összes felhasználója számára az alkalmazás használatát.
  - Más szóval:
    - A fejlesztőként a Azure Portal a **Grant (Engedélyezés** ) gombot saját maga is kiválasztva.
    - Vagy a bérlői rendszergazda a (z) **{bérlői tartomány} gomb engedélyezés/visszavonás rendszergazdai jóváhagyása** elemét választotta az alkalmazás regisztrációjának **API-engedélyek** lapján. További információ: a [webes API eléréséhez szükséges engedélyek hozzáadása](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).
    - Vagy lehetősége van arra, hogy a felhasználók beleegyezett az alkalmazásba. További információ: [egyéni felhasználói engedély kérése](./v2-permissions-and-consent.md#requesting-individual-user-consent).
    - Vagy megadta a lehetőséget, hogy a bérlői rendszergazda beleegyezett az alkalmazásba. További információ: [rendszergazdai beleegyezett](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

- Ez a folyamat engedélyezve van a .NET Desktop, a .NET Core és a UWP alkalmazásokhoz.

További információ a beleegyező adatokról: a [Microsoft Identity platform engedélyei és beleegyezik](./v2-permissions-and-consent.md).

### <a name="learn-how-to-use-it"></a>Tudnivalók a használatáról

# <a name="net"></a>[.NET](#tab/dotnet)

A MSAL.NET-ben használja a következőket:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Általában csak egy paraméterre () van szükség `scopes` . Attól függően, hogy a Windows-rendszergazda hogyan állította be a házirendeket, előfordulhat, hogy a Windows rendszerű gépén lévő alkalmazások nem jogosultak a bejelentkezett felhasználó keresésére. Ebben az esetben használjon egy második metódust, `.WithUsername()` és adja át a bejelentkezett felhasználó felhasználónevét UPN formátumként, például: `joe@contoso.com` .

A következő minta a legfrissebb eseteket mutatja be, és magyarázatokkal szolgál a beolvasott kivételek típusára és azok enyhítésére.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }


 Console.WriteLine(result.Account.Username);
}
```

A AcquireTokenByIntegratedWindowsAuthentication lehetséges módosítók listájáért lásd: [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Ez a kivonat a [MSAL Java dev-mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)származik.

```Java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Ez a folyamat nem vonatkozik a macOS-re.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ez a folyamat még nem támogatott a MSAL csomópontban.

# <a name="python"></a>[Python](#tab/python)

Ez a folyamat még nem támogatott a MSAL Pythonban.

---

## <a name="username-and-password"></a>Felhasználónév és jelszó

A tokent a Felhasználónév és a jelszó megadásával is megvásárolhatja. Ez a folyamat korlátozott, és nem ajánlott, de továbbra is használhatók a szükséges esetek.

### <a name="this-flow-isnt-recommended"></a>Ez a folyamat nem ajánlott

A Felhasználónév és a jelszó folyamata *nem ajánlott* , mert az alkalmazás arra kéri a felhasználót, hogy a jelszavuk ne legyen biztonságos. További információ: [Mi a megoldás a jelszavak egyre növekvő problémájára?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/) A tokenek Windows-tartományhoz csatlakoztatott gépeken való csendes beszerzésének előnyben részesített folyamata [integrált Windows-hitelesítés](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Integrated-Windows-Authentication). Az [eszköz kódjának folyamatát](https://aka.ms/msal-net-device-code-flow)is használhatja.

A Felhasználónév és a jelszó használata bizonyos esetekben hasznos, például DevOps forgatókönyvek esetén. Ha azonban olyan interaktív helyzetekben szeretné használni a felhasználónevet és a jelszót, ahol a saját felhasználói felületét adja meg, gondolja át, hogyan helyezheti át. Felhasználónév és jelszó használatával több dolgot is megadhat:

- A modern identitás alapvető vonalai. A jelszó beolvasható és visszajátszható, mivel a közös titkos kulcs elfogása megoldható. Nem kompatibilis a jelszóval.
- Az MFA-t nem használó felhasználóknak nem lehet bejelentkezniük, mert nincs interakció.
- A felhasználók nem végezhetnek egyszeri bejelentkezést (SSO).

### <a name="constraints"></a>Korlátozások

A következő korlátozások is érvényesek:

- A Felhasználónév és a jelszó folyamata nem kompatibilis a feltételes hozzáféréssel és a többtényezős hitelesítéssel. Ennek következményeként, ha az alkalmazás egy Azure AD-bérlőn fut, ahol a bérlői rendszergazda a többtényezős hitelesítést igényli, nem használhatja ezt a folyamatot. Számos szervezet ezt teszi.
- Csak a munkahelyi és iskolai fiókok (nem MSA) esetében működik.
- A folyamat elérhető a .NET Desktopban és a .NET Core-on, de nem a UWP-on.

### <a name="b2c-specifics"></a>B2C-sajátosságok

További információ: erőforrás- [tulajdonosi jelszó hitelesítő adatai (ROPC) a B2C-vel](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics#resource-owner-password-credentials-ropc-with-b2c).

### <a name="use-it"></a>Használat

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`a metódust tartalmazza `AcquireTokenByUsernamePassword` .

Az alábbi példa egyszerűsített esetet mutat be.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
       .WithAuthority(authority)
       .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
}
```

A következő minta a legfrissebb eseteket mutatja be, és magyarázatokkal szolgál a beolvasott kivételek típusára és azok enyhítésére.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
                                   .WithAuthority(authority)
                                   .Build();
 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
                    .ExecuteAsync();
 }
 else
 {
  try
  {
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password keystroke
    securePassword.AppendChar(c);  // by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                    securePassword)
                    .ExecuteAsync();
  }
  catch (MsalUiRequiredException ex) when (ex.Message.Contains("AADSTS65001"))
  {
   // Here are the kind of error messages you could have, and possible mitigations

   // ------------------------------------------------------------------------
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'. Send an interactive authorization request for this user and resource.

   // Mitigation: you need to get user consent first. This can be done either statically (through the portal),
   /// or dynamically (but this requires an interaction with Azure AD, which is not possible with
   // the username/password flow)
   // Statically: in the portal by doing the following in the "API permissions" tab of the application registration:
   // 1. Click "Add a permission" and add all the delegated permissions corresponding to the scopes you want (for instance
   // User.Read and User.ReadBasic.All)
   // 2. Click "Grant/revoke admin consent for <tenant>") and click "yes".
   // Dynamically, if you are not using .NET Core (which does not have any Web UI) by
   // calling (once only) AcquireTokenInteractive.
   // remember that Username/password is for public client applications that is desktop/mobile applications.
   // If you are using .NET core or don't want to call AcquireTokenInteractive, you might want to:
   // - use device code flow (See https://aka.ms/msal-net-device-code-flow)
   // - or suggest the user to navigate to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ErrorCode: invalid_grant
   // SubError: basic_action
   // MsalUiRequiredException: AADSTS50079: The user is required to use multi-factor authentication.
   // The tenant admin for your organization has chosen to oblige users to perform multi-factor authentication.
   // Mitigation: none for this flow
   // Your application cannot use the Username/Password grant.
   // Like in the previous case, you might want to use an interactive flow (AcquireTokenInteractive()),
   // or Device Code Flow instead.
   // Note this is one of the reason why using username/password is not recommended;
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // Message = "AADSTS70002: Error validating credentials.
   // AADSTS50126: Invalid username or password
   // In the case of a managed user (user from an Azure AD tenant opposed to a
   // federated user, which would be owned
   // in another IdP through ADFS), the user has entered the wrong password
   // Mitigation: ask the user to re-enter the password
   // ------------------------------------------------------------------------

   // ------------------------------------------------------------------------
   // ex.ErrorCode: invalid_grant
   // subError: null
   // MsalServiceException: ADSTS50034: To sign into this application the account must be added to
   // the {domainName} directory.
   // or The user account does not exist in the {domainName} directory. To sign into this application,
   // the account must be added to the directory.
   // The user was not found in the directory
   // Explanation: wrong username
   // Mitigation: ask the user to re-enter the username.
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_request")
  {
   // ------------------------------------------------------------------------
   // AADSTS90010: The grant type is not supported over the /common or /consumers endpoints.
   // Please use the /organizations or tenant-specific endpoint.
   // you used common.
   // Mitigation: as explained in the message from Azure AD, the authority you use in the application needs
   // to be tenanted or otherwise "organizations". change the
   // "Tenant": property in the appsettings.json to be a GUID (tenant Id), or domain name (contoso.com)
   // if such a domain is registered with your tenant
   // or "organizations", if you want this application to sign-in users in any Work and School accounts.
   // ------------------------------------------------------------------------

  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "unauthorized_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS700016: Application with identifier '{clientId}' was not found in the directory '{domain}'.
   // This can happen if the application has not been installed by the administrator of the tenant or consented
   // to by any user in the tenant.
   // You may have sent your authentication request to the wrong tenant
   // Cause: The clientId in the appsettings.json might be wrong
   // Mitigation: check the clientId and the app registration
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException ex) when (ex.ErrorCode == "invalid_client")
  {
   // ------------------------------------------------------------------------
   // AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
   // Explanation: this can happen if your application was not registered as a public client application in Azure AD
   // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   // ------------------------------------------------------------------------
  }
  catch (MsalServiceException)
  {
   throw;
  }

  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user_type")
  {
   // Message = "Unsupported User Type 'Unknown'. Please see https://aka.ms/msal-net-up"
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "user_realm_discovery_failed")
  {
   // The user is not recognized as a managed user, or a federated user. Azure AD was not
   // able to identify the IdP that needs to process the user. That's for instance the case
   // if you use a phone number
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "unknown_user")
  {
   // the username was probably empty
   // ex.Message = "Could not identify the user logged into the OS. See https://aka.ms/msal-net-iwa for details."
   throw new ArgumentException("U/P: Wrong username", ex);
  }
  catch (MsalClientException ex) when (ex.ErrorCode == "parsing_wstrust_response_failed")
  {
   // ------------------------------------------------------------------------
   // In the case of a Federated user (that is owned by a federated IdP, as opposed to a managed user owned in an Azure AD tenant)
   // ID3242: The security token could not be authenticated or authorized.
   // The user does not exist or has entered the wrong password
   // ------------------------------------------------------------------------
  }
 }

 Console.WriteLine(result.Account.Username);
}
```

További információ az összes alkalmazható módosítóról `AcquireTokenByUsernamePassword` : [AcquireTokenByUsernamePasswordParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyusernamepasswordparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

A következő kivonat a [MSAL Java dev-mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)származik.

```Java
private static IAuthenticationResult acquireTokenUsernamePassword() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();
        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            UserNamePasswordParameters parameters =
                    UserNamePasswordParameters
                            .builder(SCOPE, USER_NAME, USER_PASSWORD.toCharArray())
                            .build();
            // Try to acquire a token via username/password. If successful, you should see
            // the token and account information printed out to console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Ez a folyamat nem támogatott a macOS-MSAL.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ez a kivonat a [MSAL Node dev-mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/username-password)származik. Az alábbi kódrészletben a Felhasználónév és a jelszó csak illusztrációs célokra hardcoded. Ezt el kell kerülni éles környezetben. Ehelyett egy alapszintű felhasználói felület, amely felszólítja a felhasználót, hogy adja meg felhasználónevét vagy jelszavát, ajánlott. 

```JavaScript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

// For testing, enter your username and password below.
// In production, replace this with a UI prompt instead.
const usernamePasswordRequest = {
    scopes: ["user.read"],
    username: "", // Add your username here
    password: "", // Add your password here
};

pca.acquireTokenByUsernamePassword(usernamePasswordRequest).then((response) => {
    console.log("acquired token by password grant");
}).catch((error) => {
    console.log(error);
});
```

# <a name="python"></a>[Python](#tab/python)

Ez a kivonat a [MSAL Python dev-mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)származik.

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    # See this page for constraints of Username Password Flow.
    # https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Username-Password-Authentication
    result = app.acquire_token_by_username_password(
        config["username"], config["password"], scopes=config["scope"])
```

---

## <a name="command-line-tool-without-a-web-browser"></a>Webböngésző nélküli parancssori eszköz

### <a name="device-code-flow"></a>Eszköz kódjának folyamata

Ha olyan parancssori eszközt ír, amely nem rendelkezik webes vezérlőkkel, és nem szeretné használni az előző folyamatokat, használja az eszköz kódját.

Az Azure AD-vel való interaktív hitelesítéshez webböngésző szükséges. További információ: [böngészők használata](https://aka.ms/msal-net-uses-web-browser). Ha olyan eszközökön vagy operációs rendszereken szeretné hitelesíteni a felhasználókat, amelyek nem biztosítanak webböngészőt, az eszköz kódjának folyamata lehetővé teszi, hogy a felhasználó egy másik eszközt, például egy számítógépet vagy egy mobiltelefont használjon interaktív bejelentkezéshez. Az eszköz kódjának használatával az alkalmazás egy kétlépéses folyamaton keresztül szerzi be a jogkivonatokat, amelyeket ezekhez az eszközökhöz vagy operációs rendszerekhez terveztek. Ilyen alkalmazások például a iOT vagy parancssori eszközökön (CLI) futó alkalmazások. Az a gondolat, hogy:

1. Ha felhasználói hitelesítésre van szükség, az alkalmazás kódot biztosít a felhasználó számára. A felhasználónak meg kell adnia egy másik eszközt, például egy internetkapcsolattal rendelkező okostelefont, hogy megnyissa az URL-címet (például: `https://microsoft.com/devicelogin` ). Ezután a rendszer megkéri a felhasználót a kód megadására. Ennek megfelelően a weblap egy normál hitelesítési felülettel vezeti a felhasználót, és szükség esetén beletartozik a hozzájárulási kérések és a többtényezős hitelesítés.

2. A sikeres hitelesítés után a parancssori alkalmazás egy visszaadott csatornán keresztül fogadja a szükséges jogkivonatokat, és a használatával a szükséges webes API-hívásokat hajtja végre.

### <a name="use-it"></a>Használat

# <a name="net"></a>[.NET](#tab/dotnet)

`IPublicClientApplication`egy nevű metódust tartalmaz `AcquireTokenWithDeviceCode` .

```csharp
 AcquireTokenWithDeviceCode(IEnumerable<string> scopes,
                            Func<DeviceCodeResult, Task> deviceCodeResultCallback)
```

A metódus a következő paramétereket veszi figyelembe:

- A `scopes` () hozzáférési jogkivonatának kérése a következőhöz:.
- A-t fogadó visszahívás `DeviceCodeResult` .

  ![DeviceCodeResult tulajdonságai](https://user-images.githubusercontent.com/13203188/56024968-7af1b980-5d11-11e9-84c2-5be2ef306dc5.png)

Az alábbi mintakód a legtöbb aktuális eset összegzését mutatja be, és magyarázattal szolgál a beolvasott kivételek típusáról és azok enyhítéséről. A teljes funkcionalitású mintakód: [Active-Directory-dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) a githubon.

```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```

# <a name="java"></a>[Java](#tab/java)

Ez a kivonat a [MSAL Java dev-mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/)származik.

```java
private static IAuthenticationResult acquireTokenDeviceCode() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            Consumer<DeviceCode> deviceCodeConsumer = (DeviceCode deviceCode) ->
                    System.out.println(deviceCode.message());

            DeviceCodeFlowParameters parameters =
                    DeviceCodeFlowParameters
                            .builder(SCOPE, deviceCodeConsumer)
                            .build();

            // Try to acquire a token via device code flow. If successful, you should see
            // the token and account information printed out to console, and the sample_cache.json
            // file should have been updated with the latest tokens.
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Ez a folyamat nem vonatkozik a macOS-re.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ez a kivonat a [MSAL Node dev-mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/device-code)származik.

```JavaScript
const msal = require('@azure/msal-node');

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const deviceCodeRequest = {
    deviceCodeCallback: (response) => (console.log(response.message)),
    scopes: ["user.read"],
    timeout: 20,
};

pca.acquireTokenByDeviceCode(deviceCodeRequest).then((response) => {
    console.log(JSON.stringify(response));
}).catch((error) => {
    console.log(JSON.stringify(error));
});
```

# <a name="python"></a>[Python](#tab/python)

Ez a kivonat a [MSAL Python dev-mintákból](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/dev/sample/)származik.

```Python
# Create a preferably long-lived app instance which maintains a token cache.
app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )

# The pattern to acquire a token looks like this.
result = None

# Note: If your device-flow app does not have any interactive ability, you can
#   completely skip the following cache part. But here we demonstrate it anyway.
# We now check the cache to see if we have some end users signed in before.
accounts = app.get_accounts()
if accounts:
    logging.info("Account(s) exists in cache, probably with token too. Let's try.")
    print("Pick the account you want to use to proceed:")
    for a in accounts:
        print(a["username"])
    # Assuming the end user chose this one
    chosen = accounts[0]
    # Now let's try to find a token in cache for this account
    result = app.acquire_token_silent(config["scope"], account=chosen)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")

    flow = app.initiate_device_flow(scopes=config["scope"])
    if "user_code" not in flow:
        raise ValueError(
            "Fail to create device flow. Err: %s" % json.dumps(flow, indent=4))

    print(flow["message"])
    sys.stdout.flush()  # Some terminal needs this to ensure the message is shown

    # Ideally you should wait here, in order to save some unnecessary polling
    # input("Press Enter after signing in from another device to proceed, CTRL+C to abort.")

    result = app.acquire_token_by_device_flow(flow)  # By default it will block
        # You can follow this instruction to shorten the block time
        #    https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow
        # or you may even turn off the blocking behavior,
        # and then keep calling acquire_token_by_device_flow(flow) in your own customized loop
```

---

## <a name="file-based-token-cache"></a>Fájl alapú jogkivonat-gyorsítótár

A MSAL.NET-ben alapértelmezés szerint a memóriában tárolt jogkivonat-gyorsítótár van megadva.

### <a name="serialization-is-customizable-in-windows-desktop-apps-and-web-apps-or-web-apis"></a>A szerializálás testreszabható a Windows asztali alkalmazásokban és webalkalmazásokban, illetve webes API-kon

Ha a .NET-keretrendszer és a .NET Core esetében nem csinál semmit, a memóriában tárolt jogkivonat-gyorsítótár az alkalmazás időtartamára tart. Annak megismeréséhez, hogy a szerializálás miért nem áll rendelkezésre a dobozból, ne feledje, hogy a MSAL .NET Desktop vagy .NET Core alkalmazások lehetnek konzolos vagy Windows-alkalmazások (amelyek hozzáférhetnek a fájlrendszerhez), de webalkalmazásokhoz vagy webes API-khoz *is* . Ezek a webalkalmazások és webes API-k bizonyos gyorsítótárazási mechanizmusokat, például adatbázisokat, elosztott gyorsítótárat és Redis gyorsítótárat használhatnak. Ha állandó jogkivonat-gyorsítótárazási alkalmazást szeretne telepíteni a .NET Desktopban vagy a .NET Core-ban, testre kell szabnia a szerializálást.

A jogkivonat-gyorsítótár szerializálásakor részt vevő osztályok és felületek a következők:

- ``ITokenCache``, amely a jogkivonat-gyorsítótár szerializálási kéréseire előfizetett eseményeket, valamint a gyorsítótár szerializálására vagy deszerializálására szolgáló metódusokat határozza meg különböző formátumokban (ADAL v 3.0, MSAL 2. x és MSAL 3. x = ADAL v 5.0).
- ``TokenCacheCallback`` az eseményeknek átadott visszahívás, hogy kezelni tudja a szerializálást. A rendszer a típusú argumentumokkal hívja meg őket ``TokenCacheNotificationArgs`` .
- ``TokenCacheNotificationArgs`` csak az alkalmazást és annak a felhasználónak a hivatkozását adja meg, ``ClientId`` amelyhez a jogkivonat elérhető.

  ![Jogkivonat-gyorsítótár szerializálási diagramja](https://user-images.githubusercontent.com/13203188/56027172-d58d1480-5d15-11e9-8ada-c0292f1800b3.png)

> [!IMPORTANT]
> A MSAL.NET jogkivonat-gyorsítótárat hoz létre az Ön számára, és megadja a `IToken` gyorsítótárat az alkalmazás és a tulajdonságok meghívásakor `UserTokenCache` `AppTokenCache` . Nem kell önállóan megvalósítani a felületet. Az egyéni jogkivonat-gyorsítótár szerializálásának megvalósításakor a következőt kell tennie:
>
> - Reagáljon a `BeforeAccess` -ra és az `AfterAccess` eseményekre, vagy az *aszinkron* társaira. A `BeforeAccess` delegált feladata a gyorsítótár deszerializálása. A `AfterAccess` delegált feladata a gyorsítótár szerializálásának elvégzése.
> - Ismerje meg, hogy ezeknek az eseményeknek a része tárolja vagy betölti a blobokat, amelyeket az Event argumentumon át kell adni a kívánt tárterülethez.

A stratégiák eltérőek attól függően, hogy egy nyilvános ügyfélalkalmazás, például egy asztali számítógép vagy egy bizalmas ügyfélalkalmazás (például egy webalkalmazás vagy webes API vagy egy démon-alkalmazás) esetében a jogkivonat-gyorsítótár szerializálását írja-e.

A MSAL v2. x óta számos lehetőség közül választhat. Az Ön választása attól függ, hogy csak a MSAL.NET-formátumra kívánja szerializálni a gyorsítótárat, amely egységes formátumú gyorsítótár, amely közös a MSAL, hanem a platformok között is. Az is előfordulhat, hogy támogatni kívánja az [örökölt](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) jogkivonat-gyorsítótár ADAL v3-es szerializálását is.

A jogkivonat-gyorsítótár szerializálásának testreszabásával megoszthatja a 3. x, a ADAL.NET 5. x és a MSAL.NET közötti egyszeri bejelentkezéses állapotot a ADAL.NET ( [Active Directory – DotNet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2)) részben leírtak szerint.

### <a name="simple-token-cache-serialization-msal-only"></a>Egyszerű jogkivonat-gyorsítótár szerializálása (csak MSAL)

Az alábbi példa egy jogkivonat-gyorsítótár egyéni szerializálásának naiv implementációja asztali alkalmazásokhoz. Itt a felhasználói jogkivonat gyorsítótára ugyanabban a mappában található, mint az alkalmazás, vagy a felhasználónkénti felhasználónkénti mappája abban az esetben, ha az alkalmazás egy [csomagolt asztali alkalmazás](/windows/msix/desktop/desktop-to-uwp-behind-the-scenes). A teljes kóddal kapcsolatban tekintse meg a következő mintát: [Active-Directory-DotNet-Desktop-msgraph-v2](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2).

Az alkalmazás létrehozása után az alkalmazás meghívásával és átadásával engedélyezheti a szerializálást ``TokenCacheHelper.EnableSerialization()`` `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

Ez a segítő osztály a következő kódrészlethez hasonlít:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
   try
   {
    // For packaged desktop apps (MSIX packages) the executing assembly folder is read-only. 
    // In that case we need to use Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path + "\msalcache.bin" 
    // which is a per-app read/write folder for packaged apps.
    // See https://docs.microsoft.com/windows/msix/desktop/desktop-to-uwp-behind-the-scenes
    CacheFilePath = System.IO.Path.Combine(Windows.Storage.ApplicationData.Current.LocalCacheFolder.Path, "msalcache.bin3");
   }
   catch (System.InvalidOperationException)
   {
    // Fall back for an un-packaged desktop app
    CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin";
   }
  }

  /// <summary>
  /// Path to the token cache
  /// </summary>
  public static string CacheFilePath { get; private set; }

  private static readonly object FileLock = new object();

  private static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeMsalV3(File.Exists(CacheFilePath)
            ? ProtectedData.Unprotect(File.ReadAllBytes(CacheFilePath),
                                      null,
                                      DataProtectionScope.CurrentUser)
            : null);
   }
  }

  private static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     // reflect changesgs in the persistent store
     File.WriteAllBytes(CacheFilePath,
                         ProtectedData.Protect(args.TokenCache.SerializeMsalV3(),
                                                 null,
                                                 DataProtectionScope.CurrentUser)
                         );
    }
   }
  }
 }
```

A Windows, Mac és Linux rendszerű asztali alkalmazásokhoz készült, a [Microsoft. Identity. Client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) nyílt forráskódú függvénytárában a termék minőségi jogkivonat-gyorsítótárának előzetes verziója érhető el a nyilvános ügyfélalkalmazások számára. Az alkalmazásokban a következő NuGet-csomaggal veheti fel: [Microsoft. Identity. Client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

> [!NOTE]
> Jogi nyilatkozat: a Microsoft. Identity. Client. Extensions. Msal függvénytár a MSAL.NET-en keresztüli kiterjesztés. Az ezekben a tárakban lévő osztályok a jövőben MSAL.NET a későbbiekben, ahogy az vagy a változtatások miatt.

### <a name="dual-token-cache-serialization-msal-unified-cache--adal-v3"></a>Kettős jogkivonat gyorsítótár-szerializálása (MSAL Unified cache + ADAL v3)

Előfordulhat, hogy a jogkivonat-gyorsítótár szerializálását az egyesített gyorsítótár formátumával szeretné megvalósítani. Ez a formátum közös a 4. x és a MSAL.NET 2. x ADAL.NET, valamint ugyanazon generáció vagy régebbi MSALs ugyanazon a platformon. A Get a következő kód ihlette:

```csharp
string appLocation = Path.GetDirectoryName(Assembly.GetEntryAssembly().Location;
string cacheFolder = Path.GetFullPath(appLocation) + @"..\..\..\..");
string adalV3cacheFileName = Path.Combine(cacheFolder, "cacheAdalV3.bin");
string unifiedCacheFileName = Path.Combine(cacheFolder, "unifiedCache.bin");

IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
                                    .Build();
FilesBasedTokenCacheHelper.EnableSerialization(app.UserTokenCache,
                                               unifiedCacheFileName,
                                               adalV3cacheFileName);

```

Ezúttal a segítő osztály a következő kódhoz hasonlít:

```csharp
using System;
using System.IO;
using System.Security.Cryptography;
using Microsoft.Identity.Client;

namespace CommonCacheMsalV3
{
 /// <summary>
 /// Simple persistent cache implementation of the dual cache serialization (ADAL V3 legacy
 /// and unified cache format) for a desktop applications (from MSAL 2.x)
 /// </summary>
 static class FilesBasedTokenCacheHelper
 {
  /// <summary>
  /// Get the user token cache
  /// </summary>
  /// <param name="adalV3CacheFileName">File name where the cache is serialized with the
  /// ADAL V3 token cache format. Can
  /// be <c>null</c> if you don't want to implement the legacy ADAL V3 token cache
  /// serialization in your MSAL 2.x+ application</param>
  /// <param name="unifiedCacheFileName">File name where the cache is serialized
  /// with the Unified cache format, common to
  /// ADAL V4 and MSAL V2 and above, and also across ADAL/MSAL on the same platform.
  ///  Should not be <c>null</c></param>
  /// <returns></returns>
  public static void EnableSerialization(ITokenCache cache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   cache.SetBeforeAccess(BeforeAccessNotification);
   cache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// File path where the token cache is serialized with the unified cache format
  /// (ADAL.NET V4, MSAL.NET V3)
  /// </summary>
  public static string UnifiedCacheFileName { get; private set; }

  /// <summary>
  /// File path where the token cache is serialized with the legacy ADAL V3 format
  /// </summary>
  public static string AdalV3CacheFileName { get; private set; }

  private static readonly object FileLock = new object();

  public static void BeforeAccessNotification(TokenCacheNotificationArgs args)
  {
   lock (FileLock)
   {
    args.TokenCache.DeserializeAdalV3(ReadFromFileIfExists(AdalV3CacheFileName));
    try
    {
     args.TokenCache.DeserializeMsalV3(ReadFromFileIfExists(UnifiedCacheFileName));
    }
    catch(Exception ex)
    {
     // Compatibility with the MSAL v2 cache if you used one
     args.TokenCache.DeserializeMsalV2(ReadFromFileIfExists(UnifiedCacheFileName));
    }
   }
  }

  public static void AfterAccessNotification(TokenCacheNotificationArgs args)
  {
   // if the access operation resulted in a cache update
   if (args.HasStateChanged)
   {
    lock (FileLock)
    {
     WriteToFileIfNotNull(UnifiedCacheFileName, args.TokenCache.SerializeMsalV3());
     if (!string.IsNullOrWhiteSpace(AdalV3CacheFileName))
     {
      WriteToFileIfNotNull(AdalV3CacheFileName, args.TokenCache.SerializeAdalV3());
     }
    }
   }
  }

  /// <summary>
  /// Read the content of a file if it exists
  /// </summary>
  /// <param name="path">File path</param>
  /// <returns>Content of the file (in bytes)</returns>
  private static byte[] ReadFromFileIfExists(string path)
  {
   byte[] protectedBytes = (!string.IsNullOrEmpty(path) && File.Exists(path))
       ? File.ReadAllBytes(path) : null;
   byte[] unprotectedBytes = encrypt ?
       ((protectedBytes != null) ? ProtectedData.Unprotect(protectedBytes, null, DataProtectionScope.CurrentUser) : null)
       : protectedBytes;
   return unprotectedBytes;
  }

  /// <summary>
  /// Writes a blob of bytes to a file. If the blob is <c>null</c>, deletes the file
  /// </summary>
  /// <param name="path">path to the file to write</param>
  /// <param name="blob">Blob of bytes to write</param>
  private static void WriteToFileIfNotNull(string path, byte[] blob)
  {
   if (blob != null)
   {
    byte[] protectedBytes = encrypt
      ? ProtectedData.Protect(blob, null, DataProtectionScope.CurrentUser)
      : blob;
    File.WriteAllBytes(path, protectedBytes);
   }
   else
   {
    File.Delete(path);
   }
  }

  // Change if you want to test with an un-encrypted blob (this is a json format)
  private static bool encrypt = true;
 }
}
```

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>Speciális A felhasználó gyorsítótárazott jogkivonatának elérése a háttérben futó alkalmazásokban és szolgáltatásokban

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a forgatókönyvben a következő cikkre léphet, és [meghívja a webes API-t az asztali alkalmazásból](scenario-desktop-call-api.md).
