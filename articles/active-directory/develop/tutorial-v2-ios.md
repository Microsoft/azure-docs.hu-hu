---
title: 'Oktatóanyag: a Microsoft Identity platformot használó iOS-vagy macOS-alkalmazás létrehozása hitelesítéshez | Azure'
titleSuffix: Microsoft identity platform
description: Ebben az oktatóanyagban olyan iOS-vagy macOS-alkalmazást hoz létre, amely a Microsoft Identity platform használatával jelentkezik be a felhasználókba, és hozzáférési jogkivonatot kap a Microsoft Graph API nevében való meghívásához.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 09/18/2020
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: e273b1c41a9c418bf0121e87e73ec59e65f2242e
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102954"
---
# <a name="tutorial-sign-in-users-and-call-microsoft-graph-from-an-ios-or-macos-app"></a>Oktatóanyag: bejelentkezés a felhasználókba és Microsoft Graph meghívása iOS-vagy macOS-alkalmazásból

Ebben az oktatóanyagban olyan iOS-vagy macOS-alkalmazást hoz létre, amely integrálható a Microsoft Identity platformmal a felhasználók aláírására és hozzáférési token beszerzésére a Microsoft Graph API meghívásához.

Az útmutató elvégzése után az alkalmazás elfogadja a személyes Microsoft-fiókok (például a outlook.com, a live.com és mások) és a munkahelyi vagy iskolai fiókok bejelentkezési adatait bármely olyan vállalattól vagy szervezettől, amely Azure Active Directoryt használ. Ez az oktatóanyag az iOS-és macOS-alkalmazásokra is érvényes. Egyes lépések eltérnek a két platform között.

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * IOS-vagy macOS-alkalmazások projekt létrehozása a *Xcode* -ben
> * Az alkalmazás regisztrálása a Azure Portalban
> * Kód hozzáadása a felhasználói bejelentkezés és a kijelentkezés támogatásához
> * Kód hozzáadása a Microsoft Graph API meghívásához
> * Az alkalmazás tesztelése

## <a name="prerequisites"></a>Előfeltételek

