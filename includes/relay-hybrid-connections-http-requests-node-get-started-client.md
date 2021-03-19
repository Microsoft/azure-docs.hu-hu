---
title: fájl belefoglalása
description: fájl belefoglalása
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 3c18efa7eb520b765c9bb3c2aff00104f971f5a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179357"
---
### <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása

Ha letiltotta az „Ügyfél-hitelesítés szükséges” beállítást a továbbító létrehozásakor, akkor bármilyen böngészővel küldhet kérelmeket a hibrid kapcsolat URL-címére. A védett végpontok eléréséhez létre kell hoznia és továbbítania kell egy tokent a `ServiceBusAuthorization` fejlécben, az itt bemutatott módon.

Első lépésként hozzon létre egy `sender.js` nevű JavaScript-fájlt.

### <a name="add-the-relay-npm-package"></a>A Relay NPM-csomag hozzáadása

Futtassa az `npm install hyco-https` parancsot a projektmappában lévő Csomópont parancssorból. Ez a csomag a megszokott `https` csomagot is importálja. Ügyféloldalon a fő különbség az, hogy a csomag funkcionalitást kínál Relay URI-k és tokenek készítéséhez.

### <a name="write-some-code-to-send-messages"></a>Írjon egy kódrészletet üzenetek küldéséhez

1. Adja hozzá a következő `constants` utasítást a `sender.js` fájl elejéhez.
   
    ```js
    const https = require('hyco-https');
    ```

2. Adja hozzá a következő állandókat a `sender.js` fájlhoz a hibrid kapcsolat részleteivel. Cserélje le a zárójelben lévő helyőrzőket a hibrid gyűjtemény létrehozásakor beszerzett megfelelő értékekre.
   
   1. `const ns` – A Relay-névtér. Ügyeljen arra, hogy a teljes névtérnevet használja, például: `{namespace}.servicebus.windows.net`.
   2. `const path` – A hibrid kapcsolat neve.
   3. `const keyrule` – Az SAS-kulcs neve.
   4. `const key` – Az SAS-kulcs értéke.

3. Adja hozzá a következő kódot a `sender.js` fájlhoz. Láthatja, hogy a kód nem tér el jelentősen a normál Node.js HTTPS-ügyfelektől; egyszerűen csak hozzáad egy engedélyeztetési fejlécet.
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    A sender.js fájlnak így kell kinéznie:
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

