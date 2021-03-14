---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 7d694ba15379dcb16f881d23712c64ead2ff6e7d
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103464964"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- A a `User Access Token` hívási ügyfél engedélyezéséhez. További információ a [beszerzéséről `User Access Token` ](../../access-tokens.md)
- Opcionális: fejezze be a gyors üzembe helyezési útmutatót az [alkalmazáshoz való hívás hozzáadásával](../getting-started-with-calling.md)

## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

> [!NOTE]
> Ez a dokumentum a hívó ügyféloldali kódtár verziójának 1.0.0-Beta. 6 verzióját használja.

A `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatásokat hívó és a javascripthez közös ügyféloldali kódtárakat.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objektummodell

Az alábbi osztályok és felületek az Azure kommunikációs szolgáltatások ügyfél-függvénytárának főbb funkcióit kezelik:

| Név                             | Leírás                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | A CallClient a hívó ügyféloldali függvénytár fő belépési pontja.                                                                       |
| CallAgent                        | A CallAgent a hívások indításához és kezeléséhez használatos.                                                                                            |
| DeviceManager                    | A DeviceManager a média eszközök felügyeletére szolgál.                                                                                           |
| AzureCommunicationTokenCredential | A AzureCommunicationTokenCredential osztály az CommunicationTokenCredential felületet valósítja meg, amely a CallAgent létrehozásához használatos. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>A CallClient inicializálása, a CallAgent létrehozása és a DeviceManager elérése

Új `CallClient` példány létrehozása. Konfigurálhatja az egyéni beállításokkal, például a Logger-példánnyal.
A példány `CallClient` létrehozása után létrehozhat egy `CallAgent` példányt a metódus meghívásával `createCallAgent` `CallClient` . Ez aszinkron módon ad vissza egy `CallAgent` példány objektumot.
A `createCallAgent` metódus `CommunicationTokenCredential` argumentumként fogadja el a [felhasználói hozzáférési tokent](../../access-tokens.md).
A callAgent- `DeviceManager` példány eléréséhez először létre kell hozni. Ezután használhatja a `getDeviceManager` metódust a `CallClient` példányon a DeviceManager lekéréséhez.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Kimenő hívás elhelyezése

Egy hívás létrehozásához és elindításához a CallAgent egyik API-t kell használnia, és meg kell adnia egy felhasználót, amelyet a kommunikációs szolgáltatások identitás-ügyféloldali kódtár használatával hozott létre.

A hívás létrehozása és a kezdés szinkronban van. A hívási példány lehetővé teszi a hívási eseményekre való előfizetést.

## <a name="place-a-call"></a>Hívás elhelyezése

### <a name="place-a-11-call-to-a-user-or-pstn"></a>1:1-hívás elhelyezése egy felhasználónak vagy PSTN-nek
Egy másik kommunikációs szolgáltatás felhasználójának hívásához hívja `startCall` meg a metódust, `callAgent` és adja át a hívott fél CommunicationUserIdentifier, amelyet [a kommunikációs szolgáltatások Identity Library használatával hozott létre](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Ha PSTN-hívást kíván elhelyezni, hívja meg a `startCall` metódust, `callAgent` és adja át a hívott fél PhoneNumberIdentifier.
A kommunikációs szolgáltatások erőforrását úgy kell konfigurálni, hogy engedélyezze a PSTN-hívást.
PSTN-szám meghívásakor meg kell adnia a másodlagos hívó AZONOSÍTÓját. Az alternatív hívóazonosító egy olyan telefonszámra hivatkozik (az E. 164 szabvány alapján), amely a hívót egy PSTN-hívásban azonosítja. Ha például másodlagos hívót ad meg a PSTN-híváshoz, akkor ez a telefonszám jelenik meg a hívott fél számára a hívás érkezésekor.

> [!WARNING]
> A PSTN-hívás jelenleg privát előzetes verzióban érhető el. A hozzáféréshez a [korai alkalmazói programra kell alkalmazni](https://aka.ms/ACS-EarlyAdopter).
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>1: n hívás elhelyezése a felhasználók és a PSTN között
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>1:1-hívás elhelyezése videokameráról
> [!WARNING]
> Jelenleg legfeljebb egy kimenő helyi videó stream lehet.
Videohívás létrehozásához a helyi kamerákat a deviceManager API használatával kell enumerálni `getCameras()` .
Miután kiválasztotta a kívánt kamerát, használja egy példány összeállításához, `LocalVideoStream` és a `videoOptions` tömbben lévő elemen belül adja át `localVideoStream` a `startCall` metódusnak.
A hívása után a rendszer automatikusan elindít egy video streamet a kiválasztott kamerából a többi résztvevő (k) felé. Ez a Call. Accept () video Options és a CallAgent. JOIN () videó beállításaira is vonatkozik.
```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Csatlakozás csoportos híváshoz
Új csoportos hívás indításához vagy egy folyamatos csoportos híváshoz való csatlakozáshoz használja a "Join" metódust, és adjon át egy objektumot egy `groupId` tulajdonsággal. Az értéknek GUID azonosítónak kell lennie.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>Csatlakozás Teams-értekezlethez
Ha csatlakozni szeretne egy csapathoz, használja a "Join" metódust, és adjon meg egy értekezlet-hivatkozást vagy egy értekezlet koordinátáit
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>Bejövő hívás fogadása