- [Xcode 11. x +](https://developer.apple.com/xcode/)

## <a name="how-tutorial-app-works"></a>Az oktatóanyag-alkalmazás működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

Az oktatóanyagban szereplő alkalmazás bejelentkezhet a felhasználókba, és lekérheti az adatok beszerzését Microsoft Graphról az Ön nevében. Ezek az adatok egy védett API-val (ebben az esetben Microsoft Graph API-val) lesznek elérhetők, és a Microsoft Identity platform védi a hitelesítést.

Pontosabban:

* Az alkalmazás egy böngészőben vagy a Microsoft Authenticator keresztül fog bejelentkezni a felhasználóba.
* A végfelhasználó el fogja fogadni az alkalmazás által kért engedélyeket.
* Az alkalmazás egy hozzáférési jogkivonatot fog kiadni a Microsoft Graph API-hoz.
* A hozzáférési jogkivonatot a rendszer a webes API-hoz tartozó HTTP-kérelemben fogja tartalmazni.
* Dolgozza fel a Microsoft Graph választ.

Ez a példa a Microsoft Authentication Library (MSAL) használatával valósítja meg a hitelesítést. A MSAL automatikusan megújítja a tokeneket, egyszeri bejelentkezést (SSO) tesz elérhetővé az eszköz más alkalmazásai között, és felügyeli a fiók (oka) t.

Ha le szeretné tölteni az oktatóanyagban felépített alkalmazás befejezett verzióját, mindkét verziót megtalálhatja a GitHubon is:

- [iOS-kód minta](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/) (GitHub)
- [MacOS-mintakód](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/) (GitHub)

## <a name="create-a-new-project"></a>Új projekt létrehozása

1. Nyissa meg a Xcode, és válassza **az új Xcode-projekt létrehozása** lehetőséget.
2. IOS-alkalmazások esetén válassza az **iOS**  >  **Egynézetű alkalmazás** lehetőséget, és kattintson a **Tovább gombra**.
3. MacOS-alkalmazások esetén válassza a **MacOS**  >  **kakaó-alkalmazás** lehetőséget, majd kattintson a **Tovább gombra**.
4. Adja meg a terméknév nevét.
5. Állítsa a **nyelvet** a **Swift** értékre, és válassza a **tovább** lehetőséget.
6. Válasszon egy mappát az alkalmazás létrehozásához, majd válassza a **Létrehozás** lehetőséget.

## <a name="register-your-application"></a>Az alkalmazás regisztrálása

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
1. Válassza **a fiókok lehetőséget bármely szervezeti címtárban (bármely Azure ad-címtár – több-bérlő) és a személyes Microsoft-fiókok (például Skype, Xbox)** a **támogatott fióktípus** területen.
1. Válassza a **Regisztráció** lehetőséget.
1. A **kezelés** területen válassza   >  **a hitelesítés Hozzáadás platform**  >  **iOS/MacOS** lehetőséget.
1. Adja meg a projekt köteg-AZONOSÍTÓját. Ha letöltötte a kódot, akkor ez a következő: `com.microsoft.identitysample.MSALiOS` . Ha saját projektet hoz létre, válassza ki a projektet a Xcode-ben, és nyissa meg az **általános** lapot. A köteg azonosítója az Identity ( **identitás** ) szakaszban jelenik meg.
1. Válassza a **Konfigurálás** lehetőséget, és mentse a **MSAL-konfigurációt** , amely megjelenik a **MSAL-konfiguráció** lapon, így később is megadhatja az alkalmazást. 
1. Válassza a **Kész** lehetőséget.

## <a name="add-msal"></a>MSAL hozzáadása

Válassza ki az alábbi módszerek egyikét a MSAL-könyvtár telepítéséhez az alkalmazásban:

### <a name="cocoapods"></a>CocoaPods

1. Ha a [CocoaPods](https://cocoapods.org/)-t használja, `MSAL` először hozzon létre egy üres fájlt, `podfile` amely ugyanabban a mappában található, mint a projekt `.xcodeproj` fájlja. Adja hozzá a következőket a következőhöz `podfile` :

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

2. Cserélje le a helyére `<your-target-here>` a projekt nevét.
3. Egy terminál ablakban navigáljon a `podfile` létrehozott és futtatott mappához a `pod install` MSAL-könyvtár telepítéséhez.
4. A Xcode bezárásához és megnyitásához nyissa meg `<your project name>.xcworkspace` újra a projektet a Xcode-ben.

### <a name="carthage"></a>Carthage

Ha a [Carthage](https://github.com/Carthage/Carthage)-t használja, a telepítéshez adja hozzá a következőt `MSAL` `Cartfile` :

```
github "AzureAD/microsoft-authentication-library-for-objc" "master"
```

Futtassa a következő parancsot egy terminál-ablakból ugyanabban a címtárban, amely a frissített `Cartfile` , majd futtassa az alábbi parancsot, hogy a Carthage frissítse a projekt függőségeit.

iOS:

```bash
carthage update --platform iOS
```

MacOS

```bash
carthage update --platform macOS
```

### <a name="manually"></a>Manuálisan

A git almodult is használhatja, vagy megtekintheti a legújabb kiadást, amelyet keretrendszerként használhat az alkalmazásában.

## <a name="add-your-app-registration"></a>Az alkalmazás regisztrációjának hozzáadása

Ezután hozzáadjuk az alkalmazás regisztrációját a kódhoz.

Először adja hozzá a következő importálási utasítást a, valamint a `ViewController.swift` `AppDelegate.swift` vagy a fájl elejéhez `SceneDelegate.swift` :

```swift
import MSAL
```

Ezután adja hozzá a következő kódot a `ViewController.swift` előtt `viewDidLoad()` :

```swift
// Update the below to your client ID you received in the portal. The below is for running the demo only
let kClientID = "Your_Application_Id_Here"
let kGraphEndpoint = "https://graph.microsoft.com/" // the Microsoft Graph endpoint
let kAuthority = "https://login.microsoftonline.com/common" // this authority allows a personal Microsoft account and a work or school account in any organization's Azure AD tenant to sign in

let kScopes: [String] = ["user.read"] // request permission to read the profile of the signed-in user

var accessToken = String()
var applicationContext : MSALPublicClientApplication?
var webViewParameters : MSALWebviewParameters?
var currentAccount: MSALAccount?
```

Az egyetlen módosítva érték az `kClientID` [alkalmazás-azonosítóhoz](./developer-glossary.md#application-id-client-id)rendelt érték. Ez az érték azon MSAL-konfigurációs adatmennyiség részét képezi, amelyet az oktatóanyag elején a lépés során mentett, hogy regisztrálja az alkalmazást a Azure Portalban.

## <a name="configure-xcode-project-settings"></a>Xcode-projekt beállításainak konfigurálása

Vegyen fel egy új kulcstartó-csoportot a projekt **aláírási & képességeibe**. A kulcstartó csoportnak iOS és macOS rendszeren kell lennie `com.microsoft.adalcache` `com.microsoft.identity.universalstorage` .

![Xcode felhasználói felület, amely megjeleníti a kulcstartó csoport beállításának módját](../../../includes/media/active-directory-develop-guidedsetup-ios-introduction/iosintro-keychainShare.png)

## <a name="for-ios-only-configure-url-schemes"></a>Csak iOS esetén konfigurálja az URL-sémákat

Ebben a lépésben regisztrálni fogja, `CFBundleURLSchemes` hogy a felhasználó átirányítható legyen az alkalmazásba a bejelentkezés után. A módon `LSApplicationQueriesSchemes` lehetővé teszi, hogy az alkalmazás használja a Microsoft Authenticator.

A Xcode nyissa meg a `Info.plist` forrásként szolgáló fájlt, és adja hozzá a következőt a `<dict>` szakaszhoz. Cserélje le a `[BUNDLE_ID]` értéket a Azure Portal használt értékre. Ha letöltötte a kódot, a köteg azonosítója `com.microsoft.identitysample.MSALiOS` . Ha saját projektet hoz létre, válassza ki a projektet a Xcode-ben, és nyissa meg az **általános** lapot. A köteg azonosítója az Identity ( **identitás** ) szakaszban jelenik meg.

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
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="for-macos-only-configure-app-sandbox"></a>Csak macOS esetén konfigurálja az alkalmazási homokozót

1. Nyissa meg a Xcode-projekt beállításai > **képességek lap**  >  **alkalmazásának homokozóját**
2. Válassza a **Kimenő kapcsolatok (ügyfél)** jelölőnégyzetet.

## <a name="create-your-apps-ui"></a>Az alkalmazás felhasználói felületének létrehozása

Most hozzon létre egy felhasználói felületet, amely tartalmaz egy gombot a Microsoft Graph API meghívásához, egy másikat a kijelentkezéshez, valamint egy szöveges nézetet, hogy a következő kódot adja hozzá a `ViewController` osztályhoz:

### <a name="ios-ui"></a>iOS felhasználói felület

```swift
var loggingText: UITextView!
var signOutButton: UIButton!
var callGraphButton: UIButton!
var usernameLabel: UILabel!

func initUI() {

    usernameLabel = UILabel()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.text = ""
    usernameLabel.textColor = .darkGray
    usernameLabel.textAlignment = .right

    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true
    usernameLabel.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    usernameLabel.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add call Graph button
    callGraphButton  = UIButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.setTitle("Call Microsoft Graph API", for: .normal)
    callGraphButton.setTitleColor(.blue, for: .normal)
    callGraphButton.addTarget(self, action: #selector(callGraphAPI(_:)), for: .touchUpInside)
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 120.0).isActive = true
    callGraphButton.widthAnchor.constraint(equalToConstant: 300.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add sign out button
    signOutButton = UIButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.setTitle("Sign Out", for: .normal)
    signOutButton.setTitleColor(.blue, for: .normal)
    signOutButton.setTitleColor(.gray, for: .disabled)
    signOutButton.addTarget(self, action: #selector(signOut(_:)), for: .touchUpInside)
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    let deviceModeButton = UIButton()
    deviceModeButton.translatesAutoresizingMaskIntoConstraints = false
    deviceModeButton.setTitle("Get device info", for: .normal);
    deviceModeButton.setTitleColor(.blue, for: .normal);
    deviceModeButton.addTarget(self, action: #selector(getDeviceMode(_:)), for: .touchUpInside)
    self.view.addSubview(deviceModeButton)

    deviceModeButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    deviceModeButton.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    deviceModeButton.widthAnchor.constraint(equalToConstant: 150.0).isActive = true
    deviceModeButton.heightAnchor.constraint(equalToConstant: 50.0).isActive = true

    // Add logging textfield
    loggingText = UITextView()
    loggingText.isUserInteractionEnabled = false
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: deviceModeButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: 10.0).isActive = true
}

func platformViewDidLoadSetup() {

    NotificationCenter.default.addObserver(self,
                        selector: #selector(appCameToForeGround(notification:)),
                        name: UIApplication.willEnterForegroundNotification,
                        object: nil)

}

@objc func appCameToForeGround(notification: Notification) {
    self.loadCurrentAccount()
}

```

### <a name="macos-ui"></a>macOS felhasználói felület

```swift

var callGraphButton: NSButton!
var loggingText: NSTextView!
var signOutButton: NSButton!

var usernameLabel: NSTextField!

func initUI() {

    usernameLabel = NSTextField()
    usernameLabel.translatesAutoresizingMaskIntoConstraints = false
    usernameLabel.stringValue = ""
    usernameLabel.isEditable = false
    usernameLabel.isBezeled = false
    self.view.addSubview(usernameLabel)

    usernameLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30.0).isActive = true
    usernameLabel.rightAnchor.constraint(equalTo: view.rightAnchor, constant: -10.0).isActive = true

    // Add call Graph button
    callGraphButton  = NSButton()
    callGraphButton.translatesAutoresizingMaskIntoConstraints = false
    callGraphButton.title = "Call Microsoft Graph API"
    callGraphButton.target = self
    callGraphButton.action = #selector(callGraphAPI(_:))
    callGraphButton.bezelStyle = .rounded
    self.view.addSubview(callGraphButton)

    callGraphButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    callGraphButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 50.0).isActive = true
    callGraphButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true

    // Add sign out button
    signOutButton = NSButton()
    signOutButton.translatesAutoresizingMaskIntoConstraints = false
    signOutButton.title = "Sign Out"
    signOutButton.target = self
    signOutButton.action = #selector(signOut(_:))
    signOutButton.bezelStyle = .texturedRounded
    self.view.addSubview(signOutButton)

    signOutButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    signOutButton.topAnchor.constraint(equalTo: callGraphButton.bottomAnchor, constant: 10.0).isActive = true
    signOutButton.heightAnchor.constraint(equalToConstant: 34.0).isActive = true
    signOutButton.isEnabled = false

    // Add logging textfield
    loggingText = NSTextView()
    loggingText.translatesAutoresizingMaskIntoConstraints = false

    self.view.addSubview(loggingText)

    loggingText.topAnchor.constraint(equalTo: signOutButton.bottomAnchor, constant: 10.0).isActive = true
    loggingText.leftAnchor.constraint(equalTo: self.view.leftAnchor, constant: 10.0).isActive = true
    loggingText.rightAnchor.constraint(equalTo: self.view.rightAnchor, constant: -10.0).isActive = true
    loggingText.bottomAnchor.constraint(equalTo: self.view.bottomAnchor, constant: -10.0).isActive = true
    loggingText.widthAnchor.constraint(equalToConstant: 500.0).isActive = true
    loggingText.heightAnchor.constraint(equalToConstant: 300.0).isActive = true
}

func platformViewDidLoadSetup() {}

```

Ezután az osztályban belül is `ViewController` cserélje le a metódust a következőre `viewDidLoad()` :

```swift
    override func viewDidLoad() {

        super.viewDidLoad()

        initUI()

        do {
            try self.initMSAL()
        } catch let error {
            self.updateLogging(text: "Unable to create Application Context \(error)")
        }

        self.loadCurrentAccount()
        self.platformViewDidLoadSetup()
    }
```

## <a name="use-msal"></a>MSAL használata

### <a name="initialize-msal"></a>MSAL inicializálása

Adja hozzá a következő `initMSAL` metódust a `ViewController` osztályhoz:

```swift
    func initMSAL() throws {

        guard let authorityURL = URL(string: kAuthority) else {
            self.updateLogging(text: "Unable to create authority URL")
            return
        }

        let authority = try MSALAADAuthority(url: authorityURL)

        let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
        self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
        self.initWebViewParams()
    }
```

Adja hozzá a következő `initMSAL` metódust a `ViewController` osztályhoz.

### <a name="ios-code"></a>iOS-kód:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    }
```

### <a name="macos-code"></a>macOS-kód:

```swift
func initWebViewParams() {
        self.webViewParameters = MSALWebviewParameters()
    }
```

### <a name="for-ios-only-handle-the-sign-in-callback"></a>Csak iOS esetén kezelje a bejelentkezési visszahívást

Nyissa meg az `AppDelegate.swift` fájlt. Ha a bejelentkezés után szeretné kezelni a visszahívást, vegye fel `MSALPublicClientApplication.handleMSALResponse` a `appDelegate` következőhöz hasonló osztályba:

```swift
// Inside AppDelegate...
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}

```

**Ha a Xcode 11**-et használja, helyette a MSAL-visszahívást kell elhelyeznie `SceneDelegate.swift` .
Ha mind a UISceneDelegate, mind a UIApplicationDelegate támogatja a régebbi iOS-kompatibilitást, akkor a MSAL visszahívást mindkét fájlba be kell helyezni.

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

#### <a name="acquire-tokens"></a>Jogkivonatok beszerzése

Most megvalósíthatja az alkalmazás felhasználói felületének feldolgozási logikáját, és interaktív módon lekérheti a jogkivonatokat a MSAL használatával.

A MSAL két elsődleges módszert tesz elérhetővé a tokenek beszerzéséhez: `acquireTokenSilently()` és `acquireTokenInteractively()` :

- `acquireTokenSilently()` megpróbál bejelentkezni egy felhasználóba, és felhasználói beavatkozás nélkül kap jogkivonatokat, amennyiben van ilyen fiók. `acquireTokenSilently()` a `MSALAccount` MSAL-fiók enumerálási API-k egyikével lekérhető érvényes. Ez a példa `applicationContext.getCurrentAccount(with: msalParameters, completionBlock: {})` az aktuális fiók lekérésére használja.

- `acquireTokenInteractively()` mindig a felhasználói FELÜLETET jeleníti meg, amikor megpróbál bejelentkezni a felhasználóba. A böngészőben vagy a Microsoft hitelesítő egyik fiókjában munkamenet-cookie-kat is használhat interaktív egyszeri bejelentkezéses felhasználói élmény biztosításához.

Adja hozzá a következő kódot a `ViewController` osztályhoz:

```swift
    func getGraphEndpoint() -> String {
        return kGraphEndpoint.hasSuffix("/") ? (kGraphEndpoint + "v1.0/me/") : (kGraphEndpoint + "/v1.0/me/");
    }

    @objc func callGraphAPI(_ sender: AnyObject) {

        self.loadCurrentAccount { (account) in

            guard let currentAccount = account else {

                // We check to see if we have a current logged in account.
                // If we don't, then we need to sign someone in.
                self.acquireTokenInteractively()
                return
            }

            self.acquireTokenSilently(currentAccount)
        }
    }

    typealias AccountCompletion = (MSALAccount?) -> Void

    func loadCurrentAccount(completion: AccountCompletion? = nil) {

        guard let applicationContext = self.applicationContext else { return }

        let msalParameters = MSALParameters()
        msalParameters.completionBlockQueue = DispatchQueue.main

        applicationContext.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

            if let error = error {
                self.updateLogging(text: "Couldn't query current account with error: \(error)")
                return
            }

            if let currentAccount = currentAccount {

                self.updateLogging(text: "Found a signed in account \(String(describing: currentAccount.username)). Updating data for that account...")

                self.updateCurrentAccount(account: currentAccount)

                if let completion = completion {
                    completion(self.currentAccount)
                }

                return
            }

            self.updateLogging(text: "Account signed out. Updating UX")
            self.accessToken = ""
            self.updateCurrentAccount(account: nil)

            if let completion = completion {
                completion(nil)
            }
        })
    }
```

#### <a name="get-a-token-interactively"></a>Token interaktív beszerzése

A következő kódrészlet első alkalommal kap egy jogkivonatot egy objektum létrehozásával `MSALInteractiveTokenParameters` és meghívásával `acquireToken` . Ezután a következő kódot fogja hozzáadni:

1. `MSALInteractiveTokenParameters`Hatókörökkel jön létre.
2. Meghívja `acquireToken()` a létrehozott paramétereket.
3. Kezeli a hibákat. További részletekért tekintse meg a [MSAL for iOS és a MacOS hibakezelés útmutatóját](msal-error-handling-ios.md).
4. Kezeli a sikeres esetet.

Adja hozzá az alábbi kódot a `ViewController` osztályhoz.

```swift
func acquireTokenInteractively() {

    guard let applicationContext = self.applicationContext else { return }
    guard let webViewParameters = self.webViewParameters else { return }

    // #1
    let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: webViewParameters)
    parameters.promptType = .selectAccount

    // #2
    applicationContext.acquireToken(with: parameters) { (result, error) in

        // #3
        if let error = error {

            self.updateLogging(text: "Could not acquire token: \(error)")
            return
        }

        guard let result = result else {

            self.updateLogging(text: "Could not acquire token: No result returned")
            return
        }

        // #4
        self.accessToken = result.accessToken
        self.updateLogging(text: "Access token is \(self.accessToken)")
        self.updateCurrentAccount(account: result.account)
        self.getContentWithToken()
    }
}
```

A `promptType` tulajdonsága `MSALInteractiveTokenParameters` konfigurálja a hitelesítési és a hozzájárulási kérések viselkedését. A következő értékek támogatottak:

- `.promptIfNecessary` (alapértelmezett) – a rendszer csak szükség esetén kéri a felhasználót. Az egyszeri bejelentkezés felhasználói felületét a webnézetben található cookie-k, valamint a fiók típusa határozza meg. Ha több felhasználó is be van jelentkezve, a rendszer a fiók kiválasztási élményét mutatja be. *Ez az alapértelmezett viselkedés*.
- `.selectAccount` – Ha nincs megadva felhasználó, a hitelesítés webnézete megjeleníti az aktuálisan bejelentkezett fiókok listáját, amelyből a felhasználó kiválaszthat.
- `.login` – A felhasználónak hitelesítenie kell magát a webnézetben. Ha ezt az értéket megadja, egyszerre csak egy fiókba lehet bejelentkezni.
- `.consent` – A felhasználónak jóvá kell hagynia a kérelem hatókörének aktuális készletét.

#### <a name="get-a-token-silently"></a>Token lekérése csendesen

A frissített jogkivonat csendes beszerzéséhez adja hozzá a következő kódot a `ViewController` osztályhoz. Létrehoz egy `MSALSilentTokenParameters` objektumot és hívásokat `acquireTokenSilent()` :

```swift

    func acquireTokenSilently(_ account : MSALAccount!) {

        guard let applicationContext = self.applicationContext else { return }

        /**

         Acquire a token for an existing account silently

         - forScopes:           Permissions you want included in the access token received
         in the result in the completionBlock. Not all scopes are
         guaranteed to be included in the access token returned.
         - account:             An account object that we retrieved from the application object before that the
         authentication flow will be locked down to.
         - completionBlock:     The completion block that will be called when the authentication
         flow completes, or encounters an error.
         */

        let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)

        applicationContext.acquireTokenSilent(with: parameters) { (result, error) in

            if let error = error {

                let nsError = error as NSError

                // interactionRequired means we need to ask the user to sign-in. This usually happens
                // when the user's Refresh Token is expired or if the user has changed their password
                // among other possible reasons.

                if (nsError.domain == MSALErrorDomain) {

                    if (nsError.code == MSALError.interactionRequired.rawValue) {

                        DispatchQueue.main.async {
                            self.acquireTokenInteractively()
                        }
                        return
                    }
                }

                self.updateLogging(text: "Could not acquire token silently: \(error)")
                return
            }

            guard let result = result else {

                self.updateLogging(text: "Could not acquire token: No result returned")
                return
            }

            self.accessToken = result.accessToken
            self.updateLogging(text: "Refreshed Access token is \(self.accessToken)")
            self.updateSignOutButton(enabled: true)
            self.getContentWithToken()
        }
    }
