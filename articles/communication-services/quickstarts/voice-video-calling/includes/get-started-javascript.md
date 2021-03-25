---
title: Rövid útmutató – VOIP-hívás hozzáadása egy webalkalmazáshoz az Azure kommunikációs szolgáltatásokkal
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a JavaScripthez készült Azure kommunikációs szolgáltatásokat hívó SDK-t
author: ddematheu
ms.author: nimag
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0c70e01aa4f27e40a2de5cddf329cae9ffe261bc
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108277"
---
Ebből a rövid útmutatóból megtudhatja, hogyan indíthat el hívást a JavaScripthez készült Azure kommunikációs szolgáltatások meghívásával.

> [!NOTE]
> Ez a dokumentum a hívó SDK-ból származó 1.0.0-Beta. 10 verziót használja.


## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/) Aktív LTS-és karbantartási LTS-verziók (8.11.1 és 10.14.1 ajánlott).
- Aktív kommunikációs szolgáltatások erőforrása. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- Felhasználói hozzáférési jogkivonat a hívási ügyfél létrehozásához. Megtudhatja, hogyan [hozhat létre és kezelhet felhasználói hozzáférési jogkivonatokat](../../access-tokens.md).


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
      id="callee-id-input"
      type="text"
      placeholder="Who would you like to call?"
      style="margin-bottom:1em; width: 200px;"
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

Hozzon létre egy fájlt **client.js** nevű projekt gyökérkönyvtárában, hogy tartalmazza az alkalmazás logikáját ehhez a rövid útmutatóhoz. Adja hozzá az alábbi kódot a hívó ügyfél importálásához és a DOM-elemekre mutató hivatkozások beszerzéséhez, hogy az üzleti logikánk is csatolható legyen. 

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az Azure kommunikációs szolgáltatások Meghívási SDK-val kapcsolatos főbb funkcióit kezelik:

| Név                             | Leírás                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | A CallClient a hívó SDK fő belépési pontja.                                                                       |
| CallAgent                        | A CallAgent a hívások indításához és kezeléséhez használatos.                                                                                            |
| AzureCommunicationTokenCredential | A AzureCommunicationTokenCredential osztály az CommunicationTokenCredential felületet valósítja meg, amely a CallAgent létrehozásához használatos. |


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az `<USER_ACCESS_TOKEN>` erőforráshoz érvényes felhasználói hozzáférési tokent kell cserélnie. Ha még nem áll rendelkezésre jogkivonat, tekintse meg a [felhasználói hozzáférési jogkivonat](../../access-tokens.md) dokumentációját. A használatával `CallClient` inicializáljon egy `CallAgent` példányt egy példánnyal, `CommunicationTokenCredential` amely lehetővé teszi a hívások kezdeményezését és fogadását. Adja hozzá a következő kódot a **client.jshoz**:

```javascript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential);
    callButton.disabled = false;
}
init();
```

## <a name="start-a-call"></a>Hívás indítása

Eseménykezelő hozzáadásával kezdeményezheti a hívását, ha a `callButton` gombra kattint:

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

## <a name="end-a-call"></a>Hívás befejezése

Esemény-figyelő hozzáadása az aktuális hívás befejezéséhez, amikor a `hangUpButton` gombra kattint:

```javascript
hangUpButton.addEventListener("click", () => {
  // end the current call
  call.hangUp({ forEveryone: true });

  // toggle button states
  hangUpButton.disabled = true;
  callButton.disabled = false;
});
```

A `forEveryone` tulajdonság véget ér a hívás minden résztvevőnél.

## <a name="run-the-code"></a>A kód futtatása

Használja az `webpack-dev-server` alkalmazást az alkalmazás létrehozásához és futtatásához. Futtassa a következő parancsot az alkalmazás gazdagépének a helyi webkiszolgálón való megadásához:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Nyissa meg a böngészőt, és navigáljon a gombra http://localhost:8080/ . A következőnek kell megjelennie:

:::image type="content" source="../media/javascript/calling-javascript-app.png" alt-text="Képernyőkép a befejezett JavaScript-alkalmazásról.":::

A kimenő VOIP-hívást úgy teheti meg, hogy egy felhasználói azonosítót biztosít a szövegmezőben, és a **hívás indítása** gombra kattint. A Calling `8:echo123` egy echo-robottal csatlakozik, ez nagyszerű megoldás az első lépésekhez és a hangeszközök ellenőrzéséhez.
