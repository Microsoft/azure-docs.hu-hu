---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: f20099943d3cfa3dd4afc161c26e5582e467ca8d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590081"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy üzembe helyezett Communication Services erőforrás. [Hozzon létre egy Communication Services erőforrást.](../../create-communication-resource.md)
- Felhasználói hozzáférési jogkivonat a hívó ügyfél engedélyezéséhez. További információ: [Hozzáférési jogkivonatok létrehozása és kezelése.](../../access-tokens.md)
- Nem kötelező: A hanghívás alkalmazáshoz való hozzáadásának rövid [útmutatója .](../getting-started-with-calling.md)

## <a name="install-the-sdk"></a>Az SDK telepítése

> [!NOTE]
> Ez a dokumentum ACS-t hívó Webes SDK-t használ.

Az `npm install` paranccsal telepítse a Azure Communication Services és a JavaScripthez használható gyakori SZOFTVERDK-okat.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Objektummodell

Az alábbi osztályok és felületek a hívó SDK néhány fő funkcióját Azure Communication Services kezelik:

| Név                             | Leírás                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | A hívó SDK fő belépési pontja.                                                                       |
| `CallAgent`                        | Hívásokat indít és kezel.                                                                                            |
| `DeviceManager`                    | Médiaeszközök kezelésére használható.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementálja `CommunicationTokenCredential` a illesztőt, amely a példányának megvalósításához `callAgent` használható. |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>CallClient-példány inicializálása, CallAgent-példány létrehozása és deviceManager elérése

Hozzon létre egy új `CallClient` példányt. Egyéni beállításokkal, például naplózó példányokkal konfigurálhatja.

Ha már van példánya, a példányon a metódus hívásával hozhat `CallClient` `CallAgent` létre `createCallAgent` `CallClient` példányt. Ez aszinkron módon egy `CallAgent` példányobjektumot ad vissza.

A `createCallAgent` metódus `CommunicationTokenCredential` argumentumként a metódust használja. Felhasználói hozzáférési [jogkivonatot fogad el.](../../access-tokens.md)

A példányon `getDeviceManager` a metódussal `CallClient` férhet hozzá a következő `deviceManager` hez: .

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

## <a name="place-a-call"></a>Hívás el helyezése

Hívás létrehozásához és indításához használja az egyik API-t a-ban, és adjon meg egy, a Communication Services `callAgent` Identity SDK-val létrehozott felhasználót.

A hívás létrehozása és az indítás szinkron. A hívási példány lehetővé teszi az események hívásának feliratkozását.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>1:n hívás a felhasználónak vagy PSTN-nek

Ha egy másik Communication Services, használja a metódust, és adja át a felügyeleti könyvtárral létrehozott `startCall` `callAgent` `CommunicationUserIdentifier` [címzettek Communication Services számára.](../../access-tokens.md)

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Nyilvános telefonhálózatra (PSTN) való híváshoz használja a metódust, és adja át a `startCall` `callAgent` címzettnek a `PhoneNumberIdentifier` metódust. A Communication Services erőforrást úgy kell konfigurálni, hogy engedélyezze a PSTN-hívásokat.

PSTN-szám hívkor adja meg a másodlagos hívóazonosítóját. A másik hívóazonosító egy telefonszám (az E.164 szabvány alapján), amely azonosítja a hívót egy PSTN-hívásban. Ezt a telefonszámot látja a hívás címzettje a bejövő híváshoz.

