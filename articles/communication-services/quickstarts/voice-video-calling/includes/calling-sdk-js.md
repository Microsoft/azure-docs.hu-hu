---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 2c894ea4bcb9701b8b65bcb9cd0b4b82c1898448
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99500413"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Egy központilag telepített kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- A a `User Access Token` hívási ügyfél engedélyezéséhez. További információ a [beszerzéséről `User Access Token` ](../../access-tokens.md)
- Opcionális: fejezze be a gyors üzembe helyezési útmutatót az [alkalmazáshoz való hívás hozzáadásával](../getting-started-with-calling.md)

## <a name="setting-up"></a>Beállítás

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

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
| AzureCommunicationUserCredential | A AzureCommunicationUserCredential osztály az CommunicationUserCredential felületet valósítja meg, amely a CallAgent létrehozásához használatos. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>A CallClient inicializálása, a CallAgent létrehozása és a DeviceManager elérése

Új `CallClient` példány létrehozása. Konfigurálhatja az egyéni beállításokkal, például a Logger-példánnyal.
A példány `CallClient` létrehozása után létrehozhat egy `CallAgent` példányt a metódus meghívásával `createCallAgent` `CallClient` . Ez aszinkron módon ad vissza egy `CallAgent` példány objektumot.
A `createCallAgent` metódus `CommunicationUserCredential` argumentumként fogadja el a [felhasználói hozzáférési tokent](../../access-tokens.md).
A callAgent- `DeviceManager` példány eléréséhez először létre kell hozni. Ezután használhatja a `getDeviceManager` metódust a `CallClient` példányon a DeviceManager lekéréséhez.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationUserCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Kimenő hívás elhelyezése

Egy hívás létrehozásához és elindításához a CallAgent egyik API-t kell használnia, és meg kell adnia a kommunikációs szolgáltatások felügyeleti ügyféloldali kódtár használatával létrehozott felhasználót.

A hívás létrehozása és a kezdés szinkronban van. A hívási példány lehetővé teszi a hívási eseményekre való előfizetést.

## <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Helyezzen 1:1-hívást egy felhasználónak vagy 1: n hívásnak a felhasználók és a PSTN használatával

Ha egy másik kommunikációs szolgáltatást használó felhasználónak szeretne hívást kezdeményezni, hívja `call` meg a metódust, `callAgent` és adja át a [kommunikációs szolgáltatások felügyeleti könyvtárával létrehozott](../../access-tokens.md)CommunicationUser.

```js
const oneToOneCall = callAgent.call([CommunicationUser]);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>1: n hívás elhelyezése a felhasználók és a PSTN között

Ha 1: n hívást szeretne elhelyezni egy felhasználóhoz és egy PSTN-számhoz, meg kell adnia egy CommunicationUser és egy telefonszámot mindkét Calle esetében.

A kommunikációs szolgáltatások erőforrását úgy kell konfigurálni, hogy engedélyezze a PSTN-hívást.
```js

const userCallee = { communicationUserId: <ACS_USER_ID> };
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const groupCall = callAgent.call([userCallee, pstnCallee], placeCallOptions);

```

### <a name="place-a-11-call-with-video-camera"></a>1:1-hívás elhelyezése videokameráról
> [!WARNING]
> Jelenleg legfeljebb egy kimenő helyi videó stream lehet.
Videohívás létrehozásához a helyi kamerákat a deviceManager API használatával kell enumerálni `getCameraList` .
Miután kiválasztotta a kívánt kamerát, használja egy példány összeállításához, `LocalVideoStream` és a `videoOptions` tömbben lévő elemen belül adja át `localVideoStream` a `call` metódusnak.
A hívása után a rendszer automatikusan elindít egy video streamet a kiválasztott kamerából a többi résztvevő (k) felé.

Ez a Call. Accept () video Options és a CallAgent. JOIN () videó beállításaira is vonatkozik.
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="receiving-an-incoming-call"></a>Bejövő hívás fogadása
```js
callAgent.on('callsUpdated', e => {
    e.added.forEach(addedCall => {
        if(addedCall.isIncoming) {
        addedCall.accept();
    }
    });
})
```

### <a name="join-a-group-call"></a>Csatlakozás csoportos híváshoz
Új csoportos hívás indításához vagy egy folyamatos csoportos híváshoz való csatlakozáshoz használja a "Join" metódust, és adjon át egy objektumot egy `groupId` tulajdonsággal. Az értéknek GUID azonosítónak kell lennie.
```js

const locator = { groupId: <GUID>}
const call = callAgent.join(locator);

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

* A hívó identitása, ha a hívás beérkező. Az identitás az egyik `Identifier` típusa
```js

const callerIdentity = call.callerIdentity;

```