A `CallAgent` példány eseményt bocsát `incomingCall` ki, ha a bejelentkezett identitás bejövő hívást fogad. Az esemény meghallgatása érdekében a következő módon fizethet elő:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo
    
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

Az `incomingCall` esemény olyan példánnyal fog szolgálni, `IncomingCall` amelyben elfogadhatja vagy elutasíthatja a hívást.


## <a name="call-management"></a>Híváskezelő

A hívási tulajdonságokat elérheti, és különböző műveleteket hajthat végre a videóval és hanggal kapcsolatos beállítások kezeléséhez.

### <a name="call-properties"></a>Hívás tulajdonságai
* A hívás egyedi AZONOSÍTÓjának (karakterláncának) beolvasása.
```js

const callId: string = call.id;

```

* Ha szeretne többet megtudni a hívás többi résztvevőjének, vizsgálja meg a `remoteParticipant` példányon a gyűjteményt `call` . A tömb lista `RemoteParticipant` objektumokat tartalmaz
```js
const remoteParticipants = call.remoteParticipants;
```

* A hívó azonosítója, ha a hívás beérkező. Az azonosító a típusok egyike `CommunicationIdentifier`
```js

const callerIdentity = call.callerInfo.identifier;

* Get the state of the Call.
```js

const callState = call.state;

```
Ez egy olyan sztringet ad vissza, amely a hívás aktuális állapotát jelöli:
* "None" – kezdeti hívás állapota
* "Bejövő" – azt jelzi, hogy a hívás bejövő, el kell fogadni vagy el kell utasítani
* "Connecting" – a kezdeti átmenet állapota a hívás elhelyezése vagy elfogadása után
* "Ringing" – kimenő hívás esetén – azt jelzi, hogy a hívás a távoli résztvevők számára, a "bejövő" állapotú.
* "EarlyMedia" – azt jelzi, hogy a rendszer milyen állapotban játssza le a hívást a hívás csatlakoztatása előtt
* Csatlakoztatott – a hívás csatlakoztatva van
* "LocalHold" – a hívást a helyi résztvevő tartja, és a helyi végpont és a távoli résztvevő (k) között nem áramlanak média.
* "RemoteHold" – a hívást a távoli résztvevő tartja, és a helyi végpont és a távoli résztvevő (k) között nem áramlanak média.
* "Kapcsolat bontása" – átmeneti állapot, mielőtt a hívás "leválasztott" állapotba kerül
* "Leválasztott" – végső hívás állapota
  * Ha a hálózati kapcsolat megszakad, az állapot körülbelül 2 percet vesz igénybe.

* Ha meg szeretné tudni, hogy egy adott hívás miért ért véget, vizsgálja meg a `callEndReason` tulajdonságot.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Ha meg szeretné tudni, hogy az aktuális hívás bejövő vagy kimenő hívás, ellenőrizze a `direction` tulajdonságot, és adja vissza `CallDirection` .
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  Annak ellenőrzéséhez, hogy az aktuális mikrofon el van-e némítva, ellenőrizze a `muted` tulajdonságot, és adja vissza `Boolean` .
```js

const muted = call.isMicrophoneMuted;

