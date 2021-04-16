---
title: Rövid útmutató – Teams-értekezlethez csatlakozás webalkalmazásból
description: Ez az oktatóanyag bemutatja, hogyan csatlakozhat Teams-értekezlethez az Azure Communication Services Calling SDK for JavaScript használatával
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 6747d1d3cfba1c9e2bee7a8a7a48d67d6bed9f8e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564428"
---
Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat Teams-értekezlethez a JavaScripthez készült Azure Communication Services használatával.

## <a name="prerequisites"></a>Előfeltételek

- Egy működő [Communication Services, amely a webalkalmazást hívja meg.](../getting-started-with-calling.md)
- Egy [Teams-üzemelő példány.](/deployoffice/teams-install)


## <a name="add-the-teams-ui-controls"></a>A Teams felhasználói felület vezérlőinek hozzáadása

Cserélje le az l index.htma következő kódrészletre.
A szövegmezőben megadjuk a Teams-értekezlet környezetét, és a gomb használatával csatlakozunk a megadott értekezlethez:

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

## <a name="enable-the-teams-ui-controls"></a>A Teams felhasználói felületi vezérlőinek engedélyezése

Cserélje le a client.js tartalmát a következő kódrészletre.

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

## <a name="get-the-teams-meeting-link"></a>A Teams-értekezlet hivatkozásának lehívása

A Teams-értekezlet hivatkozása Graph API-k használatával olvasható be. Ennek részletes leírását a [Graph dokumentációjában találhatja](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)meg.
A Communication Services hívó SDK egy teljes Teams-értekezlethivatkozást fogad el. Ez a hivatkozás az erőforrás részeként lesz visszaadva, amely a tulajdonság alatt érhető el A szükséges értekezletadatokat a Join Meeting URL (Csatlakozás az értekezlet URL-címéhez) oldalon is lekérhetők a `onlineMeeting` Teams-értekezlet [ `joinWebUrl` ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) meghívásában. 

## <a name="run-the-code"></a>A kód futtatása

A Webpack felhasználói a használatával `webpack-dev-server` buildlik és futtatják az alkalmazást. Futtassa a következő parancsot az alkalmazásgazda helyi webkiszolgálón való kötegbe való kötegl akkorához:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Nyissa meg a böngészőt, és lépjen a következőre: http://localhost:8080/ . A következőnek kell megjelennie:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="A kész JavaScript-alkalmazás képernyőképe.":::

Szúrja be a Teams-környezetet a szövegmezőbe, majd nyomja le a *Join Teams Meeting* (Teams-értekezlethez való csatlakozás) gombot, hogy csatlakozzon a Teams-értekezlethez a Communication Services alkalmazásból.
