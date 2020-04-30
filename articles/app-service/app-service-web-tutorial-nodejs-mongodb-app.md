---
title: 'Oktatóanyag: Node. js-alkalmazás a MongoDB'
description: Ismerje meg, hogyan szerezhet be egy Azure-beli Node. js-alkalmazást, amely egy Azure-beli MongoDB-adatbázishoz (Cosmos DB) csatlakozik. A MEAN. js az oktatóanyagban használatos.
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 05/04/2017
ms.custom: mvc, cli-validate, seodec18
ms.openlocfilehash: 5dd99d9aa7e63066ac4801282e548f2995e57e67
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085595"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>Oktatóanyag: Node. js-és MongoDB-alkalmazás létrehozása az Azure-ban

> [!NOTE]
> Ebben a cikkben egy alkalmazást helyezünk üzembe a Windowson futó App Service-ben. A _linuxon_app Service telepítéséhez lásd: [Node. js-és MongoDB-alkalmazás létrehozása Linuxon Azure app Service](./containers/tutorial-nodejs-mongodb-app.md).
>

Az Azure App Service egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Node. js-alkalmazást a App Serviceban, és hogyan csatlakoztatható egy MongoDB-adatbázishoz. Az oktatóanyag eredménye egy, az [Azure App Service](overview.md)-ben futó MEAN-alkalmazás (MongoDB, Express, AngularJS és Node.js) lesz. Az egyszerűség kedvéért a mintaalkalmazás a [MEAN.js webes keretrendszert](https://meanjs.org/) használja.

![Az Azure App Service-ben futó MEAN.js alkalmazás](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Ismertetett témák:

> [!div class="checklist"]
> * MongoDB-adatbázis létrehozása az Azure-ban;
> * Node.js-alkalmazás csatlakoztatása a MongoDB-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Diagnosztikai naplók streamelése az Azure-ból
> * Az alkalmazás kezelése az Azure Portalon

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

1. [A Git telepítése](https://git-scm.com/)
2. [Telepítse a Node.js-t és az NPM-et](https://nodejs.org/)
3. [Telepítse a Bowert](https://bower.io/) (a [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started) követelménye)
4. [Telepítse a Gulp.js-t](https://gulpjs.com/) (a [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started) követelménye)
5. [Telepítse és futtassa a MongoDB Community Editiont](https://docs.mongodb.com/manual/administration/install-community/). 

## <a name="test-local-mongodb"></a>Helyi MongoDB tesztelése

Nyissa meg a terminálablakot, és a `cd` paranccsal lépjen a `bin` MongoDB telepítés könyvtárába. Ezzel a terminálablakkal futtathatja az oktatóanyagban szereplő összes parancsot.

Futtassa a `mongo` parancsot a terminálban a helyi MongoDB-kiszolgálójához való csatlakozáshoz.

```bash
mongo
```

Ha a kapcsolat létrejött, akkor a MongoDB-adatbázis már fut. Ha nem, a [MongoDB Community Edition telepítését](https://docs.mongodb.com/manual/administration/install-community/) ismertető cikk lépései szerint indítsa el a helyi MongoDB-adatbázist. Gyakran előfordul, hogy a MongoDB telepítése már megtörtént, azonban még el kell indítani a `mongod` parancs futtatásával. 

Ha végzett a MongoDB-adatbázis tesztelésével, írja be a `Ctrl+C` parancsot a terminálon. 

## <a name="create-local-nodejs-app"></a>Helyi Node.js-alkalmazás létrehozása

Ebben a lépésben a helyi Node.js-projektet állítja be.

### <a name="clone-the-sample-application"></a>A mintaalkalmazás klónozása

A terminálablakban a `cd` paranccsal lépjen egy munkakönyvtárra.  

Futtassa a következő parancsot a mintatárház klónozásához. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Ez a mintaadattár a [MEAN.js-adattár](https://github.com/meanjs/mean) másolatát tartalmazza. Módosítva lett az App Service-ben való futtatáshoz (további információért tekintse meg a MEAN.js-adattár [információs fájlját](https://github.com/Azure-Samples/meanjs/blob/master/README.md)).

### <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alábbi parancsokat a szükséges csomagok telepítéséhez és az alkalmazás elindításához.

```bash
cd meanjs
npm install
npm start
```

Az alkalmazás teljes betöltését követően az alábbihoz hasonló üzenet jelenik meg:

<pre>
--
Értem. JS – fejlesztési környezet

Környezet: fejlesztői kiszolgáló: http://0.0.0.0:3000 adatbázis: mongodb://localhost/Mean-dev alkalmazás verziója: 0.5.0 Mean. JS-verzió: 0.5.0--
</pre>

Egy böngészőben nyissa meg a `http://localhost:3000` oldalt. A felső menüben kattintson a **Regisztráció** lehetőségre, és hozzon létre egy tesztfelhasználót. 

A MEAN.js-mintaalkalmazás a felhasználói adatokat az adatbázisban tárolja. Ha a felhasználó létrehozása és a bejelentkezés sikeres volt, akkor az alkalmazás a helyi MongoDB-adatbázisba írja az adatokat.

![A MEAN.js sikeresen csatlakozik a MongoDB-hez](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Válassza az **Admin > Manage Articles** (Felügyelet > Cikkek kezelése) elemet néhány cikk hozzáadásához.

A Node.js leállításához nyomja le a `Ctrl+C` billentyűkombinációt a terminálon. 

> [!NOTE]
> A [Node.js rövid útmutató](app-service-web-get-started-nodejs.md) említi, hogy a gyökéralkalmazás-könyvtárban szükség van egy web.config fájlra. Ebben az oktatóanyagban azonban ezt a web.config fájlt automatikusan létrehozza az App Service, ha a ZIP fájl üzembe helyezése helyett a [helyi Git üzembe helyezést](deploy-local-git.md) használja. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Éles MongoDB létrehozása

Ebben a lépésben egy MongoDB-adatbázist hoz létre az Azure-ban. Miután az alkalmazás üzembe lett helyezve az Azure-ban, ezt a felhőadatbázist használja.

A MongoDB esetében ez az oktatóanyag az [Azure Cosmos DB-t](/azure/documentdb/) használja. A Cosmos DB támogatja a MongoDB-ügyfélkapcsolatokat.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Cosmos DB-fiók létrehozása

> [!NOTE]
> Van bizonyos költségvonzata, ha a jelen oktatóanyag keretében az Azure Cosmos DB-adatbázisokat létrehozza a saját Azure-előfizetésében. A hét napig ingyenesen használható Azure Cosmos DB-fiók kipróbálásához kövesse [az Azure Cosmos DB ingyenes kipróbálását](https://azure.microsoft.com/try/cosmosdb/) ismertető részben leírtakat. A MongoDB csempe **Létrehozás** gombjára kattintva egyszerűen létrehozhat egy ingyenes MongoDB-adatbázist az Azure-ban. Az adatbázis létrehozása után keresse meg a **kapcsolati sztringet** a portálon, és kérje le az Azure Cosmos DB kapcsolati sztringjét, amelyet az oktatóanyag későbbi részében fog használni.
>

A Cloud Shell hozzon létre egy Cosmos DB fiókot a [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-create) paranccsal.

A következő parancsban helyettesítse be a * \<cosmosdb_name>* helyőrző egyedi Cosmos db nevét. Ezt a nevet a rendszer a Cosmos DB-végpont (`https://<cosmosdb_name>.documents.azure.com/`) részeként fogja használni, így a névnek egyedinek kell lennie az Azure-ban található összes Cosmos DB-fiókban. A név csak kisbetűket, számokat és kötőjel (-) karaktert tartalmazhat, és 3–50 karakter hosszúságú lehet.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

A *--kind MongoDB* paraméter lehetővé teszi a MongoDB-ügyfélkapcsolatok használatát.

A Cosmos DB-fiók létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

<pre>
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://&lt;cosmosdb_name&gt;.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  &lt; Output truncated for readability &gt;
}
</pre>

## <a name="connect-app-to-production-mongodb"></a>Alkalmazás csatlakoztatása az éles MongoDB-hez

Ebben a lépésben a MEAN.js-mintaalkalmazást a MongoDB-kapcsolati sztring használatával a korábban létrehozott Cosmos DB-adatbázishoz fogjuk csatlakoztatni. 

### <a name="retrieve-the-database-key"></a>Az adatbáziskulcs lekérése

A Cosmos DB-adatbázishoz való csatlakozáshoz adatbáziskulcs szükséges. Az Cloud Shell a [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-keys) parancs használatával kérje le az elsődleges kulcsot.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

Az Azure CLI az alábbi példához hasonló formában jeleníti meg az adatokat:

<pre>
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
</pre>

Másolja a `primaryMasterKey` értékét. A következő lépés során szükség lesz erre az információra.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>A kapcsolati sztring konfigurálása a Node.js-alkalmazásban

A helyi MEAN.js-adattár _config/env/_ mappájában hozzon létre egy _local-production.js_ nevű fájlt. A _.gitignore_ alapértelmezés szerint úgy van konfigurálva, hogy az adattáron kívül tartsa a fájlt. 

Másolja az alábbi kódot a fájlba. Ne felejtse el lecserélni a két * \<cosmosdb_name>* helyőrzőt a Cosmos db-adatbázis nevére, és a * \<primary_master_key>* helyőrzőt cserélje le az előző lépésben másolt kulccsal.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

A `ssl=true` beállítás a [kapcsolódási karakterlánc követelményei](../cosmos-db/connect-mongodb-account.md#connection-string-requirements)miatt szükséges. 

Mentse a módosításokat.

### <a name="test-the-application-in-production-mode"></a>Az alkalmazás tesztelése éles módban 

Egy helyi terminálablakban futtassa a következő parancsot, amellyel lekicsinyítheti és csomagolhatja a szkripteket az éles környezetben való használathoz. Ez a folyamat az éles környezet számára szükséges fájlokat hozza létre.

```bash
gulp prod
```

Futtassa az alábbi parancsot a _config/env/local-production.js_ fájlban konfigurált kapcsolati sztring használatához.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

A `NODE_ENV=production` beállítja a környezeti változót, amely az éles környezetben történő futásra utasítja a Node.js-t.  A `node server.js` elindítja a Node.js-kiszolgálót az adattár gyökérkönyvtárában található `server.js` fájllal. Így történik a Node.js-alkalmazás betöltése az Azure-ban. 

Az alkalmazás betöltése után ellenőrizze, hogy fut-e az éles környezetben:

<pre>
--
Értem. JS

Környezet: üzemi kiszolgáló: http://0.0.0.0:8443 adatbázis: mongodb://&lt;cosmosdb_name&gt;:&lt;primary_master_key&gt;@&lt;cosmosdb_name&gt;. Documents.Azure.com:10250/Mean?SSL=True&sslverifycertificate = hamis alkalmazás verziója: 0.5.0. JS-verzió: 0.5.0
</pre>

Egy böngészőben nyissa meg a `http://localhost:8443` oldalt. A felső menüben kattintson a **Regisztráció** lehetőségre, és hozzon létre egy tesztfelhasználót. Ha a felhasználó létrehozása és a bejelentkezés sikeres volt, akkor az alkalmazás a Cosmos DB-adatbázisba írja az adatokat az Azure-ban. 

A Node.js leállításához nyomja le a `Ctrl+C` billentyűkombinációt a terminálon. 

## <a name="deploy-app-to-azure"></a>Alkalmazás üzembe helyezése az Azure-ban

Ebben a lépésben a MongoDB-hez csatlakoztatott Node.js-alkalmazást fogja üzembe helyezni az Azure App Service-ben.

### <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Környezeti változó konfigurálása

Alapértelmezés szerint a MEAN.js-projekt a Git-adattáron kívül tartja a _config/env/local-production.js_ fájlt. Az Azure-alkalmazáshoz tehát az Alkalmazásbeállítások segítségével határozhatja meg a MongoDB-kapcsolódási karakterláncot.

Az Alkalmazásbeállítások megadásához használja a [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) parancsot a Cloud shell. 

Az alábbi példa egy `MONGODB_URI` alkalmazás beállítását konfigurálja az Azure-alkalmazásban. Cserélje le a * \<APP_NAME>*, * \<cosmosdb_name>* és * \<primary_master_key>* helyőrzőket.

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

A Node.js-kódban a `process.env.MONGODB_URI` használatával érheti el ezt az alkalmazásbeállítást, mint ahogy bármelyik környezeti változót is. 

A helyi MEAN.js-adattárban nyissa meg a _config/env/production.js_ fájlt (ne a _config/env/local-production.js_ fájlt), amely az éles környezetre vonatkozó konfigurációval rendelkezik. Az alapértelmezett MEAN.js-alkalmazás már a létrehozott `MONGODB_URI` környezeti változó használatára lett konfigurálva.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Leküldéses üzenet küldése a Gitből az Azure-ra

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
 * [new branch]      master -> master
</pre>

Azt tapasztalhatja, hogy az üzembehelyezési folyamat az `npm install` után kezdi meg a [Gulp](https://gulpjs.com/) futtatását. Az App Service nem futtatja a Gulp vagy a Grunt feladatait az üzembe helyezés során, ezért ez a mintaadattár két további fájllal rendelkezik a gyökérkönyvtárában ennek lehetővé tételéhez: 

- _.deployment_ – Ez a fájl utasítja az App Service-t, hogy a `bash deploy.sh` fájlt futtassa egyéni üzembehelyezési szkriptként.
- _deploy.sh_ – Az egyéni üzembehelyezési szkript. Ha áttekinti a fájlt, láthatja, hogy az `npm install` és a `bower install` után futtatja a `gulp prod` parancsot. 

Ezzel a módszerrel adhat hozzá további lépéseket a Git-alapú üzemelő példányhoz. Ha bármikor újraindítja az Azure-alkalmazást, App Service nem futtatja újra ezeket az automatizálási feladatokat.

### <a name="browse-to-the-azure-app"></a>Tallózással keresse meg az Azure-alkalmazást 

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

A felső menüben kattintson a **Regisztráció** lehetőségre, és hozzon létre egy tesztfelhasználót. 

Ha sikerrel jár, és az alkalmazás automatikusan bejelentkezik a létrehozott felhasználói fiókba, akkor a MEAN.js-alkalmazás az Azure-ban kapcsolódik a MongoDB- (Cosmos DB-) adatbázishoz. 

![Az Azure App Service-ben futó MEAN.js alkalmazás](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Válassza az **Admin > Manage Articles** (Felügyelet > Cikkek kezelése) elemet néhány cikk hozzáadásához. 

**Gratulálok!** Egy adatvezérelt Node.js-alkalmazást futtat az Azure App Service-ben.

## <a name="update-data-model-and-redeploy"></a>Az adatmodell frissítése és ismételt üzembe helyezése

Ebben a lépésben módosítja az `article` adatmodellt, és közzéteszi a módosítást az Azure-ban.

### <a name="update-the-data-model"></a>Az adatmodell frissítése

Nyissa meg a _modules/articles/server/models/article.server.model.js_ fájlt.

Az `ArticleSchema` sémában adjon hozzá egy `comment` nevű `String` típust. Amikor elkészült, a séma kódjának így kell kinéznie:

```javascript
const ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>A cikkek kódjának frissítése

Frissítse az `articles` kód többi részét a `comment` használatára.

Öt fájlt kell módosítania: a kiszolgálóvezérlőt és a négy ügyfélnézetet. 

Nyissa meg a _modules/articles/server/controllers/articles.server.controller.js_ fájlt.

A `update` függvényben vegyen fel egy hozzárendelést a `article.comment` számára. Az alábbi kódban a befejezett `update` függvény látható:

```javascript
exports.update = function (req, res) {
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Nyissa meg a _modules/articles/client/views/view-article.client.view.html_ fájlt.

Közvetlenül a záró `</section>` címke felett adja hozzá a következő sort a `comment` megjelenítéséhez a cikk további adatai mellett.

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Nyissa meg a _modules/articles/client/views/list-articles.client.view.html_ fájlt.

Közvetlenül a záró `</a>` címke felett adja hozzá a következő sort a `comment` megjelenítéséhez a cikk további adatai mellett.

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Nyissa meg a _modules/articles/client/views/admin/list-articles.client.view.html_ fájlt.

A `<div class="list-group">` elemben és közvetlenül a záró `</a>` címke felett adja hozzá a következő sort a `comment` megjelenítéséhez a cikk további adatai mellett:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Nyissa meg a _modules/articles/client/views/admin/form-article.client.view.html_ fájlt.

Keresse meg az elküldés gombot tartalmazó `<div class="form-group">` elemet, amelynek a következőképpen kell kinéznie:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Közvetlenül a címke felett adjon hozzá egy másik `<div class="form-group">` elemet, amely lehetővé teszi a `comment` mező szerkesztését a felhasználók számára. Az új elemnek így kell kinéznie:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Módosítások helyi tesztelése

Mentse az összes módosítást.

A helyi terminálablakban tesztelje újra a módosításokat éles módban.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Nyissa meg a `http://localhost:8443` címet egy böngészőben, és ellenőrizze, hogy bejelentkezett-e.

Válassza az **Admin > Manage Articles** (Felügyelet > Cikkek kezelése) elemet, majd vegyen fel egy cikket a **+** gombra kattintva.

Megjelenik az új `Comment` szövegmező.

![Cikkekhez hozzáadott megjegyzésmező](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

A Node.js leállításához nyomja le a `Ctrl+C` billentyűkombinációt a terminálon. 

### <a name="publish-changes-to-azure"></a>Módosítások közzététele az Azure-ba

A helyi terminálablakban mentse a módosításokat a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "added article comment"
git push azure master
```

A `git push` befejezése után navigáljon az Azure-alkalmazáshoz, és próbálja ki az új funkciókat.

![Az Azure-ban közzétett modell- és adatbázis-módosítások](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Ha korábban hozzáadott cikkeket, továbbra is láthatja őket. A Cosmos DB-ben meglévő adatok nem vesztek el. Emellett az adatséma frissítései is érintetlenül hagyják a meglévő adatokat.

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése 

Bár a Node.js-alkalmazás az Azure App Service-ben fut, a konzolnaplófájlokat megkaphatja a terminálban is. Így ugyanazokat a diagnosztikai üzeneteket kaphatja meg az alkalmazáshibák elhárításához.

A log streaming elindításához használja [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) a parancsot a Cloud Shellban.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
``` 

A log streaming megkezdése után frissítse az Azure-alkalmazást a böngészőben a webes forgalom eléréséhez. Ekkor láthatja, hogy a rendszer átadja a konzolnaplófájlokat a terminálnak.

A `Ctrl+C` billentyűparanccsal bármikor leállíthatja a naplóstreamelést. 

## <a name="manage-your-azure-app"></a>Az Azure-alkalmazás kezelése

A létrehozott alkalmazás megjelenítéséhez nyissa meg a [Azure Portal](https://portal.azure.com) .

A bal oldali menüben kattintson a **app Services**elemre, majd kattintson az Azure-alkalmazás nevére.

![Navigálás a portálon egy Azure-alkalmazáshoz](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

Alapértelmezés szerint a portál az alkalmazás **Áttekintés** lapját jeleníti meg. Ezen az oldalon megtekintheti az alkalmazás állapotát. Itt elvégezhet olyan alapszintű felügyeleti feladatokat is, mint a böngészés, leállítás, elindítás, újraindítás és törlés. Az oldal bal oldalán lévő lapok a különböző megnyitható konfigurációs oldalakat jelenítik meg.

![Az App Service lap az Azure Portalon](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>További lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * MongoDB-adatbázis létrehozása az Azure-ban;
> * Node.js-alkalmazás csatlakoztatása a MongoDB-hez
> * Az alkalmazás üzembe helyezése az Azure-ban
> * Az adatmodell frissítése és az alkalmazás ismételt üzembe helyezése
> * Naplók streamelése az Azure-ból a saját terminálba
> * Az alkalmazás kezelése az Azure Portalon

Lépjen a következő oktatóanyaghoz, amelyből megtudhatja, hogyan képezhet le egyedi DNS-nevet az alkalmazásokhoz.

> [!div class="nextstepaction"] 
> [Meglévő egyéni DNS-név leképezése Azure App Service](app-service-web-tutorial-custom-domain.md)