```

### <a name="call-the-microsoft-graph-api"></a>A Microsoft Graph API meghívása

Ha rendelkezik jogkivonattal, az alkalmazás a HTTP-fejlécben felhasználhatja, hogy jogosult kérést készítsen a Microsoft Graph:

| fejléc kulcsa    | érték                 |
| ------------- | --------------------- |
| Engedélyezés | Tulajdonosi \<access-token> |

Adja hozzá a következő kódot a `ViewController` osztályhoz:

```swift
    func getContentWithToken() {

        // Specify the Graph API endpoint
        let graphURI = getGraphEndpoint()
        let url = URL(string: graphURI)
        var request = URLRequest(url: url!)

        // Set the Authorization header for the request. We use Bearer tokens, so we specify Bearer + the token we got from the result
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in

            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }

            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {

                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            self.updateLogging(text: "Result from Graph: \(result))")

            }.resume()
    }
```

A Microsoft Graph API-val kapcsolatos további tudnivalókért tekintse meg [Microsoft Graph API](https://graph.microsoft.com) -t.

### <a name="use-msal-for-sign-out"></a>MSAL használata a kijelentkezéshez

Ezután vegyen fel támogatást a kijelentkezéshez.

> [!Important]
> A kijelentkezés a MSAL eltávolítja az alkalmazásból származó összes ismert információt, valamint az eszköz konfigurációja által engedélyezett aktív munkamenetet az eszközön. Kiválaszthatja a felhasználót is a böngészőből.

A kijelentkezési képesség hozzáadásához adja hozzá a következő kódot az `ViewController` osztályban belül.

```swift
@objc func signOut(_ sender: AnyObject) {

        guard let applicationContext = self.applicationContext else { return }

        guard let account = self.currentAccount else { return }

        do {

            /**
             Removes all tokens from the cache for this application for the provided account

             - account:    The account to remove from the cache
             */

            let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParameters!)
            signoutParameters.signoutFromBrowser = false // set this to true if you also want to signout from browser or webview

            applicationContext.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

                if let error = error {
                    self.updateLogging(text: "Couldn't sign out account with error: \(error)")
                    return
                }

                self.updateLogging(text: "Sign out completed successfully")
                self.accessToken = ""
                self.updateCurrentAccount(account: nil)
            })

        }
    }
