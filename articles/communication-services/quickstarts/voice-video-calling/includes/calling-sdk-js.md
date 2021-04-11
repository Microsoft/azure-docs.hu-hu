---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 49054d9bbde67dc3670ec444e4b60c3ddf503db5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645432"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- Felhasználói hozzáférési jogkivonat a hívó ügyfél engedélyezéséhez. További információ: [hozzáférési tokenek létrehozása és kezelése](../../access-tokens.md).
- Nem kötelező: fejezze be a gyors üzembe helyezést, és [adja hozzá a hanghívást az alkalmazásához](../getting-started-with-calling.md).

## <a name="install-the-sdk"></a>Az SDK telepítése

> [!NOTE]
> Ez a dokumentum a hívó SDK-ból származó 1.0.0-Beta. 10 verziót használja.

A `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatásokat hívó és gyakori SDK-kat a JavaScript használatára.
Ez a dokumentum a a hívó könyvtárának a 1.0.0-Beta. 10 verziójában található típusokra hivatkozik.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az Azure kommunikációs szolgáltatások Meghívási SDK-val kapcsolatos főbb funkcióit kezelik:

| Név                             | Leírás                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | A hívó SDK fő belépési pontja.                                                                       |
| `CallAgent`                        | A hívások indításához és kezeléséhez használatos.                                                                                            |
| `DeviceManager`                    | A média eszközök kezelésére szolgál.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementálja a `CommunicationTokenCredential` felületet, amely a létrehozásához használatos `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>CallClient-példány inicializálása, CallAgent-példány létrehozása és hozzáférés a deviceManager

Hozzon létre egy új `CallClient` példányt. Konfigurálhatja az egyéni beállításokkal, például a Logger-példánnyal.

Ha van egy `CallClient` példánya, létrehozhat egy `CallAgent` példányt úgy, hogy meghívja a `createCallAgent` metódust a `CallClient` példányon. Ez aszinkron módon ad vissza egy `CallAgent` példány objektumot.

A `createCallAgent` metódus `CommunicationTokenCredential` argumentumként használja. [Felhasználói hozzáférési tokent](../../access-tokens.md)fogad el.

A példányok létrehozása után használhatja a `callAgent` `getDeviceManager` példányon a metódust a `CallClient` hozzáféréshez `deviceManager` .

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Hívás elhelyezése

Hívás létrehozásához és elindításához használja az API-k egyikét, `callAgent` és adja meg a kommunikációs szolgáltatások Identity SDK használatával létrehozott felhasználót.

A hívás létrehozása és a kezdés szinkronban van. A hívási példány lehetővé teszi a hívási eseményekre való előfizetést.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Helyezzen egy 1: n hívást egy felhasználónak vagy PSTN-nek

Egy másik kommunikációs szolgáltatás felhasználójának meghívásához használja a `startCall` metódust, `callAgent` és adja át a címzettet, `CommunicationUserIdentifier` amelyet a [kommunikációs szolgáltatások felügyeleti könyvtárával hozott létre](../../access-tokens.md).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Nyilvános kapcsolós telefonhálózat (PSTN) hívásához használja a `startCall` metódust, `callAgent` és adja át a címzettet `PhoneNumberIdentifier` . A kommunikációs szolgáltatások erőforrását úgy kell konfigurálni, hogy engedélyezze a PSTN-hívást.

Ha egy PSTN-számot hív meg, adja meg a másodlagos hívó AZONOSÍTÓját. Az alternatív hívóazonosító egy olyan telefonszám (az E. 164 szabvány alapján), amely a hívót egy PSTN-hívásban azonosítja. Ez a telefonszám, amelyet a hívás címzettje lát a bejövő híváshoz.

