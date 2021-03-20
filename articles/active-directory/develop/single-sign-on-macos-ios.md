---
title: SSO konfigurálása macOS és iOS rendszeren
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést (SSO) macOS és iOS rendszeren.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 25389348476552298ddb947ccb59acb8b3d5bc57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80881248"
---
# <a name="how-to-configure-sso-on-macos-and-ios"></a>Útmutató: az SSO konfigurálása macOS és iOS rendszeren

A macOS és az iOS rendszerhez készült Microsoft Authentication Library (MSAL) támogatja az egyszeri bejelentkezést (SSO) macOS/iOS-alkalmazások és-böngészők között. Ez a cikk a következő SSO-forgatókönyveket ismerteti:

- [Csendes SSO több alkalmazás között](#silent-sso-between-apps)

Az ilyen típusú egyszeri bejelentkezés az azonos Apple Developer által terjesztett több alkalmazás között működik. Csendes SSO-t (azaz a felhasználót nem kéri a hitelesítő adatok megadására) a kulcstartóban más alkalmazások által írt frissítési tokenek olvasásával, valamint a hozzáférési tokenek csendes cseréjével.  

- [Egyszeri bejelentkezés a hitelesítési közvetítőn keresztül](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Ez a folyamat macOS rendszeren nem érhető el.

A Microsoft közvetítőknek nevezett alkalmazásokat biztosít, amelyek lehetővé teszik a különböző gyártóktól származó alkalmazások közötti egyszeri bejelentkezést, amennyiben a mobileszköz regisztrálva van Azure Active Directory (HRE). Az ilyen típusú SSO használatához a felhasználó eszközén telepítve kell lennie egy Broker-alkalmazásnak.

- **Egyszeri bejelentkezés a MSAL és a Safari között**

Az SSO a [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc) osztályon keresztül érhető el. A meglévő bejelentkezési állapotot használja más alkalmazásokból és a Safari böngészőből. Nem korlátozódik az azonos Apple Developer által terjesztett alkalmazásokra, de a felhasználói beavatkozásra van szükség.

Ha az alkalmazásban az alapértelmezett webes nézetet használja a felhasználók bejelentkezni, a rendszer automatikusan egyszeri bejelentkezést kap a MSAL-alapú alkalmazások és a Safari között. Ha többet szeretne megtudni a MSAL által támogatott webes nézetekről, tekintse meg a [böngészők és Webnézetek testreszabása](customize-webviews.md)című témakört.

> [!IMPORTANT]
> Ez a típusú egyszeri bejelentkezés jelenleg nem érhető el macOS rendszeren. A macOS-MSAL csak olyan WKWebView-ket támogat, amelyek nem rendelkeznek SSO-támogatással a Safariban. 

- **Csendes SSO a ADAL és a MSAL macOS/iOS-alkalmazások között**

A MSAL Objective-C a ADAL Objective-C-alapú alkalmazásokkal támogatja az áttelepítést és az egyszeri bejelentkezést. Az alkalmazásokat az Apple Developer szolgáltatásnak kell elosztania.

A ADAL és a MSAL-alapú alkalmazások közötti, az alkalmazások közötti egyszeri bejelentkezésre vonatkozó utasításokért tekintse [meg a MacOS és iOS rendszerű ADAL és MSAL alkalmazások közötti SSO](sso-between-adal-msal-apps-macos-ios.md) -t.

## <a name="silent-sso-between-apps"></a>Csendes SSO alkalmazások között

Az MSAL támogatja az egyszeri bejelentkezéses megosztást iOS-alapú kulcstartó-hozzáférési csoportokon keresztül.

Ha engedélyezni szeretné az egyszeri bejelentkezést az alkalmazásokban, a következő lépéseket kell elvégeznie, amelyeket az alábbi részletesebben ismertetünk:

1. Győződjön meg arról, hogy az összes alkalmazás ugyanazt az ügyfél-azonosítót vagy az alkalmazás AZONOSÍTÓját használja.
1. Győződjön meg arról, hogy az összes alkalmazás ugyanazzal az aláírási tanúsítvánnyal rendelkezik az Apple-től, hogy meg tudja osztani a kulcstartókat.
1. Az egyes alkalmazásokhoz ugyanazt a kulcstartó jogosultságot kell kérnie.
1. Adja meg a MSAL SDK-kat arról, hogy milyen megosztott kulcstartót szeretne használni, ha az eltér az alapértelmezetttől.

### <a name="use-the-same-client-id-and-application-id"></a>Ugyanaz az ügyfél-azonosító és alkalmazás-azonosító használata

Ahhoz, hogy a Microsoft Identity platform tudja, mely alkalmazások oszthatják meg a jogkivonatokat, az alkalmazásoknak azonos ügyfél-azonosítót vagy alkalmazás-azonosítót kell megosztaniuk. Ez az egyedi azonosító, amelyet az első alkalmazásnak a portálon való regisztrálása során adott meg.

A Microsoft Identity platform azt mutatja be, hogy az azonos alkalmazás-azonosítót használó alkalmazások **átirányítási URI**-k. Az egyes alkalmazások több átirányítási URI-t is regisztrálhatnak a bevezetési portálon. A csomag minden alkalmazásának egy másik átirányítási URI-ja lesz. Például:

App1 átirányítási URI: `msauth.com.contoso.mytestapp1://auth`  
App2 átirányítási URI: `msauth.com.contoso.mytestapp2://auth`  
App3 átirányítási URI: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> Az átirányítási URI-k formátumának kompatibilisnek kell lennie a MSAL által támogatott formátummal, amely a [MSAL átirányítási URI-formátumának követelményeiben](redirect-uris-ios.md#msal-redirect-uri-format-requirements)van dokumentálva.

### <a name="setup-keychain-sharing-between-applications"></a>A kulcstartó megosztásának beállítása az alkalmazások között

A kulcstartó megosztásának engedélyezéséhez tekintse meg az Apple [hozzáadási képességeivel](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) foglalkozó cikket. Fontos, hogy eldöntse, mit szeretne meghívni a kulcstartót, és ezt a képességet adja hozzá az összes olyan alkalmazáshoz, amely az egyszeri bejelentkezésben részt vesz.

Ha helyesen állította be a jogosultságokat, egy olyan fájlt fog látni `entitlements.plist` a projekt könyvtárában, amely a következőhöz hasonló példát tartalmaz:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>Új kulcstartó-csoport hozzáadása

Vegyen fel egy új kulcstartó-csoportot a projekt **képességeibe**. A kulcstartó csoportnak a következőket kell tennie:
* `com.microsoft.adalcache` iOS rendszeren 
* `com.microsoft.identity.universalstorage` macOS rendszeren.

![kulcstartó – példa](media/single-sign-on-macos-ios/keychain-example.png)

További információ: kulcstartó- [csoportok](howto-v2-keychain-objc.md).

## <a name="configure-the-application-object"></a>Az alkalmazás-objektum konfigurálása

Miután az összes alkalmazásban engedélyezte a kulcstartó jogosultságot, és készen áll az egyszeri bejelentkezés használatára, konfigurálja a `MSALPublicClientApplication` kulcstartó-hozzáférési csoporttal a következő példában látható módon:

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> Amikor megoszt egy kulcstartót az alkalmazásai között, bármelyik alkalmazás törölheti a felhasználókat, vagy akár az összes tokent az alkalmazásban.
> Ez különösen hatással van arra, ha olyan alkalmazásokkal rendelkezik, amelyek jogkivonatokat használnak a háttérben végzett munkához.
> A kulcstartó megosztása azt jelenti, hogy nagyon körültekintően kell lennie, ha az alkalmazás Microsoft Identity SDK eltávolítási műveleteket használ.

Ennyi az egész! A Microsoft Identity SDK mostantól megosztja a hitelesítő adatokat az összes alkalmazáson belül. A fiókok listája az alkalmazás példányai között is meg lesz osztva.

## <a name="sso-through-authentication-broker-on-ios"></a>Egyszeri bejelentkezés a hitelesítési ügynökön keresztül iOS rendszeren

A MSAL támogatja a Microsoft Authenticator felügyelt hitelesítését. A Microsoft Authenticator egyszeri bejelentkezést biztosít a HRE regisztrált eszközökhöz, és segít az alkalmazásnak a feltételes hozzáférési szabályzatok követésében is.

Az alábbi lépéseket követve engedélyezheti az egyszeri bejelentkezést az alkalmazáshoz tartozó hitelesítési közvetítő használatával:

1. Regisztráljon egy Broker-kompatibilis átirányítási URI-formátumot az alkalmazás info. plist fájljában. A Broker-kompatibilis átirányítási URI formátuma `msauth.<app.bundle.id>://auth` . Cserélje le az "<app.bundle.id>" "helyére az alkalmazás Bundle-azonosítójával. Például:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Adja hozzá az alábbi sémákat az alkalmazás info. plist fájljában `LSApplicationQueriesSchemes` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Adja hozzá a következőt a `AppDelegate.m` fájlhoz a visszahívások kezeléséhez:

    Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Ha a Xcode 11**-et használja, helyette a MSAL visszahívást kell elhelyeznie a `SceneDelegate` fájlba.
Ha mind a UISceneDelegate, mind a UIApplicationDelegate támogatja a régebbi iOS-kompatibilitást, akkor a MSAL visszahívást mindkét fájlba be kell helyezni.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

## <a name="next-steps"></a>Következő lépések

További információ a [hitelesítési folyamatokról és az alkalmazási forgatókönyvekről](authentication-flows-app-scenarios.md)