```

### <a name="enable-token-caching"></a>Jogkivonat-gyorsítótárazás engedélyezése

Alapértelmezés szerint a MSAL az iOS-vagy macOS-kulcstartóban gyorsítótárazza az alkalmazás jogkivonatait.

A jogkivonat-gyorsítótárazás engedélyezése:

1. Győződjön meg arról, hogy az alkalmazás megfelelően van aláírva
1. Lépjen a Xcode-projekt beállításai > **képességek lapon** a  >  **kulcstartó megosztásának engedélyezése** lehetőségre.
1. Válassza ki **+** és adja meg a következő **kulcstartó-csoportok** egyikét:
    - iOS `com.microsoft.adalcache`
    - MacOS `com.microsoft.identity.universalstorage`

### <a name="add-helper-methods"></a>Segítő metódusok hozzáadása
A minta végrehajtásához adja hozzá a következő segítő metódusokat a `ViewController` osztályhoz.

### <a name="ios-ui"></a>iOS felhasználói felület:

``` swift

    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.text = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.text = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

    func updateAccountLabel() {

        guard let currentAccount = self.currentAccount else {
            self.usernameLabel.text = "Signed out"
            return
        }

        self.usernameLabel.text = currentAccount.username
    }

    func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="macos-ui"></a>macOS felhasználói felület:

