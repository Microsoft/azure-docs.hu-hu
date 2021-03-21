---
title: Átirányítási URI-k használata a MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Authentication Library for ObjectiveC (iOS és macOS MSAL) és az Azure AD Authentication Library for ObjectiveC (ADAL) közötti különbségeket. ObjC) és a közöttük való Migrálás módját.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev
ms.openlocfilehash: 2c728854846a3add1f5b41cb318d7dc5fd86e742
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064198"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Átirányítási URI-k használata az iOS és a macOS rendszerhez készült Microsoft Authentication Library használatával

Amikor egy felhasználó hitelesít, Azure Active Directory (Azure AD) elküldi a jogkivonatot az alkalmazásnak az Azure AD-alkalmazásban regisztrált átirányítási URI használatával.

A Microsoft Authentication Library (MSAL) megköveteli, hogy az átirányítási URI-t egy adott formátumban regisztrálja az Azure AD-alkalmazásban. A MSAL egy alapértelmezett átirányítási URI-t használ, ha nem ad meg egyet. A formátum `msauth.[Your_Bundle_Id]://auth`.

Az alapértelmezett átirányítási URI formátum a legtöbb alkalmazáshoz és forgatókönyvhöz használható, beleértve a felügyelt hitelesítést és a rendszer webes nézetét. Ha lehetséges, használja az alapértelmezett formátumot.

Előfordulhat azonban, hogy módosítania kell az átirányítási URI-t a speciális forgatókönyvek esetében az alább leírtak szerint.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Eltérő átirányítási URI-t igénylő forgatókönyvek

### <a name="cross-app-single-sign-on-sso"></a>Alkalmazások közötti egyszeri bejelentkezés (SSO)

Ahhoz, hogy a Microsoft Identity platform megossza a jogkivonatokat az alkalmazások között, minden alkalmazásnak ugyanazzal az ügyfél-AZONOSÍTÓval vagy alkalmazás-AZONOSÍTÓval kell rendelkeznie. Ez az egyedi azonosító, amely akkor érhető el, ha az alkalmazást a portálon regisztrálta (nem pedig az Apple-alkalmazásban regisztrálva lévő alkalmazáscsomag-azonosítót).

Az átirányítási URI-azonosítóknak különbözőeknek kell lenniük az egyes iOS-alkalmazások esetében. Ez lehetővé teszi, hogy a Microsoft Identity szolgáltatás egyedi módon azonosítsa az alkalmazás-AZONOSÍTÓval rendelkező különböző alkalmazásokat. Mindegyik alkalmazás több átirányítási URI-t is regisztrál a Azure Portalban. A csomag minden alkalmazásának egy másik átirányítási URI-ja lesz. Például:

A következő alkalmazás-regisztráció a Azure Portalban:

* Ügyfél-azonosító: `ABCDE-12345` (ez egyetlen ügyfél-azonosító)
* RedirectUris: `msauth.com.contoso.app1://auth` , `msauth.com.contoso.app2://auth` , `msauth.com.contoso.app3://auth`

A App1 átirányítást használ `msauth.com.contoso.app1://auth` . \
A App2 használja `msauth.com.contoso.app2://auth` . \
A App3 használja `msauth.com.contoso.app1://auth` .

### <a name="migrating-from-adal-to-msal"></a>Migrálás a ADAL-ből a MSAL-be

Ha olyan kódot telepít át, amely az Azure AD hitelesítési függvénytárat (ADAL) MSAL-ra használta, lehetséges, hogy már rendelkezik egy átirányítási URI-val az alkalmazáshoz. Továbbra is használhatja ugyanezt az átirányítási URI-t, ha a ADAL alkalmazás úgy lett konfigurálva, hogy támogassa a felügyelt forgatókönyveket, és az átirányítási URI megfelel a MSAL átirányítási URI-formátum követelményeinek.

## <a name="msal-redirect-uri-format-requirements"></a>MSAL átirányítási URI-formátumra vonatkozó követelmények

* A MSAL átirányítási URI-nak az űrlapon kell lennie `<scheme>://host`

    Hol `<scheme>` található az alkalmazás azonosítására szolgáló egyedi karakterlánc. Elsődlegesen az alkalmazás köteg-azonosítóján alapul, amely garantálja az egyediséget. Ha például az alkalmazás Bundle `com.contoso.myapp` -azonosítója, az átirányítási URI a következő formában lesz: `msauth.com.contoso.myapp://auth` .

    Ha a ADAL-ről végez áttelepítést, az átirányítási URI-ja valószínűleg a következő formátumú lesz: `<scheme>://[Your_Bundle_Id]` , ahol a egy `scheme` egyedi karakterlánc. A MSAL használatakor ez a formátum továbbra is működni fog.

* `<scheme>` az alkalmazás info. plist fájljában kell regisztrálni `CFBundleURLTypes > CFBundleURLSchemes` .  Ebben a példában a info. plist fájl forráskódként lett megnyitva:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```

A MSAL ellenőrzi, hogy az átirányítási URI megfelelően van-e regisztrálva, és ha nem, hibaüzenetet ad vissza.
    
* Ha az univerzális hivatkozásokat átirányítási URI-ként kívánja használni, akkor azt be kell `<scheme>` jelölnie, `https` és nem kell deklarálnia `CFBundleURLSchemes` . Ehelyett konfigurálja az alkalmazást és a tartományt az Apple által a [fejlesztők számára elérhető univerzális hivatkozásokon](https://developer.apple.com/ios/universal-links/) , és hívja meg a `handleMSALResponse:sourceApplication:` metódust, `MSALPublicClientApplication` amikor az alkalmazást univerzális kapcsolaton keresztül nyitják meg.

## <a name="use-a-custom-redirect-uri"></a>Egyéni átirányítási URI használata

Ha egyéni átirányítási URI-t szeretne használni, adja át a `redirectUri` paramétert, `MSALPublicClientApplicationConfig` és adja át az objektumot az `MSALPublicClientApplication` objektum inicializálásakor. Ha az átirányítási URI-azonosító érvénytelen, az inicializálás visszaadja `nil` és beállítja a `redirectURIError` További információkat.  Például:

Objective-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>A megnyitott URL-cím eseményének kezelése

Az alkalmazásnak meg kell hívnia a MSAL, ha bármilyen választ kap az URL-sémák vagy az univerzális hivatkozások segítségével. A metódus meghívása az `handleMSALResponse:sourceApplication:` `MSALPublicClientApplication` alkalmazás megnyitásakor. Az alábbi példa az egyéni sémákat szemlélteti:

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Következő lépések

További információ a [hitelesítési folyamatokról és az alkalmazási forgatókönyvekről](authentication-flows-app-scenarios.md)
