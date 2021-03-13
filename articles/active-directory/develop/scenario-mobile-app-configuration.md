---
title: Webes API-kat meghívó Mobile apps konfigurálása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan konfigurálhatja a Mobile App kódját a webes API-k meghívásához
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/16/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6f13d789cd63bb568bb8940ce614ebdb2dbcdb83
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199749"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Webes API-kat meghívó mobil alkalmazás konfigurálása

Az alkalmazás létrehozása után megtudhatja, hogyan konfigurálhatja a kódot az alkalmazás regisztrációs paramétereinek használatával. A mobil alkalmazások a létrehozási keretrendszerbe való beépítéshez kapcsolódó bonyolultságot jelentenek.

## <a name="microsoft-libraries-supporting-mobile-apps"></a>Mobile apps-t támogató Microsoft-kódtárak

A következő Microsoft-kódtárak támogatják a Mobile Apps szolgáltatást:

[!INCLUDE [active-directory-develop-libraries-mobile](../../../includes/active-directory-develop-libraries-mobile.md)]

## <a name="instantiate-the-application"></a>Az alkalmazás példányának példánya

### <a name="android"></a>Android

A Mobile Applications az `PublicClientApplication` osztályt használja. A következő módon hozhatja létre:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Az iOS-hez készült Mobile-alkalmazásoknak az osztályt kell létrehozniuk `MSALPublicClientApplication` . Az osztály létrehozásához használja a következő kódot.

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[További MSALPublicClientApplicationConfig-tulajdonságok](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) felülbírálják az alapértelmezett szolgáltatót, megadhatnak egy átirányítási URI-t, vagy megváltoztathatják a MSAL-token gyorsítótárazásának viselkedését.

### <a name="xamarin-or-uwp"></a>Xamarin vagy UWP

Ez a szakasz azt ismerteti, hogyan hozható létre az alkalmazás a Xamarin. iOS, a Xamarin. Android és a UWP alkalmazások számára.

#### <a name="instantiate-the-application"></a>Az alkalmazás példányának példánya

A Xamarin vagy UWP az alkalmazás létrehozásának legegyszerűbb módja az alábbi kód használata. Ebben a kódban `ClientId` a regisztrált alkalmazás GUID azonosítója.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

További `With<Parameter>` módszerek: állítsa be a felhasználói felület szülőjét, bírálja felül az alapértelmezett szolgáltatót, adja meg a telemetria ügyfél nevét és verzióját, adjon meg egy átirányítási URI-t, és adja meg a használni kívánt http-gyárat. Lehetséges, hogy a HTTP-gyár használható például a proxyk kezelésére, valamint a telemetria és a naplózás megadására.

A következő szakaszokban további információkat talál az alkalmazás létrehozásáról.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Szülő felhasználói felület, ablak vagy tevékenység meghatározása

Az Androidon adja át a fölérendelt tevékenységet az interaktív hitelesítés előtt. Ha az iOS-ben közvetítőt használ, a rendszer a következőt adja meg: `ViewController` . A UWP megegyező módon érdemes átadni a fölérendelt ablakot. Ezt a token beszerzése után adja át. Az alkalmazás létrehozásakor azonban visszahívást is megadhat a visszaadott meghatalmazottként `UIParent` .

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

Androidon a használata javasolt [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin) . A létrejövő `PublicClientApplication` Builder-kód a következő példához hasonlít:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>További alkalmazás-létrehozási paraméterek keresése

Az összes elérhető metódus listáját `PublicClientApplicationBuilder` a [módszerek listájában](/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)tekintheti meg.

A által elérhetővé tett összes beállítás leírását `PublicClientApplicationOptions` a [dokumentációban](/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)találja.

## <a name="tasks-for-xamarin-ios"></a>Xamarin iOS-feladatok

Ha a MSAL.NET-t használja a Xamarin iOS-eszközön, hajtsa végre a következő feladatokat.

