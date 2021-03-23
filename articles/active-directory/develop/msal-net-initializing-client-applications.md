---
title: MSAL.NET-ügyfélalkalmazások inicializálása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan inicializálhatja a nyilvános ügyfelet és a bizalmas ügyfélalkalmazások alkalmazásait a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/18/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 7ff61811e8b736f8f6d104a253cfe5dc5e76c428
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771362"
---
# <a name="initialize-client-applications-using-msalnet"></a>Ügyfélalkalmazások inicializálása a MSAL.NET használatával
Ez a cikk a nyilvános ügyfél és a bizalmas ügyfélalkalmazások inicializálását ismerteti a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.  További információ az ügyfélalkalmazások típusairól: [nyilvános ügyfél és bizalmas ügyfélalkalmazások](msal-client-applications.md).

A 3. x MSAL.NET az alkalmazások létrehozásának ajánlott módja az alkalmazás-építők használata: `PublicClientApplicationBuilder` és `ConfidentialClientApplicationBuilder` . Hatékony mechanizmust biztosítanak az alkalmazás konfigurálásához a kódban, vagy egy konfigurációs fájlból, vagy akár mindkét módszer keverésével is.

[API-referenciák dokumentációja](/dotnet/api/microsoft.identity.client)  |  [Csomag a NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/)  |  [Könyvtár forráskódja](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)  |  [Kód mintái](sample-v2-code.md)

## <a name="prerequisites"></a>Előfeltételek
Az alkalmazás inicializálásához először [regisztrálnia](quickstart-register-app.md) kell, hogy az alkalmazás integrálható legyen a Microsoft Identity platformmal.  A regisztráció után a következő információkra lehet szüksége (amelyek a Azure Portalban találhatók):

- Az ügyfél-azonosító (GUID jelölő sztring)
- Az identitás-szolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési célközönsége. Ez a két paraméter együttesen a hatóság néven ismert.
- A bérlő azonosítója, ha csak az Ön szervezete számára ír üzletági alkalmazást (más néven egybérlős alkalmazás).
- Az alkalmazás titkos kulcsa (az ügyfél titkos karakterlánca) vagy a tanúsítvány (X509certificate2) típusú), ha bizalmas ügyfélalkalmazás.
- Webalkalmazások esetében, és esetenként a nyilvános ügyfélalkalmazások számára (különösen, ha az alkalmazásnak közvetítőt kell használnia), azt a redirectUri is be kell állítania, amelyben az identitás-szolgáltató felveszi a kapcsolatot az alkalmazással a biztonsági jogkivonatokkal.

## <a name="ways-to-initialize-applications"></a>Alkalmazások inicializálási módjai
Az ügyfélalkalmazások számos különböző módon hozhatók létre.

### <a name="initializing-a-public-client-application-from-code"></a>Nyilvános ügyfélalkalmazás inicializálása kódból

A következő kód egy nyilvános ügyfélalkalmazás, a Microsoft Azure nyilvános felhőben, a munkahelyi és az iskolai fiókkal, illetve a személyes Microsoft-fiókjaival való bejelentkezési felhasználókat hozza létre.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Bizalmas ügyfélalkalmazás inicializálása kódból

Ugyanígy a következő kód egy bizalmas alkalmazást (egy webalkalmazást) hoz létre, amely a `https://myapp.azurewebsites.net` Microsoft Azure nyilvános felhőben lévő felhasználók jogkivonatait, munkahelyi és iskolai fiókjait, illetve személyes Microsoft-fiókjait kezeli. Az alkalmazás az identitás-szolgáltatóval azonosítható az ügyfél titkos kulcsának megosztásával:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Előfordulhat, hogy az éles környezetben az ügyfél titkos kulcsa helyett érdemes megosztani az Azure AD-tanúsítvánnyal. A kód ezután a következő lesz:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Nyilvános ügyfélalkalmazás inicializálása a konfigurációs beállításokból

A következő kód egy nyilvános ügyfélalkalmazás példányát egy konfigurációs objektumból hozza létre, amely programozott módon vagy egy konfigurációs fájlból is kitölthető:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Bizalmas ügyfélalkalmazás inicializálása a konfigurációs beállításokból

Ugyanez a minta vonatkozik a bizalmas ügyfélalkalmazások alkalmazására is. Más paramétereket is hozzáadhat `.WithXXX` a módosítóhoz (itt egy tanúsítvány).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Builder-módosítók

