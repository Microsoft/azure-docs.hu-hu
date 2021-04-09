---
title: Rövid útmutató – hívás hozzáadása iOS-alkalmazáshoz az Azure Communication Services használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az iOS rendszerhez készült Azure kommunikációs szolgáltatásokat.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 536b9a9a0d1a7b48841938eef44d181d22b87bf4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609518"
---
Ebből a rövid útmutatóból megtudhatja, hogyan indíthat el egy hívást az iOS rendszerhez készült Azure Communication Services SDK-val.

> [!NOTE]
> Ez a dokumentum a hívó SDK-ból származó 1.0.0-Beta. 8 verziót használja.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő előfeltételekre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Egy [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)-t futtató Mac, valamint egy érvényes, a kulcstartóba telepített fejlesztői tanúsítvány.
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- Az Azure kommunikációs szolgáltatás [felhasználói hozzáférési jogkivonata](../../access-tokens.md) .

## <a name="setting-up"></a>Beállítás

### <a name="creating-the-xcode-project"></a>A Xcode projekt létrehozása

A Xcode-ben hozzon létre egy új iOS-projektet, és válassza ki az **Egynézetes alkalmazás** sablonját. Ez az oktatóanyag a [SwiftUI keretrendszert](https://developer.apple.com/xcode/swiftui/)használja, ezért a **nyelvet** a **Swift** értékre kell beállítani, és a **felhasználói felületet** kell **SwiftUI**. Ebben a rövid útmutatóban nem fog teszteket létrehozni. Nyugodtan szüntesse meg a **tesztek belefoglalását**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Képernyőfelvétel: az új projekt ablak a Xcode belül.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>A csomag és a függőségek telepítése a CocoaPods

1. Az alkalmazás Cocoapods létrehozásához nyissa meg a terminált, és navigáljon a projekt mappájához, és futtassa a parancsot. ```pod init```
3. Adja hozzá a következő kódot a Cocoapods, és mentse őket (Ügyeljen arra, hogy a "Target" a projekt nevével egyezik):

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

3. Futtassa az `pod install` parancsot.
3. Nyissa meg a `.xcworkspace` with Xcode.

### <a name="request-access-to-the-microphone"></a>Hozzáférés kérése a mikrofonhoz

Az eszköz mikrofonjának eléréséhez frissítenie kell az alkalmazás információs tulajdonságainak listáját `NSMicrophoneUsageDescription` . A társított értéket adja meg, `string` amely szerepelni fog a párbeszédpanelen, amelyet a rendszer a felhasználótól való hozzáférés kéréséhez használ.

Kattintson a jobb gombbal a `Info.plist` projekt fájának bejegyzésére, és válassza a **Megnyitás**  >  **forrásként kód** lehetőséget. Adja hozzá a következő sorokat a legfelső szintű `<dict>` szakaszhoz, majd mentse a fájlt.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Nyissa meg a projekt **ContentView. Swift** fájlját, és adjon hozzá egy `import` deklarációt a fájl elejéhez az importáláshoz `AzureCommunicationCalling library` . Emellett az importáláshoz `AVFoundation` szükség van a kódban a hangengedélyre vonatkozó kérelemre.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Cserélje le a struct megvalósítását `ContentView` néhány olyan egyszerű felhasználói felületi vezérlőre, amely lehetővé teszi a felhasználók számára a hívások kezdeményezését és befejezését. Ebben a rövid útmutatóban üzleti logikát fogunk csatolni ezen vezérlőkhöz.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az Azure kommunikációs szolgáltatások Meghívási SDK-val kapcsolatos főbb funkcióit kezelik:

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | A CallClient a hívó SDK fő belépési pontja.|
| CallAgent | A CallAgent a hívások indításához és kezeléséhez használatos. |
| CommunicationTokenCredential | A rendszer a CommunicationTokenCredential használja jogkivonat-hitelesítő adatként a CallAgent létrehozásához.| 
| CommunicationUserIdentifier | A CommunicationUserIdentifier a felhasználó identitásának jelölésére szolgál, amely a következők egyike lehet: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy olyan példány inicializálása `CallAgent` egy felhasználói hozzáférési jogkivonattal, amely lehetővé teszi a hívások kezdeményezését és fogadását. Adja hozzá az alábbi kódot a `onAppear` visszahíváshoz a **ContentView. Swift**:

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Az `<USER ACCESS TOKEN>` erőforráshoz érvényes felhasználói hozzáférési tokent kell cserélnie. Ha még nem áll rendelkezésre jogkivonat, tekintse meg a [felhasználói hozzáférési jogkivonat](../../access-tokens.md) dokumentációját.

## <a name="start-a-call"></a>Hívás indítása

A `startCall` metódus beállítása a *hívás indítása* gomb kiválasztásakor végrehajtandó művelet. Frissítse a megvalósítást a hívás indításához a következővel `ASACallAgent` :

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(identifier: self.callee)]
            self.call = self.callAgent?.call(participants: callees, options: StartCallOptions())
        }
    }
}
```

A-ben a tulajdonságok segítségével `StartCallOptions` megadhatja a hívás kezdeti beállításait (azaz lehetővé teszi a hívás indítását a mikrofon némításával).

## <a name="end-a-call"></a>Hívás befejezése

Az `endCall` aktuális hívás befejezésére szolgáló metódus implementálása a *befejezési hívás* gombra koppintva.

```swift
func endCall()
{    
    self.call!.hangup(HangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>A kód futtatása

Az alkalmazást az iOS-szimulátorban a **termék**  >  **futtatása** vagy a (&#8984;-R) billentyűparancs használatával hozhatja létre és futtathatja.

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="A gyors üzembe helyezési alkalmazás végső megjelenésének és működésének első lépései":::

A kimenő VOIP-hívást úgy teheti meg, hogy egy felhasználói azonosítót biztosít a szövegmezőben, és a **hívás indítása** gombra koppint. A Calling `8:echo123` egy echo-robottal csatlakozik, ez nagyszerű megoldás az első lépésekhez és a hangeszközök ellenőrzéséhez. 

> [!NOTE]
> Amikor először végez hívást, a rendszer kérni fogja a mikrofon elérését. Éles alkalmazásokban az API-val kell `AVAudioSession` [ellenőriznie az engedélyek állapotát](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) , és megfelelően frissíteni az alkalmazás viselkedését, ha az engedély nem lett megadva.

## <a name="sample-code"></a>Példakód

A minta alkalmazást letöltheti a [githubról](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling)
