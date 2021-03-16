---
title: Rövid útmutató – videohívás hozzáadása az alkalmazáshoz (JavaScript)
titleSuffix: An Azure Communication Services quickstart
description: Ebből a rövid útmutatóból megtudhatja, hogyan adhat hozzá videó-Meghívási képességeket az alkalmazáshoz az Azure kommunikációs szolgáltatások használatával.
author: xumo-95
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 82f4d9028fa94d4df0ff089fda213d64e13d56ec
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487870"
---
# <a name="quickstart-add-11-video-calling-to-your-app-javascript"></a>Gyors útmutató: az alkalmazáshoz való 1:1-videohívás hozzáadása (JavaScript)

## <a name="download-code"></a>Kód letöltése

A rövid útmutatóhoz tartozó véglegesített kód megkeresése a [githubon](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling)

## <a name="prerequisites"></a>Előfeltételek
- Azure-fiók beszerzése aktív előfizetéssel. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/) Aktív LTS-és karbantartási LTS-verziók (8.11.1 és 10.14.1)
- Hozzon létre egy aktív kommunikációs szolgáltatások erőforrást. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp).
- Hozzon létre egy felhasználói hozzáférési jogkivonatot a hívási ügyfél létrehozásához. [Megtudhatja, hogyan hozhat létre és kezelhet felhasználói hozzáférési jogkivonatokat](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-csharp).

## <a name="setting-up"></a>Beállítás
### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása
Nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>A csomag telepítése
A `npm install` paranccsal telepítheti az Azure kommunikációs szolgáltatásokat a JavaScript-hez készült ügyféloldali kódtár meghívásával.

Ez a rövid útmutató az Azure-kommunikációt hívja meg az ügyféloldali kódtár használatával `1.0.0.beta-6` . 

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Ez a rövid útmutató a webpack használatával csomagolja ki az alkalmazási eszközöket. Futtassa a következő parancsot a és a `webpack` NPM-csomagok telepítéséhez, `webpack-cli` `webpack-dev-server` és sorolja fel fejlesztési függőségeiként a következőket `package.json` :

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
Hozzon létre egy `index.html` fájlt a projekt gyökérkönyvtárában. Ezt a fájlt fogjuk használni egy alapszintű elrendezés konfigurálásához, amely lehetővé teszi, hogy a felhasználó egy 1:1-es videohívás-hívást helyezzen.

A kód a következő:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

Hozzon létre egy fájlt a projekt gyökérkönyvtárában, `client.js` amely tartalmazza az alkalmazás logikáját ehhez a rövid útmutatóhoz. Adja hozzá az alábbi kódot a hívó ügyfél importálásához és a DOM-elemekre mutató hivatkozások beszerzéséhez.

```JavaScript
import { CallClient, CallAgent, Renderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>Objektummodell

Az alábbi osztályok és felületek az Azure kommunikációs szolgáltatások ügyfél-függvénytárának főbb funkcióit kezelik:

| Név      | Leírás | 
| :---        |    :----   |
| CallClient  | A CallClient a hívó ügyféloldali függvénytár fő belépési pontja.      |
| CallAgent  | A CallAgent a hívások indításához és kezeléséhez használatos.        |
| DeviceManager | A DeviceManager a média eszközök kezelésére szolgál.    |
| AzureCommunicationTokenCredential | A AzureCommunicationTokenCredential osztály az CommunicationTokenCredential felületet valósítja meg, amely a CallAgent létrehozásához használatos.        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>Az ügyfél hitelesítése és a DeviceManager elérése

Az erőforráshoz tartozó érvényes felhasználói hozzáférési jogkivonattal kell helyettesítenie <USER_ACCESS_TOKEN>. Ha még nem áll rendelkezésre jogkivonat, tekintse meg a felhasználói hozzáférési jogkivonat dokumentációját. A CallClient használatával inicializáljon egy CallAgent-példányt egy CommunicationUserCredential, amely lehetővé teszi a hívások kezdeményezését és fogadását. A DeviceManager eléréséhez először létre kell hozni egy callAgent-példányt. Ezután a példány metódusával kérheti le a következőt: `getDeviceManager` `CallClient` `DeviceManager` .

Szúrja be a következő kódot az `client.js` fájlba:

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}
init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>Helyezzen el egy 1:1 kimenő videohívás-hívást egy felhasználónak

Esemény-figyelő hozzáadásával kezdeményezheti a hívását, ha a `callButton` gombra kattint:

Először a deviceManager getCameraList API használatával kell enumerálnia a helyi kamerákat. Ebben a rövid útmutatóban a gyűjtemény első kameráját használjuk. A kívánt kamera kijelölése után a rendszer létrehoz egy LocalVideoStream-példányt, és átadja a videoOptions-en belül a localVideoStream-tömbben lévő elemnek a Call metódusnak. A hívása után a rendszer automatikusan elkezdi elküldeni a videót a másik résztvevőnek. 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
A megjelenítéséhez `LocalVideoStream` létre kell hoznia egy új példányt `Renderer` , majd létre kell hoznia egy új RendererView-példányt az aszinkron `createView` metódus használatával. Ezután csatolhat `view.target` bármilyen felhasználóifelület-elemet. 

```JavaScript
async function localVideoView() {
    rendererLocal = new Renderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
Az összes távoli résztvevő a `remoteParticipants` hívási példányon keresztül érhető el a gyűjteményen keresztül. Elő kell fizetnie az aktuális hívás távoli résztvevőinek, és figyelnie kell az eseményre, `remoteParticipantsUpdated` hogy feliratkozik a hozzáadott távoli résztvevőkre.

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.remoteParticipants.forEach( p => {
        subscribeToRemoteParticipant(p);
    })
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToRemoteParticipant(p);
        })
    });   
}
```
Előfizethet az `remoteParticipants` aktuális hívás gyűjteményére, és megvizsgálhatja a `videoStreams` gyűjteményeket az egyes résztvevői streamek listázásához. Az remoteParticipantsUpdated eseményre is elő kell fizetnünk a hozzáadott távoli résztvevők kezelésére. 

```JavaScript
function subscribeToRemoteParticipant(remoteParticipant) {
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
}
```
Elő kell fizetnie egy `isAvailableChanged` eseményre a megjelenítéséhez `remoteVideoStream` . Ha a `isAvailable` tulajdonság módosul `true` , a távoli résztvevő egy streamet küld. Ha a távoli adatfolyamok változásai elérhetők, eldöntheti, hogy az egészet `Renderer` , egy adott `RendererView` vagy megtartja-e, de ez az üres videó keretének megjelenítését eredményezi.
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
A megjelenítéséhez `RemoteVideoStream` létre kell hoznia egy új példányt `Renderer` , majd létre kell hoznia egy új `RendererView` példányt az aszinkron `createView` metódus használatával. Ezután csatolhat `view.target` bármilyen felhasználóifelület-elemet. 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new Renderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```
## <a name="receive-an-incoming-call"></a>Bejövő hívás fogadása
A bejövő hívások kezeléséhez meg kell hallgatni az `incomingCall` eseményt `callAgent` . A bejövő hívások után fel kell sorolnia a helyi kamerákat, és létre kell hoznia egy `LocalVideoStream` példányt, hogy a videó streamet küldjön a másik résztvevőnek. A `remoteParticipants` távoli videó streamek kezelésére is elő kell fizetnünk. A hívást elfogadhatja vagy elutasíthatja a `incomingCall` példányon keresztül. 