> [!NOTE]
> A PSTN-hívás jelenleg privát előzetes verzióban érhető el. A hozzáféréshez [alkalmazza a korai örökbefogadó programot](https://aka.ms/ACS-EarlyAdopter).

1:1-híváshoz használja a következő kódot:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

1: n hívás esetén használja a következő kódot:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>1:1-hívás elhelyezése videokameráról

> [!IMPORTANT]
> Jelenleg legfeljebb egy kimenő helyi videó stream lehet.

Videohívás létrehozásához az metódusának használatával kell megadnia a kamerákat `getCameras()` `deviceManager` .

A kamera kiválasztása után egy példány létrehozásához használhatja azt `LocalVideoStream` . A `videoOptions` tömbön belüli elemként adja át `localVideoStream` a `startCall` metódusnak.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Ha a hívása csatlakozik, a automatikusan elindít egy video streamet a kiválasztott kameráról a másik résztvevőhöz. Ez a `Call.Accept()` videó beállításaira és a `CallAgent.join()` videó lehetőségeire is vonatkozik.

### <a name="join-a-group-call"></a>Csatlakozás csoportos híváshoz

> [!NOTE]
> A `groupId` paraméter rendszermetaadatoknak minősül, és a Microsoft a rendszer futtatásához szükséges műveletekhez használhatja azokat. Az értékben ne szerepeljenek személyes értékek `groupId` . A Microsoft nem kezeli ezt a paramétert személyes adatként, és annak tartalma látható lesz a Microsoft alkalmazottai számára, vagy hosszú távon tárolható.
>
> A `groupId` paraméterhez GUID formátumú érték szükséges. Azt javasoljuk, hogy véletlenszerűen generált GUID azonosítókat használjon, amelyek nem számítanak személyes adatként a rendszerekben.
>

Új csoportos hívás indításához vagy egy folyamatos csoportos híváshoz való csatlakozáshoz használja a `join` metódust, és adjon át egy objektumot egy `groupId` tulajdonsággal. Az `groupId` értéknek GUID azonosítónak kell lennie.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Csatlakozás Teams-értekezlethez

Ha csatlakozni szeretne egy csapathoz, használja a `join` metódust, és adja át az értekezlet hivatkozását vagy koordinátáit.

Csatlakozás egy értekezleti hivatkozás használatával:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Csatlakozás az értekezlet koordinátáinak használatával:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Bejövő hívás fogadása

A `callAgent` példány olyan `incomingCall` eseményt bocsát ki, amikor a bejelentkezett identitás bejövő hívást kap. Ha ezt az eseményt szeretné meghallgatni, a következő lehetőségek egyikének használatával fizethet elő:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {

    //Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

Az `incomingCall` esemény olyan `incomingCall` példányt tartalmaz, amelyet elfogad vagy visszautasíthat.

## <a name="manage-calls"></a>Hívások kezelése

Hívás közben elérheti a hívási tulajdonságokat, és kezelheti a videó-és hangbeállításokat.

### <a name="check-call-properties"></a>Hívási tulajdonságok keresése

A hívás egyedi AZONOSÍTÓjának (karakterláncának) beolvasása:

   ```js
    const callId: string = call.id;
   ```

Ismerje meg a hívás többi résztvevőjét a `remoteParticipants` "hívás" példányon található gyűjtemény vizsgálatával:

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

A bejövő hívások hívójának azonosítása:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` az egyik `CommunicationIdentifier` típus.

Hívás állapotának beolvasása:

   ```js
   const callState = call.state;
   ```

   Ez egy olyan sztringet ad vissza, amely a hívás aktuális állapotát jelöli:

  - `None`: Kezdeti hívás állapota.
  - `Connecting`: Kezdeti átmeneti állapot, ha a rendszer hívást helyez el vagy fogad el.
  - `Ringing`: Kimenő hívás esetén azt jelzi, hogy a hívás a távoli résztvevők számára csörög. Ez `Incoming` a saját oldalán van.
  - `EarlyMedia`: Azt az állapotot jelzi, amelyben a rendszer meghívja a bejelentést a hívás csatlakoztatása előtt.
  - `Connected`: Azt jelzi, hogy a hívás csatlakoztatva van.
  - `LocalHold`: Azt jelzi, hogy a hívást egy helyi résztvevő helyezi el. A helyi végpont és a távoli résztvevők között nem folyik média.
  - `RemoteHold`: Azt jelzi, hogy a hívás távoli résztvevő általi tárolásra került. A helyi végpont és a távoli résztvevők között nem folyik média.
  - `Disconnecting`: Átmeneti állapot, mielőtt a hívás `Disconnected` állapota állapotba kerül.
  - `Disconnected`: Végső hívás állapota. Ha a hálózati kapcsolat megszakad, az állapot `Disconnected` két perc után módosul.

Megtudhatja, miért ért véget a hívás a tulajdonság vizsgálatával `callEndReason` :

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

Megtudhatja, hogy az aktuális hívás bejövő vagy kimenő a tulajdonság vizsgálatával `direction` . Visszaadja `CallDirection` .

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Ellenőrizze, hogy az aktuális mikrofon el van-e némítva. Visszaadja `Boolean` .

   ```js
   const muted = call.isMuted;
   ```

Annak megállapítása, hogy a képernyő-megosztási adatfolyamot egy adott végponton küldik-e el a tulajdonság ellenőrzésével `isScreenSharingOn` . Visszaadja `Boolean` .

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Ellenőrizze az aktív videó streameket a `localVideoStreams` gyűjtemény ellenőrzésével. Objektumokat ad vissza `LocalVideoStream` .

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```

### <a name="check-a-callended-event"></a>CallEnded-esemény keresése

A `call` példány eseményt bocsát ki, `callEnded` Amikor a hívás véget ér. Az esemény figyeléséhez a következő kód használatával kell előfizetnie:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Némítás és némítás feloldása

A helyi végpont némításához vagy lenémításához használhatja a és az `mute` `unmute` aszinkron API-kat:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Helyi videó küldésének elindítása és leállítása

A videók elindításához meg kell adnia a kamerákat az `getCameras` objektum metódusának használatával `deviceManager` . Ezután hozzon létre egy új példányt a `LocalVideoStream` metódusban a kívánt kamera argumentumként való átadásával `startVideo` :

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

A videó küldésének sikeres megkezdése után egy `LocalVideoStream` példány kerül a `localVideoStreams` gyűjteménybe egy hívási példányon.

```js
call.localVideoStreams[0] === localVideoStream;
```

A helyi videó leállításához adja át a `localVideoStream` gyűjteményben elérhető példányt `localVideoStreams` :

```js
await call.stopVideo(localVideoStream);
```

Átválthat egy másik kamera-eszközre, miközben egy videót küld egy példányon való meghívásával `switchSource` `localVideoStream` :

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>Távoli résztvevők kezelése

Az összes távoli résztvevő típus szerint jelenik `RemoteParticipant` meg, és a gyűjteményen keresztül érhető el a `remoteParticipants` hívási példányon.

### <a name="list-the-participants-in-a-call"></a>Egy hívás résztvevőinek listázása

A `remoteParticipants` gyűjtemény a távoli résztvevők listáját adja vissza egy hívásban:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Hozzáférés a távoli résztvevő tulajdonságaihoz

A távoli résztvevők a társított tulajdonságokat és gyűjteményeket is magukban foglalják:

- `CommunicationIdentifier`: Távoli résztvevő azonosítójának beolvasása. Az identitás az egyik `CommunicationIdentifier` típusa:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  A következő típusok egyike lehet `CommunicationIdentifier` :

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Az ACS-felhasználót jelképező objektum.
  - `{ phoneNumber: '<E.164>' }`: Az E. 164 formátumú telefonszámot képviselő objektum.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: A csapat felhasználót jelképező objektum.
  - `{ id: string }`: az Object repredenting azonosítója, amely nem felel meg a többi azonosító típusának sem

- `state`: Távoli résztvevő állapotának beolvasása.

  ```js
  const state = remoteParticipant.state;
  ```

  Az állapot a következőket teheti:

  - `Idle`: Kezdeti állapot.
  - `Connecting`: Átmeneti állapot, amikor egy résztvevő csatlakozik a híváshoz.
  - `Ringing`: A résztvevő csengetés.
  - `Connected`: A résztvevő csatlakozik a híváshoz.
  - `Hold`: A résztvevő tart.
  - `EarlyMedia`: A résztvevőnek a híváshoz való csatlakozása előtt lejátszott közlemény.
  - `Disconnected`: Végleges állapot. A résztvevő nem kapcsolódik a híváshoz. Ha a távoli résztvevő elveszíti hálózati kapcsolatát, az állapotuk `Disconnected` két perc után módosul.

- `callEndReason`: Ha meg szeretné tudni, hogy a résztvevő miért hagyta el a hívást, tekintse meg a következő `callEndReason` tulajdonságot:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` állapot: Ha meg szeretné tudni, hogy a távoli résztvevő el van-e némítva, ellenőrizze a `isMuted` tulajdonságot. Visszaadja `Boolean` .

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` állapot: Ha szeretné megtudni, hogy egy távoli résztvevő beszél-e, ellenőrizze a `isSpeaking` tulajdonságot. Visszaadja `Boolean` .

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Az adott résztvevő által a hívásban küldött összes videó stream vizsgálatához ellenőrizze a `videoStreams` gyűjteményt. Objektumokat tartalmaz `RemoteVideoStream` .

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: A távoli résztvevő megjelenítendő nevének beolvasásához vizsgálja meg a tulajdonságot, amely a `displayName` karakterláncot adja vissza. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Résztvevő hozzáadása egy híváshoz

Ha egy résztvevőt (vagy egy felhasználót vagy egy telefonszámot) szeretne felvenni egy hívásba, használhatja a következőt: `addParticipant` . Adja meg az egyik `Identifier` típust. A példányt adja vissza `remoteParticipant` .

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Résztvevő eltávolítása egy hívásból

Ha egy résztvevőt (vagy egy felhasználót vagy egy telefonszámot) szeretne eltávolítani egy hívásból, meghívhatja a következőt: `removeParticipant` . Az egyik típust kell átadnia `Identifier` . Ez aszinkron módon oldódik fel, miután a résztvevő el lett távolítva a hívásból. A résztvevőt a gyűjteményből is eltávolítja a rendszer `remoteParticipants` .

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

A megjelenítéshez `RemoteVideoStream` elő kell fizetnie egy `isAvailableChanged` eseményre. Ha a `isAvailable` tulajdonság módosul `true` , a távoli résztvevő egy streamet küld. Ezután hozzon létre egy új példányt a alkalmazásban `VideoStreamRenderer` , majd hozzon létre egy új `VideoStreamRendererView` példányt az aszinkron `createView` metódus használatával.  Ezután csatolhat `view.target` bármely felhasználóifelület-elemhez.

Ha a távoli adatfolyamok változásai elérhetők, eldöntheti, hogy az egészet `VideoStreamRenderer` , egy adott `VideoStreamRendererView` vagy megtartja-e, de ez az üres videó keretének megjelenítését eredményezi.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Távoli video stream tulajdonságai

A távoli videó streamek a következő tulajdonságokkal rendelkeznek:

- `id`: Egy távoli video stream azonosítója.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: Lehet `Video` vagy `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Azt határozza meg, hogy egy távoli résztvevői végpont aktívan küldjön-e adatfolyamot.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer metódusok és tulajdonságok

Hozzon létre egy olyan `VideoStreamRendererView` példányt, amely csatolható az alkalmazás felhasználói felületén a távoli video stream megjelenítéséhez, aszinkron `createView()` módszerrel, amely akkor oldódik fel, ha a stream készen áll a megjelenítésre, és egy olyan objektumot ad vissza, amely egy, `target` `video` a DOM-fában tetszőlegesen hozzáfűzhető elemet jelképező tulajdonságot képvisel.

  ```js
  videoStreamRenderer.createView()
  ```

A és az `videoStreamRenderer` összes társított `VideoStreamRendererView` példány:

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererView metódusok és tulajdonságok

A létrehozásakor `VideoStreamRendererView` megadhatja a és a `scalingMode` `isMirrored` tulajdonságokat. `scalingMode` lehet `Stretch` , `Crop` , vagy `Fit` . Ha meg `isMirrored` van adva, a megjelenített adatfolyam függőlegesen van tükrözve.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Minden `VideoStreamRendererView` példány rendelkezik egy `target` tulajdonsággal, amely a renderelési felületet jelképezi. Csatolja ezt a tulajdonságot az alkalmazás felhasználói felületén:

```js
htmlElement.appendChild(view.target);
```

`scalingMode`A módszert a metódus meghívásával frissítheti `updateScalingMode` :

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Eszközfelügyelet

A alkalmazásban `deviceManager` megadhatja azokat a helyi eszközöket, amelyek továbbítják a hang-és video-adatfolyamokat egy hívásban. Emellett segítséget nyújt egy másik felhasználó mikrofonjának és kamerájának a natív böngésző API használatával való eléréséhez.

`deviceManager`A metódus meghívásával érheti el a `callClient.getDeviceManager()` következőket:

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Helyi eszközök beolvasása

A helyi eszközökhöz való hozzáféréshez használhat enumerálási metódusokat `deviceManager` .

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Az alapértelmezett mikrofon és hangszóró beállítása

A alkalmazásban beállíthat `deviceManager` egy alapértelmezett eszközt, amelyet a hívás elindításához fog használni. Ha az ügyfél alapértékei nincsenek beállítva, a kommunikációs szolgáltatások az operációs rendszer alapértelmezett értékeit használják.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Helyi kamera előzetes verziója

A `deviceManager` és `VideoStreamRenderer` a segítségével megkezdheti a streamek megjelenítését a helyi kameráról. Ezt a streamet nem lehet elküldeni a többi résztvevőnek; Ez egy helyi előnézeti hírcsatorna.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Engedély kérése a kamera és a mikrofon számára

Kérje meg a felhasználót, hogy adja meg a kamera és a mikrofon engedélyeit:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Ez egy olyan objektummal oldható fel, amely jelzi, hogy a `audio` és az `video` engedélyek megadása megtörtént-e:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Hívások rögzítése

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

A Call Recording a Core API kiterjesztett funkciója `Call` . Először be kell szereznie a rögzítési funkció API-objektumát:

```js
const callRecordingApi = call.api(Features.Recording);
```

Ezután ellenőrizze, hogy a hívás rögzítése folyamatban van-e `isRecordingActive` `callRecordingApi` . Visszaadja `Boolean` .

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

## <a name="transfer-calls"></a>Hívások átvitele

A hívásátirányítás a Core API kiterjesztett funkciója `Call` . Először be kell szereznie az átvitel funkció API-objektumát:

```js
const callTransferApi = call.api(Features.Transfer);
```

A Call Transfers három felet érint:

- *Átadó*: az a személy, aki kezdeményezi az áthelyezési kérelmet.
- *Átvevő*: az átvitt személy.
- *Továbbítási cél*: az a személy, aki átkerül a szolgáltatásba.

Az átvitelek az alábbi lépéseket követik:

1. Már van egy csatlakoztatott hívás az *átadó* és a *kedvezményezett* között. Az *átadó* úgy dönt, hogy áthelyezi a hívást a *kedvezményezetttől* az *átvitel* céljáig.
1. Az *átadó* meghívja az `transfer` API-t.
1. A *megszerző* dönti el, hogy az `accept` `reject` átadási kérelem az *átvitel céljára* vonatkozik-e egy `transferRequested` esemény használatával.
1. Az *átvitel célja* csak akkor fogad bejövő hívást, ha a *kedvezményezett* elfogadja az átvitelre vonatkozó kérelmet.

Egy aktuális hívás átviteléhez használhatja az API-t `transfer` . `transfer` a választható `transferCallOptions` lehetőséget veszi fel, amely lehetővé teszi a `disableForwardingAndUnanswered` jelző beállítását:

- `disableForwardingAndUnanswered = false`: Ha az átviteli *cél* nem válaszol az átviteli hívásra, az átvitel a továbbítási *cél* továbbítását és a nem válaszoló beállításokat követi.
- `disableForwardingAndUnanswered = true`: Ha az átviteli *cél* nem válaszol az átviteli hívásra, az átviteli kísérlet véget ér.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Az `transfer` API lehetővé teszi az előfizetést `transferStateChanged` és az `transferRequested` eseményeket. Az `transferRequested` esemény egy `call` példányból származik; egy `transferStateChanged` esemény és egy `state` `error` példányból származnak `transfer` .

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

A *kedvezményezett* elfogadhatja vagy elutasíthatja az *átadó* által az `transferRequested` eseményen a vagy a használatával kezdeményezett adatátviteli kérelmet `accept()` `reject()` `transferRequestedEventArgs` . A alkalmazásban `targetParticipant` információk és `accept` `reject` metódusok érhetők el `transferRequestedEventArgs` .

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

## <a name="learn-about-eventing-models"></a>További tudnivalók az eseményvezérelt modellekről

Vizsgálja meg a jelenlegi értékeket, és fizessen elő a későbbi értékek frissítési eseményeire.

### <a name="properties"></a>Tulajdonságok

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Gyűjtemények

```js
// Inspect the current collection
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