* [A függvény felülbírálása és implementálása `OpenUrl``AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Kulcstartó-csoportok engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Jogkivonat-gyorsítótár megosztásának engedélyezése](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Kulcstartó-hozzáférés engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

További információ: [Xamarin iOS-megfontolások](msal-net-xamarin-ios-considerations.md).

## <a name="tasks-for-msal-for-ios-and-macos"></a>Az iOS és a macOS rendszerhez készült MSAL feladatai

Ezek a feladatok akkor szükségesek, ha iOS-és macOS-MSAL használ:

* [A `openURL` visszahívás megvalósítása](#brokered-authentication-for-msal-for-ios-and-macos)
* [Kulcstartó-hozzáférési csoportok engedélyezése](howto-v2-keychain-objc.md)
* [Böngészők és WebView objektumok testreszabása](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Xamarin. Android-feladatok

Ha a Xamarin. Android-t használja, tegye a következő feladatokat:

- [Győződjön meg arról, hogy a vezérlő visszatér a MSAL, miután a hitelesítési folyamat interaktív része véget ér](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Az Android-jegyzékfájl frissítése](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support)
- [A beágyazott webes nézet használata (nem kötelező)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [A szükséges hibák megoldása](msal-net-xamarin-android-considerations.md#troubleshooting)

További információ: [Xamarin. Android megfontolások](msal-net-xamarin-android-considerations.md).

Az androidos böngészőkkel kapcsolatos megfontolásokat lásd: [Xamarin. Android-specifikus megfontolások a MSAL.net](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>UWP kapcsolatos feladatok

A UWP-on vállalati hálózatokat használhat. A következő szakasz azokat a feladatokat ismerteti, amelyeket el kell végeznie a vállalati forgatókönyvben.

További információ: [UWP-specifikus megfontolások a MSAL.net](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Az alkalmazás konfigurálása a közvetítő használatára

Az Android és az iOS rendszeren a brókerek a következőket teszik lehetővé:

- **Egyszeri bejelentkezés (SSO)**: használhat egyszeri bejelentkezést az Azure Active Directory (Azure ad) szolgáltatásban regisztrált eszközökhöz. Ha SSO-t használ, a felhasználóknak nem kell bejelentkezniük minden alkalmazásba.
- **Eszköz azonosítása**: Ez a beállítás engedélyezi az Azure ad-eszközökhöz kapcsolódó feltételes hozzáférési szabályzatokat. A hitelesítési folyamat az eszköznek a munkahelyhez való csatlakozásakor létrehozott tanúsítványát használja.
- **Alkalmazás-azonosítás ellenőrzése**: amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címét. Ezután a közvetítő ellenőrzi.

### <a name="enable-the-broker-on-xamarin"></a>A közvetítő engedélyezése a Xamarin

Ha engedélyezni szeretné a közvetítőt a Xamarin, használja a `WithBroker()` paramétert a `PublicClientApplicationBuilder.CreateApplication` metódus hívásakor. Alapértelmezés szerint a `.WithBroker()` értéke TRUE (igaz).

A Xamarin. iOS felügyelt hitelesítésének engedélyezéséhez kövesse a jelen cikk [Xamarin. iOS című szakaszának](#enable-brokered-authentication-for-xamarin-ios) lépéseit.

### <a name="enable-the-broker-for-msal-for-android"></a>Az Android rendszerhez készült MSAL-ügynök engedélyezése

További információ a brókerek Androidon való engedélyezéséről: felügyelt [hitelesítés az Androidon](msal-android-single-sign-on.md).

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Az iOS és a macOS rendszerhez készült MSAL-ügynök engedélyezése

A felügyelt hitelesítés alapértelmezés szerint engedélyezve van az Azure AD-forgatókönyvek esetében az iOS és a macOS rendszerhez készült MSAL.

A következő szakasz útmutatást nyújt az alkalmazásnak a Xamarin. iOS vagy a MSAL for iOS és macOS rendszerhez készült MSAL való közvetítéses hitelesítéshez való konfigurálásához. A két utasításban a lépések némelyike eltérő.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Felügyelt hitelesítés engedélyezése a Xamarin iOS-hez

A jelen szakaszban ismertetett lépéseket követve engedélyezheti a Xamarin. iOS-alkalmazásnak, hogy beszéljen a [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) alkalmazással.

#### <a name="step-1-enable-broker-support"></a>1. lépés: a közvetítő támogatásának engedélyezése

A Broker-támogatás alapértelmezés szerint le van tiltva. Egy adott osztály számára engedélyezheti `PublicClientApplication` . A `WithBroker()` paramétert akkor használja, ha az osztályt a használatával hozza létre `PublicClientApplication` `PublicClientApplicationBuilder` . `WithBroker()`Alapértelmezés szerint a paraméter értéke TRUE (igaz).

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2. lépés: a visszahívást kezelő AppDelegate frissítése

Amikor a MSAL.NET meghívja a közvetítőt, a közvetítő ezután visszahívja az alkalmazást. A metódus használatával hívja vissza `AppDelegate.OpenUrl` . Mivel a MSAL megvárja a közvetítőtől érkező választ, az alkalmazásnak együtt kell működnie a MSAL.NET visszahívásához. Ezt a viselkedést úgy állíthatja be `AppDelegate.cs` , hogy a fájl frissítésével felülbírálja a metódust, ahogy az alábbi kód is mutatja.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
 if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
 {
  AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
  return true;
 }
 else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
 {
  return false;
 }
 return true;
}
```

Ezt a metódust az alkalmazás minden indításakor meg kell hívni. Lehetőség van a közvetítő válaszának feldolgozására és a MSAL.NET elindított hitelesítési folyamat befejezésére.

#### <a name="step-3-set-a-uiviewcontroller"></a>3. lépés: UIViewController () beállítása

A Xamarin iOS esetében általában nem kell beállítania az objektum ablakát. Ebben az esetben azonban úgy kell beállítania, hogy a brókertől érkező válaszokat küldjön és fogadjon. Egy objektum ablakának beállításához a ( `AppDelegate.cs` ) beállításnál adja meg a következőt: `ViewController` .

Az objektum ablakának beállításához kövesse az alábbi lépéseket:

1. A ben `AppDelegate.cs` állítsa be a- `App.RootViewController` t egy új értékre `UIViewController()` . Ez a beállítás biztosítja, hogy a közvetítő hívása tartalmazza a-t `UIViewController` . Ha nincs megfelelően beállítva, a következő hibaüzenet jelenhet meg:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. A hívásban használja a következőt: `AcquireTokenInteractive` `.WithParentActivityOrWindow(App.RootViewController)` . Adja át a hivatkozást a használni kívánt objektum-ablakra. Bemutatunk egy példát:

    Az `App.cs` szkriptben:
    ```csharp
       public static object RootViewController { get; set; }
    ```
    Az `AppDelegate.cs` szkriptben:
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    A `AcquireToken` hívásban:
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

#### <a name="step-4-register-a-url-scheme"></a>4. lépés: URL-séma regisztrálása

A MSAL.NET URL-címeket használ a közvetítő meghívásához, majd visszaküldi a közvetítői választ az alkalmazásnak. A kör alakú útvonal befejezéséhez regisztrálja az alkalmazás URL-sémáját a `Info.plist` fájlban.

Az alkalmazás URL-sémájának regisztrálásához kövesse az alábbi lépéseket:

1. Előtag `CFBundleURLSchemes` az-val `msauth` .
1. Hozzáadás `CFBundleURLName` a végéhez. Kövesse ezt a mintát:

   `$"msauth.(BundleId)"`

   Itt `BundleId` egyedileg azonosíthatja az eszközt. Ha például `BundleId` az, az `yourcompany.xforms` URL-séma `msauth.com.yourcompany.xforms` .

  
      Ez az URL-séma az átirányítási URI részévé válik, amely egyedileg azonosítja az alkalmazást, amikor megkapja a közvetítő válaszát.

   ```XML
    <key>CFBundleURLTypes</key>
       <array>
         <dict>
           <key>CFBundleTypeRole</key>
           <string>Editor</string>
           <key>CFBundleURLName</key>
           <string>com.yourcompany.xforms</string>
           <key>CFBundleURLSchemes</key>
           <array>
             <string>msauth.com.yourcompany.xforms</string>
           </array>
         </dict>
       </array>
   ```

#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>5. lépés: Hozzáadás a összes szakaszhoz

A MSAL használatával `–canOpenURL:` ellenőrizhető, hogy a közvetítő telepítve van-e az eszközön. Az iOS 9-es verzióban az Apple zárolta azokat a sémákat, amelyeket az alkalmazás lekérhet.

Adja hozzá a (z) `msauthv2` `LSApplicationQueriesSchemes` szakaszt a `Info.plist` fájlhoz, a következő példában látható módon:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Felügyelt hitelesítés iOS és macOS rendszerű MSAL

A felügyelt hitelesítés alapértelmezés szerint engedélyezve van az Azure AD-forgatókönyvek esetében.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>1. lépés: a visszahívást kezelő AppDelegate frissítése

Ha az iOS és a macOS rendszerhez készült MSAL meghívja a közvetítőt, a közvetítő a metódussal visszahívja az alkalmazást az alkalmazásba `openURL` . Mivel a MSAL megvárja a közvetítőtől érkező választ, az alkalmazásnak együtt kell működnie a MSAL visszahívásához. Állítsa be ezt a képességet úgy `AppDelegate.m` , hogy a fájlt úgy frissíti, hogy felülírja a metódust, az alábbi példákban látható módon.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Ha az `UISceneDelegate` iOS 13-ös vagy újabb verzióját használja, helyezze el a MSAL-visszahívást a `scene:openURLContexts:` `UISceneDelegate` helyett. `handleMSALResponse:sourceApplication:`Az MSAL csak egyszer hívható meg minden URL-címhez.

További információt az [Apple dokumentációjában](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)talál.

#### <a name="step-2-register-a-url-scheme"></a>2. lépés: URL-séma regisztrálása

Az iOS-hez és macOS-hez készült MSAL URL-címeket használ a közvetítő meghívásához, majd visszaküldi az alkalmazásnak a közvetítő válaszát. A kör alakú útvonal befejezéséhez regisztráljon egy URL-sémát az alkalmazásához a `Info.plist` fájlban.

Séma regisztrálása az alkalmazáshoz:

1. Előtagként adja meg az egyéni URL-sémáját `msauth` .

1. Adja hozzá a köteg azonosítóját a séma végéhez. Kövesse ezt a mintát:

   `$"msauth.(BundleId)"`

   Itt `BundleId` egyedileg azonosíthatja az eszközt. Ha például `BundleId` az, az `yourcompany.xforms` URL-séma `msauth.com.yourcompany.xforms` .

    Ez az URL-séma az átirányítási URI részévé válik, amely egyedileg azonosítja az alkalmazást, amikor megkapja a közvetítő válaszát. Győződjön meg arról, hogy az átirányítási URI formátuma `msauth.(BundleId)://auth` regisztrálva van az alkalmazáshoz a [Azure Portalban](https://portal.azure.com).

   ```XML
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

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>3. lépés: összes hozzáadása

A Hozzáadás `LSApplicationQueriesSchemes` gombra kattintva engedélyezheti a Microsoft Authenticator alkalmazásnak, ha telepítve van.

> [!NOTE]
> A `msauthv3` séma akkor szükséges, ha az alkalmazást a Xcode 11 vagy újabb verziójának használatával állítják össze.

Íme egy példa a hozzáadására `LSApplicationQueriesSchemes` :

```XML
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Felügyelt hitelesítés a Xamarin. Android rendszerhez

A Broker Android rendszeren való engedélyezésével kapcsolatos információkért lásd: [Xamarin. Android](msal-net-use-brokers-with-xamarin-apps.md#brokered-authentication-for-android), felügyelt hitelesítés.

## <a name="next-steps"></a>Következő lépések

Ebben a forgatókönyvben a [token beszerzésével](scenario-mobile-acquire-token.md)léphet be a következő cikkbe.