* A hívás állapotának beolvasása.
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
* "Hold" – a hívás megtartásra kerül, és a helyi végpont és a távoli résztvevő (k) között nem folyik adathordozó.
* "Kapcsolat bontása" – átmeneti állapot, mielőtt a hívás "leválasztott" állapotba kerül
* "Leválasztott" – végső hívás állapota.
   * Ha a hálózati kapcsolat megszakad, az állapot körülbelül 2 percet vesz igénybe.


* Ha meg szeretné tudni, hogy egy adott hívás miért ért véget, vizsgálja meg a `callEndReason` tulajdonságot.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Ha meg szeretné tudni, hogy az aktuális hívás bejövő hívás-e, ellenőrizze a `isIncoming` tulajdonságot, és adja vissza `Boolean` .
```js
const isIncoming = call.isIncoming;
```

* Annak ellenőrzéséhez, hogy a rendszer rögzíti-e a hívást, ellenőrizze a `isRecordingActive` tulajdonságot, és adja vissza `Boolean` .
```js
const isResordingActive = call.isRecordingActive;
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

### <a name="mute-and-unmute"></a>Némítás és némítás feloldása

A helyi végpont némításához vagy a némítás feloldásához használhatja a `mute` és az `unmute` aszinkron API-kat:

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Helyi videó küldésének elindítása és leállítása


A videók elindításához az objektum metódusának használatával kell enumerálnia a kamerákat `getCameraList` `deviceManager` . Ezután hozzon létre egy új példányt, amely `LocalVideoStream` argumentumként adja át a kívánt kamerát a `startVideo` metódusnak:


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
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```
### <a name="faq"></a>GYIK
 * Ha a hálózati kapcsolat megszakad, a hívás állapota "leválasztva" állapotúra változik?
    * Igen, ha a hálózati kapcsolat több mint 2 percet vesz igénybe, a hívás a leválasztott állapotra vált, és a hívás véget ért.

## <a name="remote-participants-management"></a>Távoli résztvevők kezelése

Az összes távoli résztvevő típus szerint jelenik `RemoteParticipant` meg, és a gyűjteményen keresztül érhető el a `remoteParticipants` hívási példányon.

### <a name="list-participants-in-a-call"></a>Hívás résztvevőinek listázása
A `remoteParticipants` gyűjtemény az adott hívásban a távoli résztvevők listáját adja vissza:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Távoli résztvevő tulajdonságai
A távoli résztvevő rendelkezik a hozzá társított tulajdonságok és gyűjtemények készletével

* A távoli résztvevő azonosítójának beolvasása.
Az identitás az "azonosító" típusok egyike:
```js
const identifier = remoteParticipant.identifier;
//It can be one of:
// { communicationUserId: '<ACS_USER_ID'> } - object representing ACS User
// { phoneNumber: '<E.164>' } - object representing phone number in E.164 format
```

* A távoli résztvevő állapotának beolvasása.
```js

const state = remoteParticipant.state;
```
Az állapot lehet az egyik
* "Tétlen" – kezdeti állapot
* "Connecting" – átmeneti állapot, miközben a résztvevő csatlakozik a híváshoz
* "Csatlakoztatott" – a résztvevő csatlakozik a híváshoz
* "Hold" – a résztvevő tart
* "EarlyMedia" – a bejelentést csak akkor játssza le a rendszer, ha a résztvevő csatlakozik a híváshoz
* "Leválasztott" – végső állapot – a résztvevő nem kapcsolódik a híváshoz.
   * Ha a távoli résztvevő elveszti hálózati kapcsolatát, akkor a távoli résztvevő állapota körülbelül 2 percet vesz igénybe.

Annak megismeréséhez, hogy a résztvevő miért hagyta el a hívást, vizsgálja meg a `callEndReason` tulajdonságot:
```js

const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Annak ellenőrzéséhez, hogy a távoli résztvevő el van-e némítva vagy sem, vizsgálja `isMuted` meg a tulajdonságot, és adja vissza `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```

* Annak ellenőrzéséhez, hogy a távoli résztvevő beszél-e vagy sem, vizsgálja meg a visszaadott `isSpeaking` tulajdonság értékét. `Boolean`
```js

const isSpeaking = remoteParticipant.isSpeaking;

```

* Az adott résztvevő által a hívásban küldött összes videó stream vizsgálatához tekintse `videoStreams` meg a gyűjteményt, amely `RemoteVideoStream` objektumokat tartalmaz
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Résztvevő hozzáadása egy híváshoz

Meghívhatja a résztvevőt egy híváshoz (egy felhasználóhoz vagy egy telefonszámhoz) `addParticipant` .
Adja meg az "azonosító" típusok egyikét.
Ez szinkron módon visszaküldi a távoli résztvevő példányát.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>};
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier);
```

### <a name="remove-participant-from-a-call"></a>Résztvevő eltávolítása egy hívásból