Az alkalmazás-építőket használó kódrészletekben számos `.With` metódust lehet alkalmazni Módosítóként (például `.WithCertificate` és `.WithRedirectUri` ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Nyilvános és bizalmas ügyfélalkalmazások által közösen használt módosítók

A nyilvános ügyfélen vagy a bizalmas ügyfélalkalmazás-szerkesztőben beállítható módosítók a következők:

|Módosító | Leírás|
|--------- | --------- |
|`.WithAuthority()` 7 felülbírálás | Az alkalmazás alapértelmezett szolgáltatóját egy Azure AD-szolgáltatóra állítja be, és kiválaszthatja az Azure-felhőt, a célközönséget, a bérlőt (bérlői azonosítót vagy tartománynevet), vagy közvetlenül a szolgáltatói URI-t.|
|`.WithAdfsAuthority(string)` | Az alkalmazás alapértelmezett szolgáltatójának beállítása ADFS-szolgáltatóként.|
|`.WithB2CAuthority(string)` | Az alkalmazás alapértelmezett szolgáltatóját Azure AD B2C-szolgáltatóként állítja be.|
|`.WithClientId(string)` | Felülbírálja az ügyfél-azonosítót.|
|`.WithComponent(string)` | Beállítja a könyvtár nevét a MSAL.NET használatával (telemetria okok miatt). |
|`.WithDebugLoggingCallback()` | Ha a hívása megtörténik, az alkalmazás `Debug.Write` egyszerűen engedélyezi a hibakeresési nyomkövetést. További információért lásd: [naplózás](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) .|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Adja meg az alkalmazási szint további lekérdezési paramétereit, amelyeket a rendszer az összes hitelesítési kérelemben elküld. Ez a Overridable minden egyes jogkivonat-gyűjtési módszer szintjén (azonos `.WithExtraQueryParameters pattern` ) történik.|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Olyan speciális forgatókönyveket tesz lehetővé, mint például a HTTP-proxy konfigurálása vagy a MSAL kényszerítése egy adott HttpClient használatára (például ASP.NET Core Web Apps/API-ban).|
|`.WithLogging()` | Ha a hívása megtörténik, az alkalmazás meghív egy visszahívást hibakeresési nyomkövetéssel. További információért lásd: [naplózás](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) .|
|`.WithRedirectUri(string redirectUri)` | Felülbírálja az alapértelmezett átirányítási URI-t. A nyilvános ügyfélalkalmazások esetében ez hasznos lehet a közvetítőt érintő forgatókönyvek esetén.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Beállítja a telemetria küldéséhez használt delegált.|
|`.WithTenantId(string tenantId)` | Felülbírálja a bérlő AZONOSÍTÓját vagy a bérlő leírását.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Xamarin. iOS-alkalmazásokhoz tartozó módosítók

A Xamarin. iOS nyilvános ügyfélalkalmazás-építője számára beállítható módosítók a következők:

|Módosító | Leírás|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Csak Xamarin. iOS esetén**: beállítja az iOS-kulcstartó biztonsági csoportját (a gyorsítótár megőrzéséhez).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Bizalmas ügyfélalkalmazások-alkalmazásokhoz kapcsolódó módosítók

A bizalmas ügyfélalkalmazás-szerkesztőben beállítható módosítók a következők:

|Módosító | Leírás|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Beállítja az alkalmazást az Azure AD-vel azonosító tanúsítványt.|
|`.WithClientSecret(string clientSecret)` | Az alkalmazás az Azure AD-vel való azonosítására szolgáló ügyfél titkos kulcsának (alkalmazás jelszavának) beállítása.|

Ezek a módosítók kölcsönösen kizárják egymást. Ha mindkettőt megadja, a MSAL egy értelmes kivételt fog eldobni.

### <a name="example-of-usage-of-modifiers"></a>Példa a módosítók használatára

Tegyük fel, hogy az alkalmazás egy üzletági alkalmazás, amely csak a szervezete számára érhető el.  Ezután a következőket írhatja:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Ahol érdekesvé válik, az országos felhők programozása már egyszerűbbé vált. Ha azt szeretné, hogy az alkalmazás egy nemzeti felhőben több-bérlős alkalmazás legyen, írhat, például a következőt:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Az ADFS felülbírálása is (ADFS 2019 jelenleg nem támogatott):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Végül, ha Ön Azure AD B2C fejlesztő, a következőhöz hasonló módon adhatja meg a bérlőt:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

## <a name="next-steps"></a>Következő lépések

Az ügyfélalkalmazás inicializálását követően a következő feladat a felhasználói bejelentkezés, a hitelesítő API-hozzáférés vagy mindkettő támogatásának hozzáadása.

Az alkalmazási forgatókönyv dokumentációja útmutatást nyújt a felhasználóhoz való bejelentkezéshez és egy hozzáférési jogkivonat beszerzéséhez, hogy hozzáférjen egy API-hoz az adott felhasználó nevében:

- [Felhasználók számára bejelentkező webes alkalmazás: bejelentkezés és kijelentkezés](scenario-web-app-sign-user-sign-in.md)
- [Webes API-kat meghívó webalkalmazás: token beszerzése](scenario-web-app-call-api-acquire-token.md)