```

* Ha szeretné megtudni, hogy a képernyő-megosztási adatfolyamot egy adott végponton küldik-e el, ellenőrizze a `isScreenSharingOn` tulajdonságot, és adja vissza `Boolean` .
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Az aktív videó streamek vizsgálatához tekintse `localVideoStreams` meg a gyűjteményt, amely `LocalVideoStream` objektumokat tartalmaz
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>Hívás befejeződött esemény

A `Call` példány eseményt bocsát ki, `callEnded` Amikor a hívás véget ér. Ha a következő módon szeretné megfigyelni az eseményt:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Némítás és némítás feloldása

A helyi végpont némításához vagy a némítás feloldásához használhatja a `mute` és az `unmute` aszinkron API-kat:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Helyi videó küldésének elindítása és leállítása


A videók elindításához az objektum metódusának használatával kell enumerálnia a kamerákat `getCameras` `deviceManager` . Ezután hozzon létre egy új példányt, amely `LocalVideoStream` argumentumként adja át a kívánt kamerát a `startVideo` metódusnak:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

Miután sikeresen elindította a videót, egy `LocalVideoStream` példány lesz hozzáadva a `localVideoStreams` gyűjteményhez egy hívási példányon.

```js

call.localVideoStreams[0] === localVideoStream;

```

A helyi videó leállításához továbbítsa a `localVideoStream` `localVideoStreams` gyűjteményben elérhető példányt:

```js

await call.stopVideo(localVideoStream);

```

Átválthat egy másik kamera-eszközre, miközben a videó küldését egy példány meghívásával végezheti el `switchSource` `localVideoStream` :

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);

```

## <a name="remote-participants-management"></a>Távoli résztvevők kezelése

Az összes távoli résztvevő típus szerint jelenik `RemoteParticipant` meg, és a gyűjteményen keresztül érhető el a `remoteParticipants` hívási példányon.

### <a name="list-participants-in-a-call"></a>Hívás résztvevőinek listázása
A `remoteParticipants` gyűjtemény az adott hívásban a távoli résztvevők listáját adja vissza:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Távoli résztvevő tulajdonságai
A távoli résztvevő rendelkezik a hozzá társított tulajdonságok és gyűjtemények készletével
#### <a name="communicationidentifier"></a>CommunicationIdentifier
A távoli résztvevő azonosítójának beolvasása.
Az identitás a "CommunicationIdentifier" típusok egyike:
```js
const identifier = remoteParticipant.identifier;
```
A "CommunicationIdentifier" típusok egyike lehet:
  * {communicationUserId: "<ACS_USER_ID" >} – az ACS-felhasználót jelképező objektum
  * {Telefonszám: "<E. 164>"} – az E. 164 formátumú telefonszámot képviselő objektum
  * {microsoftTeamsUserId: ' <TEAMS_USER_ID> ', isAnonymous?: Boolean; felhő?: "Public" | "DoD" | "gcch"} – a csapatok felhasználóját képviselő objektum

#### <a name="state"></a>Állam
A távoli résztvevő állapotának beolvasása.
```js

const state = remoteParticipant.state;
```
Az állapot lehet az egyik
* "Tétlen" – kezdeti állapot
* "Connecting" – átmeneti állapot, miközben a résztvevő csatlakozik a híváshoz
* "Ringing" – résztvevő csengetés
* "Csatlakoztatott" – a résztvevő csatlakozik a híváshoz
* "Hold" – a résztvevő tart
* "EarlyMedia" – a bejelentést csak akkor játssza le a rendszer, ha a résztvevő csatlakozik a híváshoz
* "Leválasztott" – végső állapot – a résztvevő nem kapcsolódik a híváshoz
  * Ha a távoli résztvevő elveszti hálózati kapcsolatát, akkor a távoli résztvevő állapota körülbelül 2 percet vesz igénybe.