```swift
    func updateLogging(text : String) {

        if Thread.isMainThread {
            self.loggingText.string = text
        } else {
            DispatchQueue.main.async {
                self.loggingText.string = text
            }
        }
    }

    func updateSignOutButton(enabled : Bool) {
        if Thread.isMainThread {
            self.signOutButton.isEnabled = enabled
        } else {
            DispatchQueue.main.async {
                self.signOutButton.isEnabled = enabled
            }
        }
    }

     func updateAccountLabel() {

         guard let currentAccount = self.currentAccount else {
            self.usernameLabel.stringValue = "Signed out"
            return
        }

        self.usernameLabel.stringValue = currentAccount.username ?? ""
        self.usernameLabel.sizeToFit()
     }

     func updateCurrentAccount(account: MSALAccount?) {
        self.currentAccount = account
        self.updateAccountLabel()
        self.updateSignOutButton(enabled: account != nil)
    }
```

### <a name="for-ios-only-get-additional-device-information"></a>Csak iOS esetén szerezze be az eszköz további adatait

A következő kód használatával olvassa be az eszköz aktuális konfigurációját, beleértve azt is, hogy az eszköz megosztottként van-e konfigurálva:

```swift
    @objc func getDeviceMode(_ sender: AnyObject) {

        if #available(iOS 13.0, *) {
            self.applicationContext?.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

                guard let deviceInfo = deviceInformation else {
                    self.updateLogging(text: "Device info not returned. Error: \(String(describing: error))")
                    return
                }

                let isSharedDevice = deviceInfo.deviceMode == .shared
                let modeString = isSharedDevice ? "shared" : "private"
                self.updateLogging(text: "Received device info. Device is in the \(modeString) mode.")
            })
        } else {
            self.updateLogging(text: "Running on older iOS. GetDeviceInformation API is unavailable.")
        }
    }
```

