---
title: Node.js Express-alkalmazás létrehozása a MongoDB-hez készült Azure Cosmos DB API-val (part2)
description: A MongoDB-alkalmazások Azure Cosmos DB adatbázison Angular és Node használatával, a MongoDB-hez használt API-kkal való létrehozását ismertető oktatóanyag-sorozat 2. része.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: e3af713d6e120e66a0cde5116f4556bb8caedf55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097850"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-nodejs-express-app"></a>Szögletes alkalmazás létrehozása Azure Cosmos DB API-MongoDB – Node.js Express-alkalmazás létrehozása
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a többrészes oktatóanyag bemutatja, hogyan hozhat létre egy olyan új alkalmazást, amelyet expressz és szögletes Node.js írt be, majd a [Cosmos db API-MongoDB konfigurált Cosmos-fiókhoz](mongodb-introduction.md)csatlakozik.

Az oktatóanyag 2. része a [bevezető részre](tutorial-develop-mongodb-nodejs.md) épül, és az alábbi feladatokat ismerteti:

> [!div class="checklist"]
> * Az Angular CLI és a TypeScript telepítése
> * Új projekt létrehozása az Angular használatával
> * Az alkalmazás felépítése az Express keretrendszer használatával
> * Az alkalmazás tesztelése a Postmanben

## <a name="video-walkthrough"></a>Bemutató videó

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>Előfeltételek

Ennek a résznek a megkezdése előtt mindenképp tekintse meg a [bevezető videót](tutorial-develop-mongodb-nodejs.md).

Az oktatóanyaghoz a következőkre is szükség lesz: 
* A [Node.js](https://nodejs.org/) 8.4.0 vagy újabb verziója.
* [Postman](https://www.getpostman.com/)
* A [Visual Studio Code](https://code.visualstudio.com/) vagy egyéb előnyben részesített kódszerkesztő.

> [!TIP]
> Az oktatóanyag lépésről lépésre végigvezeti az alkalmazás létrehozásának lépésein. Ha le szeretné tölteni a kész projektet, a kész alkalmazást az [angular-cosmosdb adattárból](https://github.com/Azure-Samples/angular-cosmosdb) töltheti le a GitHubról.

## <a name="install-the-angular-cli-and-typescript"></a>Az Angular CLI és a TypeScript telepítése

1. Nyisson meg egy Windows-parancssort vagy Mac-terminálablakot, és telepítse az Angular CLI parancssori felületet.

    ```bash
    npm install -g @angular/cli
    ```

2. A parancssorban az alábbi parancs megadásával telepítse a TypeScriptet. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Az Angular CLI használata új projekt létrehozásához

1. A parancssorban váltson arra a mappára, ahol az új projektet létre szeretné hozni, majd futtassa az alábbi parancsot. A parancs létrehoz egy új mappát és egy angular-cosmosdb elnevezésű projektet, és telepíti az új alkalmazáshoz szükséges Angular-összetevőket. A minimális beállítást alkalmazza (--minimal), és meghatározza, hogy a projekt a Sass nyelvet használja (egy CSS-szerű szintaxist, a --style scss jelölővel).

    ```bash
    ng new angular-cosmosdb --minimal --style scss
    ```

2. Miután a parancs végrehajtása befejeződött, váltson a src/client mappára.

    ```bash
    cd angular-cosmosdb
    ```

3. Nyissa meg a mappát a Visual Studio Code-ban.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Az alkalmazás létrehozása az Express keretrendszer használatával

1. A Visual Studio Code-ban az **Explorer** panelen kattintson jobb gombbal az **src** mappára, kattintson a **New Folder** (Új mappa) elemre, és adja a *server* nevet az új mappának.

2. Az **Explorer** panelen kattintson jobb gombbal a **server** mappára, majd a **New File** (Új fájl) elemre, és adja az *index.js* nevet az új fájlnak.

3. Lépjen ismét a parancssorra, és a következő paranccsal telepítse a törzselemzőt. Ez segít az alkalmazásnak elemezni az API-kon keresztül átadott JSON-adatokat.

    ```bash
    npm i express body-parser --save
    ```

4. A Visual Studio Code-ban másolja az alábbi kódot az index.js fájlba. Ez a kód:
    * Hivatkozik az Expressre
    * Bekéri a törzselemzőt a JSON-adatok olvasásához a kérések törzsében
    * Az útvonal elnevezésű beépített szolgáltatást használja
    * Beállítja a gyökérváltozókat, hogy könnyebb legyen megtalálni a kód helyét
    * Beállít egy portot
    * Beindítja az Expresst
    * Megadja az alkalmazásnak, hogyan használja a kiszolgáló kiszolgálására használni kívánt közbenső szoftvert
    * Kiszolgál mindent, ami a dist mappában található, azaz a majdani statikus tartalmat
    * Kiszolgálja az alkalmazást, és szolgáltatja az index.html fájlt minden olyan GET kéréshez, amely nem a kiszolgálón található (mélyhivatkozások esetén)
    * Elindítja a kiszolgálót az app.listen használatával
    * Egy nyíl függvény használatával naplózza, hogy a port életben van-e
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist/angular-cosmosdb')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/angular-cosmosdb/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. A Visual Studio Code-ban az **Explorer** panelen kattintson jobb gombbal a **server** mappára, majd kattintson a **New File** (Új fájl) elemre. Adja a *routes.js* nevet az új fájlnak. 

6. Másolja az alábbi kódot a **routes.js** fájlba. Ez a kód:
   * Hivatkozik az Express útválasztóra
   * Lekéri a fő képeket
   * Visszaküldi a JSON-t egy megadott fő képnek

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Menti az összes módosított fájlt. 

8. A Visual Studio Code-ban kattintson a **Debug (hibakeresés** ) gombra :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: , majd a fogaskerék gombra :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png"::: . Az új launch.json fájl megnyílik a Visual Studio Code-ban. 

8. A launch.json fájl 11. sorában módosítsa a `"${workspaceFolder}\\server"` részt a `"program": "${workspaceRoot}/src/server/index.js"` kódra, és mentse a fájlt.

9. Kattintson a **hibakeresés indítása** gombra az :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png"::: alkalmazás futtatásához.

    Az alkalmazásnak hiba nélkül kell futnia.

## <a name="use-postman-to-test-the-app"></a>Az alkalmazás tesztelése a Postman használatával

1. Most nyissa meg a Postmant, és másolja a `http://localhost:3000/api/heroes` címet a GET mezőbe. 

2. Kattintson az **Send** (Küldés) gombra, és várja meg az alkalmazástól érkező JSON-választ. 

    A válaszból kiderül, hogy az alkalmazás működik és helyileg fut. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png" alt-text="Postman képe a kéréssel és a válasszal":::


## <a name="next-steps"></a>Következő lépések

Az oktatóanyagnak ebben a részében a következőket hajtotta végre:

> [!div class="checklist"]
> * Létrehozott egy Node.js-projektet az Angular CLI parancssori felülettel
> * Tesztelte az alkalmazást a Postman segítségével

Továbbléphet az oktatóanyag következő részére, amelyben létrehozza a felhasználói felületet.

> [!div class="nextstepaction"]
> [A felhasználói felület létrehozása az Angular használatával](tutorial-develop-mongodb-nodejs-part3.md)
