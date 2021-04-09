---
title: Rövid útmutató – az Azure kommunikációs szolgáltatásokkal rendelkező csapatoknak egy iOS-alkalmazáshoz való csatlakozásának hozzáadása
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure kommunikációs szolgáltatások csapatait az iOS-hez készült beágyazási kódtár használatával.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4d28864d41d6540afc87126daf589ed2929f891d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104804009"
---
Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat egy Teams-találkozóhoz az Azure kommunikációs szolgáltatások csapatának az iOS-hez készült beágyazási kódtár használatával.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következő előfeltételekre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Egy [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)-t futtató Mac, valamint egy érvényes, a kulcstartóba telepített fejlesztői tanúsítvány.
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- Az Azure kommunikációs szolgáltatás [felhasználói hozzáférési jogkivonata](../../access-tokens.md) .
- A Build-környezethez telepített [CocoaPods](https://cocoapods.org/) .

## <a name="setting-up"></a>Beállítás

### <a name="creating-the-xcode-project"></a>A Xcode projekt létrehozása

A Xcode-ben hozzon létre egy új iOS-projektet, és válassza ki az **alkalmazás** sablonját. A UIKit Storyboards-ket fogjuk használni. Ebben a rövid útmutatóban nem fog teszteket létrehozni. Nyugodtan szüntesse meg a **tesztek belefoglalását**.

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Képernyőfelvétel: az új projekt sablon kiválasztása a Xcode belül.":::

Adja meg a projekt nevét `TeamsEmbedGettingStarted` .

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Képernyőfelvétel: az új projekt részletei a Xcode belül.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>A csomag és a függőségek telepítése a CocoaPods

1. Hozzon létre egy Cocoapods az alkalmazáshoz:

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunication', '~> 1.0.0-beta.8'
end

azure_libs = [
'AzureCommunication',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. Futtassa az `pod install` parancsot.
3. Nyissa meg a generált a `.xcworkspace` Xcode.

### <a name="request-access-to-the-microphone-camera-etc"></a>Hozzáférés kérése a mikrofonhoz, a kamerához stb.

Az eszköz hardverének eléréséhez frissítse az alkalmazás információ tulajdonságai listáját. Állítsa be a társított értéket egy értékre, `string` amely szerepelni fog a párbeszédpanelen, amelyet a rendszer a felhasználótól való hozzáférés kéréséhez használ.

Kattintson a jobb gombbal a `Info.plist` projekt fájának bejegyzésére, és válassza a **Megnyitás**  >  **forrásként kód** lehetőséget. Adja hozzá a következő sorokat a legfelső szintű `<dict>` szakaszhoz, majd mentse a fájlt.

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string></string>
<key>NSBluetoothPeripheralUsageDescription</key>
<string></string>
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>A Teams beágyazási keretrendszer hozzáadása

1. Töltse le a keretrendszert.
2. Hozzon létre egy `Frameworks` mappát a projekt gyökerében. Pl. `\TeamsEmbedGettingStarted\Frameworks\`
3. Másolja a letöltött `TeamsAppSDK.framework` és `MeetingUIClient.framework` keretrendszerek erre a mappába.
4. Adja hozzá a `TeamsAppSDK.framework` és a `MeetingUIClient.framework` projektet a projekthez az Általános lapon. a segítségével `Add Other`  ->  `Add Files...` navigáljon a keretrendszer fájljaihoz, és adja hozzá őket.

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="A Xcode-ben hozzáadott keretrendszereket bemutató képernyőkép.":::

5. Ha még nem tette meg, vegye fel a `$(PROJECT_DIR)/Frameworks` t `Framework Search Paths` a projekt célja Build-beállítások lapra. A beállítás megkereséséhez módosítsa a szűrőt a következőre `basic` : `all` , a jobb oldalon található keresősáv is használható.

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Képernyőfelvétel: a keretrendszer keresési útvonala a Xcode.":::

### <a name="turn-off-bitcode"></a>Bitcode kikapcsolása

Állítsa be `Enable Bitcode` a beállítást a `No` projekt létrehozási beállításai között. A beállítás megkereséséhez módosítsa a szűrőt a következőre `basic` : `all` , a jobb oldalon található keresősáv is használható.

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="A Xcode BitCode beállítását bemutató képernyőkép.":::

### <a name="add-framework-signing-script"></a>Keretrendszer-aláírási parancsfájl hozzáadása

Válassza ki az alkalmazás célját, és válassza a `Build Phases` fület.  Ezután kattintson a `+` , majd a elemre `New Run Script Phase` . Győződjön meg arról, hogy ez az új fázis a fázisok után következik be `Embed Frameworks` .



:::image type="content" source="../media/ios/xcode-build-script.png" alt-text="Képernyőfelvétel: a Build szkript hozzáadása a Xcode-ben.":::

```bash
#!/bin/sh
if [ -d "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks ]; then
    pushd "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    for EACH in *.framework; do
        echo "-- signing ${EACH}"
        /usr/bin/codesign --force --deep --sign "${EXPANDED_CODE_SIGN_IDENTITY}" --entitlements "${TARGET_TEMP_DIR}/${PRODUCT_NAME}.app.xcent" --timestamp=none $EACH
        echo "-- moving ${EACH}"
        mv -nv ${EACH} ../../
    done
    rm -rf "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    popd
    echo "BUILD DIR ${TARGET_BUILD_DIR}"
fi
```

### <a name="turn-on-voice-over-ip-background-mode"></a>A hang bekapcsolása IP-háttér módban.

Válassza ki az alkalmazás célját, és kattintson a képességek fülre.

Kapcsolja be a felső részre `Background Modes` kattintva `+ Capabilities` , majd válassza a lehetőséget `Background Modes` .

Jelölje be a jelölőnégyzetet a következőhöz: `Voice over IP` .

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Képernyőfelvétel: engedélyezett VOIP a Xcode-ben.":::

### <a name="add-a-window-reference-to-appdelegate"></a>Ablak hivatkozásának hozzáadása a AppDelegate

Nyissa meg a projekt **AppDelegate. Swift** fájlját, és adjon hozzá egy hivatkozást a "Window" elemhez.

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>Gomb hozzáadása a ViewController

Hozzon létre egy gombot a `viewDidLoad` visszahívásban a **ViewController. Swift**-ben.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

Hozzon létre egy kimenetet a gombhoz a **ViewController. Swift**-ben.

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Nyissa meg a projekt **ViewController. Swift** fájlját, és adjon hozzá egy `import` deklarációt a fájl elejéhez a `AzureCommunication library` és a importálásához `MeetingUIClient` . 

```swift
import UIKit
import AzureCommunication
import MeetingUIClient
```

Cserélje le az osztály implementációját `ViewController` egy egyszerű gombbal, hogy a felhasználó csatlakozzon egy értekezlethez. Ebben a rövid útmutatóban egy üzleti logikát fogunk csatolni a gombhoz.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az Azure kommunikációs szolgáltatások csapatának beágyazási könyvtárának főbb funkcióit kezelik:

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | A MeetingUIClient a csapat beágyazási könyvtárának fő belépési pontja. |
| MeetingUIClientDelegate | A MeetingUIClientDelegate az események fogadására, például a hívás állapotának változásaira szolgál. |
| MeetingJoinOptions | A MeetingJoinOptions konfigurálható beállítások, például a megjelenítendő név használatával használhatók. | 
| CallState | A CallState a hívási állapot változásainak jelentésére szolgál. A lehetőségek a következők: csatlakozás, waitingInLobby, csatlakoztatott és véges. |

## <a name="create-and-authenticate-the-client"></a>Az ügyfél létrehozása és hitelesítése

Egy példány inicializálása `MeetingUIClient` egy felhasználói hozzáférési jogkivonattal, amely lehetővé teszi, hogy csatlakozzanak az értekezletekhez. Adja hozzá az alábbi kódot a `viewDidLoad` visszahíváshoz a **ViewController. Swift**:

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(with: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

Cserélje le `<USER_ACCESS_TOKEN>` az elemet az erőforrás érvényes felhasználói hozzáférési jogkivonatával. Ha még nem áll rendelkezésre jogkivonat, tekintse meg a [felhasználói hozzáférési jogkivonat](../../access-tokens.md) dokumentációját.

### <a name="setup-token-refreshing"></a>Beállítási jogkivonat frissítése

Hozzon létre egy `fetchTokenAsync` metódust. Ezután adja hozzá a logikai azonosítót a `fetchToken` felhasználói jogkivonat beszerzéséhez.

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshOnCompletion) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

Cserélje le `<USER_ACCESS_TOKEN>` az elemet az erőforrás érvényes felhasználói hozzáférési jogkivonatával.

## <a name="join-a-meeting"></a>Csatlakozás értekezlethez

A `joinMeeting` metódus beállítása a *JOIN Meeting* gomb kiválasztásakor végrehajtandó művelet. Frissítse a megvalósítást, hogy csatlakozzon egy értekezlethez a következővel `MeetingUIClient` :

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")
        
    meetingUIClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Cserélje le a- `<MEETING URL>` t egy Teams Meeting hivatkozással.

### <a name="get-a-teams-meeting-link"></a>Team Meeting-hivatkozás beszerzése

A Teams Meeting-hivatkozás a Graph API-k használatával kérhető le. Ez részletesen szerepel a [Graph dokumentációjában](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
A kommunikációs szolgáltatások meghívója SDK egy teljes csapatot tárgyaló hivatkozást fogad el. Ez a hivatkozás az erőforrás részeként `onlineMeeting` érhető el, a [ `joinWebUrl` tulajdonság](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) alatt elérhető módon, a Teams Meeting Meeting (összevont **értekezlet** ) URL-címéhez tartozó szükséges értekezlet-információkat is lekérheti.

## <a name="run-the-code"></a>A kód futtatása

Az alkalmazást az iOS-szimulátorban a **termék**  >  **futtatása** vagy a (&#8984;-R) billentyűparancs használatával hozhatja létre és futtathatja.

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="A gyors üzembe helyezési alkalmazás végső megjelenésének és működésének első lépései":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="Végső megjelenés és működés a találkozó csatlakoztatása után":::

> [!NOTE]
> Amikor első alkalommal csatlakozik egy értekezlethez, a rendszer kérni fogja a mikrofon elérését. Éles alkalmazásokban az API-val kell `AVAudioSession` [ellenőriznie az engedélyek állapotát](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) , és megfelelően frissíteni az alkalmazás viselkedését, ha az engedély nem lett megadva.

## <a name="add-localization-support-based-on-your-app"></a>Honosítási támogatás hozzáadása az alkalmazás alapján

A Microsoft Teams SDK több mint 100 karakterláncot és erőforrást támogat. A Framework csomag alapszintű és angol nyelvű nyelveket tartalmaz. A többi részét a csomagban található fájl tartalmazza `Localizations.zip` .

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>Honosítások hozzáadása az SDK-hoz az alkalmazás által támogatottak alapján

1. Határozza meg, hogy az alkalmazás milyen típusú honosításokat támogat az Xcode projekt > info > honosítási lista
2. A csomaghoz tartozó Localizations.zip kicsomagolása
3. Másolja ki a honosítási mappákat a kibontott mappából az alkalmazás által a TeamsAppSDK gyökeréhez támogatott alkalmazások alapján.

## <a name="preparation-for-app-store-upload"></a>Az App Store-feltöltés előkészítése

Ha archiválásra van lehetőség, távolítsa el az i386 és x86_64 architektúrákat a keretrendszerekből.

Ha szeretné archiválni az alkalmazást, adja hozzá a ( `i386` `x86_64` z) és az architektúrát, és távolítsa el a szkripteket a Build fázisokhoz.

A projekt-Navigátorban válassza ki a projektet. A szerkesztő ablaktáblán válassza a létrehozás fázisok → kattintson a + Sign → új futtatási parancsfájl létrehozása fázis elemre.

```bash
echo "Target architectures: $ARCHS"
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
FRAMEWORK_TMP_PATH="$FRAMEWORK_EXECUTABLE_PATH-tmp"
# remove simulator's archs if location is not simulator's directory
case "${TARGET_BUILD_DIR}" in
*"iphonesimulator")
    echo "No need to remove archs"
    ;;
*)
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "i386") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "i386" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "i386 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "x86_64") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "x86_64" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "x86_64 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    ;;
esac
echo "Completed for executable $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
done
```

## <a name="sample-code"></a>Példakód

A minta alkalmazást letöltheti a [githubról](https://github.com/Azure-Samples/teams-embed-ios-getting-started)