Ha el szeretne távolítani egy résztvevőt a hívásból (vagy egy felhasználóról vagy egy telefonszámról), akkor meghívhatja `removeParticipant` .
Az "azonosító" típusok egyikének kell átadnia, ez a megoldás aszinkron módon oldódik meg, ha a résztvevő el lesz távolítva a hívásból.
A résztvevő is el lesz távolítva a `remoteParticipants` gyűjteményből.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>};
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>A távoli résztvevő video streamek megjelenítése

A távoli résztvevők videó streamek és a képernyő-megosztási streamek listázásához vizsgálja meg a `videoStreams` gyűjteményeket:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
A megjelenítéséhez `RemoteVideoStream` elő kell fizetnie egy `isAvailableChanged` eseményre.
Ha a `isAvailable` tulajdonság módosul `true` , a távoli résztvevő egy streamet küld.
Ha ez megtörténik, hozzon létre egy új példányt `Renderer` , majd hozzon létre egy új `RendererView` példányt az aszinkron `createView` metódus használatával.  Ezután csatolhat `view.target` bármilyen felhasználóifelület-elemet.
Ha a távoli adatfolyamok változásai elérhetők, eldöntheti, hogy az egész renderelő, egy adott `RendererView` vagy megtartható, de ez az üres videó keretének megjelenítését eredményezi.

```js
let renderer: Renderer;
const displayVideo = () => {
    renderer = new Renderer(remoteParticipantStream);
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
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
const type: MediaStreamType = remoteVideoStream.type;
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
A létrehozásakor `RendererView` megadhatja a `scalingMode` és a `mirrored` tulajdonságokat.
A skálázási mód lehet "stretch", "Crop" vagy "fit", ha meg `Mirrored` van adva, a megjelenített adatfolyam függőlegesen lesz tükrözve.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
Minden megadott `RendererView` példány rendelkezik egy `target` tulajdonsággal, amely a renderelési felületet jelképezi. Ezt az alkalmazás felhasználói felületén kell csatolni:
```js
document.body.appendChild(rendererView.target);
```

A skálázási módot később is frissítheti a metódus meghívásával `updateScalingMode` .
```js
view.updateScalingMode('Crop')
```
### <a name="faq"></a>GYIK
* Ha egy távoli résztvevő elveszíti hálózati kapcsolatát, akkor az állapotuk a "leválasztott" állapotra vált?
    * Igen, ha egy távoli résztvevő több mint 2 percig elveszíti hálózati kapcsolatát, az állapota leválasztva állapotba kerül, és a rendszer eltávolítja a hívást.
## <a name="device-management"></a>Eszközfelügyelet

`DeviceManager` lehetővé teszi olyan helyi eszközök enumerálását, amelyek a hang-és video-adatfolyamok továbbítására szolgáló hívásokban használhatók. Azt is lehetővé teszi, hogy engedélyt kérjen a felhasználótól, hogy a natív böngésző API használatával hozzáférjen a mikrofonhoz és a fényképezőgéphez.

A `deviceManager` by Calling `callClient.getDeviceManager()` metódussal érheti el.
> [!WARNING]
> A `callAgent` DeviceManager való hozzáféréshez jelenleg egy objektumot kell létrehozni először.

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Helyi eszközök enumerálása

A helyi eszközökhöz való hozzáféréshez használhat enumerálási metódusokat a Eszközkezelő. A számbavétel egy szinkron művelet.

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Alapértelmezett mikrofon/hangszóró beállítása

Az Eszközkezelővel beállíthat egy alapértelmezett eszközt, amelyet a rendszer a hívás indításakor fog használni.
Ha az ügyfél alapértelmezései nincsenek beállítva, a kommunikációs szolgáltatások az operációs rendszer alapértelmezett értékeire kerülnek vissza.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceMicrophone.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Helyi kamera előzetes verziója

A `DeviceManager` és `Renderer` a segítségével megkezdheti a streamek megjelenítését a helyi kameráról. Ezt a streamet nem lehet elküldeni a többi résztvevőnek; Ez egy helyi előnézeti hírcsatorna. Ez egy aszinkron művelet.

```js
const localVideoDevice = deviceManager.getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Engedély kérése a kamera/mikrofon számára

Kérje meg a felhasználót, hogy adja meg a kamera/mikrofon engedélyeit a következőkkel:

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
Ez a megoldás aszinkron módon fog megjelenni egy olyan objektummal, amely megadja `audio` , hogy megadták-e az `video` engedélyeket:
```js
console.log(result.audio);
console.log(result.video);
```

Az adott típus aktuális engedélyezési állapotát a következő hívással ellenőrizheti `getPermissionState` :

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="eventing-model"></a>Eseményvezérelt modell

Előfizethet a legtöbb tulajdonságra és gyűjteményre, hogy értesítést kapjon az értékek változásakor.

### <a name="properties"></a>Tulajdonságok
Előfizetés az `property changed` eseményekre:

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>Gyűjtemények
Előfizetés az `collection updated` eseményekre:

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
