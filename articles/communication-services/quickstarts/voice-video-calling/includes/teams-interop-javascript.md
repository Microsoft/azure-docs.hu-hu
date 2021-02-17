---
title: Gyors útmutató – csapatokhoz való csatlakozás
author: chpalm
ms.author: mikben
ms.date: 10/10/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: fa7f279e13e5dbb59af7eb3a7939be1edf0f7d5d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551482"
---
## <a name="prerequisites"></a>Előfeltételek

- Egy működő [kommunikációs szolgáltatás, amely meghívja az alkalmazást](../getting-started-with-calling.md).
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
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const meetingLinkInput = document.getElementById('teams-link-input');
const hangUpButton = document.getElementById('hang-up-button');
const teamsMeetingJoinButton = document.getElementById('join-meeting-button');
const callStateElement = document.getElementById('call-state');

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
    
    call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
    })
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
