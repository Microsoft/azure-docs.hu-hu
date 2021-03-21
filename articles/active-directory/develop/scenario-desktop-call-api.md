---
title: Webes API-k meghívása asztali alkalmazásból | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó asztali alkalmazást
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
ms.openlocfilehash: 51619fe049d580c21b338af2f86b2afea6765b04
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756531"
---
# <a name="desktop-app-that-calls-web-apis-call-a-web-api"></a>Webes API-kat meghívó asztali alkalmazás: webes API meghívása

Most, hogy rendelkezik egy jogkivonattal, meghívhat egy védett webes API-t.

## <a name="call-a-web-api"></a>Webes API-hívás

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

<!--
More includes will come later for Python and Java
-->
# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="macos"></a>[MacOS](#tab/macOS)

## <a name="call-a-web-api-in-msal-for-ios-and-macos"></a>Webes API meghívása iOS és macOS rendszerű MSAL

A jogkivonatok beszerzésének módszerei egy objektumot adnak vissza `MSALResult` . `MSALResult` egy olyan `accessToken` tulajdonság közzététele, amely a webes API-k meghívására használható. A védett webes API-hoz való hozzáférés meghívása előtt adjon hozzá egy hozzáférési jogkivonatot a HTTP-engedélyezési fejléchez.

Objective-C:

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

Swift

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]

let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="call-several-apis-incremental-consent-and-conditional-access"></a>Több API meghívása: növekményes beleegyezettés és feltételes hozzáférés

Ha több API-t szeretne meghívni ugyanahhoz a felhasználóhoz, az első API-hoz tartozó jogkivonat beszerzése után hívja meg a következőt: `AcquireTokenSilent` . A többi API-hoz az idő nagy részében csendes tokent kap.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

A beavatkozásra akkor van szükség, ha:

- A felhasználó jóváhagyta az első API-t, de most hozzá kell járulnia további hatókörökhöz. Az ilyen típusú beleegyező beleegyező beleegyezett.
- Az első API-nak nem volt szükség többtényezős hitelesítésre, de a következő lépés.

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
---

## <a name="next-steps"></a>Következő lépések

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](scenario-desktop-production.md).
