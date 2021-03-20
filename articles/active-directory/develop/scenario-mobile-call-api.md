---
title: Webes API meghívása Mobile-alkalmazásból | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó mobil alkalmazást. (Webes API meghívása)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 656cb167fb47e44c28922afed75d8c46a460aaf1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584397"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Webes API meghívása Mobile-alkalmazásból

Miután az alkalmazás bejelentkezett egy felhasználónak, és jogkivonatokat kap, a Microsoft Authentication Library (MSAL) a felhasználóval, a felhasználó környezetével és a kiállított jogkivonatokkal kapcsolatos információkat jeleníti meg. Az alkalmazás ezeket az értékeket használhatja a webes API-k meghívásához vagy egy üdvözlő üzenet megjelenítéséhez a felhasználónak.

Ebben a cikkben a MSAL eredményét fogjuk megtekinteni. Ezután megvizsgáljuk, hogyan használhat hozzáférési tokent a `AuthenticationResult` vagy `result` a használatával egy védett webes API-t.

## <a name="msal-result"></a>MSAL eredménye
A MSAL a következő értékeket biztosítja: 

- `AccessToken` egy HTTP-tulajdonosi kérelemben meghívja a védett webes API-kat.
- `IdToken` hasznos információkat tartalmaz a bejelentkezett felhasználóról. Ez az információ tartalmazza a felhasználó nevét, a Kezdőlap bérlőjét és a tárhely egyedi azonosítóját.
- `ExpiresOn` a jogkivonat lejárati ideje. A MSAL kezeli az alkalmazások automatikus frissítését.
- `TenantId` annak a bérlőnek az azonosítója, amelybe a felhasználó bejelentkezett. Azure Active Directory (Azure AD) B2B-ben lévő vendég felhasználók esetében ez az érték azonosítja azt a bérlőt, ahol a felhasználó bejelentkezett. Az érték nem azonosítja a felhasználó otthoni bérlőjét.  
- `Scopes` a tokenhez megadott hatóköröket jelzi. A megadott hatókörök lehetnek a kért hatókörök részhalmazai.

A MSAL egy érték absztrakcióját is biztosítja `Account` . Az `Account` érték az aktuális felhasználó bejelentkezett fiókját jelöli:

- `HomeAccountIdentifier` a felhasználó otthoni bérlőjét azonosítja.
- `UserName` a felhasználó elsődleges felhasználóneve. Ez az érték Azure AD B2C felhasználók számára üres lehet.
- `AccountIdentifier` azonosítja a bejelentkezett felhasználót. A legtöbb esetben ez az érték megegyezik az `HomeAccountIdentifier` értékkel, kivéve, ha a felhasználó egy másik bérlőben található vendég.

## <a name="call-an-api"></a>API meghívása

A hozzáférési jogkivonat meghívása után meghívhat egy webes API-t. Az alkalmazás a token használatával létrehoz egy HTTP-kérelmet, majd futtatja a kérést.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="msal-for-ios-and-macos"></a>MSAL iOS és macOS rendszerekre

A jogkivonatok beszerzésének módszerei egy objektumot adnak vissza `MSALResult` . `MSALResult` egy tulajdonság közzététele `accessToken` . A használatával `accessToken` webes API-t hívhat meg. A védett webes API elérésének meghívása előtt adja hozzá ezt a tulajdonságot a HTTP-engedélyezési fejléchez.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Több API-kérés készítése

Ha többször is meg szeretné hívni ugyanazt az API-t, vagy több API-t hív meg, akkor az alkalmazás létrehozásakor vegye figyelembe a következő témákat:

- **Növekményes beleegyezett**: a Microsoft Identity platform lehetővé teszi az alkalmazások számára, hogy felhasználói hozzájárulásukat kapjanak, ha nem az összes indításkor szükséges engedélyekkel rendelkeznek. Minden alkalommal, amikor az alkalmazás készen áll egy API meghívására, csak a szükséges hatóköröket kell kérnie.

- **Feltételes hozzáférés**: Ha több API-kérést hajt végre, akkor előfordulhat, hogy bizonyos helyzetekben további feltételes hozzáférési követelményeknek kell megfelelnie. A követelmények ily módon növekednek, ha az első kérelem nem rendelkezik feltételes hozzáférési házirendekkel, és az alkalmazás egy feltételes hozzáférést igénylő új API csendes elérését kísérli meg. A probléma kezeléséhez ügyeljen arra, hogy a csendes kérelmekkel kapcsolatos hibákat észlelje, és készüljön fel egy interaktív kérelem elvégzésére.  További információ: [útmutató a feltételes hozzáféréshez](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Több API meghívása növekményes beleegyezettés és feltételes hozzáférés használatával

Ha több API-t szeretne meghívni ugyanahhoz a felhasználóhoz, a jogkivonat beszerzése után a felhasználó számára többször is megkérdezheti a hitelesítő adatokat, ha később a jogkivonat beszerzését kéri `AcquireTokenSilent` :

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

A beavatkozásra akkor van szükség, ha:

- A felhasználó jóváhagyta az első API-t, de most hozzá kell járulnia további hatókörökhöz. Ebben az esetben növekményes beleegyezett.
- Az első API-nak nincs szüksége a [többtényezős hitelesítésre](../authentication/concept-mfa-howitworks.md), de a következő API-t használja.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Következő lépések

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](scenario-mobile-production.md).