#### <a name="call-end-reason"></a>Hívás befejezési oka
Annak megismeréséhez, hogy a résztvevő miért hagyta el a hívást, vizsgálja meg a `callEndReason` tulajdonságot:
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>Elnémítva
Annak ellenőrzéséhez, hogy a távoli résztvevő el van-e némítva vagy sem, vizsgálja `isMuted` meg a tulajdonságot, és adja vissza `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>Beszéd
Annak ellenőrzéséhez, hogy a távoli résztvevő beszél-e vagy sem, vizsgálja meg a visszaadott `isSpeaking` tulajdonság értékét. `Boolean`
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>Videó streamek
Az adott résztvevő által a hívásban küldött összes videó stream vizsgálatához tekintse `videoStreams` meg a gyűjteményt, amely `RemoteVideoStream` objektumokat tartalmaz
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Résztvevő hozzáadása egy híváshoz

Meghívhatja a résztvevőt egy híváshoz (egy felhasználóhoz vagy egy telefonszámhoz) `addParticipant` .
Adja meg az "azonosító" típusok egyikét.
Ez szinkron módon visszaküldi a távoli résztvevő példányát.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>Résztvevő eltávolítása egy hívásból

Ha el szeretne távolítani egy résztvevőt a hívásból (vagy egy felhasználóról vagy egy telefonszámról), akkor meghívhatja `removeParticipant` .
Az "azonosító" típusok egyikének kell átadnia, ez a megoldás aszinkron módon oldódik meg, ha a résztvevő el lesz távolítva a hívásból.
A résztvevő is el lesz távolítva a `remoteParticipants` gyűjteményből.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>A távoli résztvevő video streamek megjelenítése

A távoli résztvevők videó streamek és a képernyő-megosztási streamek listázásához vizsgálja meg a `videoStreams` gyűjteményeket:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

A megjelenítéséhez `RemoteVideoStream` elő kell fizetnie egy `isAvailableChanged` eseményre.
Ha a `isAvailable` tulajdonság módosul `true` , a távoli résztvevő egy streamet küld.
Ha ez megtörténik, hozzon létre egy új példányt `Renderer` , majd hozzon létre egy új `RendererView` példányt az aszinkron `createView` metódus használatával.  Ezután csatolhat `view.target` bármilyen felhasználóifelület-elemet.
Ha a távoli adatfolyamok változásai elérhetők, eldöntheti, hogy az egész renderelő, egy adott `RendererView` vagy megtartható, de ez az üres videó keretének megjelenítését eredményezi.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Távoli video stream tulajdonságai
A távoli videó streamek a következő tulajdonságokkal rendelkeznek:

* `Id` -Egy távoli videó stream azonosítója
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` – távoli video stream mérete (szélesség/magasság)
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` -lehet "Video" vagy "ScreenSharing"
```js
const type: MediaStreamType = remoteVideoStream.mediaStreamType;
```
* `isAvailable` – Azt jelzi, hogy a távoli résztvevői végpont aktívan küld-e adatfolyamként
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Megjelenítési metódusok és tulajdonságok

* Hozzon létre egy `RendererView` példányt, amely később csatolható az alkalmazás felhasználói felületén a távoli video stream megjelenítéséhez.
```js
renderer.createView()
```

* A leképező és az összes társított példány megsemmisítése `RendererView` .
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>RendererView metódusok és tulajdonságok
A létrehozásakor `RendererView` megadhatja a `scalingMode` és a `isMirrored` tulajdonságokat.
A skálázási mód lehet "stretch", "Crop" vagy "fit", ha meg `isMirrored` van adva, a megjelenített adatfolyam függőlegesen lesz tükrözve.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```
Minden megadott `RendererView` példány rendelkezik egy `target` tulajdonsággal, amely a renderelési felületet jelképezi. Ezt az alkalmazás felhasználói felületén kell csatolni:
```js
document.body.appendChild(rendererView.target);
```

A skálázási módot később is frissítheti a metódus meghívásával `updateScalingMode` .
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Eszközfelügyelet

`DeviceManager` lehetővé teszi olyan helyi eszközök enumerálását, amelyek a hang-és video-adatfolyamok továbbítására szolgáló hívásokban használhatók. Azt is lehetővé teszi, hogy engedélyt kérjen a felhasználótól, hogy a natív böngésző API használatával hozzáférjen a mikrofonhoz és a fényképezőgéphez.

A `deviceManager` by Calling `callClient.getDeviceManager()` metódussal érheti el.
> [!WARNING]
> A `callAgent` DeviceManager való hozzáféréshez jelenleg egy objektumot kell létrehozni először.

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Helyi eszközök enumerálása

A helyi eszközökhöz való hozzáféréshez használhat enumerálási metódusokat a Eszközkezelő. Az enumerálás egy aszinkron művelet.