> [!NOTE]
> A PSTN-hívás jelenleg privát előzetes verzióban érhető el. A hozzáféréshez [alkalmazza a korai befogadó programot.](https://aka.ms/ACS-EarlyAdopter)

1:1-es híváshoz használja a következő kódot:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

1:n híváshoz használja a következő kódot:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>1:1-es hívás videokamerával

> [!IMPORTANT]
> Jelenleg nem lehet egynél több kimenő helyi videóstream.

Videóhíváshoz számba kell veszi a helyi kamerákat a `getCameras()` `deviceManager` metódusával.

Miután kiválasztott egy kamerát, a használatával építsen ki egy `LocalVideoStream` példányt. Adja át a `videoOptions` metódusnak elemként a `localVideoStream` `startCall` tömbben.


```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Amikor a hívás csatlakozik, automatikusan elindítja egy videóstream küldését a kiválasztott kamerából a másik résztvevőnek. Ez a `Call.Accept()` videóbeállításokra és a `CallAgent.join()` videóbeállításokra is vonatkozik.

### <a name="join-a-group-call"></a>Csatlakozás csoportos híváshoz

> [!NOTE]
> A paraméter rendszer-metaadatoknak minősül, és a Microsoft a rendszer futtatásához szükséges műveletekhez `groupId` használható. Személyes adatokat ne foglaljon bele az `groupId` értékbe. A Microsoft ezt a paramétert nem személyes adatként kezeli, és a tartalma látható lehet a Microsoft alkalmazottai számára, vagy hosszú távon tárolható.
>
> A `groupId` paraméterhez az adatokNAK GUID formátumúnak kell lennie. Javasoljuk, hogy véletlenszerűen generált GUID azonosítókat használ, amelyek nem számítanak a rendszerekben személyes adatoknak.
>

Egy új csoporthíváshoz vagy egy folyamatban lévő csoporthíváshoz való csatlakozáshoz használja a metódust, és adja át az objektumot `join` egy `groupId` tulajdonsággal. Az `groupId` értéknek GUID-nak kell lennie.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Csatlakozás Teams-értekezlethez
> [!NOTE]
> Ez az API fejlesztői előzetes verzióként szolgál, és a kapott visszajelzések alapján változhat. Ne használja ezt az API-t éles környezetben. Az API a webes SDK-t hívó ACS bétaverziós kiadásának használatával használható

Egy Teams-értekezlethez való csatlakozáshoz használja a metódust, és adja át az értekezlet `join` hivatkozását vagy egy értekezlet koordinátáit.

Csatlakozás értekezlet-hivatkozással:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Csatlakozás értekezletkoordinátákkal:

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

A példány egy eseményt bocsát ki, amikor a bejelentkezett identitás `callAgent` bejövő hívást `incomingCall` fogad. Ha figyelni szeretne erre az eseményre, iratkozzon fel az alábbi lehetőségek egyikének használatával:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 
    // Get incoming call ID
    var incomingCallId = incomingCall.id
    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

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

Az `incomingCall` esemény tartalmaz egy olyan `incomingCall` példányt, amely elfogadhat vagy elutasíthat.

## <a name="manage-calls"></a>Hívások kezelése

A hívás során hozzáférhet a hívás tulajdonságaihoz, és kezelheti a videó- és hangbeállításokat.

### <a name="check-call-properties"></a>A hívás tulajdonságainak ellenőrzése

Egy hívás egyedi azonosítójának (sztringének) lehívása:

   ```js
    const callId: string = call.id;
   ```
A hívásra vonatkozó információk lehívása:
> [!NOTE]
> Ez az API fejlesztői előzetes verzióként szolgál, és a kapott visszajelzések alapján változhat. Ne használja ezt az API-t éles környezetben. Az API a webes SDK-t hívó ACS bétaverziós kiadását használja
   ```js
   const callInfo = call.info;
   ```

A hívás más résztvevőinek megismeréséhez vizsgálja meg a `remoteParticipants` gyűjteményt a "hívás" példányon:

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Egy bejövő hívás hívója:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` az egyik `CommunicationIdentifier` típus.

A hívás állapotának lehívása:

   ```js
   const callState = call.state;
   ```

   Ez visszaad egy sztringet, amely a hívás aktuális állapotát ábrázolja:

  - `None`: Kezdeti hívási állapot.
  - `Connecting`: Kezdeti átváltási állapot a hívás elhelyezve vagy elfogadva.
  - `Ringing`: Kimenő hívás esetében a azt jelzi, hogy egy hívás a távoli résztvevők számára cseng. Ez az `Incoming` ő oldalukon van.
  - `EarlyMedia`: Azt az állapotot jelzi, amelyben a híváshoz való csatlakozás előtt egy bejelentés lejátszódott.
  - `Connected`: Azt jelzi, hogy a hívás csatlakoztatva van.
  - `LocalHold`: Azt jelzi, hogy a hívást egy helyi résztvevő függtette fel. Nem folyik média a helyi végpont és a távoli résztvevők között.
  - `RemoteHold`: Azt jelzi, hogy a hívást távoli résztvevő függtette fel. Nem folyik média a helyi végpont és a távoli résztvevők között.
  - `InLobby`: Azt jelzi, hogy a felhasználó az előtérben van.
  - `Disconnecting`: Állapotváltás a hívás állapotát `Disconnected` megelőzően.
  - `Disconnected`: Végső hívás állapota. Ha a hálózati kapcsolat megszakad, az állapot két perc múlva `Disconnected` állapotra változik.

A tulajdonság vizsgálatán derítse ki, miért ért véget egy `callEndReason` hívás:

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

A tulajdonság vizsgálatán megtudhatja, hogy az aktuális hívás bejövő vagy `direction` kimenő. A értéket adja `CallDirection` vissza.

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Ellenőrizze, hogy az aktuális mikrofon el van-e némítva. A értéket adja `Boolean` vissza.

   ```js
   const muted = call.isMuted;
   ```

Ellenőrizze a tulajdonságot, hogy a képernyőmegosztási stream egy adott végpontról `isScreenSharingOn` van-e elküldve. A értéket adja `Boolean` vissza.

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Vizsgálja meg az aktív videóstreameket a gyűjtemény `localVideoStreams` ellenőrzéséhez. Objektumokat `LocalVideoStream` ad vissza.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```




### <a name="mute-and-unmute"></a>Némítás és némítás némításának némítása

A helyi végpont elnémításhoz vagy némításának feloldáshoz használhatja `mute` az és `unmute` aszinkron API-kat:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Helyi videó küldésének elkezdését és küldésének leállítását

A videókhoz a objektum metódusával kell enumerálnia `getCameras` a `deviceManager` kamerákat. Ezután hozzon létre egy új példányt a kívánt kamerával, majd adja át az `LocalVideoStream` `LocalVideoStream` objektumot a `startVideo` metódusnak:

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

A videóküldés sikeres elkezdését követően a rendszer hozzáad egy példányt a gyűjteményhez `LocalVideoStream` `localVideoStreams` egy híváspéldányon.

```js
call.localVideoStreams[0] === localVideoStream;
```

A helyi videó leállításhoz adja át a gyűjteményben elérhető `localVideoStream` `localVideoStreams` példányt:

```js
await call.stopVideo(localVideoStream);
```

A videó küldése közben átválthat egy másik kameraeszközre, ha egy példányra `switchSource` inkoktál: `localVideoStream`

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>Távoli résztvevők kezelése

Minden távoli résztvevő típus szerint van `RemoteParticipant` ábrázolva, és egy híváspéldány `remoteParticipants` gyűjteményében érhető el.

### <a name="list-the-participants-in-a-call"></a>Egy hívás résztvevőinek felsorolása

A `remoteParticipants` gyűjtemény egy hívás távoli résztvevőinek listáját adja vissza:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Távoli résztvevő tulajdonságainak elérése

A távoli résztvevők társított tulajdonságokkal és gyűjteményekkel rendelkeznek:

- `CommunicationIdentifier`: Szerezze be egy távoli résztvevő azonosítóját. Az identitás az egyik `CommunicationIdentifier` típus:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  A következő típusok egyike `CommunicationIdentifier` lehet:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Az ACS-felhasználót képviselő objektum.
  - `{ phoneNumber: '<E.164>' }`: A telefonszámot E.164 formátumban képviselő objektum.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: A Teams-felhasználót képviselő objektum.
  - `{ id: string }`: a többi azonosítótípushoz nem illeszkedő objektum-ismétlődő azonosító

- `state`: Egy távoli résztvevő állapotának lekért állapota.

  ```js
  const state = remoteParticipant.state;
  ```

  Az állapot a következő lehet:

  - `Idle`: Kezdeti állapot.
  - `Connecting`: Állapotváltás, amíg egy résztvevő csatlakozik a híváshoz.
  - `Ringing`: A résztvevő csenget.
  - `Connected`: A résztvevő csatlakozik a híváshoz.
  - `Hold`: A résztvevő fel van függve.
  - `EarlyMedia`: Közlemény, amely akkor játszik szerepet, amikor egy résztvevő csatlakozik a híváshoz.
  - `InLobby`: Azt jelzi, hogy a távoli résztvevő a számítógépben van.
  - `Disconnected`: Végső állapot. A résztvevő nem csatlakozik a híváshoz. Ha a távoli résztvevő elveszíti hálózati kapcsolatát, az állapota két perc után `Disconnected` állapotra változik.

- `callEndReason`: Ha meg szeretne tudni arról, hogy egy résztvevő miért távozott a hívástól, ellenőrizze a `callEndReason` tulajdonságot:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` status: Annak ellenőrzéshez, hogy egy távoli résztvevő el van-e némítva, ellenőrizze a `isMuted` tulajdonságot. A értéket adja `Boolean` vissza.

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` status: Ha meg kell tudni, hogy egy távoli résztvevő beszél-e, ellenőrizze a `isSpeaking` tulajdonságot. A értéket adja `Boolean` vissza.

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Az adott résztvevő által a hívásban küldött összes videóstream vizsgálatéhez ellenőrizze a `videoStreams` gyűjteményt. Objektumokat `RemoteVideoStream` tartalmaz.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: A távoli résztvevő megjelenített nevének lekért megjelenítéséhez vizsgálja meg `displayName` a tulajdonságot, amely sztringet ad vissza. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Résztvevő hozzáadása egy híváshoz

Ha egy résztvevőt (egy felhasználót vagy egy telefonszámot) szeretne hozzáadni egy híváshoz, használhatja a `addParticipant` et. Adja meg az egyik `Identifier` típust. Szinkron módon adja vissza a `remoteParticipant` példányt. A Hívás esemény akkor történik, amikor egy résztvevő sikeresen hozzáadódik a `remoteParticipantsUpdated` híváshoz.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Résztvevő eltávolítása egy hívásból

Ha el szeretne távolítani egy résztvevőt (egy felhasználót vagy egy telefonszámot) egy hívásból, meghívhatja a `removeParticipant` metódust. Az egyik típust meg kell `Identifier` adni. Ez aszinkron módon oldódik fel, miután a résztvevőt eltávolították a hívásból. A résztvevő is el lesz távolítva a `remoteParticipants` gyűjteményből.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Távoli résztvevő videóstreamek renderelése

A távoli résztvevők videóstreamek és képernyőmegosztási streamek felsorolásához vizsgálja meg a `videoStreams` gyűjteményeket:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

A `RemoteVideoStream` megjelenítéséhez elő kell fizetni az `isAvailableChanged` eseményére. Ha a `isAvailable` tulajdonság a következőre változik: `true` , egy távoli résztvevő streamet küld. Ezt követően hozzon létre egy új -példányt, majd hozzon létre egy új példányt `VideoStreamRenderer` `VideoStreamRendererView` az aszinkron `createView` metódussal.  Ezt követően bármely felhasználói `view.target` felületi elemhez csatolhat.

Ha egy távoli stream rendelkezésre állása megváltozik, dönthet úgy, hogy megsemmisíti a teljes , egy adott folyamot, vagy megtartja őket, de ez üres videókeret `VideoStreamRenderer` `VideoStreamRendererView` megjelenítését eredményezi.

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

### <a name="remote-video-stream-properties"></a>Távoli videóstream tulajdonságai

A távoli videóstreamek a következő tulajdonságokkal rendelkeznek:

- `id`: Egy távoli videóstream azonosítója.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: Lehet vagy `Video` `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Azt határozza meg, hogy egy távoli résztvevő végpontja aktívan küld-e streamet.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>VideoStreamRenderer metódusok és tulajdonságok
Hozzon létre egy példányt, amely csatolható az alkalmazás felhasználói felületén a távoli videóstream renderelése és aszinkron metódus használata esetén, amikor a stream készen áll a renderelésre, és egy objektumot ad vissza olyan tulajdonsággal, amely a `VideoStreamRendererView` `createView()` `target` `video` DOM-fában bárhol hozzáfűzhető elemet képvisel

  ```js
  videoStreamRenderer.createView()
  ```

A és `videoStreamRenderer` az összes társított példány `VideoStreamRendererView` eldobás:

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>VideoStreamRendererView metódusok és tulajdonságok

A létrehozásakor `VideoStreamRendererView` megadhatja a és a `scalingMode` `isMirrored` tulajdonságot. `scalingMode` A lehet `Stretch` , `Crop` vagy `Fit` . Ha `isMirrored` meg van adva, a renderelt stream függőlegesen tükrözve lesz.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Minden `VideoStreamRendererView` példány rendelkezik egy `target` tulajdonságokkal, amelyek a renderelési felületet jelölik. Csatolja ezt a tulajdonságot az alkalmazás felhasználói felületén:

```js
htmlElement.appendChild(view.target);
```

A frissítést a `scalingMode` metódussal `updateScalingMode` frissítheti:

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Eszközfelügyelet

A-ban számba veszi a helyi eszközöket, amelyek hívásokkal továbbíthatják a hang- és `deviceManager` videostreameket. Arra is használhatja, hogy engedélyt kérjen a helyi eszköz mikrofonja és kamerái elérésére.

A metódus `deviceManager` hívásával férhet `callClient.getDeviceManager()` hozzá:

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Helyi eszközök be- és lekért

A helyi eszközök eléréséhez enumerációs metódusokat használhat a `deviceManager` oldalon. Az enumerálás aszinkron művelet

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Az alapértelmezett mikrofon és beszélő beállítása

A-ban beállíthatja a híváshoz használt `deviceManager` alapértelmezett eszközt. Ha az ügyfél alapértelmezései nincsenek beállítva, a Communication Services az operációs rendszer alapértelmezését használja.

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

### <a name="local-camera-preview"></a>Helyi kamera előnézete

A és a használatával megkezdheti a helyi kamerából `deviceManager` `VideoStreamRenderer` származó streamek renderelését. Ez a stream nem lesz elküldve a többi résztvevőnek; ez egy helyi előzetes verziójú hírcsatorna.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Kamera- és mikrofonengedély kérése

Felhasználó kérése kamera- és mikrofonengedélyek megadására:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Ez egy objektummal oldódik fel, amely jelzi, hogy a és az `audio` `video` engedély meg van-e adni:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Hívások rekordja
> [!NOTE]
> Ez az API fejlesztői előzetes verzióként szolgál, és a kapott visszajelzések alapján változhat. Ne használja ezt az API-t éles környezetben. Az API használatának lépéseként használja az ACS hívó Web SDK bétaverziós kiadását

A hívásrögzítés a fő API kibővített `Call` funkciója. Először be kell szereznie a rögzítési funkció API-objektumát:

```js
const callRecordingApi = call.api(Features.Recording);
```

Ezután annak ellenőrzéshez, hogy a hívás rögzítve van-e, vizsgálja meg `isRecordingActive` a `callRecordingApi` tulajdonságát. A értéket adja `Boolean` vissza.

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
> [!NOTE]
> Ez az API fejlesztői előzetes verzióként szolgál, és a kapott visszajelzések alapján változhat. Ne használja ezt az API-t éles környezetben. Az API a webes SDK-t hívó ACS bétaverziós kiadását használja

A hívásátvitel a fő API kibővített `Call` funkciója. Először be kell szereznie az átviteli szolgáltatás API-objektumát:

```js
const callTransferApi = call.api(Features.Transfer);
```

A hívásátadások három felet érintnek:

- *Transferor*: Az átadási kérelmet elindítani felelős személy.
- *Átvitt:* Az átvitt személy.
- *Átadási cél:* Az a személy, akinek az átadása folyamatban van.

Az átvitelek a következő lépéseket követik:

1. Az átvevő és az  átvevő között már van *egy csatlakoztatott hívás.* Az *átvevő úgy* dönt,  hogy a hívást az átvevőtől az átadási cél *felé továbbítja.*
1. A *továbbító az* `transfer` API-t hívja meg.
1. Az *átvevő egy* esemény használatával dönti el, hogy az átadási cél felé vagy felé `accept` `reject` továbbítja-e a  `transferRequested` kérést.
1. Az *átviteli cél* csak akkor fogad bejövő hívást, ha az átvevő elfogadja az átadási kérelmet. 

Az aktuális hívás átviteléhez használhatja az `transfer` API-t. `transfer` A az opcionális `transferCallOptions` jelölőt veszi fel, amely lehetővé teszi egy jelző `disableForwardingAndUnanswered` beállítását:

- `disableForwardingAndUnanswered = false`: Ha az *átviteli cél* nem válaszol az átadási  hívásra, az átvitel követi az átviteli céltovábbozási és a megválaszolatlan beállításokat.
- `disableForwardingAndUnanswered = true`: Ha az *átviteli cél* nem válaszol az átadási hívásra, az átviteli kísérlet véget ér.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Az `transfer` API-val feliratkozhat a és `transferStateChanged` `transferRequested` eseményekre. Az `transferRequested` esemény egy példányból származik; egy eseményből és egy átvitelből, valamint egy `call` `transferStateChanged` `state` `error` `transfer` példányból.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

Az *átvevő elfogadhatja* vagy elutasíthatja  az átadási kérelmet, amelyet az átadás kezdeményez az eseményben a vagy a `transferRequested` `accept()` `reject()` `transferRequestedEventArgs` használatával. Az információit `targetParticipant` és a `accept` metódusokat a `reject` következőben tudja `transferRequestedEventArgs` elérni: .

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

## <a name="learn-about-eventing-models"></a>További információ az eseménymodellekről

Vizsgálja meg az aktuális értékeket, és iratkozzon fel az események későbbi értékeinek frissítésére.

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
