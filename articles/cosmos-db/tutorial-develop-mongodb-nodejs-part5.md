---
title: A szögletes alkalmazás összekötése Azure Cosmos DB API-MongoDB a mongúz használatával
description: Ez az oktatóanyag azt ismerteti, hogyan hozhat létre egy Node.js alkalmazást a szögletes és expressz használatával a Cosmos DB tárolt adatai kezeléséhez. Ebben a részben az mongúz használatával csatlakozik Azure Cosmos DBhoz.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: a3097fa539f460ef5e8ffe73598fa5d55516717e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097804"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Szögletes alkalmazás létrehozása Azure Cosmos DB API-jával a MongoDB-hoz való kapcsolódáshoz a mongúz használatával Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a többrészes oktatóanyag bemutatja, hogyan hozhat létre egy Node.js alkalmazást expressz és szögletes kapcsolattal, és hogyan kapcsolódhat hozzá a [Cosmos db API-MongoDB konfigurált Cosmos-fiókhoz](mongodb-introduction.md). Ez a cikk az oktatóanyag 5. részét ismerteti, és a [4. részre](tutorial-develop-mongodb-nodejs-part4.md)épül.

Az oktatóanyag ezen részében a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Cosmos DBhoz való kapcsolódáshoz használjon mongúzt.
> * Szerezze be Cosmos DB a kapcsolatok karakterláncát.
> * Hozza létre a Hero modellt.
> * Hozza létre a Hero szolgáltatást a Hero-adatlekérdezéshez.
> * Futtassa helyben az alkalmazást.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Az oktatóanyag elkezdése előtt végezze el a [4. rész](tutorial-develop-mongodb-nodejs-part4.md)lépéseit.

* Ehhez az oktatóanyaghoz helyileg kell futtatnia az Azure CLI-t. Az Azure CLI 2.0-s vagy újabb verzióját kell telepíteni. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure cli 2,0 telepítését](/cli/azure/install-azure-cli)ismertető témakört.

