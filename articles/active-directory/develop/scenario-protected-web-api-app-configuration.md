---
title: Védett webes API-alkalmazások konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre védett webes API-t, és hogyan konfigurálhatja az alkalmazás kódját.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5206c2295ee7c01b4a2908e59da1cfdd8782bccd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517718"
---
# <a name="protected-web-api-code-configuration"></a>Védett webes API: kód konfigurálása

A védett webes API-hoz tartozó kód konfigurálásához Ismerje meg a következőket:

- Mi határozza meg a védett API-kat.
- Tulajdonosi jogkivonat konfigurálása.
- A jogkivonat érvényesítése.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Mi határozza meg a ASP.NET és a ASP.NET Core API-kat védettként?

A webalkalmazásokhoz hasonlóan a ASP.NET és a ASP.NET Core webes API-k is védettek, mert a vezérlő műveletei az **[engedélyezés]** attribútummal vannak előjavítva. A vezérlő műveletei csak akkor hívhatók, ha az API-t egy hitelesítő identitással hívja meg.

A következő kérdéseket kell figyelembe venni:

- Csak egy alkalmazás hívhat meg webes API-t. Hogyan ismeri az API a meghívást az alkalmazás identitásával?
- Ha az alkalmazás egy felhasználó nevében hívja meg az API-t, mi a felhasználó identitása?

## <a name="bearer-token"></a>Tulajdonosi jogkivonat

A fejlécben beállított tulajdonosi jogkivonat, amely az alkalmazás neve tartalmazza az alkalmazás identitásával kapcsolatos információkat. Emellett a felhasználóval kapcsolatos információkat is tartalmaz, kivéve, ha a webalkalmazás nem fogadja el a szolgáltatás és a szolgáltatás közötti hívásokat egy Daemon-alkalmazásból.

Íme egy C#-kód, amely egy olyan ügyfelet mutat be, amely az API-t hívja meg, miután beszerezte a tokent a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával:

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Egy ügyfélalkalmazás a *webes API*-hoz tartozó Microsoft Identity platformra kéri a tulajdonosi jogkivonatot. A webes API az egyetlen alkalmazás, amelynek ellenőriznie kell a jogkivonatot, és meg kell tekintenie a benne található jogcímeket. Az ügyfélalkalmazások soha nem próbálják meg megvizsgálni a jogkivonatokban lévő jogcímeket.
>
> A jövőben a webes API-nak szüksége lehet a jogkivonat titkosítására. Ez a követelmény megakadályozza a hozzáférési jogkivonatokat megtekintő ügyfélalkalmazások hozzáférését.

## <a name="jwtbearer-configuration"></a>JwtBearer-konfiguráció

Ez a szakasz a tulajdonosi jogkivonatok konfigurálását ismerteti.

### <a name="config-file"></a>Konfigurációs fájl

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>Ha egyéni alkalmazás-azonosító URI-t használt a webes API-hoz

Ha elfogadta az alkalmazás-regisztrációs portál által javasolt azonosító URI-t, nem kell megadnia a célközönséget (lásd: [alkalmazás-azonosító URI és hatókörök](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes)). Ellenkező esetben olyan tulajdonságot kell hozzáadnia, `Audience` amelynek értéke a webes API-hoz tartozó alkalmazás-azonosító URI.

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>Kód inicializálása

Ha egy alkalmazás hívása olyan vezérlőre történik, amely egy **[engedélyezés]** attribútummal rendelkezik, a ASP.net és a ASP.net Core Kinyeri a hozzáférési jogkivonatot az engedélyezési fejléc tulajdonosi jogkivonatával. Ezután a hozzáférési tokent továbbítjuk a JwtBearer middleware-re, amely meghívja a .NET-hez készült Microsoft IdentityModel-bővítményeket.

#### <a name="microsoftidentityweb"></a>Microsoft. Identity. Web

