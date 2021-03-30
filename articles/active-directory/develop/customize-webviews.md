---
title: Böngészők testreszabása & webnézetekben (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan szabhatja testre a MSAL iOS/macOS böngésző felhasználói felületét.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: a8486ec87b5198231a33b1dab382ba457c8c8066
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85478127"
---
# <a name="how-to-customize-browsers-and-webviews-for-iosmacos"></a>Útmutató: böngészők és webnézetek testreszabása iOS/macOS rendszerhez

Interaktív hitelesítéshez böngésző szükséges. Az iOS és a macOS 10.15 + rendszeren a Microsoft Authentication Library (MSAL) alapértelmezés szerint a rendszerböngészőt használja (amely az alkalmazás tetején jelenhet meg), hogy interaktív hitelesítést végezzen a felhasználók bejelentkezéséhez. A rendszerböngészővel megoszthatja az egyszeri bejelentkezés (SSO) állapotát más alkalmazásokkal és webalkalmazásokkal.

A felhasználói élmény megváltoztatásához testreszabhatja a konfigurációt a webes tartalmak megjelenítésének egyéb beállításaira, például a következőkre:

Csak iOS esetén:

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

IOS és macOS esetén:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

A macOS-MSAL csak `WKWebView` a régebbi operációsrendszer-verziók esetében támogatott. `ASWebAuthenticationSession` csak macOS 10,15 és újabb rendszereken támogatott. 

## <a name="system-browsers"></a>Rendszerböngészők

Az iOS, `ASWebAuthenticationSession` a `SFAuthenticationSession` , és a `SFSafariViewController` rendszer rendszerböngészőnek tekintendő. MacOS esetén csak `ASWebAuthenticationSession` elérhető. Általánosságban elmondható, hogy a rendszerböngészők a Safari böngésző alkalmazással osztják meg a cookie-kat és más webhely-adataikat.

Alapértelmezés szerint a MSAL dinamikusan felismeri az iOS-verziót, és kiválasztja az adott verzióban elérhető ajánlott rendszerböngészőt. IOS 12 + esetén a következő lesz: `ASWebAuthenticationSession` . 

### <a name="default-configuration-for-ios"></a>Alapértelmezett konfiguráció iOS-hez

| Verzió | Webböngésző |
|:-------------:|:-------------:|
| iOS 12 + | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Alapértelmezett konfiguráció macOS rendszerhez

| Verzió | Webböngésző |
|:-------------:|:-------------:|
| macOS 10.15 + | ASWebAuthenticationSession |
| egyéb verziók | WKWebView |

A fejlesztők másik rendszerböngészőt is választhatnak a MSAL-alkalmazásokhoz:

- `SFAuthenticationSession` a az iOS 11 verziója `ASWebAuthenticationSession` .
- `SFSafariViewController` általánosabb célja, és felületet biztosít a webes böngészéshez, és a bejelentkezési célokra is használható. Az iOS 9 és 10 rendszerekben a cookie-kat és más webhelyeket megosztják a Safariban – de nem iOS 11 vagy újabb verziókban.

## <a name="in-app-browser"></a>Alkalmazáson belüli böngésző

A [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) egy alkalmazásbeli böngésző, amely webes tartalmat jelenít meg. A cookie-kat és a webhelyeket nem osztja meg más **WKWebView** -példányokkal vagy a Safari böngészővel. A WKWebView egy platformfüggetlen böngésző, amely iOS és macOS rendszereken egyaránt elérhető.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>A cookie-megosztás és az egyszeri bejelentkezés (SSO) következményei

A használt böngésző a cookie-k megosztása miatt hatással van az egyszeri bejelentkezéses felhasználói élményre. Az alábbi táblázatok összefoglalják a böngészőben az egyszeri bejelentkezést.

| Technológia    | Böngésző típusa  | iOS-elérhetőség | macOS-elérhetőség | Cookie-k és egyéb adatforgalom megosztása  | MSAL rendelkezésre állása | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Rendszer | iOS12 és fel | macOS 10,15 és fel | Yes | iOS és macOS 10.15 + | w/Safari-példányok
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Rendszer | iOS11 és fel | N.A. | Igen | csak iOS esetén |  w/Safari-példányok
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Rendszer | iOS11 és fel | N/A | No | csak iOS esetén | Nem * *
| **SFSafariViewController** | Rendszer | iOS10 | N.A. | Igen | csak iOS esetén |  w/Safari-példányok
| **WKWebView**  | Alkalmazáson belüli | iOS8 és fel | macOS 10,10 és fel | No | iOS és macOS | Nem * *

* * Az egyszeri bejelentkezés működéséhez a tokeneket meg kell osztani az alkalmazások között. Ehhez jogkivonat-gyorsítótár vagy közvetítő alkalmazás szükséges, például Microsoft Authenticator iOS rendszerhez.

## <a name="change-the-default-browser-for-the-request"></a>A kérelem alapértelmezett böngészője módosítása

A következő tulajdonság megváltoztatásával használhatja az alkalmazáson belüli böngészőt vagy egy adott rendszerböngészőt az UX-követelményektől függően `MSALWebviewParameters` :

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Módosítás egy interaktív kérelem alapján

Minden kérés konfigurálható úgy, hogy a tulajdonság módosításával felülbírálja az alapértelmezett böngészőt `MSALInteractiveTokenParameters.webviewParameters.webviewType` `acquireTokenWithParameters:completionBlock:` .

Emellett a MSAL a tulajdonság beállításával támogatja a továbbítást az egyéni `WKWebView` beállításokban `MSALInteractiveTokenParameters.webviewParameters.customWebView` .

Például:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Ha egyéni webnézetet használ, a rendszer az értesítéseket használja a megjelenített webes tartalom állapotának jelzésére, például:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Beállítások

Az összes támogatott MSAL-típus deklarálva van a [MSALWebviewType enumerálásban](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Következő lépések

További információ a [hitelesítési folyamatokról és az alkalmazási forgatókönyvekről](authentication-flows-app-scenarios.md)
