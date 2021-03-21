---
title: Gyors útmutató – csapatokhoz való csatlakozás egy webalkalmazásból
description: Ebből az oktatóanyagból megtudhatja, hogyan csatlakozhat a Teams Meeting szolgáltatáshoz az Azure kommunikációs szolgáltatások használatával, amely a JavaScript-ügyfél függvénytárát hívja meg.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 43f6241f0da0ecc9c68cf60e9f1a0482509374f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103487994"
---
Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat a Teams Meeting szolgáltatáshoz az Azure kommunikációs szolgáltatások használatával, amely az ügyféloldali függvénytárat hívja meg a JavaScripthez.

## <a name="prerequisites"></a>Előfeltételek

- Egy működő [kommunikációs szolgáltatás, amely webes alkalmazást hív](../getting-started-with-calling.md)meg.
- [Csapatok üzembe helyezése](/deployoffice/teams-install).


## <a name="add-the-teams-ui-controls"></a>A csapatok felhasználói felületi vezérlőinek hozzáadása

Cserélje le a kódot index.html-re az alábbi kódrészlettel.
A rendszer a Teams Meeting-környezet megadására fogja használni a szövegmezőt, és a gomb a megadott értekezlethez való csatlakozáshoz használható:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - Calling Sample</title>
</head>
<body>
    <h4>Azure Communication Services</h4>
    <h1>Teams meeting join quickstart</h1>
    <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
        <p><span style="font-weight: bold" id="recording-state"></span></p>
    <div>
        <button id="join-meeting-button" type="button" disabled="false">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
    </div>
    <script src="./bundle.js"></script>
</body>

</html>
```

## <a name="enable-the-teams-ui-controls"></a>A csapatok felhasználói felületének vezérlésének engedélyezése

Cserélje le client.js fájl tartalmát az alábbi kódrészlettel.

```javascript
import { CallClient } from "@azure/communication-calling";
import { Features } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');
const recordingStateElement = document.getElementById('recording-state');

async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'ACS user'});
    teamsMeetingJoinButton.disabled = false;
}
init();

hangUpButton.addEventListener("click", async () => {
    // end the current call
    await call.hangUp();
  
    // toggle button states
    hangUpButton.disabled = true;
    teamsMeetingJoinButton.disabled = false;
    callStateElement.innerText = '-';
  });

teamsMeetingJoinButton.addEventListener("click", () => {    
    // join with meeting link
    call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
    call.on('stateChanged', () => {
        callStateElement.innerText = call.state;
    })

    call.api(Features.Recording).on('isRecordingActiveChanged', () => {
        if (call.api(Features.Recording).isRecordingActive) {
            recordingStateElement.innerText = "This call is being recorded";
        }
        else {
            recordingStateElement.innerText = "";
        }
    });
    // toggle button states
    hangUpButton.disabled = false;
    teamsMeetingJoinButton.disabled = true;
});
```

## <a name="get-the-teams-meeting-link"></a>A Teams Meeting-hivatkozás beszerzése

A Teams Meeting hivatkozás a Graph API-k használatával kérhető le. Ez részletesen szerepel a [Graph dokumentációjában](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta).
A kommunikációs szolgáltatások meghívója SDK egy teljes csapatot tárgyaló hivatkozást fogad el. Ez a hivatkozás az erőforrás részeként `onlineMeeting` érhető el, a [ `joinWebUrl` tulajdonság](/graph/api/resources/onlinemeeting?view=graph-rest-beta) alatt elérhető módon, a Teams Meeting Meeting (összevont **értekezlet** ) URL-címéhez tartozó szükséges értekezlet-információkat is lekérheti.

## <a name="run-the-code"></a>A kód futtatása

A webpack felhasználói használhatják az `webpack-dev-server` alkalmazást az alkalmazás létrehozásához és futtatásához. Futtassa az alábbi parancsot az alkalmazás gazdagépének a helyi webkiszolgálón való megadásához:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Nyissa meg a böngészőt, és navigáljon a gombra http://localhost:8080/ . A következőnek kell megjelennie:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Képernyőkép a befejezett JavaScript-alkalmazásról.":::

Szúrja be a csapatok kontextusát a szövegmezőbe, majd nyomja meg a *Csatlakozás* a csapatok között lehetőséget a Teams Meeting szolgáltatáshoz való csatlakozáshoz a kommunikációs szolgáltatások alkalmazásából.
