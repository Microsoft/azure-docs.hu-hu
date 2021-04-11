---
title: Brókerek használata a Xamarin iOS & Android rendszerhez | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan állíthatja be a Xamarin iOS-alkalmazásokat, amelyek használhatják a Microsoft Authenticator és a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatát. Azt is megtudhatja, hogyan telepítheti át a .NET-hez készült Azure AD Authentication Library-ről (ADAL.NET) a .NET-hez készült Microsoft Authentication Library (MSAL.NET) rendszerre.
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 226e94510709b37a7e6b1aae90a7e0ec5b4222b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199578"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Microsoft Authenticator vagy Intune Céges portál használata Xamarin-alkalmazásokban

Android és iOS rendszeren a közvetítők, például a Microsoft Authenticator és az Android-specifikus Microsoft Intune céges portál lehetővé teszik a következőket:

- **Egyszeri bejelentkezés (SSO)**: a felhasználóknak nem kell bejelentkezniük minden alkalmazásba.
- **Eszköz azonosítása**: a közvetítő hozzáfér az eszköz tanúsítványához. Ez a tanúsítvány a munkahelyhez való csatlakozáskor jön létre az eszközön.
- **Alkalmazás-azonosítás ellenőrzése**: amikor egy alkalmazás meghívja a közvetítőt, átadja az átirányítási URL-címét. A közvetítő ellenőrzi az URL-címet.

Ezen funkciók egyikének engedélyezéséhez használja a `WithBroker()` paramétert a metódus meghívásakor `PublicClientApplicationBuilder.CreateApplication` . `.WithBroker()`Alapértelmezés szerint a paraméter értéke TRUE (igaz).

A (z) .NET-hez készült Microsoft Authentication Library (MSAL.NET) szolgáltatásban a felügyelt hitelesítés telepítése a platformtól függ:

