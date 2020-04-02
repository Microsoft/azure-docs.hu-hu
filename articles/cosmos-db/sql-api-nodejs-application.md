---
title: Oktatóanyag:Node.js webalkalmazás létrehozása az Azure Cosmos DB JavaScript SDK-val az SQL API-adatok kezeléséhez
description: Ez a Node.js oktatóanyag azt ismerteti, hogyan tárolhatja és érheti el a Microsoft Azure App Service WebApps szolgáltatásában üzemeltetett Node.js Express webalkalmazás adatait a Microsoft Azure Cosmo-kiszolgáló webalkalmazások szolgáltatásában tárolt Adatok tárolására és elérésére.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
Customer intent: As a developer, I want to build a Node.js web application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 3c50e82647b22a18edee92f47abc1d136670cacc
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519670"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>Oktatóanyag: Node.js webalkalmazás létrehozása a JavaScript SDK használatával egy SQL API-fiók kezeléséhez az Azure Cosmos DB-ben 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Fejlesztőként előfordulhat, hogy noSQL dokumentumadatokat használó alkalmazások at használ. Az Azure Cosmos DB-ben egy SQL API-fiók használatával tárolhatja és érheti el a dokumentumadatokat. Ez a Node.js oktatóanyag bemutatja, hogyan tárolhatja és érheti el az adatokat egy SQL API-fiókból az Azure Cosmos DB-ben a Microsoft Azure App Service Web Apps szolgáltatásában üzemeltetett Node.js Express alkalmazás használatával. Ebben az oktatóanyagban egy webalapú alkalmazást (Todo alkalmazást) hozhat létre, amely lehetővé teszi feladatok létrehozását, lekérését és elvégzését. A feladatokat JSON-dokumentumok formájában tárolja az Azure Cosmos DB. 

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy SQL API-fiókot az Azure Cosmos DB-ben az Azure Portal használatával. Ezután hozzon létre és futtasson egy webalkalmazást, amely a Node.js SDK-ra épül egy adatbázis és tároló létrehozásához, és elemeket ad hozzá a tárolóhoz. Ez az oktatóanyag a JavaScript SDK 3.0-s verzióját használja.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Azure Cosmos DB-fiók létrehozása
> * Új Node.js-alkalmazás létrehozása
> * Az alkalmazás csatlakoztatása az Azure Cosmos DB-hez
> * Az alkalmazás futtatása és üzembe helyezése az Azure-ban

## <a name="prerequisites"></a><a name="_Toc395783176"></a>Előfeltételek