```js

//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Alapértelmezett mikrofon/hangszóró beállítása

Az Eszközkezelővel beállíthat egy alapértelmezett eszközt, amelyet a rendszer a hívás indításakor fog használni.
Ha az ügyfél alapértelmezései nincsenek beállítva, a kommunikációs szolgáltatások az operációs rendszer alapértelmezett értékeire kerülnek vissza.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Helyi kamera előzetes verziója

A `DeviceManager` és `Renderer` a segítségével megkezdheti a streamek megjelenítését a helyi kameráról. Ezt a streamet nem lehet elküldeni a többi résztvevőnek; Ez egy helyi előnézeti hírcsatorna. Ez egy aszinkron művelet.

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Engedély kérése a kamera/mikrofon számára

Kérje meg a felhasználót, hogy adja meg a kamera/mikrofon engedélyeit a következőkkel:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```
Ez a megoldás aszinkron módon fog megjelenni egy olyan objektummal, amely megadja `audio` , hogy megadták-e az `video` engedélyeket:
```js
console.log(result.audio);
console.log(result.video);
```


## <a name="call-recording-management"></a>Call Recording Management

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

A Call Recording a Core API kiterjesztett funkciója `Call` . Először be kell szereznie a rögzítési funkció API-objektumát:

```js
const callRecordingApi = call.api(Features.Recording);
```

Ezt követően ellenőrizheti, hogy a rendszer rögzíti-e a hívást, és `isRecordingActive` visszaadja-e a tulajdonságot `callRecordingApi` `Boolean` .

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

Feliratkozhat a módosítások rögzítésére is:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

## <a name="call-transfer-management"></a>Hívásátirányítás kezelése

A hívásátirányítás a Core API kiterjesztett funkciója `Call` . Először be kell szereznie az átvitel funkció API-objektumát:

```js
const callTransferApi = call.api(Features.Transfer);
```

*A hívásátirányítás három fél* átvevője, *kedvezményezettje* és *átruházási célpontja*. Az átvitel folyamata a következőképpen működik:

1. Már van egy csatlakoztatott hívás az *átadó* és a *kedvezményezett* között
2. az *átadó* úgy dönt, hogy átviszi a hívást (a *kedvezményezett*  ->  *átviteli célpontja*)
3. *átadó* Call `transfer` API
4. a *kedvezményezett* dönti el, hogy `accept` `reject` az átadási kérelem továbbítva van-e a *Target* on `transferRequested` Event használatával.
5. az *átvitel célja* csak akkor kap bejövő hívást, ha a *kedvezményezett* `accept` az áthelyezési kérelmet

### <a name="transfer-terminology"></a>Terminológia átvitele

- Átadó – az a felhasználó, aki kezdeményezi az áthelyezési kérelmet
- Átvevő – az a személy, aki átviszi az átadót az átadás céljára
- Továbbítási cél – az a cél, amelybe a rendszer átviszi

A jelenlegi hívás átviteléhez használhatja a `transfer` szinkron API-t. `transfer` nem kötelező, `TransferCallOptions` amely lehetővé teszi a `disableForwardingAndUnanswered` jelző beállítását:

- `disableForwardingAndUnanswered` = FALSE – ha a *továbbítási cél* nem válaszol az átviteli hívásra, akkor a továbbítási *cél* továbbítása és a nem válaszoló beállítások lesznek végrehajtva.
- `disableForwardingAndUnanswered` = True – ha az *átvitel célja* nem válaszol az átviteli hívásra, akkor az átviteli kísérlet véget ért

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Az átvitel lehetővé teszi az előfizetést `transferStateChanged` és az `transferRequested` eseményeket. `transferRequsted` az esemény a `call` példánytól, az `transferStateChanged` eseménytől és az átviteltől származik, `state` és `error` a `transfer` példány

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

A kedvezményezett elfogadhatja vagy elutasíthatja az átadó által az `transferRequested` eseményen keresztül `accept()` vagy `reject()` a-ben kezdeményezett adatátviteli kérést `transferRequestedEventArgs` . A alkalmazásban `targetParticipant` az információi, `accept` `reject` a metódusok érhetők el `transferRequestedEventArgs` .

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="eventing-model"></a>Eseményvezérelt modell
Meg kell vizsgálnia a jelenlegi értékeket, és fel kell fizetnünk a későbbi értékek frissítési eseményeire.

### <a name="properties"></a>Tulajdonságok

```js
// Inspect current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Gyűjtemények
```js
// Inspect current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});



// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
