---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 322f54e4fa2e8096f68d5bbc216032a5b4e53c22
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726691"
---
## <a name="prerequisites"></a>Előfeltételek
Az első lépések előtt ügyeljen a következőre:

- Aktív előfizetéssel rendelkező Azure-fiók létrehozása. Részletekért tekintse meg a [fiók ingyenes létrehozását](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)ismertető témakört.
- Telepítse [Node.js](https://nodejs.org/en/download/) aktív LTS-és karbantartási LTS-verzióit.
- Hozzon létre egy Azure kommunikációs szolgáltatások erőforrást. További információ: [Azure kommunikációs erőforrás létrehozása](../../create-communication-resource.md). Ehhez a rövid útmutatóhoz fel kell **jegyeznie az erőforrás-végpontot** .
- Hozzon létre *három* ACS-felhasználót, és adja ki a felhasználói hozzáférési jogkivonat [felhasználói hozzáférési jogkivonatát](../../access-tokens.md). Ügyeljen arra, hogy a hatókört a **csevegés** értékre állítsa, és **jegyezze fel a jogkivonat karakterláncát, valamint a userId karakterláncot**. A teljes bemutató létrehoz egy szálat két kezdeti résztvevővel, majd felvesz egy harmadik résztvevőt a szálba.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-web-application"></a>Új Webalkalmazás létrehozása

Először nyissa meg a terminált vagy a parancssorablakot, hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir chat-quickstart && cd chat-quickstart
```

A futtatásával `npm init -y` **package.js** hozhat létre az alapértelmezett beállításokkal rendelkező fájlon.

```console
npm init -y
```

### <a name="install-the-packages"></a>A csomagok telepítése

A `npm install` parancs használatával telepítse az alábbi kommunikációs szolgáltatások SDK-kat a javascripthez.

```console
npm install @azure/communication-common --save

npm install @azure/communication-identity --save

npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save

```

A (z `--save` ) lehetőség a könyvtárat listázza a **package.js** fájlon belüli függőségként.

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Ez a rövid útmutató a webpack használatával csomagolja ki az alkalmazási eszközöket. Futtassa a következő parancsot a webpack, a webpack-CLI és a webpack-dev-Server NPM csomagok telepítéséhez, és sorolja fel azokat fejlesztési függőségeiként a **package.jsban**:

```console
npm install webpack webpack-cli webpack-dev-server --save-dev
```

Hozzon létre egy `webpack.config.js` fájlt a gyökérkönyvtárban. Másolja a következő konfigurációt ebbe a fájlba:

```
module.exports = {
  entry: "./client.js",
  output: {
    filename: "bundle.js"
  },
  devtool: "inline-source-map",
  mode: "development"
}
```

Adjon hozzá egy `start` szkriptet a `package.json` -hoz, ezt az alkalmazás futtatásához fogjuk használni. A `scripts` következő szakasza tartalmazza a `package.json` hozzáadását:

```
"scripts": {
  "start": "webpack serve --config ./webpack.config.js"
}
```

Hozzon létre egy **index.html** fájlt a projekt gyökérkönyvtárában. Ezt a fájlt sablonként használjuk a csevegési funkció hozzáadásához a JavaScripthez készült Azure kommunikációs csevegési SDK használatával.

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Chat Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Chat Quickstart</h1>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Hozzon létre egy fájlt **client.js** nevű projekt gyökérkönyvtárában, hogy tartalmazza az alkalmazás logikáját ehhez a rövid útmutatóhoz.

### <a name="create-a-chat-client"></a>Csevegési ügyfél létrehozása

Ha csevegési ügyfelet szeretne létrehozni a webalkalmazásban, használja a kommunikációs szolgáltatás **végpontját** és az előfeltételként szükséges lépések részeként létrehozott **hozzáférési jogkivonatot** .

A felhasználói hozzáférési tokenek lehetővé teszik olyan ügyfélalkalmazások összeállítását, amelyek közvetlenül az Azure kommunikációs szolgáltatásokban vannak hitelesítve. Ez a rövid útmutató nem vonatkozik a csevegési alkalmazás jogkivonatait kezelő szolgáltatási rétegek létrehozására. A hozzáférési jogkivonatokkal kapcsolatos további információkért tekintse meg a [csevegési](../../../concepts/chat/concepts.md) architektúrával és a [felhasználói hozzáférési jogkivonatokkal](../../access-tokens.md) kapcsolatos további információkat.

**client.js** belül az alábbi kódban a végpont és a hozzáférési token használatával adhat hozzá csevegési képességet a javascripthez készült Azure kommunikációs csevegési SDK használatával.

```JavaScript

import { ChatClient } from '@azure/communication-chat';
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

// Your unique Azure Communication service endpoint
let endpointUrl = 'https://<RESOURCE_NAME>.communication.azure.com';
// The user access token generated as part of the pre-requisites
let userAccessToken = '<USER_ACCESS_TOKEN>';

let chatClient = new ChatClient(endpointUrl, new AzureCommunicationTokenCredential(userAccessToken));
console.log('Azure Communication Chat client created!');
```
- Cserélje le az **endpointUrl** -t a kommunikációs szolgáltatások erőforrás-végpontra: [hozzon létre egy Azure kommunikációs erőforrást](../../create-communication-resource.md) , ha még nem tette meg.
- Cserélje le a **userAccessToken** elemet a kiállított jogkivonatra.


### <a name="run-the-code"></a>A kód futtatása

Futtassa a következő parancsot az alkalmazás gazdagépének a helyi webkiszolgálón való megadásához:
```console
npm run start
```
Nyissa meg a böngészőt, és navigáljon a gombra http://localhost:8080/ .
A böngészőben a fejlesztői eszközök konzolján a következőket kell látnia:

```console
Azure Communication Chat client created!
```

## <a name="object-model"></a>Objektummodell
A következő osztályok és felületek a JavaScripthez készült Azure Communication Services csevegő SDK főbb funkcióit kezelik.

| Név                                   | Leírás                                                                                                                                                                           |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ChatClient | Ez az osztály szükséges a csevegési funkciókhoz. Létrehozza azt az előfizetési adatokkal, és felhasználhatja a szálak létrehozásához, lekéréséhez és törléséhez. |
| ChatThreadClient | Ez az osztály szükséges a csevegési szál működéséhez. A példányokat a ChatClient keresztül szerezheti be, és használhatja az üzenetek küldésére/fogadására/frissítésére/törlésére, a felhasználók hozzáadására/eltávolítására/lekérésére, valamint a beírási értesítések küldésére és a beolvasási visszaigazolásokra |


## <a name="start-a-chat-thread"></a>Csevegési szál elindítása

`createThread`Csevegési szál létrehozásához használja a metódust.

`createThreadRequest` a szál kérelmének leírására szolgál:

- A használatával `topic` témakört adhat a csevegéshez. A témaköröket a funkció használatával lehet frissíteni, miután a csevegési szál létrejött `UpdateThread` .
- A használatával `participants` listázhatja a csevegési szálba felvenni kívánt résztvevőket.

Ha megoldotta, `createChatThread` a metódus a értéket adja vissza `CreateChatThreadResult` . Ez a modell egy olyan `chatThread` tulajdonságot tartalmaz, amely az `id` újonnan létrehozott szál elérésére használható. Ezután a használatával `id` kérheti le a egy példányát `ChatThreadClient` . A `ChatThreadClient` felhasználható a művelet végrehajtására a szálon belül, például üzenetek küldésére vagy listaelemek fogadására.

```JavaScript
async function createChatThread() {
  const createChatThreadRequest = {
    topic: "Hello, World!"
  };
  const createChatThreadOptions = {
    participants: [
      {
        id: '<USER_ID>',
        displayName: '<USER_DISPLAY_NAME>'
      }
    ]
  };
  const createChatTtreadResult = await chatClient.createChatThread(
    createChatThreadRequest,
    createChatThreadOptions
  );
  const threadId = createChatThreadResult.chatThread.id;
  return threadId;
}

createChatThread().then(async threadId => {
  console.log(`Thread created:${threadId}`);
  // PLACEHOLDERS
  // <CREATE CHAT THREAD CLIENT>
  // <RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>
  // <SEND MESSAGE TO A CHAT THREAD>
  // <LIST MESSAGES IN A CHAT THREAD>
  // <ADD NEW PARTICIPANT TO THREAD>
  // <LIST PARTICIPANTS IN A THREAD>
  // <REMOVE PARTICIPANT FROM THREAD>
  });
```

Amikor frissíti a böngésző lapját, a következőnek kell megjelennie a konzolon:
```console
Thread created: <thread_id>
```

## <a name="get-a-chat-thread-client"></a>Csevegési szál ügyfelének beolvasása

A `getChatThreadClient` metódus egy olyan `chatThreadClient` szálat ad vissza, amely már létezik. A létrehozott szálon végzett műveletek végrehajtásához használható: résztvevők hozzáadása, üzenet küldése stb. szálazonosító a meglévő csevegési szál egyedi azonosítója.

```JavaScript
let chatThreadClient = chatClient.getChatThreadClient(threadId);
console.log(`Chat Thread client for threadId:${threadId}`);

```
Vegye fel ezt a kódot a `<CREATE CHAT THREAD CLIENT>` Megjegyzés helyére a **client.js**, frissítse a böngésző fület, és ellenőrizze a konzolt, és tekintse meg a következőt:
```console
Chat Thread client for threadId: <threadId>
```

## <a name="list-all-chat-threads"></a>Az összes csevegési szál listázása

A `listChatThreads` metódus a `PagedAsyncIterableIterator` típust adja vissza `ChatThreadItem` . Az összes csevegési szál listázására használható.
Az a válasz, amely a `[ChatThreadItem]` szálak listázása után tért vissza.

```JavaScript
const threads = chatClient.listChatThreads();
for await (const thread of threads) {
   // your code here
}
```

## <a name="send-a-message-to-a-chat-thread"></a>Üzenet küldése csevegési szálnak

A `sendMessage` metódus használatával küldhet üzenetet a szálazonosító által azonosított szálnak.

`sendMessageRequest` az üzenet kérésének leírására szolgál:

- `content`A csevegési üzenet tartalmának megadásához használja a következőt:

`sendMessageOptions` a művelet opcionális paraméterei leírására szolgál:

- A használatával `senderDisplayName` adja meg a feladó megjelenítendő nevét;
- `type`Az üzenet típusának (például "text" vagy "HTML") megadására használható.

`SendChatMessageResult` az üzenet elküldésekor kapott válasz egy azonosítót tartalmaz, amely az üzenet egyedi azonosítója.

```JavaScript
const sendMessageRequest =
{
  content: 'Hello Geeta! Can you share the deck for the conference?'
};
let sendMessageOptions =
{
  senderDisplayName : 'Jack',
  type: 'text'
};
const sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
const messageId = sendChatMessageResult.id;
```

Vegye fel ezt a kódot a `<SEND MESSAGE TO A CHAT THREAD>` Megjegyzés helyére **client.js**, frissítse a böngésző fület, és tekintse meg a konzolt.
```console
Message sent!, message id:<number>
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Csevegési üzenetek fogadása csevegési szálból

A valós idejű jelzésekkel előfizethet az új bejövő üzenetek figyelésére, és ennek megfelelően frissítheti az aktuális üzeneteket a memóriában. Az Azure kommunikációs szolgáltatások olyan [események listáját támogatják, amelyek előfizethetnek a szolgáltatásra](../../../concepts/chat/concepts.md#real-time-notifications).

```JavaScript
// open notifications channel
await chatClient.startRealtimeNotifications();
// subscribe to new notification
chatClient.on("chatMessageReceived", (e) => {
  console.log("Notification chatMessageReceived!");
  // your code here
});

```
Adja hozzá ezt a kódot a `<RECEIVE A CHAT MESSAGE FROM A CHAT THREAD>` Megjegyzés helyén **client.js**.
Frissítse a böngésző fület, és a konzolon egy üzenetet kell látnia `Notification chatMessageReceived` .

Azt is megteheti, hogy a csevegési üzeneteket lekérdezi a `listMessages` metódus megadott időközönkénti lekérdezésével.

```JavaScript

const messages = chatThreadClient.listMessages();
for await (const message of messages) {
   // your code here
}

```
Adja hozzá ezt a kódot a `<LIST MESSAGES IN A CHAT THREAD>` Megjegyzés helyén **client.js**.
Frissítse a fület a konzolon, és keresse meg az ebben a csevegési szálban küldött üzenetek listáját.

`listMessages` a által azonosítható különböző típusú üzeneteket ad vissza `chatMessage.type` . 

További részletek: [üzenetek típusai](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Felhasználó felvétele a csevegési szálba résztvevőként

A csevegési szál létrehozása után hozzáadhat és eltávolíthat felhasználókat. A felhasználók hozzáadásával hozzáférést biztosíthat számukra, hogy üzeneteket küldjön a csevegési szálba, és további résztvevőket vegyen fel/távolítson el.

A metódus meghívása előtt `addParticipants` Győződjön meg arról, hogy új hozzáférési jogkivonatot és identitást szerzett az adott felhasználó számára. A felhasználónak szüksége lesz erre a hozzáférési jogkivonatra ahhoz, hogy inicializálja a csevegési ügyfelet.

`addParticipantsRequest` leírja a kérelem azon objektumát, amelyben a `participants` csevegési szálhoz hozzáadandó résztvevők szerepelnek.
- `id`, kötelező, a csevegési szálhoz hozzáadandó kommunikációs azonosító.
- `displayName`, nem kötelező, a szál résztvevő megjelenítendő neve.
- `shareHistoryTime`, nem kötelező, az az idő, amely alapján a csevegési előzmények megoszthatók a résztvevővel. Ha meg szeretné osztani a beszélgetési szál kezdete óta megjelenő előzményeket, állítsa ezt a tulajdonságot bármilyen dátumra vagy kevesebbre, mint a szál létrehozási ideje. Ha a résztvevő hozzáadását megelőzően meg szeretné osztani a korábbi előzményeket, állítsa azt az aktuális dátumra. A részleges előzmények megosztásához állítsa azt a választott dátumra.

```JavaScript

const addParticipantsRequest =
{
  participants: [
    {
      id: { communicationUserId: '<NEW_PARTICIPANT_USER_ID>' },
      displayName: 'Jane'
    }
  ]
};

await chatThreadClient.addParticipants(addParticipantsRequest);

```
**NEW_PARTICIPANT_USER_ID** cseréje [új felhasználói azonosítóval](../../access-tokens.md) adja hozzá ezt a kódot a Megjegyzés helyett `<ADD NEW PARTICIPANT TO THREAD>` **client.js**

## <a name="list-users-in-a-chat-thread"></a>Csevegési szál felhasználóinak listázása
```JavaScript
const participants = chatThreadClient.listParticipants();
for await (const participant of participants) {
   // your code here
}
```
Vegye fel ezt a kódot a `<LIST PARTICIPANTS IN A THREAD>` Megjegyzés helyére **client.js**, frissítse a böngésző fület, és ellenőrizze a konzolt, és tekintse meg a szál felhasználóiról szóló információkat.

## <a name="remove-user-from-a-chat-thread"></a>Felhasználó eltávolítása csevegési szálból

A résztvevők hozzáadásához hasonlóan a csevegési szálból is eltávolíthatja a résztvevőket. Az eltávolításhoz követnie kell a felvett résztvevők azonosítóit.

Használja a `removeParticipant` metódust, ahol `participant` a kommunikációs felhasználó el lesz távolítva a szálból.

```JavaScript

await chatThreadClient.removeParticipant({ communicationUserId: <PARTICIPANT_ID> });
await listParticipants();
```
Cserélje le a **PARTICIPANT_IDt** az előző lépésben használt felhasználói azonosítóra (<NEW_PARTICIPANT_USER_ID>).
Adja hozzá ezt a kódot a `<REMOVE PARTICIPANT FROM THREAD>` Megjegyzés helyén **client.js**,
