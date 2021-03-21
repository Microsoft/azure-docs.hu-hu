---
title: Webes API-kat meghívó asztali alkalmazások konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Útmutató a webes API-kat meghívó asztali alkalmazások kódjának konfigurálásához
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 372efa217e400ffd4bcecd286f3c3dc7b81f64d6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007964"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Webes API-kat meghívó asztali alkalmazás: kód konfigurálása

Most, hogy létrehozta az alkalmazást, megtudhatja, hogyan konfigurálhatja a kódot az alkalmazás koordinátáival.

## <a name="microsoft-libraries-supporting-desktop-apps"></a>Asztali alkalmazásokat támogató Microsoft-kódtárak

A következő Microsoft-kódtárak támogatják az asztali alkalmazásokat:

[!INCLUDE [active-directory-develop-libraries-desktop](../../../includes/active-directory-develop-libraries-desktop.md)]

## <a name="public-client-application"></a>Nyilvános ügyfélalkalmazás

A kód szempontjából az asztali alkalmazások nyilvános ügyfélalkalmazások. A konfiguráció egy kicsit különbözik attól függően, hogy interaktív hitelesítést használ-e, vagy sem.

# <a name="net"></a>[.NET](#tab/dotnet)

Létre kell hoznia és módosítania kell a MSAL.NET `IPublicClientApplication` .

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Kizárólag kód alapján

A következő kód egy nyilvános ügyfélalkalmazás példányát és a Microsoft Azure nyilvános felhőben lévő felhasználók munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók való bejelentését írja elő.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Ha a korábban látott interaktív hitelesítést vagy az eszköz kódját szeretné használni, használja a `.WithRedirectUri` módosítót.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Konfigurációs fájlok használata

A következő kód egy nyilvános ügyfélalkalmazás példányát egy konfigurációs objektumból hozza létre, amely programozott módon vagy egy konfigurációs fájlból is kitölthető.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Részletesebb konfiguráció

Az alkalmazás kiépítése több módosítóval is kiegészíthető. Ha például azt szeretné, hogy az alkalmazás egy nemzeti felhőben több-bérlős alkalmazás legyen, például az USA kormánya itt látható, akkor a következőket írhatja:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

A MSAL.NET Active Directory összevonási szolgáltatások (AD FS) 2019-es módosítót is tartalmaz:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Végül, ha az Azure Active Directory (Azure AD) B2C-bérlőhöz jogkivonatokat szeretne beszerezni, adja meg a bérlőt az alábbi kódrészletben látható módon:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Tudjon meg többet

További információ a MSAL.NET asztali alkalmazások konfigurálásáról:

- Az összes elérhető módosító listájának `PublicClientApplicationBuilder` megtekintéséhez tekintse meg a Reference dokumentáció [PublicClientApplicationBuilder](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- A alkalmazásban elérhető összes beállítás leírását `PublicClientApplicationOptions` lásd a [PublicClientApplicationOptions](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) című témakörben.

### <a name="complete-example-with-configuration-options"></a>Példa teljes konfigurációs beállításokkal

Képzeljünk el egy .NET Core Console-alkalmazást, amely a következő `appsettings.json` konfigurációs fájllal rendelkezik:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Ennek a fájlnak a használatával kevés kódot kell beolvasnia. NET által biztosított konfigurációs keretrendszer:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint configuration
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Az alkalmazás létrehozásához írja be a következő kódot:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

A metódus hívása előtt `.Build()` felülbírálhatja a konfigurációt `.WithXXX` metódusok hívásával, ahogy azt korábban is láttuk.

# <a name="java"></a>[Java](#tab/java)

A MSAL Java-fejlesztési mintákban használt osztály a következő mintákat konfigurálja: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[MacOS](#tab/macOS)

A következő kód egy nyilvános ügyfélalkalmazás példányát és a Microsoft Azure nyilvános felhőben lévő felhasználók munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók való bejelentését írja elő.

### <a name="quick-configuration"></a>Gyors konfigurálás

Objective-C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Részletesebb konfiguráció

Az alkalmazás kiépítése több módosítóval is kiegészíthető. Ha például azt szeretné, hogy az alkalmazás egy nemzeti felhőben több-bérlős alkalmazás legyen, például az USA kormánya itt látható, akkor a következőket írhatja:

Objective-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Következő lépések

Ebben a forgatókönyvben a következő cikkre léphet be, amely [egy jogkivonat beszerzését kéri az asztali alkalmazás számára](scenario-desktop-acquire-token.md).