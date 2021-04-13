---
title: Rövid útmutató – Hívás hozzáadása iOS-alkalmazáshoz a Azure Communication Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az iOS-Azure Communication Services hívó SDK-t.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e1eed3f9449843e6c2dd8c77719402e709fdeb23
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327173"
---
Ebből a rövid útmutatóból megtudhatja, hogyan indítható hívás az iOS-Azure Communication Services hívó SDK-val.

[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

> [!NOTE]
> Ez a dokumentum a hívó SDK 1.0.0-beta.9-es verzióját használja.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következő előfeltételekre lesz szüksége:

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- Egy [Xcode-et](https://go.microsoft.com/fwLink/p/?LinkID=266532)futtató Mac gép, valamint egy érvényes fejlesztői tanúsítvány, amely a kulcsláncba van telepítve.
- Egy üzembe helyezett Communication Services erőforrás. [Hozzon létre egy Communication Services erőforrást.](../../create-communication-resource.md)
- Egy [felhasználói hozzáférési jogkivonat](../../access-tokens.md) az Azure Communication Service-hez.

## <a name="setting-up"></a>Beállítása

### <a name="creating-the-xcode-project"></a>Az Xcode-projekt létrehozása

Az Xcode-ban hozzon létre egy új iOS-projektet, és válassza az **Egynézetes alkalmazás sablont.** Ez az oktatóanyag a [SwiftUI keretrendszert használja,](https://developer.apple.com/xcode/swiftui/)  ezért a **Nyelvet** **Swiftre,** a Felhasználói felület **SwiftUI-t kell beállítania.** Ebben a rövid útmutatóban nem fog teszteket létrehozni. Nyugodtan törölje az **Include Tests (Tesztek is) jelölőnégyzet jelölését.**

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Képernyőkép az Új projekt ablakról az Xcode-kódban.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>A csomag és a függőségek telepítése a CocoaPods alkalmazással

1. Ha podfájlt szeretne létrehozni az alkalmazáshoz, nyissa meg a terminált, navigáljon a projektmappához, és futtassa a következőt: ```pod init```
3. Adja hozzá a következő kódot a Podfile-hoz, és mentse (győződjön meg arról, hogy a "target" megegyezik a projekt nevével):

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.9'
     pod 'AzureCommunication', '~> 1.0.0-beta.9'
     pod 'AzureCore', '~> 1.0.0-beta.9'
   end
   ```

3. Futtassa az `pod install` parancsot.
3. Nyissa meg a `.xcworkspace` kódot az Xcode-kóddal.

### <a name="request-access-to-the-microphone"></a>Hozzáférés kérése a mikrofonhoz

Az eszköz mikrofonja eléréséhez frissítenie kell az alkalmazás Information Property (Információtulajdonság) listáját egy `NSMicrophoneUsageDescription` használatával. A társított értéket egy értékre kell állítania, amely szerepelni fog a párbeszédpanelen, amikor a rendszer hozzáférést `string` kér a felhasználótól.

Kattintson a jobb gombbal `Info.plist` a projektfa bejegyzésére, és válassza a **Megnyitás**  >  **forráskódként lehetőséget.** Adja hozzá a következő sorokat a legfelső szintű `<dict>` szakaszhoz, majd mentse a fájlt.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Nyissa meg a projekt **ContentView.swift** fájlját, és adjon hozzá egy deklarációt a fájl tetejéhez `import` a `AzureCommunicationCalling library` importáláshoz. Emellett importálja a et, mert szüksége lesz rá a `AVFoundation` kódban a hangengedély-kéréshez.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Cserélje le a strukturáció implementációját néhány egyszerű felhasználói felületi vezérlőre, amelyek lehetővé teszik a felhasználó számára a hívás kezdeményezését `ContentView` és végét. Ebben a rövid útmutatóban üzleti logikát csatolunk ezekhez a vezérlőkhöz.

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

A következő osztályok és felületek kezelik a Azure Communication Services SDK néhány fő funkcióját:

| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | A CallClient a hívó SDK fő belépési pontja.|
| CallAgent | A CallAgent hívásokat indít el és kezel. |
| CommunicationTokenCredential | A CallAgent példányának példánya a CommunicationTokenCredential jogkivonat hitelesítő adata.| 
| CommunicationUserIdentifier | A CommunicationUserIdentifier a felhasználó identitását képviseli, amely a következők egyike lehet: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Inicializáljon egy `CallAgent` példányt egy felhasználói hozzáférési jogkivonattal, amellyel hívásokat kezdeményezünk és fogadhatunk. Adja hozzá a következő kódot `onAppear` a visszahíváshoz a **ContentView.swiftben:**

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

A helyére `<USER ACCESS TOKEN>` be kell helyettesítenie egy érvényes felhasználói hozzáférési jogkivonatot az erőforráshoz. Ha még [nem](../../access-tokens.md) rendelkezik elérhető jogkivonattal, tekintse meg a felhasználói hozzáférési jogkivonat dokumentációját.

## <a name="start-a-call"></a>Hívás elindítani

A metódus lesz beállítva az a művelet, amely akkor lesz végreállítva, amikor a Start Call (Hívás elindítani) `startCall` gombra koppint.  Frissítse az implementációt, hogy elindítsa a hívást a `ASACallAgent` következővel:

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(identifier: self.callee)]
            self.call = self.callAgent?.startCall(participants: callees, options: StartCallOptions())
        }
    }
}
```

A tulajdonságával a hívás kezdeti beállításait is beállíthatja (azaz lehetővé teszi a hívás elnémítását a `StartCallOptions` mikrofonnal).

## <a name="end-a-call"></a>Hívás vége

Implementálja `endCall` a metódust az aktuális hívás végét, amikor az *End Call (Hívás* vége) gombra koppint.

```swift
func endCall()
{    
    self.call!.hangUp(HangUpOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>A kód futtatása

Az alkalmazást az iOS-szimulátoron a Product Run (Termékfuttassa) vagy a (&#8984;-R) billentyűparancs használatával  >   is felépítheti és futtathatja.

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="A gyorsindító alkalmazás végső megjelenésének és megjelenésének módja":::

Kimenő VOIP-hívást úgy is elindíthat, hogy a szövegmezőben meg kell adnunk egy felhasználói azonosítót, és a Start Call (Hívás elindítani) **gombra koppintunk.** A hívása egy echo robothoz csatlakoztatja. Ez nagyszerű az első lépésekhez és annak ellenőrzéséhez, hogy `8:echo123` a hangeszközök működnek-e. 

> [!NOTE]
> Az első híváskor a rendszer kérni fogja a mikrofonhoz való hozzáférést. Éles alkalmazásokban az API-val ellenőrizze az engedélyek állapotát, és az engedély meg nem adott állapotának megszakorlott állapotának megfelelő `AVAudioSession` frissítésével frissítse az alkalmazás viselkedését. [](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)

## <a name="sample-code"></a>Példakód

A mintaalkalmazást letöltheti a [GitHubról](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling)
