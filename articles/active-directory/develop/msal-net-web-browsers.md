---
title: Webböngészők (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Xamarin Android a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával kapcsolatos konkrét szempontokat.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4121d4b9ac73ed18da7dce0e397fe919589ac6f0
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478760"
---
# <a name="using-web-browsers-msalnet"></a>Webböngészők használata (MSAL.NET)

Az interaktív hitelesítéshez webböngészők szükségesek. Alapértelmezés szerint a MSAL.NET támogatja a rendszer [webböngészőjét](#system-web-browser-on-xamarinios-xamarinandroid) xamarin.iOS és Xamarin.Android rendszeren. Az [Embedded webböngészőt](#enable-embedded-webviews-on-ios-and-android) azonban a követelmények (UX, egyszeri bejelentkezés (SSO), biztonság) igényeitől függően is engedélyezheti [a Xamarin.iOS-](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) és [Xamarin.Android-alkalmazásokban.](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) Emellett azt [](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) is megadhatja, hogy dinamikusan melyik webböngészőt használja a Chrome vagy az androidos egyéni Chrome-lapokat támogató böngésző jelenléte alapján. MSAL.NET .NET Core asztali alkalmazásokban csak a rendszerböngészőt támogatja.

## <a name="web-browsers-in-msalnet"></a>Böngészők a MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Az interakció egy webböngészőben történik

Fontos tudni, hogy a jogkivonat interaktív beszerzésekor a párbeszédpanel tartalmát nem a kódtár, hanem az STS (Security Token Service) biztosítja. A hitelesítési végpont visszaküld néhány HTML- és JavaScript-kódot, amelyek vezérlik az interakciót, amely egy webböngészőben vagy webes vezérlőben jelenik meg. Az STS-nek a HTML-interakciók kezelésével való használatának számos előnye van:

- A jelszót (ha beírta) soha nem tárolja az alkalmazás, sem a hitelesítési kódtár.
- Lehetővé teszi az átirányítást más identitásszolgáltatókhoz (például bejelentkezés munkahelyi vagy MSAL-t biztosító személyes fiókkal, vagy közösségi fiókkal Azure AD B2C).
- Lehetővé teszi az STS-nek a feltételes hozzáférés szabályozását, például azzal, hogy a felhasználónak többtényezős hitelesítést (MFA) kell használnia a hitelesítési fázisban (egy [Windows Hello-pin-kód](../authentication/concept-mfa-howitworks.md) megadása vagy hívása a telefonján, vagy egy hitelesítési alkalmazással a telefonján). Abban az esetben, ha a szükséges többtényezős hitelesítés még nincs beállítva, a felhasználó egy párbeszédablakban állíthatja be időben.  A felhasználó beírja a mobiltelefonszámát, és a rendszer arra irányítja, hogy telepítsen egy hitelesítési alkalmazást, és beolvassa a QR-címkét a fiókja hozzáadásához. Ez a kiszolgálóvezérelt interakció nagyszerű élmény!
- Lehetővé teszi, hogy a felhasználó ugyanezen a párbeszédpanelen módosítsa a jelszavát, amikor a jelszó lejárt (további mezőket ad meg a régi jelszóhoz és az új jelszóhoz).
- Lehetővé teszi a bérlő vagy az Azure AD-bérlői rendszergazda /alkalmazás tulajdonosa által vezérelt alkalmazás (rendszerképek) védjegyezését.
- Lehetővé teszi, hogy a felhasználók hozzájárulnak ahhoz, hogy az alkalmazás a hitelesítés után a nevükben hozzáférjen a nevükben az erőforrásokhoz/hatókörökhez.

### <a name="embedded-vs-system-web-ui"></a>Beágyazott és Rendszer webes felhasználói felülete

MSAL.NET egy több keretrendszerre vonatkozó kódtár, és keretrendszer-specifikus kóddal rendelkezik, amely egy böngészőt tartalmaz egy felhasználói felület vezérlőjében (például a klasszikus .NET-keretrendszeren WinFormsot használ, a Xamarinon natív mobilvezérlőket stb.). Ezt a vezérlőt webes `embedded` felhasználói felületnek nevezzük. Másik lehetőségként MSAL.NET a rendszer operációs rendszerének böngészőjét is.

Általában ajánlott a platform alapértelmezett beállítását használni, és általában ez a rendszerböngésző. A rendszerböngésző jobban emlékszik a korábban bejelentkezett felhasználókra. Ennek a viselkedésnek a módosítása a `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Gyors áttekintés

| Keretrendszer        | Beágyazott | Rendszer | Alapértelmezett |
| ------------- |-------------| -----| ----- |
| Klasszikus .NET     | Igen | Igen^ | Beágyazott |
| .NET Core     | Nem | Igen^ | Rendszer |
| .NET Standard | Nem | Igen^ | Rendszer |
| UWP | Igen | Nem | Beágyazott |
| Xamarin.Android | Igen | Igen  | Rendszer |
| Xamarin.iOS | Igen | Igen  | Rendszer |
| Xamarin.Mac| Igen | Nem | Beágyazott |

^ Requires " http://localhost " átirányítási URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Rendszer webböngészője Xamarin.iOS és Xamarin.Android rendszeren

Alapértelmezés szerint a MSAL.NET támogatja a rendszer webböngészőjét a Xamarin.iOS, a Xamarin.Android és a .NET Core rendszeren. A felhasználói felületet (azaz a .NET Core-t nem) elérhető platformokon a webböngésző-vezérlőt beágyazó kódtár párbeszédpanelt biztosít. MSAL.NET .NET Desktop és WAB beágyazott webes nézetet is használ az UWP-platformhoz. Alapértelmezés szerint azonban a  rendszer webböngészőjét használja xamarin iOS- és Xamarin Android-alkalmazásokhoz. iOS rendszeren még az operációs rendszer verziójától (iOS12, iOS11 és korábbi verziók) függően is kiválasztja a használni használt webes nézetet.

A rendszerböngésző használata azzal a jelentős előnnyel rendelkezik, hogy az SSO-állapotot más alkalmazásokkal és webalkalmazásokkal is megoszthatja közvetítő nélkül (Vállalati portál /Authenticator). A rendszerböngésző alapértelmezés szerint a MSAL.NET-ban volt használva a Xamarin iOS és Xamarin Android platformokhoz, mert ezeken a platformokon a rendszer webböngészője foglalja el a teljes képernyőt, és a felhasználói élmény jobb. A rendszer webes nézete nem különböztethető meg a párbeszédpaneltől. iOS-en azonban előfordulhat, hogy a felhasználónak engedélyt kell adnia a böngészőnek, hogy visszahívja az alkalmazást, ami zavaró lehet.

## <a name="system-browser-experience-on-net"></a>Rendszerböngészői élmény a .NET-en 

A .NET Core-MSAL.NET külön folyamatként indítja el a rendszerböngészőt. MSAL.NET nem tudja vezérelni ezt a böngészőt, de ha a felhasználó befejezte a hitelesítést, a weblap úgy lesz átirányítva, hogy az MSAL.NET el tudja fogni az URI-t.

A klasszikus .NET-hez vagy a .NET 5-höz írt alkalmazásokat a böngésző használatára is konfigurálhatja a következő megadásával:

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET nem tudja észlelni, hogy a felhasználó elnavigál vagy egyszerűen bezárja a böngészőt. Az ezzel a technikával használt alkalmazások esetében javasolt időtúllépést meghatározni (a `CancellationToken` használatával). Javasoljuk, hogy legalább néhány perces időtúllépést vegyen figyelembe, hogy figyelembe vegye az olyan eseteket, amikor a felhasználónak meg kell változtatnia a jelszavát, vagy többtényezős hitelesítést kell végeznie.

### <a name="how-to-use-the-default-os-browser"></a>Az alapértelmezett operációsrendszer-böngésző használata

MSAL.NET kell figyelnie és elfogni az AAD által küldött kódot, amikor a felhasználó végzett a hitelesítéssel (részletekért lásd az engedélyezési `http://localhost:port` kódot) [](v2-oauth2-auth-code-flow.md)

A rendszerböngésző engedélyezése:

1. Az alkalmazásregisztráció során a konfigurálás `http://localhost` átirányítási URI-ként (a B2C jelenleg nem támogatja)
2. A PublicClientApplication meghatározásakor adja meg az átirányítási URI-t:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> A konfigurálása esetén a belső MSAL.NET talál egy véletlenszerűen megnyitott `http://localhost` portot, és használja azt.

### <a name="linux-and-mac"></a>Linux és MAC

Linux rendszeren MSAL.NET az alapértelmezett operációsrendszer-böngészőt az xdg-open eszközzel. A hibaelhárításhoz futtassa az eszközt egy terminálból, például: `xdg-open "https://www.bing.com"` . Mac gépen a böngésző a következő használatával nyílik meg: `open <url>` .

### <a name="customizing-the-experience"></a>A felhasználói élmény testreszabása

> [!NOTE]
> A testreszabás a MSAL.NET 4.1.0-s vagy újabb rendszerében érhető el.

MSAL.NET http-üzenettel válaszolhat jogkivonatok esetén vagy hiba esetén. Megjeleníthet egy HTML-üzenetet, vagy átirányíthatja a kívánt URL-címre:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Adott böngésző megnyitása (kísérleti)

Testreszabhatja a böngésző MSAL.NET a böngészőt. Az alapértelmezett böngésző helyett például kényszerítheti egy adott böngésző megnyitását:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>Az UWP nem használja a System Webview-t

Asztali alkalmazások esetén azonban a System Webview indítása egy alrendszeri felhasználói felülethez vezet, ahogy a felhasználó látja a böngészőt, ahol előfordulhat, hogy már más lapok is meg vannak nyitva. Ha hitelesítés történt, a felhasználók egy oldalt kapnak, amely megkéri őket, hogy zárják be ezt az ablakot. Ha a felhasználó nem figyel, bezárhatja a teljes folyamatot (beleértve a többi lapot is, amelyek nem kapcsolódnak a hitelesítéshez). A rendszerböngésző asztali használata esetén a helyi portok megnyitására és figyelésére is szükség lehet, amihez speciális engedélyekre lehet szükség az alkalmazáshoz. Fejlesztőként, felhasználóként vagy rendszergazdaként esetleg ódódhat ettől a követelménytől.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Beágyazott webnézetek engedélyezése iOS és Android rendszeren

A beágyazott webnézeteket Xamarin.iOS- és Xamarin.Android-alkalmazásokban is engedélyezheti. A MSAL.NET 2.0.0-s előzetes verziótól kezdve a MSAL.NET támogatja a beágyazott **webnézet** lehetőséget is. A ADAL.NET beágyazott webnézet az egyetlen támogatott lehetőség.

A Xamarint MSAL.NET fejlesztőként választhat beágyazott webnézetek vagy rendszerböngészők használata között. Ez a választás a felhasználói élménytől és a megcélzott biztonsági szempontoktól függ.

Jelenleg MSAL.NET nem támogatja az Android- és iOS-közvetítőket. Ezért az egyszeri bejelentkezés (SSO) érdekében a rendszerböngésző továbbra is jobb választás lehet. A beágyazott webböngészővel támogató közvetítők a MSAL.NET vannak.

### <a name="differences-between-embedded-webview-and-system-browser"></a>A beágyazott webnézet és a rendszerböngésző közötti különbségek
A beágyazott webnézet és a rendszerböngésző között van néhány vizuális különbség a MSAL.NET.

**Interaktív bejelentkezés MSAL.NET Embedded Webview használatával:**

![beágyazva](media/msal-net-web-browsers/embedded-webview.png)

**Interaktív bejelentkezés MSAL.NET a Rendszerböngészővel:**

![Rendszerböngésző](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Fejlesztői beállítások

Az STS MSAL.NET interaktív párbeszédablakának megjelenítésére számos lehetőség áll rendelkezésre a fejlesztők számára:

- **Rendszerböngésző.** A rendszerböngésző alapértelmezés szerint be van állítva a könyvtárban. Android használata esetén olvassa el a [rendszerböngészőket](msal-net-system-browser-android-considerations.md) a hitelesítéshez támogatott böngészőkre vonatkozó információkért. Ha Android rendszeren használja a rendszerböngészőt, javasoljuk, hogy az eszközön olyan böngészőt is használjon, amely támogatja az egyéni Chrome-lapokat.  Ellenkező esetben a hitelesítés sikertelen lehet.
- **Beágyazott webnézet.** Ha csak beágyazott webnézetet MSAL.NET, `AcquireTokenInteractively` a paraméterszerkesztő tartalmaz egy `WithUseEmbeddedWebView()` metódust.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android::

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Választás a beágyazott webböngésző vagy a Xamarin.iOS rendszerböngészője között

Az iOS-alkalmazásban `AppDelegate.cs` inicializálhatja az inicializálását a `ParentWindow` következőre: `null` . iOS-hez nem használható

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Választás a beágyazott webböngésző vagy a Xamarin.Android rendszerböngészője között

Az Android-alkalmazásban beállíthatja a szülőtevékenységet, hogy a hitelesítési eredmény `MainActivity.cs` visszatérjön hozzá:

```csharp
 App.ParentWindow = this;
```

Ezután a `MainPage.xaml.cs` következőben:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Egyéni lapok jelenlétének észlelése a Xamarin.Androidon

Ha a rendszer webböngészőjében szeretné engedélyezni az SSO-t a böngészőben futó alkalmazásokkal, de aggódik, hogy az Android-eszközök felhasználói élménye nem rendelkezik egyéni böngészőtámogatással, dönthet úgy, hogy a metódust hívja meg `IsSystemWebViewAvailable()` `IPublicClientApplication` a-ban. Ez a metódus akkor ad vissza értéket, ha a PackageManager egyéni lapokat észlel, és ha nem észlelhetők `true` `false` az eszközön.

A metódus által visszaadott érték és a követelmények alapján döntést lehet hozni:

- A felhasználónak egyéni hibaüzenetet is visszaadhat. Például: "Telepítse a Chrome-ot a hitelesítés folytatásához" -OR-
- Visszatérhet a beágyazott webnézet lehetőséghez, és beágyazott webnézetként elindíthatja a felhasználói felületet.

Az alábbi kód a beágyazott webnézet lehetőséget mutatja be:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>A .NET Core nem támogatja a beágyazott böngészővel való interaktív hitelesítést

A .NET Core esetén a jogkivonatok interaktív beszerzése csak a rendszer webböngészőjében érhető el, beágyazott webes nézetekkel nem. A .NET Core egyelőre nem biztosít felhasználói felületet.
Ha testre szeretné szabni a böngészőt a rendszer webböngészőjében, megvalósíthatja az [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) felületet, és akár saját böngészőt is biztosíthat.
