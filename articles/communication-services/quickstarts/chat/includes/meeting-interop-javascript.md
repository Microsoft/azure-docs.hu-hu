---
title: Rövid útmutató – Csatlakozás Teams-értekezlethez
author: askaur
ms.author: askaur
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 49f9bac40ae803f980a22c19fd5d44d85fa99e9e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107564558"
---
## <a name="joining-the-meeting-chat"></a>Csatlakozás a megbeszéléshez 

Ha a Teams-együttműködés engedélyezve van, Communication Services felhasználó külső felhasználóként csatlakozhat a Teams-híváshoz a Hívó SDK használatával. Ha csatlakozik a híváshoz, akkor az is résztvevőként lesz hozzáadva a megbeszéléshez, ahol üzeneteket küldhetnek és fogadhatnak más felhasználókkal a hívás során. A felhasználó nem fog hozzáférni a híváshoz való csatlakozás előtt küldött csevegési üzenetekhez. Az értekezlethez való csatlakozáshoz és a csevegéshez kövesse a következő lépéseket.

## <a name="install-the-chat-packages"></a>A csevegőcsomagok telepítése

A `npm install` JavaScripthez szükséges Communication Services az paranccsal.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

npm install @azure/communication-calling --save
```

A beállítás függőségként listázza a kódtárat apackage.js`--save` **fájlban.**

## <a name="add-the-teams-ui-controls"></a>A Teams felhasználói felület vezérlőinek hozzáadása

Cserélje le az l index.htma következő kódrészletre.
Az oldal tetején található szövegmezők a Teams-értekezlet környezetének és az értekezlet szálazonosítójának a beíratása során használhatók. A "Join Teams Meeting" (Csatlakozás a Teams-értekezlethez) gombbal csatlakozhat a megadott értekezlethez.
A lap alján megjelenik egy előugró csevegés. Használható üzenetek küldésére az értekezlet szálán, és valós időben fog megjeleníteni minden, a szálra küldött üzenetet, amíg az ACS-felhasználó tag.

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

## <a name="enable-the-teams-ui-controls"></a>A Teams felhasználói felületi vezérlőinek engedélyezése

Cserélje le a client.js fájl tartalmát a következő kódrészletre.

A kódrészleten belül cserélje le a helyére a következőt: 
- `SECRET CONNECTION STRING` a kommunikációs szolgáltatás kapcsolati sztringjával 
- `ENDPOINT URL` a kommunikációs szolgáltatás végponti URL-címével

```javascript
// run using
// npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-identity";
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

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Teams-értekezlet csevegési szálának lehívása egy Communication Services számára

A Teams-értekezlet hivatkozása és csevegése a Graph API-k használatával olvasható be, a [Graph dokumentációjában talál részletes információt.](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) A Communication Services hívó SDK egy teljes Teams-értekezlethivatkozást fogad el. Ez a hivatkozás az erőforrás részeként lesz visszaadva, amely a Graph API-k segítségével a tulajdonság alatt érhető el. A következőt `onlineMeeting` is beszerezheti: [ `joinWebUrl` ](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) [](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true) `threadId` . A válasz tartalmazni fog egy objektumot, amely tartalmazza a `chatInfo` `threadID` következőt: . 

A szükséges értekezletadatokat és szálazonosítót a Teams-értekezlet meghívásában található **Join Meeting** URL -címről is lekérte.
A Teams-értekezlet hivatkozása a következő: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . A `threadId` a `meeting_chat_thread_id` hivatkozásban található hely lesz. A használata előtt győződjön meg arról, `meeting_chat_thread_id` hogy a nincs beágyazva. A következő formátumban kell lennie: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## <a name="run-the-code"></a>A kód futtatása

A Webpack felhasználói a használatával `webpack-dev-server` buildlik és futtatják az alkalmazást. Futtassa a következő parancsot az alkalmazásgazda egy helyi webkiszolgálón való kötegbe csomagolához:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Nyissa meg a böngészőt, és navigáljon a következőre: http://localhost:8080/ . A következőnek kell megjelennie:

:::image type="content" source="../acs-join-teams-meeting-chat-quickstart.png" alt-text="Az elkészült JavaScript-alkalmazás képernyőképe.":::

Szúrja be a Teams-értekezlet hivatkozását és szálazonosítóját a szövegmezőkbe. A *Teams-értekezlethez való csatlakozáshoz nyomja* le a Join Teams Meeting (Csatlakozás a Teams-értekezlethez) gombot. Miután az ACS-felhasználót felvették az értekezletre, a saját alkalmazásában Communication Services beszélgethet. A csevegéshez lépjen az oldal alján található mezőbe.

> [!NOTE] 
> Jelenleg csak az üzenetek küldése, fogadása és szerkesztése támogatott a Teamsben való együttműködési forgatókönyvek esetében. Más funkciók, például a jelzők begépelése Communication Services felhasználók teams-értekezlethez való hozzáadását vagy eltávolítását még nem támogatják.  