A Microsoft azt javasolja, hogy a [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) NuGet csomagot használja a webes API-k ASP.net Core való fejlesztésekor.

A _Microsoft. Identity. Web_ biztosítja a ASP.net Core, a köztes hitelesítés és a .net-hez készült [Microsoft Authentication Library (MSAL)](msal-overview.md) közötti összekapcsolást. A funkció lehetővé teszi a tisztább és robusztusabb fejlesztői élményt, és kihasználja a Microsoft Identity platform és a Azure AD B2C erejét.

#### <a name="using-microsoftidentityweb-templates"></a>A Microsoft. Identity. Web sablonok használata

A Microsoft. Identity. Web Project sablonok használatával létrehozhat egy teljesen új webes API-t. Részletekért lásd: [Microsoft. Identity. Web-Web API-projekt sablonja](https://aka.ms/ms-id-web/webapi-project-templates).

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>Egy meglévő ASP.NET Core 3,1-alkalmazástól kezdve

Ma ASP.NET Core 3,1 a Microsoft. AspNetCore. AzureAD. UI könyvtárat használja. A middleware inicializálása a Startup. cs fájlban történik.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

A middleware a webes API-hoz kerül a következő utasítással:

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 A ASP.NET Core-sablonok jelenleg olyan Azure Active Directory (Azure AD) webes API-kat hoznak létre, amelyek a szervezeten vagy szervezeten belül jelentkeznek be a felhasználókba. Személyes fiókkal nem jelentkezhetnek be a felhasználókba. Azonban a Microsoft [. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) használatával módosíthatja a sablonokat a Microsoft Identity platform használatára, a kódot pedig a *Startup. cs*:

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftIdentityWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

a következőt is megírhatja (amely egyenértékű)

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
             .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");

services.AddControllers();
}
```

> [!NOTE]
> Ha a Microsoft. Identity. Web-t használja, és nem állítja be a `Audience` *appsettings.json* értékre, a rendszer a következőt használja:
> -  `$"{ClientId}"` Ha a [hozzáférési jogkivonat elfogadott verzióját](scenario-protected-web-api-app-registration.md#accepted-token-version) `2` vagy Azure ad B2C webes API-k számára állította be.
> - `$"api://{ClientId}` minden más esetben (1.0-s [hozzáférési jogkivonatok](access-tokens.md)esetén).
> Részletekért lásd: Microsoft. Identity. Web [forrás kódja](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83).

Az előző kódrészletet a rendszer kinyeri a [ASP.net Core web API növekményes oktatóanyagból](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28). A **AddMicrosoftIdentityWebApiAuthentication** részletei a [Microsoft. Identity. Web webhelyen](microsoft-identity-web.md)érhetők el. Ez a metódus meghívja a [AddMicrosoftIdentityWebAPI](/dotnet/api/microsoft.identity.web.microsoftidentitywebapiauthenticationbuilderextensions.addmicrosoftidentitywebapi)-t, amely maga is arra utasítja a middleware-t, hogy hogyan érvényesítse a jogkivonatot.

## <a name="token-validation"></a>Jogkivonat ellenőrzése

Az előző kódrészletben a JwtBearer middleware, például az OpenID Connect middleware a web Appsben, a (z) értéke alapján érvényesíti a jogkivonatot `TokenValidationParameters` . A tokent szükség szerint visszafejti a rendszer, a jogcímeket kinyeri, és ellenőrzi az aláírást. A middleware ezt követően ellenőrzi a tokent, hogy ellenőrzi az alábbi adatforrást:

- Célközönség: a jogkivonat a webes API-t célozza meg.
- Sub: olyan alkalmazás számára lett kiadva, amely számára engedélyezett a webes API meghívása.
- Kiállító: egy megbízható biztonsági jogkivonat szolgáltatás (STS) adta ki.
- Lejárat: az élettartama a tartományon belül van.
- Aláírás: nem lett illetéktelenül módosítva.

