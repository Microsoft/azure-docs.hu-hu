---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 512b23b414328c0b7e9bbf8ef77a0d32083c84e5
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661526"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- A a `User Access Token` hívási ügyfél engedélyezéséhez. További információ a [beszerzéséről `User Access Token` ](../../access-tokens.md)
- Opcionális: fejezze be a gyors üzembe helyezési útmutatót az [alkalmazáshoz való hívás hozzáadásával](../getting-started-with-calling.md)

## <a name="setting-up"></a>Beállítás

### <a name="creating-the-xcode-project"></a>A Xcode projekt létrehozása

> [!NOTE]
> Ez a dokumentum a hívó ügyféloldali kódtár verziójának 1.0.0-Beta. 8 verzióját használja.

A Xcode-ben hozzon létre egy új iOS-projektet, és válassza ki az **Egynézetes alkalmazás** sablonját. Ez a rövid útmutató a [SwiftUI keretrendszert](https://developer.apple.com/xcode/swiftui/)használja, ezért állítsa be a **nyelvet** a **Swift** értékre, és a **felhasználói felületet** **SwiftUI**. Ebben a rövid útmutatóban nem hozhat létre egység-vagy felhasználói felületi teszteket. Nyugodtan szüntesse meg az **egységek tesztelését** , és törölje a **felhasználói felületi tesztek belefoglalását** is.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Képernyőfelvétel: az új projekt létrehozása ablak a Xcode belül.":::

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

2. Futtassa a `pod install` parancsot.
3. Nyissa meg a `.xcworkspace` with Xcode.

### <a name="request-access-to-the-microphone"></a>Hozzáférés kérése a mikrofonhoz

Az eszköz mikrofonjának eléréséhez frissítenie kell az alkalmazás információs tulajdonságainak listáját `NSMicrophoneUsageDescription` . A társított értéket adja meg, `string` amely szerepelni fog a párbeszédpanelen, amelyet a rendszer a kérelem hozzáférésének kéréséhez használ a felhasználótól.

Kattintson a jobb gombbal a `Info.plist` projekt fájának bejegyzésére, és válassza a **Megnyitás**  >  **forrásként kód** lehetőséget. Adja hozzá a következő sorokat a legfelső szintű `<dict>` szakaszhoz, majd mentse a fájlt.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Nyissa meg a projekt **ContentView. Swift** fájlját, és adjon hozzá egy `import` deklarációt a fájl elejéhez az importáláshoz `AzureCommunicationCalling library` . Emellett az importáláshoz `AVFoundation` szükség van a kódban az audio engedélyre vonatkozó kérelemre.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik az Azure kommunikációs szolgáltatások az iOS-hez készült ügyféloldali függvénytárának főbb funkcióit.


| Név                                  | Leírás                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | A CallClient a hívó ügyféloldali függvénytár fő belépési pontja.|
| CallAgent | A CallAgent a hívások indításához és kezeléséhez használatos. |
| CommunicationTokenCredential | A rendszer a CommunicationTokenCredential használja jogkivonat-hitelesítő adatként a CallAgent létrehozásához.| 
| CommunicationIdentifier | A CommunicationIdentifier a felhasználó identitásának jelölésére szolgál, amely a következők egyike lehet: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

> [!NOTE]
> Az esemény-meghatalmazottak megvalósításakor az alkalmazásnak erős hivatkozást kell tartania az esemény-előfizetéseket igénylő objektumokra. Ha például egy `RemoteParticipant` objektumot ad vissza a metódus meghívásakor, `call.addParticipant` és az alkalmazás beállítja a delegált figyelését `RemoteParticipantDelegate` , az alkalmazásnak erős hivatkozást kell tartalmaznia az `RemoteParticipant` objektumra. Ellenkező esetben, ha az objektum gyűjtése bekerül, a delegált végzetes kivételt fog kiváltani, ha a hívó SDK megpróbálja meghívni az objektumot.

## <a name="initialize-the-callagent"></a>A CallAgent inicializálása

Egy példány létrehozásához olyan `CallAgent` `CallClient` metódust kell használnia, `callClient.createCallAgent` amely aszinkron módon visszaadja az `CallAgent` objektumot az inicializálás után.

A hívási ügyfél létrehozásához át kell adnia egy `CommunicationTokenCredential` objektumot.

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

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Adja meg `CommunicationTokenCredential` a fent létrehozott objektumot, `CallClient` és állítsa be a megjelenítendő nevet.

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

Egy hívás létrehozásához és elindításához meg kell hívnia az egyik API `CallAgent` -t, és meg kell adnia a kommunikációs szolgáltatások identitását, amelyet a kommunikációs szolgáltatások kezelési ügyféloldali kódtár használatával kiépített.

A hívás létrehozása és a kezdés szinkronban van. Ekkor megjelenik a hívási példány, amely lehetővé teszi a hívás összes eseményére való előfizetést.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Helyezzen 1:1-hívást egy felhasználónak vagy 1: n hívásnak a felhasználók és a PSTN használatával

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>1: n hívás elhelyezése a felhasználók és a PSTN között
Ha a hívást PSTN-re szeretné helyezni, meg kell adnia a kommunikációs szolgáltatásokkal megszerzett telefonszámot
```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>1:1-hívás elhelyezése videóval
A Device Manager-példány beszerzéséhez [tekintse](#device-management) meg a következőt

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
Egy híváshoz való csatlakozáshoz meg kell hívnia az egyik API-t a *CallAgent* -on

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-for-incoming-call"></a>Előfizetés bejövő hívásra
Előfizetés a bejövő hívási eseményre

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
Hívás fogadásához hívja meg az "elfogadás" metódust egy hívás objektumon.
Delegált beállítása a CallAgent 
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

## <a name="push-notification"></a>Leküldéses értesítés

A mobil leküldéses értesítés a mobileszköz felugró értesítése. A híváshoz a VoIP-ra (Internet Protocol) leküldéses értesítések küldésére fogunk összpontosítani. A leküldéses értesítések regisztrálásához, a leküldéses értesítések kezeléséhez, valamint a leküldéses értesítések regisztrációjának megszüntetéséhez lehetőséget biztosítunk Önnek.

### <a name="prerequisite"></a>Előfeltétel

- 1. lépés: Xcode-> & képességek aláírása – > hozzáadási képesség – > "leküldéses értesítések"
- 2. lépés: Xcode – > & képességeinek aláírása – > hozzáadási képesség – > "háttér mód"
- 3. lépés: "háttérbeli üzemmódok" – > válassza a "Voice over IP" és a "távoli értesítések" lehetőséget.

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Képernyőfelvétel: képességek hozzáadása a Xcode-ben." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Regisztráció leküldéses értesítésekhez

A leküldéses értesítések regisztrálásához hívja a registerPushNotification () függvényt egy *CallAgent* -példányra egy eszköz regisztrációs jogkivonattal.

A leküldéses értesítések regisztrálását a sikeres inicializálás után kell meghívni. Az `callAgent` objektum megsemmisítve lesz `logout` meghívva, amely automatikusan törli a leküldéses értesítések regisztrációját.


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

#### <a name="push-notification-handling"></a>Leküldéses értesítések kezelését
A bejövő hívások leküldéses értesítések fogadásához hívja a *handlePushNotification ()* metódust egy *CallAgent* -példányon egy szótári adattartalommal.

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
#### <a name="unregister-push-notification"></a>Leküldéses értesítés regisztrációjának törlése

Az alkalmazások bármikor megszüntetik a leküldéses értesítések regisztrációját. Egyszerűen hívja `unregisterPushNotification` meg a metódust a *CallAgent*.
> [!NOTE]
> Az alkalmazások nem törlődnek automatikusan a leküldéses értesítésből a kijelentkezéskor.

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="mid-call-operations"></a>Közép-hívási műveletek

A videóval és hanggal kapcsolatos beállítások kezeléséhez hívás közben különféle műveleteket is végrehajthat.

### <a name="mute-and-unmute"></a>Némítás és némítás feloldása

A helyi végpont némításához vagy a némítás feloldásához használhatja a `mute` és az `unmute` aszinkron API-kat:

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

Aszinkron Helyi némítás

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

Ha meg szeretné kezdeni a helyi videó küldését a hívás más résztvevőinek, használja az `startVideo` API-t és a Pass-t a következővel: `localVideoStream``camera`

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

A videó küldésének megkezdése után a `LocalVideoStream` példány felveszi a `localVideoStreams` gyűjteményt egy hívási példányra:

```swift

call.localVideoStreams[0]

```

Aszinkron A helyi videó leállításához továbbítsa a `localVideoStream` visszaadott értéket a következő hívásból `call.startVideo` :

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Távoli résztvevők kezelése

Minden távoli résztvevőt a típus képvisel `RemoteParticipant` , és a gyűjteményen keresztül érhetők el a `remoteParticipants` hívási példányon:

### <a name="list-participants-in-a-call"></a>Hívás résztvevőinek listázása

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Távoli résztvevő tulajdonságai

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
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

Meghívhatja a résztvevőt egy híváshoz (egy felhasználóhoz vagy egy telefonszámhoz) `addParticipant` . Ez szinkron módon visszaküld egy távoli résztvevő példányt.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Résztvevő eltávolítása egy hívásból
Ha el szeretne távolítani egy résztvevőt egy hívásból (vagy egy felhasználóról vagy egy telefonszámról), meghívhatja az API-t  `removeParticipant` . Ez a megoldás aszinkron módon fog megjelenni.

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

Előfordulhat, hogy a távoli résztvevők videó-vagy nyomtatómegosztást kezdeményeznek hívás közben.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>A távoli résztvevő videó/képernyő megosztása streamek kezelése

A távoli résztvevők streamek listázásához vizsgálja meg a `videoStreams` gyűjteményeket:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Távoli video stream tulajdonságai

```swift

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Távoli résztvevői stream megjelenítése

A távoli résztvevői streamek megjelenítésének megkezdése:

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Távoli videó-megjelenítő módszerei és tulajdonságai

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>Eszközfelügyelet

`DeviceManager` lehetővé teszi a helyi eszközök enumerálását, amelyek a hang-és video-adatfolyamok továbbítására irányuló hívásokban használhatók. Azt is lehetővé teszi, hogy engedélyt kérjen a felhasználótól a mikrofon/kamera eléréséhez. `deviceManager`A következő `callClient` objektumhoz férhet hozzá:

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

A helyi eszközökhöz való hozzáféréshez használhat enumerálási metódusokat a Eszközkezelő. A számbavétel egy szinkron művelet.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Alapértelmezett mikrofon/hangszóró beállítása

Az Eszközkezelővel beállíthat egy alapértelmezett eszközt, amelyet a rendszer a hívás indításakor fog használni. Ha a verem alapértékei nincsenek beállítva, a kommunikációs szolgáltatások az operációs rendszer alapértelmezett értékeire fognak visszatérni.

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

### <a name="local-camera-preview"></a>Helyi kamera előzetes verziója

A segítségével `Renderer` megkezdheti a stream megjelenítését a helyi kameráról. Ezt a streamet nem küldi el a többi résztvevőnek; Ez egy helyi előnézeti hírcsatorna. Ez egy aszinkron művelet.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>Helyi kamera előzetes verziójának tulajdonságai

A megjelenítő olyan tulajdonságokat és metódusokat tartalmaz, amelyek lehetővé teszik a renderelés vezérlését:

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

## <a name="eventing-model"></a>Eseményvezérelt modell

Előfizethet a legtöbb tulajdonságra és gyűjteményre, hogy értesítést kapjon az értékek változásakor.

### <a name="properties"></a>Tulajdonságok
Előfizetés az `property changed` eseményekre:

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Gyűjtemények
Előfizetés az `collection updated` eseményekre:

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
