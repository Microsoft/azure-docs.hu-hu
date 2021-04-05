---
title: A MongoDB-hez Azure Cosmos DB API-val rendelkező szögletes alkalmazáshoz hozzáadhat szifilisz-függvényeket
description: A MongoDB-alkalmazások Azure Cosmos DB adatbázison Angular és Node használatával, a MongoDB-hez használt API-kkal való létrehozását ismertető oktatóanyag-sorozat 6. része.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: c8e2c707566b08219b495e76be7f6f6130d876ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93081314"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---add-crud-functions-to-the-app"></a>Szögletes alkalmazás létrehozása Azure Cosmos DB API-MongoDB – a szifilisz-függvények hozzáadása az alkalmazáshoz
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez a többrészes oktatóanyag bemutatja, hogyan hozhat létre egy olyan új alkalmazást, amelyet expressz és szögletes Node.js írt be, majd a [Cosmos db API-MongoDB konfigurált Cosmos-fiókhoz](mongodb-introduction.md)csatlakozik. Az oktatóanyag 6. része az [5. részre](tutorial-develop-mongodb-nodejs-part5.md) épül, és az alábbi feladatokat ismerteti:

> [!div class="checklist"]
> * Post, Put és Delete függvények létrehozása a főképszolgáltatáshoz
> * Az alkalmazás futtatása

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>Előfeltételek

Ennek a résznek a megkezdése előtt mindenképp végezze el az oktatóanyag [5. részében](tutorial-develop-mongodb-nodejs-part5.md) ismertetett lépéseket.

> [!TIP]
> Az oktatóanyag lépésről lépésre végigvezeti az alkalmazás létrehozásának lépésein. Ha le szeretné tölteni a kész projektet, a kész alkalmazást az [angular-cosmosdb adattárból](https://github.com/Azure-Samples/angular-cosmosdb) töltheti le a GitHubról.

## <a name="add-a-post-function-to-the-hero-service"></a>Post függvény hozzáadása a főképszolgáltatáshoz

1. A Visual Studio Code-ban nyissa meg a **routes.js** és a **hero.service.js** egymás mellé. ehhez kattintson a **felosztott szerkesztő** gombra :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png"::: .

    Láthatja, hogy a routes.js 7. sora meghívja a `getHeroes` függvényt a **hero.service.js** 5. sorában.  Ugyanilyen párosítást kell létrehoznunk a Post, Put és Delete függvényekhez. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png" alt-text="A routes.js és a hero.service.js a Visual Studio Code-ban":::
    
    Kezdjük a főképszolgáltatás kódjával. 

2. Másolja az alábbi kódot a **hero.service.js** fájlba a `getHeroes` függvény után és a `module.exports` elé. Ez a kód:  
   * A főképmodell használatával közzétesz egy új fő képet.
   * Figyeli a válaszokat, hogy nincsenek-e hibák, és visszaad egy 500-as állapotértéket.

   ```javascript
   function postHero(req, res) {
     const originalHero = { uid: req.body.uid, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. A **hero.service.js** fájlban frissítse a `module.exports` részt, hogy tartalmazza az új `postHero` függvényt. 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. A **routes.js** fájlban adjon hozzá egy útválasztót a `post` függvény számára a `get` útválasztó után. Ez az útválasztó egyszerre egy fő képet tesz közzé. Ha az útválasztó fájlt így strukturálja, egyértelműen látható lesz az összes elérhető API-végpont, és a tényleges munka a **hero.service.js** fájlra marad.

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. Az alkalmazást futtatva ellenőrizze, hogy minden működik-e. A Visual Studio Code-ban mentse az összes módosítást,  válassza a :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png"::: bal oldalon található hibakeresés gombot, majd kattintson a **hibakeresés indítása** gombra :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png"::: .

6. Most lépjen vissza a webböngészőjére, és nyissa meg a Fejlesztői eszközök Hálózat lapját – ehhez a legtöbb gépen az F12 billentyűt kell lenyomni. Lépjen a `http://localhost:3000` helyre, és tekintse meg a hálózaton keresztül végzett hívásokat.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png" alt-text="Hálózat lap a Chrome böngészőben a hálózati tevékenységgel":::

7. Adjon hozzá egy új fő képet az **Add New Hero** (Új fő kép hozzáadása) gomb választásával. Adja meg a „999” azonosítót, a „Fred” nevet és a „Hello” üzenetet, majd válassza a **Save** (Mentés) gombot. A Hálózat lapon látható, hogy POST-kérést küldött egy új fő képre vonatkozóan. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png" alt-text="Hálózat lap a Chrome böngészőben a Get és Post függvények hálózati tevékenységével":::

    Most térjünk vissza a szerkesztőre, és adjuk hozzá a Put és Delete függvényeket az alkalmazáshoz.

## <a name="add-the-put-and-delete-functions"></a>A Put és Delete függvények hozzáadása

1. A **routes.js** fájlban adja hozzá a `put` és `delete` útválasztókat a post útválasztó után.

    ```javascript
    router.put('/hero/:uid', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:uid', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. Másolja az alábbi kódot a **hero.service.js** fájlba a `checkServerError` függvény után. Ez a kód:
   * Létrehozza a `put` és `delete` függvényeket
   * Ellenőrzi, hogy a fő kép megtalálható-e
   * Elvégzi a hibakezelést 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       uid: parseInt(req.params.uid, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ uid: originalHero.uid }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const uid = parseInt(req.params.uid, 10);
     Hero.findOneAndRemove({ uid: uid })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. A **hero.service.js** fájlban exportálja az új modulokat:

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. Most, hogy frissítettük a kódot, kattintson az **Újraindítás** gombra a :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png"::: Visual Studio Code-ban.

5. Frissítse az oldalt a webböngészőben, és válassza az **Add New Hero** (Új fő kép hozzáadása) gombot. Adjon hozzá egy új fő képet a „9” azonosítóval, a „Starlord” névvel és a „Hi” üzenettel. Az új fő kép mentéséhez válassza a **Save** (Mentés) gombot.

6. Most jelölje ki a **Starlord** fő képet, és a „Hi” üzenet helyett adja meg a „Bye” üzenetet, majd válassza a **Save** (Mentés) gombot. 

    Most a Hálózat lapon az azonosítót kiválasztva megtekintheti a csomag hasznos adatait. A hasznos adatokban láthatja, hogy az üzenet most már a „Bye”.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png" alt-text="Fő képek alkalmazás és a Hálózat lap a hasznos adatokkal"::: 

    A felhasználói felületen törölheti is az egyik fő képet, és láthatja, hogy a törlési műveletek végrehajtása mennyi ideig tart. Ennek kipróbálásához válassza a „Delete” (Törlés) gombot a „Fred” nevű fő képnél.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part6/times.png" alt-text="Főképalkalmazás és a Hálózat lap a függvények végrehajtásához szükséges időkkel"::: 

    Ha frissíti az oldalt, a Hálózat lap a fő képek lekéréséhez szükséges időt mutatja. Bár az itt látható idők rövidek, rengeteg múlik azon, hogy az adatok a világ mely részén vannak tárolva, és hogy azokat a felhasználókhoz közeli helyekre tudja-e georeplikálni. A georeplikációról további információkat a nemsokára elérhető következő oktatóanyagban talál majd.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyagnak ebben a részében a következőket hajtotta végre:

> [!div class="checklist"]
> * Post, Put és Delete függvényeket adott hozzá az alkalmazáshoz 

Térjen vissza később az oktatóanyag-sorozat következő videoanyagaiért.

