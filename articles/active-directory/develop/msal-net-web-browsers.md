---
title: Böngészők használata (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Xamarin Android és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatára vonatkozó szempontokat.
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
ms.openlocfilehash: 23ba50a6eca1e398b9d459153b84719909f2ecac
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583739"
---
# <a name="using-web-browsers-msalnet"></a>Böngészők használata (MSAL.NET)

Az interaktív hitelesítéshez böngészők szükségesek. Alapértelmezés szerint a MSAL.NET támogatja a [rendszerböngészőt](#system-web-browser-on-xamarinios-xamarinandroid) a Xamarin. iOS és a Xamarin. Android rendszeren. A [beágyazott webböngészőt](#enable-embedded-webviews-on-ios-and-android) azonban a követelményektől függően (UX, egyszeri bejelentkezésre (SSO), biztonságra) is engedélyezheti a [Xamarin. iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) és a [Xamarin. Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) alkalmazásokban. Emellett azt is megteheti, hogy [dinamikusan](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) használja azt a böngészőt, amelyet a Chrome vagy az Android rendszerű egyéni Chrome-lapokat támogató böngésző jelenléte alapján használ. A MSAL.NET csak a rendszerböngészőt támogatja a .NET Core Desktop-alkalmazásokban.

## <a name="web-browsers-in-msalnet"></a>Böngészők a MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Az interakció egy böngészőben történik

Fontos tisztában lenni azzal, hogy a jogkivonat interaktív beszerzése során a párbeszédpanel tartalmát nem a könyvtár, hanem az STS (biztonsági jogkivonat szolgáltatás) is megadja. A hitelesítési végpont olyan HTML-és JavaScript-kódot küld vissza, amely a webböngészőben vagy a webes vezérlőelemben megjelenített interakciót vezérli. A HTML-interakció kezelésének engedélyezése az STS számára számos előnnyel jár:

- Az alkalmazás soha nem tárolja a jelszót (ha van ilyen), és nem a hitelesítési függvénytárat.
- Lehetővé teszi az átirányítást más identitás-szolgáltatók számára (például munkahelyi iskolai fiókkal vagy személyes fiókkal, MSAL vagy közösségi fiókkal Azure AD B2C).
- Lehetővé teszi az STS-vezérlés feltételes elérését, például azáltal, hogy a felhasználó a [többtényezős hitelesítést (MFA)](../authentication/concept-mfa-howitworks.md) használja a hitelesítési fázisban (a Windows Hello PIN-kód beírása, vagy a telefonra való hívás vagy a telefonos hitelesítés alkalmazásban). Azokban az esetekben, amikor a szükséges multi-Factor Authentication még nincs beállítva, a felhasználó időben is beállíthatja ugyanabban a párbeszédablakban.  A felhasználó beírja a mobil telefonszámát, és a rendszer vezérli egy hitelesítési alkalmazás telepítésére és egy QR-címke bevizsgálatára a fiók hozzáadásához. Ez a kiszolgáló-vezérelt interakció nagyszerű élmény!
- Lehetővé teszi, hogy a felhasználó ugyanezen a párbeszédpanelen változtassa meg a jelszavát, ha a jelszó lejárt (további mezőket biztosít a régi jelszóhoz és az új jelszóhoz).
- Engedélyezi a bérlő vagy az Azure AD-bérlői rendszergazda/alkalmazás tulajdonosa által vezérelt alkalmazás (lemezképek) arculatát.
- Lehetővé teszi a felhasználók számára, hogy az alkalmazáshoz való hozzáféréshez szükséges erőforrásokat vagy hatóköröket közvetlenül a hitelesítés után hagyják el.

### <a name="embedded-vs-system-web-ui"></a>Embedded és System webes felhasználói felület

A MSAL.NET egy multi-Framework könyvtár, amely keretrendszer-specifikus kóddal rendelkezik, amely egy felhasználói felületi vezérlőben futtatja a böngészőt (például a klasszikus .NET-es verzióban a WinForms-t használja, a Xamarin pedig natív mobil vezérlőket használ, stb.). Ezt a vezérlőt `embedded` webes felhasználói felületnek nevezzük. Azt is megteheti, hogy a MSAL.NET képes a rendszerindító böngésző kiindítására is.

Általában azt javasoljuk, hogy használja a platform alapértelmezését, és ez általában a rendszerböngésző. A rendszerböngésző jobban megjegyezi a korábban bejelentkezett felhasználókat. A viselkedés módosításához használja a következőt `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Gyors áttekintés

| Keretrendszer        | Beágyazott | Rendszer | Alapértelmezett |
| ------------- |-------------| -----| ----- |
| Klasszikus .NET     | Yes | Igen ^ | Beágyazott |
| .NET Core     | No | Igen ^ | Rendszer |
| .NET Standard | No | Igen ^ | Rendszer |
| UWP | Igen | Nem | Beágyazott |
| Xamarin.Android | Igen | Yes  | Rendszer |
| Xamarin.iOS | Igen | Yes  | Rendszer |
| Xamarin. Mac| Igen | Nem | Beágyazott |

^ A következőt igényli: " http://localhost " átirányítási URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Rendszerböngésző a Xamarin. iOS, Xamarin. Android rendszeren

Alapértelmezés szerint a MSAL.NET támogatja a rendszerböngészőt a Xamarin. iOS, a Xamarin. Android és a .NET Core rendszeren. Az összes olyan platform esetében, amely felhasználói felületet (azaz nem .NET Core-t) biztosít, a függvénytár egy webböngésző-vezérlő beágyazásával biztosít egy párbeszédpanelt. A MSAL.NET a UWP platformhoz a .NET Desktop és a WAB beágyazott webes nézetét is használja. Ez azonban alapértelmezés szerint a **rendszerböngészőt** használja az iOS-és Xamarin Android-alkalmazások Xamarin. IOS rendszeren az operációs rendszer verziójától (iOS12, iOS11 és korábbi verziók) függően a webes nézetet is választhatja.

A rendszerböngészővel jelentős előnye van annak, hogy az SSO-állapotot más alkalmazásokkal és webalkalmazásokkal közösen ossza meg anélkül, hogy közvetítőt (vállalati portált/hitelesítő) kellene használnia. A rendszerböngészőt alapértelmezetten a Xamarin iOS és a Xamarin Android platformhoz készült MSAL.NET használták, mert ezeken a platformokon a rendszer webböngészője a teljes képernyőt foglalja le, és a felhasználói élmény jobb. A rendszer webes nézete nem különbözteti meg a párbeszédpanelt. Az iOS-ben azonban előfordulhat, hogy a felhasználónak engedélyt kell adnia a böngészőnek az alkalmazás visszahívására, ami bosszantó lehet.

## <a name="system-browser-experience-on-net-core"></a>A .NET Core rendszerböngészős felülete

A .NET Core-on a MSAL.NET külön folyamatként fogja elindítani a rendszerböngészőt. A MSAL.NET nem szabályozza ezt a böngészőt, de ha a felhasználó befejezte a hitelesítést, a weblap olyan módon lesz átirányítva, amelyet a MSAL.NET el tud metszeni az URI-val.

A klasszikus .NET-hez írt alkalmazásokat is konfigurálhatja a böngésző használatára a következő megadásával:

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

A MSAL.NET nem tudja felderíteni, hogy a felhasználó navigál-e, vagy egyszerűen zárja be a böngészőt. Az ezt a technikát használó alkalmazásokat javasoljuk, hogy az időtúllépést (a-on keresztül) definiálja `CancellationToken` . Legalább néhány perc időtúllépését javasoljuk, hogy figyelembe vegye azokat az eseteket, amikor a felhasználó a jelszó módosítására vagy a többtényezős hitelesítés végrehajtására kéri.

### <a name="how-to-use-the-default-os-browser"></a>Az alapértelmezett operációsrendszer-böngésző használata

A MSAL.NET meg kell figyelnie és el kell fogadnia `http://localhost:port` a HRE által a felhasználó hitelesítése során küldött kódot (lásd a részleteket az [engedélyezési kódban](v2-oauth2-auth-code-flow.md) ).

A rendszerböngésző engedélyezése:

1. Az alkalmazás regisztrálása során `http://localhost` a konfigurálás átirányítási URI-ként történik (a B2C jelenleg nem támogatja)
2. A PublicClientApplication összeállításakor adja meg az átirányítási URI-t:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Ha konfigurálja `http://localhost` , a belső MSAL.net egy véletlenszerű nyitott portot talál, és azt használja.

### <a name="linux-and-mac"></a>Linux és MAC

Linux rendszeren a MSAL.NET a XDG-Open eszközzel nyitja meg az alapértelmezett operációsrendszer-böngészőt. A hibák megoldásához futtassa az eszközt egy terminálról, például: `xdg-open "https://www.bing.com"` . Mac gépen a böngészőt a meghívásával nyitja meg `open <url>` .

### <a name="customizing-the-experience"></a>A felhasználói élmény testreszabása

> [!NOTE]
> A Testreszabás a MSAL.NET 4.1.0 vagy újabb verzióban érhető el.

A MSAL.NET egy token fogadásakor vagy hiba esetén HTTP-üzenettel tud válaszolni. Megjeleníthet egy HTML-üzenetet, vagy átirányíthatja a kívánt URL-címet:

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

Testreszabhatja a MSAL.NET a böngésző megnyitásának módját. Például ahelyett, hogy az alapértelmezett böngészőt használja, egy adott böngésző megnyitására lehet kényszeríteni:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>A UWP nem használja a rendszerszintű webnézetet

Asztali alkalmazások esetében azonban a rendszerszintű webnézet indítása subpar felhasználói élményhez vezet, mivel a felhasználó látja a böngészőt, és előfordulhat, hogy már más lapok is meg vannak nyitva. Ha a hitelesítés megtörtént, a felhasználók egy olyan lapot kap, amely az ablak bezárását kéri. Ha a felhasználó nem figyel, akkor a teljes folyamatot (beleértve a hitelesítéshez nem kapcsolódó lapokat is) lezárhatja. Ha a rendszerböngészőt az asztalon szeretné kihasználni, a helyi portok megnyitására és a figyelésre is szükség lehet, ami speciális engedélyeket igényelhet az alkalmazáshoz. Előfordulhat, hogy a fejlesztő, a felhasználó vagy a rendszergazda nem fogja tudni ezt a követelményt.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Beágyazott webnézetek engedélyezése iOS és Android rendszereken

A Xamarin. iOS és a Xamarin. Android alkalmazásokban is engedélyezheti a beágyazott webnézeteket. A MSAL.NET 2.0.0 – előzetes verziótól kezdődően a MSAL.NET a **beágyazott** webnézetet is támogatja. A ADAL.NET esetében az egyetlen támogatott lehetőség a beágyazott Webview.

MSAL.NET-célzási Xamarin használó fejlesztőként dönthet úgy, hogy a beágyazott webnézeteket vagy a rendszerböngészőket is használja. Ez a felhasználói élménytől és a célként használni kívánt biztonsági szempontoktól függően választható.

Jelenleg a MSAL.NET még nem támogatja az Android és az iOS rendszerű közvetítőket. Ezért az egyszeri bejelentkezés (SSO) biztosításához a rendszerböngészőnek továbbra is jobb lehetőség lehet. A beágyazott webböngészővel rendelkező közvetítők támogatják a MSAL.NET várakozó fájlok számát.

### <a name="differences-between-embedded-webview-and-system-browser"></a>A beágyazott webnézet és a rendszerböngésző közötti különbségek
A MSAL.NET beágyazott webnézete és rendszerböngészője között néhány vizualizációs különbség van.

**Interaktív bejelentkezés a MSAL.NET a beágyazott webnézet használatával:**

![beágyazva](media/msal-net-web-browsers/embedded-webview.png)

**Interaktív bejelentkezés a MSAL.NET a rendszerböngésző használatával:**

![Rendszerböngésző](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Fejlesztői beállítások

A MSAL.NET-t használó fejlesztőként több lehetőség közül választhat az STS interaktív párbeszédpanelének megjelenítéséhez:

- **Rendszerböngésző.** A rendszerböngészőt a rendszer alapértelmezés szerint a könyvtárban állítja be. Android használata esetén olvassa el a [rendszerböngészők](msal-net-system-browser-android-considerations.md) című témakört, amely arról nyújt tájékoztatást, hogy mely böngészők támogatottak a hitelesítéshez. Ha Android rendszeren használja a rendszerböngészőt, javasoljuk, hogy az eszközön legyen egy böngésző, amely támogatja a Chrome egyéni lapjait.  Ellenkező esetben a hitelesítés sikertelen lehet.
- **Beágyazott webnézet.** Ha csak a beágyazott webnézetet szeretné használni a MSAL.NET-ben, a `AcquireTokenInteractively` Parameters Builder `WithUseEmbeddedWebView()` metódust tartalmaz.

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>A Xamarin. iOS fájl beágyazott webböngészője vagy rendszerböngészője közötti választás

Az iOS-alkalmazásban a alkalmazásban `AppDelegate.cs` inicializálhatja a `ParentWindow` alkalmazást `null` . IOS-ben nem használatos

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Választás a beágyazott webböngésző vagy a Xamarin. Android rendszerbeli böngésző között

Az Android-alkalmazásban beállíthatja `MainActivity.cs` a szülő tevékenységet, így a hitelesítési eredmény visszakerül a következőre:

```csharp
 App.ParentWindow = this;
```

Ezután a `MainPage.xaml.cs` :

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Egyéni lapok jelenlétének észlelése a Xamarin. Android rendszeren

Ha azt szeretné, hogy a rendszer webböngészője engedélyezze az egyszeri bejelentkezést a böngészőben futó alkalmazásokkal, de az Android-eszközök felhasználói felülete nem rendelkezik egyéni Tab-támogatással, dönthet úgy, hogy meghívja a `IsSystemWebViewAvailable()` metódust a alkalmazásban `IPublicClientApplication` . Ez a metódus `true` azt adja vissza, hogy a PackageManager észleli-e az egyéni lapokat, és `false` hogy nem észlelhetők-e az eszközön.

Az ezzel a módszerrel visszaadott érték és a követelmények alapján a következő döntéseket hozhatja:

- Egyéni hibaüzenetet adhat vissza a felhasználónak. Például: "telepítse a Chrome-t a hitelesítés folytatásához"-vagy-
- A beágyazott webnézet lehetőségre visszatérhet, és beágyazott webnézetként indíthatja el a felhasználói felületet.

Az alábbi kód a beágyazott webnézet lehetőséget mutatja be:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>A .NET Core nem támogatja az interaktív hitelesítést egy beágyazott böngészővel

A .NET Core esetében a tokenek interaktív beszerzése csak a rendszer webböngészőjén keresztül érhető el, nem pedig a beágyazott webes nézetekkel. Valójában a .NET Core még nem biztosít felhasználói felületet.
Ha testre szeretné szabni a böngészési élményt a rendszerböngészővel, megvalósíthatja a [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) felületet, és akár saját böngészőt is megadhat.
