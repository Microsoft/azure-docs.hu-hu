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
ms.openlocfilehash: fdd4e26839661ab9765b7d496e7f60c3686ba637
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "67179356"
---
### <a name="create-a-nodejs-application"></a>Node.js alkalmazás létrehozása

Hozzon létre egy `listener.js` nevű JavaScript-fájlt.

### <a name="add-the-relay-npm-package"></a>A Relay NPM-csomag hozzáadása

Futtassa az `npm install hyco-https` parancsot a projektmappában lévő Csomópont parancssorból.

### <a name="write-some-code-to-handle-requests"></a>Kód írása a kérelmek kezeléséhez

1. Adja hozzá a következő állandót a `listener.js` fájl elejéhez.

    ```js
    const https = require('hyco-https');
    ```
2. Adja hozzá a következő állandókat a `listener.js` fájlhoz a hibrid kapcsolat részleteivel. Cserélje le a zárójelben lévő helyőrzőket a hibrid gyűjtemény létrehozásakor beszerzett megfelelő értékekre.

   1. `const ns` – A Relay-névtér. Ügyeljen arra, hogy a teljes névtérnevet használja, például: `{namespace}.servicebus.windows.net`.
   2. `const path` – A hibrid kapcsolat neve.
   3. `const keyrule` – Az SAS-kulcs neve.
   4. `const key` – Az SAS-kulcs értéke.

3. Adja hozzá a következő kódot a `listener.js` fájlhoz. :

    Észreveheti, hogy a kód lényegében megegyezik a kezdő Node.js-oktatóanyagokban látható HTTP-kiszolgálópéldákkal, kivéve hogy a `createRelayedServer` függvényt használja a megszokott `createServer` függvény helyett.

    ```js
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```
    A listener.js fájlnak így kell kinéznie:
   
    ```js
    const https = require('hyco-https');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var uri = https.createRelayListenUri(ns, path);
    var server = https.createRelayedServer(
        {
            server : uri,
            token : () => https.createRelayToken(uri, keyrule, key)
        },
        (req, res) => {
            console.log('request accepted: ' + req.method + ' on ' + req.url);
            res.setHeader('Content-Type', 'text/html');
            res.end('<html><head><title>Hey!</title></head><body>Relayed Node.js Server!</body></html>');
        });

    server.listen( (err) => {
            if (err) {
              return console.log('something bad happened', err)
            }
            console.log(`server is listening on ${port}`)
          });

    server.on('error', (err) => {
        console.log('error: ' + err);
    });
    ```