* [iOS-alkalmazások](#brokered-authentication-for-ios)
* [Android-alkalmazások](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Felügyelt hitelesítés iOS rendszerhez

A következő lépésekkel engedélyezheti, hogy a Xamarin. iOS-alkalmazás kommunikáljon a [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) alkalmazással. Ha az iOS 13-at célozza meg, érdemes lehet beolvasni az [Apple megszakított API-változását](./msal-net-xamarin-ios-considerations.md).

### <a name="step-1-enable-broker-support"></a>1. lépés: a közvetítő támogatásának engedélyezése

Az egyes példányai esetében engedélyeznie kell a közvetítő támogatását `PublicClientApplication` . A támogatás alapértelmezés szerint le van tiltva. Amikor létrehozza `PublicClientApplication` `PublicClientApplicationBuilder` a-t, a `WithBroker()` következő példában látható módon használja a paramétert. `WithBroker()`Alapértelmezés szerint a paraméter értéke TRUE (igaz).

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>2. lépés: kulcstartó-hozzáférés engedélyezése

A kulcstartó-hozzáférés engedélyezéséhez rendelkeznie kell egy kulcstartó-hozzáférési csoporttal az alkalmazáshoz. Az `WithIosKeychainSecurityGroup()` API-t az alkalmazás létrehozásakor a kulcstartó-hozzáférési csoport beállításához használhatja:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

További információ: a [kulcstartók hozzáférésének engedélyezése](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>3. lépés: a visszahívás kezeléséhez a AppDelegate frissítése

Amikor a MSAL.NET meghívja a közvetítőt, a közvetítő az osztály metódusával visszahívja az alkalmazást `OpenUrl` `AppDelegate` . Mivel a MSAL megvárja a közvetítő válaszát, az alkalmazásnak együtt kell működnie a MSAL.NET visszahívásához. Az együttműködés engedélyezéséhez frissítse a *AppDelegate. cs* fájlt a következő módszer felülbírálásához.

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

Ezt a metódust az alkalmazás minden indításakor meghívja a rendszer. Lehetőség van arra, hogy feldolgozza a közvetítő válaszát, és elvégezze a MSAL.NET által elindított hitelesítési folyamatot.

### <a name="step-4-set-uiviewcontroller"></a>4. lépés: a UIViewController beállítása ()

Továbbra is az *AppDelegate. cs* fájlban adja meg az objektum ablakát. Általában nem kell beállítania az Xamarin iOS-hez készült Object (objektum) ablakot, de szüksége van egy objektumra a brókertől érkező válaszok küldéséhez és fogadásához.

Az objektum ablakának beállítása:

1. A *AppDelegate. cs* fájlban állítsa `App.RootViewController` egy új értékre `UIViewController()` . Ez a hozzárendelés biztosítja, hogy a közvetítő hívása tartalmazza a-t `UIViewController` . Ha a beállítás helytelenül van hozzárendelve, a következő hibaüzenet jelenhet meg:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. A `AcquireTokenInteractive` hívásnál használja a (z `.WithParentActivityOrWindow(App.RootViewController)` ), majd adja át a hivatkozást a használni kívánt objektum-ablakra.

    Az *app. cs*:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    A *AppDelegate. cs*:

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

### <a name="step-5-register-a-url-scheme"></a>5. lépés: URL-séma regisztrálása

A MSAL.NET URL-címeket használ a közvetítő meghívásához, majd visszaküldi az alkalmazásnak a közvetítő válaszát. A kör alakú útvonal befejezéséhez regisztráljon egy URL-sémát az alkalmazáshoz az *info. plist* fájlban.

A `CFBundleURLSchemes` névnek előtagként kell szerepelnie `msauth.` . Kövesse az előtagot a következővel: `CFBundleURLName` .

Az URL-sémában `BundleId` egyedi módon azonosítja az alkalmazást: `$"msauth.(BundleId)"` . Tehát ha `BundleId` Igen `com.yourcompany.xforms` , akkor az URL-séma `msauth.com.yourcompany.xforms` .

> [!NOTE]
> Ez az URL-séma az átirányítási URI részévé válik, amely egyedileg azonosítja az alkalmazást, amikor megkapja a választ a közvetítőtől.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>6. lépés: az ügynök azonosítójának hozzáadása a összes szakaszhoz

A MSAL `–canOpenURL:` segítségével ellenőrizhető, hogy a közvetítő telepítve van-e az eszközön. Az iOS 9-es verzióban az Apple zárolta azokat a sémákat, amelyeket az alkalmazás lekérhet.

Adja hozzá az `msauthv2` `LSApplicationQueriesSchemes` *info. plist* fájl szakaszához az alábbi példában látható módon:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>7. lépés: átirányítási URI hozzáadása az alkalmazás regisztrálásához

A közvetítő használatakor az átirányítási URI-nak külön követelménye van. Az átirányítási URI formátumának a következő formátumúnak kell _lennie_ :

```csharp
$"msauth.{BundleId}://auth"
```

Bemutatunk egy példát:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

Figyelje meg, hogy az átirányítási URI megegyezik az `CFBundleURLSchemes` *info. plist* fájlban szereplő névvel.

Adja hozzá az átirányítási URI-t az alkalmazás regisztrálásához a [Azure Portalban](https://portal.azure.com). Megfelelően formázott átirányítási URI létrehozásához használja a Azure Portal **Alkalmazásregisztrációk** , hogy létrehozza a felügyelt átirányítási URI-t a köteg-azonosítóból.

**Az átirányítási URI előállítása:**

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Válassza ki **Azure Active Directory**  >  **Alkalmazásregisztrációk** a regisztrált alkalmazás >
1. **Hitelesítés** kiválasztása  >    >  **iOS/MacOS** platform hozzáadása
1. Adja meg a köteg AZONOSÍTÓját, majd válassza a **Konfigurálás** lehetőséget.

    Másolja az **átirányítási URI** -szövegmezőben megjelenő generált ÁTirányítási URI-t a kódban való felvételhez:

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="iOS-platform beállításai a generált átirányítási URI-val Azure Portal":::
1. Az átirányítási URI létrehozásának befejezéséhez válassza a **kész** lehetőséget.

## <a name="brokered-authentication-for-android"></a>Felügyelt hitelesítés Android rendszerhez

### <a name="step-1-enable-broker-support"></a>1. lépés: a közvetítő támogatásának engedélyezése

A közvetítői támogatás alapja az engedélyezett `PublicClientApplication` . Alapértelmezés szerint le van tiltva. Használja a `WithBroker()` paramétert (alapértelmezés szerint igaz értékre állítva), amikor létrehozza a-t `IPublicClientApplication` `PublicClientApplicationBuilder` .

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>2. lépés: a visszahívást kezelő AppDelegate frissítése

Ha a MSAL.NET meghívja a közvetítőt, akkor a közvetítő a metódussal hívja vissza az alkalmazást `OnActivityResult()` . Mivel a MSAL megvárja a közvetítő válaszát, az alkalmazásnak az eredményt MSAL.NET kell irányítani.

A metódust az itt látható módon irányítsa át a `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` metódusnak `OnActivityResult()` :

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Ezt a metódust a rendszer minden alkalommal meghívja a közvetítő alkalmazás indításakor, és lehetőséget biztosít a közvetítő válaszának feldolgozására és a MSAL.NET által elindított hitelesítési folyamat befejezésére.

### <a name="step-3-set-an-activity"></a>3. lépés: tevékenység beállítása

A felügyelt hitelesítés engedélyezéséhez állítson be egy tevékenységet, hogy a MSAL képes legyen elküldeni és fogadni a közvetítőtől érkező és onnan érkező választ. Ehhez adja meg a fölérendelt objektum tevékenységét (általában a `MainActivity` ) `WithParentActivityOrWindow(object parent)` .

Például a következő hívásban `AcquireTokenInteractive()` :

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>4. lépés: átirányítási URI hozzáadása az alkalmazás regisztrálásához

A MSAL URL-címeket használ a közvetítő meghívásához, majd visszatér az alkalmazáshoz. Az adott kör átirányításának befejezéséhez regisztráljon egy **átirányítási URI** -t az alkalmazáshoz a [Azure Portal](https://portal.azure.com)használatával.

Az alkalmazás átirányítási URI formátuma az APK aláírásához használt tanúsítványtól függ. Például:

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Az URI utolsó része, a ( `hgbUYHVBYUTvuvT&Y6tr554365466=` ) az az aláírás Base64 kódolású verziója, amelyhez az apk van aláírva. Ha az alkalmazást a Visual Studióban fejleszti, és az APK egy adott tanúsítvánnyal való aláírása nélkül végzi a kód hibakeresését, a Visual Studio hibakeresési célból aláírja az APK-ot. Ha a Visual Studio ily módon aláírja az APK-t, a rendszer egyedi aláírást biztosít a beépített gépre. Így minden alkalommal, amikor az alkalmazást egy másik gépen hozza létre, frissítenie kell az átirányítási URI-t az alkalmazás kódjában, valamint az alkalmazás regisztrációját a Azure Portal a MSAL való hitelesítéshez.

A hibakeresés során előfordulhat, hogy a MSAL-kivétel (vagy a naplófájl) helytelenül van megadva az átirányítási URI-nak. **A kivétel vagy a napló üzenet azt is jelzi, hogy milyen átirányítási URI-t kell használnia** az aktuálisan felkeresett géppel. A megadott átirányítási URI használatával folytathatja az alkalmazás fejlesztését, feltéve, hogy az átirányítási URI-t a kódban frissíti, és hozzáadja a megadott átirányítási URI-t az alkalmazás regisztrálásához a Azure Portal.

Ha készen áll a kód véglegesítésére, frissítse az átirányítási URI-t a kódban, és az alkalmazás regisztrációját a Azure Portalban, hogy az a tanúsítvány aláírását használja, amellyel aláírja az APK-t.

A gyakorlatban ez azt jelenti, hogy meg kell fontolnia egy átirányítási URI hozzáadását a fejlesztői csapat minden tagjához, *valamint* egy ÁTirányítási URI-t az apk éles aláírt verziójához.

Saját maga is kiszámíthatja az aláírást, hasonlóan ahhoz, ahogyan a MSAL:

```csharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Azt is megteheti **, hogy** az alábbi parancsokkal megszerezheti a csomag aláírását:

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* MacOS
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>5. lépés (nem kötelező): a rendszerböngészőbe való visszatérés visszaesik

Ha a MSAL a közvetítő használatára van konfigurálva, de a közvetítő nincs telepítve, a MSAL webes nézet (böngésző) használatával fog visszaesni. A MSAL az eszközön az alapértelmezett rendszerböngészővel kísérli meg a hitelesítést, mert az átirányítási URI konfigurálva van a közvetítőhöz, és a rendszerböngésző nem tudja, hogyan használhatja azt a MSAL való visszatéréshez. A hiba elkerüléséhez beállíthat egy *leképezési szűrőt* a 4. lépésben használt közvetítő átirányítási URI-val.

Módosítsa az alkalmazás jegyzékfájlját a leképezési szűrő hozzáadásához:

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

Ha például van egy átirányítási URI `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` -ja, a jegyzékfájlnak a következő XML-kódrészlethez hasonlóan kell kinéznie.

Az értékben lévő aláírás előtt a Forward-Slash ( `/` ) `android:path` értéket **kötelező megadni**.

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

Az alkalmazás rendszerböngészővel és az Android 11 támogatással való konfigurálásával kapcsolatos további információkért lásd: [az Android-jegyzékfájl frissítése a rendszerböngésző támogatásához](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support).

Másik lehetőségként beállíthatja, hogy a MSAL a beágyazott böngészőre térjen vissza, amely nem az átirányítási URI-ra támaszkodik:

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Hibaelhárítási tippek az Android rendszerű felügyelt hitelesítéshez

Íme néhány tipp a probléma elkerüléséhez, amikor felügyelt hitelesítést hajt végre az Androidon:

- **Átirányítási URI** – adjon hozzá egy átirányítási URI-t az alkalmazás regisztrálásához a [Azure Portalban](https://portal.azure.com/). Egy hiányzó vagy helytelen átirányítási URI a fejlesztők által észlelt gyakori probléma.
- **Broker verziója** – telepítse a Broker-alkalmazások minimálisan szükséges verzióját. A két alkalmazás közül bármelyik használható felügyelt hitelesítéshez Androidon.
  - [Intune céges portál](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (5.0.4689.0 vagy újabb verzió)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (6.2001.0140 vagy újabb verzió).
- **Közvetítői prioritás** – a MSAL az eszközön *telepített első közvetítővel* kommunikál, ha több közvetítő van telepítve.

    Példa: Ha először telepíti a Microsoft Authenticatort, majd telepíti a Intune Céges portál, a felügyelt hitelesítés *csak* a Microsoft Authenticator történik.
- **Naplók** – ha a közvetítő hitelesítéssel kapcsolatos problémába ütközik, a közvetítő naplófájljainak megtekintése segíthet az OK diagnosztizálásában.
  - Microsoft Authenticator naplók beolvasása:

    1. Kattintson a menü gombra az alkalmazás jobb felső sarkában.
    1. Válassza a **visszajelzés küldése**  >  **problémát?** lehetőséget.
    1. A **mit próbál tenni?** területen válasszon ki egy lehetőséget, és adjon hozzá egy leírást.
    1. A naplók elküldéséhez kattintson a nyílra az alkalmazás jobb felső sarkában.

    A naplók elküldése után egy párbeszédpanel megjeleníti az incidens AZONOSÍTÓját. Jegyezze fel az incidens AZONOSÍTÓját, és adja meg a Segítség kérése során.

  - Intune Céges portál naplók beolvasása:

    1. Kattintson a menü gombra az alkalmazás bal felső sarkában.
    1. Válassza a **Súgó**  >  **e-mail támogatás** lehetőséget.
    1. A naplók elküldéséhez válassza a **naplók feltöltése** lehetőséget.

    A naplók elküldése után egy párbeszédpanel megjeleníti az incidens AZONOSÍTÓját. Jegyezze fel az incidens AZONOSÍTÓját, és adja meg a Segítség kérése során.

## <a name="next-steps"></a>Következő lépések

Tudnivalók a [univerzális Windows-platform és a MSAL.net használatának szempontjairól](msal-net-uwp-considerations.md).
