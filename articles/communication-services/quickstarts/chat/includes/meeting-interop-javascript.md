---
title: Gyors útmutató – csapatokhoz való csatlakozás
author: askaur
ms.author: askaur
ms.date: 02/17/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0c5ff52e5d3769124cd101b2483f18aea6963d25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750188"
---
## <a name="joining-the-meeting-chat"></a>Az értekezlet csevegésének csatlakoztatása 

A csapatok együttműködési képességének engedélyezése után a kommunikációs szolgáltatások felhasználója külső felhasználóként hívhatja a csapatokat a hívó ügyféloldali kódtár használatával. A híváshoz való csatlakozáskor a rendszer résztvevőként hozzáadja őket az értekezlet csevegéséhez, ahol üzeneteket küldhet és fogadhat a hívás más felhasználóival. A felhasználó nem férhet hozzá a híváshoz való csatlakozás előtt küldött csevegési üzenetekhez. Az értekezlethez való csatlakozáshoz és a csevegés megkezdéséhez kövesse a következő lépéseket.

## <a name="install-the-chat-packages"></a>A csevegési csomagok telepítése

A `npm install` parancs használatával telepítse a szükséges kommunikációs szolgáltatások ügyféloldali kódtárait a javascripthez.

```console
npm install @azure/communication-common --save

npm install @azure/communication-administration --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

A (z `--save` ) lehetőség a könyvtárat listázza a **package.js** fájlon belüli függőségként.

## <a name="add-the-teams-ui-controls"></a>A csapatok felhasználói felületi vezérlőinek hozzáadása

Cserélje le a kódot index.html-re az alábbi kódrészlettel.
A lap tetején található szövegmezők megadhatják a csapatok Értekezletének környezetét és az értekezlet szálának AZONOSÍTÓját. A "Join Teams Meeting" gomb a megadott értekezlethez való csatlakozáshoz lesz használva.
Az oldal alján megjelenik egy csevegési előugró ablak. Felhasználható üzenetek küldésére az értekezlet szálon, és valós időben jelenik meg a szálon küldött összes üzenet, amikor az ACS-felhasználó tagja.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## <a name="enable-the-teams-ui-controls"></a>A csapatok felhasználói felületének vezérlésének engedélyezése

Cserélje le a client.js fájl tartalmát a következő kódrészletre.

A kódrészleten belül cserélje le a 
- `SECRET CONNECTION STRING` a kommunikációs szolgáltatás kapcsolati karakterláncával 
- `ENDPOINT URL` a kommunikációs szolgáltatás végpontjának URL-címével

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-administration";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var userId = '';
var messages = '';

async function init() {
  const connectionString = "<SECRET CONNECTION STRING>";
  const endpointUrl = "<ENDPOINT URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  userId = identityResponse.communicationUserId;
  console.log(
    `\nCreated an identity with ID: ${identityResponse.communicationUserId}`
  );

  let tokenResponse = await identityClient.issueToken(identityResponse, [
    "voip",
    "chat",
  ]);
  const { token, expiresOn } = tokenResponse;
  console.log(
    `\nIssued an access token that expires at ${expiresOn}:`
  );
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationUserCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationUserCredential(token)
  );

  console.log('Azure Communication Chat client created!');
}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
  call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = messages;
  
  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new message notifications
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    
    if (e.sender.communicationUserId != userId) {
       renderReceivedMessage(e.content);
    }
    else {
       renderSentMessage(e.content);
    }
  });
  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderReceivedMessage(message) {
   messages += '<div class="container lighter">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

async function renderSentMessage(message) {
   messages += '<div class="container darker">' + message + '</div>';
   messagesContainer.innerHTML = messages;
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp();

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });

sendMessageButton.addEventListener("click", async () =>
  {
      let message = messagebox.value;

      let sendMessageRequest = { content: message };
      let sendMessageOptions = { senderDisplayName : 'Jack' };
      let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
      let messageId = sendChatMessageResult.id;

      messagebox.value = '';
      console.log(`Message sent!, message id:${messageId}`);
  });
```

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>A kommunikációs szolgáltatások felhasználóinak csevegési szálának beolvasása a csapatoknak

A Teams Meeting link és chat a Graph API-k használatával kérhető le, részletesen a [Graph dokumentációjában](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta). A kommunikációs szolgáltatások meghívója SDK egy teljes csapatot tárgyaló hivatkozást fogad el. Ezt a hivatkozást a `onlineMeeting` [Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta)-k [ `joinWebUrl` tulajdonság](/graph/api/resources/onlinemeeting?view=graph-rest-beta) alatt elérhető erőforrás részeként adja vissza a rendszer `threadId` . A válasz egy olyan objektummal fog rendelkezni `chatInfo` , amely tartalmazza a `threadID` . 

A Meeting Meeting Information (összevont **értekezlet** ) URL-címében is lekérheti a szükséges értekezleti információkat és a szál azonosítóját, ha a teams
A csapatok Értekezletének hivatkozása a következőképpen néz ki: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . A `threadId` lesz a `meeting_chat_thread_id` hivatkozás. A használata előtt győződjön meg arról, hogy az `meeting_chat_thread_id` nem érhető el. A következő formátumúnak kell lennie: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>A kód futtatása

A webpack felhasználói használhatják az `webpack-dev-server` alkalmazást az alkalmazás létrehozásához és futtatásához. Futtassa az alábbi parancsot az alkalmazás gazdagépének a helyi webkiszolgálón való megadásához:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Nyissa meg a böngészőt, és navigáljon a gombra http://localhost:8080/ . A következőnek kell megjelennie:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Képernyőkép a befejezett JavaScript-alkalmazásról.":::

Szúrja be a Teams Meeting hivatkozást és a szál AZONOSÍTÓját a szövegmezőbe. A Teams Meeting szolgáltatáshoz való csatlakozáshoz kattintson a *Csatlakozás csapatok Értekezlete* gombra. Miután az ACS-felhasználót bevezette az értekezletbe, a kommunikációt a kommunikációs szolgáltatások alkalmazásán belülről is elvégezheti. A csevegés megkezdéséhez navigáljon a lap alján található mezőbe.

> [!NOTE] 
> Jelenleg csak az üzenetek küldése, fogadása és szerkesztése támogatott a Teams együttműködési forgatókönyvek esetében. Más funkciók, mint például a kijelzők és a kommunikációs szolgáltatások felhasználói a Teams Meeting más felhasználóinak hozzáadásával vagy eltávolításával még nem támogatottak.  
