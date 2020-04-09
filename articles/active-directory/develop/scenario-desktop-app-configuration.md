---
title: Webes API-kat hívó asztali alkalmazások konfigurálása – Microsoft identity platform | Azure
description: A webes API-kat meghívjaó asztali alkalmazások kódjának konfigurálása
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
ms.openlocfilehash: f6cd9e17045fc400d32e9822cef870f1763179ab
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885262"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Webes API-kat meghívjaó asztali alkalmazás: Kódkonfiguráció

Most, hogy létrehozta az alkalmazást, megtudhatja, hogyan konfigurálhatja a kódot az alkalmazás koordinátáival.

## <a name="microsoft-authentication-libraries"></a>Microsoft hitelesítést árak

A következő Microsoft authentication libraries (MSALs) támogatja az asztali alkalmazásokat.

  Microsoft Authentication Library | Leírás
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Támogatja az asztali alkalmazások készítését több platformon, például Linux, Windows és macOS rendszerben.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Támogatja az asztali alkalmazások több platformon történő készítését.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Támogatja az asztali alkalmazások több platformon történő készítését.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Támogatja a csak macOS rendszeren futó asztali alkalmazásokat.

## <a name="public-client-application"></a>Nyilvános ügyfélalkalmazás

Kódszempontjából az asztali alkalmazások nyilvános ügyfélalkalmazások. A konfiguráció egy kicsit más lesz attól függően, hogy interaktív hitelesítést használ-e vagy sem.

# <a name="net"></a>[.NET](#tab/dotnet)

Meg kell építeni, és manipulálni `IPublicClientApplication`MSAL.NET .

![IPublicClientAlkalmazás](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Kizárólag kód szerint

A következő kód egy nyilvános ügyfélalkalmazást hoz össze, és bejelentkezik a felhasználókba a Microsoft Azure nyilvános felhőben egy munkahelyi vagy iskolai fiókkal vagy egy személyes Microsoft-fiókkal.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Ha interaktív hitelesítést vagy eszközkód-áramlást kíván használni, ahogy azt korábban láthatta, használja a `.WithRedirectUri` módosítót.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Konfigurációs fájlok használata

A következő kód egy nyilvános ügyfélalkalmazást hoz végre egy konfigurációs objektumból, amely programozott módon kitölthető vagy egy konfigurációs fájlból olvasható.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Részletesebb konfiguráció

Az alkalmazásépületet számos módosító hozzáadásával fejtheti ki. Ha például azt szeretné, hogy az alkalmazás több-bérlős alkalmazás legyen egy nemzeti felhőben, például az Egyesült Államok kormánya itt látható, akkor a következőket írhatja:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET active Directory összevonási szolgáltatások 2019-es módosítóját is tartalmazza:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Végül, ha egy Azure Active Directory (Azure AD) B2C-bérlő jogkivonatait szeretné beszerezni, adja meg a bérlőt a következő kódrészletben látható módon:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Részletek

Ha többet szeretne megtudni az asztali MSAL.NET alkalmazások konfigurálásáról:

- A lapon `PublicClientApplicationBuilder`elérhető összes módosító listáját a [PublicClientApplicationBuilder referenciadokumentációban találja.](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- A programban elérhető `PublicClientApplicationOptions`összes beállítás leírását a hivatkozási dokumentáció [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) című részében találja.

### <a name="complete-example-with-configuration-options"></a>Teljes példa konfigurációs beállításokkal

Képzeljen el egy .NET Core `appsettings.json` konzolalkalmazást, amely a következő konfigurációs fájlt rendelkezik:

```JSon
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

Kevés kódot kell olvasnia ebben a fájlban a segítségével. NET által biztosított konfigurációs keretrendszer:

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

Most, hogy hozzon létre az alkalmazást, írja be a következő kódot:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

A metódus hívása előtt felülbírálhatja a `.Build()` konfigurációt `.WithXXX` metódusok hívásaival, ahogy azt korábban láthatta.

# <a name="java"></a>[Java](#tab/java)

Az MSAL Java fejlesztési mintákban használt osztály a minták konfigurálásához: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

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

# <a name="macos"></a>[Macos](#tab/macOS)

A következő kód egy nyilvános ügyfélalkalmazást hoz össze, és bejelentkezik a felhasználókba a Microsoft Azure nyilvános felhőben egy munkahelyi vagy iskolai fiókkal vagy egy személyes Microsoft-fiókkal.

### <a name="quick-configuration"></a>Gyors konfiguráció

C célkitűzés:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Részletesebb konfiguráció

Az alkalmazásépületet számos módosító hozzáadásával fejtheti ki. Ha például azt szeretné, hogy az alkalmazás több-bérlős alkalmazás legyen egy nemzeti felhőben, például az Egyesült Államok kormánya itt látható, akkor a következőket írhatja:

C célkitűzés:

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

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Token beszerzése asztali alkalmazáshoz](scenario-desktop-acquire-token.md)