* Ez az oktatóanyag végigvezeti az alkalmazás lépésről lépésre történő felépítésének lépésein. Ha le szeretné tölteni a kész projektet, a kész alkalmazást az [angular-cosmosdb adattárból](https://github.com/Azure-Samples/angular-cosmosdb) töltheti le a GitHubról.

## <a name="use-mongoose-to-connect"></a>Az mongúz használata a kapcsolódáshoz

A mongúz a MongoDB és a Node.js Object adatmodellezési (ODM) könyvtára. Az Azure Cosmos DB-fiókhoz való kapcsolódáshoz a mongúzt használhatja. Az alábbi lépéseket követve telepítse a mongúzt, és kapcsolódjon a Azure Cosmos DBhoz:

1. Telepítse a mongúz NPM modult, amely a MongoDB való kommunikációhoz használt API.

    ```bash
    npm i mongoose --save
    ```

1. A **Server** mappában hozzon létre egy **mongo.js** nevű fájlt. A Azure Cosmos DB-fiók kapcsolati adatait hozzáadja a fájlhoz.

1. Másolja az alábbi kódot a **mongo.js** fájlba. A kód a következő funkciókat biztosítja:

   * A Mongoose használatát igényli.
   * Felülbírálja a Mongo ígéretet, hogy a ES6/ES2015 és újabb verziókban beépített alapszintű ígéretet használja.
   * Olyan env-fájl hívása, amely lehetővé teszi bizonyos dolgok beállítását, attól függően, hogy az előkészítés, a gyártás vagy a fejlesztés van-e. Ezt a fájlt a következő szakaszban fogja létrehozni.
   * Tartalmazza a MongoDB-kapcsolatok karakterláncát, amely az ENV fájlban van beállítva.
   * Létrehoz egy connect függvényt, amely meghívja a Mongoose-t.

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. Az Explorer ablaktábla **kiszolgáló** területén hozzon létre egy **környezet** nevű mappát. A **környezet** mappában hozzon létre egy **environment.js** nevű fájlt.

1. A mongo.js fájlból meg kell adnia a `dbName` , a `key` és a paraméterek értékeit `cosmosPort` . Másolja a következő kódot a **environment.js** fájlba:

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

Az alkalmazás Azure Cosmos DBhoz való összekapcsolásához frissítenie kell az alkalmazás konfigurációs beállításait. A beállítások frissítéséhez kövesse az alábbi lépéseket: 

1. A Azure Portal adja meg a portszámot, Azure Cosmos DB a fiók nevét és az elsődleges kulcs értékeit a Azure Cosmos DB-fiókhoz.

1. A **environment.js** fájlban módosítsa a értékét 10255-re `port` . 

    ```javascript
    const port = 10255;
    ```

1. A **environment.js** fájlban módosítsa a értékét az `accountName` oktatóanyag [4. részében](tutorial-develop-mongodb-nodejs-part4.md) létrehozott Azure Cosmos db-fiók nevére. 

1. Kérje le az Azure Cosmos DB-fiók elsődleges kulcsát az alábbi parancssori paranccsal a terminálablakban: 

    ```azurecli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> az oktatóanyag [4. részében](tutorial-develop-mongodb-nodejs-part4.md) létrehozott Azure Cosmos db fiók neve.

1. Másolja az elsődleges kulcsot a **environment.js** fájlba `key` értékként.

Most az alkalmazás minden szükséges információt tartalmaz a Azure Cosmos DBhoz való kapcsolódáshoz. 

## <a name="create-a-hero-model"></a>Főképmodell létrehozása

Ezután meg kell határoznia a Azure Cosmos DB tárolni kívánt adatsémát egy modellel definiálva. A következő lépésekkel hozhat létre egy olyan _hős-modellt_ , amely meghatározza az adatsémát:

1. Az Explorer ablaktábla **kiszolgáló** mappájában hozzon létre egy **hero.model.js** nevű fájlt.

1. Másolja az alábbi kódot a **hero.model.js** fájlba. A kód a következő funkciókat biztosítja:

   * A Mongoose használatát igényli.
   * Egy új sémát hoz létre egy azonosítóval, egy névvel és egy üzenettel.
   * Létrehoz egy modellt a séma használatával.
   * Exportálja a modellt. 
   * A gyűjteményhez tartozó **hősök** neve (a **heros** helyett a gyűjtemény alapértelmezett neve, amely a többes számú mongúz-elnevezési szabályok alapján).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Főképszolgáltatás létrehozása

A Hero modell létrehozása után meg kell határoznia egy szolgáltatást az adatok olvasásához, valamint a lista, a létrehozás, a törlés és a frissítési művelet végrehajtásához. A következő lépésekkel hozhat létre egy olyan _Hero-szolgáltatást_ , amely a Azure Cosmos DBból lekérdezi az adatait:

1. Az Explorer ablaktábla **kiszolgáló** mappájában hozzon létre egy **hero.service.js** nevű fájlt.

1. Másolja az alábbi kódot a **hero.service.js** fájlba. A kód a következő funkciókat biztosítja:

   * Lekéri a létrehozott modellt.
   * Csatlakozik az adatbázishoz.
   * Egy olyan `docquery` változót hoz létre, amely a `hero.find` metódus használatával definiál egy lekérdezést, amely az összes hősöket visszaadja.
   * Futtat egy lekérdezést a `docquery.exec` függvénnyel, és megígéri, hogy beolvassa az összes olyan hősök listáját, ahol a válasz állapota 200. 
   * A hibaüzenetet küldi vissza, ha az állapota 500.
   * Beolvassa a hősöket, mert modulokat használunk. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>Útvonalak konfigurálása

Ezután be kell állítania az útvonalakat a Get, a Create, az olvasás és a DELETE kérelmek URL-címeinek kezeléséhez. Az útválasztási módszerek a visszahívási függvényeket (más néven _kezelő függvények_) adják meg. Ezeket a függvényeket akkor hívja meg a rendszer, amikor az alkalmazás egy kérést kap a megadott végpont és HTTP-metódus számára. A következő lépésekkel adhatja hozzá a Hero szolgáltatást, és meghatározhatja az útvonalakat:

1. A Visual Studio Code-ban a **routes.js** fájlban tegye megjegyzésbe a `res.send` minta Hero-adatokat küldő függvényt. Ehelyett adjon hozzá egy sort a függvény meghívásához `heroService.getHeroes` .

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. A **routes.js** fájlban `require` a Hero szolgáltatás:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. A **hero.service.js** fájlban frissítse a `getHeroes` függvényt, hogy a `req` és a `res` paramétereket az alábbiak szerint végezze el:

    ```javascript
    function getHeroes(req, res) {
    ```

Egy perc alatt áttekintheti az előző kódot, és elvégezheti az átjárást. Először is bemutatjuk a index.js fájlt, amely beállítja a csomópont-kiszolgálót. Figyelje meg, hogy beállítja és meghatározza az útvonalakat. Ezután a routes.js-fájl a Hero szolgáltatással beszélget, és azt jelzi, hogy a függvényeket, például a **getHeroes**, és továbbítja a kérést és a választ. A hero.service.js fájl megkapja a modellt, és csatlakozik a Mongo. Ezt követően végrehajtja a **getHeroes** , amikor meghívjuk, és visszaadja a 200-es választ. 

## <a name="run-the-app"></a>Az alkalmazás futtatása

Ezután futtassa az alkalmazást az alábbi lépések végrehajtásával:

1. A Visual Studio Code-ban mentse az összes módosítást. A bal oldalon válassza a **hibakeresés** gombot :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png"::: , majd kattintson a **hibakeresés indítása** gombra :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png"::: .

1. Most váltson a böngészőre. Nyissa meg a **fejlesztői eszközöket** és a **hálózat lapot**. Itt `http://localhost:3000` láthatja az alkalmazást.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png" alt-text="Új Azure Cosmos DB-fiók az Azure Portalon":::

Az alkalmazásban még nincsenek hősök tárolva. Az oktatóanyag következő részében a Put, a push és a DELETE funkciókat vesszük fel. Ezt követően hozzáadhat, frissíthet és törölhet hősöket a felhasználói felületen az Azure Cosmos-adatbázishoz tartozó mongúz-kapcsolatok használatával. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforrásokra, törölheti az erőforráscsoportot, Azure Cosmos DB fiókot és az összes kapcsolódó erőforrást. Az erőforráscsoport törléséhez kövesse az alábbi lépéseket:

 1. Keresse meg azt az erőforráscsoportot, amelyben létrehozta a Azure Cosmos DB fiókot.
 1. Válassza az **Erőforráscsoport törlése** elemet.
 1. Erősítse meg a törölni kívánt erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Folytassa az oktatóanyag 6. részével, hogy hozzáadja a post, Put és DELETE függvényeket az alkalmazáshoz:

> [!div class="nextstepaction"]
> [6. rész: a post, Put és DELETE függvények hozzáadása az alkalmazáshoz](tutorial-develop-mongodb-nodejs-part6.md)