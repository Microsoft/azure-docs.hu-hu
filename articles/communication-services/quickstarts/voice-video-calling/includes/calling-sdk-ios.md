---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 479aa522462d14f295177e6b2d2fcc4707657760
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106498837"
---
[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Egy üzembe helyezett Azure kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- Felhasználói hozzáférési jogkivonat a hívási ügyfél engedélyezéséhez. [Felhasználói hozzáférési jogkivonat beszerzése](../../access-tokens.md).
- Nem kötelező: fejezze be a [hanghívás hozzáadása az alkalmazáshoz](../getting-started-with-calling.md) című rövid útmutatót.

## <a name="set-up-your-system"></a>A System beállítása

### <a name="create-the-xcode-project"></a>A Xcode projekt létrehozása

A Xcode-ben hozzon létre egy új iOS-projektet, és válassza ki az **Egynézetes alkalmazás** sablonját. Ez a rövid útmutató a [SwiftUI keretrendszert](https://developer.apple.com/xcode/swiftui/)használja, ezért a **nyelvet** a **Swift** és a **felhasználói felület** **SwiftUI** kell beállítania. 

Ebben a rövid útmutatóban nem hozhat létre egység-vagy felhasználói felületi teszteket. Nyugodtan törölheti a **belefoglalási egység teszteket** , és a **felhasználói felületi tesztek** szövegmezőket is megadhatja.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Képernyőkép, amely megjeleníti a Xcode belüli projekt létrehozásának ablakát.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>A csomag és a függőségek telepítése a CocoaPods

1. Hozzon létre egy Cocoapods az alkalmazáshoz, például a következőhöz:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. Futtassa az `pod install` parancsot.
3. Nyissa meg `.xcworkspace` a Xcode.

### <a name="request-access-to-the-microphone"></a>Hozzáférés kérése a mikrofonhoz

Az eszköz mikrofonjának eléréséhez frissítenie kell az alkalmazás információ-tulajdonságainak listáját a következővel: `NSMicrophoneUsageDescription` . A társított értéket adja meg, `string` amely szerepelni fog a párbeszédpanelen, amelyet a rendszer a felhasználótól való hozzáférés kéréséhez használ.

Kattintson a jobb gombbal a `Info.plist` projekt fájának bejegyzésére, és válassza a **Megnyitás**  >  **forrásként kód** lehetőséget. Adja hozzá a következő sorokat a legfelső szintű `<dict>` szakaszban, majd mentse a fájlt.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Nyissa meg a projekt *ContentView. Swift* fájlját, és adjon hozzá egy `import` deklarációt a fájl elejéhez a könyvtár importálásához `AzureCommunicationCalling` . Emellett importálja az importálást is `AVFoundation` . Szüksége lesz rá a kódban szereplő hangengedély-kérelmekre.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="learn-the-object-model"></a>A objektummodell megismerése

A következő osztályok és felületek az Azure kommunikációs szolgáltatások az iOS-hez készült SDK-val kapcsolatos főbb funkcióit kezelik.

> [!NOTE]
> Ez a rövid útmutató a Calling SDK-ból származó 1.0.0-Beta. 8 verziót használja.


| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `CallClient` | `CallClient` a hívó SDK fő belépési pontja.|
| `CallAgent` | `CallAgent` a hívások indításához és kezeléséhez használatos. |
| `CommunicationTokenCredential` | `CommunicationTokenCredential` a rendszer jogkivonat-hitelesítő adatként használja a példány létrehozásához `CallAgent` .| 
| `CommunicationIdentifier` | `CommunicationIdentifier` a felhasználó identitásának ábrázolására szolgál. Az identitás lehet `CommunicationUserIdentifier` , `PhoneNumberIdentifier` , vagy `CallingApplication` . |

> [!NOTE]
> Amikor az alkalmazás végrehajtja az esemény-delegált, erős hivatkozást kell tartania az esemény-előfizetéseket igénylő objektumokra. Ha például egy `RemoteParticipant` objektumot ad vissza a metódus meghívásakor, `call.addParticipant` és az alkalmazás beállítja a delegált figyelését `RemoteParticipantDelegate` , az alkalmazásnak erős hivatkozást kell tartalmaznia az `RemoteParticipant` objektumra. Ellenkező esetben, ha az objektum gyűjtése bekerül, a delegált végzetes kivételt fog kiváltani, ha a hívó SDK megpróbálja meghívni az objektumot.

## <a name="initialize-callagent"></a>CallAgent inicializálása

Egy példánynak a alkalmazásból való létrehozásához `CallAgent` `CallClient` olyan metódust kell használnia, `callClient.createCallAgent` amely aszinkron módon visszaadja az `CallAgent` objektumot az inicializálás után.

Hívási ügyfél létrehozásához át kell adnia egy `CommunicationTokenCredential` objektumot.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
var userCredential: CommunicationTokenCredential?
   do {
       userCredential = try CommunicationTokenCredential(with: CommunicationTokenRefreshOptions(initialToken: token, 
                                                                     refreshProactively: true,
                                                                     tokenRefresher: self.fetchTokenSync))
   } catch {
       return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Adja meg a `CommunicationTokenCredential` létrehozott objektumot `CallClient` , és állítsa be a megjelenítendő nevet.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()!
options.displayName = " iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Kimenő hívás elhelyezése

Hívás létrehozásához és elindításához meg kell hívnia az egyik API-t, és meg kell `CallAgent` adnia a kommunikációs szolgáltatások identitását, amelyet a Communication Services Management SDK használatával kiépített.

A hívás létrehozása és a kezdés szinkronban van. Egy hívási példányt fog kapni, amely lehetővé teszi a hívás összes eseményére való előfizetést.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Helyezzen 1:1-hívást egy felhasználónak vagy 1: n hívásnak a felhasználók és a PSTN használatával

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>1: n hívás elhelyezése a felhasználók és a PSTN között
Ha a hívást PSTN-re szeretné helyezni, meg kell adnia a kommunikációs szolgáltatásokkal megszerzett telefonszámot.

```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-video"></a>1:1-hívás elhelyezése videóval
A Device Manager-példányok beszerzéséhez tekintse meg az [eszközök kezelése](#manage-devices)című szakaszt.

```swift

let camera = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUserIdentifier(identifier: 'UserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Csatlakozás csoportos híváshoz
Egy híváshoz való csatlakozáshoz meg kell hívnia az egyik API-t `CallAgent` .

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-to-an-incoming-call"></a>Előfizetés bejövő hívásra
Előfizetés bejövő hívási eseményre.

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func onIncomingCall(_ callAgent: CallAgent!, incomingcall: IncomingCall!) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func onCallEnded(_ incomingCall: IncomingCall!, args: PropertyChangedEventArgs!) {
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>Bejövő hívás elfogadása
Hívás fogadásához hívja `accept` meg a metódust egy hívási objektumon. Delegált beállítása a következőre: `CallAgent` .

```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions) { (call, error) in
               if error == nil {
                   print("Incoming call accepted")
               } else {
                   print("Failed to accept incoming call")
               }
           }
} else {
   print("No incoming call found to accept")
}
```

## <a name="set-up-push-notifications"></a>Leküldéses értesítések beállítása

A mobil leküldéses értesítés az előugró értesítés, amelyet a mobileszközön kap. A híváshoz a VoIP (hangvezérelt Internet Protocol) leküldéses értesítések küldésére fogunk összpontosítani. 

A következő szakaszok ismertetik a leküldéses értesítések regisztrálását, kezelését és regisztrációjának megszüntetését. A feladatok elkezdése előtt végezze el az alábbi előfeltételeket:

1. A Xcode-ben lépjen az **&-képességek aláírása lehetőségre**. Adjon hozzá egy képességet a **+ képesség lehetőség** kiválasztásával, majd válassza a **leküldéses értesítések** lehetőséget.
2. Adjon hozzá egy másik képességet a **+ képesség lehetőség** kiválasztásával, majd válassza a **háttér mód** lehetőséget.
3. A **háttér mód** területen jelölje be az **IP-cím** és a **távoli értesítések** hangjelzése jelölőnégyzetet.

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Képernyőkép, amely bemutatja, hogyan adhat hozzá képességeket a Xcode-ben." lightbox="../media/ios/xcode-push-notification.png":::

### <a name="register-for-push-notifications"></a>Regisztráció leküldéses értesítésekhez

A leküldéses értesítések regisztrálásához hívjon `registerPushNotification()` be egy `CallAgent` példányt egy eszköz regisztrációs jogkivonattal.

A sikeres inicializálás után a leküldéses értesítések regisztrációjának kell történnie. Az `callAgent` objektum megsemmisítve lesz `logout` meghívva, amely automatikusan törli a leküldéses értesítések regisztrációját.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken: deviceToken) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

### <a name="handle-push-notifications"></a>Leküldéses értesítések kezelése
Ha leküldéses értesítéseket szeretne kapni a bejövő hívásokról, hívjon fel egy olyan példányt, amely `handlePushNotification()` `CallAgent` szótári adattartalommal rendelkezik.

```swift

let callNotification = IncomingCallInformation.from(payload: pushPayload?.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
}

```
### <a name="unregister-push-notifications"></a>Leküldéses értesítések regisztrációjának törlése

Az alkalmazások bármikor megszüntetik a leküldéses értesítések regisztrációját. Egyszerűen hívja `unregisterPushNotification` meg a metódust `CallAgent` .

> [!NOTE]
> Az alkalmazások nem törlődnek automatikusan a leküldéses értesítésekből a kijelentkezéskor.

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="perform-mid-call-operations"></a>Telefonos műveletek végrehajtása

A videóval és hanggal kapcsolatos beállítások kezeléséhez hívás közben különféle műveleteket is végrehajthat.

### <a name="mute-and-unmute"></a>Némítás és némítás feloldása

A helyi végpont némításához vagy a némítás feloldásához használhatja a `mute` és az `unmute` aszinkron API-kat.

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

A következő kód használatával aszinkron módon lehet felnémítani a helyi végpontot.

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>Helyi videó küldésének elindítása és leállítása

Ha meg szeretné kezdeni a helyi videó küldését a hívás más résztvevői számára, használja az API-t, és adja meg a következőt: `startVideo` `localVideoStream` `camera` .

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

A videó küldésének megkezdése után a `LocalVideoStream` példány felveszi a `localVideoStreams` gyűjteményt egy hívási példányra.

```swift

call.localVideoStreams[0]

```

A helyi videó leállításához adja meg a `localVideoStream` hívásból visszaadott példányt `call.startVideo` . Ez egy aszinkron művelet.

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="manage-remote-participants"></a>Távoli résztvevők kezelése

Minden távoli résztvevőt a típus képvisel `RemoteParticipant` , és a gyűjteményen keresztül érhetők el a `remoteParticipants` hívási példányon.

### <a name="list-participants-in-a-call"></a>Hívás résztvevőinek listázása

```swift

call.remoteParticipants

```

### <a name="get-remote-participant-properties"></a>Távoli résztvevő tulajdonságainak beolvasása

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identity

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Résztvevő hozzáadása egy híváshoz

Ha szeretne hozzáadni egy résztvevőt egy híváshoz (vagy egy felhasználóhoz vagy telefonszámhoz), akkor meghívhatja a következőt: `addParticipant` . Ezzel a paranccsal a rendszer szinkron módon visszaküld egy távoli résztvevő példányt.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Résztvevő eltávolítása egy hívásból
Ha el szeretne távolítani egy résztvevőt egy hívásból (vagy egy felhasználóról vagy egy telefonszámról), meghívja az API-t `removeParticipant` . Ez a megoldás aszinkron módon fog megjelenni.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>A távoli résztvevő video streamek megjelenítése

A távoli résztvevők videó-vagy nyomtatómegosztást indíthatnak hívás közben.

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>Kezelheti a távoli résztvevők videó-megosztási vagy képernyő-megosztási streamjét

A távoli résztvevők streamek listázásához vizsgálja meg a `videoStreams` gyűjteményeket.

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="get-remote-video-stream-properties"></a>Távoli video stream-tulajdonságok beolvasása

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-streams"></a>Távoli résztvevői streamek megjelenítése

A távoli résztvevői streamek megjelenítésének megkezdéséhez használja a következő kódot.

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="get-remote-video-renderer-methods-and-properties"></a>Távoli videó-megjelenítő metódusok és tulajdonságok beolvasása

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="manage-devices"></a>Eszközök kezelése

`DeviceManager` lehetővé teszi a helyi eszközök enumerálását, amelyek a hang-és video-adatfolyamok továbbítására irányuló hívásokban használhatók. Azt is lehetővé teszi, hogy engedélyt kérjen a felhasználótól egy mikrofonhoz vagy kamerához való hozzáféréshez. Elérheti `deviceManager` az `callClient` objektumot.

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>Helyi eszközök enumerálása

A helyi eszközök eléréséhez használhat enumerálási metódusokat az Eszközkezelőben. A számbavétel egy szinkron művelet.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-the-default-microphone-or-speaker"></a>Az alapértelmezett mikrofon vagy hangszóró beállítása

Az Eszközkezelővel beállíthat egy alapértelmezett eszközt, amelyet a rendszer a hívás indításakor használni fog. Ha a verem alapértékei nincsenek beállítva, a kommunikációs szolgáltatások az operációs rendszer alapértelmezett értékeire kerülnek vissza.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.cameras!.first
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.speakers!
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="get-a-local-camera-preview"></a>Helyi kamera előzetes verziójának beolvasása

A segítségével `Renderer` megkezdheti a stream megjelenítését a helyi kameráról. Ezt a streamet nem lehet elküldeni a többi résztvevőnek; Ez egy helyi előnézeti hírcsatorna. Ez egy aszinkron művelet.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="get-local-camera-preview-properties"></a>Helyi kamera előnézeti tulajdonságainak beolvasása

A megjelenítő olyan tulajdonságokat és metódusokat tartalmaz, amelyek lehetővé teszik a renderelés vezérlését.

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [RendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="subscribe-to-notifications"></a>Feliratkozás értesítésekre

Előfizethet a legtöbb tulajdonságra és gyűjteményre, hogy értesítést kapjon az értékek változásakor.

### <a name="properties"></a>Tulajdonságok
Az eseményekre való előfizetéshez `property changed` használja a következő kódot.

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Gyűjtemények
Az eseményekre való előfizetéshez `collection updated` használja a következő kódot.

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
