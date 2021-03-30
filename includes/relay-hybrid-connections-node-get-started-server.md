---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 531866ece1c4acacb926c9e9c6598158c1aa077f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "67179709"
---
### <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása

Hozzon létre egy `listener.js` nevű JavaScript-fájlt.

### <a name="add-the-relay-npm-package"></a>A Relay NPM-csomag hozzáadása

Futtassa az `npm install hyco-ws` parancsot a projektmappában lévő Csomópont parancssorból.

### <a name="write-some-code-to-receive-messages"></a>Írjon egy kódrészletet az üzenetek fogadásához

1. Adja hozzá a következő állandót a `listener.js` fájl elejéhez.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Adja hozzá a következő állandókat a `listener.js` fájlhoz a hibrid kapcsolat részleteivel. Cserélje le a zárójelben lévő helyőrzőket a hibrid gyűjtemény létrehozásakor beszerzett megfelelő értékekre.
   
   1. `const ns` – A Relay-névtér. Ügyeljen arra, hogy a teljes névtérnevet használja, például: `{namespace}.servicebus.windows.net`.
   2. `const path` – A hibrid kapcsolat neve.
   3. `const keyrule` – Az SAS-kulcs neve.
   4. `const key` – Az SAS-kulcs értéke.

3. Adja a következő kódot a `listener.js` fájlhoz:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    A listener.js fájlnak így kell kinéznie:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