A bejövő hívások kezeléséhez helyezze a megvalósítást a alkalmazásban `init()` . 

```JavaScript
callAgent.on('incomingCall', async e => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    localVideoView();

    stopVideoButton.disabled = false;
    callButton.disabled = true;
    hangUpButton.disabled = false;

    const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
    call = addedCall;

    subscribeToRemoteParticipantInCall(addedCall);   
});
```
## <a name="end-the-current-call"></a>Az aktuális hívás befejezése
Esemény-figyelő hozzáadása az aktuális hívás befejezéséhez, amikor a `hangUpButton` gombra kattint:
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
## <a name="subscribe-to-call-updates"></a>Előfizetés a hívások frissítéseire
Elő kell fizetnie az eseményre, amikor a távoli résztvevő befejezi a hívást a videó-megjelenítő és a váltógomb állapotának eldobására. 

Az eseményre való előfizetéshez helyezze el az init () implementációját `callsUpdated` . 
 ```JavaScript 
callAgent.on('callsUpdated', e => {
    e.removed.forEach(removedCall => {
        // dispose of video renders
        rendererLocal.dispose();
        rendererRemote.dispose();
        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        stopVideoButton.disabled = true;
    })
})
```

## <a name="start-and-end-video-during-the-call"></a>Videó indítása és befejezése a hívás során
A videót leállíthatja az aktuális hívás során, ha hozzáad egy eseményt figyelőt a videó leállítása gombhoz, hogy eldobja a megjelenítőt `localVideoStream` . 
 ```JavaScript       
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
Egy esemény-figyelőt a videó indítása gombra kattintva adhat vissza a videó visszakapcsolásához, amikor az aktuális hívás során leállt. 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>A kód futtatása
Használja az `webpack-dev-server` alkalmazást az alkalmazás létrehozásához és futtatásához. Futtassa a következő parancsot az alkalmazás gazdagépének a helyi webkiszolgálón való megadásához:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Nyissa meg a böngészőt, és navigáljon a gombra http://localhost:8080/ . A következőnek kell megjelennie:

:::image type="content" source="./media/javascript/1-on-1-video-calling.png" alt-text="1 a videohívás oldalon":::

1:1 kimenő videohívás létrehozásához adjon meg egy felhasználói azonosítót a szövegmezőben, és kattintson a hívás indítása gombra. 

## <a name="sample-code"></a>Példakód
A mintaalkalmazást a [GitHubról](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling) töltheti le.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha törölni szeretné a kommunikációs szolgáltatások előfizetését, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek. További információ az [erőforrások tisztításáról](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp#clean-up-resources).

## <a name="next-steps"></a>Következő lépések
További információért tekintse át a következő cikkeket:
- Tekintse meg a [web Calling mintát](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Tudnivalók az [ügyféloldali kódtár képességeinek meghívásáról](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- További információ a [hívás működéséről](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)
