---
title: Bizalmas ügyfélalkalmazás (MSAL.NET) létrehozása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy bizalmas ügyfélalkalmazás konfigurációs beállításokkal a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: d477c419bb677a6b8f24a3aae26c403e47cc96cb
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583942"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Bizalmas ügyfélalkalmazás létrehozása konfigurációs beállításokkal a MSAL.NET használatával

Ez a cikk azt ismerteti, hogyan hozható létre egy [bizalmas ügyfélalkalmazás](msal-client-applications.md) a .net-hez készült Microsoft Authentication Library (MSAL.net) használatával.  Az alkalmazás egy beállítási fájlban megadott konfigurációs beállításokkal lett létrehozva.

Az alkalmazás inicializálásához először [regisztrálnia](quickstart-register-app.md) kell, hogy az alkalmazás integrálható legyen a Microsoft Identity platformmal. A regisztráció után a következő információkra lehet szüksége (amelyek a Azure Portalban találhatók):

- Az ügyfél-azonosító (GUID jelölő sztring)
- Az identitás-szolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési célközönsége. Ez a két paraméter együttesen a hatóság néven ismert.
- A bérlő azonosítója, ha csak az Ön szervezete számára (egy egybérlős alkalmazás is megnevezett) üzletági alkalmazást ír.
- Az alkalmazás titkos kulcsa (az ügyfél titkos karakterlánca) vagy a tanúsítvány (X509certificate2) típusú), ha bizalmas ügyfélalkalmazás.
- Webalkalmazások esetében, és esetenként a nyilvános ügyfélalkalmazások számára (különösen, ha az alkalmazásnak közvetítőt kell használnia), azt a redirectUri is be kell állítania, amelyben az identitás-szolgáltató felveszi a kapcsolatot az alkalmazással a biztonsági jogkivonatokkal.

## <a name="configure-the-application-from-the-config-file"></a>Az alkalmazás konfigurálása a konfigurációs fájlból
A MSAL.NET lévő beállítások tulajdonságainak neve megegyezik a `AzureADOptions` ben lévő ASP.net Core tulajdonságainak nevével, így nincs szükség ragasztó-kód írására.

ASP.NET Core alkalmazás konfigurációját a fájl egy *appsettings.js* írja le:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

A MSAL.NET v3. x verziótól kezdődően beállíthatja a bizalmas ügyfélalkalmazás konfigurációját a konfigurációs fájlból.

Deklaráljon egy objektumot abban az osztályban, amelyben az alkalmazást konfigurálni és létrehozni szeretné `ConfidentialClientApplicationOptions` .  Kösse a forrásból beolvasott konfigurációt (beleértve a fájl appconfig.jsis) az alkalmazás beállításainak egy példányához, az `IConfigurationRoot.Bind()`Microsoft.Extensions.Configszülő metódusának használatával [ . Kötés NuGet csomagja](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Ez lehetővé teszi a fájl *appsettings.jsjának* "AzureAD" szakaszának tartalmát, hogy az objektum megfelelő tulajdonságaihoz legyen kötve `ConfidentialClientApplicationOptions` .  Következő lépésként hozzon létre egy `ConfidentialClientApplication` objektumot:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Futtatókörnyezet-konfiguráció hozzáadása
Egy bizalmas ügyfélalkalmazás esetében általában gyorsítótár felhasználónként. Ezért be kell szereznie a felhasználóhoz tartozó gyorsítótárat, és értesítenie kell a használni kívánt Application Builder-t. Ugyanígy előfordulhat, hogy egy dinamikusan számított átirányítási URI-val rendelkezik. Ebben az esetben a kód a következő:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