### <a name="multi-account-applications"></a>Több fiókból álló alkalmazások

Ez az alkalmazás egyetlen fiókra épül. A MSAL támogatja a többfiókos forgatókönyveket is, de az alkalmazások további munkája szükséges. Létre kell hoznia egy felhasználói felületet, amely segítségével a felhasználók kiválaszthatják, hogy melyik fiókot szeretnék használni a tokeneket igénylő műveletekhez. Azt is megteheti, hogy az alkalmazás a MSAL összes fiókjának lekérdezésével kijelöli a használni kívánt fiókot. Példa: `accountsFromDeviceForParameters:completionBlock:` [API](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplication.html#/c:objc(cs)MSALPublicClientApplication(im)accountsFromDeviceForParameters:completionBlock:)

## <a name="test-your-app"></a>Az alkalmazás tesztelése

Az alkalmazás létrehozása és üzembe helyezése tesztelési eszközön vagy szimulátoron. A bejelentkezéshez és az Azure AD-vagy személyes Microsoft-fiókokhoz tartozó jogkivonatok beszerzéséhez be kell tudnia jelentkezni.

Amikor a felhasználó először jelentkezik be az alkalmazásba, a Microsoft Identity a kért engedélyekkel való beleegyező jogosultságot kér. Míg a legtöbb felhasználó képes hozzájárulni, néhány Azure AD-bérlő letiltotta a felhasználói beleegyezését, amely megköveteli, hogy a rendszergazdák az összes felhasználó nevében hozzájárulásukat adjanak. A forgatókönyv támogatásához regisztrálja az alkalmazás hatóköreit a Azure Portalban.

A bejelentkezést követően az alkalmazás megjeleníti az Microsoft Graph végpont által visszaadott adatok megjelenítését `/me` .

## <a name="next-steps"></a>Következő lépések

További információ a védett webes API-kat meghívó mobil alkalmazások létrehozásáról a többrészes forgatókönyvek sorozatában.

> [!div class="nextstepaction"]
> [Forgatókönyv: webes API-kat meghívó mobil alkalmazás](scenario-mobile-overview.md)