A jelen cikkben lévő utasítások követése előtt győződjön meg arról, hogy rendelkezik az alábbi erőforrásokkal:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* A [Node.js][Node.js] 6.10-es vagy újabb verziója.
* [Express generator](https://www.expressjs.com/starter/generator.html) (az Expresst az `npm install express-generator -g` segítségével telepítheti)
* Telepítse a [Git][Git] szoftvert a helyi munkaállomáson.

## <a name="create-an-azure-cosmos-db-account"></a><a name="_Toc395637761"></a>Azure Cosmos DB-fiók létrehozása
Először hozzon létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik fiókkal, vagy az oktatóanyagban az Azure Cosmos DB Emulatort használja, továbbléphet a [2. lépés: Új Node.js-alkalmazás létrehozása](#_Toc395783178) című lépésre.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="create-a-new-nodejs-application"></a><a name="_Toc395783178"></a>Új Node.js-alkalmazás létrehozása
Most megtanulhatja, hogyan hozhat létre egy alapszintű Hello World Node.js-projektet az Express-keretrendszer használatával.

1. Nyissa meg kedvenc terminálját, például a Node.js parancssort.

1. Keresse meg azt a könyvtárat, amelyben tárolni szeretné az új alkalmazást.

1. Az Express generátor használatával hozzon létre egy új alkalmazást **todo** (teendők) néven.

   ```bash
   express todo
   ```

1. Nyissa meg a **todo** könyvtárat, és telepítse a függőségeket.

   ```bash
   cd todo
   npm install
   ```

1. Futtassa az új alkalmazást.

   ```bash
   npm start
   ```

1. Az új alkalmazás megtekintéséhez navigáljon a böngészőben a következő címre: `http://localhost:3000`.
   
   ![A Node.js megismerése – Képernyőfelvétel a Hello World alkalmazásról egy böngészőablakban](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

   Állítsa le az alkalmazást a terminálablak CTRL+C billentyűkombinációjával, és válassza az **y** lehetőséget a kötegelt feldolgozás leállításához.

## <a name="install-the-required-modules"></a><a name="_Toc395783179"></a>Telepítse a szükséges modulokat

A **package.json** fájl egyike azon fájloknak, amelyek a projekt gyökérmappájában létrejönnek. Ez a fájl tartalmazza a Node.js-alkalmazáshoz szükséges további modulok listáját. Amikor az Azure-ba telepíti az alkalmazást, a rendszer ennek a fájlnak a segítségével határozza meg, hogy melyik modulokat kell az Azure-ban telepíteni ahhoz, hogy működjön az alkalmazás. A jelen oktatóanyagban telepítsen még két csomagot.

1. Telepítse az ** \@azure/cosmos modult** npm-en keresztül. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="connect-the-nodejs-application-to-azure-cosmos-db"></a><a name="_Toc395783180"></a>A Node.js alkalmazás csatlakoztatása az Azure Cosmos DB-hez
Miután most végrehajtottuk a kezdeti telepítést és konfigurálást, megírjuk a kódot, amelyre a Teendők alkalmazásnak szüksége van az Azure Cosmos DB-vel való kommunikációhoz.

### <a name="create-the-model"></a>A modell létrehozása
1. A projektkönyvtár gyökerében hozzon létre egy új könyvtár nevű **modellt.**  

2. A **models** könyvtárban hozzon létre egy új fájlt **taskDao.js** néven. Ez a fájl az adatbázis és a tároló létrehozásához szükséges kódot tartalmazza. Azt is meghatározza, hogy az Azure Cosmos DB-ben olvassa, frissítse, hozza létre és találja meg a feladatokat. 

3. Másolja a következő kódot a **taskDao.js** fájlba:

   ```javascript
    // @ts-check
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const debug = require('debug')('todo:taskDao')

    // For simplicity we'll set a constant partition key
    const partitionKey = undefined
    class TaskDao {
      /**
       * Manages reading, adding, and updating Tasks in Cosmos DB
       * @param {CosmosClient} cosmosClient
       * @param {string} databaseId
       * @param {string} containerId
       */
      constructor(cosmosClient, databaseId, containerId) {
        this.client = cosmosClient
        this.databaseId = databaseId
        this.collectionId = containerId

        this.database = null
        this.container = null
      }

      async init() {
        debug('Setting up the database...')
        const dbResponse = await this.client.databases.createIfNotExists({
          id: this.databaseId
        })
        this.database = dbResponse.database
        debug('Setting up the database...done!')
        debug('Setting up the container...')
        const coResponse = await this.database.containers.createIfNotExists({
          id: this.collectionId
        })
        this.container = coResponse.container
        debug('Setting up the container...done!')
      }

      async find(querySpec) {
        debug('Querying for items from the database')
        if (!this.container) {
          throw new Error('Collection is not initialized.')
        }
        const { resources } = await this.container.items.query(querySpec).fetchAll()
        return resources
      }

      async addItem(item) {
        debug('Adding an item to the database')
        item.date = Date.now()
        item.completed = false
        const { resource: doc } = await this.container.items.create(item)
        return doc
      }

      async updateItem(itemId) {
        debug('Update an item in the database')
        const doc = await this.getItem(itemId)
        doc.completed = true

        const { resource: replaced } = await this.container
          .item(itemId, partitionKey)
          .replace(doc)
        return replaced
      }

      async getItem(itemId) {
        debug('Getting an item from the database')
        const { resource } = await this.container.item(itemId, partitionKey).read()
        return resource
      }
    }

    module.exports = TaskDao
   ```
4. Mentse és zárja be a **taskDao.js** fájlt.  

### <a name="create-the-controller"></a>A vezérlő létrehozása

1. A projekt **routes** könyvtárában hozzon létre egy új fájlt **tasklist.js** néven.  

2. Adja hozzá a következő kódot a **tasklist.js** fájlhoz. Ez a kód betölti a **tasklist.js** fájl által használt CosmosClient és async modult. A kód a **TaskList** (Feladatlista) osztályt is meghatározza, amelyet a rendszer a korábban definiált **TaskDao** objektum példányába továbbít:
   
   ```javascript
    const TaskDao = require("../models/TaskDao");
    
    class TaskList {
      /**
       * Handles the various APIs for displaying and managing tasks
       * @param {TaskDao} taskDao
       */
      constructor(taskDao) {
        this.taskDao = taskDao;
      }
      async showTasks(req, res) {
        const querySpec = {
          query: "SELECT * FROM root r WHERE r.completed=@completed",
          parameters: [
            {
              name: "@completed",
              value: false
            }
          ]
        };

        const items = await this.taskDao.find(querySpec);
        res.render("index", {
          title: "My ToDo List ",
          tasks: items
        });
      }

      async addTask(req, res) {
        const item = req.body;

        await this.taskDao.addItem(item);
        res.redirect("/");
      }

      async completeTask(req, res) {
        const completedTasks = Object.keys(req.body);
        const tasks = [];

        completedTasks.forEach(task => {
          tasks.push(this.taskDao.updateItem(task));
        });

        await Promise.all(tasks);

        res.redirect("/");
      }
    }

    module.exports = TaskList;
   ```

3. Mentse és zárja be a **tasklist.js** fájlt.

### <a name="add-configjs"></a>A config.js fájl hozzáadása

1. A projektkönyvtár gyökerében hozzon létre egy új fájlt **config.js** néven. 

2. Adja hozzá a következő kódot a **config.js** fájlhoz. Ez a kód határozza meg az alkalmazáshoz szükséges konfigurációs beállításokat és értékeket.
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. A **config.js** fájlban frissítse a HOST és a AUTH_KEY értékeit az Azure Cosmos DB-fiók Keys lapján az [Azure Portalon](https://portal.azure.com)található értékek használatával. 

4. Mentse és zárja be a **config.js** fájlt.

### <a name="modify-appjs"></a>Az app.js fájl módosítása

1. A projekt könyvtárában nyissa meg az **app.js** fájlt. Ez a fájl korábban, az Express-webalkalmazás létrehozásakor jött létre.  

2. Adja hozzá a következő kódot az **app.js** fájlhoz. Ez a kód fogja meghatározni a használni kívánt konfigurációs fájlt, és betölteni az értékeket néhány változóba, amelyekre a következő szakaszokban szüksége lesz. 
   
   ```javascript
    const CosmosClient = require('@azure/cosmos').CosmosClient
    const config = require('./config')
    const TaskList = require('./routes/tasklist')
    const TaskDao = require('./models/taskDao')

    const express = require('express')
    const path = require('path')
    const logger = require('morgan')
    const cookieParser = require('cookie-parser')
    const bodyParser = require('body-parser')

    const app = express()

    // view engine setup
    app.set('views', path.join(__dirname, 'views'))
    app.set('view engine', 'jade')

    // uncomment after placing your favicon in /public
    //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
    app.use(logger('dev'))
    app.use(bodyParser.json())
    app.use(bodyParser.urlencoded({ extended: false }))
    app.use(cookieParser())
    app.use(express.static(path.join(__dirname, 'public')))

    //Todo App:
    const cosmosClient = new CosmosClient({
      endpoint: config.host,
      key: config.authKey
    })
    const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId)
    const taskList = new TaskList(taskDao)
    taskDao
      .init(err => {
        console.error(err)
      })
      .catch(err => {
        console.error(err)
        console.error(
          'Shutting down because there was an error settinig up the database.'
        )
        process.exit(1)
      })

    app.get('/', (req, res, next) => taskList.showTasks(req, res).catch(next))
    app.post('/addtask', (req, res, next) => taskList.addTask(req, res).catch(next))
    app.post('/completetask', (req, res, next) =>
      taskList.completeTask(req, res).catch(next)
    )
    app.set('view engine', 'jade')

    // catch 404 and forward to error handler
    app.use(function(req, res, next) {
      const err = new Error('Not Found')
      err.status = 404
      next(err)
    })

    // error handler
    app.use(function(err, req, res, next) {
      // set locals, only providing error in development
      res.locals.message = err.message
      res.locals.error = req.app.get('env') === 'development' ? err : {}

      // render the error page
      res.status(err.status || 500)
      res.render('error')
    })

    module.exports = app
   ```

3. Végül mentse és zárja be az **app.js** fájlt.

## <a name="build-a-user-interface"></a><a name="_Toc395783181"></a>Felhasználói felület létrehozása

Most hozzuk létre a felhasználói felületet, hogy a felhasználó kommunikálhat az alkalmazással. Az előző szakaszokban létrehozott Express-alkalmazás a **Jade** megjelenítési motort használja.

1. A rendszer a **views** (nézetek) könyvtárban található **layout.jade** fájlt használja a többi **.jade** fájl globális sablonjaként. Ebben a lépésben ezt a sablont a Twitter Bootstrap eszközkészletre módosítja majd, amellyel webhelyeket tervezhet.  

2. Nyissa meg a **views** (nézetek) mappában található **layout.jade** fájlt, és cserélje ki annak tartalmát az alábbi kódra:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Ez a kód azt mondja a **Jade** motor tegyék néhány HTML alkalmazásunk, és létrehoz egy **blokk** nevű **tartalom,** ahol tudjuk a kínálat az elrendezés a mi tartalmi oldalakon. Mentse és zárja be a **layout.jade** fájlt.

3. Most nyissa meg az **index.jade** fájlt, az alkalmazás által használt nézetet, és cserélje ki a fájl tartalmát az alábbi kódra:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Ez a kód kibővíti az elrendezést, és tartalmat biztosít a **layout.jade** fájlban az imént látott **content** (tartalom) helyőrző számára. Ebben az elrendezésben két HTML-űrlapot hoztunk létre.

Az első űrlap az adatok táblázatát, valamint egy gombot tartalmaz, amely lehetővé teszi az elemek frissítését úgy, hogy elküldi őket a vezérlő **/completeTask** metódusának.
    
A második űrlap két beviteli mezőt és egy gombot tartalmaz, amely lehetővé teszi új elemek létrehozását úgy, hogy elküldi azokat a vezérlő **/addtask** metódusának. Az alkalmazás működéséhez csak ennyire van szükség.

## <a name="run-your-application-locally"></a><a name="_Toc395783181"></a>Az alkalmazás helyi futtatása

Most, hogy már felépítette az alkalmazást, helyileg futtathatja a következő lépésekkel:  

1. Az alkalmazás helyi számítógépen történő `npm start` teszteléséhez futtassa a terminálon [http://localhost:3000](http://localhost:3000) az alkalmazás elindításához, majd frissítse a böngészőlapot. Az oldalnak az alábbi képernyőképen látható módon kell kinéznie:
   
    ![Képernyőfelvétel a My Todo List (Saját teendőlista) alkalmazásról egy böngészőablakban](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Ha a layout.jade fájlban vagy az index.jade fájlban hibaüzenet jelenik meg a behúzással kapcsolatban, győződjön meg arról, hogy mindkét fájl első két sora balra igazított, szóközök nélkül. Ha az első két sor előtt szóközök vannak, távolítsa el őket, mentse mindkét fájlt, majd frissítse a böngészőablakot. 

2. Az Elem, az Elem név és a Kategória mezővel adjon meg új feladatot, majd válassza **az Elem hozzáadása**lehetőséget. Ez egy dokumentumot hoz létre az Azure Cosmos DB-ben a megadott tulajdonságokkal. 

3. Az oldal ekkor frissül, és megjeleníti az újonnan létrehozott elemet a teendőlistában.
   
    ![Képernyőfelvétel az alkalmazásról és a teendőlista új eleméről](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)

4. Feladat végrehajtásához jelölje be a Kiegészítés oszlopjelölőnégyzetét, majd a **Tevékenységek frissítése**lehetőséget. Ez frissíti a már létrehozott dokumentumot, és eltávolítja a nézetből.

5. Az alkalmazás leállításához nyomja le a CTRL+C billentyűkombinációt a terminálablakban, majd a kötegelt feladat leállításához válassza az **Y** elemet.

## <a name="deploy-your-application-to-web-apps"></a><a name="_Toc395783182"></a>Az alkalmazás telepítése webalkalmazásokba

Miután az alkalmazás sikeres helyileg sikeres, az alábbi lépésekkel telepítheti az Azure-ba:

1. Ha még nem tette meg, engedélyezze a git-tárházat a Web Apps alkalmazáshoz.

2. Adja hozzá a Web Apps alkalmazást git távoliként.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Helyezze üzembe az alkalmazást a távoli mappához küldéssel.
   
   ```bash
   git push azure master
   ```

4. Néhány másodpercen belül az alkalmazás közzé lesz téve és el lesz indítva egy böngészőben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szükség, törölheti az erőforráscsoportot, az Azure Cosmos DB-fiókot és az összes kapcsolódó erőforrást. Ehhez válassza ki az Azure Cosmos DB-fiókhoz használt erőforráscsoportot, válassza a **Törlés**lehetőséget, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

## <a name="next-steps"></a><a name="_Toc395637775"></a>További lépések

> [!div class="nextstepaction"]
> [Mobilalkalmazások létrehozása a Xamarin és az Azure Cosmos DB használatával](mobile-apps-with-xamarin.md)


[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

