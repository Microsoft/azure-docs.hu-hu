---
title: Rövid útmutató – VOIP-hívás hozzáadása egy webalkalmazáshoz a Azure Communication Services
description: Ez az oktatóanyag bemutatja, hogyan használhatja a Azure Communication Services Calling SDK for JavaScriptet
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: a93fe6c6203140bfed3771da8353ea7843b7694f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327454"
---
Ebből a rövid útmutatóból megtudhatja, hogyan indítható el egy hívás a Azure Communication Services Sdk for JavaScript használatával.

> [!NOTE]
> Ez a dokumentum a hívó SDK 1.0.0-beta.10-es verzióját használja.


## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Node.js](https://nodejs.org/) Active LTS és Maintenance LTS verziók (8.11.1 és 10.14.1 ajánlott).
- Egy aktív Communication Services erőforrás. [Hozzon létre egy Communication Services erőforrást.](../../create-communication-resource.md)
- Felhasználói hozzáférési jogkivonat a hívási ügyfél példányosíthoz. Megtudhatja, [hogyan hozhat létre és kezelhet felhasználói hozzáférési jogkivonatokat.](../../access-tokens.md)


[!INCLUDE [Calling with JavaScript](./get-started-javascript-setup.md)]

A kód a következő:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Communication Client - Calling Sample</title>
  </head>
  <body>
    <h4>Azure Communication Services</h4>
    <h1>Calling Quickstart</h1>
    <input 
      id="token-input"
      type="text"
      placeholder="User access token"
      style="margin-bottom:1em; width: 200px;"
    />
    </div>
    <button id="token-submit" type="button">
        Submit
    </button>
    <input 
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px; display: block;"
    />
    <div>
      <button id="call-button" type="button" disabled="true">
        Start Call
      </button>
      &nbsp;
      <button id="hang-up-button" type="button" disabled="true">
        Hang Up
      </button>
    </div>
    <script src="./bundle.js"></script>
  </body>
</html>
```

Hozzon létre egy fájlt a projekt gyökérkönyvtárában **client.js,** hogy tartalmazza a rövid útmutató alkalmazáslogikát. Adja hozzá a következő kódot a hívó ügyfél importálása és a DOM-elemek hivatkozásának lehívása az üzleti logika csatolása előtt. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
let userTokenCredential = "";
const userToken = document.getElementById("token-input");
const calleeInput = document.getElementById("callee-id-input");
const submitToken = document.getElementById("token-submit");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Objektummodell

Az alábbi osztályok és felületek a hívó SDK néhány fő funkcióját Azure Communication Services kezelik:

| Név                             | Leírás                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | A CallClient a hívó SDK fő belépési pontja.                                                                       |
| CallAgent (CallAgent)                        | A CallAgent hívásokat indít el és kezel.                                                                                            |
| AzureCommunicationTokenCredential | Az AzureCommunicationTokenCredential osztály a CommunicationTokenCredential felületet implementálja, amely a CallAgent példányának példányosítását használja. |


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A szövegmezőbe be kell írnunk egy érvényes felhasználói hozzáférési jogkivonatot az erőforráshoz, és a Küldés gombra kell kattintanunk. Ha még nem [rendelkezik](../../access-tokens.md) elérhető jogkivonattal, tekintse meg a felhasználói hozzáférési jogkivonat dokumentációját. A használatával inicializáljon egy példányt egy használatával, amely lehetővé teszi a `CallClient` `CallAgent` hívások `CommunicationTokenCredential` hívását és fogadását. Adja hozzá a következő kódot **aclient.js:**

```javascript
submitToken.addEventListener("click", async () => {
  const callClient = new CallClient(); 
  const userTokenCredential = userToken.value;
    try {
      tokenCredential = new AzureCommunicationTokenCredential(userTokenCredential);
      callAgent = await callClient.createCallAgent(tokenCredential);
      callButton.disabled = false;
      submitToken.disabled = true;
    } catch(error) {
      window.alert("Please submit a valid token!");
    }
})
```

## <a name="start-a-call"></a>Hívás elindítani

Adjon hozzá egy eseménykezelőt, amely hívást kezdeményez, amikor `callButton` a elemre kattint:

```javascript
callButton.addEventListener("click", () => {
    // start a call
    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ id: userToCall }],
        {}
    );
    // toggle button states
    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```

## <a name="end-a-call"></a>Hívás vége

Adjon hozzá egy esemény figyelőt az aktuális hívásának a hívásának a `hangUpButton` gombra való kattintása esetén:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
  submitToken.disabled = false;
});
```

A `forEveryone` tulajdonság minden hívási résztvevő számára befejezi a hívást.

## <a name="run-the-code"></a>A kód futtatása

A használatával `webpack-dev-server` készítse el és futtassa az alkalmazást. Futtassa a következő parancsot az alkalmazásgazda helyi webkiszolgálón való kötegl akkorához:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Nyissa meg a böngészőt, és navigáljon a következőre: http://localhost:8080/ . A következőnek kell megjelennie:

:::image type="content" source="../media/javascript/calling-javascript-app-2.png" alt-text="Az elkészült JavaScript-alkalmazás képernyőképe.":::

Kimenő VOIP-hívást úgy hívhat meg, hogy a szövegmezőben egy felhasználói azonosítót ad meg, majd a **Start Call (Hívás kezdete) gombra** kattint. A hívása egy echo robothoz csatlakoztatja. Ez nagyszerű az első lépésekhez és annak ellenőrzéséhez, hogy `8:echo123` a hangeszközök működnek-e.

## <a name="sample-code"></a>Mintakód

A mintaalkalmazást a [GitHubról töltheti le.](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-voice-calling)
