---
title: 'Gyors útmutató: bejelentkezés felvétele a Microsofttal iOS-vagy macOS-alkalmazásba | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan jelentkezhet be egy iOS-vagy macOS-alkalmazás a felhasználókba, hogyan szerezhet be hozzáférési tokent a Microsoft Identity platformból, és meghívja a Microsoft Graph API-t.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/24/2019
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:iOS
ms.openlocfilehash: d2c5af6aeccfbae0851513ff575bde3c39e3ca5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103787"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>Gyors útmutató: bejelentkezés a felhasználókba és a Microsoft Graph API meghívása iOS-vagy macOS-alkalmazásból

Ebben a rövid útmutatóban egy kódrészletet tölt le és futtat, amely bemutatja, hogyan jelentkezhet be a natív iOS-vagy macOS-alkalmazás a felhasználókba, és hogyan kérhet hozzáférési jogkivonatot a Microsoft Graph API meghívásához.

A rövid útmutató az iOS-és macOS-alkalmazásokra is vonatkozik. Néhány lépésre csak az iOS-alkalmazások esetében van szükség, és a rendszer ezt fogja jelezni.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* XCode 10 +
* iOS 10 +
* macOS 10.12 +

## <a name="how-the-sample-works"></a>A minta működése

![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-ios/ios-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Expressz] [1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [Kézi] [2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>1. lehetőség: az alkalmazás regisztrálása és automatikus konfigurálása, majd a mintakód letöltése
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálása
> 1. Lépjen a <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Alkalmazásregisztrációk</a> gyors üzembe helyezési élményhez.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.    
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. Válassza a **Regisztráció** lehetőséget.
> 1. A **kezelés** területen válassza a **hitelesítés** a  >  **platform hozzáadása**  >  **iOS** lehetőséget.
> 1. Adja meg az alkalmazáshoz tartozó **köteg azonosítóját** . A köteg-azonosító egy egyedi karakterlánc, amely egyedileg azonosítja az alkalmazást, például: `com.<yourname>.identitysample.MSALMacOS` . Jegyezze fel a használt értéket. Vegye figyelembe, hogy az iOS-konfiguráció a macOS-alkalmazásokra is érvényes.
> 1. Válassza a **Konfigurálás** lehetőséget, majd mentse a **MSAL-konfiguráció** részleteit a rövid útmutató későbbi részében.
> 1. Válassza a **Kész** lehetőséget.

> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>1. lépés: Az alkalmazás konfigurálása
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, adjon hozzá egy **átirányítási URI** -t, amely kompatibilis a hitelesítési közvetítővel.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-ios/green-check.png) Az alkalmazás már konfigurálva van ezekkel az attribútumokkal
>
> #### <a name="step-2-download-the-sample-project"></a>2. lépés: a minta projekt letöltése
> > [!div id="autoupdate_ios" class="nextstepaction"]
> > [Az iOS-hez készült kód mintájának letöltése]()
>
> > [!div id="autoupdate_macos" class="nextstepaction"]
> > [Töltse le a macOS rendszerhez készült kód mintáját]()
> [!div renderon="docs"]
> #### <a name="step-2-download-the-sample-project"></a>2. lépés: a minta projekt letöltése
>
> - [Az iOS-hez készült kód mintájának letöltése](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
> - [Töltse le a macOS rendszerhez készült kód mintáját](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>3. lépés: a függőségek telepítése

A terminál ablakban navigáljon a letöltött kód minta nevű mappához, és futtassa a parancsot a `pod install` legújabb MSAL-könyvtár telepítéséhez.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-4-your-app-is-configured-and-ready-to-run"></a>4. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékével konfiguráltuk, és készen áll a futtatásra.
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`
>
> [!div renderon="docs"]
>#### <a name="step-4-configure-your-project"></a>4. lépés: a projekt konfigurálása
> Ha a fenti 1. lehetőséget választotta, akkor kihagyhatja ezeket a lépéseket.
> 1. Csomagolja ki a tömörített fájlt, és nyissa meg a projektet az XCode-ban.
> 1. Szerkessze a **ViewController. Swift** -et, és cserélje le a "Let kClientID" kezdetű sort a következő kódrészlettel. Ne felejtse el frissíteni a értéket az `kClientID` alkalmazás a portálon korábban a portálon való regisztrálása során mentett clientID:
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. Ha az [Azure ad nemzeti felhőkhöz](/graph/deployments#app-registration-and-token-service-root-endpoints)hoz létre alkalmazást, cserélje le a sort a "Let kGraphEndpoint" és a "Let kAuthority" kifejezésre a megfelelő végpontokkal. Globális hozzáférés esetén használja az alapértelmezett értékeket:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.com/"
>     let kAuthority = "https://login.microsoftonline.com/common"
>     ```
> 1. A többi végpontot [itt](/graph/deployments#app-registration-and-token-service-root-endpoints)dokumentálja. Ha például a gyors üzembe helyezést az Azure AD Germany használatával szeretné futtatni, használja a következőt:
>     ```swift
>     let kGraphEndpoint = "https://graph.microsoft.de/"
>     let kAuthority = "https://login.microsoftonline.de/common"
>     ```
> 1. Nyissa meg a projekt beállításait. Az **identitás** szakaszban adja meg a portálon megadott **köteg-azonosítót** .
> 1. Kattintson a jobb gombbal az **info. plist** elemre, és válassza **a Megnyitás**  >  **forrásként kód** lehetőséget.
> 1. A dict gyökérszintű csomópont alatt cserélje le a `Enter_the_bundle_Id_Here` -t a portálon használt ***csomag-azonosítóra*** .
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>
>    ```
> 1. Build & az alkalmazás futtatása!

## <a name="more-information"></a>További információ

Az erre a rövid útmutatóra vonatkozó további információért tekintse meg ezeket a szakaszokat.

### <a name="get-msal"></a>MSAL beolvasása

A MSAL ([MSAL. Framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kérhetnek. Az MSAL-t az alábbi eljárással adhatja hozzá az alkalmazáshoz:

```
$ vi Podfile

```
Adja hozzá a következőt ehhez a cocoapods (a projekt céljával):

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

CocoaPods-telepítési parancs futtatása:

```pod install```

### <a name="initialize-msal"></a>MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```swift
import MSAL
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)

let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |Ahol: | Leírás |
> |---------|---------|
> | `clientId` | A *portal.azure.com* webhelyen regisztrált alkalmazásból származó alkalmazásazonosító |
> | `authority` | A Microsoft Identity platform. A legtöbb esetben ez a következő lesz: `https://login.microsoftonline.com/common` |
> | `redirectUri` | Az alkalmazás átirányítási URI-ja. Az alapértelmezett értéket vagy az egyéni átirányítási URI-t a "Nil" értékre lehet átadni. |

### <a name="for-ios-only-additional-app-requirements"></a>Csak iOS esetén, további alkalmazásokra vonatkozó követelmények

Az alkalmazásnak a következőkkel is rendelkeznie kell a következővel: `AppDelegate` . Ez lehetővé teszi, hogy a MSAL SDK kezelje a jogkivonat választ az Auth Broker alkalmazástól a hitelesítés során.

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> Ha a helyett az iOS 13 + verziót alkalmazza, `UISceneDelegate` `UIApplicationDelegate` Helyezze el ezt a kódot a `scene:openURLContexts:` visszahívás helyett (lásd az [Apple dokumentációját](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)).
> Ha mind a UISceneDelegate, mind a UIApplicationDelegate támogatja a régebbi iOS-kompatibilitást, a MSAL visszahívást mindkét helyre kell helyezni.

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

Végezetül az alkalmazásnak tartalmaznia kell egy `LSApplicationQueriesSchemes` bejegyzést az ***info. plist*** fájl mellett `CFBundleURLTypes` . Ehhez a mintához tartozik.

   ```xml
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>Bejelentkezési felhasználók & kérelmek jogkivonatai

Az MSAL a következő két metódust használja a jogkivonatok beszerzéséhez: `acquireToken` és `acquireTokenSilent`

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: token beszerzése interaktív módon

Bizonyos helyzetekben a felhasználóknak a Microsoft Identity platformmal való interakcióra van szükségük. Ezekben az esetekben előfordulhat, hogy a végfelhasználónak ki kell választania a fiókját, meg kell adnia a hitelesítő adatait, vagy jóvá kell hagynia az alkalmazás engedélyeit. Példa:

* Az első alkalommal, amikor felhasználók bejelentkeznek az alkalmazásba
* Ha a felhasználó alaphelyzetbe állítja a jelszavát, meg kell adnia a hitelesítő adataikat
* Ha az alkalmazás első alkalommal kér hozzáférést egy erőforráshoz
* Ha MFA-vagy más feltételes hozzáférési szabályzatokra van szükség

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |Ahol:| Leírás |
> |---------|---------|
> | `scopes` | A kért hatóköröket tartalmazza (azaz `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni webes API-khoz ( `api://<Application ID>/access_as_user` ) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: hozzáférési token lekérése csendesen

Az alkalmazásoknak nem kell minden alkalommal bejelentkezniük a felhasználóktól, amikor jogkivonatot kérnek. Ha a felhasználó már bejelentkezett, ez a módszer lehetővé teszi, hogy az alkalmazások csendesen igényeljenek jogkivonatokat.

```swift
self.applicationContext!.getCurrentAccount(with: nil) { (currentAccount, previousAccount, error) in

   guard let account = currentAccount else {
      return
   }

   let silentParams = MSALSilentTokenParameters(scopes: self.kScopes, account: account)
   self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
}
```

> |Ahol: | Leírás |
> |---------|---------|
> | `scopes` | A kért hatóköröket tartalmazza (azaz `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni webes API-khoz ( `api://<Application ID>/access_as_user` ) |
> | `account` | Az a fiók, amelynek a tokenjét kérik. Ez a rövid útmutató egyetlen Account-alkalmazásról szól. Ha többfiókos alkalmazást szeretne létrehozni, meg kell határoznia a logikai azonosítót, amely meghatározza, hogy melyik fiókot használja a rendszer a jogkivonat-kérelmekhez a `accountsFromDeviceForParameters:completionBlock:` és a megfelelő érték beadásával. `accountIdentifier` |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

Lépjen be a lépésenkénti oktatóanyagba, amelyben létrehoz egy iOS-vagy macOS-alkalmazást, amely a Microsoft Identity platform hozzáférési jogkivonatát kéri le, és a Microsoft Graph API meghívására használja.

> [!div class="nextstepaction"]
> [Oktatóanyag: bejelentkezés a felhasználókba és Microsoft Graph meghívása iOS-vagy macOS-alkalmazásból](tutorial-v2-ios.md)