Speciális érvényesítés is lehetséges. Ellenőrizheti például, hogy az aláíró kulcsok egy tokenbe ágyazva vannak-e, megbízhatók-e, és hogy a jogkivonat nincs-e visszajátszva. Végezetül bizonyos protokollok speciális érvényesítést igényelnek.

### <a name="validators"></a>Érvényesítőket

Az érvényesítési lépések a [Microsoft IdentityModel Extensions for .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) nyílt forráskódú kódtár által biztosított validatorokban vannak rögzítve. A validatorok meghatározása a könyvtár forrásfájl [Microsoft. IdentityModel. tokens/validators. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)fájljában történik.

Ez a táblázat a validatorokat ismerteti:

| Validator | Description |
|---------|---------|
| **ValidateAudience** | Gondoskodik arról, hogy a jogkivonat az alkalmazáshoz legyen hitelesítve, amely érvényesíti a jogkivonatot. |
| **ValidateIssuer** | Gondoskodik arról, hogy a tokent egy megbízható STS bocsátotta ki, ami azt jelenti, hogy a jogkivonatot megbízhatónak minősíti. |
| **ValidateIssuerSigningKey** | Gondoskodik arról, hogy a jogkivonatot érvényesítő alkalmazás megbízza a jogkivonat aláírásához használt kulcsot. Van egy speciális eset, amikor a kulcs be van ágyazva a tokenbe. Ez az eset azonban általában nem fordul elő. |
| **ValidateLifetime** | Gondoskodik arról, hogy a jogkivonat még mindig vagy már érvényes legyen. Az érvényesítő ellenőrzi, hogy a jogkivonat élettartama a **notBefore** által megadott tartományba esik-e, és hogy a jogcímek **lejárnak** -e. |
| **ValidateSignature** | Gondoskodik arról, hogy a jogkivonat ne legyen illetéktelenül módosítva. |
| **ValidateTokenReplay** | Gondoskodik arról, hogy a jogkivonat ne legyen újrajátszva. Van egy speciális eset néhány már használatban lévő protokollhoz. |

#### <a name="customizing-token-validation"></a>Jogkivonat-érvényesítés testreszabása

A validatorok a **TokenValidationParameters** osztály tulajdonságaival vannak társítva. A tulajdonságok a ASP.NET és a ASP.NET Core konfigurációból lesznek inicializálva.

A legtöbb esetben nem kell módosítania a paramétereket. Azok az alkalmazások, amelyek nem önálló bérlők, kivételek. Ezek a webalkalmazások elfogadják a felhasználókat bármely szervezettől vagy személyes Microsoft-fiókból. Ebben az esetben a kiállítókat érvényesíteni kell. A Microsoft. Identity. web is gondoskodik a kibocsátó érvényesítéséről. Részletekért lásd: Microsoft. Identity. Web [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

Ha a jogkivonat-ellenőrzési paramétereket testre szeretné szabni a ASP.NET Coreban, használja az alábbi kódrészletet az *indításakor. cs*:

```c#
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  };
});
```

Az ASP.NET MVC esetében a következő mintakód bemutatja, hogyan végezheti el az egyéni jogkivonat-érvényesítést:

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Jogkivonat-érvényesítés Azure Functions

A bejövő hozzáférési jogkivonatokat is ellenőrizheti Azure Functionsban. Ilyen érvényesítési példákat a GitHubon a következő mintakód-mintákon talál:

- .NET: [Azure-Samples/MS-Identity-DotNet-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js: [Azure-Samples/MS-Identity-NodeJS-webapi-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python: [Azure-Samples/MS-Identity-Python-webapi-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>Következő lépések

Az ebben a forgatókönyvben lévő következő cikkre való áttéréskor [ellenőrizze a kódban a hatóköröket és az alkalmazás-szerepköröket](scenario-protected-web-api-verification-scope-app-roles.md).
