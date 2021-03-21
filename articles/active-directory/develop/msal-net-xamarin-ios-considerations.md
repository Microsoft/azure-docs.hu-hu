---
title: Xamarin iOS-megfontolások (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Xamarin iOS és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatának szempontjait.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 62eb4ab9eb6e4b0e7be0f7aadae1173950d21615
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064487"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>A Xamarin iOS és a MSAL.NET használatának szempontjai

Ha a .NET-hez készült Microsoft Authentication Library (MSAL.NET) szolgáltatást használja a Xamarin iOS-ben, akkor a következőket kell tennie:

- Felülbírálja és implementálja a `OpenUrl` függvényt a alkalmazásban `AppDelegate` .
- Kulcstartó-csoportok engedélyezése.
- Engedélyezze a jogkivonat-gyorsítótár megosztását.
- Kulcstartó-hozzáférés engedélyezése.
- Ismerje meg az iOS 12 és az iOS 13 és a hitelesítés ismert problémáit.

## <a name="implement-openurl"></a>OpenUrl megvalósítása

A `OpenUrl` `FormsApplicationDelegate` származtatott osztály és a hívás metódusának felülbírálása `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` . Bemutatunk egy példát:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Továbbá hajtsa végre a következő feladatokat:

* Adjon meg egy átirányítási URI-sémát.
* Engedély megkövetelése az alkalmazás számára egy másik alkalmazás meghívásához.
* Az átirányítási URI-hoz adott formában van.
* [Regisztráljon egy átirányítási URI](quickstart-register-app.md#add-a-redirect-uri) -t a Azure Portal.

### <a name="enable-keychain-access"></a>Kulcstartó-hozzáférés engedélyezése

A kulcstartó-hozzáférés engedélyezéséhez győződjön meg arról, hogy az alkalmazás rendelkezik kulcstartó-hozzáférési csoporttal. A kulcstartó-hozzáférési csoportot beállíthatja úgy, hogy az API használatával hozza létre az alkalmazást `WithIosKeychainSecurityGroup()` .

A gyorsítótár és az egyszeri bejelentkezés (SSO) kihasználása érdekében állítsa a kulcstartó-hozzáférési csoportot az összes alkalmazás azonos értékére.

A telepítőhöz tartozó példa a MSAL 4. x verzióját használja:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Engedélyezze a kulcstartó-hozzáférést is a `Entitlements.plist` fájlban. Használja a következő hozzáférési csoportot vagy a saját hozzáférési csoportját.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Ha az `WithIosKeychainSecurityGroup()` API-t használja, a MSAL automatikusan hozzáfűzi a biztonsági csoportot az alkalmazás *Team ID* () végéhez `AppIdentifierPrefix` . A MSAL hozzáadja a biztonsági csoportot, mert amikor az alkalmazást a Xcode-ben hozza létre, az ugyanaz lesz. Ezért a fájl jogosultságait a kulcstartó- `Entitlements.plist` `$(AppIdentifierPrefix)` hozzáférési csoport elé kell foglalni.

További információ: [iOS-jogosultságok dokumentációja](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps).

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Jogkivonat-gyorsítótár megosztásának engedélyezése iOS-alkalmazások között

A MSAL 2. x verziótól kezdődően megadhat egy kulcstartó-hozzáférési csoportot, amely több alkalmazás között is megőrzi a jogkivonat-gyorsítótárat. Ez a beállítás lehetővé teszi, hogy megossza a jogkivonat-gyorsítótárat több olyan alkalmazás között, amelyek ugyanazzal a kulcstartó-hozzáférési csoporttal rendelkeznek. A jogkivonat-gyorsítótárat megoszthatja a [ADAL.net](https://aka.ms/adal-net) -alkalmazások, a MSAL.net-Xamarin. iOS-alkalmazások és a [ADAL. ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc) vagy [MSAL.](https://github.com/AzureAD/microsoft-authentication-library-for-objc)ObjC által fejlesztett natív iOS-alkalmazások között.

A jogkivonat-gyorsítótár megosztásával engedélyezheti az egyszeri bejelentkezést (SSO) az összes olyan alkalmazás között, amelyek ugyanazt a kulcstartó-hozzáférési csoportot használják.

A gyorsítótár megosztásának engedélyezéséhez a `WithIosKeychainSecurityGroup()` metódus használatával állítsa be a kulcstartó-hozzáférési csoportot az összes olyan alkalmazásban, amely ugyanazt a gyorsítótárat használja. A cikkben szereplő első példa azt mutatja be, hogyan használható a metódus.

A cikk korábbi szakaszaiban megtudta, hogy `$(AppIdentifierPrefix)` a MSAL az API használatakor is felhasználja `WithIosKeychainSecurityGroup()` . A MSAL hozzáadja ezt az elemet, mert a csoport azonosítója `AppIdentifierPrefix` biztosítja, hogy csak az azonos közzétevő által készített alkalmazások tudják megosztani a kulcstartó-hozzáférést.

> [!NOTE]
> A `KeychainSecurityGroup` tulajdonság elavult. Ehelyett használja a `iOSKeychainSecurityGroup` tulajdonságot. Az `TeamId` előtag nem szükséges a használatakor `iOSKeychainSecurityGroup` .

### <a name="use-microsoft-authenticator"></a>Microsoft Authenticator használata

Az alkalmazás a Microsoft Authenticatort közvetítőként használhatja a következők engedélyezéséhez:

- **SSO**: Ha engedélyezi az SSO-t, a felhasználóknak nem kell bejelentkezniük minden alkalmazásba.
- **Eszköz azonosítása**: a hitelesítéshez használja az eszköz azonosítását az eszköz tanúsítványának eléréséhez. Ez a tanúsítvány a munkahelyhez való csatlakozáskor jön létre az eszközön. Az alkalmazás készen áll, ha a bérlői rendszergazdák engedélyezik az eszközökhöz kapcsolódó feltételes hozzáférést.
- **Alkalmazás-azonosítás ellenőrzése**: amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címét. A közvetítő ellenőrzi az átirányítási URL-címet.

A közvetítők engedélyezésével kapcsolatos további információkért lásd: [Microsoft Authenticator vagy Microsoft Intune céges portál használata iOS-és Android-alkalmazások Xamarin](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Az iOS 12 és a hitelesítés ismert problémái

A Microsoft [biztonsági tanácsadót](https://github.com/aspnet/AspNetCore/issues/4647) bocsátott ki az iOS 12 és bizonyos típusú hitelesítések közötti inkompatibilitással kapcsolatban. A kompatibilitási megszakítja a közösségi, WSFed és OIDC bejelentkezéseket. A biztonsági tanácsadó segítségével megismerheti, hogyan távolíthat el ASP.NET biztonsági korlátozásokat az alkalmazásaiból, hogy azok kompatibilisek legyenek az iOS 12 rendszerrel.

Ha MSAL.NET-alkalmazásokat fejleszt ki a Xamarin iOS-ben, akkor a rendszer végtelen hurkot fog látni, amikor megpróbál bejelentkezni a webhelyekre az iOS 12 rendszerből. Az ilyen viselkedés hasonló ehhez a ADAL-problémához a GitHubon: [végtelen hurok, amikor megpróbál bejelentkezni a webhelyre az iOS 12 #1329ból](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329).

Előfordulhat, hogy az iOS 12 Safari-ban ASP.NET Core OIDC-hitelesítés is megszakad. További információkért tekintse meg ezt a [WebKit-problémát](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="known-issues-with-ios-13-and-authentication"></a>Az iOS 13 és a hitelesítés ismert problémái

Ha az alkalmazáshoz feltételes hozzáférés vagy tanúsítványalapú hitelesítés támogatása szükséges, engedélyezze az alkalmazásnak, hogy kommunikáljon a Microsoft Authenticator Broker alkalmazással. Ezután a MSAL felelős az alkalmazás és a Microsoft Authenticator közötti kérelmek és válaszok kezeléséért.

IOS 13 esetében az Apple a betörést okozó API-változást úgy szüntette meg, hogy az alkalmazás nem tudja beolvasni a forrásoldali alkalmazást, amikor egy külső alkalmazástól választ kap az egyéni URL-sémák használatával.

Az Apple [UIApplicationOpenURLOptionsSourceApplicationKey](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) -állapotokra vonatkozó dokumentációja:

> *Ha a kérelem egy másik, a csapathoz tartozó alkalmazásból származik, a UIKit a kulcs értékét az alkalmazás AZONOSÍTÓJÁRA állítja be. Ha a kezdeményező alkalmazás csoportjának azonosítója eltér az aktuális alkalmazás csoportjának azonosítójával, a kulcs értéke üres.*

Ez a változás a MSAL miatt szakad meg, mivel a rendszer a `UIApplication.SharedApplication.OpenUrl` MSAL és a Microsoft Authenticator alkalmazás közötti kommunikáció ellenőrzésére támaszkodik.

Emellett az iOS 13 rendszeren a fejlesztőnek meg kell adnia egy bemutató vezérlőt a használatakor `ASWebAuthenticationSession` .

Az alkalmazás hatással van, ha a Xcode 11 használatával dolgozik, és az iOS-közvetítőt vagy a-t használja `ASWebAuthenticationSession` .

Ilyen esetekben a [MSAL.net 4.4.0 +](https://www.nuget.org/packages/Microsoft.Identity.Client/) használatával engedélyezheti a sikeres hitelesítést.

### <a name="additional-requirements"></a>További követelmények

- A legújabb MSAL-kódtárak használatakor győződjön meg arról, Microsoft Authenticator hogy a **6.3.19 + verziója** telepítve van az eszközön.
- A MSAL.NET 4.4.0 + verzióra való frissítéskor frissítse a saját adatait `LSApplicationQueriesSchemes` az *info. plist* fájlban, és adja hozzá a következőt `msauthv3` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    Az `msauthv3` *info. plist* fájlhoz való hozzáadásával észlelhető a legújabb Microsoft Authenticator alkalmazás jelenléte az iOS 13 operációs rendszert támogató eszközön.

## <a name="report-an-issue"></a>Probléma bejelentése

Ha kérdése van, vagy szeretne jelenteni egy problémát a MSAL.NET-ben, nyisson meg egy problémát a GitHubon található [AzureAD/Microsoft-Authentication-Library-for-DotNet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) adattárban.

## <a name="next-steps"></a>Következő lépések

További információ a Xamarin iOS-es tulajdonságairól: a következő minta README.md-fájljának [iOS-specifikus szempontjait](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) ismertető bekezdése:

Sample | Platform | Description
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Univerzális Windows-platform (UWP) | Egy egyszerű Xamarin űrlapos alkalmazás, amely bemutatja, hogyan hitelesítheti a Microsoft személyes fiókjait és az Azure AD-t az Azure AD 2,0-végponton keresztül a MSAL használatával. Az alkalmazás azt is bemutatja, hogyan használható az eredményül kapott jogkivonat a Microsoft Graph eléréséhez.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
