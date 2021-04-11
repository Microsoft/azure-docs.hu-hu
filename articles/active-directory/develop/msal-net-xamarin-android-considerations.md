---
title: Xamarin Android-kódok konfigurálása és hibaelhárítása (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Xamarin Android használatának szempontjait a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 11642480ac817b50d102e396b8ab5e200948a615
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199564"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Konfigurációs követelmények és hibaelhárítási tippek a Xamarin Androidhoz a MSAL.NET

A Xamarin Android és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával számos konfigurációs módosítást kell végrehajtania a kódban. A következő szakaszok ismertetik a szükséges módosításokat, majd egy [hibaelhárítási](#troubleshooting) szakaszt, amely segít elkerülni a leggyakoribb problémák némelyikét.

## <a name="set-the-parent-activity"></a>A szülő tevékenység beállítása

Az Android Xamarin állítsa be a szülő tevékenységet, hogy a jogkivonat az interakció után visszaadja a következőt:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

A MSAL.NET 4,2-es és újabb verzióiban ezt a funkciót a [PublicClientApplication][PublicClientApplication]szintjén is megadhatja. Ehhez visszahívás használata:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Ha a [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin)-t használja, a [`PublicClientApplication`][PublicClientApplication] Builder-kódnak ehhez a kódrészlethez hasonlóan kell kinéznie:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Győződjön meg arról, hogy a vezérlő visszaadja a MSAL

Ha a hitelesítési folyamat interaktív része véget ér, a vezérlőt a MSAL való visszalépéssel visszaállíthatja [`Activity`][Activity] .[`OnActivityResult()`][OnActivityResult] metódus.

A felülbírálásban hívja meg a MSAL. NET `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` a hitelesítési folyamat interaktív részének végén a vezérlés visszaadására szolgáló metódus MSAL.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest-for-system-webview-support"></a>A System Webview-támogatás Android-jegyzékfájljának frissítése 

A rendszer webnézetének támogatásához a *AndroidManifest.xml* fájlnak a következő értékeket kell tartalmaznia:

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

Az `android:scheme` érték az alkalmazás-portálon konfigurált átirányítási URI-ből jön létre. Ha például az átirányítási URI-ja `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth` , a `android:scheme` jegyzékfájlban szereplő bejegyzés a következő példához hasonlóan fog kinézni:

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

Azt is megteheti, [hogy a tevékenységet programkódban hozza létre](/xamarin/android/platform/android-manifest#the-basics) ahelyett, hogy manuálisan szerkeszti a *AndroidManifest.xml*. A tevékenység kódban való létrehozásához először hozzon létre egy osztályt, amely tartalmazza az `Activity` attribútumot és az `IntentFilter` attribútumot.

Az alábbi példa egy olyan osztályra mutat, amely az XML-fájl értékeit jelöli:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="use-system-webview-in-brokered-authentication"></a>A System Webview használata a felügyelt hitelesítésben

Ha az alkalmazást felügyelt hitelesítés használatára konfigurálta, és az eszköz nem rendelkezik telepített közvetítővel, akkor a MSAL a közvetítő átirányítási URI-ja használatával rögzítheti a rendszer webnézetét az interaktív hitelesítéshez való tartalékként. A MSAL az eszköz alapértelmezett webnézetének használatával kísérli meg a hitelesítést, amikor azt észleli, hogy a közvetítő nem érhető el. Ez az alapértelmezett beállítás sikertelen lesz, mert az átirányítási URI egy közvetítő használatára van konfigurálva, és a rendszer webnézete nem tudja, hogyan használható a MSAL való visszatéréshez. Ennek feloldásához hozzon létre egy _leképezési szűrőt_ a korábban konfigurált közvetítő átirányítási URI használatával. Adja hozzá a leképezési szűrőt az alkalmazás jegyzékfájljának módosításával, a következő példához hasonlóan:

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
<activity
      android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
          <action android:name="android.intent.action.VIEW" />
          <category android:name="android.intent.category.DEFAULT" />
          <category android:name="android.intent.category.BROWSABLE" />
          <data android:scheme="msauth"
              android:host="Enter_the_Package_Name"
              android:path="/Enter_the_Signature_Hash" />
    </intent-filter>
</activity>
```

Helyettesítse be a Azure Portalban regisztrált csomag nevét az `android:host=` értékhez. Helyettesítse be a Azure Portalban regisztrált kulcs kivonatát az `android:path=` értékhez. Az aláírási kivonat *nem* lehet URL-kódolású. Győződjön meg arról, hogy a kezdő perjel ( `/` ) megjelenik az aláírás kivonatának elején.

### <a name="xamarinforms-43x-manifest"></a>Xamarin. Forms 4.3. x jegyzékfájl

A Xamarin. Forms 4.3. x olyan kódot állít elő, amely az attribútumotAndroidManifest.xmlértékre állítja be `package` `com.companyname.{appName}` . ** Ha a `DataScheme` as-t használja `msal{client_id}` , érdemes lehet módosítani az értéket, hogy az megfeleljen a névtér értékének `MainActivity.cs` .

## <a name="android-11-support"></a>Android 11-támogatás

A rendszerböngésző és az Android 11 rendszerű felügyelt hitelesítés használatához először deklarálnia kell ezeket a csomagokat, hogy azok láthatók legyenek az alkalmazás számára. Az Android 10 (API 29) és korábbi verziókat megcélzó alkalmazások lekérhetik az operációs rendszert az eszközön elérhető csomagok listájára. Az adatvédelem és a biztonság támogatása érdekében az Android 11 csökkenti a csomagok láthatóságát az operációs rendszer csomagjainak alapértelmezett listájára és az alkalmazás *AndroidManifest.xml* fájljában megadott csomagokra. 

Ha engedélyezni szeretné, hogy az alkalmazás a rendszerböngészővel és a közvetítővel is hitelesítse magát, adja hozzá a következő szakaszt *AndroidManifest.xml*:

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

Cserélje le `{Package Name}` az nevet az alkalmazáscsomag nevére. 

A frissített jegyzékfájl, amely mostantól támogatja a rendszerböngészőt és a felügyelt hitelesítést, az alábbi példához hasonlóan kell kinéznie:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

## <a name="use-the-embedded-web-view-optional"></a>A beágyazott webes nézet használata (nem kötelező)

Alapértelmezés szerint a MSAL.NET a rendszer webböngészőjét használja. Ez a böngésző lehetővé teszi az egyszeri bejelentkezést (SSO) a webalkalmazások és más alkalmazások használatával. Bizonyos ritkán előforduló esetekben előfordulhat, hogy a rendszer egy beágyazott webes nézetet szeretne használni.

Ez a kódrészlet egy beágyazott webes nézet beállítását mutatja be:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

További információkért lásd: [webböngészők használata a MSAL.net](msal-net-web-browsers.md) és a [Xamarin Android rendszerbeli böngészőkkel kapcsolatos megfontolások](msal-net-system-browser-android-considerations.md).

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="general-tips"></a>Általános tippek

- Frissítse a meglévő MSAL.NET NuGet-csomagot a [MSAL.net legújabb verziójára](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Ellenőrizze, hogy a Xamarin. Forms a legújabb verzióban van-e.
- Ellenőrizze, hogy a Xamarin. Android. support. v4 a legújabb verzió-e.
- Győződjön meg arról, hogy az összes Xamarin. Android. support csomag a legújabb verziót célozza meg.
- Törölje vagy építse újra az alkalmazást.
- A Visual Studióban próbálja meg beállítani a párhuzamos projektek maximális számát **1** értékre. Ehhez válassza a **Beállítások**  >  **projektek és megoldások**  >  **Létrehozás és Futtatás**  >  **maximális száma párhuzamos projektek** buildek lehetőséget.
- Ha a parancssorból épít, és a parancs a parancsot használja `/m` , próbálja meg eltávolítani az elemet a parancsból.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Hiba: a AuthenticationContinuationHelper név nem létezik az aktuális környezetben.

Ha egy hiba azt jelzi, hogy `AuthenticationContinuationHelper` az aktuális környezetben nem létezik, előfordulhat, hogy a Visual Studio hibásan frissítette az *Android. csproj \** fájlt. Előfordul, hogy a fájl elérési útja nem megfelelően van megadva a `<HintPath>` `netstandard13` helyett `monoandroid90` .

Ez a példa a fájl helyes elérési útját tartalmazza:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Következő lépések

További információ: a [Microsoft Identity platformot használó Xamarin Mobile-alkalmazás](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations)mintája. A következő táblázat összefoglalja a fontos információkat a README fájlban.

| Sample | Platform | Leírás |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | Egy egyszerű Xamarin. Forms-alkalmazás, amely bemutatja, hogyan hitelesítheti a Microsoft személyes fiókjait és az Azure AD-t az Azure AD 2,0-végponton keresztül a MSAL használatával. Az alkalmazás azt is bemutatja, hogyan lehet elérni Microsoft Graph és megjeleníti az eredményül kapott jogkivonatot. <br>![Hitelesítési folyamat ábrája](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
