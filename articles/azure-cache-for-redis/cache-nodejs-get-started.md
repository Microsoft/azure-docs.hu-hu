---
title: 'Gyors útmutató: az Azure cache használata a Redis Node.js'
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure cache-t a Redis Node.js és node_redis használatával.
author: yegu-ms
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2018
ms.author: yegu
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: e4c58d67668a67eee38a73d46a2a40ca29c1dfd8
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121252"
---
# <a name="quickstart-use-azure-cache-for-redis-in-nodejs"></a>Gyors útmutató: az Azure cache használata a Redis Node.js

Ebben a rövid útmutatóban beépíti az Azure cache-t a Redis-ba egy Node.js alkalmazásba, hogy hozzáférjen egy olyan biztonságos, dedikált gyorsítótárhoz, amely az Azure-on belül bármely alkalmazásból elérhető.

## <a name="skip-to-the-code-on-github"></a>Ugrás a kódra a GitHubon

Ha közvetlenül a kódra szeretne ugrani, tekintse meg a következőt: [Node.js](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/nodejs) rövid útmutató a githubon.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [node_redis](https://github.com/mranney/node_redis), amelyet a paranccsal telepíthet `npm install redis` . 

Az egyéb Node.js-ügyfeleket használó példákért tekintse meg az egyes Node.js-ügyfelek dokumentációját a [Node.js Redis-ügyfeleket](https://redis.io/clients#nodejs) felsoroló weblapon.

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


Adjon hozzá környezeti változókat a **GAZDAGÉP NEVE** és **Elsődleges** hozzáférési kulcs megadásához. A kód ezen változóit fogja használni ahelyett, hogy közvetlenül a kódban tárolná a bizalmas információkat.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>Csatlakozás a gyorsítótárhoz

A legújabb buildek [node_redis](https://github.com/mranney/node_redis) biztosítanak támogatást az Azure cache-hez való csatlakozáshoz a TLS-t használó Redis. Az alábbi példa bemutatja, hogyan csatlakozhat az Azure cache-hez a Redis a 6380-es TLS-végpont használatával. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

Ne hozzon létre új kapcsolatokat a kódban szereplő összes művelethez. Ehelyett a lehető legtöbbször hasznosítsa újra a kapcsolatokat. 

## <a name="create-a-new-nodejs-app"></a>Új Node.js-alkalmazás létrehozása

Hozzon létre egy *redistest.js* nevű új szkriptfájlt. A parancs használatával `npm install redis bluebird` telepítse a szükséges csomagokat.

Adja hozzá a következő példa JavaScriptet a fájlhoz. Ez a kód azt mutatja be, hogyan csatlakozhat egy Azure cache-hez a Redis-példányhoz a gyorsítótár állomásneve és a legfontosabb környezeti változók használatával. A kód emellett tárolja és lekéri gyorsítótár egyik sztringértékét. A rendszer a `PING` és a `CLIENT LIST` parancsot is végrehajtja. További példák a Redis használatára a [node_redis](https://github.com/mranney/node_redis) ügyféllel: [https://redis.js.org/](https://redis.js.org/).

```js
var redis = require("redis");
var bluebird = require("bluebird");

// Convert Redis client API to use promises, to make it usable with async/await syntax
bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the TLS port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

Futtassa a szkriptet a Node.js-sel.

```
node redistest.js
```

Az alábbi példában a `Message` kulcsot láthatja. A kulcsnak korábban gyorsítótárazott értéke volt, amely az Azure Portalon, a Redis Console használatával lett beállítva. Az alkalmazás frissítette ezt a gyorsítótárazott értéket. Az alkalmazás továbbá végrehajtotta a `PING` és a `CLIENT LIST` parancsot.

![Redis Cache alkalmazás befejeződött](./media/cache-nodejs-get-started/redis-cache-app-complete.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja és újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ha azonban befejezte az oktatóanyag mintaalkalmazásának használatát, a díjak elkerülése érdekében törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
>

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

A **szűrés név alapján** szövegmezőbe írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *TestResources* nevű erőforráscsoportot használtak. Az erőforráscsoport az eredmény listán válassza a **...** , majd az **erőforráscsoport törlése** elemet.

![Azure-erőforráscsoport törlése](./media/cache-nodejs-get-started/redis-cache-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. Adja meg a megerősíteni kívánt erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja az Azure cache-t a Redis egy Node.js alkalmazásból. Folytassa a következő rövid útmutatóval, hogy az Azure cache-t használja a Redis egy ASP.NET-webalkalmazással.

> [!div class="nextstepaction"]
> [Hozzon létre egy ASP.NET-webalkalmazást, amely egy Azure cache-t használ a Redis.](./cache-web-app-howto.md)
