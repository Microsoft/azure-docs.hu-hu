---
title: Jogkivonat-gyorsítótár szerializálási (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: A jogkivonat-gyorsítótár szerializálásának és ügyfél-szerializálásának megismerése a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 60ce3d32ffa20fc9117890528eac053d1af9fdf2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99583908"
---
# <a name="token-cache-serialization-in-msalnet"></a>Jogkivonat-gyorsítótár szerializálása a MSAL.NET-ben
A [jogkivonat beszerzése](msal-acquire-cache-tokens.md)után a rendszer a Microsoft Authentication Library (MSAL) gyorsítótárba helyezi.  Az alkalmazás kódjának meg kell próbálnia kapni a tokent a gyorsítótárból, mielőtt másik módszerrel beszerezze a jogkivonatot.  Ez a cikk a jogkivonat-gyorsítótár alapértelmezett és egyéni szerializálását ismerteti a MSAL.NET-ben.

Ez a cikk a 3. x MSAL.NET. Ha érdekli a 2. x MSAL.NET, tekintse meg [a jogkivonat-gyorsítótár szerializálását a MSAL.NET 2. x verzióban](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Token-cache-serialization-2x).

## <a name="default-serialization-for-mobile-platforms"></a>A mobil platformok alapértelmezett szerializálása

A MSAL.NET-ben alapértelmezés szerint a memóriában tárolt jogkivonat-gyorsítótár van megadva. Alapértelmezés szerint a szerializálást olyan platformokhoz biztosítjuk, ahol a platform részeként a biztonságos tárterület elérhető a felhasználók számára. Ez a Univerzális Windows-platform (UWP), a Xamarin. iOS és a Xamarin. Android esetében.

> [!Note]
> Ha egy Xamarin. Android projektet telepít át a MSAL.NET 1. x verzióról a MSAL.NET 3. x-re, érdemes lehet hozzáadni a `android:allowBackup="false"` projekthez, hogy elkerülje a régi gyorsítótárazott tokenek visszaszerzését, amikor a Visual Studio-telepítések elindítják a helyi tároló visszaállítását. Lásd: [probléma #659](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/659#issuecomment-436181938).

## <a name="custom-serialization-for-windows-desktop-apps-and-web-appsweb-apis"></a>Egyéni szerializálás Windowsos asztali alkalmazásokhoz és webalkalmazásokhoz/webes API-khoz

Ne feledje, hogy az egyéni szerializálás nem érhető el a mobil platformokon (UWP, Xamarin. iOS és Xamarin. Android). A MSAL már definiál egy biztonságos és teljesítménybeli szerializálási mechanizmust ezekhez a platformokhoz. A .NET Desktop és a .NET Core alkalmazások azonban eltérő architektúrákkal rendelkeznek, és a MSAL nem tudnak általános célú szerializálási mechanizmust megvalósítani. A webhelyek például dönthetnek úgy, hogy Redis-gyorsítótárban tárolják a jogkivonatokat, vagy az asztali alkalmazások egy titkosított fájlban tárolják a jogkivonatokat. Így a szerializálás nem áll rendelkezésére. Ha állandó jogkivonat-gyorsítótárazási alkalmazást szeretne a .NET Desktopban vagy a .NET Core-ban, szabja testre a szerializálást.

A következő osztályok és felületek használatosak a jogkivonat-gyorsítótár szerializálásakor:

- `ITokenCache`, amely a jogkivonat-gyorsítótár szerializálási kéréseire előfizetett eseményeket, valamint a gyorsítótár szerializálásának vagy deszerializálásának módszereit határozza meg különböző formátumokban (ADAL v 3.0, MSAL 2. x és MSAL 3. x = ADAL v 5.0).
- `TokenCacheCallback` az eseményeknek átadott visszahívás, hogy kezelni tudja a szerializálást. A rendszer a típusú argumentumokkal hívja meg őket `TokenCacheNotificationArgs` .
- `TokenCacheNotificationArgs` a csak az `ClientId` alkalmazás és annak a felhasználónak a hivatkozását adja meg, amelyhez a jogkivonat elérhető.

  ![Osztály diagramja](media/msal-net-token-cache-serialization/class-diagram.png)

> [!IMPORTANT]
> A MSAL.NET jogkivonat-gyorsítótárat hoz létre az Ön számára, és megadja a `IToken` gyorsítótárat az alkalmazás és a tulajdonságok meghívásakor `UserTokenCache` `AppTokenCache` . Saját magának nem kell megvalósítani a felületet. Az egyéni jogkivonat-gyorsítótár szerializálásának megvalósításakor a következőt kell tennie:
> - Reagálás az `BeforeAccess` `AfterAccess` "események" (vagy az aszinkron ízek) értékre. A `BeforeAccess` delegált feladata a gyorsítótár deszerializálása, míg az `AfterAccess` egyik felelős a gyorsítótár szerializálásához.
> - Az események egy része tárolja vagy betölti a blobokat, amelyeket az Event argumentumon át kell adni a kívánt tárterülethez.

A stratégiák eltérőek attól függően, hogy egy [nyilvános ügyfélalkalmazás](msal-client-applications.md) (asztali) vagy egy [bizalmas ügyfélalkalmazás](msal-client-applications.md)(webalkalmazás/webes API, Daemon-alkalmazás) esetében a jogkivonat-gyorsítótár szerializálását írja-e a rendszer.

### <a name="token-cache-for-a-public-client"></a>Nyilvános ügyfél jogkivonat-gyorsítótára

A MSAL.NET v2. x óta számos lehetőség van a nyilvános ügyfél jogkivonat-gyorsítótárának szerializálására. A gyorsítótárat csak a MSAL.NET-formátumra lehet szerializálni (az egyesített formátum gyorsítótára közös a MSAL és a platformok között).  Az [örökölt](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization) jogkivonat-gyorsítótár szerializálását is támogathatja a ADAL V3 esetében.

A jogkivonat-gyorsítótár szerializálásának testreszabásával megoszthatja a ADAL.NET 3. x, ADAL.NET 5. x és MSAL.NET közötti egyszeri bejelentkezési állapotot a következő példa részeként: [Active-Directory-DotNet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2).

> [!Note]
> A MSAL.NET 1.1.4 – előzetes verziójú tokenek gyorsítótárazási formátuma már nem támogatott a MSAL 2. x verzióban. Ha a MSAL.NET 1. x-et használó alkalmazásokkal rendelkezik, a felhasználóknak újra be kell jelentkezniük. Másik lehetőségként a ADAL 4. x (és 3. x) közötti áttelepítés támogatott.

#### <a name="simple-token-cache-serialization-msal-only"></a>Egyszerű jogkivonat-gyorsítótár szerializálása (csak MSAL)

Az alábbi példa egy jogkivonat-gyorsítótár egyéni szerializálásának naiv implementációját mutatja be asztali alkalmazásokhoz. Itt a felhasználói jogkivonat gyorsítótára egy olyan fájl, amely ugyanabban a mappában található, mint az alkalmazás.

Az alkalmazás létrehozása után a metódus meghívásával `TokenCacheHelper.EnableSerialization()` és az alkalmazás átadásával engedélyezheti a szerializálást `UserTokenCache` .

```csharp
app = PublicClientApplicationBuilder.Create(ClientId)
    .Build();
TokenCacheHelper.EnableSerialization(app.UserTokenCache);
```

A `TokenCacheHelper` segítő osztály a következőképpen van definiálva:

```csharp
static class TokenCacheHelper
 {
  public static void EnableSerialization(ITokenCache tokenCache)
  {
   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
  }

  /// <summary>
  /// Path to the token cache. Note that this could be something different for instance for MSIX applications:
  /// private static readonly string CacheFilePath =
  /// $"{Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData)}\{AppName}\msalcache.bin";
  /// </summary>
  public static readonly string CacheFilePath = System.Reflection.Assembly.GetExecutingAssembly().Location + ".msalcache.bin3";

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

A termék minőségi jogkivonat-gyorsítótára a nyilvános ügyfélalkalmazások számára (Windows, Mac és Linux rendszeren futó asztali alkalmazások esetén) a [Microsoft. Identity. Client. Extensions. Msal](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Msal) nyílt forráskódú függvénytárában érhető el. Az alkalmazásokban a következő NuGet-csomaggal veheti fel: [Microsoft. Identity. Client. Extensions. Msal](https://www.nuget.org/packages/Microsoft.Identity.Client.Extensions.Msal/).

#### <a name="dual-token-cache-serialization-msal-unified-cache-and-adal-v3"></a>Kettős jogkivonat-gyorsítótár szerializálási (MSAL Unified cache és ADAL v3)

Ha a jogkivonat-gyorsítótár szerializálását az egyesített gyorsítótár formátumával (Common ADAL.NET 4. x, MSAL.NET 2. x és más, ugyanazon generáció vagy régebbi MSALs) szeretné megvalósítani, tekintse meg a következő kódot:

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

Ezúttal a segítő osztály a következőként van meghatározva:

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
  /// Enables the serialization of the token cache
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
  public static void EnableSerialization(ITokenCache tokenCache, string unifiedCacheFileName, string adalV3CacheFileName)
  {
   UnifiedCacheFileName = unifiedCacheFileName;
   AdalV3CacheFileName = adalV3CacheFileName;

   tokenCache.SetBeforeAccess(BeforeAccessNotification);
   tokenCache.SetAfterAccess(AfterAccessNotification);
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

### <a name="token-cache-for-a-web-app-confidential-client-application"></a>Jogkivonat-gyorsítótár webalkalmazásokhoz (bizalmas ügyfélalkalmazás)

A Web Apps vagy webes API-k esetében a gyorsítótár kihasználhatja a munkamenetet, a Redis cache-t vagy egy adatbázist. A Web Apps vagy a webes API-k esetében egy jogkivonat-gyorsítótárat kell megtartania a fiókban. 

A webalkalmazások esetében a jogkivonat-gyorsítótárat a fiók AZONOSÍTÓjának kell megadnia.

Webes API-k esetében a fióknak az API meghívásához használt jogkivonat kivonatával kell megjelennie.

A MSAL.NET egyéni jogkivonat-gyorsítótárazási szerializálást biztosít a .NET-keretrendszer és a .NET Core alplatformok számára. Ha a gyorsítótár elérhető, az alkalmazások eldönthetik, hogy szerializálják vagy deszerializálják a gyorsítótárat. A felhasználókat kezelő bizalmas ügyfélalkalmazások (webalkalmazások, amelyek bejelentkeznek a felhasználók számára, és meghívhatják a webes API-kat, valamint a webes API-kat hívó webes API-k), több felhasználó is lehet, és a felhasználók párhuzamosan lesznek feldolgozva. A biztonság és a teljesítmény érdekében javasoljuk, hogy felhasználónként egy gyorsítótárat szerializáljon. A szerializálási események kiszámítják a gyorsítótár-kulcsot a feldolgozott felhasználó identitása alapján, valamint az adott felhasználóhoz tartozó jogkivonat-gyorsítótár szerializálását/deszerializálását.

A [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web) Library egy előzetes verziójú NuGet-csomagot biztosít a [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) , amely a jogkivonat-gyorsítótár szerializálását tartalmazza:

| Kiterjesztési módszer | Microsoft. Identity. Web sub névtér | Description  |
| ---------------- | --------- | ------------ |
| `AddInMemoryTokenCaches` | `TokenCacheProviders.InMemory` | A memória-jogkivonat gyorsítótárának szerializálásakor. Ez a megvalósítás nagyszerű a mintákban. Az éles környezetben is jó, ha nem bánod, ha a jogkivonat gyorsítótára elvész a webalkalmazás újraindításakor. `AddInMemoryTokenCaches` egy opcionális paramétert használ, `MsalMemoryTokenCacheOptions` amely lehetővé teszi, hogy megadja azt az időtartamot, ameddig a gyorsítótár-bejegyzés lejár, kivéve, ha használatban van.
| `AddSessionTokenCaches` | `TokenCacheProviders.Session` | A jogkivonat-gyorsítótár a felhasználói munkamenethez van kötve. Ez a lehetőség nem ideális, ha az azonosító jogkivonat sok jogcímet tartalmaz, mivel a cookie túl nagy lesz.
| `AddDistributedTokenCaches` | `TokenCacheProviders.Distributed` | A jogkivonat-gyorsítótár a ASP.NET Core `IDistributedCache` implementációja, ezért lehetővé teszi az elosztott memória-gyorsítótár, a Redis cache, az elosztott NCache vagy a SQL Server cache közötti választást. A megvalósításokkal kapcsolatos további információkért `IDistributedCache` lásd: https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache .

Íme egy példa arra, hogyan használhatja a memóriabeli gyorsítótárat az [indítási](/aspnet/core/fundamentals/startup) osztály [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.startupbase.configureservices) metódusában egy ASP.net Core alkalmazásban:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddInMemoryTokenCaches();
```

Példák a lehetséges elosztott gyorsítótárokra:

```C#
// or use a distributed Token Cache by adding
    services.AddSignIn(Configuration);
    services.AddWebAppCallsProtectedWebApi(Configuration, new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

Használatuk a [ASP.net Core webalkalmazás-oktatóanyagban](/aspnet/core/tutorials/first-mvc-app/) az 2-2-os [token gyorsítótárban](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache)című szakaszban szerepel.

## <a name="next-steps"></a>Következő lépések

A következő minták a jogkivonat-gyorsítótár szerializálását szemléltetik.

| Sample | Platform | Description|
| ------ | -------- | ----------- |
|[Active-Directory-DotNet-Desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | Asztali (WPF) | A Microsoft Graph API-t hívó Windowsos asztali .NET (WPF) alkalmazás. ![A diagram egy olyan topológiát mutat be, amelyben az asztali alkalmazás W P F TodoListClient az Azure A D-be, a jogkivonat interaktív beszerzésével és a Microsoft Graphával.](media/msal-net-token-cache-serialization/topology.png)|
|[Active-Directory-DotNet-v1-to-v2](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2) | Asztal (konzol) | A Visual Studio-megoldások készlete az Azure AD 1.0-alkalmazások (ADAL.NET használatával) áttelepítését szemlélteti a Microsoft Identity platform alkalmazásaihoz (a MSAL.NET használatával). Különösen lásd: [jogkivonat-gyorsítótár áttelepítése](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/blob/master/TokenCacheMigration/README.md)|
